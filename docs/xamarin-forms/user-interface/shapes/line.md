---
title: 'Xamarin.Forms Formas: linha'
description: A Xamarin.Forms classe line pode ser usada para desenhar linhas.
ms.prod: xamarin
ms.assetid: 384F1A72-6D3B-4FD3-BC40-E00A73A463EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/05/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 962800c5deb546ddf6a74d1f52ffaf60734ae463
ms.sourcegitcommit: 06701714021545eb5e932847829b876082194ffc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585839"
---
# <a name="xamarinforms-shapes-line"></a>Xamarin.Forms Formas: linha

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

A `Line` classe deriva da `Shape` classe e pode ser usada para desenhar linhas. Para obter informações sobre as propriedades que a `Line` classe herda da `Shape` classe, consulte [ Xamarin.Forms formas](index.md).

`Line` define as propriedades a seguir:

- `X1`, do tipo Double, indica a coordenada x do ponto inicial da linha. O valor padrão dessa propriedade é 0,0.
- `Y1`, do tipo Double, indica a coordenada y do ponto de partida da linha. O valor padrão dessa propriedade é 0,0.
- `X2`, do tipo Double, indica a coordenada x do ponto de extremidade da linha. O valor padrão dessa propriedade é 0,0.
- `Y2`, do tipo Double, indica a coordenada y do ponto de extremidade da linha. O valor padrão dessa propriedade é 0,0.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

Para obter informações sobre como controlar como terminações de linha são desenhadas, consulte [extremidades de linha de controle](index.md#control-line-ends).

## <a name="create-a-line"></a>Criar uma linha

Para desenhar uma linha, crie um `Line` objeto e defina suas `X1` `Y1` Propriedades e como seu ponto de partida e suas `X2` `Y` Propriedades e para seu ponto de extremidade. Além disso, defina sua `Stroke` propriedade como um [`Brush`](xref:Xamarin.Forms.Brush) objeto derivado porque uma linha sem um traço é invisível. Para obter mais informações sobre `Brush` objetos, consulte [ Xamarin.Forms pincéis](~/xamarin-forms/user-interface/brushes/index.md).

> [!NOTE]
> A definição da `Fill` propriedade de um `Line` não tem efeito, pois uma linha não tem interior.

O exemplo de XAML a seguir mostra como desenhar uma linha:

```xaml
<Line X1="40"
      Y1="0"
      X2="0"
      Y2="120"
      Stroke="Red" />
```

Neste exemplo, uma linha diagonal vermelha é desenhada de (40, 0) para (0120):

![Linha diagonal](line-images/line.png "Linha")

Como as `X1` `Y1` Propriedades,, e `X2` `Y2` têm valores padrão de 0, é possível desenhar algumas linhas com a sintaxe mínima:

```xaml
<Line Stroke="Red"
      X2="200" />
```

Neste exemplo, uma linha horizontal que é de 200 unidades independentes de dispositivo é definida por extenso. Como as outras propriedades são 0 por padrão, uma linha é desenhada de (0, 0) para (200, 0).

O exemplo de XAML a seguir mostra como desenhar uma linha tracejada:

```xaml
<Line X1="40"
      Y1="0"
      X2="0"
      Y2="120"
      Stroke="DarkBlue"
      StrokeDashArray="1,1"
      StrokeDashOffset="6" />
```

Neste exemplo, uma linha diagonal pontilhada azul escura é desenhada de (40, 0) para (0120):

![Linha tracejada](line-images/dashed-line.png "Linha tracejada")

Para obter mais informações sobre como desenhar uma linha tracejada, consulte [desenhar formas tracejadas](index.md#draw-dashed-shapes).

## <a name="related-links"></a>Links relacionados

- [ShapeDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Existentes](index.md)
- [Xamarin.Forms Pincéis](~/xamarin-forms/user-interface/brushes/index.md)
