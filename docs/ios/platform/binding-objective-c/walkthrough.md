---
title: 'Walkthrough: associando uma biblioteca de Objective do iOS-C'
description: Este artigo fornece uma explicação prática de como criar uma associação do Xamarin. iOS para uma biblioteca Objective-C existente, InfColorPicker. Ele aborda tópicos como a compilação de uma biblioteca Objective-C estática, sua associação e o uso da associação em um aplicativo Xamarin. iOS.
ms.prod: xamarin
ms.assetid: D3F6FFA0-3C4B-4969-9B83-B6020B522F57
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2017
ms.openlocfilehash: f5e5af7d9b4ec85832f2d6050f632d054ba089a2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032676"
---
# <a name="walkthrough-binding-an-ios-objective-c-library"></a>Walkthrough: associando uma biblioteca de Objective do iOS-C

_Este artigo fornece uma explicação prática de como criar uma associação do Xamarin. iOS para uma biblioteca Objective-C existente, InfColorPicker. Ele aborda tópicos como a compilação de uma biblioteca Objective-C estática, sua associação e o uso da associação em um aplicativo Xamarin. iOS._

Ao trabalhar no iOS, você pode encontrar casos em que deseja consumir uma biblioteca de Objective-C de terceiros. Nessas situações, você pode usar um _projeto de associação_ do xamarin. Ios para criar uma [ C# Associação](~/cross-platform/macios/binding/overview.md) que permitirá que você consuma a biblioteca em seus aplicativos Xamarin. Ios.

Geralmente, no ecossistema do iOS, você pode encontrar bibliotecas em 3 tipos:

- Como um arquivo de biblioteca estática pré-compilado com `.a` extensão junto com seus cabeçalhos (arquivos. h). Por exemplo, [biblioteca de análise do Google](https://developers.google.com/analytics/devguides/collection/ios/v3/sdk-download?hl=es#download_sdk)
- Como uma estrutura pré-compilada. Essa é apenas uma pasta que contém a biblioteca estática, os cabeçalhos e, às vezes, recursos adicionais com a extensão `.framework`. Por exemplo, a [biblioteca o AdMob do Google](https://developers.google.com/admob/ios/download).
- Como apenas arquivos de código-fonte. Por exemplo, uma biblioteca que contém apenas `.m` e `.h` arquivos de objetivo C.

No primeiro e segundo cenário, já haverá uma biblioteca estática CocoaTouch precompilada, portanto, neste artigo, nos concentraremos no terceiro cenário. Lembre-se, antes de começar a criar uma associação, sempre verifique a licença fornecida com a biblioteca para garantir que você esteja livre para associá-la.

Este artigo fornece uma explicação passo a passo de como criar um projeto de associação usando o projeto de [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) de software livre Objective-C como um exemplo, no entanto, todas as informações neste guia podem ser adaptadas para uso com qualquer biblioteca Objective-c de terceiros . A biblioteca InfColorPicker fornece um controlador de exibição reutilizável que permite ao usuário selecionar uma cor com base em sua representação HSB, tornando a seleção de cores mais amigável.

[![](walkthrough-images/run01.png "Example of the InfColorPicker library running on iOS")](walkthrough-images/run01.png#lightbox)

Abordaremos todas as etapas necessárias para consumir essa API Objective-C específica no Xamarin. iOS:

- Primeiro, criaremos uma biblioteca estática Objective-C usando o Xcode.
- Em seguida, ligaremos essa biblioteca estática com o Xamarin. iOS.
- Em seguida, mostre como a nitidez do objetivo pode reduzir a carga de trabalho gerando automaticamente algumas (mas não todas) as definições de API necessárias exigidas pela associação do Xamarin. iOS.
- Por fim, criaremos um aplicativo Xamarin. iOS que usa a associação.

O aplicativo de exemplo demonstrará como usar um delegado forte para a comunicação entre a API InfColorPicker e C# nosso código. Depois de observarmos como usar um delegado forte, abordaremos como usar delegados fracos para executar as mesmas tarefas.

## <a name="requirements"></a>Requisitos

Este artigo pressupõe que você tenha alguma familiaridade com o Xcode e com a linguagem Objective-C e tenha lido nossa documentação [de Binding Objective-c](~/cross-platform/macios/binding/index.md) . Além disso, é necessário o seguinte para concluir as etapas apresentadas:

- O **Xcode e o SDK do IOS** – o Xcode da Apple e a mais recente API do IOS precisam ser instalados e configurados no computador do desenvolvedor.
- **[Ferramentas de linha de comando do Xcode](#Installing_the_Xcode_Command_Line_Tools)** – as ferramentas de linha de comando do Xcode devem ser instaladas para a versão atualmente instalada do Xcode (veja abaixo os detalhes da instalação).
- **Visual Studio para Mac ou Visual Studio** -a versão mais recente do Visual Studio para Mac ou do Visual Studio deve ser instalada e configurada no computador de desenvolvimento. Um Apple Mac é necessário para desenvolver um aplicativo Xamarin. iOS e, ao usar o Visual Studio, você deve estar conectado a [um host de Build do xamarin. Ios](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
- **A última versão do objetivo de Objective** -uma cópia atual da ferramenta de nitidez objetiva baixada [daqui.](~/cross-platform/macios/binding/objective-sharpie/get-started.md) Se você já tiver a nitidez do objetivo instalada, poderá atualizá-la para a versão mais recente usando o `sharpie update`

<a name="Installing_the_Xcode_Command_Line_Tools"/>

## <a name="installing-the-xcode-command-line-tools"></a>Instalando as ferramentas de linha de comando do Xcode

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Conforme mencionado acima, usaremos as ferramentas de linha de comando do Xcode (especificamente `make` e `lipo`) neste passo a passos. O comando `make` é um utilitário UNIX muito comum que automatizará a compilação de programas e bibliotecas executáveis usando um _makefile_ que especifica como o programa deve ser compilado. The `lipo` command is an OS X command line utility for creating multi-architecture files; it will combine multiple `.a` files into one file that can be used by all hardware architectures.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

As stated above, we'll be using Xcode Command Line Tools on the **Mac Build Host** (specifically `make` and `lipo`) in this walkthrough. The `make` command is a very common Unix utility that will automate the compilation of executable programs and libraries by using a _makefile_ to specifies how to build the program. The `lipo` command is an OS X command line utility for creating multi-architecture files; it will combine multiple `.a` files into one file that can be used by all hardware architectures.

-----

According to Apple's [Building from the Command Line with Xcode FAQ](https://developer.apple.com/library/ios/technotes/tn2339/_index.html) documentation, in OS X 10.9 and greater, the **Downloads** pane of Xcode **Preferences** dialog not longer supports the downloading command-line tools.

You'll need to use one of the following methods to install the tools:

- **Install Xcode** - When you install Xcode, it comes bundled with all your command-line tools. In OS X 10.9  shims (installed in `/usr/bin`), can map any tool included in `/usr/bin` to the corresponding tool inside Xcode. For example, the `xcrun` command, which allows you to find or run any tool inside Xcode from the command line.
- **The Terminal Application** - From the Terminal application, you can install the command line tools by running the `xcode-select --install` command:
  - Start the Terminal Application.
  - Type `xcode-select --install` and press **Enter**, for example:

  ```bash
  Europa:~ kmullins$ xcode-select --install
  ```

  - You'll be asked to install the command line tools, click the **Install** button: [![](walkthrough-images/xcode01.png "Installing the command line tools")](walkthrough-images/xcode01.png#lightbox)

  - The tools will be downloaded and installed from Apple's servers: [![](walkthrough-images/xcode02.png "Downloading the tools")](walkthrough-images/xcode02.png#lightbox)

- **Downloads for Apple Developers** - The Command Line Tools package is available the [Downloads for Apple Developers](https://developer.apple.com/downloads/index.action) web page. Log in with your Apple ID, then search for and download the Command Line Tools: [![](walkthrough-images/xcode03.png "Finding the Command Line Tools")](walkthrough-images/xcode03.png#lightbox)

With the Command Line Tools installed, we're ready to continue on with the walkthrough.

## <a name="walkthrough"></a>Passo a passo

In this walkthrough, we'll cover the following steps:

- **[Create a Static Library](#Creating_A_Static_Library)** - This step involves creating a static library of the **InfColorPicker** Objective-C code. The static library will have the `.a` file extension, and will be embedded into the .NET assembly of the library project.
- **[Create a Xamarin.iOS Binding Project](#Create_a_Xamarin.iOS_Binding_Project)** - Once we have a static library, we will use it to create a Xamarin.iOS binding project. The binding project consists of the static library we just created and metadata in the form of C# code that explains how the Objective-C API can be used. This metadata is commonly referred to as the API definitions. We'll use **[Objective Sharpie](#Using_Objective_Sharpie)** to help us with create the API definitions.
- **[Normalize the API Definitions](#Normalize_the_API_Definitions)** - Objective Sharpie does a great job of helping us, but it can't do everything. We'll discuss some changes that we need to make to the API definitions before they can be used.
- **[Use the Binding Library](#Using_the_Binding)** - Finally, we'll create a Xamarin.iOS application to show how to use our newly created binding project.

Now that we understand what steps are involved, let's move on to the rest of the walkthrough.

<a name="Creating_A_Static_Library"/>

## <a name="creating-a-static-library"></a>Creating A Static Library

If we inspect the code for InfColorPicker in Github:

[![](walkthrough-images/image02.png "Inspect the code for InfColorPicker in Github")](walkthrough-images/image02.png#lightbox)

We can see the following three directories in the project:

- **InfColorPicker** - This directory contains the Objective-C code for the project.
- **PickerSamplePad** - This directory contains a sample iPad project.
- **PickerSamplePhone** - This directory contains a sample iPhone project.

Let's download the InfColorPicker project from [GitHub](https://github.com/InfinitApps/InfColorPicker/archive/master.zip) and unzip it in the directory of our choosing. Abrindo o destino do Xcode para `PickerSamplePhone` projeto, vemos a seguinte estrutura de projeto no navegador Xcode:

[![](walkthrough-images/image03.png "The project structure in the Xcode Navigator")](walkthrough-images/image03.png#lightbox)

Esse projeto realiza a reutilização de código adicionando diretamente o código-fonte InfColorPicker (na caixa vermelha) em cada projeto de exemplo. O código para o projeto de exemplo está dentro da caixa azul. Como esse projeto específico não nos fornece uma biblioteca estática, é necessário para nós criar um projeto do Xcode para compilar a biblioteca estática.

A primeira etapa é adicionar o código-fonte InfoColorPicker à biblioteca estática. Para obter isso, vamos fazer o seguinte:

1. Inicie o Xcode.
2. No menu **arquivo** , selecione **novo** projeto de >  **...** :

    [![](walkthrough-images/image04.png "Starting a new project")](walkthrough-images/image04.png#lightbox)
3. Selecione **estrutura & biblioteca**, o modelo de **biblioteca estática Cocoa Touch** e clique no botão **Avançar** :

    [![](walkthrough-images/image05.png "Select the Cocoa Touch Static Library template")](walkthrough-images/image05.png#lightbox)

4. Insira `InfColorPicker` para o **nome do projeto** e clique no botão **Avançar** :

    [![](walkthrough-images/image06.png "Enter InfColorPicker for the Project Name")](walkthrough-images/image06.png#lightbox)
5. Selecione um local para salvar o projeto e clique no botão **OK** .
6. Agora precisamos adicionar a origem do projeto InfColorPicker ao nosso projeto de biblioteca estática. Como o arquivo **InfColorPicker. h** já existe em nossa biblioteca estática (por padrão), o Xcode não nos permitirá substituí-lo. No **localizador**, navegue até o código-fonte InfColorPicker no projeto original que descompactamos do GitHub, copie todos os arquivos InfColorPicker e cole-os em nosso novo projeto de biblioteca estática:

    [![](walkthrough-images/image12.png "Copy all of the InfColorPicker files")](walkthrough-images/image12.png#lightbox)

7. Retorne ao Xcode, clique com o botão direito do mouse na pasta **InfColorPicker** e selecione **Adicionar arquivos a "InfColorPicker..."** :

    [![](walkthrough-images/image08.png "Adding files")](walkthrough-images/image08.png#lightbox)

8. Na caixa de diálogo Adicionar arquivos, navegue até os arquivos de código-fonte do InfColorPicker que acabamos de copiar, selecione todos eles e clique no botão **Adicionar** :

    [![](walkthrough-images/image09.png "Select all and click the Add button")](walkthrough-images/image09.png#lightbox)

9. O código-fonte será copiado em nosso projeto:

    [![](walkthrough-images/image10.png "The source code will be copied into the project")](walkthrough-images/image10.png#lightbox)

10. No navegador de projeto do Xcode, selecione o arquivo **InfColorPicker. m** e comente as duas últimas linhas (devido à maneira como essa biblioteca foi gravada, esse arquivo não é usado):

    [![](walkthrough-images/image14.png "Editing the InfColorPicker.m file")](walkthrough-images/image14.png#lightbox)

11. Agora, precisamos verificar se há alguma estrutura exigida pela biblioteca. Você pode encontrar essas informações no arquivo LEIAme ou abrindo um dos projetos de exemplo fornecidos. Este exemplo usa `Foundation.framework`, `UIKit.framework`e `CoreGraphics.framework`, portanto, vamos adicioná-los.

12. Selecione o **destino InfColorPicker > fases de Build** e expanda a seção **vincular binário com bibliotecas** :

    [![](walkthrough-images/image16b.png "Expand the Link Binary With Libraries section")](walkthrough-images/image16b.png#lightbox)

13. Use o botão **+** para abrir a caixa de diálogo, permitindo que você adicione as estruturas de quadros necessárias listadas acima:

    [![](walkthrough-images/image16c.png "Add the required frames frameworks listed above")](walkthrough-images/image16c.png#lightbox)

14. A seção **vincular binário com bibliotecas** agora deve ser parecida com a imagem abaixo:

    [![](walkthrough-images/image16d.png "The Link Binary With Libraries section")](walkthrough-images/image16d.png#lightbox)

Neste ponto, estamos próximos, mas não estamos prontos. A biblioteca estática foi criada, mas precisamos criá-la para criar um binário de Fat que inclui todas as arquiteturas necessárias para o dispositivo iOS e o simulador de iOS.

### <a name="creating-a-fat-binary"></a>Criando um binário do Fat

Todos os dispositivos iOS têm processadores da plataforma ARM desenvolvidos com o passar do tempo. Cada nova arquitetura adicionou novas instruções e outros aprimoramentos e, ao mesmo tempo, mantém a compatibilidade com versões anteriores. os dispositivos iOS têm conjuntos de instruções ARMv6, ARMv7, armv7s, arm64, embora [ARMv6 não sejam mais usados](~/ios/deploy-test/compiling-for-different-devices.md). O simulador do iOS não é alimentado pelo ARM e, em vez disso, é um simulador equipado com x86 e x86_64. Isso significa que as bibliotecas devem ser fornecidas para cada conjunto de instruções.

Uma biblioteca de Fat é `.a` arquivo que contém todas as arquiteturas com suporte.

A criação de um binário de Fat é um processo de três etapas:

- Compile uma versão de & ARM64 do ARM 7 da biblioteca estática.
- Compile uma versão x86 e x84_64 da biblioteca estática.
- Use a ferramenta de linha de comando `lipo` para combinar as duas bibliotecas estáticas em uma.

Embora essas três etapas sejam bem simples, pode ser necessário repeti-las no futuro quando a biblioteca Objective-C receber atualizações ou se exigirem correções de bugs. Se você decidir automatizar essas etapas, ele simplificará a manutenção futura e o suporte do projeto de associação do iOS.

Há muitas ferramentas disponíveis para automatizar tarefas – um script de Shell, [Rake](https://rake.rubyforge.org/), [xbuild](https://www.mono-project.com/docs/tools+libraries/tools/xbuild/)e [Make](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/make.1.html). Quando as ferramentas de linha de comando do Xcode são instaladas, o `make` também é instalado, de modo que é o sistema de compilação que será usado para este passo a passos. Aqui está um **makefile** que você pode usar para criar uma biblioteca compartilhada com várias arquiteturas que funcionará em um dispositivo IOS e o simulador para qualquer biblioteca:

<!--markdownlint-disable MD010 -->
```makefile
XBUILD=/Applications/Xcode.app/Contents/Developer/usr/bin/xcodebuild
PROJECT_ROOT=./YOUR-PROJECT-NAME
PROJECT=$(PROJECT_ROOT)/YOUR-PROJECT-NAME.xcodeproj
TARGET=YOUR-PROJECT-NAME

all: lib$(TARGET).a

lib$(TARGET)-i386.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphonesimulator -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphonesimulator/lib$(TARGET).a $@

lib$(TARGET)-armv7.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch armv7 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

lib$(TARGET)-arm64.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch arm64 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

lib$(TARGET).a: lib$(TARGET)-i386.a lib$(TARGET)-armv7.a lib$(TARGET)-arm64.a
    xcrun -sdk iphoneos lipo -create -output $@ $^

clean:
    -rm -f *.a *.dll
```
<!--markdownlint-enable MD010 -->

Insira os comandos **makefile** no editor de texto sem formatação de sua escolha e atualize as seções com **seu-Project-Name** com o nome do seu projeto. Também é importante garantir que você cole as instruções acima exatamente, com as guias dentro das instruções preservadas.

Salve o arquivo com o nome **makefile** no mesmo local que a biblioteca estática do Xcode InfColorPicker que criamos acima:

[![](walkthrough-images/lib00.png "Save the file with the name Makefile")](walkthrough-images/lib00.png#lightbox)

Abra o aplicativo de terminal no seu Mac e navegue até o local do seu makefile. Digite `make` no terminal, pressione **Enter** e o **makefile** será executado:

[![](walkthrough-images/lib01.png "Sample makefile output")](walkthrough-images/lib01.png#lightbox)

Ao executar make, você verá muita rolagem de texto por. Se tudo funcionou corretamente, você verá que as palavras **foram criadas com êxito** e o `libInfColorPicker-armv7.a`, `libInfColorPicker-i386.a` e `libInfColorPickerSDK.a` arquivos serão copiados para o mesmo local que o **makefile**:

[![](walkthrough-images/lib02.png "The libInfColorPicker-armv7.a, libInfColorPicker-i386.a and libInfColorPickerSDK.a files generated by the Makefile")](walkthrough-images/lib02.png#lightbox)

Você pode confirmar as arquiteturas em seu binário do Fat usando o seguinte comando:

```bash
xcrun -sdk iphoneos lipo -info libInfColorPicker.a
```

Isso deve exibir o seguinte:

```bash
Architectures in the fat file: libInfColorPicker.a are: i386 armv7 x86_64 arm64
```

Neste ponto, concluimos a primeira etapa de nossa associação do iOS criando uma biblioteca estática usando o Xcode e as ferramentas de linha de comando do Xcode `make` e `lipo`. Vamos passar para a próxima etapa e usar a **nitidez do objetivo** para automatizar a criação das associações de API para nós.

<a name="Create_a_Xamarin.iOS_Binding_Project"/>

## <a name="create-a-xamarinios-binding-project"></a>Criar um projeto de associação do Xamarin. iOS

Antes que possamos usar a **nitidez do objetivo** para automatizar o processo de ligação, precisamos criar um projeto de associação do Xamarin. Ios para alojar as definições de API (que usaremos a nitidez do **objetivo** para nos ajudar a criar) e criar C# a associação para nós.

Vamos fazer o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Iniciar Visual Studio para Mac.
1. No menu **arquivo** , selecione **nova** solução de >  **...** :

    ![](walkthrough-images/bind01.png "Starting a new solution")

1. Na caixa de diálogo nova solução, selecione **biblioteca** > **projeto de associação Ios**:

    ![](walkthrough-images/bind02.png "Select iOS Binding Project")

1. Clique no botão **Avançar** .

1. Digite "InfColorPickerBinding" como o **nome do projeto** e clique no botão **criar** para criar a solução:

    ![](walkthrough-images/bind02a.png "Enter InfColorPickerBinding as the Project Name")

A solução será criada e dois arquivos padrão serão incluídos:

![](walkthrough-images/bind03.png "The solution structure in the Solution Explorer")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Inicie o Visual Studio.

1. No menu **arquivo** , selecione **novo** projeto de >  **...** :

    ![Iniciando um novo projeto](walkthrough-images/bind01vs.png "Starting a new project")

1. Na caixa de diálogo novo projeto, selecione **Visual C# > iPhone & iPad > biblioteca de associações do IOS (Xamarin)** :

    [![selecionar a biblioteca de associações do iOS](walkthrough-images/bind02.w157-sml.png)](walkthrough-images/bind02.w157.png#lightbox)

1. Digite "InfColorPickerBinding" como o **nome** e clique no botão **OK** para criar a solução.

A solução será criada e dois arquivos padrão serão incluídos:

![](walkthrough-images/bind03vs.png "The solution structure in the Solution Explorer")

-----

- **ApiDefinition.cs** -este arquivo conterá os contratos que definem como as APIs Objective-C serão encapsuladas C#.
- **Structs.cs** -esse arquivo terá quaisquer estruturas ou valores de enumeração exigidos pelas interfaces e pelos delegados.

Vamos trabalhar com esses dois arquivos mais adiante neste guia. Primeiro, precisamos adicionar a biblioteca InfColorPicker ao projeto de associação.

### <a name="including-the-static-library-in-the-binding-project"></a>Incluindo a biblioteca estática no projeto de associação

Agora temos nosso projeto de associação base pronto, precisamos adicionar a biblioteca binária de Fat que criamos acima para a biblioteca **InfColorPicker** .

Siga estas etapas para adicionar a biblioteca:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Clique com o botão direito do mouse na pasta **referências nativas** no painel de soluções e selecione **Adicionar referências nativas**:

    ![](walkthrough-images/bind04a.png "Add Native References")

1. Navegue até o binário de Fat que fizemos anteriormente (`libInfColorPickerSDK.a`) e pressione o botão **abrir** :

    ![](walkthrough-images/bind05.png "Select the libInfColorPickerSDK.a file")
1. O arquivo será incluído no projeto:

    ![](walkthrough-images/bind04.png "Including a file")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Copie o `libInfColorPickerSDK.a` do seu **host de Build do Mac** e cole-o em seu projeto de associação.

1. Clique com o botão direito do mouse no projeto e escolha **adicionar > item existente...** :

    ![](walkthrough-images/bind04vs.png "Adding an existing file")

1. Navegue até a `libInfColorPickerSDK.a` e pressione o botão **Adicionar** :

    ![](walkthrough-images/bind05vs.png "Adding libInfColorPickerSDK.a")

1. O arquivo será incluído no projeto.

-----

Quando o arquivo **. a** é adicionado ao projeto, o Xamarin. Ios definirá automaticamente a **ação de Build** do arquivo como **ObjcBindingNativeLibrary**e criará um arquivo especial chamado `libInfColorPickerSDK.linkwith.cs`.

Esse arquivo contém o atributo `LinkWith` que informa ao Xamarin. iOS como lidar com a biblioteca estática que acabamos de adicionar. O conteúdo desse arquivo é mostrado no seguinte trecho de código:

```csharp
using ObjCRuntime;

[assembly: LinkWith ("libInfColorPickerSDK.a", SmartLink = true, ForceLoad = true)]
```

O atributo `LinkWith` identifica a biblioteca estática para o projeto e alguns sinalizadores de vinculador importantes.

A próxima coisa que precisamos fazer é criar as definições de API para o projeto InfColorPicker. Para os fins deste passo a passos, usaremos a nitidez objetiva para gerar o arquivo **ApiDefinition.cs**.

<a name="Using_Objective_Sharpie"/>

## <a name="using-objective-sharpie"></a>Usando a nitidez do objetivo

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

A Sharpde objetiva é uma ferramenta de linha de comando (fornecida pelo Xamarin) que pode ajudar a criar as definições necessárias para associar uma biblioteca de Objective C#-C de terceiros ao. Nesta seção, usaremos a nitidez objetiva para criar o **ApiDefinition.cs** inicial para o projeto InfColorPicker.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

A Sharpde objetiva é uma ferramenta de linha de comando (fornecida pelo Xamarin) que pode ajudar a criar as definições necessárias para associar uma biblioteca de Objective C#-C de terceiros ao. Nesta seção, usaremos a nitidez do objetivo em nosso host de **Build do Mac** para criar o **ApiDefinition.cs** inicial para o projeto InfColorPicker.

-----

Para começar, vamos baixar o arquivo do instalador de nitidez do objetivo conforme detalhado neste [guia](~/cross-platform/macios/binding/objective-sharpie/get-started.md#installing). Execute o instalador e siga todos os prompts na tela do assistente de instalação para instalar a nitidez do objetivo em nosso computador de desenvolvimento.

Após a instalação bem-sucedida do Objective, vamos iniciar o aplicativo terminal e inserir o seguinte comando para obter ajuda sobre todas as ferramentas que ele fornece para auxiliar na ligação:

```bash
sharpie -help
```

Se executarmos o comando acima, a seguinte saída será gerada:

```bash
Europa:Resources kmullins$ sharpie -help
usage: sharpie [OPTIONS] TOOL [TOOL_OPTIONS]

Options:
  -h, --helpShow detailed help
  -v, --versionShow version information

Available Tools:

  xcode    Get information about Xcode installations and available SDKs.

  bind     Create a Xamarin C# binding to Objective-C APIs
Europa:Resources kmullins$
```

Para a finalidade deste guia, usaremos as seguintes ferramentas de nitidez objetivas:

- **Xcode** – essas ferramentas nos fornecem informações sobre a instalação atual do Xcode e as versões das APIs do IOS e Mac que instalamos. Usaremos essas informações posteriormente quando gerarmos nossas associações.
- **BIND** – usaremos essa ferramenta para analisar os arquivos **. h** no projeto InfColorPicker nos arquivos **ApiDefinition.cs** e **StructsAndEnums.cs** iniciais.

Para obter ajuda sobre uma ferramenta de nitidez de objetivo específica, digite o nome da ferramenta e a opção de `-help`. Por exemplo, `sharpie xcode -help` retorna a seguinte saída:

```bash
Europa:Resources kmullins$ sharpie xcode -help
usage: sharpie xcode [OPTIONS]+

Options:
  -h, --help                 Show detailed help
  -v, --verbose              Be verbose with output
      --sdks                 List all available Xcode SDKs. Pass -verbose for
                               more details.
Europa:Resources kmullins$
```

Antes que possamos iniciar o processo de associação, precisamos obter informações sobre nossos SDKs instalados atuais digitando o seguinte comando no terminal `sharpie xcode -sdks`:

```bash
amyb:Desktop amyb$ sharpie xcode -sdks
sdk: appletvos9.2    arch: arm64
sdk: iphoneos9.3     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: watchos2.2      arch: armv7
```

A partir do acima, podemos ver que temos o SDK do `iphoneos9.3` instalado em nosso computador. Com essas informações em vigor, estamos prontos para analisar o projeto InfColorPicker `.h` arquivos na **ApiDefinition.cs** inicial e `StructsAndEnums.cs` para o projeto InfColorPicker.

Insira o seguinte comando no aplicativo de terminal:

```bash
sharpie bind --output=InfColorPicker --namespace=InfColorPicker --sdk=[iphone-os] [full-path-to-project]/InfColorPicker/InfColorPicker/*.h
```

Em que `[full-path-to-project]` é o caminho completo para o diretório em que o arquivo de projeto do **InfColorPicker** Xcode está localizado em nosso computador e [iPhone-os] é o SDK do IOS que instalamos, conforme observado pelo comando `sharpie xcode -sdks`. Observe que, neste exemplo, passamos **\*. h** como um parâmetro, que inclui *todos* os arquivos de cabeçalho nesse diretório, normalmente você não deve fazer isso, mas, em vez disso, ler atentamente os arquivos de cabeçalho para localizar o arquivo **. h** de nível superior Isso faz referência a todos os outros arquivos relevantes e só o passa para a nitidez objetiva.

A seguinte [saída](walkthrough-images/os05.png) será gerada no terminal:

```bash
Europa:Resources kmullins$ sharpie bind -output InfColorPicker -namespace InfColorPicker -sdk iphoneos8.1 /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h -unified
Compiler configuration:
    -isysroot /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdk -miphoneos-version-min=8.1 -resource-dir /Library/Frameworks/ObjectiveSharpie.framework/Versions/1.1.1/clang-resources -arch armv7 -ObjC

[  0%] parsing /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h
In file included from /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h:60:
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:28:1: warning: no 'assign',
      'retain', or 'copy' attribute is specified - 'assign' is assumed [-Wobjc-property-no-attribute]
@property (nonatomic) UIColor* sourceColor;
^
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:28:1: warning: default property
      attribute 'assign' not appropriate for non-GC object [-Wobjc-property-no-attribute]
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:29:1: warning: no 'assign',
      'retain', or 'copy' attribute is specified - 'assign' is assumed [-Wobjc-property-no-attribute]
@property (nonatomic) UIColor* resultColor;
^
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:29:1: warning: default property
      attribute 'assign' not appropriate for non-GC object [-Wobjc-property-no-attribute]
4 warnings generated.
[100%] parsing complete
[bind] InfColorPicker.cs
Europa:Resources kmullins$
```

E os arquivos **InfColorPicker.Enums.cs** e **InfColorPicker.cs** serão criados em nosso diretório:

[![](walkthrough-images/os06.png "The InfColorPicker.enums.cs and InfColorPicker.cs files")](walkthrough-images/os06.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Abra esses dois arquivos no projeto de associação que criamos acima. Copie o conteúdo do arquivo **InfColorPicker.cs** e cole-o no arquivo **ApiDefinition.cs** , substituindo o bloco de código de `namespace ...` existente pelo conteúdo do arquivo **InfColorPicker.cs** (deixando as instruções `using` intactas):

![](walkthrough-images/os07.png "The InfColorPickerControllerDelegate file")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Abra esses dois arquivos no projeto de associação que criamos acima. Copie o conteúdo do arquivo **InfColorPicker.cs** (do host de **Build do Mac**) e cole-o no arquivo **ApiDefinition.cs** , substituindo o bloco de código de `namespace ...` existente pelo conteúdo do arquivo **InfColorPicker.cs** (deixando as instruções `using` intactas).

-----

<a name="Normalize_the_API_Definitions"/>

## <a name="normalize-the-api-definitions"></a>Normalizar as definições de API

Às vezes, a nitidez do objetivo tem um problema ao traduzir `Delegates`, portanto, precisaremos modificar a definição da interface `InfColorPickerControllerDelegate` e substituir a linha de `[Protocol, Model]` pelo seguinte:

```csharp
[BaseType(typeof(NSObject))]
[Model]
```

Para que a definição seja parecida com:

[![](walkthrough-images/os11.png "The definition")](walkthrough-images/os11.png#lightbox)

Em seguida, fazemos a mesma coisa com o conteúdo do arquivo `InfColorPicker.enums.cs`, copiando e colando-os no arquivo `StructsAndEnums.cs` deixando as instruções `using` intactas:

[![](walkthrough-images/os09.png "The contents the StructsAndEnums.cs file ")](walkthrough-images/os09.png#lightbox)

Você também pode achar que a nitidez do objetivo anotava a associação com `[Verify]` atributos. Esses atributos indicam que você deve verificar se a nitidez objetiva fez a coisa correta comparando a associação com a declaração C/Objective-C original (que será fornecida em um comentário acima da declaração associada). Depois de verificar as associações, você deve remover o atributo Verify. Para obter mais informações, consulte o guia de [verificação](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) .

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Neste ponto, nosso projeto de associação deve estar completo e pronto para ser criado. Vamos criar nosso projeto de associação e certificar-se de que terminamos sem erros:

[Compile o projeto de associação e verifique se não há erros](walkthrough-images/os12.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Neste ponto, nosso projeto de associação deve estar completo e pronto para ser criado. Vamos criar nosso projeto de associação e certificar-se de que terminamos sem erros.

-----

<a name="Using_the_Binding"/>

## <a name="using-the-binding"></a>Usando a associação

Siga estas etapas para criar um aplicativo de exemplo do iPhone para usar a biblioteca de associação do iOS criada acima:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. **Criar projeto xamarin. Ios** -adicione um novo projeto Xamarin. Ios chamado **InfColorPickerSample** à solução, conforme mostrado nas seguintes capturas de tela:

    ![](walkthrough-images/use01.png "Adding a Single View App")

    ![](walkthrough-images/use01a.png "Setting the Identifier")

1. **Adicionar referência ao projeto de associação** -atualize o projeto **InfColorPickerSample** para que ele tenha uma referência ao projeto **InfColorPickerBinding** :

    ![](walkthrough-images/use02.png "Adding Reference to the Binding Project")

1. **Create the iPhone User Interface** - Double click on the **MainStoryboard.storyboard** file in the **InfColorPickerSample** project to edit it in the iOS Designer. Add a **Button** to the view and call it `ChangeColorButton`, as shown in the following:

    ![](walkthrough-images/use03.png "Adding a Button to the view")

1. **Add the InfColorPickerView.xib** - The InfColorPicker Objective-C library includes a **.xib** file. Xamarin.iOS will not include this **.xib** in the binding project, which will cause run-time errors in our sample application. A solução alternativa para isso é adicionar o arquivo **. xib** ao nosso projeto Xamarin. Ios. Selecione o projeto Xamarin. iOS, clique com o botão direito do mouse e selecione **adicionar > adicionar arquivos**e adicione o arquivo **. xib** , conforme mostrado na seguinte captura de tela:

    ![](walkthrough-images/use04.png "Add the InfColorPickerView.xib")

1. Quando solicitado, copie o arquivo **. xib** no projeto.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. **Criar projeto xamarin. Ios** -adicione um novo projeto Xamarin. Ios chamado **InfColorPickerSample** usando o modelo de **aplicativo de modo de exibição único** :

    [![projeto do aplicativo iOS (Xamarin)](walkthrough-images/use01.w157-sml.png)](walkthrough-images/use01.w157.png#lightbox)

    [![selecionar modelo](walkthrough-images/use01-2.w157-sml.png)](walkthrough-images/use01-2.w157.png#lightbox)

1. **Adicionar referência ao projeto de associação** -atualize o projeto **InfColorPickerSample** para que ele tenha uma referência ao projeto **InfColorPickerBinding** :

    ![](walkthrough-images/use02vs.png "Add Reference to the Binding Project")

1. **Create the iPhone User Interface** - Double click on the **MainStoryboard.storyboard** file in the **InfColorPickerSample** project to edit it in the iOS Designer. Add a **Button** to the view and call it `ChangeColorButton`, as shown in the following:

    ![](walkthrough-images/use03vs.png "Create the iPhone User Interface")

1. **Add the InfColorPickerView.xib** - The InfColorPicker Objective-C library includes a **.xib** file. Xamarin.iOS will not include this **.xib** in the binding project, which will cause run-time errors in our sample application. The workaround for this is to add the **.xib** file to our Xamarin.iOS project from our **Mac Build Host**. Select the Xamarin.iOS project, right-click and select **Add** > **Existing Item...** , and add the **.xib** file.

-----

Next, let's take a quick look at Protocols in Objective-C and how we handle them in binding and C# code.

### <a name="protocols-and-xamarinios"></a>Protocols and Xamarin.iOS

In Objective-C, a protocol defines methods (or messages) that can be used in certain circumstances. Conceptually, they are very similar to interfaces in C#. One major difference between an Objective-C protocol and a C# interface is that protocols can have optional methods - methods that a class does not have to implement. Objective-C uses the @optional keyword is used to indicate which methods are optional. For more information on protocols see [Events, Protocols and Delegates](~/ios/app-fundamentals/delegates-protocols-and-events.md).

**InfColorPickerController** has one such protocol, shown in the code snippet below:

```csharp
@protocol InfColorPickerControllerDelegate

@optional

- (void) colorPickerControllerDidFinish: (InfColorPickerController*) controller;
// This is only called when the color picker is presented modally.

- (void) colorPickerControllerDidChangeColor: (InfColorPickerController*) controller;

@end
```

This protocol is used by **InfColorPickerController** to inform clients that the user has picked a new color and that the **InfColorPickerController** is finished. Objective Sharpie mapped this protocol as shown in the following code snippet:

```csharp
[BaseType(typeof(NSObject))]
[Model]
public partial interface InfColorPickerControllerDelegate {

    [Export ("colorPickerControllerDidFinish:")]
    void ColorPickerControllerDidFinish (InfColorPickerController controller);

    [Export ("colorPickerControllerDidChangeColor:")]
    void ColorPickerControllerDidChangeColor (InfColorPickerController controller);
}

```

When the binding library is compiled, Xamarin.iOS will create an abstract base class called `InfColorPickerControllerDelegate`, which implements this interface with virtual methods.

There are two ways that we can implement this interface in a Xamarin.iOS application:

- **Strong Delegate** - Using a strong delegate involves creating a C# class that subclasses `InfColorPickerControllerDelegate` and overrides the appropriate methods. **InfColorPickerController** will use an instance of this class to communicate with its clients.
- **Weak Delegate** - A weak delegate is a slightly different technique that involves creating a public method on some class (such as `InfColorPickerSampleViewController`) and then exposing that method to the `InfColorPickerDelegate` protocol via an `Export` attribute.

Strong delegates provide Intellisense, type safety, and better encapsulation. For these reasons, you should use strong delegates where you can, instead of a weak delegate.

In this walkthrough we will discuss both techniques: first implementing a strong delegate, and then explaining how to implement a weak delegate.

### <a name="implementing-a-strong-delegate"></a>Implementing a Strong Delegate

Finish the Xamarin.iOS application by using a strong delegate to respond to the `colorPickerControllerDidFinish:` message:

**Subclass InfColorPickerControllerDelegate** - Add a new class to the project called `ColorSelectedDelegate`. Edit the class so that it has the following code:

```csharp
using InfColorPickerBinding;
using UIKit;

namespace InfColorPickerSample
{
  public class ColorSelectedDelegate:InfColorPickerControllerDelegate
  {
    readonly UIViewController parent;

    public ColorSelectedDelegate (UIViewController parent)
    {
      this.parent = parent;
    }

    public override void ColorPickerControllerDidFinish (InfColorPickerController controller)
    {
      parent.View.BackgroundColor = controller.ResultColor;
      parent.DismissViewController (false, null);
    }
  }
}
```

Xamarin.iOS will bind the Objective-C delegate by creating an abstract base class called `InfColorPickerControllerDelegate`. Subclass this type and override the `ColorPickerControllerDidFinish` method to access the value of the `ResultColor` property of `InfColorPickerController`.

**Create a instance of ColorSelectedDelegate** - Our event handler will need an instance of the `ColorSelectedDelegate` type that we created in the previous step. Edit the class `InfColorPickerSampleViewController` and add the following instance variable to the class:

```csharp
ColorSelectedDelegate selector;
```

**Inicialize a variável ColorSelectedDelegate** – para garantir que `selector` seja uma instância válida, atualize o método `ViewDidLoad` em `ViewController` para corresponder ao seguinte trecho de código:

```csharp
public override void ViewDidLoad ()
{
  base.ViewDidLoad ();
  ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithStrongDelegate;
  selector = new ColorSelectedDelegate (this);
}
```

**Implemente o método HandleTouchUpInsideWithStrongDelegate** -Next implemente o manipulador de eventos para quando o usuário tocar em **ColorChangeButton**. Edite `ViewController`e adicione o seguinte método:

```csharp
using InfColorPicker;
...

private void HandleTouchUpInsideWithStrongDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.Delegate = selector;
    picker.PresentModallyOverViewController (this);
}

```

Primeiro, obtemos uma instância de `InfColorPickerController` por meio de um método estático e fazemos com que essa instância reconheça nosso forte delegado por meio da propriedade `InfColorPickerController.Delegate`. Essa propriedade foi gerada automaticamente para nós por nitidez objetiva. Por fim, chamamos `PresentModallyOverViewController` para mostrar a exibição `InfColorPickerSampleViewController.xib` para que o usuário possa selecionar uma cor.

**Execute o aplicativo** – neste ponto, fizemos todo o nosso código. Se você executar o aplicativo, deverá ser capaz de alterar a cor da tela de fundo do `InfColorColorPickerSampleView`, conforme mostrado nas capturas de tela a seguir:

[![](walkthrough-images/run01.png "Running the Application")](walkthrough-images/run01.png#lightbox)

Parabéns! Neste ponto, você criou e com êxito uma biblioteca Objective-C para uso em um aplicativo Xamarin. Ios. Em seguida, vamos aprender sobre o uso de delegados fracos.

### <a name="implementing-a-weak-delegate"></a>Implementando um delegado fraco

Em vez de subclasser uma classe associada ao protocolo Objective-C para um determinado delegado, o Xamarin. iOS também permite que você implemente os métodos de protocolo em qualquer classe derivada de `NSObject`, decorando seus métodos com o `ExportAttribute`e, em seguida, fornecendo o seletores apropriados. Ao usar essa abordagem, você atribui uma instância de sua classe à propriedade `WeakDelegate` em vez de à propriedade `Delegate`. Um delegado fraco oferece a você a flexibilidade de levar sua classe de representante para uma hierarquia de herança diferente. Vejamos como implementar e usar um delegado fraco em nosso aplicativo Xamarin. iOS.

**Criar manipulador de eventos para TouchUpInside** – vamos criar um novo manipulador de eventos para o evento `TouchUpInside` do botão Alterar cor da fundo. Esse manipulador preencherá a mesma função que o manipulador de `HandleTouchUpInsideWithStrongDelegate` que criamos na seção anterior, mas usará um delegado fraco em vez de um delegado forte. Edite a classe `ViewController`e adicione o seguinte método:

```csharp
private void HandleTouchUpInsideWithWeakDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.WeakDelegate = this;
    picker.SourceColor = this.View.BackgroundColor;
    picker.PresentModallyOverViewController (this);
}
```

**Atualizar ViewDidLoad** -é necessário alterar `ViewDidLoad` para que ele use o manipulador de eventos que acabamos de criar. Edite `ViewController` e altere `ViewDidLoad` para se parecer com o seguinte trecho de código:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithWeakDelegate;
}

```

**Manipular o colorPickerControllerDidFinish: Message** -quando o `ViewController` for concluído, o Ios enviará a mensagem `colorPickerControllerDidFinish:` para o `WeakDelegate`. Precisamos criar um C# método que possa manipular essa mensagem. Para fazer isso, criamos um C# método e, em seguida, adornamos com o`ExportAttribute`. Edite `ViewController`e adicione o seguinte método à classe:

```csharp
[Export("colorPickerControllerDidFinish:")]
public void ColorPickerControllerDidFinish (InfColorPickerController controller)
{
    View.BackgroundColor = controller.ResultColor;
    DismissViewController (false, null);
}

```

Execute o aplicativo. Agora ele deve se comportar exatamente como fazia antes, mas está usando um delegado fraco em vez do delegado forte. Neste ponto, você concluiu com êxito este guia. Agora você deve ter uma compreensão de como criar e consumir um projeto de associação do Xamarin. iOS.

## <a name="summary"></a>Resumo

Este artigo descreveu o processo de criação e uso de um projeto de associação do Xamarin. iOS. Primeiro, discutimos como compilar uma biblioteca Objective-C existente em uma biblioteca estática. Em seguida, abordamos como criar um projeto de associação Xamarin. iOS e como usar a nitidez objetiva para gerar as definições de API para a biblioteca Objective-C. Discutimos como atualizar e ajustar as definições de API geradas para torná-las adequadas para o consumo público. After the Xamarin.iOS binding project was finished, we moved on to consuming that binding in a Xamarin.iOS application, with a focus on using strong delegates and weak delegates.

## <a name="related-links"></a>Links relacionados

- [Binding Example (sample)](https://docs.microsoft.com/samples/xamarin/ios-samples/infcolorpicker)
- [Associação de bibliotecas de Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Binding Details](~/cross-platform/macios/binding/overview.md)
- [Binding Types Reference Guide](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin para desenvolvedores de Objective-C](~/ios/get-started/objective-c-developers/index.md)
- [Diretrizes de design do Framework](https://msdn.microsoft.com/library/ms229042.aspx)
