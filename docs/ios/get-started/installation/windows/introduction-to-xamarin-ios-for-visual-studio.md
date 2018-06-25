---
title: Introdução ao Xamarin.iOS para Visual Studio
description: Este documento descreve como compilar e testar aplicativos do Xamarin.iOS usando o Visual Studio. Ele aborda a criação de um projeto, a execução e a depuração de um aplicativo e a conexão com um host de build do Mac no Windows.
ms.prod: xamarin
ms.assetid: bf3c779f-959f-428d-babb-428f363f7e4e
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/02/2018
ms.openlocfilehash: e07119bee6478a503ca6c586fa3348206ccd16f7
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786194"
---
# <a name="introduction-to-xamarinios-for-visual-studio"></a>Introdução ao Xamarin.iOS para Visual Studio

O Xamarin para Windows permite escrever e testar aplicativos iOS no Visual Studio, com um Mac em rede fornecendo o serviço de build e implantação.

Este artigo aborda as etapas para instalar e configurar as ferramentas Xamarin.iOS em cada computador, para compilar aplicativos iOS usando o Visual Studio.

Desenvolver para o iOS dentro do Visual Studio proporciona vários benefícios:

-  Criar soluções de plataforma cruzada para aplicativos iOS, Android e Windows.
-  Usar suas ferramentas do Visual Studio favoritas (como **Resharper** e **Team Foundation Server**) para todos seus projetos de plataforma cruzada, incluindo código-fonte do iOS.
-  Trabalhar com um IDE familiar, aproveitando as vantagens das associações do Xamarin.iOS de todas as APIs da Apple.

<a name="Requirements_and_Installation" />

## <a name="requirements-and-installation"></a>Requisitos e Instalação

Há alguns requisitos que devem ser seguidos ao desenvolver para iOS no Visual Studio. Como mencionado brevemente na visão geral, um Mac é necessário para compilar arquivos IPA e os aplicativos não podem ser implantados em um dispositivo sem certificados e ferramentas de assinatura de código da Apple.

Há várias opções de configuração disponíveis para você escolher a que funciona melhor para suas necessidades de desenvolvimento. Elas estão listadas abaixo:

