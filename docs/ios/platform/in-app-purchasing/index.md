---
title: Compras no Aplicativo
description: aplicativos iOS podem vender produtos digitais e serviços usando as APIs do Kit de armazenamento. Produtos são criados e gerenciados no portal de conectar iTunes. Gerencia o processamento de transações e aprovar todos os produtos antes que possam ser vendidos, Apple e cobra uma taxa para cada transação (atualmente 30%). Apple requer que você use a compra no aplicativo para as vendas digitais em seu aplicativo, mas você não pode usá-lo para as vendas de produtos físicos ou serviços não digital. Aplicativos que oferecem opções de pagamento alternativo para serviços e produtos digitais têm probabilidade de ser rejeitada. Este documento explica como configurar seu aplicativo para usar o Kit de armazenamento e fornece exemplos de xamarin dos cenários mais comuns de compras no aplicativo.
ms.prod: xamarin
ms.assetid: B41929D8-47E4-466D-1F09-6CC3C09C83B2
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 7a8dec6051caeba55c45df29c085ecfcddd160d2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="in-app-purchasing"></a>Compras no Aplicativo

_aplicativos iOS podem vender produtos digitais e serviços usando as APIs do Kit de armazenamento. Produtos são criados e gerenciados no portal de conectar iTunes. Gerencia o processamento de transações e aprovar todos os produtos antes que possam ser vendidos, Apple e cobra uma taxa para cada transação (atualmente 30%). Apple requer que você use a compra no aplicativo para as vendas digitais em seu aplicativo, mas você não pode usá-lo para as vendas de produtos físicos ou serviços não digital. Aplicativos que oferecem opções de pagamento alternativo para serviços e produtos digitais têm probabilidade de ser rejeitada. Este documento explica como configurar seu aplicativo para usar o Kit de armazenamento e fornece exemplos de xamarin dos cenários mais comuns de compras no aplicativo._


aplicativos iOS podem vender produtos digitais ou serviços usando StoreKit – um conjunto de APIs fornecidas pelo iOS que se comunicam com servidores da Apple para realizar transações financeiras com o usuário por meio de sua ID da Apple. As APIs StoreKit são principalmente preocupado com a recuperação de informações de produto e realizar transações – não há nenhum componente de interface do usuário. Os aplicativos que implementam a compra no aplicativo devem criar sua própria interface de usuário e acompanhar itens comprados com código personalizado para fornecer os serviços ou produtos necessários para o usuário.

Fornece funcionalidade de compra no aplicativo requer um número de etapas:

-  **Configuração do aplicativo** – o perfil de provisionamento do aplicativo deve ser configurado corretamente.
-  **Criar produtos** – preços e descrições de produto devem ser criados no portal de conectar iTunes.
-  **Implementando StoreKit** – a API de StoreKit deve ser implementada de acordo com os tipos de produtos vendidos.
-  **Criando a interface do usuário e os produtos propriamente ditos** – os produtos devem ser implementados, incluindo mecanismos para controlar a cada compra e backup/restauração-los se apropriado.
-  **Monitoramento de vendas e receber fundos** – Use as informações fornecidas pelo iTunes Connect para monitorar tendências de vendas e acompanhar seu renda.


Este documento explica como concluir todas estas etapas para fornecer compras no aplicativo usando o xamarin.


## <a name="requirements"></a>Requisitos

Para dar suporte a compra no aplicativo, você deve usar xamarin 5.0 ou mais recente com o Xcode 7 e posterior.

## <a name="contents"></a>Conteúdo

 * [Noções básicas e configuração de compras no aplicativo](~/ios/platform/in-app-purchasing/in-app-purchase-basics-and-configuration.md)

 * [Visão geral do Store kit e recuperação de informações do produto](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)

 * [Comprar produtos de consumo](~/ios/platform/in-app-purchasing/purchasing-consumable-products.md)

 * [Comprar produtos não consumíveis](~/ios/platform/in-app-purchasing/purchasing-non-consumable-products.md)

 * [Transações e verificação](~/ios/platform/in-app-purchasing/transactions-and-verification.md)

 * [Assinaturas e relatórios](~/ios/platform/in-app-purchasing/subscriptions-and-reporting.md)


## <a name="summary"></a>Resumo

Este artigo tem introduziu o conceito de compra no aplicativo, descritas como configurar seu aplicativo para tirar proveito dele e apresentados exemplos que usam o xamarin. Abordou:

-  **Portal de provisionamento do iOS** – diretrizes para habilitar no aplicativo de compra funcionalidade.
-  **conectar-se de iTunes** – configuração de produtos para venda no seu aplicativo.
-  **Armazenar Kit** – explicação das classes usadas para criar recursos de compra no aplicativo.
-  **Seu aplicativo de compras de codificação** – exemplos de como criar as compras no aplicativo em um aplicativo xamarin.
-  **Relatório** – visão geral das estatísticas disponíveis por meio do Connect do iTunes.


## <a name="related-links"></a>Links relacionados

- [InAppPurchaseSample](https://developer.xamarin.com/samples/StoreKit/)
- [Na guia de programação de aplicativo de compra](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/StoreKitGuide/Introduction.html)
- [Guia do Desenvolvedor do iTunes Connect](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/iTunesConnect_Guide.pdf)
- [Referência de estrutura do Kit de armazenamento](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/StoreKit_Collection/StoreKit_Collection.pdf)
- [Identificadores de produtos de compra no aplicativo p e r](https://developer.apple.com/library/ios/#qa/qa1329/_index.html)
- [Nota técnica de compra no aplicativo](https://developer.apple.com/library/ios/#technotes/tn2259/_index.html)
- [O envio do primeiro repositório de aplicativo](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
- [Central de recursos de armazenamento de aplicativo](https://developer.apple.com/appstore/index.html)
- [Dicas de Envio da App Store](https://developer.apple.com/appstore/resources/submission/tips.html)
- [Diretrizes de Análise da App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [Gerenciando seus aplicativos](https://developer.apple.com/appstore/resources/managing/index.html)
