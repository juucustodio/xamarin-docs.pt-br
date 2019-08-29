---
title: Modo escuro no Xamarin. iOS
description: O modo escuro é uma nova opção de todo o sistema para temas leves e escuros. Agora, o usuário do iOS pode escolher um tema ou permitir que o iOS altere a aparência dinamicamente.
ms.prod: xamarin
ms.assetid: 4F44446E-36B6-4743-9B4D-32278D1D3D66
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/28/2019
ms.openlocfilehash: d21afcc7d7b130528e9cceac47840acd49b91f59
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129968"
---
# <a name="dark-mode-in-xamarinios"></a>Modo escuro no Xamarin. iOS

![Esta API está atualmente em visualização](~/media/shared/preview.png)

O modo escuro é uma opção de todo o sistema para temas claros e escuros. Agora, os usuários do iOS podem escolher o tema ou permitir que o iOS altere a aparência dinamicamente com base no ambiente e na hora do dia.

Este documento apresenta o modo escuro e dá suporte ao modo escuro em aplicativos iOS 13.

## <a name="requirements"></a>Requisitos

O modo escuro requer o iOS 13 e o Xcode 11, o Xamarin. iOS 12,99 e o Visual Studio 2019 ou o Visual Studio 2019 para Mac com suporte ao Xcode 11.

## <a name="turning-on-dark-mode"></a>Ativando o modo escuro

A Apple fornece um menu de desenvolvedor no iOS 13 para alternar entre os modos escuro e claro. No simulador do iOS 13, abra **as configurações** e escolha a seção **desenvolvedor** e role até a opção de **aparência escura** . A alteração será refletida em todo o ambiente do simulador:

![Ativando o modo escuro](dark-mode-images/LightAndDark_DeveloperSetting.png)

## <a name="assets-for-light-and-dark-modes"></a>Ativos para os modos claro e escuro

O catálogo de ativos no Visual Studio agora dá suporte a imagens e cores opcionais para cada modo de aparência: Universal, Dark e Light. Ao definir imagens e cores dessa forma, o iOS escolherá automaticamente a imagem e a cor apropriadas.

Abra seu arquivo **assets. xcassets** no seu projeto do IOS e adicione um novo conjunto de imagens. Observe que você pode especificar imagens universais, escuras e leves em qualquer uma das resoluções de destino. Na captura de tela abaixo, há uma imagem para Dark e Light com o nome de "MicrosoftLogo":

![Ativos para os modos claro e escuro](dark-mode-images/LightAndDark_AssetCatalog2.png)

**Assets. xcassets** também contém entradas para **backgroundColor** e **TitleColor**, que são definições de cores. Essas cores agora estão disponíveis pelo nome a ser usado em todo o aplicativo. O **backgroundColor** foi atribuído ao plano de fundo da exibição e **TitleColor** ao rótulo, conforme mostrado nesta captura de tela:

![Ativos para os modos claro e escuro](dark-mode-images/LightAndDark_01.png)

## <a name="dynamic-system-colors"></a>Cores dinâmicas do sistema

A Apple introduziu novas cores semânticas que ajustam sua aparência dinamicamente com base na nova configuração de modo escuro.

## <a name="summary"></a>Resumo

Este artigo introduziu o modo escuro para iOS e especificando imagens e cores para cada modo usando o catálogo de ativos.

## <a name="related-links"></a>Links relacionados

- [Diretrizes de design de modo escuro](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/dark-mode/)
- [Cores semânticas](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/color/#dynamic-system-colors)
