---
title: Suporte à linguagem de programação no Xamarin
description: Este documento descreve várias linguagens de programação com suporte no Xamarin. Ele aborda C#, F#, o Visual Portable Basic.net e os modelos do Razor.
ms.prod: xamarin
ms.assetid: CEE8C464-67D7-45F4-9614-EAEF5217CACC
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: 2ec934b2747f89e959d659615629489e86449660
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065151"
---
# <a name="programming-language-support-in-xamarin"></a>Suporte à linguagem de programação no Xamarin

## <a name="c"></a>C\#

### <a name="async-support-overviewcross-platformplatformasyncmd"></a>[Visão geral do suporte assíncrono](~/cross-platform/platform/async.md)

A versão 5 C# do introduziu duas novas palavras-chave para expressar operações assíncronas: Async e Await. Essas palavras-chave permitem escrever código simples que utiliza a biblioteca de tarefas paralelas para executar operações de longa execução (como acesso à rede) em outro thread e acessar facilmente os resultados na conclusão. As versões mais recentes do Xamarin. iOS e Xamarin. Android dão suporte a Async e Await-este documento fornece explicações e um exemplo de como usar a nova sintaxe com o Xamarin.

### <a name="c-6-language-featurescross-platformplatformcsharp-sixmd"></a>[Recursos de linguagem C# 6](~/cross-platform/platform/csharp-six.md)

A versão mais recente da C# linguagem – versão 6 – continua a evoluir a linguagem para ter menos nitidez, maior clareza e mais consistência. A sintaxe de inicialização mais limpa, a `await` capacidade `catch/finally` de usar em blocos e o operador `?` nulo-condicional são especialmente úteis.

## <a name="ffsharpindexmd"></a>[F#](fsharp/index.md)

Criando aplicativos móveis com F# o e o Xamarin.

## <a name="portable-visual-basicnetcross-platformplatformvisual-basicindexmd"></a>[Basic.NET Visual portátil](~/cross-platform/platform/visual-basic/index.md)

O Visual Studio dá suporte à criação de bibliotecas de classes portáteis usando o Visual Basic.NET, que pode ser incorporado em aplicativos Xamarin. Este artigo mostra como criar um novo Visual Basic PCL e, em seguida, usá-lo em um aplicativo de exemplo Xamarin. iOS, Xamarin. Android e Windows Phone.

## <a name="building-html-views-using-razor-templatescross-platformplatformrazor-html-templatesindexmd"></a>[Criando exibições HTML usando modelos do Razor](~/cross-platform/platform/razor-html-templates/index.md)

O Xamarin permite que os desenvolvedores aproveitem o mecanismo de modelagem do Razor, originalmente introduzidos C# com o ASP.NET MVC, junto com para combinar facilmente dados com HTML, JavaScript e CSS, sem a complicação de criar manualmente cadeias de caracteres HTML no código.
Este artigo demonstra como usar modelos do Razor com Xamarin para Android e iOS.
