---
title: Referenciando as bibliotecas nativas no xamarin. IOS
description: Este documento aborda como vincular as bibliotecas de C nativas em um aplicativo xamarin. IOS. Ele descreve como criar bibliotecas nativas universais e acessar os métodos de C do C#.
ms.prod: xamarin
ms.assetid: 1DA80280-E78A-EC4B-8673-C249C8425CF5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/28/2016
ms.openlocfilehash: 7ed8fc18624f46abd4a9fc293d8c33a1722da7dd
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61424615"
---
# <a name="referencing-native-libraries-in-xamarinios"></a>Referenciando as bibliotecas nativas no xamarin. IOS

Xamarin. IOS dá suporte à vinculação com bibliotecas de C nativas e bibliotecas de Objective-C. Este documento aborda como vincular suas bibliotecas C nativas com o seu projeto xamarin. IOS. Para obter informações sobre como fazer o mesmo para bibliotecas de Objective-C, consulte nosso [tipos de associação de Objective-C](~/ios/platform/binding-objective-c/index.md) documento.

<a name="building_native" />

## <a name="building-universal-native-libraries-i386-armv7-and-arm64"></a>Criar bibliotecas nativas de Universal (i386, ARMv7 e ARM64)

Geralmente é desejável criar suas bibliotecas nativas para cada uma das plataformas com suporte para desenvolvimento do iOS (i386 os próprios dispositivos para o simulador e ARMv7/ARM64). Se você já tem um projeto do Xcode para sua biblioteca, isso é realmente simples para fazer.

Para compilar a versão de i386 da sua biblioteca nativa, execute o seguinte comando em um terminal:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphonesimulator -arch i386 -configuration Release clean build
```

Isso resultará em uma biblioteca estática nativa em `MyProject.xcodeproj/build/Release-iphonesimulator/`. Copiar (ou mover) o arquivo de arquivamento de biblioteca (libMyLibrary.a) para algum lugar seguro para uso posterior, dando a ele um nome exclusivo (como **libMyLibrary i386.a**), de modo que ele não estar em conflito com as versões arm64 e armv7 na mesma biblioteca que você irá criar a seguir.

Para compilar a versão ARM64 da sua biblioteca nativa, execute o seguinte comando:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch arm64 -configuration Release clean build
```

Neste momento, a biblioteca nativa criada estará localizada em `MyProject.xcodeproj/build/Release-iphoneos/`. Mais uma vez, copiar (ou mover) esse arquivo para um local seguro, renomeá-lo para algo como **libMyLibrary arm64.a** , de modo que ele não estar em conflito.

Agora, compile a versão de ARMv7 da biblioteca:

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

