---
title: Autenticar o acesso aos serviços Web
description: Este guia explica como integrar serviços de autenticação em um aplicativo xamarin. Forms para permitir que os usuários compartilhem um back-end tendo apenas o acesso a seus próprios dados.
ms.prod: xamarin
ms.assetid: E6FCFAE1-4F83-4F93-9190-EC5290360C54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: d598a9b3de31ea6823530f911c3544bf3cebb37f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331176"
---
# <a name="authenticating-access-to-web-services"></a>Autenticar o acesso aos serviços Web

_Este guia explica como integrar serviços de autenticação em um aplicativo xamarin. Forms para permitir que os usuários compartilhem um back-end tendo apenas o acesso a seus próprios dados. Os tópicos abordados incluem usando a autenticação básica com um serviço REST, usando o componente de AUTH para autenticar em relação a provedores de identidade OAuth, e usando os mecanismos de autenticação interna oferecidos por diferentes provedores._

## <a name="authenticating-a-restful-web-servicerestmd"></a>[Autenticar um serviço Web RESTful](rest.md)

HTTP oferece suporte ao uso de vários mecanismos de autenticação para controlar o acesso aos recursos. Autenticação básica oferece acesso a recursos somente a clientes que possuem as credenciais corretas. Este artigo demonstra como usar a autenticação básica para proteger o acesso aos recursos do serviço web RESTful.

## <a name="authenticating-users-with-an-identity-provideroauthmd"></a>[Autenticar usuários com um provedor de identidade](oauth.md)

AUTH é um SDK de plataforma cruzada para autenticar usuários e armazenar suas contas. Ele inclui os autenticadores de OAuth que oferecem suporte para o consumo de provedores de identidade, como Google, Microsoft, Facebook e Twitter. Este artigo explica como usar auth para gerenciar o processo de autenticação em um aplicativo xamarin. Forms.

## <a name="authenticating-users-with-azure-mobile-appsazuremd"></a>[Autenticar usuários com aplicativos móveis do Azure](azure.md)

Aplicativos móveis do Azure usam uma variedade de provedores de identidade externos para dar suporte à autenticação e autorização de usuários do aplicativo. Em seguida, permissões podem ser definidas em tabelas para restringir o acesso somente para usuários autenticados. Este artigo explica como usar aplicativos móveis do Azure para gerenciar o processo de autenticação em um aplicativo xamarin. Forms.

## <a name="authenticating-users-with-azure-active-directory-b2cazure-ad-b2cmd"></a>[Autenticar usuários com o Azure Active Directory B2C](azure-ad-b2c.md)

Azure Active Directory B2C do diretório é uma solução de gerenciamento de identidade de nuvem para aplicativos móveis e web voltados ao consumidor. Este artigo demonstra como usar a biblioteca de autenticação da Microsoft (MSAL) e o Azure Active Directory B2C para integrar o gerenciamento de identidade do consumidor em um aplicativo xamarin. Forms.

## <a name="integrating-azure-active-directory-b2c-with-azure-mobile-appsazure-ad-b2c-mobile-appmd"></a>[Integração do Azure Active Directory B2C com Aplicativos Móveis do Azure](azure-ad-b2c-mobile-app.md)

Azure Active Directory B2C do diretório pode ser usado para gerenciar o fluxo de trabalho de autenticação para aplicativos móveis do Azure. Com essa abordagem, a experiência de gerenciamento de identidade está totalmente definida na nuvem e pode ser modificada sem alterar o código do aplicativo móvel. Este artigo demonstra como usar o Azure Active Directory B2C para fornecer autenticação e autorização para uma instância de aplicativos móveis do Azure com o xamarin. Forms.

## <a name="related-links"></a>Links relacionados

- [Introdução aos serviços Web](~/cross-platform/data-cloud/web-services/index.md)
- [Visão geral do suporte assíncrono](~/cross-platform/platform/async.md)
