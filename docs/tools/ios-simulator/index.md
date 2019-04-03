---
title: Simulador remoto de iOS para Windows
description: O simulador para Windows de iOS remoto permite que você teste seus aplicativos em um simulador de iOS exibido no Windows, junto com o Visual Studio de 2019.
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: lobrien
ms.author: laobri
ms.date: 04/02/2019
ms.openlocfilehash: b962390d5a5a365ada93d1778e3efb65839f41c5
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58854945"
---
# <a name="remoted-ios-simulator-for-windows"></a>Simulador remoto de iOS para Windows

O simulador para Windows de iOS remoto permite que você teste seus aplicativos em um simulador de iOS exibido no Windows, juntamente com 2019 do Visual Studio e Visual Studio 2017.

[![iSimulador de sistema operacional em execução no Windows](images/hero-sml.png "simulador de iOS em execução no Windows")](images/hero.png#lightbox)

## <a name="getting-started"></a>Introdução

O simulador de iOS remoto para Windows é instalado automaticamente como parte do Xamarin no Visual Studio de 2019 e Visual Studio 2017. Para usá-lo, siga estas etapas:

1. [Emparelhe o Visual de 2019 para um host de Build do Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. No Visual Studio, inicie a depuração de um projeto do iOS ou tvOS. O simulador de iOS remoto para Windows aparecerá em seu computador Windows.

Assista [este vídeo](deploy.md) para obter um guia passo a passo.

## <a name="simulator-window"></a>Janela do simulador

Barra de ferramentas na parte superior da janela do simulador contém um número de botões úteis:

- **Página inicial** – simula o botão página inicial em um dispositivo iOS.
- **Bloqueio** – bloqueia o simulador (passar o dedo para desbloquear).
- **Captura de tela** – salva uma captura de tela do simulador (armazenado no **Pictures\Xamarin\iOS Simulator\\**).
- [**As configurações** ](#settings) – exibe o teclado, local e outras configurações.
- [**Outras opções** ](#other-options) – traz várias opções de simulador, como rotação, shake gestos e ID de toque.

    [![iSimulador de sistema operacional mapeia exemplo](images/maps-app-sml.png "simulador de iOS mapeia de exemplo")](images/maps-app.png#lightbox)

## <a name="settings"></a>Configurações

Clicar no ícone da barra de ferramentas engrenagem abre o **configurações** janela:

[![iAs configurações do sistema operacional simulator](images/settings-sml.png "configurações do simulador de iOS")](images/settings.png#lightbox)

Essas configurações permitem que você habilite o teclado de hardware, escolha um local que o dispositivo deve relatório (estáticos e em movimento locais têm suporte), habilitar o Touch ID e redefinir o conteúdo e configurações para o simulador.

## <a name="other-options"></a>Outras opções

Botão de reticências da barra de ferramentas revela as outras opções, como rotação, shake gestos e reinicializar. Essas mesmas opções podem ser exibidas como uma lista clicando em qualquer lugar na janela do simulador:

[![iConfigurações adicionais do sistema operacional simulador](images/more-sml.png "configurações adicionais do simulador de iOS")](images/more.png#lightbox)

## <a name="touchscreen-support"></a>Suporte de tela sensível ao toque

Computadores mais modernos do Windows possuem telas sensíveis ao toque. Como o simulador para Windows de iOS remoto oferece suporte a interações de toque, você pode testar seu aplicativo com a mesma situação de emergência, passe o dedo e gestos de toque de vários dedos que você usa com dispositivos iOS físicos.

Da mesma forma, o simulador para Windows de iOS remoto trata entrada de caneta do Windows como entrada de lápis de Apple.

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>Desabilitando o simulador para Windows de iOS remoto

Para desabilitar o simulador para Windows de iOS remoto, navegue até **Ferramentas > Opções > Xamarin > configurações do iOS** e desmarque **Simulator remoto para Windows**.

[![checkbox para usar o simulador](images/options-sml.png "caixa de seleção para usar o simulador")](images/options.png#lightbox)

Com essa opção desabilitada, depuração abre o simulador de iOS no Mac conectado host de build.
