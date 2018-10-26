---
title: Atualizar seu aplicativo para iOS 11
description: Este documento leva a vários guias que descrevem os novos recursos disponíveis para desenvolvedores do xamarin. IOS com a versão do iOS 11. Por exemplo, atualizações de design visual, alterações de Store de aplicativo e de atualizações de ícone do aplicativo.
ms.prod: xamarin
ms.assetid: EC809504-9CF6-4949-B6EE-36384297E744
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: 3193f27c30080a4335abfe969acb3c8b33516469
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110772"
---
# <a name="updating-your-app-to-ios-11"></a>Atualizar seu aplicativo para iOS 11

No iOS 11, a Apple introduziu as atualizações de arquitetura, novas alterações visual e um processo de conectar-se atualizado do iTunes. Este guia explora a cada uma dessas alterações, ajudando você a obter o aplicativo xamarin. IOS atualizado para o iOS 11.

## <a name="architecture-changesarchitecture-changesmd"></a>[Alterações na arquitetura](architecture-changes.md)

Uma das maiores alterações que você deve estar atento com o iOS 11 é a substituição do suporte de 32 bits para aplicativos, conforme detalhado na [da Apple](https://developer.apple.com/news/?id=06282017b) comunicado à imprensa.

Este guia orienta você pelo atualizando seu aplicativo de 64 bits.

## <a name="visual-design-updatesvisual-designmd"></a>[Atualizações no design visual](visual-design.md)

No iOS 11, a Apple introduziu novas alterações visual, incluindo atualizações para a barra de navegação, barra de pesquisa e modos de exibição de tabela. Além de melhorias foram feitas para permitir mais flexibilidade sobre as margens e o conteúdo de tela inteira. Essas alterações são abordadas neste guia.

## <a name="app-store-changesapp-store-changesmd"></a>[Alterações na App Store](app-store-changes.md)

No iOS App Store teve uma reformulação completa, que não só permite aos usuários navegar com eficiência o armazenamento, mas também permite que você, como desenvolvedor, para promover seu aplicativo aos usuários. Esses promoções incluem as atualizações para compras no aplicativo e a página do produto. iOS 11 também adiciona as atualizações relacionadas a como se comunicar com os usuários, como adicionar o ícone do aplicativo e como liberar seu aplicativo para o público.

## <a name="app-icon-updates"></a>Atualizações de ícone do aplicativo

> [!NOTE]
> Ícones do aplicativo devem ser entregues agora por um _catálogo de ativos_. 

Para obter informações sobre como usar os catálogos de ativos, consulte a [ícone da App Store](~/ios/app-fundamentals/images-icons/app-store-icon.md) guia. Para obter ajuda com a migração ícones de um Info. plist para um catálogo de ativos, consulte a [Migrando do Info. plist para catálogos de ativos](~/ios/app-fundamentals/images-icons/app-icons.md) guia.

O ícone necessário no catálogo de ativos é denominado **App Store** e deve ser 1024 x 1024 em tamanho. A Apple declarou que o ícone da App Store no catálogo de ativos não pode ser transparente nem conter um canal alfa.

![Aplicativo local de ícone do repositório no catálogo de ativos.](images/image1.png)

## <a name="related-links"></a>Links relacionados

- [O que há de novo no iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página de Store de aplicativo atualizado do produto (Apple)](https://developer.apple.com/app-store/product-page/)
- [Atualizar seu aplicativo para iOS 11 (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)
