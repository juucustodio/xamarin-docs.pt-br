---
title: Acesso a dados do Xamarin. Android
description: A maioria dos aplicativos tem algum requisito para salvar dados no dispositivo localmente. A menos que a quantidade de dados seja trivialmente pequena, isso geralmente requer um banco de dados e uma camada de dados no aplicativo para gerenciar o acesso ao banco de dado.  O Android tem o mecanismo de banco de dados SQLite ' interno ' e o acesso a armazenamento e recuperação de dados é simplificado pela plataforma do Xamarin. Este documento mostra como acessar um banco de dados SQLite em uma forma de plataforma cruzada.
ms.prod: xamarin
ms.assetid: 6B47E864-C6E7-4AA2-8DEF-2C8BF551D17C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 2343603199661ea39b1f0af172ce0ccf48a2cd66
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754584"
---
# <a name="xamarinandroid-data-access"></a>Acesso a dados do Xamarin. Android

_A maioria dos aplicativos tem algum requisito para salvar dados no dispositivo localmente. A menos que a quantidade de dados seja trivialmente pequena, isso geralmente requer um banco de dados e uma camada de dados no aplicativo para gerenciar o acesso ao banco de dado.  O Android tem o mecanismo de banco de dados SQLite ' interno ' e o acesso a armazenamento e recuperação de dados é simplificado pela plataforma do Xamarin. Este documento mostra como acessar um banco de dados SQLite em uma forma de plataforma cruzada._

## <a name="data-access-overview"></a>Visão geral de acesso a dados

A maioria dos aplicativos tem algum requisito para salvar dados no dispositivo localmente. A menos que a quantidade de dados seja trivialmente pequena, isso geralmente requer um banco de dados e uma camada de dados no aplicativo para gerenciar o acesso ao banco de dado. O Android tem o mecanismo de banco de dados SQLite "interno" e o acesso aos dados é simplificado pela plataforma Xamarin, que vem com o SQLite Provedor de Dados.

O Xamarin. Android dá suporte a APIs de acesso ao banco de dados, como:

- Estrutura ADO.NET.
- Biblioteca SQLite-NET de terceiros.

A maior parte do código nesta seção é completamente entre plataformas e será executada no iOS ou Android sem modificação. Há dois aplicativos de exemplo discutidos:

- [**DataAccess_Basic**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) &ndash; Operações de dados simples gravam os resultados em um controle de exibição de texto;

- [**DataAccess_Advanced**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) &ndash; Integra operações de dados em um pequeno aplicativo de trabalho que lista e edita uma estrutura de dados simples.

Ambas as soluções de exemplo contêm projetos de aplicativo de exemplo iOS e Android.

Para aplicativos Xamarin. Forms, leia [trabalhando com bancos de dados](~/xamarin-forms/data-cloud/data/databases.md) que explica como trabalhar com o SQLite em uma biblioteca PCL com Xamarin. Forms.

Os tópicos nesta seção discutem o acesso a dados no Xamarin. Android usando o SQLite como o mecanismo de banco de dados. O banco de dados pode ser acessado "diretamente" usando a sintaxe ADO.NET ou você pode incluir o ORM SQLite.NET e C#executar operações de dados no.

Dois exemplos são revisados: um que contém código de acesso a dados muito simples que gera um campo de texto e um aplicativo simples que inclui a funcionalidade criar, ler, atualizar e excluir. O threading e como propagar seu aplicativo com um banco de dados SQLite previamente preenchido também é discutido.

Para obter exemplos adicionais de acesso a dados entre plataformas, consulte nosso estudo de caso do [profissional de tarefas](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) .

## <a name="related-links"></a>Links relacionados

- [DataAccess básico (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançado (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Receitas de dados do Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Acesso a dados do Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
