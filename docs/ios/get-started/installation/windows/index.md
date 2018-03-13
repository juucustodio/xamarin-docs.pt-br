---
title: Como instalar o Xamarin.iOS no Windows
description: "Este artigo mostra como configurar o Xamarin.iOS para Visual Studio. Aborda o processo de instalação para a extensão Xamarin para Visual Studio e discute a conexão ao SDK da Apple instalado no Mac."
ms.topic: article
ms.prod: xamarin
ms.assetid: abf85d3e-a365-44a2-b1a4-6c572c7f76dd
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/29/2017
ms.openlocfilehash: cfbe2df23317ee3ad11c9970ab892ddcc251b9d6
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="installing-xamarinios-on-windows"></a>Como instalar o Xamarin.iOS no Windows

_Este artigo mostra como configurar o Xamarin.iOS para Visual Studio. Ele aborda o processo de instalação para a extensão Xamarin para Visual Studio e discute a conexão ao SDK da Apple instalado no Mac._

## <a name="overview"></a>Visão geral

O Xamarin.iOS para Visual Studio permite escrever e testar aplicativos iOS em computadores Windows, com um Mac em rede fornecendo o serviço de build e implantação.

Desenvolver para iOS no Visual Studio possibilita:

- Criar soluções de plataforma cruzada para aplicativos iOS, Android e Windows.
- Usar ferramentas do Visual Studio (como *Resharper* e *Team Foundation Server*) para todos os projetos de plataforma cruzada, incluindo código-fonte do iOS.
- Trabalhar com um IDE familiar, aproveitando as vantagens das associações do Xamarin.iOS de todas as APIs da Apple

O Xamarin.iOS para Visual Studio dá suporte a configurações em que o Visual Studio é executado dentro de uma máquina virtual do Windows em um Mac (usando Parallels ou VMWare) ou quando está em um computador separado visível na mesma rede que um Mac. Independentemente de qual configuração funciona melhor para você, o Visual Studio fará a conexão ao Mac com segurança e de modo imediato usando SSH.

Este artigo aborda as etapas para instalar e configurar as ferramentas Xamarin.iOS nos computadores Windows e Mac, bem como as etapas para se conectar ao host do Mac para que os desenvolvedores possam compilar, depurar e implantar aplicativos Xamarin.iOS usando o Visual Studio.

O diagrama a seguir mostra uma visão geral simples do fluxo de trabalho de desenvolvimento do Xamarin.iOS:

