---
title: Acesso a dados do iOS
description: "A maioria dos aplicativos têm alguns requisitos para salvar dados no dispositivo local. A menos que a quantidade de dados é muito pequena, isso geralmente exige um banco de dados e uma camada de dados do aplicativo para gerenciar o acesso ao banco de dados. iOS tem o mecanismo de banco de dados SQLite \"interno\" e acesso para armazenar e recuperar dados é simplificado pela plataforma do Xamarin. Este documento mostra como acessar um banco de dados SQLite."
ms.topic: article
ms.prod: xamarin
ms.assetid: 6B47E864-C6E7-4AA2-8DEF-2C8BF551D17C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/11/2016
ms.openlocfilehash: 9ca929f584ec2b0d98300e7645707131df89969f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="ios-data-access"></a>Acesso a dados do iOS

_A maioria dos aplicativos têm alguns requisitos para salvar dados no dispositivo local. A menos que a quantidade de dados é muito pequena, isso geralmente exige um banco de dados e uma camada de dados do aplicativo para gerenciar o acesso ao banco de dados. iOS tem o mecanismo de banco de dados SQLite "interno" e acesso para armazenar e recuperar dados é simplificado pela plataforma do Xamarin. Este documento mostra como acessar um banco de dados SQLite._

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
-  [Usando SQLite.NET ORM](using-sqlite-orm.md)
-  [Usando o ADO.NET](using-adonet.md)
-  [Usando dados de um aplicativo](using-data-in-an-app.md)


## <a name="summary"></a>Resumo

Este capítulo discute o acesso a dados em xamarin usando SQLite como o mecanismo de banco de dados. O banco de dados pode ser acessado "diretamente" usando a sintaxe do ADO.NET ou você pode incluir o ORM SQLite.NET e executar operações de dados em c#.

Analisamos dois exemplos: uma que contém o código de acesso de dados muito simples que saídas para um campo de texto e um aplicativo simples que inclui criar, ler, atualizar e excluir a funcionalidade. Também discutimos threading e como a propagação de seu aplicativo com um banco de dados SQLite preenchido previamente.

Para obter exemplos adicionais de acesso a dados de várias plataformas, consulte nosso [Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) estudo de caso.

## <a name="related-links"></a>Links relacionados

- [DataAccess Basic (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançados (amostra)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS receitas de dados](https://developer.xamarin.com/recipes/ios/data/sqlite/)
- [Acesso a dados xamarin. Forms](~/xamarin-forms/app-fundamentals/databases.md)
