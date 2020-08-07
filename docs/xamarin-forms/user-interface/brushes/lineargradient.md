---
title: 'Xamarin.FormsPincéis: gradientes lineares'
description: A Xamarin.Forms classe LinearGradientBrush pinta uma área com um gradiente linear.
ms.prod: xamarin
ms.assetid: BEA2B3F5-96B0-4E39-88A6-0FAFE95C3DCD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 734ecae4fdafd71f0c88ddc5e4b4ed0c672f2019
ms.sourcegitcommit: 579ec4f2884fa391e5e214a3952cd6004c521eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87919626"
---
# <a name="no-locxamarinforms-brushes-linear-gradients"></a>Xamarin.FormsPincéis: gradientes lineares

![API de visualização](~/media/shared/preview.png "Esta API está atualmente em pré-lançamento")

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/BrushDemos)

A `LinearGradientBrush` classe deriva da `GradientBrush` classe e pinta uma área com um gradiente linear, que mistura duas ou mais cores ao longo de uma linha conhecida como o eixo gradiente. `GradientStop`os objetos são usados para especificar as cores no gradiente e suas posições. Para obter mais informações sobre `GradientStop` objetos, consulte [ Xamarin.Forms pincéis: gradientes](gradient.md).

A `LinearGradientBrush` classe define as seguintes propriedades:

- `StartPoint`, do tipo [`Point`](xref:Xamarin.Forms.Point) , que representa as coordenadas bidimensionais iniciais do gradiente linear. O valor padrão dessa propriedade é (0, 0).
- `EndPoint`, do tipo [`Point`](xref:Xamarin.Forms.Point) , que representa as coordenadas bidimensionais finais do gradiente linear. O valor padrão dessa propriedade é (1, 1).

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

A `LinearGradientBrush` classe também como um `IsEmpty` método que retorna um `bool` que representa se os objetos foram atribuídos a um pincel `GradientStop` .

> [!NOTE]
> Gradientes lineares também podem ser criados com a `linear-gradient()` função CSS.

## <a name="create-a-lineargradientbrush"></a>Criar um LinearGradientBrush

As interrupções de gradiente de gradiente linear são posicionadas ao longo do eixo de gradiente. A orientação e o tamanho do eixo de gradiente podem ser alterados usando as `StartPoint` Propriedades e do pincel `EndPoint` . Ao manipular essas propriedades, você pode criar gradientes horizontais, verticais e diagonais, reverter a direção do gradiente, condensar a disseminação do gradiente e muito mais.

As `StartPoint` `EndPoint` Propriedades e são relativas à área que está sendo pintada. (0, 0) representa o canto superior esquerdo da área que está sendo pintada e (1, 1) representa o canto inferior direito da área que está sendo pintada. O diagrama a seguir mostra o eixo de gradiente para um pincel de gradiente linear diagonal:

![Quadro com um eixo de gradiente diagonal](lineargradient-images/gradient-axis.png)

Neste diagrama, a linha tracejada mostra o eixo gradiente, que realça o caminho de interpolação do gradiente do ponto inicial até o ponto de extremidade.

### <a name="create-a-horizontal-linear-gradient"></a>Criar um gradiente linear horizontal

Para criar um gradiente linear horizontal, crie um `LinearGradientBrush` objeto e defina seu `StartPoint` como (0, 0) e seu `EndPoint` como (1, 0). Em seguida, adicione dois ou mais `GradientStop` objetos à `LinearGradientBrush.GradientStops` coleção, que especificam as cores no gradiente e suas posições.

O exemplo de XAML a seguir mostra uma horizontal `LinearGradientBrush` que é definida como `Background` de um [`Frame`](xref:Xamarin.Forms.Frame) :

```xaml
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
    <Frame.Background>
        <!-- StartPoint defaults to (0,0) -->
        <LinearGradientBrush EndPoint="1,0">
            <GradientStop Color="Yellow"
                          Offset="0.1" />
            <GradientStop Color="Green"
                          Offset="1.0" />
        </LinearGradientBrush>
    </Frame.Background>
</Frame>  
```

Neste exemplo, o plano de fundo do [`Frame`](xref:Xamarin.Forms.Frame) é pintado com um `LinearGradientBrush` que interpola de amarelo para verde horizontalmente:

![Quadro pintado com um LinearGradientBrush horizontal](lineargradient-images/horizontal.png)

### <a name="create-a-vertical-linear-gradient"></a>Criar um gradiente linear vertical

Para criar um gradiente linear vertical, crie um `LinearGradientBrush` objeto e defina seu `StartPoint` como (0, 0) e seu `EndPoint` como (0, 1). Em seguida, adicione dois ou mais `GradientStop` objetos à `LinearGradientBrush.GradientStops` coleção, que especificam as cores no gradiente e suas posições.

O exemplo de XAML a seguir mostra uma vertical `LinearGradientBrush` que é definida como `Background` de um [`Frame`](xref:Xamarin.Forms.Frame) :

```xaml
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
    <Frame.Background>
        <!-- StartPoint defaults to (0,0) -->    
        <LinearGradientBrush EndPoint="0,1">
            <GradientStop Color="Yellow"
                          Offset="0.1" />
            <GradientStop Color="Green"
                          Offset="1.0" />
        </LinearGradientBrush>
    </Frame.Background>
</Frame>
```

Neste exemplo, o plano de fundo do [`Frame`](xref:Xamarin.Forms.Frame) é pintado com um `LinearGradientBrush` que interpola de amarelo para verde verticalmente:

![Quadro pintado com um LinearGradientBrush vertical](lineargradient-images/vertical.png)

### <a name="create-a-diagonal-linear-gradient"></a>Criar um gradiente linear diagonal

Para criar um gradiente linear diagonal, crie um `LinearGradientBrush` objeto e defina seu `StartPoint` como (0, 0) e seu `EndPoint` como (1, 1). Em seguida, adicione dois ou mais `GradientStop` objetos à `LinearGradientBrush.GradientStops` coleção, que especificam as cores no gradiente e suas posições.

O exemplo de XAML a seguir mostra uma diagonal `LinearGradientBrush` definida como a `Background` de um [`Frame`](xref:Xamarin.Forms.Frame) :

```xaml
<Frame BorderColor="LightGray"
       HasShadow="True"
       CornerRadius="12"
       HeightRequest="120"
       WidthRequest="120">
    <Frame.Background>
        <!-- StartPoint defaults to (0,0)      
             Endpoint defaults to (1,1) -->
        <LinearGradientBrush>
            <GradientStop Color="Yellow"
                          Offset="0.1" />
            <GradientStop Color="Green"
                          Offset="1.0" />
        </LinearGradientBrush>
    </Frame.Background>
</Frame>
```

Neste exemplo, o plano de fundo do [`Frame`](xref:Xamarin.Forms.Frame) é pintado com um `LinearGradientBrush` que interpola de amarelo para verde diagonalmente:

![Quadro pintado com um LinearGradientBrush diagonal](lineargradient-images/diagonal.png)

## <a name="related-links"></a>Links relacionados

- [BrushesDemos (exemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/BrushDemos)
- [Xamarin.FormsPincéis: gradientes](gradient.md)
