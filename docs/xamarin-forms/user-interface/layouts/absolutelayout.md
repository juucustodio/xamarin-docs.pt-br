---
title: Xamarin.Forms AbsoluteLayout
description: O Xamarin.Forms AbsoluteLayout é usado para posicionar e dimensionar elementos usando valores explícitos ou valores proporcionais ao tamanho do layout.
ms.prod: xamarin
ms.assetid: 01A5CCE0-AD45-4806-84FD-72C007005B38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2020
ms.custom: contperfq1
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b5dab372f5ebd3850702d122b21317fcb48e0a25
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371970"
---
# <a name="no-locxamarinforms-absolutelayout"></a>Xamarin.Forms AbsoluteLayout

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-absolutelayoutdemos)

[![::: no-Loc (Xamarin. Forms)::: AbsoluteLayout](absolutelayout-images/layouts.png)](absolutelayout-images/layouts-large.png#lightbox)

Um [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) é usado para posicionar e dimensionar filhos usando valores explícitos. A posição é especificada pelo canto superior esquerdo do filho em relação ao canto superior esquerdo do `AbsoluteLayout` , em unidades independentes do dispositivo. `AbsoluteLayout` também implementa um recurso de posicionamento e dimensionamento proporcional. Além disso, ao contrário de algumas outras classes de layout, `AbsoluteLayout` é possível posicionar filhos para que eles se sobreponham.

Um [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) deve ser considerado um layout de finalidade especial para ser usado somente quando você puder impor um tamanho em filhos ou quando o tamanho do elemento não afetar o posicionamento de outros filhos.

A [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) classe define as seguintes propriedades:

- [`LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty), do tipo [`Rectangle`](xref:Xamarin.Forms.Rectangle) , que é uma propriedade anexada que representa a posição e o tamanho de um filho. O valor padrão dessa propriedade é (0, 0, AutoSize, AutoSize).
- [`LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty), do tipo [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) , que é uma propriedade anexada que indica se as propriedades dos limites de layout usados para posicionar e dimensionar o filho são interpretadas proporcionalmente. O valor padrão dessa propriedade é `AbsoluteLayoutFlags.None`.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados e com estilo. Para obter mais informações sobre propriedades anexadas, consulte [ Xamarin.Forms Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md).

A [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) classe deriva da `Layout<T>` classe, que define uma `Children` Propriedade do tipo `IList<T>` . A `Children` propriedade é a `ContentProperty` da `Layout<T>` classe e, portanto, não precisa ser definida explicitamente a partir de XAML.

> [!TIP]
> Para obter o melhor desempenho de layout possível, siga as diretrizes em [otimizar o desempenho do layout](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance).

## <a name="position-and-size-children"></a>Posição e tamanho filhos

A posição e o tamanho dos filhos em um [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) são definidos pela definição da [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) Propriedade anexada de cada filho, usando valores absolutos ou valores proporcionais. Valores absolutos e proporcionais podem ser misturados para filhos quando a posição deve ser dimensionada, mas o tamanho deve permanecer fixo ou vice-versa. Para obter informações sobre valores absolutos, consulte [posicionamento absoluto e dimensionamento](#absolute-positioning-and-sizing). Para obter informações sobre valores proporcionais, consulte [posicionamento proporcional e dimensionamento](#proportional-positioning-and-sizing).

A [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) Propriedade anexada pode ser definida usando dois formatos, independentemente de os valores absolutos ou proporcionais serem usados:

- `x, y`. Com esse formato, os `x` `y` valores e indicam a posição do canto superior esquerdo do filho em relação ao seu pai. O filho é irrestrito e tem seu próprio tamanho.
- `x, y, width, height`. Com esse formato, os `x` `y` valores e indicam a posição do canto superior esquerdo do filho em relação ao seu pai, enquanto os `width` `height` valores e indicam o tamanho do filho.

Para especificar que os tamanhos filho são horizontalmente ou verticalmente, ou ambos, defina os `width` valores e/ou `height` para a [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) propriedade. No entanto, o uso excessivo dessa propriedade pode prejudicar o desempenho do aplicativo, pois faz com que o mecanismo de layout execute cálculos de layout adicionais.

> [!IMPORTANT]
> As [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriedades e não têm efeito sobre os filhos de um [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) .

## <a name="absolute-positioning-and-sizing"></a>Posicionamento absoluto e dimensionamento

Por padrão, [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) as posições e os tamanhos são filhos usando valores absolutos, especificados em unidades independentes de dispositivo, que definem explicitamente onde os filhos devem ser colocados no layout. Isso é obtido adicionando filhos à `Children` coleção de um `AbsoluteLayout` e definindo a [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) Propriedade anexada em cada filho para a posição absoluta e/ou valores de tamanho.

> [!WARNING]
> Usar valores absolutos para posicionar e dimensionar filhos pode ser problemático, pois diferentes dispositivos têm diferentes tamanhos de tela e resoluções. Portanto, as coordenadas do centro da tela em um dispositivo podem ser deslocadas em outros dispositivos.

O XAML a seguir mostra um [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) cujos filhos são posicionados usando valores absolutos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="AbsoluteLayoutDemos.Views.StylishHeaderDemoPage"
             Title="Stylish header demo">
    <AbsoluteLayout Margin="20">
        <BoxView Color="Silver"
                 AbsoluteLayout.LayoutBounds="0, 10, 200, 5" />
        <BoxView Color="Silver"
                 AbsoluteLayout.LayoutBounds="0, 20, 200, 5" />
        <BoxView Color="Silver"
                 AbsoluteLayout.LayoutBounds="10, 0, 5, 65" />
        <BoxView Color="Silver"
                 AbsoluteLayout.LayoutBounds="20, 0, 5, 65" />
        <Label Text="Stylish Header"
               FontSize="24"
               AbsoluteLayout.LayoutBounds="30, 25" />
    </AbsoluteLayout>
</ContentPage>
```

Neste exemplo, a posição de cada [`BoxView`](xref:Xamarin.Forms.BoxView) objeto é definida usando os dois primeiros valores absolutos especificados na [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) Propriedade anexada. O tamanho de cada `BoxView` é definido usando os valores de terceiro e para trás. A posição do [`Label`](xref:Xamarin.Forms.Label) objeto é definida usando os dois valores absolutos especificados na `AbsoluteLayout.LayoutBounds` Propriedade anexada. Os valores de tamanho não são especificados para o `Label` e, portanto, não são restringidos e tamanhos próprios. Em todos os casos, os valores absolutos representam unidades independentes de dispositivo.

A captura de tela a seguir mostra o layout resultante:

![Filhos colocados em um AbsoluteLayout usando valores absolutos](absolutelayout-images/absolute-values.png)

O código C# equivalente é mostrado abaixo:

```csharp
public class StylishHeaderDemoPageCS : ContentPage
{
    public StylishHeaderDemoPageCS()
    {
        AbsoluteLayout absoluteLayout = new AbsoluteLayout
        {
            Margin = new Thickness(20)
        };

        absoluteLayout.Children.Add(new BoxView
        {
            Color = Color.Silver,
        }, new Rectangle(0, 10, 200, 5));
        absoluteLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, new Rectangle(0, 20, 200, 5));
        absoluteLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, new Rectangle(10, 0, 5, 65));
        absoluteLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, new Rectangle(20, 0, 5, 65));

        absoluteLayout.Children.Add(new Label
        {
            Text = "Stylish Header",
            FontSize = 24
        }, new Point(30,25));                    

        Title = "Stylish header demo";
        Content = absoluteLayout;
    }
}
```

Neste exemplo, a posição e o tamanho de cada [`BoxView`](xref:Xamarin.Forms.BoxView) um é definido usando um [`Rectangle`](xref:Xamarin.Forms.Rectangle) objeto. A posição do [`Label`](xref:Xamarin.Forms.Label) é definida usando um [`Point`](xref:Xamarin.Forms.Point) objeto.

Em C#, também é possível definir a posição e o tamanho de um filho de um [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) depois que ele foi adicionado à `Children` coleção, usando o [`AbsoluteLayout.SetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds*) método. O primeiro argumento para esse método é filho e o segundo é um [`Rectangle`](xref:Xamarin.Forms.Rectangle) objeto.

