---
title: Simulador remoto de iOS para Windows
description: O Simulador iOS remoto para Windows permite testar seus aplicativos em um simulador iOS exibido no Windows ao lado do Visual Studio 2019.
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: davidortinau
ms.author: daortin
ms.date: 04/26/2019
ms.openlocfilehash: d5898f9c6ee30eb1f12bf6480b93a609e762e6ea
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "75886587"
---
# <a name="remoted-ios-simulator-for-windows"></a>Simulador remoto de iOS para Windows

O Simulador iOS remoto para Windows permite testar seus aplicativos em um simulador iOS exibido no Windows ao lado do Visual Studio 2019 e do Visual Studio 2017.

[![simulador iOS em execução no Windows](images/hero-sml.png "simulador iOS em execução no Windows")](images/hero.png#lightbox)

## <a name="getting-started"></a>Introdução

O Simulador IOS Remoto para Windows é instalado automaticamente como parte do Xamarin no Visual Studio 2019 e visual studio 2017. Para usá-lo, siga estas etapas:

1. [Par Visual 2019 para um host Mac Build](~/ios/get-started/installation/windows/connecting-to-mac/index.md).
2. No Visual Studio, comece a depurar um projeto iOS ou tvOS. O simulador iOS remoto para Windows aparecerá na sua máquina Windows.

Assista [a este vídeo](deploy.md) para obter um guia passo-a-passo.

## <a name="simulator-window"></a>Janela do simulador

A barra de ferramentas na parte superior da janela do simulador contém uma série de botões úteis:

- **Home** – Simula o botão home em um dispositivo iOS.
- **Bloqueio** – Bloqueia o simulador (deslize para desbloquear).
- **Captura de tela** – Salva uma captura de tela do simulador (armazenado em **\\Pictures\Xamarin\iOS Simulator**).
- [**Configurações**](#settings) – Exibe teclado, localização e outras configurações.
- [**Outras opções**](#other-options) – Traz várias opções de simulador, como rotação, gestos de shake e Touch ID.

    [![Exemplo de mapas de simulador do iOS](images/maps-app-sml.png "Exemplo de mapas de simulador do iOS")](images/maps-app.png#lightbox)

## <a name="settings"></a>Configurações

Clicar no ícone de engrenagem da barra de ferramentas abre a janela **Configurações:**

[![configurações do simulador do iOS](images/settings-sml.png "configurações do simulador do iOS")](images/settings.png#lightbox)

Essas configurações permitem ativar o teclado de hardware, escolher um local que o dispositivo deve relatar (locais estáticos e móveis são suportados), ativar o Touch ID e redefinir o conteúdo e as configurações do simulador.

## <a name="other-options"></a>Outras opções

O botão de elipse da barra de ferramentas revela outras opções, como rotação, gestos de agitação e reinicialização. Essas mesmas opções podem ser vistas como uma lista clicando com o botão direito do mouse em qualquer lugar na janela do simulador:

[![configurações adicionais do simulador do iOS](images/more-sml.png "configurações adicionais do simulador do iOS")](images/more.png#lightbox)

## <a name="touchscreen-support"></a>Suporte a tela sensível ao toque

A maioria dos computadores Windows modernos têm telas sensíveis ao toque. Como o Simulador iOS remoto para Windows suporta interações de toque, você pode testar seu aplicativo com os mesmos gestos de pinça, deslize e toque de vários dedos que você usa com dispositivos físicos iOS.

Da mesma forma, o Simulador iOS remoto para Windows trata a entrada do Windows Stylus como entrada Apple Pencil.

## <a name="sound-handling"></a>Manuseio de som

Os sons tocados pelo simulador virão dos alto-falantes do Host Mac.
Os sons do iOS não são ouvidos no computador Windows.

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>Desabilitando o simulador iOS remoto para Windows

Para desativar o simulador iOS remoto para Windows, navegue até **ferramentas > opções > Xamarin > configurações do iOS** e desmarque **o simulador remoto para windows**.

[![caixa de seleção para usar simulador](images/options-sml.png "caixa de seleção para usar simulador")](images/options.png#lightbox)

Com esta opção desativada, a depuração abre o simulador do iOS no host de compilação Mac conectado.

## <a name="troubleshooting"></a>Solução de problemas

Se você tiver problemas com o Simulador iOS remoto, poderá visualizar os logs nesses locais:

- **Mac** -`~/Library/Logs/Xamarin/Simulator.Server`
- **Janelas** –`%LOCALAPPDATA%\Xamarin\Logs\Xamarin.Simulator`

Se você [relatar um problema no Visual Studio,](https://docs.microsoft.com/visualstudio/ide/how-to-report-a-problem-with-visual-studio)anexar esses logs pode ser útil (há opções para manter uploads privados).
