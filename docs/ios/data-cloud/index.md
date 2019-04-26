---
title: Dados e serviços de nuvem em aplicativos xamarin. IOS
description: Este documento leva a guias que descrevem como trabalhar com dados locais, iCloud e CloudKit em um aplicativo xamarin. IOS.
ms.prod: xamarin
ms.assetid: 945719F7-7CE6-4207-BF0F-23195125FC84
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/13/2017
ms.openlocfilehash: 21a6c1c0c0ceb5596a056f0818dec39041808504
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61218239"
---
# <a name="data-and-cloud-services-in-xamarinios-apps"></a>Dados e serviços de nuvem em aplicativos xamarin. IOS

##  <a name="data-accessiosdata-clouddataindexmd"></a>[Acesso a dados](~/ios/data-cloud/data/index.md)

A maioria dos aplicativos tem algum requisito para salvar os dados no dispositivo localmente. A menos que a quantidade de dados for muito pequena, isso normalmente requer um banco de dados e uma camada de dados no aplicativo para gerenciar o acesso ao banco de dados. iOS tem o mecanismo de banco de dados Sqlite "incorporado" e o acesso aos dados é simplificado pela plataforma do Xamarin que vem com o provedor de dados SQLite.

##  <a name="icloudiosdata-cloudintroduction-to-icloudmd"></a>[iCloud](~/ios/data-cloud/introduction-to-icloud.md)

A API de armazenamento no iCloud no iOS 5 permite que os aplicativos salvar documentos do usuário e dados específicos do aplicativo em um local central e acessar esses itens em todos os dispositivos do usuário.

##  <a name="cloudkitiosdata-cloudintro-to-cloudkitmd"></a>[CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)

A estrutura de CloudKit simplifica o desenvolvimento de aplicativos do iCloud esse acesso. Isso inclui a recuperação de dados de aplicativo e direitos de ativo, bem como sendo capaz de armazenar com segurança informações do aplicativo. Esse kit oferece aos usuários uma camada de anonimato, permitindo o acesso a aplicativos com sua IDs do iCloud sem compartilhar informações pessoais.