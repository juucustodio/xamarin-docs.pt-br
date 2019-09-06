---
title: Como atualizar seu aplicativo para iOS 11
description: Este documento contém links para vários guias que descrevem os novos recursos disponíveis para desenvolvedores do Xamarin. iOS com o lançamento do iOS 11. Por exemplo, atualizações de Design Visual, alterações na loja de aplicativos e atualizações de ícone de aplicativo.
ms.prod: xamarin
ms.assetid: EC809504-9CF6-4949-B6EE-36384297E744
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/13/2016
ms.openlocfilehash: 3f331c38d470ba77de7b3fc4ef0143bb673683e8
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286336"
---
# <a name="updating-your-app-to-ios-11"></a>Como atualizar seu aplicativo para iOS 11

No iOS 11, a Apple introduziu atualizações de arquitetura, novas alterações visuais e um processo do iTunes Connect atualizado. Este guia explora cada uma dessas alterações, ajudando você a obter seu aplicativo Xamarin. iOS atualizado para o iOS 11.

## <a name="architecture-changesarchitecture-changesmd"></a>[Alterações na arquitetura](architecture-changes.md)

Uma das maiores alterações que você deve estar ciente com o iOS 11 é a substituição do suporte de 32 bits para aplicativos, conforme detalhado na versão [de imprensa da Apple](https://developer.apple.com/news/?id=06282017b) .

Este guia o orienta na atualização de seu aplicativo para 64 bits.

## <a name="visual-design-updatesvisual-designmd"></a>[Atualizações no design visual](visual-design.md)

No iOS 11, a Apple introduziu novas alterações visuais, incluindo atualizações na barra de navegação, na barra de pesquisa e nos modos de exibição de tabela. Além disso, foram feitas melhorias para permitir mais flexibilidade em relação às margens e ao conteúdo de tela inteira. Essas alterações são abordadas neste guia.

## <a name="app-store-changesapp-store-changesmd"></a>[Alterações na App Store](app-store-changes.md)

A iOS App Store teve uma reformulação completa, o que permite que os usuários naveguem com eficiência na loja, mas também permite que você, como desenvolvedor, promova seu aplicativo para os usuários. Essas promoções incluem atualizações para compras e atualizações no aplicativo na página do produto. o iOS 11 também adiciona atualizações sobre como se comunicar com os usuários, como adicionar o ícone do aplicativo e como liberar seu aplicativo para o público.

## <a name="app-icon-updates"></a>Atualizações de ícone do aplicativo

> [!NOTE]
> Os ícones de aplicativo agora devem ser entregues por um _Catálogo de ativos_. 

Para obter informações sobre como usar catálogos de ativos, consulte o guia de [ícone da loja de aplicativos](~/ios/app-fundamentals/images-icons/app-store-icon.md) . Para obter ajuda com a migração de ícones de um info. plist para um catálogo de ativos, consulte o guia [migrando do info. plist para catálogos de ativos](~/ios/app-fundamentals/images-icons/app-icons.md) .

O ícone necessário no catálogo de ativos é denominado **App Store** e deve ter 1024 x 1024 de tamanho. A Apple declarou que o ícone da App Store no catálogo de ativos não pode ser transparente nem conter um canal alfa.

![Local do ícone da loja de aplicativos no catálogo de ativos.](images/image1.png)

## <a name="related-links"></a>Links relacionados

- [O que há de novo no iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página de produto da App Store atualizada (Apple)](https://developer.apple.com/app-store/product-page/)
- [Atualizando seu aplicativo para iOS 11 (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)
