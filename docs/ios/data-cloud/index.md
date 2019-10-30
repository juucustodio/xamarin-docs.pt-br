---
title: Dados e serviços de nuvem em aplicativos Xamarin. iOS
description: Este documento contém links para guias que descrevem como trabalhar com dados locais, iCloud e CloudKit em um aplicativo Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 945719F7-7CE6-4207-BF0F-23195125FC84
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/13/2017
ms.openlocfilehash: 4b3bf1fcc6be7713c87b16ef9ec083189793b324
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73008063"
---
# <a name="data-and-cloud-services-in-xamarinios-apps"></a>Dados e serviços de nuvem em aplicativos Xamarin. iOS

## <a name="data-accessiosdata-clouddataindexmd"></a>[Acesso a dados](~/ios/data-cloud/data/index.md)

A maioria dos aplicativos tem algum requisito para salvar dados no dispositivo localmente. A menos que a quantidade de dados seja trivialmente pequena, isso geralmente requer um banco de dados e uma camada de dados no aplicativo para gerenciar o acesso ao banco de dado. o iOS tem o mecanismo de banco de dados SQLite "interno" e o acesso aos dados é simplificado pela plataforma Xamarin, que vem com o SQLite Provedor de Dados.

## <a name="icloudiosdata-cloudintroduction-to-icloudmd"></a>[iCloud](~/ios/data-cloud/introduction-to-icloud.md)

A API de armazenamento do iCloud no iOS 5 permite que os aplicativos salvem os documentos do usuário e os dados específicos do aplicativo em um local central e acessem esses itens de todos os dispositivos do usuário.

## <a name="cloudkitiosdata-cloudintro-to-cloudkitmd"></a>[CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)

A estrutura CloudKit simplifica o desenvolvimento de aplicativos que acessam o iCloud. Isso inclui a recuperação de dados de aplicativos e direitos de ativos, bem como a capacidade de armazenar informações de aplicativos com segurança. Esse kit oferece aos usuários uma camada de anonimato, permitindo o acesso a aplicativos com suas IDs do iCloud sem compartilhar informações pessoais.
