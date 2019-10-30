---
title: Microsoft Azure aplicativos móveis
description: Este documento contém links para guias que descrevem como criar um aplicativo Xamarin que está conectado ao Azure. Ele aborda o trabalho com o componente, os usuários e as notificações por push do Xamarin Azure.
ms.prod: xamarin
ms.assetid: 7B9AA8D9-C181-4C33-8AB0-2F56E4DBFC03
author: davidortinau
ms.author: daortin
ms.date: 04/02/2017
ms.openlocfilehash: 84517e4961dc3ad728b6cc352e9fb992d9e8b5bf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016588"
---
# <a name="microsoft-azure-mobile-apps"></a>Microsoft Azure aplicativos móveis

> [!NOTE]
> O Visual Studio App Center dá suporte a serviços de ponta a ponta e integrados central ao desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **Compilar**, **testar** e **distribuir** serviços para configurar um pipeline de integração e entrega contínua. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso de seus aplicativos usando os serviços de **análise** e **diagnóstico** e se envolver com os usuários usando o serviço de **envio por push** . Os desenvolvedores também podem aproveitar a **autenticação** para autenticar seus usuários e o serviço de **dados** para persistir e sincronizar dados de aplicativos na nuvem.
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com o [app Center](https://appcenter.ms/signup?utm_source=XamarinDocs&utm_medium=Azure&utm_campaign=docs) hoje mesmo.

_Exemplos e downloads de código para a documentação do portal do Azure._

<!--
NOTE TO AUTHORS: this page is referenced from
https://azure.microsoft.com/develop/mobile/xamarin/
as https://developer xamarin com/guides/cross-platform/data-cloud/mobile-services/
A redirect has been put in place to /mobile-apps/ HOWEVER the /Resources/ .ZIP files are still located in /mobile-services/ so that the following permalinks don't break

The ZIPs in /Resources/ are also referenced by inbound links
Getting Started https://go.microsoft.com/fwlink/p/?LinkId=331359
Get started with data https://go.microsoft.com/fwlink/p/?LinkId=331302
Get started with push https://go.microsoft.com/fwlink/p/?LinkId=331303
Get started with authentication https://go.microsoft.com/fwlink/p/?LinkId=331328
Get started with Notification Hubs https://go.microsoft.com/fwlink/p/?LinkId=331329
Validate and modify data  https://go.microsoft.com/fwlink/p/?LinkId=331330
-->

Esses links são para a documentação do Xamarin disponível no site de [aplicativos móveis do Azure](https://docs.microsoft.com/azure/app-service-mobile/) .
Adicionando a funcionalidade do Azure a um aplicativo Xamarin baixando o [cliente móvel do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).

## <a name="working-with-the-xamarin-azure-component"></a>Trabalhando com o componente Xamarin Azure

Documentação geral que [trabalha com a biblioteca de cliente do Xamarin (componente)](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library) para realizar várias tarefas com os aplicativos móveis do Azure. Esta página contém muitos trechos de código de exemplo, sem as explicações detalhadas e os exemplos disponíveis em cada um dos artigos passo a passos listados abaixo.

## <a name="getting-started"></a>Guia de Introdução

Este artigo fornece instruções passo a passo para colocar seu primeiro aplicativo do Xamarin Azure em funcionamento.
Ele aborda a criação de um novo aplicativo móvel do Azure no portal e, em seguida, o download e a execução do aplicativo pré-configurado.

- [iOS](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-ios-get-started/)
- [Android](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-android-get-started/)
- [Xamarin.Forms](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started)

<!--
## Validate, Modify and Augment Data in Scripts

Demonstrates how to add server-side scripts to Azure Mobile Services data tables to implement server-side validation and other functionality.

- [iOS](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
- [Android](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
-->

<!--
## Add Paging to Data

A quick example of paging large sets of data using Skip() and Take().

- [iOS](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
- [Android](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
-->

## <a name="get-started-with-users"></a>Introdução aos usuários

Fornece instruções completas para configurar e codificar uma tela de logon usando os serviços móveis do Azure. Os provedores de autenticação com suporte incluem Microsoft, Google, Facebook e Twitter.

- [iOS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-ios-get-started-users/)
- [Android](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-android-get-started-users/)

## <a name="authorize-users-in-scripts"></a>Autorizar usuários em scripts

Alguns códigos de exemplo para back-ends de JavaScript

- [Todo. js](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/personal-table/tables/TodoItem.js#L38)

## <a name="get-started-with-push"></a>Introdução ao push

Conclua as instruções para configurar notificações por push nos sites da Apple e do Google e, em seguida, envie uma notificação por push dos serviços móveis do Azure para um dispositivo.

- [iOS](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-ios-get-started-push)
- [Android](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-android-get-started-push)

## <a name="get-started-with-notification-hubs"></a>Introdução aos hubs de notificação

Conclua as instruções para configurar notificações por push nos sites da Apple e do Google, configure o Hub de notificação do Azure e, em seguida, gere notificações por push para dispositivos.

- [iOS](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started)
- [Android](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm)

## <a name="related-links"></a>Links relacionados

- [GettingStarted (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GettingStarted)
- [GetStartedWithData (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithData)
- [GetStartedWithUsers (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithUsers)
- [GetStartedWithPush (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithPush)
- [NotificationHubs (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs)
- [Cliente móvel do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [Roteiro de aprendizagem de aplicativos móveis do Azure](https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/)

<!--
- [ValidateModifyData (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/ValidateModifyData)
-->
