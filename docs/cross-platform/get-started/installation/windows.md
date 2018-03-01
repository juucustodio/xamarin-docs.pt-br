---
title: Instalando o Xamarin no Visual Studio no Windows
ms.topic: article
ms.prod: xamarin
ms.assetid: E20D4463-368E-4B60-A059-F50DB8C5552D
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 09/29/2017
ms.openlocfilehash: b68e03251b83192bdc5836af6ea54446ddaad24a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="installing-xamarin-in-visual-studio-on-windows"></a>Instalando o Xamarin no Visual Studio no Windows

Como o Xamarin agora está incluído em todas as edições do Visual Studio sem custos adicionais e sem a necessidade de uma licença separada, você pode usar o Instalador do Visual Studio para baixar e instalar ferramentas do Xamarin.


-   [Requisitos](#requirements)
-   [Instalação](#installation)
-   [Adicionando o Xamarin ao Visual Studio 2017](#vs2017)
-   [Adicionando o Xamarin ao Visual Studio 2015](#vs2015)
-   [Verificando a instalação](#verifying)
-   [Próximas Etapas](#nextsteps)


<a name="requirements" />

# <a name="requirements"></a>Requisitos

Os itens a seguir são necessários para a instalação das Ferramentas do Visual Studio para Xamarin:

1. Windows 7 ou superior.

2. Visual Studio 2015 ou 2017 (Community, Professional ou Enterprise).

3. Xamarin para Visual Studio.

Observe que o Xamarin não pode ser usado com as edições Express do Visual Studio devido à falta de suporte para plug-in.

Para obter mais informações sobre os pré-requisitos de instalação e uso do Xamarin, consulte [Requisitos do Sistema](~/cross-platform/get-started/requirements.md).


<a name="installation" />

# <a name="installation"></a>Instalação

O Xamarin pode ser instalado como parte de uma nova instalação do Visual Studio.
Para fazer isso, siga estas etapas:

1. Baixe o Visual Studio Community, o Visual Studio Professional ou o Visual Studio Enterprise na página do [Visual Studio](https://www.visualstudio.com/vs/) (os links para download são fornecidos na parte inferior).

2. Clique duas vezes no pacote baixado para iniciar a instalação.

3. Selecione a carga de trabalho **Desenvolvimento móvel com .NET** na tela de instalação: 

    [![Seleção de Desenvolvimento móvel com .NET na tela Cargas de trabalho](windows-images/01-mobile-dev-workload-sml.png)](windows-images/01-mobile-dev-workload.png)

4. Enquanto **Desenvolvimento móvel com .NET** estiver selecionado, veja o painel **Resumo** à direita. Nele, é possível anular a seleção das opções de desenvolvimento móvel que você não deseja instalar. Por padrão, todas as opções exibidas na captura de tela a seguir são instaladas (**Xamarin Workbooks**, **Xamarin Profiler**, **Xamarin Remoted Simulator**, **NDK do Android**, **SDK do Android**, **Java SE Development Kit**, **Emulador do Android do Google**, **Suporte para F#** e **Intel HAXM**):

    ![Painel Resumo com a lista de opções do Xamarin que podem ser instaladas](windows-images/02-summary.png)

5. Quando estiver pronto para começar a instalação do Visual Studio, clique no botão **Instalar** no canto inferior direito:

    ![Local do botão de instalação](windows-images/03-click-install.png)

   Dependendo da edição do Visual Studio que você estiver instalando, o processo de instalação pode levar muito tempo para ser concluído. Você pode usar as barras de progresso para monitorar a instalação:

    ![Captura de tela de exemplo das barras de progresso durante a instalação](windows-images/04-progress-bars.png)

6. Quando a instalação do Visual Studio for concluída, clique no botão **Iniciar** para iniciá-lo:

    ![Local do botão Iniciar](windows-images/05-launch.png)


<a name="vs2017" />

## <a name="adding-xamarin-to-visual-studio-2017"></a>Adicionando o Xamarin ao Visual Studio 2017

Se o Visual Studio 2017 já está instalado, é possível adicionar o Xamarin executando novamente o instalador do Visual Studio para modificar as cargas de trabalho (consulte [Modificar o Visual Studio](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) para obter detalhes). Em seguida, siga as etapas listadas acima para instalar o Xamarin.

Para obter mais informações sobre como baixar e instalar o Visual Studio 2017, consulte [Instalar o Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio).


<a name="vs2015" />

## <a name="adding-xamarin-to-visual-studio-2015"></a>Adicionando o Xamarin ao Visual Studio 2015

Para adicionar o Xamarin.Android a uma instalação existente do Visual Studio 2015, siga estas etapas:

1. Clique com o botão direito do mouse no botão **Iniciar** do Windows e selecione **Programas e Recursos**.

2. Clique com o botão direito do mouse em **Microsoft Visual Studio** e clique em **Alterar**.

3. Quando a caixa de diálogo do Instalador do Visual Studio aparecer, clique no botão **Modificar**.

4. Na guia **Recursos**, role para baixo até **Desenvolvimento de Celular de Plataforma Cruzada**. Clique na caixa de seleção ao lado de **C#/.NET (Xamarin)**:

    ![Adicionando o C#/.NET Xamarin ao Visual Studio 2015](windows-images/06-add-xamarin.png)

5. Clique no botão **ATUALIZAR** para adicionar o Xamarin ao Visual Studio.


<a name="verifying" />

## <a name="verifying-installation"></a>Verificando a instalação

No Visual Studio 2017, verifique se o Xamarin está instalado clicando no menu **Ajuda**. Se o Xamarin estiver instalado, você verá o item de menu **Xamarin**, conforme mostrado nesta captura de tela:

![Item de menu Xamarin exibido no menu Ajuda](windows-images/12-xamarin-menu-item.png)

Se estiver usando versões anteriores do Visual Studio, você poderá clicar em **Ajuda > Sobre o Microsoft Visual Studio** e percorrer a lista de produtos instalados para ver se o Xamarin está instalado:

![Tela de produtos instalados do Visual Studio](windows-images/13-xamarin-is-installed.png)

Para saber mais sobre como localizar informações de versão, consulte [Where can I find my version information and logs?](~/cross-platform/troubleshooting/questions/version-logs.md) (Onde posso encontrar minhas informações de versão e logs?)

<a name="nextsteps" />

# <a name="next-steps"></a>Próximas etapas

A instalação das Ferramentas do Visual Studio para Xamarin permite que você comece a escrever código para seus aplicativos, mas é preciso uma configuração adicional para criar e implantar os aplicativos no simulador, no emulador e no dispositivo. Acesse os guias a seguir para concluir a instalação e iniciar a criação de aplicativos multiplataforma.

## <a name="ios"></a>iOS

Para obter informações mais detalhadas, consulte o guia [Como instalar o Xamarin.iOS no Windows](~/ios/get-started/installation/windows/index.md). 

1. [Instalar as ferramentas do Xamarin.iOS no Mac](~/ios/get-started/installation/windows/index.md#installation)
2. [Configurando o Mac](~/ios/get-started/installation/windows/index.md#configuration)
3. [Configuração do desenvolvedor iOS](~/ios/get-started/installation/windows/index.md#developersetup) (para executar o aplicativo no dispositivo).
4. [Conectando o Visual Studio ao host de build do Mac](~/ios/get-started/installation/windows/index.md#connectingtomac)
5. [Simulador de iOS remoto](~/tools/ios-simulator.md)
6. [Introdução ao Xamarin.iOS para Visual Studio](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)

## <a name="android"></a>Android

Para obter informações mais detalhadas, consulte o guia [Instalando o Xamarin.Android no Windows](~/android/get-started/installation/windows.md).

1. [Configuração do Xamarin.Android](~/android/get-started/installation/windows.md#configuration)
2. [Usando o Gerenciador de SDK do Xamarin Android](~/android/get-started/installation/android-sdk.md?ide=vs)
3. [Emulador do SDK do Android](~/android/get-started/installation/android-emulator/index.md)
4. [Configurar o dispositivo para desenvolvimento](~/android/get-started/installation/set-up-device-for-development.md)
