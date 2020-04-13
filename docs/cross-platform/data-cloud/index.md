---
title: Microsoft Azure e Xamarin
description: Este documento vincula-se à documentação sobre serviços conectados no Visual Studio para Mac, Azure Mobile Apps, Active Directory Authentication e WebAPI.
ms.prod: xamarin
ms.assetid: 7b9aa8d9-c181-4c33-8ab0-2f56e4dbfc04
author: davidortinau
ms.author: daortin
ms.date: 10/09/2017
ms.openlocfilehash: 2b6dfeb0de0fac59556280609dbf870c23a9298b
ms.sourcegitcommit: 2a8eb8bce427e72d4e7edd06ce432e19f17dcdd7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388519"
---
# <a name="microsoft-azure-and-xamarin"></a>Microsoft Azure e Xamarin

[![](images/evolve-mikej-azure-sml.png "Azure App Services features are easy to add to Xamarin apps, including cloud data storage and cross-platform push notifications")](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

[Evoluir 2016: Desenvolvimento de aplicativos conectados usando Azure e Xamarin](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

## <a name="connected-services-in-visual-studio-for-mac"></a>Serviços conectados no Visual Studio para Mac

O novo recurso [Connected Services](connected-services.md) do Visual Studio for Mac ajuda os desenvolvedores a adicionar de forma rápida e fácil a funcionalidade do Azure a aplicativos móveis de dentro do IDE. Atualmente disponível para testes no canal Alpha.

## <a name="azure-app-services"></a>Serviços de Aplicativo do Azure

Há uma coleção de documentação do [Azure Mobile Apps](~/cross-platform/data-cloud/mobile-apps.md) que orienta o processo de implementação do Cliente Móvel Do [Azure.](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
A Xamarin também oferece um pacote NuGet de Mensagens Azure para [iOS](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.iOS/) e [Android](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.Android/) para ajudar a implementar notificações push em todas as plataformas.

Configure seus aplicativos no [portal Azure App Services](https://portal.azure.com/) para acessar aplicativos móveis, APIs da Web, armazenamento e muito mais. Saiba [como os serviços de aplicativos são diferentes](https://azure.microsoft.com/updates/whats-new-with-azure-app-service/) e assista [a esses vídeos da Microsoft](https://azure.microsoft.com/campaigns/azure-march-announcement/).

## <a name="active-directory-authentication"></a>Autenticação do ActiveDirectory

[O Azure Active Directory](~/cross-platform/data-cloud/active-directory/index.md) pode ser usado para fazer login com usuários em aplicativos Xamarin. Os aplicativos podem então acessar serviços adicionais como o Office 365.

## <a name="webapi"></a>WebAPI

A API web da Microsoft expõe uma interface semelhante ao REST que pode ser facilmente consumida por aplicativos Xamarin.
Você pode facilmente girar um [site do Azure](https://trywebsites.azurewebsites.net/) e criar um aplicativo baseado na WebAPI para se conectar aos aplicativos Xamarin.

### <a name="introduction-to-web-services"></a>[Introdução aos serviços web](~/cross-platform/data-cloud/web-services/index.md)

Este tutorial apresenta como integrar as tecnologias de serviço web REST, WCF e SOAP com aplicativos móveis Xamarin. Examina várias implementações de serviços, avalia ferramentas e bibliotecas disponíveis para integrá-las e fornece padrões de amostra para o consumo de dados de serviços. Finalmente, ele fornece uma visão geral básica da criação de um serviço web RESTful para consumo com um aplicativo móvel Xamarin.

## <a name="samples"></a>Exemplos

Além das amostras de [documentação,](https://github.com/xamarin/mobile-samples/tree/master/Azure)os seguintes aplicativos completos demonstram vários recursos do Azure incorporados aos aplicativos Xamarin:

- [Sport](https://github.com/xamarin/Sport) – aplicativo amigável de rastreamento da liga esportiva que usa armazenamento de dados & notificações push.
- [Momentos](https://github.com/pierceboggan/Moments) – compartilhamento instantâneo de fotos que usa o Azure Storage para imagens.
- [Xamarin CRM](https://github.com/xamarin/app-crm) – usa API web para o back-end.
- [MyShoppe](https://github.com/jamesmontemagno/MyShoppe) - Azure Mobile Apps.

- [eShop](https://github.com/dotnet-architecture/eShopOnContainers) – amostra para a [série arquitetura](https://www.microsoft.com/net/learn/architecture) de ebooks.
- [MyDriving](https://azure.microsoft.com/campaigns/mydriving/) – Amostra Azure + IoT da Build 2016.

## <a name="related-links"></a>Links relacionados

- [Exemplo azure PCL @paulbatum(por ) (amostra)](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Azure portal](https://azure.microsoft.com/)
- [Cliente móvel para Xamarin (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
