---
title: Compra no xamarin. IOS no aplicativo
description: Este documento descreve como vender produtos digitais e serviços usando as APIs de StoreKit. Ele vincula às guias que discutem a configuração, produtos de consumo, produtos não consumíveis, transações, assinaturas e muito mais.
ms.prod: xamarin
ms.assetid: B41929D8-47E4-466D-1F09-6CC3C09C83B2
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 4b301c18ea0e69c818cf65b3b7df1cc8351350f5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119326"
---
# <a name="in-app-purchasing-in-xamarinios"></a>Compra no xamarin. IOS no aplicativo

aplicativos do iOS podem vender produtos digitais ou serviços que usam StoreKit – um conjunto de APIs fornecidos pelo iOS que se comunicam com servidores da Apple para conduzir transações financeiras com o usuário por meio de sua ID da Apple. As APIs StoreKit estão principalmente preocupados com Recuperando informações de produto e conduzir transações – não há nenhum componente de interface do usuário. Aplicativos que implementam a compras no aplicativo devem criar sua própria interface do usuário e acompanhar os itens comprados com código personalizado para fornecer os serviços ou produtos necessários para o usuário.

Fornecendo funcionalidade de compra no aplicativo requer um número de etapas:

-  **Configurar seu aplicativo** – o perfil de provisionamento do aplicativo deve ser configurado corretamente.
-  **Criação de produtos** – preços e as descrições de produto devem ser criados no portal do iTunes Connect.
-  **Implementando StoreKit** – StoreKit com a API da deve ser implementada de acordo com os tipos de produtos que estão sendo vendidos.
-  **Criando a interface do usuário e os produtos propriamente ditos** – os produtos devem ser implementados, incluindo mecanismos para controlar cada compra e backup/restauração-los se apropriado.
-  **Monitoramento de vendas e recebimento de fundos** – Use as informações fornecidas pelo iTunes Connect para monitorar as tendências de vendas e acompanhar seu renda.

Este documento explica como concluir todas essas etapas para fornecer a compras no aplicativo usando o xamarin. IOS.

## <a name="requirements"></a>Requisitos

Para dar suporte a compras no aplicativo, você deve usar o xamarin. IOS 5.0 ou mais recente com o Xcode 7 e posterior.

## <a name="contents"></a>Conteúdo

 * [Noções básicas e configuração de compras no aplicativo](~/ios/platform/in-app-purchasing/in-app-purchase-basics-and-configuration.md)

 * [Visão geral de StoreKit e ao recuperar informações do produto](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)

 * [Comprar produtos de consumo](~/ios/platform/in-app-purchasing/purchasing-consumable-products.md)

 * [Comprar produtos não consumíveis](~/ios/platform/in-app-purchasing/purchasing-non-consumable-products.md)

 * [Transações e verificação](~/ios/platform/in-app-purchasing/transactions-and-verification.md)

 * [Assinaturas e relatórios](~/ios/platform/in-app-purchasing/subscriptions-and-reporting.md)

## <a name="summary"></a>Resumo

Este artigo tem introduziu o conceito de compras no aplicativo, descreveu como configurar seu aplicativo para tirar proveito dela e apresentados exemplos usando o xamarin. IOS. Ele abordou:

-  **Portal de provisionamento iOS** – diretrizes para habilitação no aplicativo de compra funcionalidade.
-  **o iTunes Connect** – configurar produtos para vender em seu aplicativo.
-  **Store Kit** – explicação das classes usadas para criar recursos de compra no aplicativo.
-  **Codificar seu aplicativo para a compra de** – exemplos de como criar as compras no aplicativo em um aplicativo xamarin. IOS.
-  **Relatórios** – visão geral das estatísticas disponíveis por meio do iTunes Connect.


## <a name="related-links"></a>Links relacionados

- [InAppPurchaseSample](https://developer.xamarin.com/samples/StoreKit/)
- [Na guia de programação de aplicativo de compra](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/StoreKitGuide/Introduction.html)
- [Guia do Desenvolvedor do iTunes Connect](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/iTunesConnect_Guide.pdf)
- [Referência do Store Kit Framework](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/StoreKit_Collection/StoreKit_Collection.pdf)
- [Identificadores de produtos de compra no aplicativo p e r](https://developer.apple.com/library/ios/#qa/qa1329/_index.html)
- [Nota técnica de compra no aplicativo](https://developer.apple.com/library/ios/#technotes/tn2259/_index.html)
- [O envio da primeira App Store](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
- [Central de recursos do App Store](https://developer.apple.com/appstore/index.html)
- [Dicas de Envio da App Store](https://developer.apple.com/appstore/resources/submission/tips.html)
- [Diretrizes de Análise da App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [Gerenciar seus aplicativos](https://developer.apple.com/appstore/resources/managing/index.html)
