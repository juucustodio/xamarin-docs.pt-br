---
title: 'Passo a passo: Associação de uma biblioteca de Objective-C do iOS'
description: Este artigo fornece uma explicação passo a passo prática de criação de uma associação xamarin para uma biblioteca Objective-C existente, InfColorPicker. Ele aborda tópicos como compilar uma biblioteca estática de Objective-C, associação e usando a associação em um aplicativo xamarin.
ms.prod: xamarin
ms.assetid: D3F6FFA0-3C4B-4969-9B83-B6020B522F57
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/02/2017
ms.openlocfilehash: 15e1f53d053046b4a51666647ac846366d3c858b
ms.sourcegitcommit: 4db5f5c93f79f273d8fc462de2f405458b62fc02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2018
ms.locfileid: "34335210"
---
# <a name="walkthrough-binding-an-ios-objective-c-library"></a>Passo a passo: Associação de uma biblioteca de Objective-C do iOS

_Este artigo fornece uma explicação passo a passo prática de criação de uma associação xamarin para uma biblioteca Objective-C existente, InfColorPicker. Ele aborda tópicos como compilar uma biblioteca estática de Objective-C, associação e usando a associação em um aplicativo xamarin._

Ao trabalhar no iOS, você pode encontrar casos em que você deseja consumir uma biblioteca de Objective-C de terceiros. Nessas situações, você pode usar um xamarin _projeto de vinculação_ para criar um [c# associação](~/cross-platform/macios/binding/overview.md) que permitirá que você consuma a biblioteca em seus aplicativos xamarin.

Geralmente no ecossistema do iOS, você pode encontrar bibliotecas de 3 tipos:

* Como um arquivo de biblioteca estática pré-compilado com `.a` extensão junto com seu (s) (arquivos. h). Por exemplo, [biblioteca de análise do Google](https://developers.google.com/analytics/devguides/collection/ios/v3/sdk-download?hl=es#download_sdk)
* Como uma estrutura pré-compilado. Esta é apenas uma pasta que contém a biblioteca estática, cabeçalhos e recursos adicionais, às vezes, com `.framework` extensão. Por exemplo, [AdMob biblioteca do Google](https://developers.google.com/admob/ios/download).
* Como apenas os arquivos de código fonte. Por exemplo, uma biblioteca que contém apenas `.m` e `.h` arquivos Objective C.

O primeiro e o segundo cenário já haverá uma biblioteca estática de CocoaTouch pré-compilados para este artigo nos concentraremos no terceiro cenário. Lembre-se de que, antes de começar a criar uma associação, sempre verifique a licença fornecida com a biblioteca para garantir que você está livre para associá-lo.

Este artigo fornece instruções passo a passo de criação de um projeto de associação usando o código-fonte aberto [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) Objective-C do projeto como um exemplo, no entanto, todas as informações neste guia podem ser adaptadas para uso com qualquer biblioteca de Objective-C de terceiros. A biblioteca de InfColorPicker fornece um controlador de exibição reutilizáveis que permite que o usuário selecione uma cor com base em sua representação HSB, fazer a seleção de cor mais amigável.

[![](walkthrough-images/run01.png "Exemplo da biblioteca InfColorPicker em execução no iOS")](walkthrough-images/run01.png#lightbox)

Vamos abordar todas as etapas necessárias para utilizar essa API em particular Objective-C em xamarin:

- Primeiro, vamos criar uma biblioteca estática Objective-C usando Xcode.
- Em seguida, faremos o vínculo esta biblioteca estática com xamarin.
- Em seguida, mostrar como objetivo Sharpie pode reduzir a carga de trabalho ao gerar automaticamente alguns (mas não todos) das definições de API necessárias exigidas pela associação xamarin.
- Por fim, vamos criar um aplicativo xamarin que usa a associação.

O aplicativo de exemplo demonstrará como usar um delegado de alta segurança para a comunicação entre a API InfColorPicker e nosso código c#. Depois que já vimos como usar um delegado de alta segurança, abordaremos como usar delegados fracos para executar as mesmas tarefas.

## <a name="requirements"></a>Requisitos

Este artigo pressupõe que você tenha alguma familiaridade com a linguagem Objective-C e Xcode e leu nosso [associação Objective-C](~/cross-platform/macios/binding/index.md) documentação. Além disso, é necessário o seguinte para concluir as etapas apresentadas:

-  **Xcode e SDK do iOS** -Xcode da Apple e o API do iOS mais recente precisa ser instalado e configurado no computador do desenvolvedor.
-  **[Ferramentas de linha de comando do Xcode](#Installing_the_Xcode_Command_Line_Tools)**  -as ferramentas de linha de comando do Xcode deve estar instaladas para a versão atualmente instalada do Xcode (consulte abaixo para obter detalhes de instalação).
-  **O Visual Studio para Mac ou o Visual Studio** -a versão mais recente do Visual Studio para Mac ou o Visual Studio deve ser instalada e configurada no computador de desenvolvimento. Um Apple Mac é necessário para desenvolver um aplicativo xamarin e ao usar o Visual Studio deve estar conectado para [um xamarin criar host](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
-  **A versão mais recente do objetivo Sharpie** -obtido por uma cópia atualizada da ferramenta objetivo Sharpie [aqui](~/cross-platform/macios/binding/objective-sharpie/get-started.md). Se você já tiver o objetivo Sharpie instalado, você pode atualizá-lo para a versão mais recente usando o `sharpie update`

<a name="Installing_the_Xcode_Command_Line_Tools"/>

## <a name="installing-the-xcode-command-line-tools"></a>Instalando as ferramentas de linha de comando do Xcode

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)


Como mencionado acima, estaremos usando ferramentas de linha de comando do Xcode (especificamente `make` e `lipo`) neste passo a passo. O `make` comando é um utilitário Unix muito comum que automatiza a compilação de programas executáveis e bibliotecas usando um _makefile_ que especifica como o programa deve ser criado. O `lipo` comando é um utilitário de linha de comando OS X para a criação de arquitetura de vários arquivos; ele combina vários `.a` arquivos em um arquivo que pode ser usado por todas as arquiteturas de hardware.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


Como mencionado acima, estaremos usando ferramentas de linha de comando do Xcode no **Mac criar Host** (especificamente `make` e `lipo`) neste passo a passo. O `make` comando é um utilitário Unix muito comum que automatiza a compilação de programas executáveis e bibliotecas usando um _makefile_ Especifica como o programa de compilação. O `lipo` comando é um utilitário de linha de comando OS X para a criação de arquitetura de vários arquivos; ele combina vários `.a` arquivos em um arquivo que pode ser usado por todas as arquiteturas de hardware.


-----

De acordo com a da Apple [compilando na linha de comando com perguntas frequentes sobre o Xcode](https://developer.apple.com/library/ios/technotes/tn2339/_index.html) documentação, nos X 10.9 e superior, o **Downloads** painel do Xcode **preferências** caixa de diálogo não oferece mais suporte as ferramentas de linha de comando de download.

Você precisará usar um dos seguintes métodos para instalar as ferramentas:

- **Instalar o Xcode** - quando você instala o Xcode, ele é fornecido com todas as suas ferramentas de linha de comando. Nos X 10.9 shims (instalado no `/usr/bin`), poderá mapear qualquer ferramenta incluída no `/usr/bin` para a ferramenta correspondente no Xcode. Por exemplo, o `xcrun` comando, que permite que você localize ou execute qualquer ferramenta de Xcode da linha de comando.
- **O aplicativo de Terminal** -do aplicativo de Terminal, você pode instalar as ferramentas de linha de comando executando o `xcode-select --install` comando:
    - Inicie o aplicativo de Terminal.
    - Tipo `xcode-select --install` e pressione **Enter**, por exemplo:

    ```bash
    Europa:~ kmullins$ xcode-select --install
    ```

    - Você será solicitado a instalar as ferramentas de linha de comando, clique no **instalar** botão: [ ![ ] (walkthrough-images/xcode01.png "instalar as ferramentas de linha de comando")](walkthrough-images/xcode01.png#lightbox)

    - As ferramentas serão baixadas e instaladas a partir de servidores da Apple: [ ![ ] (walkthrough-images/xcode02.png "baixar as ferramentas")](walkthrough-images/xcode02.png#lightbox)

- **Downloads para desenvolvedores da Apple** -pacote das ferramentas de linha de comando está disponível a [Downloads para desenvolvedores da Apple]() página da web. Faça logon com sua ID da Apple, em seguida, procurar e baixar as ferramentas de linha de comando: [ ![ ] (walkthrough-images/xcode03.png "encontrar as ferramentas de linha de comando")](walkthrough-images/xcode03.png#lightbox)

Com as ferramentas de linha de comando instalado, estamos prontos para continuar com o passo a passo.

## <a name="walkthrough"></a>Passo a passo

Neste passo a passo, vamos abordar as etapas a seguir:

- **[Criar uma biblioteca estática](#Creating_A_Static_Library)**  -esta etapa envolve a criação de uma biblioteca estática do **InfColorPicker** código Objective-C. A biblioteca estática terá o `.a` extensão de arquivo e será inserido no assembly .NET do projeto de biblioteca.
- **[Criar um projeto de associação xamarin](#Create_a_Xamarin.iOS_Binding_Project)**  -assim que tivermos uma biblioteca estática, usaremos-lo para criar um projeto de associação xamarin. O projeto de vinculação consiste de biblioteca estática que acabamos de criar e metadados na forma de código em c# que explica como a API Objective-C pode ser usada. Esses metadados é conhecido como as definições de API. Vamos usar **[objetivo Sharpie](#Using_Objective_Sharpie)** para nos ajudar a criar as definições de API.
- **[Normalizar as definições de API](#Normalize_the_API_Definitions)**  - Sharpie objetivo faz um excelente trabalho para nos ajudar a, mas ele não pode fazer tudo. Vamos discutir algumas alterações que precisamos fazer as definições de API antes que possam ser usados.
- **[Use a biblioteca de ligação](#Using_the_Binding)**  -por fim, vamos criar um aplicativo xamarin para mostrar como usar nosso projeto de associação recém-criada.

Agora que sabemos quais etapas são envolvidas, vamos passar para o restante do passo a passo.

<a name="Creating_A_Static_Library"/>

## <a name="creating-a-static-library"></a>Criando uma biblioteca estática

Se, inspecione o código para InfColorPicker no Github:

[![](walkthrough-images/image02.png "Inspecione o código para InfColorPicker no Github")](walkthrough-images/image02.png#lightbox)

Podemos ver os seguintes três diretórios no projeto:

- **InfColorPicker** -este diretório contém o código Objective-C para o projeto.
- **PickerSamplePad** -este diretório contém um projeto de exemplo iPad.
- **PickerSamplePhone** -este diretório contém um projeto de exemplo do iPhone.

Vamos baixar o projeto de InfColorPicker de [GitHub](https://github.com/InfinitApps/InfColorPicker/archive/master.zip) e descompacte-o no diretório de sua escolha. Abrindo o destino do Xcode para `PickerSamplePhone` projeto, podemos ver a seguinte estrutura de projeto no navegador de Xcode:

[![](walkthrough-images/image03.png "A estrutura do projeto no navegador de Xcode")](walkthrough-images/image03.png#lightbox)

Este projeto atinge a reutilização de código adicionando diretamente o código-fonte InfColorPicker (na caixa vermelha) para cada projeto de exemplo. O código para o projeto de exemplo está dentro da caixa azul. Como este projeto não nos fornecer uma biblioteca estática, é necessário para nós criar um projeto Xcode para compilar a biblioteca estática.

A primeira etapa é para adicionar o código-fonte InfoColorPicker para a biblioteca estática. Para fazer isso vamos fazer o seguinte:

1. Inicie o Xcode.
2. Do **arquivo** menu, selecione **novo** > **projeto...** :

    [![](walkthrough-images/image04.png "Iniciando um novo projeto")](walkthrough-images/image04.png#lightbox)
3. Selecione **Framework & biblioteca**, o **biblioteca estática Touch Cocoa** modelo e clique no **próximo** botão:

    [![](walkthrough-images/image05.png "Selecione o modelo de biblioteca estática Touch Cocoa")](walkthrough-images/image05.png#lightbox)

4. Digite `InfColorPicker` para o **nome do projeto** e clique no **próximo** botão:

    [![](walkthrough-images/image06.png "Digite InfColorPicker para o nome do projeto")](walkthrough-images/image06.png#lightbox)
5. Selecione um local para salvar o projeto e clique no **Okey** botão.
6. Agora, precisamos adicionar o código-fonte do projeto InfColorPicker ao nosso projeto de biblioteca estática. Porque o **InfColorPicker.h** arquivo já existe em nossa biblioteca estática (por padrão), Xcode não permitirá que substituí-lo. Do **localizador**, navegue até o código-fonte InfColorPicker no projeto original são descompactados do GitHub, copie todos os arquivos InfColorPicker e colá-los em nosso novo projeto de biblioteca estática:

    [![](walkthrough-images/image12.png "Copiar todos os arquivos de InfColorPicker")](walkthrough-images/image12.png#lightbox)

7. Retornar à Xcode, clique com o botão direito no **InfColorPicker** pasta e selecione **adicionar arquivos ao "InfColorPicker …"**:

    [![](walkthrough-images/image08.png "Adicionando arquivos")](walkthrough-images/image08.png#lightbox)

8. Na caixa de diálogo Adicionar arquivos, navegue até os arquivos de código de origem InfColorPicker que acabou de copiar, selecioná-los e clique no **adicionar** botão:

    [![](walkthrough-images/image09.png "Selecionar tudo e clique no botão Adicionar")](walkthrough-images/image09.png#lightbox)

9. O código-fonte será copiado para o nosso projeto:

    [![](walkthrough-images/image10.png "O código-fonte será copiado para o projeto")](walkthrough-images/image10.png#lightbox)

10. No navegador de projeto Xcode, selecione o **InfColorPicker.m** de arquivo e comente as duas últimas linhas (devido a essa biblioteca foi escrito, esse arquivo não é usado de maneira):

    [![](walkthrough-images/image14.png "Editar o arquivo InfColorPicker.m")](walkthrough-images/image14.png#lightbox)

11. Agora, precisamos verificar se há quaisquer estruturas necessárias para a biblioteca. Você pode encontrar essas informações no arquivo README ou abrindo um dos projetos de exemplo fornecidos. Este exemplo usa `Foundation.framework`, `UIKit.framework`, e `CoreGraphics.framework` vamos adicioná-los.

12. Selecione o **InfColorPicker destino > fases de compilação** e expanda o **Link binário com bibliotecas** seção:

    [![](walkthrough-images/image16b.png "Expanda a seção de Link binário com bibliotecas")](walkthrough-images/image16b.png#lightbox)

13. Use o **+** para abrir a caixa de diálogo que permite adicionar as estruturas de quadros necessários listados acima:

    [![](walkthrough-images/image16c.png "Adicionar que as estruturas de quadros necessários listados acima")](walkthrough-images/image16c.png#lightbox)

14. O **Link binário com bibliotecas** seção agora deve ser semelhante a imagem a seguir:

    [![](walkthrough-images/image16d.png "A seção de Link binário com bibliotecas")](walkthrough-images/image16d.png#lightbox)

Agora estamos fechar, mas não está pronto. A biblioteca estática foi criada, mas precisamos para compilá-lo para criar uma Fat binário que inclui todas as arquiteturas necessárias para o dispositivo iOS e do simulador de iOS.

### <a name="creating-a-fat-binary"></a>Criando um binário Fat

Todos os dispositivos iOS possuem processadores usando a arquitetura ARM desenvolveram ao longo do tempo. Cada nova arquitetura adicionadas novas instruções e outras melhorias e ainda manter compatibilidade com versões anteriores. Em dispositivos iOS temos armv6, armv7, armv7s, conjuntos de instruções de arm64 – Embora [não usamos armv6](~/ios/deploy-test/compiling-for-different-devices.md). O simulador de iOS não é alimentado por ARM e em vez um x86 e x86_64 alimentado simulador. É o que significa para nós é que podemos deve fornecer bibliotecas em cada instrução define.

É uma biblioteca Fat `.a` arquivo que contém todas as arquiteturas com suporte.

Criar uma fat binário é um processo de três etapas:

- Compile uma versão de 7 de ARM e ARM64 da biblioteca estática.
- Compile uma versão x86 e x84_64 da biblioteca estática.
- Use o `lipo` ferramenta de linha de comando para combinar as duas bibliotecas estáticas em uma.

Embora essas três etapas simples, e talvez seja necessário repeti-los no futuro, quando a biblioteca Objective-C recebe atualizações ou se exigimos correções de bugs. Se você decidir automatizar estas etapas, isso simplificará o suporte do projeto de vinculação de iOS e manutenção futura.

Há muitas ferramentas disponíveis para automatizar tarefas - um script de shell, [rake](http://rake.rubyforge.org/), [xbuild](http://www.mono-project.com/docs/tools+libraries/tools/xbuild/), e [fazer](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/make.1.html). Quando é instalado as ferramentas de linha de comando do Xcode, podemos também instalado Verifique, de forma que é o sistema de compilação que será usado para este passo a passo. Aqui está uma **Makefile** que você pode usar para criar uma biblioteca compartilhada de multi-arquitetura que funcione em um dispositivo iOS e o simulador para qualquer biblioteca:

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

Insira o **Makefile** comandos no editor de texto sem formatação de sua escolha e atualize as seções com **nome do seu projeto** com o nome do seu projeto. Também é importante garantir que estamos cole as instruções acima, as guias dentro das instruções foram preservadas.

Salve o arquivo com o nome **Makefile** no mesmo local como a biblioteca estática do Xcode InfColorPicker criado anteriormente:

[![](walkthrough-images/lib00.png "Salve o arquivo com o nome de Makefile")](walkthrough-images/lib00.png#lightbox)

Abra o aplicativo de Terminal no seu Mac e navegue até o local do seu Makefile. Tipo `make` no Terminal, pressione **Enter** e **Makefile** será executada:

[![](walkthrough-images/lib01.png "Exemplo de saída de makefile")](walkthrough-images/lib01.png#lightbox)

Quando você executa o fazer, você verá muita rolagem por de texto. Se tudo funcionou corretamente, você verá as palavras **compilação bem-sucedida** e `libInfColorPicker-armv7.a`, `libInfColorPicker-i386.a` e `libInfColorPickerSDK.a` arquivos serão copiados para o mesmo local que o **Makefile**:

[![](walkthrough-images/lib02.png "Os arquivos de libInfColorPicker armv7.a, libInfColorPicker i386.a e libInfColorPickerSDK.a gerados pelo Makefile")](walkthrough-images/lib02.png#lightbox)

Você pode confirmar as arquiteturas dentro de seu binário Fat usando o seguinte comando:

```bash
xcrun -sdk iphoneos lipo -info libInfColorPicker.a
```

Isso deve exibir o seguinte:

```bash
Architectures in the fat file: libInfColorPicker.a are: i386 armv7 x86_64 arm64
```

Neste ponto, concluímos a primeira etapa da nossa associação iOS por meio da criação de uma biblioteca estática usando Xcode e as ferramentas de linha de comando do Xcode `make` e `lipo`. Vamos passar para a próxima etapa e usar **Sharpie objetivo** para automatizar a criação das associações de API para nós.

<a name="Create_a_Xamarin.iOS_Binding_Project"/>

## <a name="create-a-xamarinios-binding-project"></a>Criar um projeto de vinculação de xamarin

Antes que possamos usar **objetivo Sharpie** para automatizar o processo de associação, é preciso criar um projeto de associação xamarin para armazenar as definições de API (que usaremos **Sharpie objetivo** para nos ajudar a compilação) e criar a associação do c# para nós.

Vamos fazer o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Inicie o Visual Studio para Mac.
1. Do **arquivo** menu, selecione **novo** > **solução...** :

    ![](walkthrough-images/bind01.png "Iniciando uma nova solução")

1. Na caixa de diálogo nova solução, selecione **biblioteca** > **iOS projeto associação**:

    ![](walkthrough-images/bind02.png "Selecione o projeto de vinculação do iOS")

1. Clique o **próximo** botão.

1. Insira "InfColorPickerBinding" como o **nome do projeto** e clique no **criar** botão para criar a solução:

    ![](walkthrough-images/bind02a.png "Digite InfColorPickerBinding como o nome do projeto")

A solução será criada e dois arquivos padrão será incluídos:

![](walkthrough-images/bind03.png "A estrutura da solução no Gerenciador de soluções")


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


1. Inicie o Visual Studio.

1. Do **arquivo** menu, selecione **novo** > **projeto...** :

    ![Iniciando um novo projeto](walkthrough-images/bind01vs.png "iniciando um novo projeto")

1. Na caixa de diálogo Novo projeto, selecione **Visual C# > iPhone & iPad > iOS associações de biblioteca (Xamarin)**:

    [![Selecione a biblioteca de associações do iOS](walkthrough-images/bind02.w157-sml.png)](walkthrough-images/bind02.w157.png#lightbox)

1. Insira "InfColorPickerBinding" como o **nome** e clique no **Okey** botão para criar a solução.

A solução será criada e dois arquivos padrão será incluídos:

![](walkthrough-images/bind03vs.png "A estrutura da solução no Gerenciador de soluções")

-----

- **ApiDefinition.cs** -este arquivo contém os contratos que definem como Objective-C API será encapsulado em c#.
- **Structs.CS** - esse arquivo irá conter qualquer estrutura ou valores de enumeração necessários para as interfaces e delegados.

Trabalharemos com esses dois arquivos posteriormente no passo a passo. Primeiro, precisamos adicionar a biblioteca de InfColorPicker para o projeto de associação.

### <a name="including-the-static-library-in-the-binding-project"></a>Incluindo a biblioteca estática no projeto de vinculação

Agora temos nosso projeto base de associação pronto, precisamos adicionar a biblioteca Fat binário criado anteriormente para o **InfColorPicker** biblioteca.

Siga estas etapas para adicionar a biblioteca:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Clique com botão direito no **referências nativo** pasta no preenchimento da solução e selecione **adicionar referências nativo**:

    ![](walkthrough-images/bind04a.png "Adicione referências nativo")

1. Navegue até o Fat binário fizemos anteriormente (`libInfColorPickerSDK.a`) e pressione a **abrir** botão:

    ![](walkthrough-images/bind05.png "Selecione o arquivo libInfColorPickerSDK.a")
1. O arquivo será incluído no projeto:

    ![](walkthrough-images/bind04.png "Incluindo um arquivo")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Copiar o `libInfColorPickerSDK.a` de seu **Mac criar Host** e colá-lo em seu projeto de associação.

1. Clique com botão direito no projeto e escolha **Adicionar > Item existente...** :

    ![](walkthrough-images/bind04vs.png "Adicionando um arquivo existente")

1. Navegue até o `libInfColorPickerSDK.a` e pressione a **adicionar** botão:

    ![](walkthrough-images/bind05vs.png "Adicionando libInfColorPickerSDK.a")

1. O arquivo será incluído no projeto.

-----

Quando o **.a** arquivo é adicionado ao projeto, xamarin definirá automaticamente a **ação de compilação** do arquivo a ser **ObjcBindingNativeLibrary**e criar um arquivo especial chamado `libInfColorPickerSDK.linkwith.cs`.


Este arquivo contém o `LinkWith` atributo que informa o xamarin como identificador de biblioteca estática que simplesmente adicionado. O conteúdo desse arquivo é mostrado no trecho de código a seguir:

```csharp
using ObjCRuntime;

[assembly: LinkWith ("libInfColorPickerSDK.a", SmartLink = true, ForceLoad = true)]
```

O `LinkWith` atributo identifica a biblioteca estática para o projeto e alguns sinalizadores do vinculador importantes.


A próxima coisa que precisamos fazer é criar as definições de API para o projeto InfColorPicker. Para os fins deste passo a passo, usaremos objetivo Sharpie para gerar o arquivo **ApiDefinition.cs**.

<a name="Using_Objective_Sharpie"/>

## <a name="using-objective-sharpie"></a>Usando Sharpie objetivo

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)


Objetivo Sharpie é uma linha de comando (fornecida pelo Xamarin) de ferramenta que pode ajudar a criar as definições necessárias para associar uma biblioteca de Objective-C 3ª parte para c#. Nesta seção, vamos usar objetivo Sharpie criar inicial **ApiDefinition.cs** para o projeto InfColorPicker.


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


Objetivo Sharpie é uma linha de comando (fornecida pelo Xamarin) de ferramenta que pode ajudar a criar as definições necessárias para associar uma biblioteca de Objective-C 3ª parte para c#. Nesta seção, vamos usar objetivo Sharpie em nosso **Mac criar Host** criar inicial **ApiDefinition.cs** para o projeto InfColorPicker.


-----

Para começar, vamos baixar arquivo do instalador Sharpie objetivo conforme detalhado na [guia](~/cross-platform/macios/binding/objective-sharpie/get-started.md#installing). Execute o instalador e siga todas as instruções na tela do Assistente de instalação para instalar o objetivo Sharpie em nosso computador de desenvolvimento.

Depois que temos objetivo Sharpie com êxito instalado, vamos iniciar o aplicativo de Terminal e insira o seguinte comando para obter ajuda sobre todas as ferramentas que ele fornece para auxiliar na associação de:

```bash
sharpie -help
```

Se podemos executar o comando acima, a seguinte saída será gerada:

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

- **Xcode** – essa ferramentas nos fornece informações sobre nossa instalação Xcode atual e as versões do iOS e Mac APIs que podemos instalados. Usaremos essas informações posteriormente quando geramos nosso associações.
- **associar** -vamos usar essa ferramenta para analisar o **. h** arquivos no projeto InfColorPicker em inicial **ApiDefinition.cs** e **StructsAndEnums.cs** arquivos.

Para obter ajuda sobre uma ferramenta Sharpie objetivo específica, digite o nome da ferramenta e o `-help` opção. Por exemplo, `sharpie xcode -help` retorna a seguinte saída:

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

Para que possamos começar o processo de ligação, precisamos obter informações sobre nossos SDKs instalados atuais digitando o seguinte comando no Terminal `sharpie xcode -sdks`:

```bash
amyb:Desktop amyb$ sharpie xcode -sdks
sdk: appletvos9.2    arch: arm64
sdk: iphoneos9.3     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: watchos2.2      arch: armv7
```

Acima, podemos ver que temos o `iphoneos9.3` SDK instalado na máquina. Com essas informações em vigor, você está pronto para analisar o projeto InfColorPicker `.h` arquivos em inicial **ApiDefinition.cs** e `StructsAndEnums.cs` para o projeto InfColorPicker.

Digite o seguinte comando no aplicativo de Terminal:

```bash
sharpie bind --output=InfColorPicker --namespace=InfColorPicker --sdk=[iphone-os] [full-path-to-project]/InfColorPicker/InfColorPicker/*.h
```

Onde `[full-path-to-project]` é o caminho completo para o diretório onde o **InfColorPicker** arquivo de projeto Xcode está localizado em nosso computador e [iphone-os] é o iOS SDK instalou, conforme observado o `sharpie xcode -sdks` comando. Observe que neste exemplo é passou  **\*. h** como um parâmetro, que inclui *todos os* os arquivos de cabeçalho neste diretório - normalmente, você deve não fazer isso, mas em vez disso, leia atentamente por meio de arquivos de cabeçalho para localizar o nível superior **. h** arquivo faz referência a todos os outros arquivos relevantes e apenas passá-lo para Sharpie objetivo.

O seguinte [saída](walkthrough-images/os05.png) será gerado no terminal:

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

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)


Abra ambos os arquivos no projeto de associação que criamos acima. Copie o conteúdo do **InfColorPicker.cs** de arquivo e cole-o no **ApiDefinition.cs** arquivo, substituindo existente `namespace ...` bloco de código com o conteúdo do  **InfColorPicker.cs** arquivo (deixando o `using` instruções intactas):

![](walkthrough-images/os07.png "O arquivo InfColorPickerControllerDelegate")


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


Abra ambos os arquivos no projeto de associação que criamos acima. Copie o conteúdo do **InfColorPicker.cs** arquivo (da **Mac criar Host**) e cole-o no **ApiDefinition.cs** arquivo, substituindo existente `namespace ...` bloco de código com o conteúdo do **InfColorPicker.cs** arquivo (deixando o `using` instruções intactas).


-----

<a name="Normalize_the_API_Definitions"/>

## <a name="normalize-the-api-definitions"></a>Normalizar as definições de API

Objetivo Sharpie às vezes, tem um problema convertendo `Delegates`, portanto, será necessário modificar a definição do `InfColorPickerControllerDelegate` interface e substitua o `[Protocol, Model]` linha com o seguinte:

```csharp
[BaseType(typeof(NSObject))]
[Model]
```
Para que a definição é semelhante a:

[![](walkthrough-images/os11.png "A definição")](walkthrough-images/os11.png#lightbox)

Em seguida, fazemos a mesma coisa com o conteúdo do `InfColorPicker.enums.cs` arquivo, copiando e colando-os no `StructsAndEnums.cs` arquivo deixando o `using` instruções intactas:

[![](walkthrough-images/os09.png "O conteúdo de StructsAndEnums.cs arquivo ")](walkthrough-images/os09.png#lightbox)

Você também pode descobrir que o objetivo Sharpie tem anotado a associação com `[Verify]` atributos. Esses atributos indicam que você deve verificar que o objetivo Sharpie foi a ação correta, comparando a associação com a declaração original do C/Objective-C (que será fornecida em um comentário acima da declaração associada). Depois de verificar as associações, você deve remover o atributo de verificação. Para obter mais informações, consulte o [verificar](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) guia.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)


Neste ponto, nosso projeto de associação deve ser concluído e pronto para compilação. Vamos compilar nosso projeto de vinculação e certifique-se de que acabamos sem erros:

[Compilar o projeto de vinculação e verifique se que não existem erros](walkthrough-images/os12.png)


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)


Neste ponto, nosso projeto de associação deve ser concluído e pronto para compilação. Vamos compilar nosso projeto de vinculação e certifique-se de que acabamos sem erros.


-----

<a name="Using_the_Binding"/>

## <a name="using-the-binding"></a>Usando a associação

Siga estas etapas para criar um exemplo de aplicativo de iPhone para usar o biblioteca de associação criada acima do iOS:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. **Criar projeto xamarin** -adicionar um novo projeto de xamarin chamado **InfColorPickerSample** para a solução, conforme mostrado nas capturas de tela seguir:

    ![](walkthrough-images/use01.png "Adicionar um aplicativo de modo único")

    ![](walkthrough-images/use01a.png "Definir o identificador")

1. **Adicionar referência ao projeto de vinculação** -atualização de **InfColorPickerSample** de projeto para que ele tem uma referência para o **InfColorPickerBinding** projeto:

    ![](walkthrough-images/use02.png "Adicionando referência ao projeto de vinculação")

1. **Criar o Interface do usuário do iPhone** -clique duas vezes no **MainStoryboard.storyboard** arquivo o **InfColorPickerSample** projeto para editá-lo no Designer de iOS. Adicionar um **botão** para o modo de exibição e chamá-lo `ChangeColorButton`, conforme mostrado a seguir:

    ![](walkthrough-images/use03.png "Adicionar um botão para o modo de exibição")

1. **Adicionar o InfColorPickerView.xib** -o InfColorPicker Objective-C biblioteca inclui uma **.xib** arquivo. Xamarin não incluirá **.xib** no projeto de associação, que fará com que os erros de tempo de execução em nosso exemplo de aplicativo. A solução para isso é para adicionar o **.xib** arquivo ao nosso projeto xamarin. Selecione o projeto de xamarin, com o botão direito e selecione **Adicionar > Adicionar arquivos**e adicione o **.xib** arquivo conforme mostrado na seguinte captura de tela:

    ![](walkthrough-images/use04.png "Adicionar o InfColorPickerView.xib")

1. Quando solicitado, copie o **.xib** arquivo no projeto.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. **Criar projeto xamarin** -adicionar um novo projeto de xamarin denominado **InfColorPickerSample** usando o **único aplicativo de exibição** modelo:

    [![projeto de aplicativo (Xamarin) do iOS](walkthrough-images/use01.w157-sml.png)](walkthrough-images/use01.w157.png#lightbox)

    [![Selecionar modelo](walkthrough-images/use01-2.w157-sml.png)](walkthrough-images/use01-2.w157.png#lightbox)

1. **Adicionar referência ao projeto de vinculação** -atualização de **InfColorPickerSample** de projeto para que ele tem uma referência para o **InfColorPickerBinding** projeto:

    ![](walkthrough-images/use02vs.png "Adicionar referência ao projeto de vinculação")

1. **Criar o Interface do usuário do iPhone** -clique duas vezes no **MainStoryboard.storyboard** arquivo o **InfColorPickerSample** projeto para editá-lo no Designer de iOS. Adicionar um **botão** para o modo de exibição e chamá-lo `ChangeColorButton`, conforme mostrado a seguir:

    ![](walkthrough-images/use03vs.png "Criar o Interface do usuário do iPhone")

1. **Adicionar o InfColorPickerView.xib** -o InfColorPicker Objective-C biblioteca inclui uma **.xib** arquivo. Xamarin não incluirá **.xib** no projeto de associação, que fará com que os erros de tempo de execução em nosso exemplo de aplicativo. A solução para isso é para adicionar o **.xib** arquivo ao nosso projeto xamarin do nosso **Mac criar Host**. Selecione o projeto de xamarin, com o botão direito e selecione **adicionar** > **Item existente...** e adicione o **.xib** arquivo.

-----

Em seguida, vamos dar uma olhada rápida protocolos em Objective-C e como podemos tratá-los na associação e o código c#.

### <a name="protocols-and-xamarinios"></a>Protocolos e xamarin

Em Objective-C, um protocolo define métodos (ou mensagens) que podem ser usados em determinadas circunstâncias. Conceitualmente, eles são muito semelhantes às interfaces em c#. Uma diferença importante entre um protocolo Objective-C e uma interface c# é que os protocolos podem ter métodos opcionais - métodos que não tem uma classe para implementar. Objective-C usa a @optional palavra-chave é usada para indicar quais métodos são opcionais. Para obter mais informações sobre protocolos, consulte [eventos, protocolos e delegados](~/ios/app-fundamentals/delegates-protocols-and-events.md).

**InfColorPickerController** tem um esse protocolo, mostrado no trecho de código abaixo:

```csharp
@protocol InfColorPickerControllerDelegate

@optional

- (void) colorPickerControllerDidFinish: (InfColorPickerController*) controller;
// This is only called when the color picker is presented modally.

- (void) colorPickerControllerDidChangeColor: (InfColorPickerController*) controller;

@end
```

Esse protocolo é usado por **InfColorPickerController** para informar aos clientes que o usuário tiver selecionado uma nova cor e que o **InfColorPickerController** for concluído. Objetivo Sharpie mapeado esse protocolo, conforme mostrado no trecho de código a seguir:

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

Quando a biblioteca de ligação é compilada, xamarin criará uma classe base abstrata chamada `InfColorPickerControllerDelegate`, que implementa essa interface com métodos virtuais.

Há duas maneiras que podemos implementar essa interface em um aplicativo xamarin:

- **Delegar forte** -usando um delegado forte envolve a criação de uma classe do c# que as subclasses `InfColorPickerControllerDelegate` e substitui os métodos apropriados. **InfColorPickerController** usará uma instância dessa classe para se comunicar com seus clientes.
- **Delegado fraco** -um delegado fraco é uma técnica ligeiramente diferente que envolve a criação de um método público em uma classe (como `InfColorPickerSampleViewController`) e, em seguida, expor esse método para o `InfColorPickerDelegate` protocolo por meio de um `Export` atributo.

Delegados fortes fornecem Intellisense, segurança de tipo e ao encapsulamento melhor. Por esses motivos, você deve usar delegados fortes onde você pode, em vez de um delegado fraco.

Este passo a passo, vamos discutir ambas as técnicas: primeiro, Implementando um delegado de alta segurança e, em seguida, explica como implementar um delegado fraco.

### <a name="implementing-a-strong-delegate"></a>Implementando um delegado de alta segurança

Concluir o aplicativo xamarin usando um delegado de alta segurança para responder a `colorPickerControllerDidFinish:` mensagem:

**Subclasse InfColorPickerControllerDelegate** -adicionar uma nova classe ao projeto chamado `ColorSelectedDelegate`. Edite a classe para que ele tem o código a seguir:

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

Xamarin associará o representante Objective-C, criando uma classe base abstrata chamada `InfColorPickerControllerDelegate`. Subclasse esse tipo e a substituição de `ColorPickerControllerDidFinish` método para acessar o valor da `ResultColor` propriedade de `InfColorPickerController`.

**Criar uma instância de ColorSelectedDelegate** -nosso manipulador de eventos precisará de uma instância das `ColorSelectedDelegate` tipo que criamos na etapa anterior. Edite a classe `InfColorPickerSampleViewController` e adicione a seguinte variável de instância para a classe:

```csharp
ColorSelectedDelegate selector;
```

**Inicializar a variável ColorSelectedDelegate** - para garantir que `selector` é válido da instância, o método update `ViewDidLoad` na `ViewController` para corresponder o trecho a seguir:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithStrongDelegate;
    selector = new ColorSelectedDelegate (this);
}
```
**Implementar o método HandleTouchUpInsideWithStrongDelegate** -seguida implementar o manipulador de eventos quando o usuário toca **ColorChangeButton**. Editar `ViewController`e adicione o seguinte método:

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

Primeiro, obtemos uma instância do `InfColorPickerController` por meio de um método estático e essa instância atento à nosso delegado forte por meio da propriedade `InfColorPickerController.Delegate`. Essa propriedade foi gerada automaticamente para nós com objetivo Sharpie. Por fim, podemos chamar `PresentModallyOverViewController` para mostrar o modo de exibição `InfColorPickerSampleViewController.xib` para que o usuário pode selecionar uma cor.

**Executar o aplicativo** - neste momento terminamos com todo nosso código. Se você executar o aplicativo, você deve ser capaz de alterar a cor de fundo a `InfColorColorPickerSampleView` conforme mostrado nas capturas de tela seguir:

[![](walkthrough-images/run01.png "Executando o aplicativo")](walkthrough-images/run01.png#lightbox)

Parabéns! Neste ponto você com êxito criado e associado a uma biblioteca Objective-C para uso em um aplicativo xamarin. Em seguida, vamos aprender sobre como usar delegados fracos.

### <a name="implementing-a-weak-delegate"></a>Implementando um delegado fraco

Em vez de subclasses de uma classe associada ao protocolo Objective-C para um representante específico, xamarin também permite que você implemente os métodos de protocolo em qualquer classe que deriva de `NSObject`, decorando seus métodos com o `ExportAttribute`e, em seguida, fornecendo os seletores apropriados. Quando você usar essa abordagem, você atribui uma instância de sua classe para o `WeakDelegate` propriedade em vez de para o `Delegate` propriedade. Um delegado fraco oferece a flexibilidade para tomar sua classe delegate para baixo de uma hierarquia de herança diferente. Vamos ver como implementar e usar um delegado fraco em nosso aplicativo xamarin.

**Criar um manipulador de eventos para TouchUpInside** -vamos criar um novo manipulador de eventos para o `TouchUpInside` eventos do botão Alterar a cor de plano de fundo. Este manipulador preencherá a mesma função como o `HandleTouchUpInsideWithStrongDelegate` manipulador que é criado na seção anterior, mas usa um delegado fraco em vez de um delegado de alta segurança. Edite a classe `ViewController`e adicione o seguinte método:

```csharp
private void HandleTouchUpInsideWithWeakDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.WeakDelegate = this;
    picker.SourceColor = this.View.BackgroundColor;
    picker.PresentModallyOverViewController (this);
}
```
**Atualizar ViewDidLoad** -podemos deve alterar `ViewDidLoad` para que ele usa o manipulador de eventos que acabou de criar. Editar `ViewController` e alterar `ViewDidLoad` se pareça com o seguinte trecho de código:


```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithWeakDelegate;
}

```

**Manipular o colorPickerControllerDidFinish: mensagem** - quando o `ViewController` é concluída, iOS enviará a mensagem `colorPickerControllerDidFinish:` para o `WeakDelegate`. É preciso criar um método em c# que pode lidar com essa mensagem. Para fazer isso, crie um método c# e, em seguida, Adornar com o `ExportAttribute`. Editar `ViewController`e adicione o seguinte método à classe:

```csharp
[Export("colorPickerControllerDidFinish:")]
public void ColorPickerControllerDidFinish (InfColorPickerController controller)
{
    View.BackgroundColor = controller.ResultColor;
    DismissViewController (false, null);
}

```

Execute o aplicativo. Agora ele deve se comportam exatamente como fazia antes, mas ele está usando um delegado fraco em vez do delegado de alta segurança. Agora você concluiu com êxito neste passo a passo. Agora você deve ter um entendimento de como criar e consumir um projeto de associação xamarin.

## <a name="summary"></a>Resumo

Este artigo movimentada pelo processo de criação e uso de um projeto de associação xamarin. Primeiro, discutimos como compilar uma biblioteca Objective-C existente em uma biblioteca estática. Em seguida, abordamos como criar um projeto de associação xamarin e como usar o objetivo Sharpie para gerar as definições de API para a biblioteca Objective-C. Discutimos como atualizar e ajustar as definições de API geradas para torná-las mais adequado para consumo público. Depois que o projeto de associação xamarin foi concluído, passamos para essa associação em um aplicativo xamarin, com um foco sobre como usar delegados fortes e fracos delegados de consumo.

## <a name="related-links"></a>Links relacionados

- [Exemplo de associação (exemplo)](https://developer.xamarin.com/samples/monotouch/InfColorPicker/)
- [Associação de bibliotecas de Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Detalhes da associação](~/cross-platform/macios/binding/overview.md)
- [Guia de referência de tipos de associação](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin para desenvolvedores de Objective-C](~/ios/get-started/objective-c-developers/index.md)
- [Diretrizes de design do Framework](http://msdn.microsoft.com/library/ms229042.aspx)
