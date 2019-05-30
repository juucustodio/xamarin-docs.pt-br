---
title: 'Passo a passo: Como associar uma biblioteca Objective-C do iOS'
description: Este artigo fornece uma passo a passo prático de criação de uma associação xamarin. IOS para uma biblioteca Objective-C existente, InfColorPicker. Ele aborda tópicos como compilar uma biblioteca estática do Objective-C, associá-la e usando a associação em um aplicativo xamarin. IOS.
ms.prod: xamarin
ms.assetid: D3F6FFA0-3C4B-4969-9B83-B6020B522F57
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: 2897129779b698eae60338d44f9af19b6a2761bc
ms.sourcegitcommit: 10b4ccbfcf182be940899c00fc0fecae1e199c5b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252351"
---
# <a name="walkthrough-binding-an-ios-objective-c-library"></a>Passo a passo: Como associar uma biblioteca Objective-C do iOS

_Este artigo fornece uma passo a passo prático de criação de uma associação xamarin. IOS para uma biblioteca Objective-C existente, InfColorPicker. Ele aborda tópicos como compilar uma biblioteca estática do Objective-C, associá-la e usando a associação em um aplicativo xamarin. IOS._

Ao trabalhar no iOS, você poderá encontrar casos em que você deseja consumir uma biblioteca do Objective-C de terceiros. Nessas situações, você pode usar um xamarin. IOS _projeto de vinculação_ para criar um [ C# associação](~/cross-platform/macios/binding/overview.md) que permitirá que você consuma a biblioteca em seus aplicativos xamarin. IOS.

Em geral no ecossistema do iOS, você pode encontrar bibliotecas 3 tipos:

* Como um arquivo de biblioteca estática pré-compilado com `.a` extensão juntamente com seu (s) (arquivos. h). Por exemplo, [biblioteca de análise do Google](https://developers.google.com/analytics/devguides/collection/ios/v3/sdk-download?hl=es#download_sdk)
* Como uma estrutura pré-compilado. Esta é apenas uma pasta que contém a biblioteca estática, os cabeçalhos e os recursos adicionais, às vezes, com `.framework` extensão. Por exemplo, [AdMob biblioteca do Google](https://developers.google.com/admob/ios/download).
* Como apenas os arquivos de código-fonte. Por exemplo, uma biblioteca que contém apenas `.m` e `.h` arquivos Objective C.

O primeiro e o segundo cenário já haverá uma biblioteca estática de CocoaTouch pré-compilado portanto, neste artigo nos concentraremos no terceiro cenário. Lembre-se de que, antes de começar a criar uma associação, sempre verifique a licença fornecida com a biblioteca para garantir que você está livre para associá-lo.

Este artigo fornece uma explicação passo a passo de criação de um projeto de associação usando o código-fonte aberto [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) Objective-C do projeto como um exemplo, no entanto, todas as informações neste guia podem ser adaptadas para uso com qualquer biblioteca do Objective-C de terceiros. A biblioteca de InfColorPicker fornece um controlador de exibição reutilizáveis que permite ao usuário selecionar uma cor com base em sua representação HSB, tornando mais fácil de usar a seleção de cor.

[![](walkthrough-images/run01.png "Exemplo da biblioteca InfColorPicker que executam o iOS")](walkthrough-images/run01.png#lightbox)

Abordaremos todas as etapas necessárias para consumir essa determinada API Objective-C no xamarin. IOS:

- Primeiro, vamos criar uma biblioteca estática do Objective-C usando o Xcode.
- Em seguida, faremos o vínculo essa biblioteca estática com xamarin. IOS.
- Em seguida, mostrar como objetivo Sharpie pode reduzir a carga de trabalho gerando automaticamente algumas (mas não todas) das definições de API necessárias necessárias para a associação do xamarin. IOS.
- Por fim, vamos criar um aplicativo xamarin. IOS que usa a associação.

O aplicativo de exemplo demonstram como usar um delegado de alta segurança para a comunicação entre a API InfColorPicker e em nosso C# código. Depois que já vimos como usar um delegado de alta segurança, abordaremos como usar delegados fracos para executar as mesmas tarefas.

## <a name="requirements"></a>Requisitos

Este artigo pressupõe que você tem alguma familiaridade com a linguagem do Objective-C e Xcode e ter lido nossos [de associação Objective-C](~/cross-platform/macios/binding/index.md) documentação. Além disso, é necessário o seguinte para concluir as etapas apresentadas:

-  **Xcode e o SDK do iOS** -Xcode da Apple e a API do iOS mais recente precisa ser instalado e configurado no computador do desenvolvedor.
-  **[Ferramentas de linha de comando do Xcode](#Installing_the_Xcode_Command_Line_Tools)**  -as ferramentas de linha de comando do Xcode deve estar instaladas para a versão atualmente instalada do Xcode (veja abaixo detalhes da instalação).
-  **O Visual Studio para Mac ou Visual Studio** -a versão mais recente do Visual Studio para Mac ou Visual Studio deve ser instalada e configurada no computador de desenvolvimento. Um Apple Mac é necessário para desenvolver um aplicativo xamarin. IOS e, ao usar o Visual Studio deve estar conectado para [um xamarin. IOS build host](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
-  **A versão mais recente do objetivo Sharpie** -obtido por uma cópia atual da ferramenta objetivo Sharpie [aqui](~/cross-platform/macios/binding/objective-sharpie/get-started.md). Se você já tiver Sharpie objetivo instalado, você pode atualizá-lo para a versão mais recente usando o `sharpie update`

<a name="Installing_the_Xcode_Command_Line_Tools"/>

## <a name="installing-the-xcode-command-line-tools"></a>Instalando as ferramentas de linha de comando do Xcode

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)


Como mencionado acima, estaremos usando ferramentas de linha de comando do Xcode (especificamente `make` e `lipo`) neste passo a passo. O `make` comando é um utilitário de Unix muito comum que irá automatizar a compilação de programas executáveis e bibliotecas usando um _makefile_ que especifica como o programa deve ser criado. O `lipo` comando é um utilitário de linha de comando dos X para a criação de arquitetura de vários arquivos; ele combina vários `.a` arquivos em um único arquivo que pode ser usado por todas as arquiteturas de hardware.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


Como mencionado acima, estaremos usando ferramentas de linha de comando do Xcode sobre o **Host de Build do Mac** (especificamente `make` e `lipo`) neste passo a passo. O `make` comando é um utilitário de Unix muito comum que irá automatizar a compilação de programas executáveis e bibliotecas usando um _makefile_ Especifica como compilar o programa. O `lipo` comando é um utilitário de linha de comando dos X para a criação de arquitetura de vários arquivos; ele combina vários `.a` arquivos em um único arquivo que pode ser usado por todas as arquiteturas de hardware.


-----

De acordo com a da Apple [compilando da linha de comando com perguntas Frequentes do Xcode](https://developer.apple.com/library/ios/technotes/tn2339/_index.html) documentação, nos X 10.9 e superior, o **Downloads** painel do Xcode **preferências** caixa de diálogo não oferece mais suporte as ferramentas de linha de comando de download.

Você precisará usar um dos métodos a seguir para instalar as ferramentas:

- **Instale o Xcode** - quando você instala o Xcode, ele é fornecido com todas as suas ferramentas de linha de comando. Nos X 10.9 shims (instalado na `/usr/bin`), poderá mapear qualquer ferramenta incluída no `/usr/bin` para a ferramenta correspondente dentro do Xcode. Por exemplo, o `xcrun` comando, que permite que você localize ou executar qualquer ferramenta dentro do Xcode na linha de comando.
- **O aplicativo de Terminal** -no aplicativo Terminal, você pode instalar as ferramentas de linha de comando, executando o `xcode-select --install` comando:
    - Inicie o aplicativo de Terminal.
    - Tipo de `xcode-select --install` e pressione **Enter**, por exemplo:

    ```bash
    Europa:~ kmullins$ xcode-select --install
    ```

    - Você será solicitado a instalar as ferramentas de linha de comando, clique no **instalar** botão:   [![](walkthrough-images/xcode01.png "Instalando as ferramentas de linha de comando")](walkthrough-images/xcode01.png#lightbox)

    - As ferramentas serão baixadas e instaladas a partir de servidores da Apple:   [![](walkthrough-images/xcode02.png "Baixar as ferramentas")](walkthrough-images/xcode02.png#lightbox)

- **Downloads para desenvolvedores da Apple** -as ferramentas de linha de comando do pacote está disponível a [Downloads para desenvolvedores da Apple](https://developer.apple.com/downloads/index.action) página da web. Faça logon com sua ID da Apple, em seguida, pesquisar e baixar as ferramentas de linha de comando: [![](walkthrough-images/xcode03.png "Localizando as ferramentas de linha de comando")](walkthrough-images/xcode03.png#lightbox)

Com as ferramentas de linha de comando instalado, estamos prontos para continuar com o passo a passo.

## <a name="walkthrough"></a>Passo a passo

Neste passo a passo, abordaremos as etapas a seguir:

- **[Criar uma biblioteca estática](#Creating_A_Static_Library)**  -esta etapa envolve a criação de uma biblioteca estática da **InfColorPicker** código Objective-C. A biblioteca estática terá o `.a` extensão de arquivo e será inserido no assembly do .NET do projeto de biblioteca.
- **[Criar um projeto de associação do xamarin. IOS](#Create_a_Xamarin.iOS_Binding_Project)**  -após temos uma biblioteca estática, iremos usá-lo para criar um projeto de associação do xamarin. IOS. O projeto de associação consiste de biblioteca estática que acabamos de criar e metadados na forma de C# código que explica como a API de Objective-C pode ser usada. Esses metadados é conhecido como as definições de API. Usaremos **[objetivo Sharpie](#Using_Objective_Sharpie)** para ajudar com criar as definições de API.
- **[Normalizar as definições de API](#Normalize_the_API_Definitions)**  - objetivo Sharpie faz um excelente trabalho para nos ajudar, mas ele não pode fazer tudo. Vamos discutir algumas alterações que precisamos fazer para as definições de API antes que possam ser usados.
- **[Use a biblioteca de ligação](#Using_the_Binding)**  – por fim, vamos criar um aplicativo xamarin. IOS para mostrar como usar nosso projeto de associação recém-criada.

Agora que compreendemos as etapas que estão envolvidas, vamos passar para o restante do passo a passo.

<a name="Creating_A_Static_Library"/>

## <a name="creating-a-static-library"></a>Criando uma biblioteca estática

Se podemos inspecionar o código para InfColorPicker no Github:

[![](walkthrough-images/image02.png "Inspecione o código para InfColorPicker no Github")](walkthrough-images/image02.png#lightbox)

Podemos ver os três diretórios a seguir no projeto:

- **InfColorPicker** -esse diretório contém o código Objective-C para o projeto.
- **PickerSamplePad** -esse diretório contém um projeto de exemplo iPad.
- **PickerSamplePhone** -esse diretório contém um projeto de exemplo iPhone.

Vamos baixar InfColorPicker Proje [GitHub](https://github.com/InfinitApps/InfColorPicker/archive/master.zip) e descompacte-o no diretório de sua escolha. Abrindo o destino do Xcode para `PickerSamplePhone` projeto, podemos ver a seguinte estrutura de projeto no navegador de Xcode:

[![](walkthrough-images/image03.png "A estrutura do projeto no navegador de Xcode")](walkthrough-images/image03.png#lightbox)

Este projeto atinge a reutilização de código adicionando o código-fonte InfColorPicker (na caixa vermelha) diretamente em cada projeto de exemplo. O código para o projeto de exemplo está dentro da caixa azul. Como este projeto não nos fornecer uma biblioteca estática, é necessário para criar um projeto do Xcode para compilar a biblioteca estática.

A primeira etapa é para que possamos adicionar o código-fonte InfoColorPicker para a biblioteca estática. Para fazer isso vamos fazer o seguinte:

1. Inicie o Xcode.
2. Dos **arquivo** menu, selecione **New** > **projeto...** :

    [![](walkthrough-images/image04.png "Iniciar um novo projeto")](walkthrough-images/image04.png#lightbox)
3. Selecione **Framework & biblioteca**, o **Touch biblioteca estática do Cocoa** modelo e clique no **próxima** botão:

    [![](walkthrough-images/image05.png "Selecione o modelo de biblioteca estática da Cocoa Touch")](walkthrough-images/image05.png#lightbox)

4. Insira `InfColorPicker` para o **nome do projeto** e clique no **próximo** botão:

    [![](walkthrough-images/image06.png "Insira InfColorPicker para o nome do projeto")](walkthrough-images/image06.png#lightbox)
5. Selecione um local para salvar o projeto e clique no **Okey** botão.
6. Agora, precisamos adicionar o código-fonte do projeto InfColorPicker ao nosso projeto de biblioteca estática. Porque o **InfColorPicker.h** arquivo já existe em nossa biblioteca estática (por padrão), o Xcode não permitirá a substituí-la. Dos **Finder**, navegue até o código-fonte InfColorPicker no projeto original que estamos descompactados do GitHub, copie todos os arquivos InfColorPicker e colá-los em nosso novo projeto de biblioteca estática:

    [![](walkthrough-images/image12.png "Copie todos os arquivos de InfColorPicker")](walkthrough-images/image12.png#lightbox)

7. Retornar para o Xcode, clique com botão direito do **InfColorPicker** pasta e selecione **adicionar arquivos ao "InfColorPicker..."** :

    [![](walkthrough-images/image08.png "Adicionando arquivos")](walkthrough-images/image08.png#lightbox)

8. Na caixa de diálogo Adicionar arquivos, navegue até os arquivos de código de origem InfColorPicker que acabou de copiar, selecionar tudo e clique no **adicionar** botão:

    [![](walkthrough-images/image09.png "Selecionar tudo e clique no botão Adicionar")](walkthrough-images/image09.png#lightbox)

9. O código-fonte será copiado para nosso projeto:

    [![](walkthrough-images/image10.png "O código-fonte será copiado para o projeto")](walkthrough-images/image10.png#lightbox)

10. No navegador de projeto Xcode, selecione a **InfColorPicker.m** de arquivo e comente as duas últimas linhas (por causa da forma que essa biblioteca tenha sido escrito, esse arquivo não é usado):

    [![](walkthrough-images/image14.png "Editando o arquivo InfColorPicker.m")](walkthrough-images/image14.png#lightbox)

11. Agora, precisamos verificar se há quaisquer estruturas necessárias pela biblioteca. Você pode encontrar essas informações no arquivo Leiame ou abrindo um dos projetos de exemplo fornecidos. Este exemplo usa `Foundation.framework`, `UIKit.framework`, e `CoreGraphics.framework` vamos adicioná-los.

12. Selecione o **InfColorPicker destino > fases de compilação** e expanda o **vincular binário com bibliotecas** seção:

    [![](walkthrough-images/image16b.png "Expanda a seção de vincular binário com bibliotecas")](walkthrough-images/image16b.png#lightbox)

13. Use o **+** botão para abrir a caixa de diálogo permitindo que você adicione as estruturas de quadros necessários listados acima:

    [![](walkthrough-images/image16c.png "Adicione que as estruturas de quadros necessários listados acima")](walkthrough-images/image16c.png#lightbox)

14. O **vincular binário com bibliotecas** seção agora deve ser semelhante a imagem a seguir:

    [![](walkthrough-images/image16d.png "A seção de vincular binário com bibliotecas")](walkthrough-images/image16d.png#lightbox)

Neste ponto, estamos fechar, mas não exatamente terminamos. A biblioteca estática foi criada, mas precisamos compilá-lo para criar uma Fat binário que inclui todas as arquiteturas necessárias para o dispositivo iOS e o simulador de iOS.

### <a name="creating-a-fat-binary"></a>Criação de um binário Fat

Todos os dispositivos iOS têm processadores multifuncionais na arquitetura ARM que desenvolveram ao longo do tempo. Cada nova arquitetura adicionadas novas instruções e outras melhorias e ainda manter compatibilidade com versões anteriores. os dispositivos iOS têm armv6, armv7, armv7s, conjuntos de instruções arm64 – Embora [armv6 não mais usada](~/ios/deploy-test/compiling-for-different-devices.md). O simulador de iOS não é alimentado por ARM e em vez disso, é um x86 e x86_64 alimentado simulator. Isso significa que bibliotecas devem ser fornecidas para cada conjunto de instruções.

É uma biblioteca Fat `.a` arquivo que contém todas as arquiteturas com suporte.

Criar uma fat binário é um processo de três etapas:

- Compile uma versão de 7 de ARM e ARM64 da biblioteca estática.
- Compile uma versão x86 e x84_64 da biblioteca estática.
- Use o `lipo` ferramenta de linha de comando para combinar as duas bibliotecas estáticas em uma.

Embora essas três etapas são simples em vez disso, ele pode ser necessário repeti-los no futuro, quando a biblioteca do Objective-C recebe atualizações ou se é necessário correções de bugs. Se você decidir automatizar estas etapas, simplificará a manutenção futura e o suporte de projeto de associação do iOS.

Há muitas ferramentas disponíveis para automatizar tarefas - um script de shell [rake](http://rake.rubyforge.org/), [xbuild](https://www.mono-project.com/docs/tools+libraries/tools/xbuild/), e [fazer](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/make.1.html). Quando as ferramentas de linha de comando do Xcode estão instaladas, `make` também estiver instalado, portanto, que é o sistema de compilação que será usado para este passo a passo. Aqui está uma **Makefile** que você pode usar para criar uma biblioteca compartilhada de várias arquitetura que funcionará em um dispositivo iOS e o simulador para qualquer biblioteca:

```bash
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

Insira o **Makefile** comandos no editor de texto sem formatação de sua escolha e atualize as seções com **nome do seu projeto** com o nome do seu projeto. Também é importante garantir que você cola as instruções acima exatamente, com as guias dentro das instruções preservadas.

Salve o arquivo com o nome **Makefile** no mesmo local como a biblioteca estática do Xcode InfColorPicker criamos acima:

[![](walkthrough-images/lib00.png "Salve o arquivo com o nome do Makefile")](walkthrough-images/lib00.png#lightbox)

Abra o aplicativo Terminal no Mac e navegue até o local do seu Makefile. Tipo de `make` no Terminal, pressione **Enter** e o **Makefile** serão executadas:

[![](walkthrough-images/lib01.png "Exemplo de saída de makefile")](walkthrough-images/lib01.png#lightbox)

Quando você executa o faça, você verá muita rolagem por de texto. Se tudo funcionou corretamente, você verá as palavras **BUILD bem-sucedido** e o `libInfColorPicker-armv7.a`, `libInfColorPicker-i386.a` e `libInfColorPickerSDK.a` arquivos serão copiados para o mesmo local que o **Makefile**:

[![](walkthrough-images/lib02.png "Os arquivos libInfColorPicker armv7.a, libInfColorPicker i386.a e libInfColorPickerSDK.a gerados pelo Makefile")](walkthrough-images/lib02.png#lightbox)

Você pode confirmar as arquiteturas de dentro do seu binário Fat, usando o seguinte comando:

```bash
xcrun -sdk iphoneos lipo -info libInfColorPicker.a
```

Isso deve exibir o seguinte:

```bash
Architectures in the fat file: libInfColorPicker.a are: i386 armv7 x86_64 arm64
```

Neste ponto, concluímos a primeira etapa da nossa ligação de iOS com a criação de uma biblioteca estática usando o Xcode e as ferramentas de linha de comando do Xcode `make` e `lipo`. Vamos passar para a próxima etapa e usar **Sharpie objetivo** para automatizar a criação das associações de API para nós.

<a name="Create_a_Xamarin.iOS_Binding_Project"/>

## <a name="create-a-xamarinios-binding-project"></a>Criar um xamarin. IOS associando projeto

Antes que podemos usar **objetivo Sharpie** para automatizar o processo de associação, precisamos criar um projeto de associação do xamarin. IOS para hospedar as definições de API (que usaremos **Sharpie objetivo** para nos ajudar a Build) e crie o C# de associação para nós.

Vamos fazer o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Inicie o Visual Studio para Mac.
1. Dos **arquivo** menu, selecione **New** > **solução...** :

    ![](walkthrough-images/bind01.png "Iniciando uma nova solução")

1. Na caixa de diálogo nova solução, selecione **biblioteca** > **associando projeto iOS**:

    ![](walkthrough-images/bind02.png "Selecione a associação de projeto do iOS")

1. Clique o **próxima** botão.

1. Insira "InfColorPickerBinding" como o **nome do projeto** e clique no **criar** botão para criar a solução:

    ![](walkthrough-images/bind02a.png "Digite InfColorPickerBinding como o nome do projeto")

A solução será criada e dois arquivos padrão será incluídos:

![](walkthrough-images/bind03.png "A estrutura da solução no Gerenciador de soluções")


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


1. Inicie o Visual Studio.

1. Dos **arquivo** menu, selecione **New** > **projeto...** :

    ![Iniciar um novo projeto](walkthrough-images/bind01vs.png "iniciar um novo projeto")

1. Na caixa de diálogo Novo projeto, selecione **Visual C# > iPhone & iPad > iOS (Xamarin) biblioteca de associações**:

    [![Selecione a biblioteca de associações do iOS](walkthrough-images/bind02.w157-sml.png)](walkthrough-images/bind02.w157.png#lightbox)

1. Insira "InfColorPickerBinding" como o **nome** e clique no **Okey** botão para criar a solução.

A solução será criada e dois arquivos padrão será incluídos:

![](walkthrough-images/bind03vs.png "A estrutura da solução no Gerenciador de soluções")

-----

- **ApiDefinition.cs** -esse arquivo conterá os contratos que definem como Objective-C API será encapsulada em C#.
- **Structs.CS** – esse arquivo conterá as estruturas ou os valores de enumeração que são necessários para as interfaces e delegados.

Trabalharemos com esses dois arquivos mais tarde no passo a passo. Primeiro, precisamos adicionar a biblioteca InfColorPicker ao projeto de associação.

### <a name="including-the-static-library-in-the-binding-project"></a>Incluindo a biblioteca estática do projeto de associação

Agora temos nosso projeto base de associação pronto, precisamos adicionar a biblioteca de binários Fat que criamos acima para o **InfColorPicker** biblioteca.

Siga estas etapas para adicionar a biblioteca:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Clique com botão direito no **referências nativas** pasta no painel de soluções e selecione **adicionar referências nativas**:

    ![](walkthrough-images/bind04a.png "Adicionar referências nativas")

1. Navegue até o Fat binário que fizemos anteriormente (`libInfColorPickerSDK.a`) e pressione a **aberto** botão:

    ![](walkthrough-images/bind05.png "Selecione o arquivo libInfColorPickerSDK.a")
1. O arquivo será incluído no projeto:

    ![](walkthrough-images/bind04.png "Incluindo um arquivo")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Cópia de `libInfColorPickerSDK.a` de seu **Host de Build do Mac** e cole-o em seu projeto de associação.

1. Clique com botão direito no projeto e escolha **Adicionar > Item existente...** :

    ![](walkthrough-images/bind04vs.png "Adicionando um arquivo existente")

1. Navegue até a `libInfColorPickerSDK.a` e pressione a **Add** botão:

    ![](walkthrough-images/bind05vs.png "Adding libInfColorPickerSDK.a")

1. O arquivo será incluído no projeto.

-----

Quando o **1).a** arquivo é adicionado ao projeto, xamarin. IOS definirá automaticamente a **Build Action** do arquivo a ser **ObjcBindingNativeLibrary**e crie um arquivo especial chamado `libInfColorPickerSDK.linkwith.cs`.


Esse arquivo contém o `LinkWith` atributo que diz ao xamarin. IOS como a biblioteca estática que acabamos de identificador adicionado. O conteúdo desse arquivo é mostrado no trecho de código a seguir:

```csharp
using ObjCRuntime;

[assembly: LinkWith ("libInfColorPickerSDK.a", SmartLink = true, ForceLoad = true)]
```

O `LinkWith` atributo identifica a biblioteca estática para o projeto e alguns sinalizadores do vinculador importantes.


A próxima coisa que precisamos fazer é criar as definições de API para o projeto InfColorPicker. Para os fins deste passo a passo, usaremos objetivo Sharpie para gerar o arquivo **ApiDefinition.cs**.

<a name="Using_Objective_Sharpie"/>

## <a name="using-objective-sharpie"></a>Usando Sharpie objetivo

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)


Objetivo Sharpie é uma linha de comando ferramenta (fornecida pelo Xamarin) que pode ajudar a criar as definições necessárias para associar uma biblioteca de Objective-C de terceiros 3ª para C#. Nesta seção, vamos usar Sharpie objetivo criar inicial **ApiDefinition.cs** para o projeto InfColorPicker.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


Objetivo Sharpie é uma linha de comando ferramenta (fornecida pelo Xamarin) que pode ajudar a criar as definições necessárias para associar uma biblioteca de Objective-C de terceiros 3ª para C#. Nesta seção, vamos usar objetivo Sharpie em nossa **Host de Build do Mac** para criar inicial **ApiDefinition.cs** para o projeto InfColorPicker.


-----

Para começar, vamos baixar o arquivo do instalador Sharpie objetivo como descrito neste [guia](~/cross-platform/macios/binding/objective-sharpie/get-started.md#installing). Execute o instalador e siga todos os prompts na tela do Assistente de instalação para instalar o objetivo Sharpie em nosso computador de desenvolvimento.

Após obtermos Sharpie objetivo com êxito instalado, vamos iniciar o aplicativo Terminal e digite o seguinte comando para obter ajuda sobre todas as ferramentas fornecidas por ele para ajudar a associação:

```bash
sharpie -help
```

Se executamos o comando acima, a seguinte saída será gerada:

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

Para fins deste passo a passo, vamos usar as seguintes ferramentas Sharpie objetivo:

- **Xcode** - este ferramentas nos dá informações sobre nossa instalação atual do Xcode e as versões do iOS e as APIs do Mac que podemos ter instalado. Vamos usar essas informações posteriormente ao gerarmos nosso associações.
- **associar** -vamos usar essa ferramenta para analisar os **. h** arquivos no projeto em inicial InfColorPicker **ApiDefinition.cs** e **StructsAndEnums.cs** arquivos.

Para obter ajuda sobre uma ferramenta Sharpie objetivo específica, insira o nome da ferramenta e o `-help` opção. Por exemplo, `sharpie xcode -help` retorna a seguinte saída:

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

Para que possamos começar o processo de associação, precisamos obter informações sobre nossos SDKs instalados atuais digitando o seguinte comando no Terminal `sharpie xcode -sdks`:

```bash
amyb:Desktop amyb$ sharpie xcode -sdks
sdk: appletvos9.2    arch: arm64
sdk: iphoneos9.3     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: watchos2.2      arch: armv7
```

Acima, podemos ver que temos o `iphoneos9.3` SDK instalado em nosso computador. Com essas informações em vigor, estamos prontos para analisar o projeto InfColorPicker `.h` arquivos no inicial **ApiDefinition.cs** e `StructsAndEnums.cs` para o projeto InfColorPicker.

Digite o seguinte comando no aplicativo Terminal:

```bash
sharpie bind --output=InfColorPicker --namespace=InfColorPicker --sdk=[iphone-os] [full-path-to-project]/InfColorPicker/InfColorPicker/*.h
```

Em que `[full-path-to-project]` é o caminho completo para o diretório onde o **InfColorPicker** arquivo de projeto do Xcode está localizado em nosso computador, e [SO iphone] é o iOS SDK instalamos, conforme indicado pela `sharpie xcode -sdks` comando. Observe que neste exemplo passamos  **\*. h** como um parâmetro, que inclui *todos os* os arquivos de cabeçalho nesse diretório - normalmente, você deve não fazer isso, mas em vez disso, leia atentamente por meio de arquivos de cabeçalho para localizar o nível superior **. h** arquivo que faz referência a todos os outros arquivos relevantes e apenas passá-lo para Sharpie objetivo.

O seguinte [saída](walkthrough-images/os05.png) será gerada no terminal:

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

E o **InfColorPicker.enums.cs** e **InfColorPicker.cs** arquivos serão criados em nosso diretório:

[![](walkthrough-images/os06.png "Os arquivos InfColorPicker.enums.cs e InfColorPicker.cs")](walkthrough-images/os06.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)


Abra ambos os arquivos no projeto de associação que criamos acima. Copie o conteúdo do **InfColorPicker.cs** do arquivo e cole-o na **ApiDefinition.cs** arquivo, substituindo os existentes `namespace ...` bloco de código com o conteúdo do  **InfColorPicker.cs** arquivo (deixando o `using` instruções intactas):

![](walkthrough-images/os07.png "O arquivo InfColorPickerControllerDelegate")


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


Abra ambos os arquivos no projeto de associação que criamos acima. Copie o conteúdo do **InfColorPicker.cs** arquivo (da **Host de Build do Mac**) e cole-o na **ApiDefinition.cs** arquivo, substituindo os existentes `namespace ...` bloco de código com o conteúdo do **InfColorPicker.cs** arquivo (deixando o `using` instruções intactas).


-----

<a name="Normalize_the_API_Definitions"/>

## <a name="normalize-the-api-definitions"></a>Normalizar as definições de API

Objetivo Sharpie às vezes, tem um problema convertendo `Delegates`, portanto, precisamos modificar a definição do `InfColorPickerControllerDelegate` interface e substitua o `[Protocol, Model]` linha com o seguinte:

```csharp
[BaseType(typeof(NSObject))]
[Model]
```
Para que a definição é semelhante a:

[![](walkthrough-images/os11.png "A definição")](walkthrough-images/os11.png#lightbox)

Em seguida, fazemos a mesma coisa com o conteúdo do `InfColorPicker.enums.cs` arquivo, copiando e colando-os em de `StructsAndEnums.cs` arquivo deixando o `using` instruções intactas:

[![](walkthrough-images/os09.png "O conteúdo a StructsAndEnums.cs arquivo ")](walkthrough-images/os09.png#lightbox)

Você também pode achar que o objetivo Sharpie tem anotado a associação com `[Verify]` atributos. Esses atributos indicam que você deve verificar que o objetivo Sharpie fez a coisa correta, comparando a ligação com a declaração do C/Objective-C original (que será fornecida em um comentário acima da declaração associada). Depois de verificar as associações, você deve remover o atributo de verificar. Para obter mais informações, consulte o [Verify](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) guia.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)


Neste ponto, nosso projeto de associação deve ser concluído e pronto para compilar. Vamos criar nosso projeto de associação e certifique-se de que acabamos sem erros:

[Compilar o projeto de associação e verifique se que não existem erros](walkthrough-images/os12.png)


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


Neste ponto, nosso projeto de associação deve ser concluído e pronto para compilar. Vamos criar nosso projeto de associação e certifique-se de que acabamos sem erros.


-----

<a name="Using_the_Binding"/>

## <a name="using-the-binding"></a>Usando a associação

Siga estas etapas para criar um aplicativo de iPhone de exemplo para usar o biblioteca de vinculação criada acima do iOS:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. **Criar o projeto xamarin. IOS** -adicionar um novo projeto do xamarin. IOS chamado **InfColorPickerSample** à solução, conforme mostrado nas capturas de tela seguir:

    ![](walkthrough-images/use01.png "Adicionar um aplicativo do modo de exibição único")

    ![](walkthrough-images/use01a.png "Definir o identificador")

1. **Adicionar a referência ao projeto de associação** -Update a **InfColorPickerSample** do projeto para que ele tenha uma referência para o **InfColorPickerBinding** projeto:

    ![](walkthrough-images/use02.png "Adicionando referência ao projeto de associação")

1. **Criar o Interface do usuário do iPhone** -clique duas vezes no **Mainstoryboard** arquivo o **InfColorPickerSample** projeto para editá-lo no Designer do iOS. Adicionar um **botão** para o modo de exibição e chamá-lo `ChangeColorButton`, conforme mostrado a seguir:

    ![](walkthrough-images/use03.png "Adicionando um botão para o modo de exibição")

1. **Adicionar o InfColorPickerView.xib** -o InfColorPicker Objective-C biblioteca inclui um **. XIB** arquivo. Xamarin. IOS não incluirá isso **. XIB** no projeto de associação, que fará com que os erros de tempo de execução em nosso aplicativo de exemplo. A solução para isso é adicionar o **. XIB** arquivo ao nosso projeto xamarin. IOS. Selecione o projeto xamarin. IOS, o botão direito do mouse e selecione **Adicionar > Adicionar arquivos**e adicione o **. XIB** conforme mostrado na seguinte captura de tela de arquivo:

    ![](walkthrough-images/use04.png "Adicionar o InfColorPickerView.xib")

1. Quando solicitado, copie o **. XIB** arquivo no projeto.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. **Criar o projeto xamarin. IOS** -adicionar um novo projeto do xamarin. IOS chamado **InfColorPickerSample** usando o **aplicativo de exibição única** modelo:

    [![projeto do iOS App (Xamarin)](walkthrough-images/use01.w157-sml.png)](walkthrough-images/use01.w157.png#lightbox)

    [![Selecionar modelo](walkthrough-images/use01-2.w157-sml.png)](walkthrough-images/use01-2.w157.png#lightbox)

1. **Adicionar a referência ao projeto de associação** -Update a **InfColorPickerSample** do projeto para que ele tenha uma referência para o **InfColorPickerBinding** projeto:

    ![](walkthrough-images/use02vs.png "Adicionar a referência ao projeto de associação")

1. **Criar o Interface do usuário do iPhone** -clique duas vezes no **Mainstoryboard** arquivo o **InfColorPickerSample** projeto para editá-lo no Designer do iOS. Adicionar um **botão** para o modo de exibição e chamá-lo `ChangeColorButton`, conforme mostrado a seguir:

    ![](walkthrough-images/use03vs.png "Criar o Interface do usuário do iPhone")

1. **Adicionar o InfColorPickerView.xib** -o InfColorPicker Objective-C biblioteca inclui um **. XIB** arquivo. Xamarin. IOS não incluirá isso **. XIB** no projeto de associação, que fará com que os erros de tempo de execução em nosso aplicativo de exemplo. A solução para isso é adicionar o **. XIB** arquivo ao nosso projeto xamarin. IOS do nosso **Host de Build do Mac**. Selecione o projeto xamarin. IOS, o botão direito do mouse e selecione **Add** > **Item existente...** e adicione o **. XIB** arquivo.

-----

Em seguida, vamos dar uma olhada nos protocolos em Objective-C e como lidar com elas na associação e C# código.

### <a name="protocols-and-xamarinios"></a>Protocolos e xamarin. IOS

Em Objective-C, um protocolo define métodos (ou mensagens) que pode ser usado em determinadas circunstâncias. Conceitualmente, eles são muito semelhantes às interfaces no C#. Uma das principais diferenças entre um protocolo de Objective-C e um C# interface é que os protocolos podem ter métodos opcionais - métodos que não tem uma classe para implementar. Objective-C usa a @optional palavra-chave é usada para indicar quais métodos são opcionais. Para obter mais informações sobre protocolos, consulte [eventos, protocolos e delegados](~/ios/app-fundamentals/delegates-protocols-and-events.md).

**InfColorPickerController** tem um esse protocolo, mostrado no trecho de código abaixo:

```csharp
@protocol InfColorPickerControllerDelegate

@optional

- (void) colorPickerControllerDidFinish: (InfColorPickerController*) controller;
// This is only called when the color picker is presented modally.

- (void) colorPickerControllerDidChangeColor: (InfColorPickerController*) controller;

@end
```

Esse protocolo é usado pelo **InfColorPickerController** para informar aos clientes que o usuário selecionou uma nova cor e que o **InfColorPickerController** for concluído. Objetivo Sharpie mapeado esse protocolo, conforme mostrado no trecho de código a seguir:

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

Quando a biblioteca de ligação é compilada, o xamarin. IOS criará uma classe base abstrata chamada `InfColorPickerControllerDelegate`, que implementa essa interface com métodos virtuais.

Há duas maneiras que podemos implementar essa interface em um aplicativo xamarin. IOS:

- **Forte delegar** -usando um delegado de alta segurança envolve a criação de um C# que pode efetuar subclasses de classes `InfColorPickerControllerDelegate` e substitui os métodos apropriados. **InfColorPickerController** usará uma instância dessa classe para se comunicar com seus clientes.
- **Delegado fraco** -um delegado fraco é uma técnica ligeiramente diferente que envolve a criação de um método público em uma classe (como `InfColorPickerSampleViewController`) e, em seguida, expor esse método para o `InfColorPickerDelegate` por meio do protocolo um `Export` atributo.

Forte delegados fornecem Intellisense, segurança de tipo e melhor encapsulamento. Por esses motivos, você deve usar delegados forte onde você pode, em vez de um delegado fraco.

Este passo a passo, vamos discutir as duas técnicas: implementar primeiro um delegado de alta segurança e, em seguida, explica como implementar um delegado fraco.

### <a name="implementing-a-strong-delegate"></a>Implementando um delegado de alta segurança

Concluir o aplicativo xamarin. IOS usando um delegado de alta segurança para responder ao `colorPickerControllerDidFinish:` mensagem:

**Subclasse InfColorPickerControllerDelegate** -adicione uma nova classe ao projeto chamado `ColorSelectedDelegate`. Edite a classe para que ele tenha o código a seguir:

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

Xamarin. IOS associará o delegado de Objective-C, criando uma classe base abstrata chamada `InfColorPickerControllerDelegate`. Subclasse esse tipo e a substituição de `ColorPickerControllerDidFinish` método para acessar o valor da `ResultColor` propriedade de `InfColorPickerController`.

**Criar uma instância de ColorSelectedDelegate** -nosso manipulador de eventos precisará de uma instância das `ColorSelectedDelegate` tipo que criamos na etapa anterior. Edite a classe `InfColorPickerSampleViewController` e adicione a seguinte variável de instância à classe:

```csharp
ColorSelectedDelegate selector;
```

**Inicialize a variável ColorSelectedDelegate** – para garantir que `selector` é válida da instância, atualize o método `ViewDidLoad` em `ViewController` para coincidir com o trecho a seguir:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithStrongDelegate;
    selector = new ColorSelectedDelegate (this);
}
```
**Implementar o método HandleTouchUpInsideWithStrongDelegate** -em seguida, implementar o manipulador de eventos para quando o usuário toca **ColorChangeButton**. Editar `ViewController`e adicione o seguinte método:

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

Primeiro, obtemos uma instância do `InfColorPickerController` por meio de um método estático e tornar essa instância ciente do nosso delegado forte por meio da propriedade `InfColorPickerController.Delegate`. Essa propriedade foi gerada automaticamente para nós por objetivo Sharpie. Por fim, chamamos `PresentModallyOverViewController` para mostrar o modo de exibição `InfColorPickerSampleViewController.xib` para que o usuário pode selecionar uma cor.

**Executar o aplicativo** – nesse momento terminamos com todo o nosso código. Se você executar o aplicativo, você deve ser capaz de alterar a cor de fundo a `InfColorColorPickerSampleView` conforme mostrado nas capturas de tela seguir:

[![](walkthrough-images/run01.png "Executando o aplicativo")](walkthrough-images/run01.png#lightbox)

Parabéns! Neste ponto você com êxito criado e associado a uma biblioteca Objective-C para uso em um aplicativo xamarin. IOS. Em seguida, vamos aprender sobre como usar delegados fracos.

### <a name="implementing-a-weak-delegate"></a>Implementando um delegado fraco

Em vez de uma classe associada ao protocolo Objective-C para um representante específico da subclasse, xamarin. IOS também permite que você implemente os métodos de protocolo em qualquer classe que deriva de `NSObject`, decorar os métodos com o `ExportAttribute`e, em seguida, fornecendo os seletores apropriados. Quando você usar essa abordagem, você atribuir uma instância da classe para o `WeakDelegate` propriedade em vez de para o `Delegate` propriedade. Um delegado fraco oferece a flexibilidade para levar sua classe de delegado para baixo de uma hierarquia de herança diferente. Vamos ver como implementar e usar um delegado fraco em nosso aplicativo xamarin. IOS.

**Criar o manipulador de eventos para TouchUpInside** -vamos criar um novo manipulador de eventos para o `TouchUpInside` evento do botão Alterar a cor de plano de fundo. Esse manipulador preencherá a mesma função que o `HandleTouchUpInsideWithStrongDelegate` manipulador que é criado na seção anterior, mas usa um delegado fraco em vez de um delegado de alta segurança. Edite a classe `ViewController`e adicione o seguinte método:

```csharp
private void HandleTouchUpInsideWithWeakDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.WeakDelegate = this;
    picker.SourceColor = this.View.BackgroundColor;
    picker.PresentModallyOverViewController (this);
}
```
**Atualizar ViewDidLoad** -devemos alterar `ViewDidLoad` para que ele usa o manipulador de eventos que acabamos de criar. Edite `ViewController` e altere `ViewDidLoad` se pareça com o seguinte trecho de código:


```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithWeakDelegate;
}

```

**Lidar com o colorPickerControllerDidFinish: Mensagem** - quando o `ViewController` é concluído, iOS enviará a mensagem `colorPickerControllerDidFinish:` para o `WeakDelegate`. É necessário criar um C# método que pode lidar com essa mensagem. Para fazer isso, podemos criar um C# método e, em seguida, adorná-lo com o `ExportAttribute`. Editar `ViewController`e adicione o seguinte método à classe:

```csharp
[Export("colorPickerControllerDidFinish:")]
public void ColorPickerControllerDidFinish (InfColorPickerController controller)
{
    View.BackgroundColor = controller.ResultColor;
    DismissViewController (false, null);
}

```

Execute o aplicativo. Agora ele deve se comportar exatamente como fazia antes, mas ele está usando um delegado fraco em vez do delegado de alta segurança. Agora você concluiu com êxito neste passo a passo. Agora você deve ter um entendimento de como criar e consumir um projeto de associação do xamarin. IOS.

## <a name="summary"></a>Resumo

Este artigo percorreu pelo processo de criação e uso de um projeto de associação do xamarin. IOS. Primeiro, discutimos como compilar uma biblioteca Objective-C existente em uma biblioteca estática. Em seguida, abordamos como criar um projeto de associação do xamarin. IOS e como usar o objetivo Sharpie para gerar as definições de API para a biblioteca do Objective-C. Discutimos como atualizar e ajustar as definições de API geradas para torná-los adequado para consumo público. Depois que o projeto de associação do xamarin. IOS tiver sido terminado, passamos para consumo de que a associação em um aplicativo xamarin. IOS, com um foco sobre como usar delegados fortes e fracos delegados.

## <a name="related-links"></a>Links relacionados

- [Exemplo de associação (amostra)](https://developer.xamarin.com/samples/monotouch/InfColorPicker/)
- [Associação de bibliotecas de Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Detalhes da associação](~/cross-platform/macios/binding/overview.md)
- [Guia de referência de tipos de associação](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin para desenvolvedores de Objective-C](~/ios/get-started/objective-c-developers/index.md)
- [Diretrizes de design do Framework](https://msdn.microsoft.com/library/ms229042.aspx)
