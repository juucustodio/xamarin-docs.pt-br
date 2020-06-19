---
title: 'Xamarin.FormsFormas: linha'
description: A Xamarin.Forms classe line pode ser usada para desenhar linhas.
ms.prod: xamarin
ms.assetid: 384F1A72-6D3B-4FD3-BC40-E00A73A463EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a69c505fe83618f06bafe6a49b8b5ce84aef096b
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101296"
---
# <a name="xamarinforms-shapes-line"></a>Xamarin.FormsFormas: linha

![](~/media/shared/preview.png "This API is currently pre-release")

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

A `Line` classe deriva da `Shape` classe e pode ser usada para desenhar linhas. Para obter informações sobre as propriedades que a `Line` classe herda da `Shape` classe, consulte [ Xamarin.Forms formas](index.md).

`Line` define as propriedades a seguir:

- `X1`, do tipo Double, indica a coordenada x do ponto inicial da linha. O valor padrão dessa propriedade é 0,0.
- `X2`, do tipo Double, indica a coordenada x do ponto de extremidade da linha. O valor padrão dessa propriedade é 0,0.
- `Y1`, do tipo Double, indica a coordenada y do ponto de partida da linha. O valor padrão dessa propriedade é 0,0.
- `Y2`, do tipo Double, indica a coordenada y do ponto de extremidade da linha. O valor padrão dessa propriedade é 0,0.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

## <a name="create-a-line"></a>Criar uma linha

O exemplo de XAML a seguir mostra como desenhar uma linha:

```xaml
<Line X1="40"
      Y1="0"
      X2="0"
      Y2="120"
      Stroke="DarkBlue"
      StrokeThickness="4"
      HeightRequest="120"
      WidthRequest="120" />
```

> [!NOTE]
> A definição da `Fill` propriedade de um `Line` não tem efeito, pois uma linha não tem interior.

## <a name="related-links"></a>Links relacionados

- [ShapeDemos (exemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.FormsExistentes](index.md)
