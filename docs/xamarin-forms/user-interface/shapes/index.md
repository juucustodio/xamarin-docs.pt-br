---
title: Xamarin.FormsExistentes
description: Xamarin.FormsAs formas são tipos de modos de exibição que permitem desenhar formas na tela.
ms.prod: xamarin
ms.assetid: 4E749FE8-852C-46DA-BB1E-652936106357
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9c7c552abe724dca6b06265b73346a399d35c3cb
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101397"
---
# <a name="xamarinforms-shapes"></a>Xamarin.FormsExistentes

![](~/media/shared/preview.png "This API is currently pre-release")

Um `Shape` é um tipo de [`View`](xref:Xamarin.Forms.View) que permite desenhar uma forma na tela. `Shape`os objetos podem ser usados nas classes de layout e na maioria dos controles, porque a `Shape` classe deriva da `View` classe.

Xamarin.FormsAs formas estão disponíveis no `Xamarin.Forms.Shapes` namespace no Ios, no Android, no MacOS, no plataforma universal do Windows (UWP) e no Windows Presentation Foundation (WPF).

> [!IMPORTANT]
> Xamarin.FormsAs formas são experimentais no momento e só podem ser usadas pela definição do `Shapes_Experimental` sinalizador. Para obter mais informações, consulte [sinalizadores experimentais](~/xamarin-forms/internals/experimental-flags.md).

`Shape` define as propriedades a seguir:

- `Aspect`, do tipo `Stretch` , descreve como a forma preenche seu espaço alocado. O valor padrão dessa propriedade é `Stretch.None`.
- `Fill`, do tipo `Color` , indica a cor usada para pintar o interior da forma.
- `Stroke`, do tipo `Color` , indica a cor usada para pintar o contorno da forma.
- `StrokeDashArray`, do tipo `DoubleCollection` , que representa uma coleção de `double` valores que indicam o padrão de traços e lacunas que são usados para estruturar uma forma.
- `StrokeDashOffset`, do tipo `double` , especifica a distância dentro do padrão de tracejado onde um traço começa. O valor padrão dessa propriedade é 0,0.
- `StrokeLineCap`, do tipo `PenLineCap` , descreve a forma no início e no fim de uma linha ou segmento. O valor padrão dessa propriedade é `PenLineCap.Flat`.
- `StrokeLineJoin`, do tipo `PenLineJoin` , especifica o tipo de junção que é usado nos vértices de uma forma. O valor padrão dessa propriedade é `PenLineJoin.Miter`.
- `StrokeThickness`, do tipo `double` , indica a largura do contorno da forma. O valor padrão dessa propriedade é 1,0.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

Xamarin.Formsdefine um número de objetos que derivam da `Shape` classe. Eles incluem `Ellipse`, `Line`, `Path`, `Polygon`, `Polyline` e `Rectangle`.

## <a name="related-links"></a>Links relacionados

- [ShapeDemos (exemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
