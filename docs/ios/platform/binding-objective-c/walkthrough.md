---
title: 'Walkthrough: associando uma biblioteca de Objective do iOS-C'
description: Este artigo fornece uma explicação prática de como criar uma associação do Xamarin. iOS para uma biblioteca Objective-C existente, InfColorPicker. Ele aborda tópicos como a compilação de uma biblioteca Objective-C estática, sua associação e o uso da associação em um aplicativo Xamarin. iOS.
ms.prod: xamarin
ms.assetid: D3F6FFA0-3C4B-4969-9B83-B6020B522F57
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2017
ms.openlocfilehash: 328633bc58f17216c071a2b2cd779704da2bbf74
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84569407"
---
# <a name="walkthrough-binding-an-ios-objective-c-library"></a>Walkthrough: associando uma biblioteca de Objective do iOS-C

_Este artigo fornece uma explicação prática de como criar uma associação do Xamarin. iOS para uma biblioteca Objective-C existente, InfColorPicker. Ele aborda tópicos como a compilação de uma biblioteca Objective-C estática, sua associação e o uso da associação em um aplicativo Xamarin. iOS._

Ao trabalhar no iOS, você pode encontrar casos em que deseja consumir uma biblioteca de Objective-C de terceiros. Nessas situações, você pode usar um _projeto de associação_ do Xamarin. Ios para criar uma [Associação C#](~/cross-platform/macios/binding/overview.md) que permitirá que você consuma a biblioteca em seus aplicativos Xamarin. Ios.

Geralmente, no ecossistema do iOS, você pode encontrar bibliotecas em 3 tipos:

- Como um arquivo de biblioteca estática pré-compilado com `.a` extensão junto com seus cabeçalhos (arquivos. h). Por exemplo, [biblioteca de análise do Google](https://developers.google.com/analytics/devguides/collection/ios/v3/sdk-download?hl=es#download_sdk)
- Como uma estrutura pré-compilada. Essa é apenas uma pasta que contém a biblioteca estática, os cabeçalhos e, às vezes, recursos adicionais com `.framework` extensão. Por exemplo, a [biblioteca o AdMob do Google](https://developers.google.com/admob/ios/download).
- Como apenas arquivos de código-fonte. Por exemplo, uma biblioteca que contém `.m` arquivos apenas e `.h` Objective C.

No primeiro e segundo cenário, já haverá uma biblioteca estática CocoaTouch precompilada, portanto, neste artigo, nos concentraremos no terceiro cenário. Lembre-se, antes de começar a criar uma associação, sempre verifique a licença fornecida com a biblioteca para garantir que você esteja livre para associá-la.

Este artigo fornece uma explicação passo a passo de como criar um projeto de associação usando o projeto de [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) de software livre Objective-C como um exemplo, no entanto, todas as informações neste guia podem ser adaptadas para uso com qualquer biblioteca Objective-c de terceiros. A biblioteca InfColorPicker fornece um controlador de exibição reutilizável que permite ao usuário selecionar uma cor com base em sua representação HSB, tornando a seleção de cores mais amigável.

[![](walkthrough-images/run01.png "Example of the InfColorPicker library running on iOS")](walkthrough-images/run01.png#lightbox)

Abordaremos todas as etapas necessárias para consumir essa API Objective-C específica no Xamarin. iOS:

- Primeiro, criaremos uma biblioteca estática Objective-C usando o Xcode.
- Em seguida, ligaremos essa biblioteca estática com o Xamarin. iOS.
- Em seguida, mostre como a nitidez do objetivo pode reduzir a carga de trabalho gerando automaticamente algumas (mas não todas) as definições de API necessárias exigidas pela associação do Xamarin. iOS.
- Por fim, criaremos um aplicativo Xamarin. iOS que usa a associação.

O aplicativo de exemplo demonstrará como usar um delegado forte para a comunicação entre a API InfColorPicker e nosso código C#. Depois de observarmos como usar um delegado forte, abordaremos como usar delegados fracos para executar as mesmas tarefas.

## <a name="requirements"></a>Requisitos

Este artigo pressupõe que você tenha alguma familiaridade com o Xcode e com a linguagem Objective-C e tenha lido nossa documentação [de Binding Objective-c](~/cross-platform/macios/binding/index.md) . Além disso, é necessário o seguinte para concluir as etapas apresentadas:

- O **Xcode e o SDK do IOS** – o Xcode da Apple e a mais recente API do IOS precisam ser instalados e configurados no computador do desenvolvedor.
- **[Ferramentas de linha de comando do Xcode](#Installing_the_Xcode_Command_Line_Tools)** – as ferramentas de linha de comando do Xcode devem ser instaladas para a versão atualmente instalada do Xcode (veja abaixo os detalhes da instalação).
- **Visual Studio para Mac ou Visual Studio** -a versão mais recente do Visual Studio para Mac ou do Visual Studio deve ser instalada e configurada no computador de desenvolvimento. Um Apple Mac é necessário para desenvolver um aplicativo Xamarin. iOS e, ao usar o Visual Studio, você deve estar conectado a [um host de Build do xamarin. Ios](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
- **A última versão do objetivo de Objective** -uma cópia atual da ferramenta de nitidez objetiva baixada [daqui.](~/cross-platform/macios/binding/objective-sharpie/get-started.md) Se você já tiver a nitidez do objetivo instalada, poderá atualizá-la para a versão mais recente usando o`sharpie update`

<a name="Installing_the_Xcode_Command_Line_Tools"></a>

## <a name="installing-the-xcode-command-line-tools"></a>Instalando as ferramentas de linha de comando do Xcode

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Conforme mencionado acima, usaremos as ferramentas de linha de comando do Xcode (especificamente `make` e `lipo` ) neste passo a passos. O `make` comando é um utilitário UNIX muito comum que automatizará a compilação de programas e bibliotecas executáveis usando um _makefile_ que especifica como o programa deve ser compilado. O `lipo` comando é um utilitário de linha de comando do os X para criar arquivos com várias arquiteturas; ele combinará vários `.a` arquivos em um arquivo que pode ser usado por todas as arquiteturas de hardware.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Como mencionado acima, usaremos as ferramentas de linha de comando do Xcode no **host de Build do Mac** (especificamente `make` e `lipo` ) neste passo a passos. O `make` comando é um utilitário UNIX muito comum que automatizará a compilação de programas e bibliotecas executáveis usando um _makefile_ para especificar como criar o programa. O `lipo` comando é um utilitário de linha de comando do os X para criar arquivos com várias arquiteturas; ele combinará vários `.a` arquivos em um arquivo que pode ser usado por todas as arquiteturas de hardware.

-----

De acordo com a [criação da Apple na linha de comando com a documentação das perguntas frequentes sobre o Xcode](https://developer.apple.com/library/ios/technotes/tn2339/_index.html) , no OS X 10,9 e superior, o painel **downloads** da caixa de diálogo **preferências** do Xcode não dá mais suporte ao download de ferramentas de linha de comando.

Você precisará usar um dos seguintes métodos para instalar as ferramentas:

- **Instalar o Xcode** – quando você instala o Xcode, ele vem em conjunto com todas as suas ferramentas de linha de comando. Em shims do OS X 10,9 (instalados em `/usr/bin` ), o pode mapear qualquer ferramenta incluída no `/usr/bin` para a ferramenta correspondente dentro do Xcode. Por exemplo, o `xcrun` comando, que permite localizar ou executar qualquer ferramenta dentro do Xcode na linha de comando.
- **Aplicativo de terminal** -do aplicativo de terminal, você pode instalar as ferramentas de linha de comando executando o `xcode-select --install` comando:
  - Inicie o aplicativo de terminal.
  - Digite `xcode-select --install` e pressione **Enter**, por exemplo:

  ```bash
  Europa:~ kmullins$ xcode-select --install
  ```

  - Você será solicitado a instalar as ferramentas de linha de comando, clicar no botão **instalar** :[![](walkthrough-images/xcode01.png "Instalando as ferramentas de linha de comando")](walkthrough-images/xcode01.png#lightbox)

  - As ferramentas serão baixadas e instaladas nos servidores da Apple:[![](walkthrough-images/xcode02.png "Baixando as ferramentas")](walkthrough-images/xcode02.png#lightbox)

- **Downloads para desenvolvedores da Apple** – o pacote de ferramentas de linha de comando está disponível a página [downloads para desenvolvedores da Apple](https://developer.apple.com/downloads/index.action) . Faça logon com sua ID da Apple e procure e baixe as ferramentas de linha de comando:[![](walkthrough-images/xcode03.png "Encontrando as ferramentas de linha de comando")](walkthrough-images/xcode03.png#lightbox)

Com as ferramentas de linha de comando instaladas, estamos prontos para continuar com o passo a passo.

## <a name="walkthrough"></a>Passo a passo

Neste tutorial, abordaremos as seguintes etapas:

- **[Criar uma biblioteca estática](#Creating_A_Static_Library)** – esta etapa envolve a criação de uma biblioteca estática do código **InfColorPicker** Objective-C. A biblioteca estática terá a `.a` extensão de arquivo e será inserida no assembly .net do projeto de biblioteca.
- **[Criar um projeto de associação do Xamarin. Ios](#Create_a_Xamarin.iOS_Binding_Project)** -uma vez que temos uma biblioteca estática, usaremos isso para criar um projeto de associação do Xamarin. Ios. O projeto de associação consiste na biblioteca estática que acabamos de criar e nos metadados na forma de código C# que explica como a API Objective-C pode ser usada. Esses metadados são comumente chamados de definições de API. Usaremos a **[nitidez objetiva](#Using_Objective_Sharpie)** para nos ajudar a criar as definições de API.
- **[Normalizar as definições de API – a](#Normalize_the_API_Definitions)** nitidez do objetivo faz um ótimo trabalho para nos ajudar, mas não pode fazer tudo. Discutiremos algumas alterações que precisamos fazer nas definições de API antes que possam ser usadas.
- **[Usar a biblioteca de associação](#Using_the_Binding)** – finalmente, criaremos um aplicativo Xamarin. Ios para mostrar como usar nosso projeto de associação recém-criado.

Agora que compreendemos quais etapas estão envolvidas, vamos passar para o restante do passo a passos.

<a name="Creating_A_Static_Library"></a>

## <a name="creating-a-static-library"></a>Criando uma biblioteca estática

Se inspecionarmos o código para InfColorPicker no github:

[![](walkthrough-images/image02.png "Inspect the code for InfColorPicker in Github")](walkthrough-images/image02.png#lightbox)

Podemos ver os três diretórios a seguir no projeto:

- **InfColorPicker** -esse diretório contém o código Objective-C para o projeto.
- **PickerSamplePad** -esse diretório contém um projeto de exemplo do iPad.
- **PickerSamplePhone** -esse diretório contém um projeto de exemplo do iPhone.

Vamos baixar o projeto InfColorPicker do [GitHub](https://github.com/InfinitApps/InfColorPicker/archive/master.zip) e descompactá-lo no diretório de nossa escolha. Abrindo o destino do Xcode para o `PickerSamplePhone` projeto, vemos a seguinte estrutura de projeto no navegador Xcode:

[![](walkthrough-images/image03.png "The project structure in the Xcode Navigator")](walkthrough-images/image03.png#lightbox)

Esse projeto realiza a reutilização de código adicionando diretamente o código-fonte InfColorPicker (na caixa vermelha) em cada projeto de exemplo. O código para o projeto de exemplo está dentro da caixa azul. Como esse projeto específico não nos fornece uma biblioteca estática, é necessário para nós criar um projeto do Xcode para compilar a biblioteca estática.

A primeira etapa é adicionar o código-fonte InfoColorPicker à biblioteca estática. Para obter isso, vamos fazer o seguinte:

1. Inicie o Xcode.
2. No menu **arquivo** , selecione **novo**  >  **projeto...**:

    [![](walkthrough-images/image04.png "Starting a new project")](walkthrough-images/image04.png#lightbox)
3. Selecione **estrutura & biblioteca**, o modelo de **biblioteca estática Cocoa Touch** e clique no botão **Avançar** :

    [![](walkthrough-images/image05.png "Select the Cocoa Touch Static Library template")](walkthrough-images/image05.png#lightbox)

4. Insira `InfColorPicker` para o **nome do projeto** e clique no botão **Avançar** :

    [![](walkthrough-images/image06.png "Enter InfColorPicker for the Project Name")](walkthrough-images/image06.png#lightbox)
5. Selecione um local para salvar o projeto e clique no botão **OK** .
6. Agora precisamos adicionar a origem do projeto InfColorPicker ao nosso projeto de biblioteca estática. Como o arquivo **InfColorPicker. h** já existe em nossa biblioteca estática (por padrão), o Xcode não nos permitirá substituí-lo. No **localizador**, navegue até o código-fonte InfColorPicker no projeto original que descompactamos do GitHub, copie todos os arquivos InfColorPicker e cole-os em nosso novo projeto de biblioteca estática:

    [![](walkthrough-images/image12.png "Copy all of the InfColorPicker files")](walkthrough-images/image12.png#lightbox)

7. Retorne ao Xcode, clique com o botão direito do mouse na pasta **InfColorPicker** e selecione **Adicionar arquivos a "InfColorPicker..."**:

    [![](walkthrough-images/image08.png "Adding files")](walkthrough-images/image08.png#lightbox)

8. Na caixa de diálogo Adicionar arquivos, navegue até os arquivos de código-fonte do InfColorPicker que acabamos de copiar, selecione todos eles e clique no botão **Adicionar** :

    [![](walkthrough-images/image09.png "Select all and click the Add button")](walkthrough-images/image09.png#lightbox)

9. O código-fonte será copiado em nosso projeto:

    [![](walkthrough-images/image10.png "The source code will be copied into the project")](walkthrough-images/image10.png#lightbox)

10. No navegador de projeto do Xcode, selecione o arquivo **InfColorPicker. m** e comente as duas últimas linhas (devido à maneira como essa biblioteca foi gravada, esse arquivo não é usado):

    [![](walkthrough-images/image14.png "Editing the InfColorPicker.m file")](walkthrough-images/image14.png#lightbox)

11. Agora, precisamos verificar se há alguma estrutura exigida pela biblioteca. Você pode encontrar essas informações no arquivo LEIAme ou abrindo um dos projetos de exemplo fornecidos. Este exemplo usa `Foundation.framework` , `UIKit.framework` e, `CoreGraphics.framework` portanto, vamos adicioná-los.

12. Selecione o **destino InfColorPicker > fases de Build** e expanda a seção **vincular binário com bibliotecas** :

    [![](walkthrough-images/image16b.png "Expand the Link Binary With Libraries section")](walkthrough-images/image16b.png#lightbox)

13. Use o **+** botão para abrir a caixa de diálogo, permitindo que você adicione as estruturas de quadros necessárias listadas acima:

    [![](walkthrough-images/image16c.png "Add the required frames frameworks listed above")](walkthrough-images/image16c.png#lightbox)

14. A seção **vincular binário com bibliotecas** agora deve ser parecida com a imagem abaixo:

    [![](walkthrough-images/image16d.png "The Link Binary With Libraries section")](walkthrough-images/image16d.png#lightbox)

Neste ponto, estamos próximos, mas não estamos prontos. A biblioteca estática foi criada, mas precisamos criá-la para criar um binário de Fat que inclui todas as arquiteturas necessárias para o dispositivo iOS e o simulador de iOS.

### <a name="creating-a-fat-binary"></a>Criando um binário do Fat

Todos os dispositivos iOS têm processadores da plataforma ARM desenvolvidos com o passar do tempo. Cada nova arquitetura adicionou novas instruções e outros aprimoramentos e, ao mesmo tempo, mantém a compatibilidade com versões anteriores. os dispositivos iOS têm conjuntos de instruções ARMv6, ARMv7, armv7s, arm64, embora [ARMv6 não sejam mais usados](~/ios/deploy-test/compiling-for-different-devices.md). O simulador do iOS não é alimentado pelo ARM e, em vez disso, é um simulador com x86 e x86_64. Isso significa que as bibliotecas devem ser fornecidas para cada conjunto de instruções.

Uma biblioteca Fat é um `.a` arquivo que contém todas as arquiteturas com suporte.

A criação de um binário de Fat é um processo de três etapas:

- Compile uma versão de & ARM64 do ARM 7 da biblioteca estática.
- Compile uma versão x86 e x84_64 da biblioteca estática.
- Use a `lipo` ferramenta de linha de comando para combinar as duas bibliotecas estáticas em uma.

Embora essas três etapas sejam bem simples, pode ser necessário repeti-las no futuro quando a biblioteca Objective-C receber atualizações ou se exigirem correções de bugs. Se você decidir automatizar essas etapas, ele simplificará a manutenção futura e o suporte do projeto de associação do iOS.

Há muitas ferramentas disponíveis para automatizar tarefas – um script de Shell, [Rake](https://rake.rubyforge.org/), [xbuild](https://www.mono-project.com/docs/tools+libraries/tools/xbuild/)e Make. Quando as ferramentas de linha de comando do Xcode são instaladas, o `make` também é instalado, portanto, é o sistema de compilação que será usado para este passo a passos. Aqui está um **makefile** que você pode usar para criar uma biblioteca compartilhada com várias arquiteturas que funcionará em um dispositivo IOS e o simulador para qualquer biblioteca:

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

Ao executar make, você verá muita rolagem de texto por. Se tudo funcionou corretamente, você verá que as palavras **foram criadas com êxito** , e os `libInfColorPicker-armv7.a` `libInfColorPicker-i386.a` `libInfColorPickerSDK.a` arquivos serão copiados para o mesmo local que o **makefile**:

[![](walkthrough-images/lib02.png "The libInfColorPicker-armv7.a, libInfColorPicker-i386.a and libInfColorPickerSDK.a files generated by the Makefile")](walkthrough-images/lib02.png#lightbox)

Você pode confirmar as arquiteturas em seu binário do Fat usando o seguinte comando:

```bash
xcrun -sdk iphoneos lipo -info libInfColorPicker.a
```

Isso deve exibir o seguinte:

```bash
Architectures in the fat file: libInfColorPicker.a are: i386 armv7 x86_64 arm64
```

Neste ponto, concluimos a primeira etapa de nossa associação do iOS criando uma biblioteca estática usando o Xcode e as ferramentas de linha de comando do Xcode `make` e o `lipo` . Vamos passar para a próxima etapa e usar a **nitidez do objetivo** para automatizar a criação das associações de API para nós.

<a name="Create_a_Xamarin.iOS_Binding_Project"></a>

## <a name="create-a-xamarinios-binding-project"></a>Criar um projeto de associação do Xamarin. iOS

Antes que possamos usar a **nitidez do objetivo** para automatizar o processo de ligação, precisamos criar um projeto de associação do Xamarin. Ios para alojar as definições de API (que usaremos a nitidez do **objetivo** para nos ajudar a criar) e criar a ligação em C# para nós.

Vamos fazer o seguinte:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Iniciar Visual Studio para Mac.
1. No menu **arquivo** , selecione **nova**  >  **solução...**:

    ![](walkthrough-images/bind01.png "Starting a new solution")

1. Na caixa de diálogo nova solução, selecione **biblioteca**  >  **projeto de associação Ios**:

    ![](walkthrough-images/bind02.png "Select iOS Binding Project")

1. Clique no botão **Avançar**.

1. Digite "InfColorPickerBinding" como o **nome do projeto** e clique no botão **criar** para criar a solução:

    ![](walkthrough-images/bind02a.png "Enter InfColorPickerBinding as the Project Name")

A solução será criada e dois arquivos padrão serão incluídos:

![](walkthrough-images/bind03.png "The solution structure in the Solution Explorer")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Inicie o Visual Studio.

1. No menu **arquivo** , selecione **novo**  >  **projeto...**:

    ![Iniciando um novo projeto](walkthrough-images/bind01vs.png "Iniciando um novo projeto")

1. Na caixa de diálogo novo projeto, selecione **Visual C# > iPhone & iPad > biblioteca de associações do IOS (Xamarin)**:

    [![Selecionar a biblioteca de associações do iOS](walkthrough-images/bind02.w157-sml.png)](walkthrough-images/bind02.w157.png#lightbox)

1. Digite "InfColorPickerBinding" como o **nome** e clique no botão **OK** para criar a solução.

A solução será criada e dois arquivos padrão serão incluídos:

![](walkthrough-images/bind03vs.png "The solution structure in the Solution Explorer")

-----

- **ApiDefinition.cs** -este arquivo conterá os contratos que definem como as APIs Objective-C serão encapsuladas em C#.
- **Structs.cs** -esse arquivo terá quaisquer estruturas ou valores de enumeração exigidos pelas interfaces e pelos delegados.

Vamos trabalhar com esses dois arquivos mais adiante neste guia. Primeiro, precisamos adicionar a biblioteca InfColorPicker ao projeto de associação.

### <a name="including-the-static-library-in-the-binding-project"></a>Incluindo a biblioteca estática no projeto de associação

Agora temos nosso projeto de associação base pronto, precisamos adicionar a biblioteca binária de Fat que criamos acima para a biblioteca **InfColorPicker** .

Siga estas etapas para adicionar a biblioteca:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Clique com o botão direito do mouse na pasta **referências nativas** no painel de soluções e selecione **Adicionar referências nativas**:

    ![](walkthrough-images/bind04a.png "Add Native References")

1. Navegue até o binário de Fat que fizemos anteriormente ( `libInfColorPickerSDK.a` ) e pressione o botão **abrir** :

    ![](walkthrough-images/bind05.png "Select the libInfColorPickerSDK.a file")
1. O arquivo será incluído no projeto:

    ![](walkthrough-images/bind04.png "Including a file")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Copie o `libInfColorPickerSDK.a` do seu **host de Build do Mac** e cole-o em seu projeto de associação.

1. Clique com o botão direito do mouse no projeto e escolha **adicionar > item existente...**:

    ![](walkthrough-images/bind04vs.png "Adding an existing file")

1. Navegue até o `libInfColorPickerSDK.a` e pressione o botão **Adicionar** :

    ![](walkthrough-images/bind05vs.png "Adding libInfColorPickerSDK.a")

1. O arquivo será incluído no projeto.

-----

Quando o arquivo **. a** é adicionado ao projeto, o Xamarin. Ios definirá automaticamente a **ação de compilação** do arquivo como **ObjcBindingNativeLibrary**e criará um arquivo especial chamado `libInfColorPickerSDK.linkwith.cs` .

Esse arquivo contém o `LinkWith` atributo que informa ao Xamarin. Ios como lidar com a biblioteca estática que acabamos de adicionar. O conteúdo desse arquivo é mostrado no seguinte trecho de código:

```csharp
using ObjCRuntime;

[assembly: LinkWith ("libInfColorPickerSDK.a", SmartLink = true, ForceLoad = true)]
```

O `LinkWith` atributo identifica a biblioteca estática para o projeto e alguns sinalizadores importantes do vinculador.

A próxima coisa que precisamos fazer é criar as definições de API para o projeto InfColorPicker. Para os fins deste passo a passos, usaremos a nitidez objetiva para gerar o arquivo **ApiDefinition.cs**.

<a name="Using_Objective_Sharpie"></a>

## <a name="using-objective-sharpie"></a>Usando a nitidez do objetivo

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

A Sharpde objetiva é uma ferramenta de linha de comando (fornecida pelo Xamarin) que pode ajudar a criar as definições necessárias para associar uma biblioteca de Objective-C de terceiros ao C#. Nesta seção, usaremos a nitidez objetiva para criar o **ApiDefinition.cs** inicial para o projeto InfColorPicker.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

A Sharpde objetiva é uma ferramenta de linha de comando (fornecida pelo Xamarin) que pode ajudar a criar as definições necessárias para associar uma biblioteca de Objective-C de terceiros ao C#. Nesta seção, usaremos a nitidez do objetivo em nosso host de **Build do Mac** para criar o **ApiDefinition.cs** inicial para o projeto InfColorPicker.

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
  xcode              Get information about Xcode installations and available SDKs.
  pod                Create a Xamarin C# binding to Objective-C CocoaPods
  bind               Create a Xamarin C# binding to Objective-C APIs
  update             Update to the latest release of Objective Sharpie
  verify-docs        Show cross reference documentation for [Verify] attributes
  docs               Open the Objective Sharpie online documentation
```

Para a finalidade deste guia, usaremos as seguintes ferramentas de nitidez objetivas:

- **Xcode** – essas ferramentas nos fornecem informações sobre a instalação atual do Xcode e as versões das APIs do IOS e Mac que instalamos. Usaremos essas informações posteriormente quando gerarmos nossas associações.
- **BIND** – usaremos essa ferramenta para analisar os arquivos **. h** no projeto InfColorPicker nos arquivos **ApiDefinition.cs** e **StructsAndEnums.cs** iniciais.

Para obter ajuda sobre uma ferramenta de nitidez de objetivo específica, insira o nome da ferramenta e a `-help` opção. Por exemplo, `sharpie xcode -help` retorna a seguinte saída:

```bash
Europa:Resources kmullins$ sharpie xcode -help
usage: sharpie xcode [OPTIONS]+

Options:
  -h, -help           Show detailed help
  -v, -verbose        Be verbose with output

Xcode Options:
  -sdks               List all available Xcode SDKs. Pass -verbose for more
                        details.
  -sdkpath SDK        Output the path of the SDK
  -frameworks SDK     List all available framework directories in a given SDK.
```

Antes que possamos iniciar o processo de associação, precisamos obter informações sobre nossos SDKs instalados atuais digitando o seguinte comando no terminal `sharpie xcode -sdks` :

```bash
amyb:Desktop amyb$ sharpie xcode -sdks
sdk: appletvos9.2    arch: arm64
sdk: iphoneos9.3     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: watchos2.2      arch: armv7
```

A partir do acima, podemos ver que o SDK está `iphoneos9.3` instalado em nosso computador. Com essas informações em vigor, estamos prontos para analisar os arquivos de projeto InfColorPicker para `.h` o **ApiDefinition.cs** inicial e `StructsAndEnums.cs` para o projeto InfColorPicker.

Insira o seguinte comando no aplicativo de terminal:

```bash
sharpie bind --output=InfColorPicker --namespace=InfColorPicker --sdk=[iphone-os] -scope [full-path-to-project]/InfColorPicker/InfColorPicker [full-path-to-project]/InfColorPicker/InfColorPicker/*.h
```

Em que `[full-path-to-project]` é o caminho completo para o diretório em que o arquivo de projeto **InfColorPicker** Xcode está localizado em nosso computador e [iPhone-os] é o SDK do IOS que instalamos, conforme observado pelo `sharpie xcode -sdks` comando. Observe que, neste exemplo, passamos ** \* . h** como um parâmetro, que inclui *todos* os arquivos de cabeçalho nesse diretório – normalmente, você não deve fazer isso, mas, em vez disso, ler atentamente os arquivos de cabeçalho para localizar o arquivo **. h** de nível superior que faz referência a todos os outros arquivos relevantes e apenas passá-lo para a nitidez objetiva. 

> [!TIP] 
> Para o `-scope` argumento, passe a pasta que tem os cabeçalhos que você deseja associar. Sem o `-scope` argumento, a nitidez do objetivo tentará gerar associações para todos os cabeçalhos do SDK do IOS que são importados, por exemplo `#import <UIKit.h>` , resultando em um arquivo de definições enorme que provavelmente gerará erros ao compilar o projeto de associação. Com o `-scope` conjunto de argumentos, a nitidez do objetivo não gerará associações para nenhum cabeçalho fora da pasta com escopo. 

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

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Abra esses dois arquivos no projeto de associação que criamos acima. Copie o conteúdo do arquivo **InfColorPicker.cs** e cole-o no arquivo **ApiDefinition.cs** , substituindo o `namespace ...` bloco de código existente pelo conteúdo do arquivo **InfColorPicker.cs** (deixando as `using` instruções intactas):

![](walkthrough-images/os07.png "The InfColorPickerControllerDelegate file")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Abra esses dois arquivos no projeto de associação que criamos acima. Copie o conteúdo do arquivo **InfColorPicker.cs** (do host de **Build do Mac**) e cole-o no arquivo **ApiDefinition.cs** , substituindo o `namespace ...` bloco de código existente pelo conteúdo do arquivo **InfColorPicker.cs** (deixando as `using` instruções intactas).

-----

<a name="Normalize_the_API_Definitions"></a>

## <a name="normalize-the-api-definitions"></a>Normalizar as definições de API

Às vezes, a nitidez do objetivo tem um problema de tradução `Delegates` . portanto, precisaremos modificar a definição da `InfColorPickerControllerDelegate` interface e substituir a `[Protocol, Model]` linha pelo seguinte:

```csharp
[BaseType(typeof(NSObject))]
[Model]
```

Para que a definição seja parecida com:

[![](walkthrough-images/os11.png "The definition")](walkthrough-images/os11.png#lightbox)

Em seguida, fazemos a mesma coisa com o conteúdo do `InfColorPicker.enums.cs` arquivo, copiando-os e colando-os no `StructsAndEnums.cs` arquivo deixando as `using` instruções intactas:

[![](walkthrough-images/os09.png "The contents the StructsAndEnums.cs file ")](walkthrough-images/os09.png#lightbox)

Você também pode achar que a nitidez do objetivo anotava a associação com `[Verify]` atributos. Esses atributos indicam que você deve verificar se a nitidez objetiva fez a coisa correta comparando a associação com a declaração C/Objective-C original (que será fornecida em um comentário acima da declaração associada). Depois de verificar as associações, você deve remover o atributo Verify. Para obter mais informações, consulte o guia de [verificação](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) .

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Neste ponto, nosso projeto de associação deve estar completo e pronto para ser criado. Vamos criar nosso projeto de associação e certificar-se de que terminamos sem erros:

[Compile o projeto de associação e verifique se não há erros](walkthrough-images/os12.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Neste ponto, nosso projeto de associação deve estar completo e pronto para ser criado. Vamos criar nosso projeto de associação e certificar-se de que terminamos sem erros.

-----

<a name="Using_the_Binding"></a>

## <a name="using-the-binding"></a>Usando a associação

Siga estas etapas para criar um aplicativo de exemplo do iPhone para usar a biblioteca de associação do iOS criada acima:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. **Criar projeto xamarin. Ios** -adicione um novo projeto Xamarin. Ios chamado **InfColorPickerSample** à solução, conforme mostrado nas seguintes capturas de tela:

    ![](walkthrough-images/use01.png "Adding a Single View App")

    ![](walkthrough-images/use01a.png "Setting the Identifier")

1. **Adicionar referência ao projeto de associação** -atualize o projeto **InfColorPickerSample** para que ele tenha uma referência ao projeto **InfColorPickerBinding** :

    ![](walkthrough-images/use02.png "Adding Reference to the Binding Project")

1. **Crie a interface do usuário do iPhone** -clique duas vezes no arquivo **MainStoryboard. Storyboard** no projeto **InfColorPickerSample** para editá-lo no designer do Ios. Adicione um **botão** à exibição e chame-o `ChangeColorButton` , conforme mostrado a seguir:

    ![](walkthrough-images/use03.png "Adding a Button to the view")

1. **Adicione o InfColorPickerView. xib** -a biblioteca InfColorPicker objec-C inclui um arquivo **. xib** . O Xamarin. iOS não incluirá this **. xib** no projeto de associação, o que causará erros em tempo de execução em nosso aplicativo de exemplo. A solução alternativa para isso é adicionar o arquivo **. xib** ao nosso projeto Xamarin. Ios. Selecione o projeto Xamarin. iOS, clique com o botão direito do mouse e selecione **adicionar > adicionar arquivos**e adicione o arquivo **. xib** , conforme mostrado na seguinte captura de tela:

    ![](walkthrough-images/use04.png "Add the InfColorPickerView.xib")

1. Quando solicitado, copie o arquivo **. xib** no projeto.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. **Criar projeto xamarin. Ios** -adicione um novo projeto Xamarin. Ios chamado **InfColorPickerSample** usando o modelo de **aplicativo de modo de exibição único** :

    [![projeto do aplicativo iOS (Xamarin)](walkthrough-images/use01.w157-sml.png)](walkthrough-images/use01.w157.png#lightbox)

    [![Selecionar modelo](walkthrough-images/use01-2.w157-sml.png)](walkthrough-images/use01-2.w157.png#lightbox)

1. **Adicionar referência ao projeto de associação** -atualize o projeto **InfColorPickerSample** para que ele tenha uma referência ao projeto **InfColorPickerBinding** :

    ![](walkthrough-images/use02vs.png "Add Reference to the Binding Project")

1. **Crie a interface do usuário do iPhone** -clique duas vezes no arquivo **MainStoryboard. Storyboard** no projeto **InfColorPickerSample** para editá-lo no designer do Ios. Adicione um **botão** à exibição e chame-o `ChangeColorButton` , conforme mostrado a seguir:

    ![](walkthrough-images/use03vs.png "Create the iPhone User Interface")

1. **Adicione o InfColorPickerView. xib** -a biblioteca InfColorPicker objec-C inclui um arquivo **. xib** . O Xamarin. iOS não incluirá this **. xib** no projeto de associação, o que causará erros em tempo de execução em nosso aplicativo de exemplo. A solução alternativa para isso é adicionar o arquivo **. xib** ao nosso projeto Xamarin. Ios de nosso **host de Build do Mac**. Selecione o projeto Xamarin. Ios, clique com o botão direito do mouse e selecione **Adicionar**  >  **Item existente...** e adicione o arquivo **. xib** .

-----

Em seguida, vamos dar uma olhada rápida nos protocolos em Objective-C e como os tratamos em Binding e em código C#.

### <a name="protocols-and-xamarinios"></a>Protocolos e Xamarin. iOS

Em Objective-C, um protocolo define os métodos (ou mensagens) que podem ser usados em determinadas circunstâncias. Conceitualmente, eles são muito semelhantes às interfaces em C#. Uma grande diferença entre um protocolo Objective-C e uma interface C# é que os protocolos podem ter métodos opcionais-métodos que uma classe não precisa implementar. O Objective-C usa a @optional palavra-chave é usado para indicar quais métodos são opcionais. Para obter mais informações sobre protocolos [, consulte eventos, protocolos e delegados](~/ios/app-fundamentals/delegates-protocols-and-events.md).

**InfColorPickerController** tem um desses protocolos, mostrado no trecho de código abaixo:

```csharp
@protocol InfColorPickerControllerDelegate

@optional

- (void) colorPickerControllerDidFinish: (InfColorPickerController*) controller;
// This is only called when the color picker is presented modally.

- (void) colorPickerControllerDidChangeColor: (InfColorPickerController*) controller;

@end
```

Esse protocolo é usado pelo **InfColorPickerController** para informar aos clientes que o usuário selecionou uma nova cor e que o **InfColorPickerController** foi concluído. A nitidez do objetivo mapeou esse protocolo, conforme mostrado no seguinte trecho de código:

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

Quando a biblioteca de associação é compilada, o Xamarin. iOS criará uma classe base abstrata chamada `InfColorPickerControllerDelegate` , que implementa essa interface com métodos virtuais.

Há duas maneiras de implementar essa interface em um aplicativo Xamarin. iOS:

- **Delegado forte** – usar um delegado forte envolve a criação de uma classe C# que cria subclasses `InfColorPickerControllerDelegate` e substitui os métodos apropriados. O **InfColorPickerController** usará uma instância dessa classe para se comunicar com seus clientes.
- **Delegado fraco** – um delegado fraco é uma técnica ligeiramente diferente que envolve a criação de um método público em alguma classe (como `InfColorPickerSampleViewController` ) e a exposição desse método ao `InfColorPickerDelegate` protocolo por meio de um `Export` atributo.

Delegados fortes fornecem IntelliSense, segurança de tipo e encapsulamento melhor. Por esses motivos, você deve usar delegados fortes onde você pode, em vez de um delegado fraco.

Neste tutorial, discutiremos as duas técnicas: primeiro implemente um forte delegado e, em seguida, explicando como implementar um delegado fraco.

### <a name="implementing-a-strong-delegate"></a>Implementando um forte delegado

Conclua o aplicativo Xamarin. iOS usando um delegado forte para responder à `colorPickerControllerDidFinish:` mensagem:

**InfColorPickerControllerDelegate de subclasse** – adicione uma nova classe ao projeto chamada `ColorSelectedDelegate` . Edite a classe para que ela tenha o seguinte código:

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

O Xamarin. iOS associará o delegado Objective-C criando uma classe base abstrata chamada `InfColorPickerControllerDelegate` . Subclasse este tipo e substitua o `ColorPickerControllerDidFinish` método para acessar o valor da `ResultColor` propriedade de `InfColorPickerController` .

**Criar uma instância de ColorSelectedDelegate** -nosso manipulador de eventos precisará de uma instância do `ColorSelectedDelegate` tipo que criamos na etapa anterior. Edite a classe `InfColorPickerSampleViewController` e adicione a seguinte variável de instância à classe:

```csharp
ColorSelectedDelegate selector;
```

**Inicialize a variável ColorSelectedDelegate** -para garantir que `selector` seja uma instância válida, atualize o método `ViewDidLoad` em `ViewController` para corresponder ao seguinte trecho:

```csharp
public override void ViewDidLoad ()
{
  base.ViewDidLoad ();
  ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithStrongDelegate;
  selector = new ColorSelectedDelegate (this);
}
```

**Implemente o método HandleTouchUpInsideWithStrongDelegate** -Next implemente o manipulador de eventos para quando o usuário tocar em **ColorChangeButton**. Edite `ViewController` e adicione o seguinte método:

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

Primeiro obtemos uma instância de `InfColorPickerController` por meio de um método estático e fazemos com que essa instância reconheça nosso forte delegado por meio da propriedade `InfColorPickerController.Delegate` . Essa propriedade foi gerada automaticamente para nós por nitidez objetiva. Por fim, chamamos `PresentModallyOverViewController` para mostrar a exibição `InfColorPickerSampleViewController.xib` para que o usuário possa selecionar uma cor.

**Execute o aplicativo** – neste ponto, fizemos todo o nosso código. Se você executar o aplicativo, deverá ser capaz de alterar a cor da tela de fundo do `InfColorColorPickerSampleView` conforme mostrado nas capturas de tela a seguir:

[![](walkthrough-images/run01.png "Running the Application")](walkthrough-images/run01.png#lightbox)

Parabéns! Neste ponto, você criou e com êxito uma biblioteca Objective-C para uso em um aplicativo Xamarin. Ios. Em seguida, vamos aprender sobre o uso de delegados fracos.

### <a name="implementing-a-weak-delegate"></a>Implementando um delegado fraco

Em vez de subclasser uma classe associada ao protocolo Objective-C para um determinado delegado, o Xamarin. iOS também permite que você implemente os métodos de protocolo em qualquer classe derivada de `NSObject` , decorar seus métodos com o `ExportAttribute` e, em seguida, fornecendo os seletores apropriados. Ao usar essa abordagem, você atribui uma instância de sua classe à `WeakDelegate` propriedade em vez de à `Delegate` propriedade. Um delegado fraco oferece a você a flexibilidade de levar sua classe de representante para uma hierarquia de herança diferente. Vejamos como implementar e usar um delegado fraco em nosso aplicativo Xamarin. iOS.

**Criar manipulador de eventos para TouchUpInside** – vamos criar um novo manipulador de eventos para o `TouchUpInside` evento do botão Alterar cor do plano de fundo. Esse manipulador preencherá a mesma função que o `HandleTouchUpInsideWithStrongDelegate` manipulador que criamos na seção anterior, mas usará um delegado fraco em vez de um delegado forte. Edite a classe `ViewController` e adicione o seguinte método:

```csharp
private void HandleTouchUpInsideWithWeakDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.WeakDelegate = this;
    picker.SourceColor = this.View.BackgroundColor;
    picker.PresentModallyOverViewController (this);
}
```

**Atualizar ViewDidLoad** -devemos alterar `ViewDidLoad` para que ele use o manipulador de eventos que acabamos de criar. Edite `ViewController` e altere `ViewDidLoad` para se parecer com o seguinte trecho de código:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithWeakDelegate;
}

```

**Manipular o colorPickerControllerDidFinish: Message** -quando o `ViewController` for concluído, o Ios enviará a mensagem `colorPickerControllerDidFinish:` para o `WeakDelegate` . Precisamos criar um método C# que possa manipular essa mensagem. Para fazer isso, criamos um método C# e, em seguida, adornamos com o `ExportAttribute` . Edite `ViewController` e adicione o seguinte método à classe:

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

Este artigo descreveu o processo de criação e uso de um projeto de associação do Xamarin. iOS. Primeiro, discutimos como compilar uma biblioteca Objective-C existente em uma biblioteca estática. Em seguida, abordamos como criar um projeto de associação Xamarin. iOS e como usar a nitidez objetiva para gerar as definições de API para a biblioteca Objective-C. Discutimos como atualizar e ajustar as definições de API geradas para torná-las adequadas para o consumo público. Após a conclusão do projeto de associação do Xamarin. iOS, passamos para o consumo de associação em um aplicativo Xamarin. iOS, com foco no uso de delegados fortes e de delegados fracos.

## <a name="related-links"></a>Links relacionados

- [Associação de bibliotecas Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Detalhes da Associação](~/cross-platform/macios/binding/overview.md)
- [Guia de referência de tipos de associação](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin para desenvolvedores de Objective-C](~/ios/get-started/objective-c-developers/index.md)
- [Diretrizes de design de estrutura](https://msdn.microsoft.com/library/ms229042.aspx)
