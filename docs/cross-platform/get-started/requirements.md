---
title: Requisitos de sistema
description: Pré-requisitos para usar o Xamarin
ms.prod: xamarin
ms.assetid: dd344d57-18e2-42a5-8c15-3f5be4123c72
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 08/28/2017
ms.openlocfilehash: ed9992eb162b57cd9c0dd1bc9f4abda4235bac12
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2018
---
# <a name="system-requirements"></a>Requisitos de sistema

_Pré-requisitos para usar o Xamarin_

Os produtos Xamarin contam com os SDKs de plataforma da Apple e do Google para direcionar para iOS ou Android, portanto, os nossos requisitos de sistema correspondem aos requisitos deles. Esta página descreve a compatibilidade do sistema para a plataforma Xamarin, o ambiente de desenvolvimento recomendado e as versões do SDK.

- [Ambientes de Desenvolvimento](#devenv)
- [Requisitos do macOS](#mac)
- [Requisitos do Windows](#windows)

Visite as [instruções de instalação](#install) para mais informações sobre como obter o software e os SDKs necessários.

<a name="devenv" />

## <a name="development-environments"></a>Ambientes de desenvolvimento

Esta tabela mostra quais plataformas podem ser criadas com diferentes combinações de ferramentas de desenvolvimento e sistemas operacionais:

[!include[](~/cross-platform/includes/development-environment.md)]


> [!NOTE]
> Para desenvolver para iOS em computadores Windows, deve haver um [computador Mac acessível na rede](~/ios/get-started/installation/windows/connecting-to-mac/index.md), para compilação remota e depuração. Isso também funciona se o Visual Studio estiver em execução dentro de uma VM do Windows em um computador Mac.

<a name="mac" />

## <a name="macos-requirements"></a>Requisitos do macOS

Para usar um computador Mac para desenvolvimento do Xamarin são necessárias as seguintes versões de software/SDK. Verifique a versão do sistema operacional e siga as instruções do [Instalador do Xamarin](#install).

[!include[](~/cross-platform/includes/macos-requirements.md)]

> [!NOTE]
> O Xcode pode ser instalado (e atualizado) em [developer.apple.com](https://developer.apple.com/xcode/download/) ou por meio da Mac App Store.

### <a name="testing--debugging-on-macos"></a>Teste e depuração no macOS

Os aplicativos móveis do Xamarin podem ser implantados em dispositivos físicos através de USB para teste e depuração (os aplicativos Xamarin.Mac podem ser testados diretamente no computador de desenvolvimento. Os aplicativos Apple Watch são primeiro implantados no iPhone emparelhado).

[!include[](~/cross-platform/includes/macos-testing.md)]


<a name="windows" />

## <a name="windows-requirements"></a>Requisitos do Windows

Para usar um computador Windows para desenvolvimento do Xamarin são necessárias as seguintes versões de software/SDK.
Verifique a versão do sistema operacional (e confirme se você não está usando uma versão *Express* do Visual Studio. Caso esteja, considere atualizar para uma edição *Community*).
Os instaladores do Visual Studio 2015 e 2017 incluem uma opção para instalar o Xamarin automaticamente.

[!include[](~/cross-platform/includes/windows-requirements.md)]


> [!NOTE]
>
>* O Xamarin para Visual Studio dá suporte a qualquer versão do Visual Studio 2015 ou 2017 (Community, Professional e Enterprise).
>
>* Para desenvolver aplicativos do Xamarin.Forms para a UWP (Plataforma Universal do Windows), é necessário o Windows 10 com o Visual Studio 2015 ou 2017.


### <a name="testing--debugging-on-windows"></a>Teste e Depuração no Windows

Os aplicativos móveis do Xamarin podem ser implantados em dispositivos físicos através de USB para teste e depuração (os dispositivos iOS devem estar conectados ao computador Mac e não no computador que está executando o Visual Studio).

[!include[](~/cross-platform/includes/windows-testing.md)]


> [!NOTE]
>
>* [Download do emulador do Windows Phone 8.1](https://www.microsoft.com/download/details.aspx?id=43719).
>* O emulador do Windows Phone 10 está incluído com o SDK da UWP do Visual Studio 2015.

<a name="install" />

## <a name="installation-instructions"></a>Instruções de instalação

A versão mais recente do Xamarin para macOS pode ser baixada em [xamarin.com/download](http://xamarin.com/download). Para o Windows, siga as instruções de instalação do [Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio).

Uma lista completa das nossas versões atuais do produto está disponível na [página de versões atuais](http://developer.xamarin.com/releases/current/). Esta página também descreve as versões de produto individuais (e links para as notas de versão) dos nossos canais alfa e beta.

Instruções de [instalação](~/cross-platform/get-started/installation/index.md) específicas para cada plataforma estão disponíveis aqui:

- [Xamarin.iOS](~/ios/get-started/installation/index.md)
- [Xamarin.Android](~/android/get-started/installation/index.md)
- [Xamarin.Mac](~/mac/get-started/installation.md)

Também há informações adicionais sobre [Requisitos e plataformas com suporte no Xamarin.Forms](~/xamarin-forms/get-started/installation.md).


## <a name="related-links"></a>Links relacionados

- [Baixe o Xamarin](https://xamarin.com/download/)
- [Versões atuais](https://developer.xamarin.com/releases/current/)
