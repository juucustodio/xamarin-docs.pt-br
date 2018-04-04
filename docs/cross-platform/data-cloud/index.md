---
title: Microsoft Azure
description: Downloads de documentação e código de exemplo para o Azure.
ms.prod: xamarin
ms.assetid: 7b9aa8d9-c181-4c33-8ab0-2f56e4dbfc04
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/09/2017
ms.openlocfilehash: 13868864813a2f120857fb6347924197e374638c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="microsoft-azure"></a>Microsoft Azure

_Downloads de documentação e código de exemplo para o Azure._

[ ![](images/evolve-mikej-azure-sml.png "Recursos de serviços de aplicativos do Azure são fáceis de adicionar a aplicativos Xamarin, incluindo o armazenamento de dados de nuvem e notificações por push da plataforma cruzada")](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

[Evoluir 2016: Desenvolvendo aplicativos conectados usando o Azure e Xamarin](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

## <a name="connected-services-in-visual-studio-for-mac"></a>Conectado serviços no Visual Studio para Mac

O novo [serviços conectados](connected-services.md) recurso do Visual Studio para Mac ajuda os desenvolvedores de forma rápida e fácil adicionar funcionalidade do Azure para aplicativos móveis de dentro do IDE. Atualmente disponíveis para testes no canal alfa.


## <a name="azure-app-services"></a>Serviços de aplicativos do Azure

Há um conjunto de [documentação de aplicativos do Azure Mobile](~/cross-platform/data-cloud/mobile-apps.md) que orienta você durante o processo de implementação de [de cliente móvel do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).
Xamarin também oferece pacotes do NuGet um sistema de mensagens do Azure para [iOS](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.iOS/) e [Android](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.Android/) para ajudar a implementar notificações por push entre plataformas.

Configurar seus aplicativos no [portal dos serviços de aplicativo do Azure](https://portal.azure.com/) para acessar APIs da Web, aplicativos móveis, armazenamento e muito mais. Saiba mais sobre [como serviços de aplicativos são diferentes](http://azure.microsoft.com/en-us/updates/whats-new-with-azure-app-service/) e observe em [esses vídeos da Microsoft](http://azure.microsoft.com/en-us/campaigns/azure-march-announcement/).

## <a name="active-directory-authentication"></a>Autenticação do Active Directory

[Active Directory do Azure](~/cross-platform/data-cloud/active-directory/index.md) pode ser usado para os usuários de logon em aplicativos Xamarin por meio de [Xamarin.Auth componente](https://www.nuget.org/packages/Xamarin.Auth/).
Os aplicativos podem acessar serviços adicionais, como o Office 365.

## <a name="webapi"></a>WebAPI

Web API da Microsoft expõe uma interface REST que pode ser facilmente consumida por aplicativos Xamarin.
Você pode facilmente rotação backup de um [site do Azure](https://trywebsites.azurewebsites.net/) e criar um aplicativo baseado em WebAPI para se conectar a aplicativos Xamarin.


###  <a name="introduction-to-web-servicescross-platformdata-cloudweb-servicesindexmd"></a>[Introdução aos serviços Web](~/cross-platform/data-cloud/web-services/index.md)

Este tutorial apresenta como integrar o REST, SOAP e o WCF web tecnologias de serviço com aplicativos móveis do Xamarin. Examina várias implementações de serviço, avalia as ferramentas disponíveis e bibliotecas para integrá-los e fornece padrões de exemplo para consumir os dados de serviço. Por fim, ele fornece uma visão geral básica da criação de um serviço web RESTful para consumo com um aplicativo móvel do Xamarin.

## <a name="samples"></a>Exemplos

Além de [exemplos de documentação](https://github.com/xamarin/mobile-samples/tree/master/Azure), os seguintes aplicativos completos demonstram vários recursos do Azure, incorporados em aplicativos Xamarin:

- [Esporte](https://github.com/xamarin/Sport) – liga de esportes amigável controle de aplicativo que usa notificações de envio e de armazenamento de dados.
- [Momentos](https://github.com/pierceboggan/Moments) – foto instantânea compartilhamento que usa o armazenamento do Azure para imagens.
- [Xamarin CRM](https://github.com/xamarin/app-crm) – usa a API da Web para o back-end.
- [MyShoppe](https://github.com/jamesmontemagno/MyShoppe) -aplicativos móveis do Azure.

- [eShop](https://github.com/dotnet-architecture/eShopOnContainers) – exemplo para o [arquitetura série](https://www.microsoft.com/net/learn/architecture) de livros eletrônicos.
- [MyDriving](https://azure.microsoft.com/en-us/campaigns/mydriving/) – Azure + IoT amostras da compilação 2016.


## <a name="related-links"></a>Links relacionados

- [Exemplo do Azure PCL (por @paulbatum) (amostra)](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Portal do Azure](http://azure.microsoft.com/)
- [Cliente móvel para Xamarin (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
