---
title: Objetivo Sharpie
description: "Esta seção fornece uma introdução ao objetivo Sharpie, ferramenta de linha de comando do Xamarin usada para automatizar o processo de criação de uma associação a uma biblioteca Objective-C"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8A832A76-A770-1A7C-24BA-B3E6F57617A0
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: 02eebb7d8f579a207b6777771dbea223d30211cc
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="objective-sharpie"></a>Objetivo Sharpie

_Esta seção fornece uma introdução ao objetivo Sharpie, ferramenta de linha de comando do Xamarin usada para automatizar o processo de criação de uma associação a uma biblioteca Objective-C_

<style type="text/css"> azul .terminal {cor: rgb(10,96,254);} .terminal verde {cor: rgb(12,156,26);} .terminal magenta {cor: rgb(152,12,103);} </style>

- [Visão geral de](#overview) & [histórico](#history)
- [Introdução](get-started.md)
- [Ferramentas e comandos](tools.md)
- [Recursos](platform/index.md)
- [Exemplos](examples/index.md)
- [Passo a passo completo](~/ios/platform/binding-objective-c/walkthrough.md)
- [Histórico de lançamento](releases.md)

#<a name="overview"></a>Visão geral

Objetivo Sharpie é uma ferramenta de linha de comando para inicializar a primeira passagem de uma associação.
Funciona analisando os arquivos de cabeçalho de uma biblioteca nativa para mapear a API pública para o [associação definição](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) (um processo que anteriormente era feito manualmente).

Objetivo Sharpie usa arquivos de cabeçalho de análise de Clang, para que a associação é exato e completo possível. Isso pode reduzir significativamente o tempo e esforço necessário para produzir uma associação de qualidade.

> [!IMPORTANT]
> **Aviso:** Sharpie objetivo é uma ferramenta para desenvolvedores do Xamarin experientes com conhecimento avançado de Objective-C (e, por extensão, C). Antes de tentar associar uma biblioteca Objective-C, você deve ter um conhecimento sólido de como criar a biblioteca nativa na linha de comando (e uma boa compreensão de como funciona a biblioteca nativa).



#<a name="history"></a>Histórico

Nós foram desenvolvendo e usando o objetivo Sharpie internamente em Xamarin nos últimos três anos. Apresenta a potência do objetivo Sharpie, APIs introduzido no xamarin e Xamarin.Mac desde iOS 8, Mac OS X 10.10 e watchOS 2.0 foi inicializados completamente com o objetivo de Sharpie. Xamarin depende muito objetivo Sharpie internamente para criar seus próprios produtos.

No entanto, o objetivo Sharpie é uma ferramenta muito avançada que requer conhecimento avançado de Objective-C e C, como usar o compilador clang na linha de comando e bibliotecas nativas como geralmente são colocadas juntos. Devido a essa barra alta, percebemos que, com uma GUI assistente define as expectativas erradas, e como tal, objetivo Sharpie atualmente só está disponível como uma ferramenta de linha de comando.



## <a name="related-links"></a>Links relacionados

- [Download de Sharpie objetivo](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie.pkg)
- [Passo a passo: Associando uma biblioteca Objective-C](~/ios/platform/binding-objective-c/walkthrough.md)
- [Associação de bibliotecas Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Detalhes da associação](~/cross-platform/macios/binding/overview.md)
- [Guia de referência de tipos de associação](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin para desenvolvedores de Objective-C](~/ios/get-started/objective-c-developers/index.md)
