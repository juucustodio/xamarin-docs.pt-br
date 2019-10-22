---
title: Simulador remoto de iOS para Windows
description: O iOS Simulator remoto para Windows permite que você teste seus aplicativos em um simulador de iOS exibido no Windows juntamente com o Visual Studio 2019.
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: conceptdev
ms.author: crdun
ms.date: 04/26/2019
ms.openlocfilehash: 3067493315c62c46f44fb94aa61a919e1449080b
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70289703"
---
# <a name="remoted-ios-simulator-for-windows"></a>Simulador remoto de iOS para Windows

O iOS Simulator remoto para Windows permite que você teste seus aplicativos em um simulador de iOS exibido no Windows juntamente com o Visual Studio 2019 e o Visual Studio 2017.

[![simulador do iOS em execução no Windows](images/hero-sml.png "simulador do iOS em execução no Windows")](images/hero.png#lightbox)

## <a name="getting-started"></a>Introdução

O iOS Simulator remoto para Windows é instalado automaticamente como parte do Xamarin no Visual Studio 2019 e no Visual Studio 2017. Para usá-lo, siga estas etapas:

1. [Emparelhe o Visual 2019 em um host de Build do Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. No Visual Studio, inicie a depuração de um projeto iOS ou tvOS. O iOS Simulator remoto para Windows será exibido no computador Windows.

Assista [a este vídeo](deploy.md) para obter um guia passo a passo.

## <a name="simulator-window"></a>Janela do simulador

A barra de ferramentas na parte superior da janela do simulador contém vários botões úteis:

- **Página inicial** – simula o botão página inicial em um dispositivo IOS.
- **Bloquear** – bloqueia o simulador (passe o dedo para desbloquear).
- **Captura de tela** – salva uma captura de tela do simulador (armazenado no **Pictures\Xamarin\iOS Simulator \\** ).
- [**Configurações**](#settings) – exibe o teclado, o local e outras configurações.
- [**Outras opções**](#other-options) – exibe várias opções de simulador, como rotação, gestos de agitar e Touch ID.

    [![exemplo de mapas do simulador do iOS](images/maps-app-sml.png "exemplo de mapas do simulador do iOS")](images/maps-app.png#lightbox)

## <a name="settings"></a>Configurações

Clicar no ícone de engrenagem da barra de ferramentas abre a janela **configurações** :

[![configurações do simulador do iOS](images/settings-sml.png "configurações do simulador do iOS")](images/settings.png#lightbox)

Essas configurações permitem que você habilite o teclado de hardware, escolha um local que o dispositivo deve relatar (os locais estáticos e em movimento têm suporte), habilite a ID de toque e redefina o conteúdo e as configurações do simulador.

## <a name="other-options"></a>Outras opções

O botão de reticências da barra de ferramentas revela outras opções, como rotação, agitar gestos e reinicialização. Essas mesmas opções podem ser exibidas como uma lista clicando com o botão direito do mouse em qualquer lugar na janela do simulador:

[![configurações adicionais do simulador do iOS](images/more-sml.png "configurações adicionais do simulador do iOS")](images/more.png#lightbox)

## <a name="touchscreen-support"></a>Suporte à tela touch

A maioria dos computadores Windows modernos tem telas de toque. Como o iOS Simulator remoto para Windows dá suporte a interações de toque, você pode testar seu aplicativo com os mesmos gestos de toque, passar o dedo e vários dedos que você usa com dispositivos iOS físicos.

Da mesma forma, o iOS Simulator remoto para Windows trata a entrada da caneta do Windows como entrada do Apple lápis.

## <a name="sound-handling"></a>Manipulação de som

Os sons reproduzidos pelo simulador serão provenientes dos alto-falantes do Mac do host.
os sons do iOS não são ouvidos no computador com Windows.

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>Desabilitando o simulador do iOS remoto para Windows

Para desabilitar o simulador do iOS remoto para Windows, navegue até **ferramentas > opções > Xamarin > Ios configurações** e desmarque **simulador remoto para Windows**.

[![caixa de seleção para usar o simulador](images/options-sml.png "caixa de seleção para usar o simulador")](images/options.png#lightbox)

Com essa opção desabilitada, a depuração abre o simulador de iOS no host de Build do Mac conectado.