Se você estiver recebendo um `does not implement methodSignatureForSelector` ou `does not implement doesNotRecognizeSelector` mensagem na saída em tempo de execução ao tentar consumir uma biblioteca do Objective-C no iOS Simulator, sua biblioteca provavelmente não foi compilada para a arquitetura i386 (consulte a [construção universais Bibliotecas nativas](#building_native) seção acima).

Para verificar as arquiteturas compatíveis com uma determinada biblioteca, use o seguinte comando no Terminal:

```bash
lipo -info /full/path/to/libraryname.a
```

Em que `/full/path/to/` é o caminho completo para a biblioteca que está sendo consumida e `libraryname.a` é o nome da biblioteca em questão.

Se você tiver o código-fonte para a biblioteca, você precisará compilar e agrupá-la para a arquitetura i386 Além disso, se você quiser testar seu aplicativo no simulador de iOS.

### <a name="linking-your-library"></a>Sua biblioteca de vinculação

Qualquer biblioteca de terceiros que você consumir precisa ser estaticamente vinculados ao seu aplicativo. 

Se você quiser vincular estaticamente a biblioteca "libMyLibrary.a" que você obteve da Internet ou de compilação com o Xcode, você precisa fazer algumas coisas:

-  Colocar a biblioteca em seu projeto
-  Configurar o xamarin. IOS para vincular a biblioteca
-  Acesse os métodos da biblioteca.


Para **colocar a biblioteca em seu projeto**, selecione o projeto no Gerenciador de soluções e pressione **Command + Option + a**. Navegue até o libMyLibrary.a e adicioná-lo ao projeto. Quando solicitado, informar ao Visual Studio para Mac ou Visual Studio para copiá-lo para o projeto. Depois de adicioná-lo, encontre o libFoo.a no projeto, clique com o botão direito nele e defina as **ação de compilação** para **none**.

Para **configurar xamarin. IOS para a biblioteca de vínculo**, nas opções de projeto para seu arquivo executável final (não a biblioteca em si, mas o final do programa) você precisa adicionar nos **Build do iOS**do argumento Extra (esses são parte das suas opções de projeto) a "-gcc_flags" opção seguido por uma cadeia de caracteres entre aspas que contém todas as bibliotecas adicionais que são necessárias para o seu programa, por exemplo:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load ${ProjectDir}/libMyLibrary.a"
```

O exemplo acima vinculará **libMyLibrary.a**

Você pode usar `-gcc_flags` para especificar qualquer conjunto de argumentos de linha de comando para passar para o compilador GCC usado para fazer o link final de seu executável. Por exemplo, esta linha de comando, também faz referência a estrutura CFNetwork:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

Se sua biblioteca nativa contém o código de C++, você também deve transmitir o sinalizador - cxx em seu "argumentos extras" para que o xamarin. IOS Saiba usar o compilador correto. As opções anteriores seriam semelhante para C++:

```bash
-cxx -gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

<a name="Accessing_C_Methods_from_C#" />

## <a name="accessing-c-methods-from-c35"></a>Acessando os métodos de C do C&#35;

Há dois tipos de bibliotecas nativas disponíveis no iOS:

-  Bibliotecas compartilhadas que fazem parte do sistema operacional.

-  Bibliotecas estáticas que são fornecidos com o seu aplicativo.


Para acessar os métodos definidos em qualquer um deles, use [funcionalidade de P/Invoke do Mono](https://www.mono-project.com/docs/advanced/pinvoke/) que é a mesma tecnologia que você deseja usar em .NET, que é aproximadamente:

-  Determinar a função C que você deseja invocar
-  Determinar sua assinatura
-  Determinar qual biblioteca reside no
-  Gravar a declaração de P/Invoke adequada

Quando você usar P/Invoke, você precisará especificar o caminho da biblioteca que você está vinculando com. Quando usando iOS bibliotecas compartilhadas, você pode a codificar o caminho ou você pode usar as constantes de conveniência que definimos em nossa `Constants`, constantes devem abranger as bibliotecas compartilhada do iOS.

Por exemplo, se você quisesse invocar o método UIRectFrameUsingBlendMode da biblioteca de UIKit da Apple que tem essa assinatura em c:

```csharp
void UIRectFrameUsingBlendMode (CGRect rect, CGBlendMode mode);
```

Sua declaração de P/Invoke teria esta aparência:

```csharp
[DllImport (Constants.UIKitLibrary,EntryPoint="UIRectFrameUsingBlendMode")]
public extern static void RectFrameUsingBlendMode (RectangleF rect, CGBlendMode blendMode);
```

O Constants.UIKitLibrary é meramente uma constante definida como "/ System/Library/Frameworks/UIKit.framework/UIKit", o ponto de entrada nos permite especificar, opcionalmente, o nome externo (UIRectFramUsingBlendMode) durante a exposição de um nome diferente em C#, o RectFrameUsingBlendMode mais curto.

<a name="Accessing_C_Dylibs" />

### <a name="accessing-c-dylibs"></a>Acessando dylibs de C

Se você tiver a necessidade de consumir um Dylib C em seu aplicativo xamarin. IOS, há um pouco de configuração adicional é necessária antes de chamar o `DllImport` atributo.

Por exemplo, se tivermos um `Animal.dylib` com um `Animal_Version` método que a chamada em nosso aplicativo, precisamos informar ao xamarin. IOS do local da biblioteca antes de tentar consumi-lo.

Para fazer isso, edite o `Main.CS` de arquivo e torná-lo semelhante ao seguinte:

```csharp
static void Main (string[] args)
{
    // Load Dylib
    MonoTouch.ObjCRuntime.Dlfcn.dlopen ("/full/path/to/Animal.dylib", 0);

    // Start application
    UIApplication.Main (args, null, "AppDelegate");
}
```

Onde `/full/path/to/` é o caminho completo para o Dylib sendo consumido. Com esse código funcionando, pode, em seguida, o vincularemos o `Animal_Version` método da seguinte maneira:

```csharp
[DllImport("Animal.dylib", EntryPoint="Animal_Version")]
public static extern double AnimalLibraryVersion();
```

<a name="Static_Libraries" />

### <a name="static-libraries"></a>Bibliotecas estáticas

Como você só pode usar bibliotecas estáticas no iOS, não há nenhuma biblioteca compartilhada externa para vincular com, portanto, o parâmetro de caminho no atributo DllImport deve usar o nome especial `__Internal` (Observe os caracteres de duplo sublinhado no início do nome) em vez de o nome do caminho.

Isso força DllImport para pesquisar o símbolo do método que você está fazendo referência no programa atual, em vez de tentar carregá-lo de uma biblioteca compartilhada.

