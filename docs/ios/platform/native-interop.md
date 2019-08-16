---
title: Referenciando bibliotecas nativas no Xamarin. iOS
description: Este documento discute como vincular bibliotecas C nativas em um aplicativo Xamarin. iOS. Ele descreve como criar bibliotecas nativas universais e acessar métodos C#C do.
ms.prod: xamarin
ms.assetid: 1DA80280-E78A-EC4B-8673-C249C8425CF5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/28/2016
ms.openlocfilehash: 426b46f578ac3a09372fa4bf63ace1b6545c4866
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528172"
---
# <a name="referencing-native-libraries-in-xamarinios"></a>Referenciando bibliotecas nativas no Xamarin. iOS

O Xamarin. iOS dá suporte à vinculação com bibliotecas C nativas e bibliotecas objec-C. Este documento discute como vincular suas bibliotecas nativas do C ao seu projeto do Xamarin. iOS. Para obter informações sobre como fazer o mesmo para bibliotecas Objective-C, consulte nosso documento de [tipos Objective-c de associação](~/ios/platform/binding-objective-c/index.md) .

<a name="building_native" />

## <a name="building-universal-native-libraries-i386-armv7-and-arm64"></a>Criando bibliotecas nativas universais (i386, ARMv7 e ARM64)

Geralmente, é desejável criar suas bibliotecas nativas para cada uma das plataformas com suporte para o desenvolvimento do iOS (i386 para o simulador e ARMv7/ARM64 para os próprios dispositivos). Se você já tem um projeto do Xcode para sua biblioteca, isso é realmente trivial.

Para criar a versão i386 da sua biblioteca nativa, execute o seguinte comando em um terminal:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphonesimulator -arch i386 -configuration Release clean build
```

Isso resultará em uma biblioteca estática nativa em `MyProject.xcodeproj/build/Release-iphonesimulator/`. Copie (ou mova) o arquivo morto da biblioteca (libMyLibrary. a) para ficar seguro para uso posterior, dando a ele um nome exclusivo (como **libMyLibrary-i386. a**) para que ele não entre em conflito com as versões arm64 e ARMv7 da mesma biblioteca que você compilará em seguida.

Para criar a versão ARM64 da sua biblioteca nativa, execute o seguinte comando:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch arm64 -configuration Release clean build
```

Desta vez, a biblioteca nativa criada será localizada em `MyProject.xcodeproj/build/Release-iphoneos/`. Mais uma vez, copie (ou mova) esse arquivo para um local seguro, renomeando-o para algo como **libMyLibrary-arm64. a** para que ele não entre em conflito.

Agora, crie a versão ARMv7 da biblioteca:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch armv7 -configuration Release clean build
```

Copie (ou mova) o arquivo de biblioteca resultante para o mesmo local em que você moveu as outras 2 versões da biblioteca, renomeando-o para algo como **libMyLibrary-ARMv7. a**.

Para criar um binário universal, você pode usar a `lipo` ferramenta da seguinte forma:

```bash
lipo -create -output libMyLibrary.a libMyLibrary-i386.a libMyLibrary-arm64.a libMyLibrary-armv7.a
```

Isso cria `libMyLibrary.a` qual será uma biblioteca universal (FAT) que será adequada para uso em todos os destinos de desenvolvimento do Ios.


### <a name="missing-required-architecture-i386"></a>Falta a arquitetura necessária i386

Se você estiver recebendo uma `does not implement methodSignatureForSelector` mensagem `does not implement doesNotRecognizeSelector` ou em sua saída de tempo de execução ao tentar consumir uma biblioteca Objective-C no simulador de Ios, sua biblioteca provavelmente não foi compilada para a arquitetura i386 (consulte o [edifício universal Native ](#building_native)Seção de bibliotecas acima).

Para verificar as arquiteturas com suporte em uma determinada biblioteca, use o seguinte comando no terminal:

```bash
lipo -info /full/path/to/libraryname.a
```

Em `/full/path/to/` que é o caminho completo para a biblioteca que está `libraryname.a` sendo consumida e é o nome da biblioteca em questão.

Se você tiver a origem para a biblioteca, precisará compilá-la e agrupá-la para a arquitetura i386 também, se desejar testar seu aplicativo no simulador de iOS.

### <a name="linking-your-library"></a>Vinculando sua biblioteca

Qualquer biblioteca de terceiros que você consumir precisa ser vinculada estaticamente ao seu aplicativo. 

Se você quisesse vincular estaticamente a biblioteca "libMyLibrary. a" obtida da Internet ou compilada com o Xcode, precisaria fazer algumas coisas:

- Traga a biblioteca para seu projeto
- Configurar o Xamarin. iOS para vincular a biblioteca
- Acesse os métodos da biblioteca.


Para **colocar a biblioteca em seu projeto**, selecione o projeto no Gerenciador de soluções e pressione **Command + Option + a**. Navegue até o libMyLibrary. a e adicione-o ao projeto. Quando solicitado, informe Visual Studio para Mac ou o Visual Studio para copiá-lo no projeto. Depois de adicioná-lo, localize o libFoo. a no projeto, clique com o botão direito do mouse nele e defina a **ação de Build** como **None**.

Para **Configurar o Xamarin. Ios para vincular a biblioteca**, nas opções de projeto do seu executável final (não a biblioteca em si, mas o programa final) você precisa adicionar o argumento extra do **Build do IOS**(que fazem parte de suas opções de projeto) o "-gcc_flags" opção seguida de uma cadeia de caracteres entre aspas que contém todas as bibliotecas adicionais necessárias para o seu programa, por exemplo:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load ${ProjectDir}/libMyLibrary.a"
```

