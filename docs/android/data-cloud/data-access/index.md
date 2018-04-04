---
title: Acesso a dados do xamarin
description: A maioria dos aplicativos têm alguns requisitos para salvar dados no dispositivo local. A menos que a quantidade de dados é muito pequena, isso geralmente exige um banco de dados e uma camada de dados do aplicativo para gerenciar o acesso ao banco de dados.  Android tem o mecanismo de banco de dados SQLite 'interno' e acesso para armazenar e recuperar dados é simplificado pela plataforma do Xamarin. Este documento mostra como acessar um banco de dados SQLite de uma maneira de plataforma cruzada.
ms.prod: xamarin
ms.assetid: 6B47E864-C6E7-4AA2-8DEF-2C8BF551D17C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 508a8f54723bfdd30b1c8ea8d4b6c1d422ae24e9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinandroid-data-access"></a>Acesso a dados do xamarin

_A maioria dos aplicativos têm alguns requisitos para salvar dados no dispositivo local. A menos que a quantidade de dados é muito pequena, isso geralmente exige um banco de dados e uma camada de dados do aplicativo para gerenciar o acesso ao banco de dados.  Android tem o mecanismo de banco de dados SQLite 'interno' e acesso para armazenar e recuperar dados é simplificado pela plataforma do Xamarin. Este documento mostra como acessar um banco de dados SQLite de uma maneira de plataforma cruzada._

## <a name="data-access-overview"></a>Visão geral do acesso de dados

A maioria dos aplicativos têm alguns requisitos para salvar dados no dispositivo local. A menos que a quantidade de dados é muito pequena, isso geralmente exige um banco de dados e uma camada de dados do aplicativo para gerenciar o acesso ao banco de dados. Android ambos tem o mecanismo de banco de dados Sqlite "interno" e acesso aos dados é simplificado pela plataforma do Xamarin que vem com o provedor de dados SQLite.

Xamarin dão suporte às APIs de acesso a banco de dados como:

-  Estrutura do ADO.NET.
-  Biblioteca de terceiros 3º SQLite-NET.

A maior parte do código nesta seção é completamente entre plataformas e serão executados em iOS ou Android sem modificação. Há dois aplicativos de exemplo discutidos:

-  [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) &ndash; operações de dados simples grava os resultados para um texto exibirem controle;

-  [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) &ndash; integra as operações de dados em um pequeno aplicativo de trabalho que lista e edita uma estrutura de dados simples.

Ambas as soluções de exemplo contém iOS e projetos de aplicativo de exemplo do Android.

Para aplicativos xamarin. Forms, leia [trabalhando com bancos de dados](~/xamarin-forms/app-fundamentals/databases.md) que explica como trabalhar com SQLite em uma biblioteca PCL com xamarin. Forms.

Os tópicos nesta seção abordam o acesso a dados em xamarin usando SQLite como o mecanismo de banco de dados. O banco de dados pode ser acessado "diretamente" usando a sintaxe do ADO.NET, ou você pode incluir o ORM SQLite.NET e executar operações de dados em c#.

Dois exemplos sejam revisados: uma que contém o código de acesso de dados muito simples que saídas para um campo de texto e um aplicativo simples que inclui criar, ler, atualizar e excluir a funcionalidade. Threading e como a propagação de seu aplicativo com um banco de dados SQLite preenchido previamente também é abordado.

Para obter exemplos adicionais de acesso a dados de várias plataformas, consulte nosso [Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) estudo de caso.


## <a name="related-links"></a>Links relacionados

- [DataAccess Basic (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançados (amostra)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Dados do Android receitas](https://developer.xamarin.com/recipes/android/data/)
- [Acesso a dados xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
