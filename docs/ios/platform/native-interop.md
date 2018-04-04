---
title: Fazendo referência a bibliotecas nativas
ms.prod: xamarin
ms.assetid: 1DA80280-E78A-EC4B-8673-C249C8425CF5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/28/2016
ms.openlocfilehash: 4d58e869dc1357faef71ea88ed6b5ea30aaf960d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="referencing-native-libraries"></a>Fazendo referência a bibliotecas nativas

Xamarin dá suporte à vinculação com bibliotecas nativas do C e bibliotecas Objective-C. Este documento descreve como vincular suas bibliotecas C nativo com o seu projeto xamarin. Para obter informações sobre como fazer o mesmo para bibliotecas Objective-C, consulte nosso [associação Objective-C tipos](~/ios/platform/binding-objective-c/index.md) documento.

<a name="building_native" />

## <a name="building-universal-native-libraries-i386-armv7-and-arm64"></a>Criar bibliotecas nativas de Universal (i386, ARMv7 e ARM64)

Geralmente é desejável criar suas bibliotecas nativas para cada uma das plataformas com suporte para desenvolvimento do iOS (i386 os próprios dispositivos para o simulador e ARMv7/ARM64). Se você já tem um projeto Xcode para a sua biblioteca, isso é realmente trivial fazer.

Para criar a versão i386 da biblioteca nativa, execute o seguinte comando em um terminal:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphonesimulator -arch i386 -configuration Release clean build
```

Isso resultará em uma biblioteca estática nativo em `MyProject.xcodeproj/build/Release-iphonesimulator/`. Copiar (ou mover) o arquivo biblioteca (libMyLibrary.a) para em um local seguro para uso posterior, dando a ele um nome exclusivo (como **libMyLibrary i386.a**) para que ele não podem conflitar com as versões arm64 e armv7 da mesma biblioteca que você vai construir a seguir.

Para criar a versão de ARM64 da biblioteca nativa, execute o seguinte comando:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch arm64 -configuration Release clean build
```

Neste momento, a biblioteca nativa criada estará localizada em `MyProject.xcodeproj/build/Release-iphoneos/`. Uma vez, copiar (ou mover) esse arquivo para um local seguro, renomeá-lo para algo como **libMyLibrary arm64.a** para que ele não podem conflitar.

Agora o ARMv7 versão da compilação a biblioteca:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch armv7 -configuration Release clean build
```

Copiar (ou mover) o arquivo resultante da biblioteca no mesmo local você moveu as 2 versões da biblioteca, renomeá-lo para algo como **libMyLibrary armv7.a**.

Para tornar um universal binário, você pode usar o `lipo` ferramenta da seguinte forma:

```bash
lipo -create -output libMyLibrary.a libMyLibrary-i386.a libMyLibrary-arm64.a libMyLibrary-armv7.a
```

Isso cria `libMyLibrary.a` que será uma biblioteca (fat) universal que será adequada a ser usado para todos os destinos de desenvolvimento do iOS.


### <a name="missing-required-architecture-i386"></a>I386 de arquitetura necessárias ausentes

Se você estiver recebendo um `does not implement methodSignatureForSelector` ou `does not implement doesNotRecognizeSelector` mensagem na saída em tempo de execução durante a tentativa de consumir uma biblioteca Objective-C no simulador, sua biblioteca iOS provavelmente não foi compilada para a arquitetura i386 (consulte o [construção Universal Bibliotecas nativas](#building_native) seção acima).

Para verificar as arquiteturas com suporte por uma determinada biblioteca, use o seguinte comando no Terminal:

```bash
lipo -info /full/path/to/libraryname.a
```

Onde `/full/path/to/` é o caminho completo para a biblioteca que está sendo consumido e `libraryname.a` é o nome da biblioteca em questão.

Se você possui a fonte para a biblioteca, você precisará compilar e agrupá-lo para a arquitetura i386 também, se você quiser testar seu aplicativo no simulador iOS.

### <a name="linking-your-library"></a>A biblioteca de vinculação

Qualquer biblioteca de terceiros que você consumir deve ser vinculado estaticamente com seu aplicativo. 

Se você quiser vincular estaticamente a biblioteca "libMyLibrary.a" que você obteve da Internet ou de compilação com o Xcode, você precisa fazer algumas coisas:

-  Colocar a biblioteca em seu projeto
-  Configurar o xamarin para a biblioteca de vínculo
-  Acesse os métodos da biblioteca.


Para **colocar a biblioteca em seu projeto**, selecione o projeto do Gerenciador de soluções e pressione **comando + opção + a**. Navegue até o libMyLibrary.a e adicioná-lo ao projeto. Quando solicitado, informe o Visual Studio para Mac ou o Visual Studio para copiá-lo para o projeto. Depois de adicioná-lo, localizar o libFoo.a no projeto, clique com o botão direito nela e definir o **ação de compilação** para **nenhum**.

Para **xamarin configurar para a biblioteca de vínculo**, as opções de projeto para o executável final (não a própria biblioteca, mas o final do programa) você precisa adicionar no **iOS compilação**do argumento Extra (esses são parte de suas opções de projeto) de "-gcc_flags" opção seguido por uma cadeia de caracteres entre aspas que contém todas as bibliotecas adicionais que são necessárias para seu programa, por exemplo:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load ${ProjectDir}/libMyLibrary.a"
```

