---
title: Suporte a idiomas no Xamarin de programação
description: Este documento descreve as várias linguagens de programação com suporte pelo Xamarin. Ele discute C#, F#, portátil Visual Basic.NET e modelos do Razor.
ms.prod: xamarin
ms.assetid: CEE8C464-67D7-45F4-9614-EAEF5217CACC
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: 6c0b8e6de0c414fb708c4027f4c536a21b6b011a
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864372"
---
# <a name="programming-language-support-in-xamarin"></a>Suporte a idiomas no Xamarin de programação

## <a name="c"></a>C# 

### <a name="async-support-overviewcross-platformplatformasyncmd"></a>[Visão geral do suporte assíncrono](~/cross-platform/platform/async.md)

Versão 5 do C# introduziu duas novas palavras-chave para expressar operações assíncronas: async e await. Essas palavras-chave permitem que você escreva código simple que utiliza a biblioteca paralela de tarefas para executar operações de longa execução (como o acesso à rede) em outro thread e acessar facilmente os resultados após a conclusão. As versões mais recentes do xamarin. IOS e xamarin. Android dão suporte a async e await – este documento fornece explicações e um exemplo de como usar a nova sintaxe com o Xamarin.

### <a name="c-6-language-featurescross-platformplatformcsharp-sixmd"></a>[Recursos de linguagem C# 6](~/cross-platform/platform/csharp-six.md)

A versão mais recente do C# linguagem – versão 6 – continua a evoluir a linguagem para ter menos clichê, melhor clareza e mais consistência. A sintaxe de inicialização mais limpa, a capacidade de usar `await` na `catch/finally` blocos e o nulo condicional `?` operador são especialmente úteis.

## <a name="ffsharpindexmd"></a>[F#](fsharp/index.md)

Criando aplicativos móveis com o F# e o Xamarin.

## <a name="portable-visual-basicnetcross-platformplatformvisual-basicindexmd"></a>[Visual Basic.NET portátil](~/cross-platform/platform/visual-basic/index.md)

Visual Studio oferece suporte à criação de bibliotecas de classes portáteis usando o Visual Basic.NET que, em seguida, podem ser incorporados em aplicativos Xamarin. Este artigo mostra como criar uma nova PCL no Visual Basic e, em seguida, usá-lo em um aplicativo xamarin. IOS, xamarin. Android e Windows Phone de exemplo.

## <a name="building-html-views-using-razor-templatescross-platformplatformrazor-html-templatesindexmd"></a>[Modos de exibição HTML de construção usando modelos do Razor](~/cross-platform/platform/razor-html-templates/index.md)

O Xamarin permite aos desenvolvedores aproveitar o mecanismo de modelagem do Razor, originalmente introduzido com o ASP.NET MVC, juntamente com C# para combinar facilmente dados com HTML, Javascript e CSS sem o incômodo de criação manual de cadeias de caracteres HTML no código.
Este artigo demonstra como usar modelos do Razor com o Xamarin para Android e iOS.
