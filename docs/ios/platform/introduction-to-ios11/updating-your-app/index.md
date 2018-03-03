---
title: Atualizar seu aplicativo para iOS 11
description: Explorando os novos recursos do iOS 11
ms.topic: article
ms.prod: xamarin
ms.assetid: EC809504-9CF6-4949-B6EE-36384297E744
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: cced7cc3d1b0579c36d598ef0d05da872478c8dc
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="updating-your-app-to-ios-11"></a>Atualizar seu aplicativo para iOS 11

_Explorando os novos recursos do iOS 11_

No iOS 11, Apple introduziu arquitetura atualizações, novas alterações visuais e um processo de conectar-se de iTunes atualizado. Este guia explora a cada uma dessas alterações, ajudando você a obter o aplicativo xamarin atualizado para iOS 11.

## <a name="architecture-changesarchitecture-changesmd"></a>[Alterações de arquitetura](architecture-changes.md)

Uma das maiores alterações que você deve estar atento com iOS 11 é a substituição do suporte de 32 bits para aplicativos, conforme detalhado no [da Apple](https://developer.apple.com/news/?id=06282017b) imprensa.

Este guia orienta a atualizar seu aplicativo de 64 bits.

## <a name="visual-design-updatesvisual-designmd"></a>[Atualizações de Design Visual](visual-design.md)

No iOS 11, a Apple introduziu novas alterações visual, incluindo atualizações para a barra de navegação, barra de pesquisa e modos de exibição de tabela. Além de melhorias foram feitas para permitir mais flexibilidade em conteúdo de tela inteira e de margens. Essas alterações são abordadas neste guia.

## <a name="app-store-changesapp-store-changesmd"></a>[Alterações de loja de aplicativos](app-store-changes.md)

IOS App Store teve uma reestruturação completa, que não só permite aos usuários navegar com eficiência o armazenamento, mas também permite que você, como desenvolvedor, promover o seu aplicativo aos usuários. Esses promoções incluem as atualizações para compras no aplicativo e a página do produto. iOS 11 também adiciona atualizações sobre como se comunicar com os usuários, como adicionar o ícone do aplicativo e a versão do seu aplicativo para o público.

## <a name="app-icon-updates"></a>Atualizações de ícone do aplicativo

> [!NOTE]
> Ícones de aplicativo agora devem ser entregue por um _catálogo de ativos_. 

Para obter informações sobre como usar o catálogo de ativos, consulte o [App Store ícone](~/ios/app-fundamentals/images-icons/app-store-icon.md) guia. Para obter ajuda com a migração ícones de um Info. plist para um catálogo de ativos, consulte o [migrando de info. plist em catálogos do Asset](~/ios/app-fundamentals/images-icons/app-icons.md) guia.

Chamado no ícone necessário no catálogo de ativos de **App Store** e deve ser 1024 x 1024 em tamanho. A Apple declarou que o ícone da App Store no catálogo de ativos não pode ser transparente nem conter um canal alfa.

![Localização do ícone de loja de aplicativos no catálogo de ativos.](images/image1.png)

## <a name="related-links"></a>Links relacionados

- [O que há de novo no iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página de produto atualizados App Store (Apple)](https://developer.apple.com/app-store/product-page/)
- [Atualizar seu aplicativo para iOS 11 (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)
