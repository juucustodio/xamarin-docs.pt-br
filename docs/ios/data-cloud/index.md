---
title: Dados e serviços de nuvem em aplicativos Xamarin. iOS
description: Este documento contém links para guias que descrevem como trabalhar com dados locais, iCloud e CloudKit em um aplicativo Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 945719F7-7CE6-4207-BF0F-23195125FC84
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/13/2017
ms.openlocfilehash: e9f910804f3da9d173206d51c59e1d7ddd9b90a6
ms.sourcegitcommit: bad1ab3f78d7f94d48511666626b54f8ba155689
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663506"
---
# <a name="data-and-cloud-services-in-xamarinios-apps"></a>Dados e serviços de nuvem em aplicativos Xamarin. iOS

## <a name="data-accessiosdata-clouddataindexmd"></a>[Acesso a dados](~/ios/data-cloud/data/index.md)

A maioria dos aplicativos tem algum requisito para salvar dados no dispositivo localmente. A menos que a quantidade de dados seja trivialmente pequena, isso geralmente requer um banco de dados e uma camada de dados no aplicativo para gerenciar o acesso ao banco de dado. o iOS tem o mecanismo de banco de dados SQLite "interno" e o acesso aos dados é simplificado pela plataforma Xamarin, que vem com o SQLite Provedor de Dados.

## <a name="icloudiosdata-cloudintroduction-to-icloudmd"></a>[iCloud](~/ios/data-cloud/introduction-to-icloud.md)

A API de armazenamento do iCloud permite que os aplicativos salvem documentos do usuário e dados específicos do aplicativo em um local central e acessem esses itens de todos os dispositivos do usuário.

## <a name="cloudkitiosdata-cloudintro-to-cloudkitmd"></a>[CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)

A estrutura CloudKit simplifica o desenvolvimento de aplicativos que acessam o iCloud. Isso inclui a recuperação de dados de aplicativos e direitos de ativos, bem como a capacidade de armazenar informações de aplicativos com segurança. Esse kit oferece aos usuários uma camada de anonimato, permitindo o acesso a aplicativos com suas IDs do iCloud sem compartilhar informações pessoais.
