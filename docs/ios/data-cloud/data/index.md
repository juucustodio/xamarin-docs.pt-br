---
title: Acesso a dados do Xamarin. iOS
description: Este documento contém links para guias que descrevem como trabalhar com bancos de dados locais em um aplicativo Xamarin. iOS. O conteúdo vinculado discute SQLite.NET, ADO.NET e muito mais.
ms.prod: xamarin
ms.assetid: 3AEDFD8D-FB10-4CEF-BE04-CCD14E95F02C
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 10/11/2016
ms.openlocfilehash: 6050320f781ea89c5455e10a8754a89d7086e92a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70281737"
---
# <a name="xamarinios-data-access"></a>Acesso a dados do Xamarin. iOS

O Xamarin. iOS dá suporte a APIs de acesso ao banco de dados, como:

- Estrutura ADO.NET.
- Biblioteca SQLite-NET de terceiros.

Este guia fornece uma visão geral de alto nível dos bancos de dados em geral antes de descrever como configurar o ADO.NET e o SQLite.NET para acessar bancos de dados SQLite em seus aplicativos Xamarin. iOS. 

A maior parte do código neste documento é completamente entre plataformas e será executado no iOS ou Android sem modificação. Há dois aplicativos de exemplo discutidos:

- [**DataAccess_Basic**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) – operações de dados simples gravam os resultados em um controle de exibição de texto;
- [**DataAccess_Advanced**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) – integra operações de dados em um pequeno aplicativo de trabalho que lista e edita uma estrutura de dados simples.

Ambas as soluções de exemplo contêm projetos de aplicativo de exemplo iOS e Android.

Para aplicativos Xamarin. Forms, leia [trabalhando com bancos de dados](~/xamarin-forms/data-cloud/data/databases.md) que explica como trabalhar com o SQLite em uma biblioteca PCL com Xamarin. Forms.

## <a name="sections"></a>Seções

- [Introdução](introduction.md)
- [Configuração](configuration.md)
- [Usar SQLite.NET ORM](using-sqlite-orm.md)
- [Usar ADO.NET](using-adonet.md)
- [Usar dados em um aplicativo](using-data-in-an-app.md)

## <a name="summary"></a>Resumo

Este capítulo abordou o acesso a dados no Xamarin. iOS usando o SQLite como o mecanismo de banco de dados. O banco de dados pode ser acessado "diretamente" usando a sintaxe ADO.NET ou você pode incluir o ORM SQLite.NET C#e executar operações de dados no.

Revisamos dois exemplos: um que contém código de acesso a dados muito simples que gera um campo de texto e um aplicativo simples que inclui a funcionalidade criar, ler, atualizar e excluir. Também discutimos Threading e como propagar seu aplicativo com um banco de dados SQLite previamente preenchido.

Para obter exemplos adicionais de acesso a dados entre plataformas, consulte nosso estudo de caso do [profissional de tarefas](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) .

## <a name="related-links"></a>Links relacionados

- [DataAccess básico (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avançado (exemplo)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Receitas de dados do iOS](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Acesso a dados do Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
