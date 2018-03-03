---
title: Microsoft Azure Mobile Apps
description: "Exemplos e código de baixa para a documentação do portal do Azure."
ms.topic: article
ms.prod: xamarin
ms.assetid: 7B9AA8D9-C181-4C33-8AB0-2F56E4DBFC03
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: fb3c26b7d090ca42328c61192c794dec1544d1d3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="microsoft-azure-mobile-apps"></a>Microsoft Azure Mobile Apps

_Exemplos e código de baixa para a documentação do portal do Azure._

<!--
NOTE TO AUTHORS: this page is referenced from
http://azure.microsoft.com/en-us/develop/mobile/xamarin/
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


Esses links são para a documentação do Xamarin disponível no [aplicativos móveis do Azure](https://azure.microsoft.com/en-us/documentation/services/app-service/mobile/) site.
Adicionando funcionalidade do Azure para um Xamarin aplicativo é tão simple quanto baixando o [de cliente móvel do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).

## <a name="working-with-the-xamarin-azure-component"></a>Trabalhando com o componente Xamarin Azure

Documentação geral [trabalhar com a biblioteca de cliente Xamarin (componente)](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-dotnet-how-to-use-client-library/) realizar várias tarefas com aplicativos móveis do Azure. Esta página contém vários trechos de código de exemplo, sem as explicações detalhadas e exemplos disponíveis em cada um dos artigos de instruções passo a passo listados abaixo.

## <a name="getting-started"></a>Guia de Introdução

Este artigo fornece instruções passo a passo para obter seu primeiro aplicativo do Xamarin Azure em funcionamento.
Ele abrange a criar um novo aplicativo móvel do Azure no portal do e, em seguida, baixar e executar o aplicativo pré-configurado.

-  [iOS](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-ios-get-started/)
-  [Android](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-android-get-started/)
-  [Xamarin.Forms](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-forms-get-started/)

## <a name="validate-modify-and-augment-data-in-scripts"></a>Validar, modificar e aumentar os dados em Scripts

Demonstra como adicionar scripts de servidor a tabelas de dados de serviços móveis do Azure para implementar a validação do lado do servidor e outras funcionalidades.

-  [iOS](https://azure.microsoft.com/en-us/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
-  [Android](https://azure.microsoft.com/en-us/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)


## <a name="add-paging-to-data"></a>Adicionar paginação a dados

Um exemplo rápido de grandes conjuntos de dados usando Skip() e Take() de paginação.

-  [iOS](https://azure.microsoft.com/en-us/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
-  [Android](https://azure.microsoft.com/en-us/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)


## <a name="get-started-with-users"></a>Introdução a usuários

Fornece instruções completas para configurar e codificar uma tela de logon usando os serviços móveis do Azure. Provedores de autenticação com suporte incluem Microsoft, Google, Facebook e Twitter.

-  [iOS](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-ios-get-started-users/)
-  [Android](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-android-get-started-users/)


## <a name="authorize-users-in-scripts"></a>Autorizar usuários em Scripts

Um exemplo de código de back-ends Javascript

-  [Todo.js](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/personal-table/tables/TodoItem.js#L38)


## <a name="get-started-with-push"></a>Introdução ao envio

Siga as instruções para configurar notificações por push nos sites Apple e Google e, em seguida, enviar uma notificação por push de serviços móveis do Azure para um dispositivo.

-  [iOS](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-ios-get-started-push/)
-  [Android](https://azure.microsoft.com/en-us/documentation/articles/app-service-mobile-xamarin-android-get-started-push/)


## <a name="get-started-with-notification-hubs"></a>Introdução aos Hubs de notificação

Instruções completas para configurar notificações por push nos sites Apple e Google, configure o Hub de notificação do Azure e, em seguida, gerar notificações por push para dispositivos.

-  [iOS](http://azure.microsoft.com/en-us/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/)
-  [Android](http://azure.microsoft.com/en-us/documentation/articles/partner-xamarin-notification-hubs-android-get-started/)



## <a name="related-links"></a>Links relacionados

- [GettingStarted (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GettingStarted)
- [GetStartedWithData (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithData)
- [GetStartedWithUsers (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithUsers)
- [GetStartedWithPush (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithPush)
- [ValidateModifyData (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/ValidateModifyData)
- [Hubs de notificação (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs)
- [Exemplo do Azure PCL (por @paulbatum) (amostra)](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [Caminho de aprendizado de aplicativos móveis do Azure](https://azure.microsoft.com/en-us/documentation/learning-paths/appservice-mobileapps/)
