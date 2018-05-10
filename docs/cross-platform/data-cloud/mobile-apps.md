---
title: Aplicativos móveis do Microsoft Azure
description: Exemplos e código de baixa para a documentação do portal do Azure.
ms.prod: xamarin
ms.assetid: 7B9AA8D9-C181-4C33-8AB0-2F56E4DBFC03
author: conceptdev
ms.author: crdun
ms.date: 04/02/2017
ms.openlocfilehash: 54ea6ee764054da0f24ce94303a8899971fd63af
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="microsoft-azure-mobile-apps"></a>Aplicativos móveis do Microsoft Azure

_Exemplos e código de baixa para a documentação do portal do Azure._

<!--
NOTE TO AUTHORS: this page is referenced from
http://azure.microsoft.com/develop/mobile/xamarin/
as https://developer.xamarin.com/guides/cross-platform/data-cloud/mobile-services/
A redirect has been put in place to /mobile-apps/ HOWEVER the /Resources/ .ZIP files are still located in /mobile-services/ so that the following permalinks don't break

The ZIPs in /Resources/ are also referenced by inbound links
Getting Started  http://go.microsoft.com/fwlink/p/?LinkId=331359
Get started with data   http://go.microsoft.com/fwlink/p/?LinkId=331302
Get started with push   http://go.microsoft.com/fwlink/p/?LinkId=331303
Get started with authentication http://go.microsoft.com/fwlink/p/?LinkId=331328
Get started with Notification Hubs  http://go.microsoft.com/fwlink/p/?LinkId=331329
Validate and modify data    http://go.microsoft.com/fwlink/p/?LinkId=331330
-->


Esses links são para a documentação do Xamarin disponível no [aplicativos móveis do Azure](https://docs.microsoft.com/azure/app-service-mobile/) site.
Adicionando funcionalidade do Azure para um aplicativo Xamarin baixando o [de cliente móvel do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).

## <a name="working-with-the-xamarin-azure-component"></a>Trabalhando com o componente Xamarin Azure

Documentação geral [trabalhar com a biblioteca de cliente Xamarin (componente)](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library) realizar várias tarefas com aplicativos móveis do Azure. Esta página contém vários trechos de código de exemplo, sem as explicações detalhadas e exemplos disponíveis em cada um dos artigos de instruções passo a passo listados abaixo.

## <a name="getting-started"></a>Guia de Introdução

Este artigo fornece instruções passo a passo para obter seu primeiro aplicativo do Xamarin Azure em funcionamento.
Ele abrange a criar um novo aplicativo móvel do Azure no portal do e, em seguida, baixar e executar o aplicativo pré-configurado.

-  [iOS](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-ios-get-started/)
-  [Android](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-android-get-started/)
-  [Xamarin.Forms](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started)

<!--
## Validate, Modify and Augment Data in Scripts

Demonstrates how to add server-side scripts to Azure Mobile Services data tables to implement server-side validation and other functionality.

-  [iOS](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
-  [Android](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
-->

<!--
## Add Paging to Data

A quick example of paging large sets of data using Skip() and Take().

-  [iOS](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
-  [Android](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
-->

## <a name="get-started-with-users"></a>Introdução a usuários

Fornece instruções completas para configurar e codificar uma tela de logon usando os serviços móveis do Azure. Provedores de autenticação com suporte incluem Microsoft, Google, Facebook e Twitter.

-  [iOS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-ios-get-started-users/)
-  [Android](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-android-get-started-users/)


## <a name="authorize-users-in-scripts"></a>Autorizar usuários em Scripts

Um exemplo de código de back-ends Javascript

-  [Todo.js](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/personal-table/tables/TodoItem.js#L38)


## <a name="get-started-with-push"></a>Introdução ao envio

Siga as instruções para configurar notificações por push nos sites Apple e Google e, em seguida, enviar uma notificação por push de serviços móveis do Azure para um dispositivo.

-  [iOS](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-ios-get-started-push)
-  [Android](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-android-get-started-push)


## <a name="get-started-with-notification-hubs"></a>Introdução aos Hubs de notificação

Instruções completas para configurar notificações por push nos sites Apple e Google, configure o Hub de notificação do Azure e, em seguida, gerar notificações por push para dispositivos.

-  [iOS](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started)
-  [Android](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm)



## <a name="related-links"></a>Links relacionados

- [GettingStarted (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GettingStarted)
- [GetStartedWithData (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithData)
- [GetStartedWithUsers (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithUsers)
- [GetStartedWithPush (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithPush)
- [Hubs de notificação (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs)
- [Cliente móvel do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [Caminho de aprendizado de aplicativos móveis do Azure](https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/)

<!--
- [ValidateModifyData (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/ValidateModifyData)
-->
