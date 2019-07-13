---
title: Microsoft Azure e o Xamarin
description: Este documento leva a documentação sobre os serviços conectados no Visual Studio para Mac, aplicativos móveis do Azure, autenticação do Active Directory e da API Web.
ms.prod: xamarin
ms.assetid: 7b9aa8d9-c181-4c33-8ab0-2f56e4dbfc04
author: asb3993
ms.author: amburns
ms.date: 10/09/2017
ms.openlocfilehash: 723970a4ad7e2ced85147dbcc6c22f9a45519121
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864609"
---
# <a name="microsoft-azure-and-xamarin"></a>Microsoft Azure e o Xamarin

[![](images/evolve-mikej-azure-sml.png "Recursos de serviços de aplicativos do Azure são fáceis de adicionar a aplicativos do Xamarin, incluindo o armazenamento de dados de nuvem e notificações por push de plataforma cruzada")](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

[Evolve 2016: Desenvolvimento de aplicativos conectados usando o Azure e o Xamarin](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

## <a name="connected-services-in-visual-studio-for-mac"></a>Serviços conectados no Visual Studio para Mac

O novo [serviços conectados](connected-services.md) recurso do Visual Studio para Mac ajuda os desenvolvedores a rápida e facilmente adicionar funcionalidade do Azure para aplicativos móveis de dentro do IDE. Disponível atualmente para o canal alfa de teste.

## <a name="azure-app-services"></a>Serviços de aplicativo do Azure

Há uma coleção de [documentação de aplicativos móveis do Azure](~/cross-platform/data-cloud/mobile-apps.md) que explica o processo de implementação de [Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).
O Xamarin oferece também pacotes do NuGet um sistema de mensagens do Azure para [iOS](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.iOS/) e [Android](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.Android/) para ajudar a implementar notificações por push entre plataformas.

Configurar seus aplicativos na [portal de serviços de aplicativo do Azure](https://portal.azure.com/) para acessar APIs da Web, aplicativos móveis, armazenamento e muito mais. Saiba mais sobre [como os serviços de aplicativos são diferentes](https://azure.microsoft.com/updates/whats-new-with-azure-app-service/) e assista na [esses vídeos da Microsoft](https://azure.microsoft.com/campaigns/azure-march-announcement/).

## <a name="active-directory-authentication"></a>Autenticação do Active Directory

[Do Azure Active Directory](~/cross-platform/data-cloud/active-directory/index.md) pode ser usado para os usuários de logon em aplicativos Xamarin por meio de [AUTH componente](https://www.nuget.org/packages/Xamarin.Auth/).
Os aplicativos podem acessar serviços adicionais como o Office 365.

## <a name="webapi"></a>WebAPI

API da Web da Microsoft expõe uma interface REST que pode ser facilmente consumida por aplicativos do Xamarin.
Você pode facilmente rotação backup de um [site do Azure](https://trywebsites.azurewebsites.net/) e compilar um aplicativo de API da Web para se conectar a aplicativos Xamarin.


### <a name="introduction-to-web-servicescross-platformdata-cloudweb-servicesindexmd"></a>[Introdução aos serviços Web](~/cross-platform/data-cloud/web-services/index.md)

Este tutorial apresenta como integrar o REST, WCF e SOAP web tecnologias de serviço com aplicativos móveis do Xamarin. Examina várias implementações de serviço, avalia as ferramentas disponíveis e bibliotecas para integrá-las e fornece padrões de exemplo para o consumo de dados do serviço. Por fim, ele fornece uma visão geral básica de criação de um serviço web RESTful para consumo com um aplicativo móvel do Xamarin.

## <a name="samples"></a>Exemplos

Além de [exemplos de documentação](https://github.com/xamarin/mobile-samples/tree/master/Azure), os aplicativos completos a seguir demonstram vários recursos do Azure, incorporados a aplicativos do Xamarin:

- [Esporte](https://github.com/xamarin/Sport) – amigável liga esportiva-aplicativo de acompanhamento que usa notificações por push e de armazenamento de dados.
- [Momentos](https://github.com/pierceboggan/Moments) – foto instantânea, compartilhamento que usa o armazenamento do Azure para imagens.
- [Xamarin CRM](https://github.com/xamarin/app-crm) – usa a API da Web para o back-end.
- [MyShoppe](https://github.com/jamesmontemagno/MyShoppe) -aplicativos móveis do Azure.

- [eShop](https://github.com/dotnet-architecture/eShopOnContainers) – exemplo para o [Architecture series](https://www.microsoft.com/net/learn/architecture) de livros eletrônicos.
- [MyDriving](https://azure.microsoft.com/campaigns/mydriving/) – Azure + IoT de exemplo do Build 2016.


## <a name="related-links"></a>Links relacionados

- [Exemplo de PCL do Azure (por @paulbatum) (amostra)](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Portal do Azure](https://azure.microsoft.com/)
- [Cliente móvel para Xamarin (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
