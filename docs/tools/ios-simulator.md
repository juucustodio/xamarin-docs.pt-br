---
title: Simulador remoto de iOS para Windows
description: O simulador do Windows do iOS remoto permite que você teste seus aplicativos em um simulador de iOS exibido nas janelas junto com o Visual Studio de 2017.
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: topgenorth
ms.author: toopge
ms.date: 05/11/2018
ms.openlocfilehash: b07cc24e63f4aa3ce4451e3bdb5819f1df1058c6
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
---
# <a name="remoted-ios-simulator-for-windows"></a>Simulador remoto de iOS para Windows

O simulador do Windows do iOS remoto permite que você teste seus aplicativos em um simulador de iOS exibido nas janelas junto com o Visual Studio de 2017.

[![](ios-simulator-images/hero-sml.png "Simulador de iOS em execução no Windows")](ios-simulator-images/hero.png#lightbox)

## <a name="getting-started"></a>Introdução

O simulador de iOS remoto para Windows é instalado automaticamente como parte do Xamarin no Visual Studio de 2017. Para usá-lo, siga estas etapas:

1. [Emparelhe 2017 Visual para um host de compilação de Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. No Visual Studio de 2017, inicie a depuração de um projeto do iOS ou tvOS. O simulador de iOS remoto para Windows aparecerá no seu computador Windows.

## <a name="simulator-window"></a>Janela do simulador

Barra de ferramentas na parte superior da janela do simulador contém vários botões úteis:

- **Home** – simula o botão página inicial em um dispositivo iOS
- **Bloqueio** – bloqueia o simulador (passe o dedo para desbloquear)
- **Captura de tela de** – salva uma captura de tela do simulador
- [**Configurações de** ](#settings) – exibe o teclado, local e outras configurações
- [**Outras opções** ](#other-options) – traz várias opções de simulador, como gestos de rotação e agitação

    [![](ios-simulator-images/maps-app-sml.png "exemplo de mapas do simulador de iOS")](ios-simulator-images/maps-app.png#lightbox)

## <a name="settings"></a>Configurações

Clicar no ícone de engrenagem da barra de ferramentas abrirá o **configurações** janela:

[![](ios-simulator-images/settings-sml.png "configurações do simulador de iOS")](ios-simulator-images/settings.png#lightbox)

Estas configurações permitem que você habilite o teclado de hardware, selecione um local que o dispositivo deve relatório (locais estáticos e em movimento suporte), habilitar Touch ID e redefinir o conteúdo e configurações do simulador.

## <a name="other-options"></a>Outras opções

Botão de reticências da barra de ferramentas revela outras opções, como rotação, gestos de agitação e a reinicialização. Essas mesmas opções podem ser exibidas como uma lista clicando em qualquer lugar na janela do simulador:

[![](ios-simulator-images/more-sml.png "configurações adicionais do simulador de iOS")](ios-simulator-images/more.png#lightbox)

## <a name="touchscreen-support"></a>Suporte a tela sensível ao toque

Computadores mais modernos do Windows têm telas sensíveis ao toque. Como o simulador do Windows do iOS remoto oferece suporte a interações de toque, você pode testar seu aplicativo com o mesmo aperto, passe o dedo e gestos de toque de dedo vários que você usa com dispositivos iOS físicos.

Da mesma forma, o simulador do Windows do iOS remoto trata entrada de caneta do Windows como entrada de lápis da Apple.

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>Desabilitando o iOS remoto simulador para Windows

Para desabilitar o simulador do Windows do iOS remoto, navegue até **Ferramentas > Opções > Xamarin > configurações do iOS** e desmarque **simulador remoto para Windows**.

[![](ios-simulator-images/options-sml.png "caixa de seleção para usar o simulador")](ios-simulator-images/options.png#lightbox)

Com essa opção desabilitada, depuração abre o simulador no Mac conectado do iOS criar host.