[![O fluxo de trabalho de desenvolvimento do Xamarin.iOS](images/xma2.png)](images/xma2.png#lightbox)

> [!IMPORTANT]
>  Na verdade, o Visual Studio inicia um processo separado do MSBuild para compilar os projetos. Esse processo cria uma nova conexão com o Mac, significando que, na verdade, há duas conexões SSH do Windows com o Mac no build do Visual Studio. Compilar usando a [linha de comando](~/ios/get-started/installation/windows/connecting-to-mac/index.md) apenas cria o único processo MSBuild. Para simplificar esse diagrama, todas as conexões são simplesmente representadas por uma seta.

## <a name="requirements"></a>Requisitos

O Xamarin.iOS para Visual Studio realiza um feito incrível: permite aos desenvolvedores criar, compilar e depurar aplicativos iOS em um computador com Windows usando o IDE do Visual Studio. Ele não pode fazer isso sozinho – aplicativos iOS não podem ser criados sem o compilador da Apple e não podem ser implantados sem os certificados e as ferramentas de assinatura de código da Apple. Isso significa que um Xamarin.iOS para a instalação do Visual Studio requer uma conexão a um computador Mac OS X em rede para realizar essas tarefas. Uma vez configuradas, as ferramentas da Xamarin tornarão o processo mais direto possível.


<a name="system-requirements"/>

### <a name="system-requirements"></a>Requisitos de sistema

Os requisitos do sistema são:

### <a name="windows"></a>Windows

1. Windows 7 ou superior.

2. Visual Studio 2015 Professional ou superior

    a. Se você tiver uma licença empresarial, precisará instalar o Visual Studio Enterprise.

3. Xamarin para Visual Studio.

As ferramentas do Xamarin não podem ser usadas com as edições Express do Visual Studio devido à falta de suporte para plug-in. Há suporte para o Xamarin no Visual Studio Community.

### <a name="mac"></a>Mac

1. Um Mac executando macOS Sierra (10.12) ou superior (embora a versão estável mais recente seja recomendada).

2. SDK do iOS do Xamarin. Isso é instalado ao baixar o Visual Studio para Mac

3. IDE Xcode da Apple e SDK do iOS (A versão estável mais recente da Mac App Store seja recomendada).

**O computador Windows deve ser capaz de acessar o Mac por meio da rede.**

### <a name="apple-developer-account"></a>Conta de desenvolvedor da Apple

Para implantar aplicativos em um dispositivo ou enviá-los à App Store, uma conta de Desenvolvedor da Apple é necessária. Os certificados de desenvolvedor e os perfis de provisionamento relevantes devem ser criados e instalados no Mac em rede para que o Xamarin.iOS para Visual Studio possa funcionar. Consulte o artigo [Provisionamento de dispositivo](~/ios/get-started/installation/device-provisioning/index.md) para conhecer as etapas para obter um certificado de desenvolvimento e provisionar um dispositivo.

## <a name="features"></a>Recursos 

O Xamarin.iOS para Visual Studio permite criar, editar, compilar e implantar projetos do Xamarin.iOS do Windows. Isso inclui os seguintes recursos:

- Criar novos projetos do iOS.

- Editar projetos do iOS e soluções de plataforma cruzada que também incluam projetos Xamarin.Android e UWP.

- Compilar projetos do iOS e soluções de plataforma cruzada que também incluam projetos Xamarin.Android e UWP.

- Storyboard e .xib dão suporte ao uso do Designer do iOS.

- Implantar e depurar aplicativos iOS, em que o aplicativo em si é executado em um simulador ou em um dispositivo conectado ao Mac.

- Simulador de iOS no Windows – para saber mais sobre como usar o simulador de iOS no Windows, consulte [este](~/tools/ios-simulator.md) guia.

<a name="configuring" />

## <a name="configuring-your-mac"></a>Configurando o Mac

<a name="installation"/>

### <a name="installation"></a>Instalação

Para instalar as ferramentas do Xamarin.iOS no host do mac, você deve [instalar o Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/installation).

Depois que o software estiver instalado, siga as etapas nas próximas seções para configurar o Xamarin.iOS no macOS para permitir que Xamarin para Visual Studio conectar-se a ele.

> [!IMPORTANT]
>  O computador Windows deve estar usando a mesma versão do Xamarin.iOS que o Mac ao qual está conectado. Para garantir que isso seja verdadeiro:
>
> - **Visual Studio 2015 e anterior**: verifique se você está no mesmo [canal de atualizações](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/) que o Visual Studio para Mac.
>
> - **Visual Studio 2017, Versão de Lançamento**: verifique se você está no canal **Estável** do Visual Studio para Mac.
>
> - **Visual Studio 2017, Versão de Visualização**: verifique se você está no canal **Alfa** do Visual Studio para Mac.

<a name="configuration" />


### <a name="configuration"></a>Configuração

Para acessar a comunicação entre a extensão Xamarin para Visual Studio e seu Mac, você precisará permitir **Logon Remoto** no Mac. Siga as etapas abaixo para configurar isso:

1. Abra *Destaque* (**Cmd-Espaço**), pesquise **Logon Remoto** e, em seguida, selecione o resultado **Compartilhamento**. Isso abrirá as **Preferências do Sistema** no painel **Compartilhamento**.

![Pesquisa do Spotlight para acesso remoto](images/spotlight.png)

2. Marque a opção **Logon Remoto** na lista **Serviço** à esquerda para permitir que o Xamarin para Visual Studio se conecte ao Mac.

![Marque a opção Acesso Remoto na Lista de serviços](images/sharing.png)

3. Verifique se o **Logon Remoto** está definido para permitir o acesso de **Todos os usuários** ou se o nome de usuário ou grupo do Mac está incluído na lista de usuários permitidos à direita.

O Mac agora deverá ser detectável pelo Visual Studio se estiver na mesma rede.

> [!NOTE]
> Se você tiver o firewall do macOS definido para bloquear aplicativos assinados por padrão, poderá ser preciso permitir ao `mono-sgen` receber conexões de entrada. Uma caixa de diálogo de alerta será exibida para avisá-lo, se for o caso.

<a name="developersetup"/>

### <a name="ios-developer-setup"></a>Configuração do desenvolvedor de iOS

Para o desenvolvimento do iOS, é importante que o computador Mac esteja configurado com as identidades de assinatura relevantes. Isso permite que você assine seus aplicativos corretamente para que eles possam ser distribuídos por meio da App Store ou Ad Hoc. Siga o link abaixo para instruções sobre como configurar um Mac para desenvolvimento do iOS com o Xamarin:

- [Provisionamento de Dispositivo](~/ios/get-started/installation/device-provisioning/index.md?ide=vs)

Com o Mac configurado, é hora de configurar seu computador Windows.

<a name="windowsinstallation"/>

## <a name="windows-installation"></a>Instalação do Windows

O Xamarin pode ser instalado como parte da instalação do Visual Studio 2017 ou 2015. Para instalar as Ferramentas do Visual Studio para Xamarin, consulte o guia de [Instalação do Windows](~/cross-platform/get-started/installation/windows.md).

## <a name="installation-complete"></a>Instalação concluída

Depois da conclusão do processo de instalação, ainda há mais algumas etapas necessárias para fazer tudo funcionar:

- [Conectar o Visual Studio ao Mac](#connectingtomac) – o Visual Studio deve ser conectado ao host de build do Mac antes de poder compilar projetos do Xamarin.iOS.
- [Configurar a Barra de Ferramentas do Visual Studio](#toolbar) – isso permitirá que você acesse facilmente os recursos do Xamarin.iOS no Visual Studio.

<a name="connectingtomac" /> 

### <a name="connecting-to-the-mac"></a>Conectando-se ao Mac

Uma conexão é feita do Xamarin.iOS ao Visual Studio para seu host de build Mac por meio de uma conexão SSH entre computadores. Para saber mais sobre a conexão, consulte o guia [Conexão ao Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

Para conectar seu Mac, siga as etapas abaixo:

- Navegue até **Ferramentas > Opções** e, em **Xamarin**, selecione **Configurações do iOS**:

  [![A tela Configurações do iOS](images/image2.png)](images/image2.png#lightbox)

- Desde que o Mac tenha sido [configurado](#configuration) corretamente para permitir **Logon Remoto**, você deverá poder selecionar seu Mac na lista:

  [![A caixa de diálogo Host remoto](images/xma3.png)](images/xma3.png#lightbox)

- Isso solicitará as credenciais administrativas do host do Mac:

  [![A caixa de diálogo de logon](images/xma4.png)](images/xma4.png#lightbox)

- Quando você tiver se conectado, exibirá o ícone “Conexão Bem-Sucedida” ao lado do nome do computador:

  [![A caixa de diálogo Host remoto que exibe o ícone Conexão bem-sucedida ao lado do nome do computador](images/image6.png)](images/image6.png#lightbox)

Você será reconectado sempre que iniciar o Visual Studio.

<a name="toolbar" />

## <a name="visual-studio-toolbar-configuration"></a>Configuração de barra de ferramentas do Visual Studio

Quando um projeto do iOS for aberto, a barra de ferramentas do iOS estará visível por padrão e não precisará ser configurada.

As etapas a seguir podem ser usadas se a barra de ferramentas do iOS não for exibida.

Para configurar a barra de ferramentas, primeiro abra o menu **Exibir > Barras de Ferramentas** e verifique se a entrada **iOS** está selecionada. Escolha o item de menu conforme mostrado nesta captura de tela; ele deve estar marcado para indicar que a barra de ferramentas está visível:

[![Escolha Barra de ferramentas > iOS](images/image31.png)](images/image31.png#lightbox)

### <a name="visual-studio-2015"></a>Visual Studio 2015

Nas versões anteriores ao Visual Studio 2017, o botão **Plataformas da Solução** pode precisar ser incluído na barra de ferramentas Padrão. Isso permite que um dispositivo iOS ou simulador do iOS seja selecionado durante a depuração. Siga as instruções abaixo para configurar isso

Clique no botão de menu no lado direito da barra de ferramentas Padrão:

- Escolha **Adicionar ou Remover Botões**
- Selecione **Plataformas de solução**

[![Selecione Plataforma de solução](images/image35.png)](images/image35.png#lightbox)

As barras de ferramentas **Padrão** e **iOS** agora devem se parecer com esta captura de tela:

[![Agora, as barras de ferramentas Padrão e iOS devem ter a aparência desta captura de tela](images/image36.png)](images/image36.png#lightbox)

Uma vez concluída a configuração da barra de ferramentas, você está pronto para começar a usar o Xamarin iOS para Visual Studio.

## <a name="summary"></a>Resumo

Este artigo apresentou um guia passo a passo para instalar, configurar e usar o Xamarin iOS para Visual Studio.

Ele abordou a instalação e a configuração dos pré-requisitos de ferramentas no Windows e no Mac OS X.


## <a name="related-links"></a>Links relacionados

- [Instalação](~/cross-platform/get-started/installation/windows.md)
- [Provisionamento de Dispositivo](~/ios/get-started/installation/device-provisioning/index.md)
- [Introdução ao Xamarin.iOS para Visual Studio](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)
- [Conectar um Mac ao seu ambiente do Visual Studio com XMA (vídeo)](https://university.xamarin.com/lightninglectures/xamarin-mac-agent)
