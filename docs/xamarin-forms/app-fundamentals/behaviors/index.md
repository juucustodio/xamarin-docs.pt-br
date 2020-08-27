---
title: Xamarin.Forms Comportamento
description: Comportamentos permitem adicionar funcionalidade a controles de interface do usuário sem precisar dividi-los em subclasses. Comportamentos são escritos em código e adicionados aos controles em XAML ou em código.
ms.prod: xamarin
ms.assetid: 42E32AD7-8E3B-48B3-B402-E75B758DA913
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d917d7d6421cfae7fc877c81023a835573fa99b1
ms.sourcegitcommit: a003b036f6fb83818e2ecc9c72a641e3aeb373bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88964617"
---
# <a name="no-locxamarinforms-behaviors"></a>Xamarin.Forms Comportamento

_Os comportamentos permitem que você adicione funcionalidade aos controles da interface do usuário sem precisar subclasseá-los. Os comportamentos são escritos em código e adicionados a controles em XAML ou código._

## <a name="introduction-to-behaviors"></a>[Introdução aos comportamentos](introduction.md)

Os comportamentos permitem que você implemente código que normalmente precisaria escrever como code-behind, porque ele interage diretamente com a API do controle de forma que ele pode ser anexado de forma concisa ao controle. Este artigo fornece uma introdução a comportamentos.

## <a name="attached-behaviors"></a>[Comportamentos anexados](attached.md)

Comportamentos anexados são classes `static` com uma ou mais propriedades anexadas. Este artigo demonstra como criar e consumir comportamentos anexados.

## <a name="no-locxamarinforms-behaviors"></a>[Xamarin.Forms Comportamento](creating.md)

Xamarin.Forms os comportamentos são criados pela derivação da [`Behavior`](xref:Xamarin.Forms.Behavior) [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) classe ou. Este artigo demonstra como criar e consumir Xamarin.Forms comportamentos.

## <a name="reusable-effectbehavior"></a>[EffectBehavior reutilizável](effect-behavior.md)

Os comportamentos são uma abordagem útil para adicionar um efeito a um controle, removendo o código de texto clichê de tratamento de efeito dos arquivos code-behind. Este artigo demonstra como criar e consumir um Xamarin.Forms comportamento para adicionar um efeito a um controle.
