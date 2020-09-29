---
title: Como instalar o Xamarin.iOS no Windows
description: Este documento descreve como configurar um computador Windows, como configurar um host de build Mac e como emparelhar o Windows com o Mac para desenvolvimento do Xamarin.iOS.
zone_pivot_groups: platform-win
ms.prod: xamarin
ms.assetid: abf85d3e-a365-44a2-b1a4-6c572c7f76dd
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/16/2018
ms.openlocfilehash: 89c6125b4afacd23ddf2c2877c97dff34b5692e3
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436631"
---
# <a name="installing-xamarinios-on-windows"></a>Como instalar o Xamarin.iOS no Windows

_Este artigo descreve como configurar um computador Windows e um host de build do Mac para desenvolvimento do Xamarin.iOS._

::: zone pivot="windows"

## <a name="overview"></a>Visão geral

Para criar aplicativos do Xamarin.iOS com o Visual Studio 2019 no Windows, você precisará de:

- Um computador Windows com o Visual Studio 2019 instalado. Ele pode ser físico ou uma máquina virtual.

  - [Requisitos de sistema do Windows](~/cross-platform/get-started/requirements.md#windows-requirements)

- Uma rede acessível por Mac configurada com ferramentas de build da Apple e Xamarin.iOS. O Visual Studio 2019 acessa esse computador através de uma conexão de rede para usar as ferramentas de build da Apple, que são necessárias para compilar aplicativos do iOS nativos.

  - [Requisitos de sistema do Mac](~/cross-platform/get-started/requirements.md#macos-requirements)

  > [!TIP]
  > Você não tem acesso a um Mac?
  >
  > Se não tiver acesso a um Mac, você poderá usar [MacinCloud](https://www.macincloud.com/pages/visual-studio-mac.html) ou [MacStadium](https://www.macstadium.com/). Ambos os serviços fornecem o hardware do Mac baseado em nuvem que você pode usar para criar projetos do Xamarin.iOS.

## <a name="setup"></a>Instalação

Para obter a configuração para o desenvolvimento do Xamarin.iOS no Visual Studio 2019, siga estas etapas:

1. Configurar o Windows (instalar o Visual Studio 2019)

    O Xamarin.iOS funciona com o Visual Studio 2019 edições Community, Professional e Enterprise, em uma máquina virtual ou independente.

    - [Instale o Visual Studio 2019](~/get-started/installation/windows.md).

2. Configurar Mac (instalar Xcode e Visual Studio para Mac)

    Para criar, depurar e assinar aplicativos do iOS para distribuição, o Visual Studio 2017 deve ter acesso à rede para um host de build do Mac configurado com as ferramentas para desenvolvedores da Apple (Xcode) e Xamarin.iOS.

    - [Baixar e instalar o Xcode da Mac App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12).
    - [Instalar o Visual Studio para Mac](/visualstudio/mac/installation), que também instala o Xamarin.iOS.

    > [!NOTE]
    > Caso prefira não instalar o Visual Studio para Mac, o Visual Studio 2019 pode [configurar automaticamente](/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning) o host de build do Mac com o software necessário para criar aplicativos do Xamarin.iOS.
    > Para obter mais informações, veja [Provisionamento automático do Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning).

3. Emparelhar com Mac (conectar o Visual Studio 2019 ao Mac)

    Para o Visual Studio 2019 usar as ferramentas de build do iOS no Mac, os dois computadores devem se conectar em uma rede.

    - [Leia o guia Emparelhar com Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

::: zone-end
::: zone pivot="win-vs2017"

## <a name="overview"></a>Visão geral

Para criar aplicativos Xamarin.iOS com o Visual Studio 2017 no Windows, você precisará de:

- Um computador Windows com o Visual Studio 2017 instalado. Ele pode ser físico ou uma máquina virtual.
  - [Requisitos de sistema do Windows](~/cross-platform/get-started/requirements.md#windows-requirements)

- Uma rede acessível por Mac configurada com ferramentas de build da Apple e Xamarin.iOS. O Visual Studio 2017 acessa esse computador com uma conexão de rede para usar as ferramentas de build da Apple, que são necessárias para compilar aplicativos do iOS nativos.
  - [Requisitos de sistema do Mac](~/cross-platform/get-started/requirements.md#macos-requirements)

## <a name="setup"></a>Instalação

Para obter a configuração para o desenvolvimento do Xamarin.iOS no Visual Studio 2017, siga estas etapas:

1. Configurar o Windows (instalar o Visual Studio 2017)

    O Xamarin.iOS funciona com o Visual Studio 2017 edições Community, Professional e Enterprise, em uma máquina virtual ou independente.

    - [Instale o Visual Studio 2017](~/get-started/installation/windows.md).

2. Configurar Mac (instalar Xcode e Visual Studio para Mac)

    Para criar, depurar e assinar aplicativos do iOS para distribuição, o Visual Studio 2017 deve ter acesso à rede para um host de build do Mac configurado com as ferramentas para desenvolvedores da Apple (Xcode) e Xamarin.iOS.

    - [Baixar e instalar o Xcode da Mac App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12).
    - [Instalar o Visual Studio para Mac](/visualstudio/mac/installation), que também instala o Xamarin.iOS.

    > [!NOTE]
    > Caso prefira não instalar o Visual Studio para Mac, a partir do [Visual Studio 2017 versão 15.6](/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning), o Visual Studio 2017 pode configurar automaticamente o host de build do Mac com o software necessário para criar aplicativos Xamarin.iOS. Para obter mais informações, veja [Provisionamento automático do Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning).

3. Emparelhar com Mac (conectar o Visual Studio 2017 ao Mac)

    Para o Visual Studio 2017 usar as ferramentas de build do iOS no Mac, os dois computadores devem se conectar em uma rede.

    - [Leia o guia Emparelhar com Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

::: zone-end

## <a name="summary"></a>Resumo

Este artigo descreve como configurar um computador Windows e um host de build do Mac associado para desenvolvimento do Xamarin.iOS.

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Xamarin.iOS para Visual Studio](introduction-to-xamarin-ios-for-visual-studio.md)
- [Configurando o Visual Studio para desenvolvimento do iOS](config-options.md)
- [Provisionamento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md)