---
title: Compras no aplicativo no Xamarin. iOS
description: Este documento descreve como vender produtos e serviços digitais usando as APIs do StoreKit. Ele é vinculado a guias que abordam a configuração, produtos consumíveis, produtos não consumíveis, transações, assinaturas e muito mais.
ms.prod: xamarin
ms.assetid: B41929D8-47E4-466D-1F09-6CC3C09C83B2
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 7e061f12e45e6d60c1772fc8699ffdb1f1b1d730
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430395"
---
# <a name="in-app-purchasing-in-xamarinios"></a>Compras no aplicativo no Xamarin. iOS

os aplicativos iOS podem vender produtos ou serviços digitais usando o StoreKit – um conjunto de APIs fornecidas pelo iOS que se comunicam com os servidores da Apple para conduzir transações financeiras com o usuário por meio de sua ID Apple. As APIs StoreKit se preocupam principalmente com a recuperação de informações do produto e a condução de transações – não há nenhum componente de interface do usuário. Os aplicativos que implementam a compra no aplicativo devem criar sua própria interface do usuário e acompanhar os itens comprados com código personalizado para fornecer os produtos ou serviços necessários ao usuário.

Fornecer a funcionalidade de compra no aplicativo requer uma série de etapas:

- **Configurando seu aplicativo** – o perfil de provisionamento do aplicativo deve ser configurado corretamente.
- **Criando produtos** – as descrições e os preços do produto devem ser criados no portal do iTunes Connect.
- **Implementando StoreKit** – a API StoreKit deve ser implementada de acordo com os tipos de produtos vendidos.
- **Criando a interface do usuário e os próprios produtos** – os produtos devem ser implementados, incluindo mecanismos para controlar cada compra e fazer backup/restaurá-los, se apropriado.
- **Monitorando vendas e recebendo fundos** – use as informações fornecidas pelo iTunes Connect para monitorar as tendências de vendas e acompanhar sua renda.

Este documento explica como concluir todas essas etapas para fornecer compras no aplicativo usando o Xamarin. iOS.

## <a name="requirements"></a>Requisitos

Para dar suporte à compra no aplicativo, você deve usar o Xamarin. iOS 5,0 ou mais recente com o Xcode 7 e superior.

## <a name="contents"></a>Sumário

- [Noções básicas e configuração de compra no aplicativo](~/ios/platform/in-app-purchasing/in-app-purchase-basics-and-configuration.md)

- [Visão geral do StoreKit e recuperação das informações do produto](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)

- [Como adquirir produtos de consumo](~/ios/platform/in-app-purchasing/purchasing-consumable-products.md)

- [Como adquirir produtos não consumíveis](~/ios/platform/in-app-purchasing/purchasing-non-consumable-products.md)

- [Transações e verificação](~/ios/platform/in-app-purchasing/transactions-and-verification.md)

- [Assinaturas e relatórios](~/ios/platform/in-app-purchasing/subscriptions-and-reporting.md)

## <a name="summary"></a>Resumo

Este artigo introduziu o conceito de compra no aplicativo, descreveu como configurar seu aplicativo para tirar proveito dele e obter exemplos apresentados usando o Xamarin. iOS. Ele abordou:

- **portal de provisionamento do IOS** – diretrizes para habilitar a funcionalidade de compra no aplicativo.
- **iTunes Connect** – Configurando produtos para vender em seu aplicativo.
- **Store kit** – explicação das classes usadas para criar recursos de compra no aplicativo.
- **Codificação de seu aplicativo para compra** – exemplos de como criar compras no aplicativo em um aplicativo Xamarin. Ios.
- **Relatório** – visão geral das estatísticas disponíveis por meio do iTunes Connect.

## <a name="related-links"></a>Links Relacionados

- [InAppPurchaseSample](/samples/xamarin/ios-samples/storekit/)
- [Guia de programação de compra no aplicativo](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/StoreKitGuide/Introduction.html)
- [Guia do Desenvolvedor do iTunes Connect](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/iTunesConnect_Guide.pdf)
- [Referência da estrutura do kit de repositório](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/StoreKit_Collection/StoreKit_Collection.pdf)
- [Identificadores de produtos de compra no aplicativo Q&A](https://developer.apple.com/library/ios/#qa/qa1329/_index.html)
- [Nota técnica de compra no aplicativo](https://developer.apple.com/library/ios/#technotes/tn2259/_index.html)
- [Seu primeiro envio da loja de aplicativos](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
- [Dicas de Envio da App Store](https://developer.apple.com/appstore/resources/submission/tips.html)
- [Diretrizes de Análise da App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [Gerenciando seus aplicativos](https://developer.apple.com/appstore/resources/managing/index.html)