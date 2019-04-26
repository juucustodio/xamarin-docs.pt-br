---
title: Acesso a dados do xamarin. IOS
description: Este documento leva a guias que descrevem como trabalhar com bancos de dados locais em um aplicativo xamarin. IOS. Conteúdo vinculado discute SQLite.NET, ADO.NET e muito mais.
ms.prod: xamarin
ms.assetid: 3AEDFD8D-FB10-4CEF-BE04-CCD14E95F02C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/11/2016
ms.openlocfilehash: 8d2513ba1c2ae2769e81659c98f3897f33d83fbf
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61218032"
---
# <a name="xamarinios-data-access"></a>Acesso a dados do xamarin. IOS

Xamarin. IOS dá suporte a APIs de acesso a banco de dados, como:

-  Framework do ADO.NET.
-  Biblioteca de terceiros 3º SQLite-NET.

Este guia fornece uma visão geral de bancos de dados em geral, antes de descrever como configurar o ADO.NET e SQLite.NET para acessar bancos de dados SQLite em seus aplicativos xamarin. IOS. 

A maioria do código neste documento é completamente entre plataforma e serão executados no iOS ou Android sem modificação. Há dois aplicativos de exemplo discutidos:

-  [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) – operações de dados simples que grava os resultados para um texto exibirem o controle;
-  [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) – integra as operações de dados em um pequeno aplicativo de trabalho que lista e edita uma estrutura de dados simples.

Exemplos de ambas as soluções contêm projetos de aplicativos de exemplo do Android e iOS.

Para aplicativos xamarin. Forms, leia [trabalhando com bancos de dados](~/xamarin-forms/app-fundamentals/databases.md) que explica como trabalhar com o SQLite em uma biblioteca PCL com xamarin. Forms.

## <a name="sections"></a>Seções

-  [Introdução](introduction.md)
-  [Configuração](configuration.md)
-  [Usar SQLite.NET ORM](using-sqlite-orm.md)
-  [Usar ADO.NET](using-adonet.md)
-  [Usar dados em um aplicativo](using-data-in-an-app.md)

## <a name="summary"></a>Resumo

Este capítulo discute o acesso a dados no xamarin. IOS usando o SQLite como o mecanismo de banco de dados. O banco de dados pode ser acessado "diretamente" usando a sintaxe ADO.NET ou você pode incluir SQLite.NET ORM e executar operações de dados em c#.

Examinamos duas amostras: uma que contém o código de acesso de dados muito simples que saídas para um campo de texto e um aplicativo simples que inclui criar, ler, atualizar e excluir a funcionalidade. Também abordamos o threading e como semear seu aplicativo com um banco de dados SQLite previamente preenchido.

Para obter exemplos adicionais de acesso a dados de plataforma cruzada, consulte nosso [Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) estudo de caso.

## <a name="related-links"></a>Links relacionados

- [DataAccess Basic (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançadas (amostra)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS receitas de dados](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Acesso a dados do xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
