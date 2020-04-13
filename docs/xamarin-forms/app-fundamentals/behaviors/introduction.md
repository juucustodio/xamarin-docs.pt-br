---
title: Introdução aos comportamentos
description: Comportamentos permitem adicionar funcionalidade a controles de interface do usuário sem precisar dividi-los em subclasses. Em vez disso, a funcionalidade é implementada em uma classe de comportamento e anexada ao controle como se fizesse parte do próprio controle. Este artigo fornece uma introdução a comportamentos.
ms.prod: xamarin
ms.assetid: 0DF1EF8C-A212-4142-A3C6-DF760A82A757
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: d62ba6b025b2fe9865df8279a5e98eba254bb5a2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70772057"
---
# <a name="introduction-to-behaviors"></a>Introdução aos comportamentos

_Os comportamentos permitem adicionar funcionalidade aos controles de interface do usuário sem ter que subclassificá-los. Em vez disso, a funcionalidade é implementada em uma classe de comportamento e anexada ao controle como se fosse parte do controle em si. Este artigo fornece uma introdução aos comportamentos._

Os comportamentos permitem que você implemente código que normalmente precisaria escrever como code-behind, porque interagem diretamente com a API do controle, de forma que podem ser anexados de forma concisa ao controle e empacotados para reutilização em mais de um aplicativo. Eles podem ser usados para fornecer uma ampla gama de funcionalidade para controles, como:

- Adicionando um validador de e-mail a um [`Entry`](xref:Xamarin.Forms.Entry).
- Criar um controle de classificação usando um reconhecedor de gesto de toque.
- Controlar uma animação.
- Adicionar um efeito a um controle.

Os comportamentos também permitem cenários mais avançados. No contexto dos *comandos*, os comportamentos são uma abordagem útil para conectar um controle a um comando. Além disso, eles podem ser usados para associar comandos a controles que não foram projetados para interagir com comandos. Por exemplo, podem ser usados para invocar um comando em resposta ao acionamento de um evento.

O Xamarin.Forms dá suporte a dois estilos diferentes de comportamentos:

- **Comportamentos do Xamarin.Forms** – as classes que derivam da classe [`Behavior`](xref:Xamarin.Forms.Behavior) ou [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1), em que `T` é o tipo do controle ao qual o comportamento deve ser aplicado. Para obter mais informações sobre comportamentos no Xamarin.Forms, confira [Comportamentos do Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md) e [Comportamentos reutilizáveis](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md).
- **Comportamentos anexados** `static` – classes com uma ou mais propriedades anexadas. Para obter mais informações sobre comportamentos anexados, confira [Comportamentos anexados](~/xamarin-forms/app-fundamentals/behaviors/attached.md).

Este guia se concentra em comportamentos do Xamarin.Forms porque eles são a abordagem preferida para construção de comportamentos.

## <a name="related-links"></a>Links relacionados

- [Comportamento](xref:Xamarin.Forms.Behavior)
- [Comportamento&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