-  Usar um Mac como seu computador de desenvolvimento principal e executar uma máquina virtual Windows com Visual Studio instalado. É recomendável usar o software de VM como [Parallels](http://www.parallels.com/products/desktop/) ou [VMWare](http://www.vmware.com/products/fusion/).
-  Usar um Mac apenas como um host de build. Nesse cenário, estaria conectado à mesma rede que um computador Windows com as ferramentas [necessárias](~/cross-platform/get-started/installation/windows.md#installation) instaladas.

Em qualquer caso, você deve seguir estas etapas:

- [Instalar o Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/installation)
- [Instalar as ferramentas Xamarin no Windows](~/cross-platform/get-started/installation/windows.md)

## <a name="connecting-to-the-mac"></a>Conectando-se ao Mac

Para conectar o Visual Studio em seu host de build do Mac, siga as instruções no guia [Emparelhar Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="visual-studio-toolbar-overview"></a>Visão geral da barra de ferramentas do Visual Studio

O Xamarin iOS para o Visual Studio adiciona itens à barra de ferramentas padrão e à nova barra de ferramentas do iOS.
As funções dessas barras de ferramentas são explicadas abaixo.

### <a name="standard-toolbar"></a>Barra de ferramentas padrão

Os controles relevantes para o desenvolvimento do Xamarin iOS estão circulados em vermelho:

 [![](introduction-to-xamarin-ios-for-visual-studio-images/03.png "Os controles relevantes para o desenvolvimento do Xamarin iOS estão circulados em vermelho")](introduction-to-xamarin-ios-for-visual-studio-images/03.png#lightbox "Os controles relevantes para o desenvolvimento do Xamarin iOS estão circulados em vermelho")

-  **Iniciar** – inicia a depuração ou a execução do aplicativo na plataforma selecionada. Deve haver um Mac conectado (consulte o indicador de status na barra de ferramentas do iOS).
-  **Configurações da Solução** – permite selecionar a configuração a ser utilizada (por exemplo, Depuração, Liberação).
-  **Plataformas de Solução** – permite selecionar iPhone ou iPhoneSimulator para implantação.

### <a name="ios-toolbar"></a>Barra de ferramentas do iOS

A barra de ferramentas do iOS no Visual Studio é semelhante em cada versão do Visual Studio. Elas são mostradas abaixo:

[![](introduction-to-xamarin-ios-for-visual-studio-images/iostoolbar.png "Barra de ferramentas do iOS")](introduction-to-xamarin-ios-for-visual-studio-images/iostoolbar.png#lightbox)

Cada item é explicado a seguir:

-  **Gerenciador de Conexão/Agente Mac** – exibe a caixa de diálogo do Agente Mac do Xamarin. Esse ícone aparecerá em *laranja* ao se conectar e em *verde* quando conectado.
-  **Mostrar Simulador de iOS** – traz a janela do Simulador de iOS para a frente no Mac.
-  **Mostrar Arquivo de IPA no Servidor de Build** – abre o Finder no Mac para o local do arquivo de saída IPA do aplicativo.

## <a name="ios-output-options"></a>Opções de saída do iOS

### <a name="output-window"></a>Janela Saída

Há opções no painel *Saída* que você pode exibir para detectar erros e mensagens de build, implantação e conexão.

Captura de tela abaixo mostra as janelas de saída disponíveis, que podem ser diferente conforme o tipo de projeto:

[![](introduction-to-xamarin-ios-for-visual-studio-images/output-sml.png "As janelas de saída disponíveis")](introduction-to-xamarin-ios-for-visual-studio-images/output-large.png#lightbox)

- **Xamarin** – contém informações relacionadas apenas ao Xamarin, como a conexão ao Mac e o status de ativação.

    [![](introduction-to-xamarin-ios-for-visual-studio-images/output3-sml.png "Informações relacionadas apenas ao Xamarin, como a conexão ao Mac e o status de ativação")](introduction-to-xamarin-ios-for-visual-studio-images/output3-large.png#lightbox)

- **Diagnóstico do Xamarin** – mostra informações mais detalhadas sobre seu projeto Xamarin, como a interação com Android.

    [![](introduction-to-xamarin-ios-for-visual-studio-images/output4-sml.png "Informações detalhadas sobre o projeto do Xamarin")](introduction-to-xamarin-ios-for-visual-studio-images/output3-large.png#lightbox)

Outros painéis de Saída do Visual Studio padrão, como Depuração e Build, ainda estão disponíveis na exibição Saída e são usados para Saída de Depuração e Saída do MSBuild:

-  **Depurar**

    [![](introduction-to-xamarin-ios-for-visual-studio-images/output2-sml.png "Saída de Depuração")](introduction-to-xamarin-ios-for-visual-studio-images/output2-large.png#lightbox)

- **Build** & **Ordem de Build**

    [![](introduction-to-xamarin-ios-for-visual-studio-images/output1-sml.png "Saída do MSBuild")](introduction-to-xamarin-ios-for-visual-studio-images/output1-large.png#lightbox)

## <a name="ios-project-properties"></a>Propriedades do projeto iOS

As Propriedades do Projeto do Visual Studio podem ser acessadas clicando com o botão direito do mouse no nome do Projeto e selecionando *Propriedades* no menu de contexto. Isso permitirá configurar seu aplicativo iOS, como mostra a captura de tela abaixo:

 ![](introduction-to-xamarin-ios-for-visual-studio-images/iosproperties.png "Configurando um aplicativo iOS")

-  *Assinatura do Pacote do iOS* – conecta-se ao Mac para preencher as identidades de assinatura de código e os perfis de provisionamento:

 ![](introduction-to-xamarin-ios-for-visual-studio-images/bundlesigning.png "Preencha as identidades de assinatura de código e os perfis de provisionamento")

-  *Opções de IPA do iOS* – o arquivo IPA será salvo no sistema de arquivos do Mac:

 ![](introduction-to-xamarin-ios-for-visual-studio-images/ipaoptions.png "Opções de IPA do iOS")

-  *Opções de Execução do iOS* – configure parâmetros adicionais:

 ![](introduction-to-xamarin-ios-for-visual-studio-images/iosrunoptions.png "Opções de Execução do iOS")

## <a name="creating-a-new-project-for-ios-applications"></a>Como criar um novo projeto para aplicativos iOS

Um novo projeto do iOS no Visual Studio é criado como qualquer outro tipo de projeto. Selecionar **Arquivo > Novo Projeto** abrirá a caixa de diálogo mostrada abaixo, que ilustra alguns dos tipos de projetos disponíveis para criar um projeto do iOS:

![Criando um novo projeto](introduction-to-xamarin-ios-for-visual-studio-images/newproject.w157.png)

Selecionar **Aplicativo iOS (Xamarin)** mostrará os seguintes modelos para criar um aplicativo Xamarin.iOS:

![Selecionando o modelo para um aplicativo iOS](introduction-to-xamarin-ios-for-visual-studio-images/newproject-2.w157.png)

Arquivos Storyboard e .xib podem ser editados no Visual Studio usando o Designer do iOS. Para criar um Storyboard, escolha um dos modelos de Storyboard. Isso gerará um arquivo **Main.storyboard** no **Gerenciador de Soluções** conforme ilustra a captura de tela abaixo:

![O arquivo Main.storyboard no Gerenciador de Soluções](introduction-to-xamarin-ios-for-visual-studio-images/solution-explorer-new.w157.png)

Para começar a criar ou editar o Storyboard, clique duas vezes em `Main.storyboard` para abri-lo no Designer do iOS:

![](introduction-to-xamarin-ios-for-visual-studio-images/iosdesigner.png "O Main.storyboard no Designer de iOS")

Para adicionar objetos à exibição, use o painel **Caixa de Ferramentas** para arrastar e soltar itens na Superfície de Design. A Caixa de Ferramentas poderá ser adicionada selecionando **Exibir > Caixa de Ferramentas**, se ainda não tiver adicionada. As propriedades de objeto podem ser modificadas, seus layouts podem ser ajustados e eventos podem ser criados usando o painel **Propriedades**, conforme ilustrado abaixo:

![](introduction-to-xamarin-ios-for-visual-studio-images/properties.png "O painel Propriedades")

 Para saber mais sobre como usar o designer do iOS, consulte os guias do [Designer](~/ios/user-interface/designer/index.md).

## <a name="running--debugging-ios-applications"></a>Como executar e depurar aplicativos iOS

### <a name="device-logging"></a>Registro de dispositivo

Os blocos de log do Android e do iOS são unificados no Visual Studio 2017.

A nova janela de ferramentas de Log do Dispositivo para Visual Studio permite mostrar logs de dispositivos Android e iOS. Ela pode ser exibida executando qualquer um dos seguintes comandos:

- **Exibir -> Outras Janelas -> Log do Dispositivo**
- **Ferramentas > iOS > Log do Dispositivo**
- **Barra de ferramentas do iOS > Log do Dispositivo**

Quando a janela de ferramentas é exibida, o usuário pode selecionar o dispositivo físico na lista suspensa de dispositivos. Quando um dispositivo é selecionado, os logs serão automaticamente adicionados à tabela. Alternar entre dispositivos interromperá e iniciará o registro de dispositivo.

Para os dispositivos serem exibidos na caixa de combinação, é necessário carregar um projeto do iOS. Além disso, para iOS, o Visual Studio deve estar [conectado ao Servidor Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para descobrir os dispositivos iOS conectados ao Mac.

Essa janela de ferramentas fornece: uma tabela de entradas de log, uma lista suspensa para seleção de dispositivo, uma maneira de limpar entradas de log, uma caixa de pesquisa e botões reproduzir/parar/pausar.

### <a name="set-debugging-stops"></a>Definir paradas de depuração

Pontos de interrupção podem ser definidos a qualquer momento em seu aplicativo para sinalizar para o depurador interromper temporariamente a execução do programa. Para definir um ponto de interrupção no Visual Studio, clique na área de margem do editor ao lado do número de linha do código que você deseja interromper em:

![](introduction-to-xamarin-ios-for-visual-studio-images/image18.png "Configuração de um ponto de depuração")

Iniciar depuração e use o simulador ou dispositivo para navegar de seu aplicativo para um ponto de interrupção. Quando um ponto de interrupção for atingido, a linha será realçada e o comportamento de depuração normal do Visual Studio será habilitado: você pode intervir, encerrar ou sair do código, examinar variáveis locais ou usar a Janela Imediata.

Esta captura de tela mostra a execução do Simulador de iOS ao lado do Visual Studio usando o Parallels em OS X:

![](introduction-to-xamarin-ios-for-visual-studio-images/image19.png "Esta captura de tela mostra o Simulador de iOS em execução ao lado do Visual Studio usando o Parallels no OS X")

### <a name="examine-local-variables"></a>Examinar variáveis locais

![](introduction-to-xamarin-ios-for-visual-studio-images/image20.png "Examinando as variáveis locais com a depuração")

## <a name="summary"></a>Resumo

Este artigo descreveu como usar o Xamarin iOS para Visual Studio. Ele listou os diversos recursos disponíveis para criar, compilar e testar um aplicativo iOS de dentro do Visual Studio e percorreu a criação e a depuração de um aplicativo simples do iOS.

## <a name="related-links"></a>Links relacionados

- [Xamarin.iOS Installation](~/ios/get-started/installation/windows/index.md)
- [Provisionamento de Dispositivo](~/ios/get-started/installation/device-provisioning/index.md)
- [Criando a interface do usuário iOS no Código](~/ios/app-fundamentals/ios-code-only.md)
- [Conectar um Mac ao seu ambiente do Visual Studio com XMA (vídeo)](https://university.xamarin.com/lightninglectures/xamarin-mac-agent)
