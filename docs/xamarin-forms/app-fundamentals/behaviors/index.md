---
title: Comportamentos do Xamarin.Forms
description: Comportamentos permitem adicionar funcionalidade a controles de interface do usuário sem precisar dividi-los em subclasses. Comportamentos são escritos em código e adicionados aos controles em XAML ou em código.
ms.prod: xamarin
ms.assetid: 42E32AD7-8E3B-48B3-B402-E75B758DA913
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: df0a767976247166205ae8a3d70fd59c521646f6
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "61197533"
---
# <a name="xamarinforms-behaviors"></a>Comportamentos do Xamarin.Forms

_Os comportamentos permitem adicionar funcionalidade aos controles de interface do usuário sem ter que subclassificá-los. Os comportamentos são escritos em código e adicionados aos controles em XAML ou código._

## <a name="introduction-to-behaviors"></a>[Introdução aos comportamentos](introduction.md)

Os comportamentos permitem que você implemente código que normalmente precisaria escrever como code-behind, porque ele interage diretamente com a API do controle de forma que ele pode ser anexado de forma concisa ao controle. Este artigo fornece uma introdução a comportamentos.

## <a name="attached-behaviors"></a>[Comportamentos anexados](attached.md)

Comportamentos anexados são classes `static` com uma ou mais propriedades anexadas. Este artigo demonstra como criar e consumir comportamentos anexados.

## <a name="xamarinforms-behaviors"></a>[Comportamentos do Xamarin.Forms](creating.md)

Os comportamentos do Xamarin.Forms são criados por derivada da [`Behavior`](xref:Xamarin.Forms.Behavior) classe ou [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) da classe. Este artigo demonstra como criar e consumir comportamentos do Xamarin.Forms.

## <a name="reusable-behaviors"></a>[Comportamentos reutilizáveis](reusable/index.md)

Comportamentos são reutilizáveis em mais de um aplicativo. Estes artigos explicam como criar comportamentos úteis para executar a funcionalidade comumente usada.
