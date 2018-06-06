---
title: Acesso a dados do xamarin
description: Links neste documento em guias que descrevem como trabalhar com bancos de dados locais em um aplicativo xamarin. O conteúdo vinculado discute SQLite.NET, ADO.NET e muito mais.
ms.prod: xamarin
ms.assetid: 3AEDFD8D-FB10-4CEF-BE04-CCD14E95F02C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/11/2016
ms.openlocfilehash: a986ea9931f62497e5a6863c84bd4041983d66d9
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784570"
---
# <a name="xamarinios-data-access"></a>Acesso a dados do xamarin

Xamarin dá suporte a APIs de acesso a banco de dados, como:

-  Estrutura do ADO.NET.
-  Biblioteca de terceiros 3º SQLite-NET.

Este guia fornece uma visão geral de bancos de dados em geral, antes de descrever como configurar SQLite.NET e ADO.NET para acessar bancos de dados SQLite em seus aplicativos xamarin. 

A maior parte do código neste documento é completamente entre plataformas e serão executados em iOS ou Android sem modificação. Há dois aplicativos de exemplo discutidos:

-  [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) – operações de dados simples grava os resultados para um texto exibirem controle;
-  [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) – integra as operações de dados em um pequeno aplicativo de trabalho que lista e edita uma estrutura de dados simples.

Ambas as soluções de exemplo contém iOS e projetos de aplicativo de exemplo do Android.

Para aplicativos xamarin. Forms, leia [trabalhando com bancos de dados](~/xamarin-forms/app-fundamentals/databases.md) que explica como trabalhar com SQLite em uma biblioteca PCL com xamarin. Forms.

## <a name="sections"></a>Seções

-  [Introdução](introduction.md)
-  [Configuração](configuration.md)
-  [Usar SQLite.NET ORM](using-sqlite-orm.md)
-  [Usar ADO.NET](using-adonet.md)
-  [Usar dados em um aplicativo](using-data-in-an-app.md)

## <a name="summary"></a>Resumo

Este capítulo discute o acesso a dados em xamarin usando SQLite como o mecanismo de banco de dados. O banco de dados pode ser acessado "diretamente" usando a sintaxe do ADO.NET ou você pode incluir o ORM SQLite.NET e executar operações de dados em c#.

Analisamos dois exemplos: uma que contém o código de acesso de dados muito simples que saídas para um campo de texto e um aplicativo simples que inclui criar, ler, atualizar e excluir a funcionalidade. Também discutimos threading e como a propagação de seu aplicativo com um banco de dados SQLite preenchido previamente.

Para obter exemplos adicionais de acesso a dados de várias plataformas, consulte nosso [Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) estudo de caso.

## <a name="related-links"></a>Links relacionados

- [DataAccess Basic (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançados (amostra)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS receitas de dados](https://developer.xamarin.com/recipes/ios/data/sqlite/)
- [Acesso a dados xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