O exemplo acima irá vincular **libMyLibrary. a**

Você pode usar `-gcc_flags` para especificar qualquer conjunto de argumentos de linha de comando a serem passados para o compilador gcc usado para fazer o link final do seu executável. Por exemplo, essa linha de comando também faz referência à estrutura CFNetwork:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

Se sua biblioteca nativa contiver C++ código, você também deverá passar o sinalizador-cxx em seus "argumentos extras" para que o Xamarin. Ios saiba usar o compilador correto. Para C++ as opções anteriores, teriam a seguinte aparência:

```bash
-cxx -gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

<a name="Accessing_C_Methods_from_C#" />

## <a name="accessing-c-methods-from-c35"></a>Acessando métodos C de C&#35;

Há dois tipos de bibliotecas nativas disponíveis no iOS:

- Bibliotecas compartilhadas que fazem parte do sistema operacional.

- Bibliotecas estáticas que você envia com seu aplicativo.


Para acessar os métodos definidos em qualquer um deles, use a [funcionalidade P/Invoke do mono](https://www.mono-project.com/docs/advanced/pinvoke/) , que é a mesma tecnologia que você usaria no .net, que é aproximadamente:

- Determinar qual função C você deseja invocar
- Determinar sua assinatura
- Determinar em qual biblioteca ela reside
- Gravar a declaração P/Invoke apropriada

Ao usar P/Invoke, você precisa especificar o caminho da biblioteca com a qual você está vinculando. Ao usar bibliotecas compartilhadas do IOS, você pode codificar o caminho ou pode usar as constantes de conveniência que definimos em nosso `Constants`, essas constantes devem abranger as bibliotecas compartilhadas do Ios.

Por exemplo, se você quisesse invocar o método UIRectFrameUsingBlendMode da biblioteca UIKit da Apple que tem essa assinatura em C:

```csharp
void UIRectFrameUsingBlendMode (CGRect rect, CGBlendMode mode);
```

Sua declaração P/Invoke ficaria assim:

```csharp
[DllImport (Constants.UIKitLibrary,EntryPoint="UIRectFrameUsingBlendMode")]
public extern static void RectFrameUsingBlendMode (RectangleF rect, CGBlendMode blendMode);
```

Constants. UIKitLibrary é meramente uma constante definida como "/System/Library/Frameworks/UIKit.framework/UIKit", o EntryPoint nos permite especificar, opcionalmente, o nome externo (UIRectFramUsingBlendMode) ao mesmo tempo em que C#expõe um nome diferente no, o RectFrameUsingBlendMode mais curta.

<a name="Accessing_C_Dylibs" />

### <a name="accessing-c-dylibs"></a>Acessando C Dylibs

Se você tiver a necessidade de consumir um C dylib em seu aplicativo Xamarin. Ios, há um pouco de configuração extra necessária antes de chamar o `DllImport` atributo.

Por exemplo, se tivermos um `Animal.dylib` com um `Animal_Version` método que iremos chamar em nosso aplicativo, precisamos informar ao Xamarin. Ios o local da biblioteca antes de tentar consumi-lo.

Para fazer isso, edite `Main.CS` o arquivo e faça com que ele se pareça com o seguinte:

```csharp
static void Main (string[] args)
{
    // Load Dylib
    MonoTouch.ObjCRuntime.Dlfcn.dlopen ("/full/path/to/Animal.dylib", 0);

    // Start application
    UIApplication.Main (args, null, "AppDelegate");
}
```

Em `/full/path/to/` que é o caminho completo para o dylib que está sendo consumido. Com esse código em vigor, podemos vincular ao `Animal_Version` método da seguinte maneira:

```csharp
[DllImport("Animal.dylib", EntryPoint="Animal_Version")]
public static extern double AnimalLibraryVersion();
```

<a name="Static_Libraries" />

### <a name="static-libraries"></a>Bibliotecas estáticas

Como você só pode usar bibliotecas estáticas no Ios, não há uma biblioteca compartilhada externa com a qual vincular, portanto, o parâmetro de caminho no atributo DllImport precisa usar o `__Internal` nome especial (Observe os caracteres de sublinhado duplo no início do nome) em vez de o nome do caminho.

Isso força o DllImport a pesquisar o símbolo do método que você está fazendo referência no programa atual, em vez de tentar carregá-lo de uma biblioteca compartilhada.

