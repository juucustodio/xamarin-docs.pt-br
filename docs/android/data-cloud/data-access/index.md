---
title: Acesso a dados do xamarin. Android
description: A maioria dos aplicativos tem algum requisito para salvar os dados no dispositivo localmente. A menos que a quantidade de dados for muito pequena, isso normalmente requer um banco de dados e uma camada de dados no aplicativo para gerenciar o acesso ao banco de dados.  O Android tem o mecanismo de banco de dados SQLite 'interno' e acesso para armazenar e recuperar dados é simplificado pela plataforma do Xamarin. Este documento mostra como acessar um banco de dados SQLite de uma maneira de plataforma cruzada.
ms.prod: xamarin
ms.assetid: 6B47E864-C6E7-4AA2-8DEF-2C8BF551D17C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 08720734de73af12d8a7383fa7d523dc350c4462
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674738"
---
# <a name="xamarinandroid-data-access"></a>Acesso a dados do xamarin. Android

_A maioria dos aplicativos tem algum requisito para salvar os dados no dispositivo localmente. A menos que a quantidade de dados for muito pequena, isso normalmente requer um banco de dados e uma camada de dados no aplicativo para gerenciar o acesso ao banco de dados.  O Android tem o mecanismo de banco de dados SQLite 'interno' e acesso para armazenar e recuperar dados é simplificado pela plataforma do Xamarin. Este documento mostra como acessar um banco de dados SQLite de uma maneira de plataforma cruzada._

## <a name="data-access-overview"></a>Visão geral do acesso de dados

A maioria dos aplicativos tem algum requisito para salvar os dados no dispositivo localmente. A menos que a quantidade de dados for muito pequena, isso normalmente requer um banco de dados e uma camada de dados no aplicativo para gerenciar o acesso ao banco de dados. Ambos os Android tem o mecanismo de banco de dados SQLite "incorporado" e acesso aos dados é simplificado pela plataforma do Xamarin que vem com o provedor de dados SQLite.

Xamarin. Android dão suporte a APIs de acesso a banco de dados como:

- Framework do ADO.NET.
- Biblioteca de terceiros 3º SQLite-NET.

A maioria do código desta seção é completamente entre plataforma e serão executados no iOS ou Android sem modificação. Há dois aplicativos de exemplo discutidos:

- [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) &ndash; operações de dados simples que grava os resultados para um texto exibirem o controle;

- [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) &ndash; integra as operações de dados em um pequeno aplicativo de trabalho que lista e edita uma estrutura de dados simples.

Exemplos de ambas as soluções contêm projetos de aplicativos de exemplo do Android e iOS.

Para aplicativos xamarin. Forms, leia [trabalhando com bancos de dados](~/xamarin-forms/app-fundamentals/databases.md) que explica como trabalhar com o SQLite em uma biblioteca PCL com xamarin. Forms.

Os tópicos nesta seção abordam o acesso a dados no xamarin. Android usando o SQLite como o mecanismo de banco de dados. O banco de dados pode ser acessado "diretamente", usando a sintaxe ADO.NET ou você pode incluir SQLite.NET ORM e executar operações de dados em c#.

Dois exemplos são revisados: uma que contém o código de acesso de dados muito simples que saídas para um campo de texto e um aplicativo simples que inclui criar, ler, atualizar e excluir a funcionalidade. Threading e como semear seu aplicativo com um banco de dados SQLite previamente preenchido também é discutido.

Para obter exemplos adicionais de acesso a dados de plataforma cruzada, consulte nosso [Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) estudo de caso.


## <a name="related-links"></a>Links relacionados

- [DataAccess Basic (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançadas (amostra)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Receitas de dados do Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Acesso a dados do xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