O exemplo acima vinculará **libMyLibrary.a**

Você pode usar `-gcc_flags` para especificar um conjunto de argumentos de linha de comando para passar para o compilador GCC usado para fazer o link final de seu executável. Por exemplo, esta linha de comando, também faz referência a estrutura de CFNetwork:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

Se sua biblioteca nativa contém código de C++, você também deve transmitir o sinalizador - cxx "Extra argumentos" para que xamarin Saiba usar o compilador correto. As opções anteriores seria semelhante para C++:

```bash
-cxx -gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

<a name="Accessing_C_Methods_from_C#" />

## <a name="accessing-c-methods-from-c35"></a>Acessar os métodos de C do C&#35;

Há dois tipos de bibliotecas nativas disponível no iOS:

-  Bibliotecas compartilhadas que fazem parte do sistema operacional.

-  Bibliotecas estáticas que são fornecidos com o seu aplicativo.


Para acessar os métodos definidos em um deles, use [funcionalidade de P/Invoke do Mono](http://www.mono-project.com/docs/advanced/pinvoke/) que é a mesma tecnologia que você deseja usar no .NET, que é aproximadamente:

-  Determinar qual função de C que você deseja invocar
-  Determinar sua assinatura
-  Determinar qual ela reside de biblioteca
-  Gravar a declaração P/Invoke apropriada


Quando você usar P/Invoke, você precisa especificar o caminho da biblioteca que você está vinculando com. Quando usar iOS bibliotecas compartilhadas, você pode ou codificar o caminho ou você pode usar as constantes de conveniência que definimos no nosso [classe constantes](https://developer.xamarin.com/api/type/Constants/), constantes devem abranger as bibliotecas de iOS compartilhados.

Por exemplo, se você quisesse invocar o método UIRectFrameUsingBlendMode da biblioteca de UIKit da Apple que possui essa assinatura em c:

```csharp
void UIRectFrameUsingBlendMode (CGRect rect, CGBlendMode mode);
```

A declaração P/Invoke ficaria assim:

```csharp
[DllImport (Constants.UIKitLibrary,EntryPoint="UIRectFrameUsingBlendMode")]
public extern static void RectFrameUsingBlendMode (RectangleF rect, CGBlendMode blendMode);
```

O Constants.UIKitLibrary é simplesmente uma constante definida como "/ System/Library/Frameworks/UIKit.framework/UIKit", o ponto de entrada nos permite especificar opcionalmente o nome externo (UIRectFramUsingBlendMode) durante a exposição de um nome diferente em c#, o menor RectFrameUsingBlendMode.

<a name="Accessing_C_Dylibs" />

### <a name="accessing-c-dylibs"></a>Acessando Dylibs C

Se você tiver a necessidade de consumir um Dylib C em seu aplicativo xamarin, há um pouco de configuração adicional que é necessária antes de chamar o `DllImport` atributo.

Por exemplo, se houver um `Animal.dylib` com um `Animal_Version` método de chamada em nosso aplicativo, é necessário informar o xamarin do local da biblioteca antes de tentar consumi-lo.

Para fazer isso, edite o `Main.CS` de arquivo e torná-lo a aparência a seguir:

```csharp
static void Main (string[] args)
{
    // Load Dylib
    MonoTouch.ObjCRuntime.Dlfcn.dlopen ("/full/path/to/Animal.dylib", 0);

    // Start application
    UIApplication.Main (args, null, "AppDelegate");
}
```

Onde `/full/path/to/` é o caminho completo para o Dylib sendo consumida. Com esse código, nós, vá para o `Animal_Version` método da seguinte maneira:

```csharp
[DllImport("Animal.dylib", EntryPoint="Animal_Version")]
public static extern double AnimalLibraryVersion();
```

<a name="Static_Libraries" />

### <a name="static-libraries"></a>Bibliotecas estáticas

Desde que você só pode usar bibliotecas estáticas no iOS, não há nenhuma biblioteca compartilhada externa para vincular com, assim, o parâmetro de caminho no atributo DllImport precisa usar o nome especial `__Internal` (Observe os caracteres de sublinhado duplo no início do nome) em vez de o nome do caminho.

Isso força DllImport para pesquisar o símbolo do método que você está fazendo referência no programa atual, em vez de tentar carregá-lo de uma biblioteca compartilhada.

