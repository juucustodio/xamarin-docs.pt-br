---
title: "Dados e serviços de nuvem"
description: "Estabilização e guias de implantação"
ms.topic: article
ms.prod: xamarin
ms.assetid: 92B35AB1-7AB7-3D3B-DB31-CC971E0B43AE
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/13/2017
ms.openlocfilehash: 5814936289164f14a07b33c219ad1fd01b00473b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="data-and-cloud-services"></a>Dados e serviços de nuvem


##  <a name="data-accessiosdata-clouddataindexmd"></a>[Acesso a dados](~/ios/data-cloud/data/index.md)

A maioria dos aplicativos têm alguns requisitos para salvar dados no dispositivo local. A menos que a quantidade de dados é muito pequena, isso geralmente exige um banco de dados e uma camada de dados do aplicativo para gerenciar o acesso ao banco de dados. iOS tem o mecanismo de banco de dados Sqlite "interno" e o acesso aos dados é simplificado pela plataforma do Xamarin que vem com o provedor de dados SQLite.

##  <a name="icloudiosdata-cloudintroduction-to-icloudmd"></a>[iCloud](~/ios/data-cloud/introduction-to-icloud.md)

A API de armazenamento no iCloud no iOS 5 permite que aplicativos salvar os documentos do usuário e dados específicos do aplicativo para um local central e acessar esses itens em todos os dispositivos do usuário.

##  <a name="cloudkitiosdata-cloudintro-to-cloudkitmd"></a>[CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)

A estrutura CloudKit simplifica o desenvolvimento de aplicativos que iCloud de acesso. Isso inclui a recuperação de dados de aplicativo e direitos de ativo e ser capaz de armazenar com segurança informações do aplicativo. Esse kit oferece aos usuários uma camada de anonimato permitindo o acesso a aplicativos com sua IDs de iCloud sem compartilhar informações pessoais.