> [!NOTE]
> Um [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) que usa valores absolutos pode posicionar e dimensionar filhos para que eles não caibam nos limites do layout.

## <a name="proportional-positioning-and-sizing"></a>Posicionamento e dimensionamento proporcional

Um [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) pode posicionar e dimensionar filhos usando valores proporcionais. Isso é obtido pela adição de filhos à `Children` coleção do `AbsoluteLayout` e pela definição da [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) Propriedade anexada em cada filho para a posição proporcional e/ou valores de tamanho no intervalo de 0-1. Os valores de posição e tamanho se tornam proporcionais definindo a [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) Propriedade anexada em cada filho.

A [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) Propriedade anexada, do tipo [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) , permite que você defina um sinalizador que indica que a posição dos limites de layout e os valores de tamanho de um filho são proporcionais ao tamanho do `AbsoluteLayout` . Ao dispor de um filho, `AbsoluteLayout` o dimensiona os valores de posição e de tamanho adequadamente para qualquer tamanho de dispositivo.

A [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) enumeração define os seguintes membros:

- `None`, indica que os valores serão interpretados como absolutos. Esse é o valor padrão da [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) Propriedade anexada.
- `XProportional`, indica que o `x` valor será interpretado como proporcional, ao mesmo tempo que trata todos os outros valores como absolutos.
- `YProportional`, indica que o `y` valor será interpretado como proporcional, ao mesmo tempo que trata todos os outros valores como absolutos.
- `WidthProportional`, indica que o `width` valor será interpretado como proporcional, ao mesmo tempo que trata todos os outros valores como absolutos.
- `HeightProportional`, indica que o `height` valor será interpretado como proporcional, ao mesmo tempo que trata todos os outros valores como absolutos.
- `PositionProportional`, indica que os `x` `y` valores e serão interpretados como proporcionais, enquanto os valores de tamanho são interpretados como absolutos.
- `SizeProportional`, indica que os `width` `height` valores e serão interpretados como proporcionais, enquanto os valores de posição são interpretados como absolutos.
- `All`, indica que todos os valores serão interpretados como proporcionais.

