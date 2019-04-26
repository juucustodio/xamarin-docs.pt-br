---
title: Aplicativos móveis do Microsoft Azure
description: Este documento leva a guias que descrevem como compilar um aplicativo Xamarin que está conectado ao Azure. Ele discute como trabalhar com o componente do Xamarin do Azure, os usuários e notificações por push.
ms.prod: xamarin
ms.assetid: 7B9AA8D9-C181-4C33-8AB0-2F56E4DBFC03
author: conceptdev
ms.author: crdun
ms.date: 04/02/2017
ms.openlocfilehash: baa687bfb3b2e8306e70e83b6a6ee54595110860
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61186844"
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


Esses links são para a documentação do Xamarin disponível na [aplicativos móveis do Azure](https://docs.microsoft.com/azure/app-service-mobile/) site.
Adicionando a funcionalidade do Azure para um aplicativo Xamarin baixando a [do Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).

## <a name="working-with-the-xamarin-azure-component"></a>Trabalhando com o componente Xamarin Azure

Documentação geral [trabalhando com a biblioteca de cliente do Xamarin (componente)](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library) realizar diversas tarefas com aplicativos móveis do Azure. Esta página contém vários trechos de código de exemplo, sem as explicações detalhadas e exemplos disponíveis em cada um dos artigos de instruções passo a passo listados a seguir.

## <a name="getting-started"></a>Guia de Introdução

Este artigo fornece instruções passo a passo para obter seu primeiro aplicativo do Azure do Xamarin em funcionamento.
Ele aborda a criação de um novo aplicativo móvel do Azure no portal do e, em seguida, baixando e executando o aplicativo previamente configurado.

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

## <a name="get-started-with-users"></a>Introdução aos usuários

Fornece instruções completas para configurar e codificar uma tela de logon usando os serviços móveis do Azure. Provedores de autenticação com suporte incluem Microsoft, Google, Facebook e Twitter.

-  [iOS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-ios-get-started-users/)
-  [Android](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-android-get-started-users/)


## <a name="authorize-users-in-scripts"></a>Autorizar usuários em Scripts

Um código de exemplo para o back-ends do Javascript

-  [Todo.js](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/personal-table/tables/TodoItem.js#L38)


## <a name="get-started-with-push"></a>Introdução ao envio por Push

Conclua as instruções para configurar notificações por push nos sites da Apple e Google e, em seguida, enviar uma notificação por push de serviços móveis do Azure para um dispositivo.

-  [iOS](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-ios-get-started-push)
-  [Android](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-android-get-started-push)


## <a name="get-started-with-notification-hubs"></a>Introdução aos Hubs de notificação

Instruções completas para configurar notificações por push nos sites da Apple e Google, configurar o Hub de notificação do Azure e, em seguida, gerar notificações por push para dispositivos.

-  [iOS](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started)
-  [Android](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm)



## <a name="related-links"></a>Links relacionados

- [GettingStarted (amostra)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GettingStarted)
- [GetStartedWithData (amostra)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithData)
- [GetStartedWithUsers (amostra)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithUsers)
- [GetStartedWithPush (amostra)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithPush)
- [Hubs de notificação (amostra)](https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs)
- [Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [Roteiro de aprendizagem de aplicativos móveis do Azure](https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/)

<!--
- [ValidateModifyData (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/ValidateModifyData)
-->
