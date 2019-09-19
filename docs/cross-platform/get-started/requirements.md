---
title: Requisitos de sistema
description: Este documento lista os requisitos de sistema para compilar aplicativos com o Xamarin em computadores Mac e Windows. Também contém links para instruções de instalação.
ms.prod: xamarin
ms.assetid: dd344d57-18e2-42a5-8c15-3f5be4123c72
author: conceptdev
ms.author: crdun
ms.date: 04/26/2018
ms.openlocfilehash: adedb10f3baf7faa97260ebbc94e3d1802b7d847
ms.sourcegitcommit: 6b833f44d5fd8dc7ab7f8546e8b7d383e5a989db
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105940"
---
# <a name="system-requirements"></a>Requisitos de sistema

Os produtos Xamarin contam com os SDKs de plataforma da Apple e do Google para direcionar para iOS ou Android, portanto, os nossos requisitos de sistema correspondem aos requisitos deles. Esta página descreve a compatibilidade do sistema para a plataforma Xamarin, o ambiente de desenvolvimento recomendado e as versões do SDK.

Observe as [instruções de instalação](#installation-instructions) para mais informações sobre como obter o software e os SDKs necessários.

## <a name="development-environments"></a>Ambientes de desenvolvimento

Esta tabela mostra quais plataformas podem ser criadas com diferentes combinações de ferramentas de desenvolvimento e sistemas operacionais:

[!include[](~/cross-platform/includes/development-environment.md)]

> [!NOTE]
> Para desenvolver para iOS em computadores Windows, deve haver um [computador Mac acessível na rede](~/ios/get-started/installation/windows/connecting-to-mac/index.md), para compilação remota e depuração. Isso também funciona se o Visual Studio estiver em execução dentro de uma VM do Windows em um computador Mac.

## <a name="macos-requirements"></a>requisitos do macOS

Para usar um computador Mac para desenvolvimento do Xamarin são necessárias as seguintes versões de software/SDK. Verifique a versão do sistema operacional e siga as instruções do [Instalador do Xamarin](#installation-instructions).

[!include[](~/cross-platform/includes/macos-requirements.md)]

> [!NOTE]
> O Xcode pode ser instalado (e atualizado) em [developer.apple.com](https://developer.apple.com/xcode/download/) ou por meio da Mac App Store.

### <a name="testing--debugging-on-macos"></a>Teste e depuração no macOS

- Os aplicativos móveis do Xamarin podem ser implantados em dispositivos físicos via USB para teste e depuração (os aplicativos Apple Watch são implantados primeiro no iPhone emparelhado).
- Aplicativos Xamarin.Mac podem ser testados diretamente no computador de desenvolvimento.

[!include[](~/cross-platform/includes/macos-testing.md)]

> [!WARNING]
> O Xamarin.Mac 4.8 dá suporte apenas ao macOS 10.9 (Mavericks) ou superior.
> As versões anteriores do Xamarin.Mac eram compatíveis com macOS 10.7 ou superior, mas essas versões mais antigas do macOS não têm infraestrutura TLS suficiente para dar suporte ao TLS 1.2. Para macOS 10.7 ou macOS 10.8, use o Xamarin.Mac 4.6 ou anterior.

## <a name="windows-requirements"></a>Requisitos do Windows

Para usar um computador Windows para desenvolvimento do Xamarin são necessárias as seguintes versões de software/SDK.
Verifique a versão do sistema operacional (e confirme se você não está usando uma versão *Express* do Visual Studio. Caso esteja, considere atualizar para uma edição *Community*).
O instalador do Visual Studio 2019 e Visual Studio 2017 inclui uma opção para instalar o Xamarin automaticamente (a carga de trabalho **Desenvolvimento móvel com .NET**).

[!include[](~/cross-platform/includes/windows-requirements.md)]

> [!NOTE]
>
> - O Xamarin para Visual Studio é compatível com qualquer versão do Visual Studio 2019 ou Visual Studio 2017 (Community, Professional e Enterprise).
> - Para desenvolver aplicativos do Xamarin.Forms para a UWP (Plataforma Universal do Windows), é necessário o Windows 10 com o Visual Studio 2017. É recomendável o Visual Studio 2019.

### <a name="testing--debugging-on-windows"></a>Teste e depuração no Windows

Os aplicativos móveis do Xamarin podem ser implantados em dispositivos físicos via USB ou sem fio para teste e depuração (os dispositivos iOS devem estar conectados ao computador Mac e não no computador que está executando o Visual Studio).

[!include[](~/cross-platform/includes/windows-testing.md)]

## <a name="installation-instructions"></a>Instruções de instalação

A versão mais recente do Xamarin para macOS pode ser baixada com o [Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/installation). Para o Windows, siga as [instruções de instalação do Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio).

Uma lista completa das nossas versões atuais do produto está disponível na [página de novidades](~/whats-new/index.yml). Esta página também contém links para as notas sobre a versão.

Instruções de [instalação](~/get-started/installation/index.md) específicas para cada plataforma estão disponíveis aqui:

- [Xamarin.iOS](~/ios/get-started/installation/index.md)
- [Xamarin.Android](~/android/get-started/installation/index.md)
- [Xamarin.Mac](~/mac/get-started/installation.md)

Também há informações adicionais sobre [Requisitos e plataformas com suporte no Xamarin.Forms](~/get-started/requirements.md).

## <a name="related-links"></a>Links relacionados

- [Baixe o Xamarin](https://visualstudio.microsoft.com/xamarin/)
- [Notas sobre a versão do Xamarin.Forms](/xamarin/xamarin-forms/release-notes/)
- [Notas sobre a versão do Xamarin.Android](/xamarin/android/release-notes/)
- [Notas sobre a versão do Xamarin.iOS](/xamarin/ios/release-notes/)