> [!TIP]
> A [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) enumeração é uma `Flags` enumeração, o que significa que os membros da enumeração podem ser combinados. Isso é feito em XAML com uma lista separada por vírgulas e em C# com o operador OR bit a ponto.

Por exemplo, se você usar o `SizeProportional` sinalizador e definir a largura de um filho como 0,25 e a altura como 0,1, o filho será um quarto da largura do [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) e um décimo da altura. O `PositionProportional` sinalizador é semelhante. Uma posição de (0, 0) coloca o filho no canto superior esquerdo, enquanto uma posição de (1, 1) coloca o filho no canto inferior direito e uma posição de (0,5, 0.5) centraliza o filho dentro do `AbsoluteLayout` .

O XAML a seguir mostra um [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) cujos filhos são posicionados usando valores proporcionais:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="AbsoluteLayoutDemos.Views.ProportionalDemoPage"
             Title="Proportional demo">
    <AbsoluteLayout>
        <BoxView Color="Blue"
                 AbsoluteLayout.LayoutBounds="0.5,0,100,25"
                 AbsoluteLayout.LayoutFlags="PositionProportional" />
        <BoxView Color="Green"
                 AbsoluteLayout.LayoutBounds="0,0.5,25,100"
                 AbsoluteLayout.LayoutFlags="PositionProportional" />
        <BoxView Color="Red"
                 AbsoluteLayout.LayoutBounds="1,0.5,25,100"
                 AbsoluteLayout.LayoutFlags="PositionProportional" />
        <BoxView Color="Black"
                 AbsoluteLayout.LayoutBounds="0.5,1,100,25"
                 AbsoluteLayout.LayoutFlags="PositionProportional" />
        <Label Text="Centered text"
               AbsoluteLayout.LayoutBounds="0.5,0.5,110,25"
               AbsoluteLayout.LayoutFlags="PositionProportional" />
    </AbsoluteLayout>
