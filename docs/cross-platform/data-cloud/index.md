---
title: Microsoft Azure e Xamarin
description: Este documento contém links para a documentação sobre os serviços conectados no Visual Studio para Mac, os aplicativos móveis do Azure, a autenticação Active Directory e o WebAPI.
ms.prod: xamarin
ms.assetid: 7b9aa8d9-c181-4c33-8ab0-2f56e4dbfc04
author: davidortinau
ms.author: daortin
ms.date: 10/09/2017
ms.openlocfilehash: 441ac213531c84aa3b7edfd6dfabab3f2c37d15d
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86934778"
---
# <a name="microsoft-azure-and-xamarin"></a>Microsoft Azure e Xamarin

[![Os recursos dos serviços Azure Apps são fáceis de adicionar a aplicativos Xamarin, incluindo armazenamento de dados de nuvem e notificações por push de plataforma cruzada](images/evolve-mikej-azure-sml.png)](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

[Desenvolver 2016: desenvolver aplicativos conectados usando o Azure e o Xamarin](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

## <a name="connected-services-in-visual-studio-for-mac"></a>Serviços conectados no Visual Studio para Mac

O novo recurso [Serviços conectados](connected-services.md) do Visual Studio para Mac ajuda os desenvolvedores a adicionar com rapidez e facilidade a funcionalidade do Azure a aplicativos móveis de dentro do IDE. Disponível no momento para teste no canal alfa.

## <a name="azure-app-services"></a>Serviços de Aplicativo do Azure

Há uma coleção de [documentação dos aplicativos móveis do Azure](~/cross-platform/data-cloud/mobile-apps.md) que orienta você pelo processo de implementação do [cliente móvel do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).
O Xamarin também oferece pacotes NuGet de mensagens do Azure para [Ios](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.iOS/) e [Android](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.Android/) para ajudar a implementar notificações por push entre plataformas.

Configure seus aplicativos no [portal de serviços de Azure app](https://portal.azure.com/) para acessar aplicativos móveis, APIs da Web, armazenamento e muito mais. Saiba mais sobre [como os serviços de aplicativos são diferentes](https://azure.microsoft.com/updates/whats-new-with-azure-app-service/) e assista a [esses vídeos da Microsoft](https://azure.microsoft.com/campaigns/azure-march-announcement/).

## <a name="active-directory-authentication"></a>Autenticação do ActiveDirectory

[Azure Active Directory](~/cross-platform/data-cloud/active-directory/index.md) pode ser usado para fazer logon de usuários em aplicativos Xamarin. Os aplicativos podem então acessar serviços adicionais como o Office 365.

## <a name="webapi"></a>WebAPI

A API Web da Microsoft expõe uma interface do tipo REST que pode ser facilmente consumida por aplicativos Xamarin.
Você pode facilmente girar um [site do Azure](https://trywebsites.azurewebsites.net/) e criar um aplicativo baseado em WebAPI para se conectar a aplicativos Xamarin.

### <a name="introduction-to-web-services"></a>[Introdução aos serviços Web](~/cross-platform/data-cloud/web-services/index.md)

Este tutorial apresenta como integrar tecnologias REST, WCF e serviço Web SOAP com aplicativos móveis Xamarin. Ele examina várias implementações de serviço, avalia as ferramentas e bibliotecas disponíveis para integrá-las e fornece padrões de exemplo para o consumo de dados de serviço. Por fim, ele fornece uma visão geral básica da criação de um serviço Web RESTful para consumo com um aplicativo do Xamarin Mobile.

## <a name="samples"></a>Amostras

Além dos exemplos de [documentação](https://github.com/xamarin/mobile-samples/tree/master/Azure), os seguintes aplicativos completos demonstram vários recursos do Azure incorporados aos aplicativos Xamarin:

- [Esporte](https://github.com/xamarin/Sport) – aplicativo de acompanhamento de liga de esportes amigável que usa o armazenamento de dados & notificações por push.
- [Minutos](https://github.com/pierceboggan/Moments) – compartilhamento de fotos instantâneas que usa o armazenamento do Azure para imagens.
- [XAMARIN CRM](https://github.com/xamarin/app-crm) – usa a API da Web para o back-end.
- [MyShoppe](https://github.com/jamesmontemagno/MyShoppe) – aplicativos móveis do Azure.

- [eshop](https://github.com/dotnet-architecture/eShopOnContainers) – exemplo para a [série de arquitetura](https://www.microsoft.com/net/learn/architecture) de livros eletrônicos.
- [Mydirigir](https://azure.microsoft.com/campaigns/mydriving/) – exemplo do Azure + IOT do Build 2016.

## <a name="related-links"></a>Links Relacionados

- [Exemplo de PCL do Azure (por @paulbatum ) (exemplo)](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Azure portal](https://azure.microsoft.com/)
- [Cliente móvel para Xamarin (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
