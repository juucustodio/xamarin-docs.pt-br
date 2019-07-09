---
title: Autenticação de serviço Web do xamarin. Forms
description: Este guia explica como integrar serviços de autenticação em um aplicativo xamarin. Forms para permitir que os usuários compartilhem um back-end tendo apenas o acesso a seus próprios dados.
ms.prod: xamarin
ms.assetid: E6FCFAE1-4F83-4F93-9190-EC5290360C54
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2019
ms.openlocfilehash: a36dfba7aa07de1633ca9620674ddb4887902ba9
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650418"
---
# <a name="xamarinforms-web-service-authentication"></a>Autenticação de serviço Web do xamarin. Forms

## <a name="authenticate-a-restful-web-servicerestmd"></a>[Autenticar um serviço Web RESTful](rest.md)

HTTP oferece suporte ao uso de vários mecanismos de autenticação para controlar o acesso aos recursos. Autenticação básica oferece acesso a recursos somente a clientes que possuem as credenciais corretas. Este artigo explica como usar a autenticação básica para proteger o acesso aos recursos do serviço web RESTful.

## <a name="authenticate-users-with-an-identity-provideroauthmd"></a>[Autenticar usuários com um provedor de identidade](oauth.md)

AUTH é um SDK de plataforma cruzada para autenticar usuários e armazenar suas contas. Ele inclui os autenticadores de OAuth que oferecem suporte para o consumo de provedores de identidade, como Google, Microsoft, Facebook e Twitter. Este artigo explica como usar auth para gerenciar o processo de autenticação em um aplicativo xamarin. Forms.

## <a name="authenticate-users-with-azure-active-directory-b2cazure-ad-b2cmd"></a>[Autenticar usuários com o Azure Active Directory B2C](azure-ad-b2c.md)

Azure Active Directory B2C do diretório é uma solução de gerenciamento de identidade de nuvem para aplicativos móveis e web voltados ao consumidor. Este artigo explica como usar a biblioteca de autenticação da Microsoft (MSAL) e o Azure Active Directory B2C para integrar o gerenciamento de identidade do consumidor em um aplicativo xamarin. Forms.

## <a name="authenticate-users-with-an-azure-cosmos-db-document-database-and-xamarinformsazure-cosmosdb-authmd"></a>[Autenticar usuários com um banco de dados de documentos do Azure Cosmos DB e o xamarin. Forms](azure-cosmosdb-auth.md)

Bancos de dados de documento do Cosmos DB do Azure dão suporte a coleções particionadas, o que podem abranger vários servidores e partições, e dar suporte a armazenamento ilimitado e taxa de transferência. Este artigo explica como combinar o controle de acesso com coleções particionadas, para que um usuário só pode acessar seus próprios documentos em um aplicativo xamarin. Forms.