</ContentPage>
```

Neste exemplo, cada filho é posicionado usando valores proporcionais, mas dimensionado usando valores absolutos. Isso é feito definindo a [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) Propriedade anexada de cada filho como `PositionProportional` . Os dois primeiros valores especificados na [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) Propriedade anexada, para cada filho, definem a posição usando valores proporcionais. O tamanho de cada filho é definido com o terceiro e o outro valores absolutos, usando unidades independentes de dispositivo.

A captura de tela a seguir mostra o layout resultante:

![Filhos colocados em um AbsoluteLayout usando valores de posição proporcional](absolutelayout-images/proportional-position.png)

O código C# equivalente é mostrado abaixo:

```csharp
public class ProportionalDemoPageCS : ContentPage
{
    public ProportionalDemoPageCS()
    {
        BoxView blue = new BoxView { Color = Color.Blue };
        AbsoluteLayout.SetLayoutBounds(blue, new Rectangle(0.5, 0, 100, 25));
        AbsoluteLayout.SetLayoutFlags(blue, AbsoluteLayoutFlags.PositionProportional);

        BoxView green = new BoxView { Color = Color.Green };
        AbsoluteLayout.SetLayoutBounds(green, new Rectangle(0, 0.5, 25, 100));
        AbsoluteLayout.SetLayoutFlags(green, AbsoluteLayoutFlags.PositionProportional);

        BoxView red = new BoxView { Color = Color.Red };
        AbsoluteLayout.SetLayoutBounds(red, new Rectangle(1, 0.5, 25, 100));
        AbsoluteLayout.SetLayoutFlags(red, AbsoluteLayoutFlags.PositionProportional);

        BoxView black = new BoxView { Color = Color.Black };
        AbsoluteLayout.SetLayoutBounds(black, new Rectangle(0.5, 1, 100, 25));
        AbsoluteLayout.SetLayoutFlags(black, AbsoluteLayoutFlags.PositionProportional);

        Label label = new Label { Text = "Centered text" };
        AbsoluteLayout.SetLayoutBounds(label, new Rectangle(0.5, 0.5, 110, 25));
        AbsoluteLayout.SetLayoutFlags(label, AbsoluteLayoutFlags.PositionProportional);

        Title = "Proportional demo";
        Content = new AbsoluteLayout
        {
            Children = { blue, green, red, black, label }
        };
    }
}
```

Neste exemplo, a posição e o tamanho de cada filho são definidos com o [`AbsoluteLayout.SetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds*) método. O primeiro argumento para o método é filho e o segundo é um [`Rectangle`](xref:Xamarin.Forms.Rectangle) objeto. A posição de cada filho é definida com valores proporcionais, enquanto o tamanho de cada filho é definido com valores absolutos, usando unidades independentes de dispositivo.

> [!NOTE]
> Um [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) que usa valores proporcionais pode posicionar e dimensionar filhos para que eles não caibam nos limites do layout usando valores fora do intervalo de 0-1.

## <a name="related-links"></a>Links relacionados

- [Demonstrações do AbsoluteLayout (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-absolutelayoutdemos)
- [Xamarin.Forms Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md)
- [Escolher um Xamarin.Forms layout](choose-layout.md)
- [Melhorar o Xamarin.Forms desempenho do aplicativo](~/xamarin-forms/deploy-test/performance.md)