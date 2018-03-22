---
title: Remota simulador de iOS (para Windows)
description: Testar e depurar aplicativos iOS inteiramente dentro do Visual Studio no Windows
ms.topic: article
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 04/07/2017
ms.openlocfilehash: 6d1401728c1063ce09c5848865e4c9b3fe7687d7
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2018
---
# <a name="remoted-ios-simulator-for-windows"></a>Remota simulador de iOS (para Windows)

_Testar e depurar aplicativos iOS inteiramente dentro do Visual Studio no Windows_

[![](ios-simulator-images/hero-sml.png "Simulador de iOS em execução no Windows")](ios-simulator-images/hero.png#lightbox)

## <a name="download-and-install"></a>Baixar e instalar

Baixe o [installer](https://dl.xamarin.com/xamarin-simulator/Xamarin.Simulator.Installer.msi) e instalar no computador com Windows. Ferramentas do Visual Studio para Xamarin já deve estar instaladas.

> [!NOTE]
> Usando um simulador de iOS remoto no Visual Studio requer um Mac em rede com o Xamarin instalado.

## <a name="getting-started"></a>Guia de Introdução

Para usar o simulador de iOS remoto:

1. Verifique se o que Visual Studio conectou seu Mac pelo menos uma vez antes de iniciar o simulador de iOS remoto.
2. Certifique-se de que é um aplicativo iOS ou tvOS o **projeto de inicialização** e iniciar a depuração.

Você pode desabilitar o simulador de iOS remoto de **Ferramentas > Opções > Xamarin > configurações do iOS** desmarcando a caixa para **simulador remoto para Windows** mostrado aqui:

[![](ios-simulator-images/options-sml.png "caixa de seleção para usar o simulador")](ios-simulator-images/options.png#lightbox)

O simulador de iOS, em seguida, será aberta no computador Mac conectado. Marque esta opção para ativar o simulador de iOS remoto.

## <a name="features"></a>Recursos

O simulador de iOS remoto fornece uma maneira de testar e depurar aplicativos do iOS no simulador completamente do Visual Studio no Windows.

### <a name="simulator-window"></a>Janela do simulador

Barra de ferramentas inclui vários botões para interagir com o simulador:

- **Home** – simula o botão página inicial do dispositivo.
- **Bloqueio** – bloqueia o simulador (você pode passar para desbloquear).
- **Captura de tela de** – salva uma captura de tela do simulador para o disco.
- [**Configurações de** ](#settings) – configurar o teclado e o local.
- Outros [ **opções** ](#options) – uma variedade de opções do simulator estão disponível como girar, agitação ou chamar outros estados no simulador. Algumas opções são ocultadas, eles podem ser acessados pelo ícone do botão de reticências que aparece na barra de ferramentas ou clicando na janela.

    [![](ios-simulator-images/maps-app-sml.png "exemplo de mapas do simulador de iOS")](ios-simulator-images/maps-app.png#lightbox)


### <a name="settings"></a>Configurações

O ícone de "engrenagem" abre o **configurações** janela:

[![](ios-simulator-images/settings-sml.png "configurações do simulador de iOS")](ios-simulator-images/settings.png#lightbox)

Isso permite que você habilitar o teclado de hardware no simulador e escolha o local é relatado para o dispositivo (incluindo um local estático, ou outras opções de local de movimentação).



### <a name="other-options"></a>Outras opções

Clique em qualquer lugar na janela do simulador para exibir todas as opções disponíveis no simulador, como rotação, disparar um gesto de agitação e reinicializar o simulador:

[![](ios-simulator-images/more-sml.png "configurações adicionais do simulador de iOS")](ios-simulator-images/more.png#lightbox)

### <a name="touchscreen-support"></a>Suporte a tela sensível ao toque

Computadores mais modernos do Windows têm telas sensíveis ao toque, e o simulador de iOS remoto permite que você tocar a janela do simulador para testar as interações do usuário em seu aplicativo iOS.

Isso inclui esmagamento, passar o dedo e gestos de toque de dedo vários - coisas que anteriormente podem ser facilmente testadas somente em dispositivos físicos.

Suporte de caneta no Windows também é convertido para entrada de Apple Lápis no simulador.

<!--
<a name="knownissues" />

# Known Issues

 - Apple Watch devices may show in the Visual Studio device list, but are not yet supported.
 - Launching in **Release** mode may also start Apple’s simulator on the networked Mac.
 - Closing the remote iOS Simulator on Windows will not immediately stop debugging in Visual Studio. Stop debugging manually from the menu or the red button.
 - Opening too many different simulators simultaneously will produce unexpected results.
 - Exception of type `Foundation.NSErrorException` may be thrown while launching Simulators. Workaround is to kill csproxy (server process) on the Mac host and re-deploy to the simulator.
 - Performance may be slower when using Xcode 8
-->
