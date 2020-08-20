---
title: Xamarin.Forms RelativeLayout
description: O Xamarin.Forms RelativeLayout é usado na posição e no tamanho de filhos em relação às propriedades dos elementos de layout ou irmãos.
ms.prod: xamarin
ms.assetid: 2530BCB8-01B8-4C4F-BF14-CA53659F1B5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/13/2020
ms.custom: contperfq1
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 77b1837fb58d5743fd887b9f636f7f7311b807d3
ms.sourcegitcommit: 9bd6b1b20d126b3f837c4cf859b25895c242e54e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88648168"
---
# <a name="no-locxamarinforms-relativelayout"></a>Xamarin.Forms RelativeLayout

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-relativelayoutdemos)

[![::: no-Loc (Xamarin. Forms)::: RelativeLayout](relativelayout-images/layouts.png)](relativelayout-images/layouts-large.png#lightbox)

Um [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) é usado para posicionar e dimensionar filhos em relação às propriedades dos elementos de layout ou irmãos. Isso permite que as UIs sejam criadas para dimensionar proporcionalmente entre os tamanhos de dispositivo. Além disso, ao contrário de algumas outras classes de layout, `RelativeLayout` é possível posicionar filhos para que se sobreponham.

A [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) classe define as seguintes propriedades:

- [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty), do tipo [`Constraint`](xref:Xamarin.Forms.Constraint) , que é uma propriedade anexada que representa a restrição na posição X do filho.
- [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty), do tipo [`Constraint`](xref:Xamarin.Forms.Constraint) , que é uma propriedade anexada que representa a restrição na posição Y do filho.
- [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty), do tipo [`Constraint`](xref:Xamarin.Forms.Constraint) , que é uma propriedade anexada que representa a restrição na largura do filho.
- [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty), do tipo [`Constraint`](xref:Xamarin.Forms.Constraint) , que é uma propriedade anexada que representa a restrição na altura do filho.
- [`BoundsConstraint`](xref:Xamarin.Forms.RelativeLayout.BoundsConstraintProperty), do tipo [`BoundsConstraint`](xref:Xamarin.Forms.BoundsConstraint) , que é uma propriedade anexada que representa a restrição na posição e no tamanho do filho. Essa propriedade não pode ser facilmente consumida a partir de XAML.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados e com estilo. Para obter mais informações sobre propriedades anexadas, consulte [ Xamarin.Forms Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md).

> [!NOTE]
> A largura e a altura de um filho em um [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) também podem ser especificadas por meio das `WidthRequest` Propriedades e do filho `HeightRequest` , em vez das [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) Propriedades anexadas e.

A [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) classe deriva da `Layout<T>` classe, que define uma `Children` Propriedade do tipo `IList<T>` . A `Children` propriedade é a `ContentProperty` da `Layout<T>` classe e, portanto, não precisa ser definida explicitamente a partir de XAML.

> [!TIP]
> Evite usar um [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) sempre que possível. Isso resultará em a CPU precisar realizar significativamente mais trabalho.

## <a name="constraints"></a>Restrições

Dentro de um [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) , a posição e o tamanho dos filhos são especificados como restrições usando valores absolutos ou valores relativos. Quando as restrições não forem especificadas, um filho será posicionado no canto superior esquerdo do layout.

A tabela a seguir mostra como especificar restrições em XAML e C#:

|     | XAML | C# |
| --- | ---- | -- |
| **Valores absolutos** | Restrições absolutas são especificadas definindo as [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) Propriedades anexadas como `double` valores. | As restrições absolutas são especificadas pelo [`Constraint.Constant`](xref:Xamarin.Forms.Constraint.Constant*) método ou usando a `Children.Add` sobrecarga que exige um `Func<Rectangle>` argumento. |
| **Valores relativos** | As restrições relativas são especificadas definindo as [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) Propriedades anexadas para [`Constraint`](xref:Xamarin.Forms.Constraint) objetos que são retornados pela `ConstraintExpression` extensão de marcação. | As restrições relativas são especificadas por [`Constraint`](xref:Xamarin.Forms.Constraint) objetos que são retornados por métodos da `Constraint` classe. |

Para obter mais informações sobre como especificar restrições usando valores absolutos, consulte [posicionamento absoluto e dimensionamento](#absolute-positioning-and-sizing). Para obter mais informações sobre como especificar restrições usando valores relativos, consulte [posicionamento relativo e dimensionamento](#relative-positioning-and-sizing).

No C#, os filhos podem ser adicionados a [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) três `Add` sobrecargas. A primeira sobrecarga requer um `Expression<Func<Rectangle>>` para especificar a posição e o tamanho de um filho. A segunda sobrecarga requer objetos opcionais `Expression<Func<double>>` para `x` os `y` argumentos,, `width` e `height` . A terceira sobrecarga requer objetos opcionais `Constraint` para `x` os `y` argumentos,, `width` e `height` .

É possível alterar a posição e o tamanho de um filho em um [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) com os [`SetXConstraint`](xref:Xamarin.Forms.RelativeLayout.SetXConstraint*) métodos, [`SetYConstraint`](xref:Xamarin.Forms.RelativeLayout.SetYConstraint*) , [`SetWidthConstraint`](xref:Xamarin.Forms.RelativeLayout.SetWidthConstraint*) e [`SetHeightConstraint`](xref:Xamarin.Forms.RelativeLayout.SetHeightConstraint*) . O primeiro argumento para cada um desses métodos é o filho, e o segundo é um [`Constraint`](xref:Xamarin.Forms.Constraint) objeto. Além disso, o [`SetBoundsConstraint`](xref:Xamarin.Forms.RelativeLayout.SetBoundsConstraint*) método também pode ser usado para alterar a posição e o tamanho de um filho. O primeiro argumento para esse método é filho e o segundo é um [`BoundsConstraint`](xref:Xamarin.Forms.BoundsConstraint) objeto.

## <a name="absolute-positioning-and-sizing"></a>Posicionamento absoluto e dimensionamento

Um [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) pode posicionar e dimensionar filhos usando valores absolutos, especificados em unidades independentes de dispositivo, que definem explicitamente onde os filhos devem ser colocados no layout. Isso é obtido com a adição de filhos à `Children` coleção de um `RelativeLayout` e a definição das [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) Propriedades,, e [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) anexadas em cada filho para os valores de posição absoluta e/ou tamanho.

> [!WARNING]
> Usar valores absolutos para posicionar e dimensionar filhos pode ser problemático, pois diferentes dispositivos têm diferentes tamanhos de tela e resoluções. Portanto, as coordenadas do centro da tela em um dispositivo podem ser deslocadas em outros dispositivos.

O XAML a seguir mostra um [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) cujos filhos são posicionados usando valores absolutos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="RelativeLayoutDemos.Views.StylishHeaderDemoPage"
             Title="Stylish header demo">
    <RelativeLayout Margin="20">
        <BoxView Color="Silver"
                 RelativeLayout.XConstraint="0"
                 RelativeLayout.YConstraint="10"
                 RelativeLayout.WidthConstraint="200"
                 RelativeLayout.HeightConstraint="5" />
        <BoxView Color="Silver"
                 RelativeLayout.XConstraint="0"
                 RelativeLayout.YConstraint="20"
                 RelativeLayout.WidthConstraint="200"
                 RelativeLayout.HeightConstraint="5" />
        <BoxView Color="Silver"
                 RelativeLayout.XConstraint="10"
                 RelativeLayout.YConstraint="0"
                 RelativeLayout.WidthConstraint="5"
                 RelativeLayout.HeightConstraint="65" />
        <BoxView Color="Silver"
                 RelativeLayout.XConstraint="20"
                 RelativeLayout.YConstraint="0"
                 RelativeLayout.WidthConstraint="5"
                 RelativeLayout.HeightConstraint="65" />
        <Label Text="Stylish header"
               FontSize="24"
               RelativeLayout.XConstraint="30"
               RelativeLayout.YConstraint="25" />
    </RelativeLayout>
</ContentPage>
```

Neste exemplo, a posição de cada [`BoxView`](xref:Xamarin.Forms.BoxView) objeto é definida usando os valores especificados nas [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) Propriedades anexadas e. O tamanho de cada `BoxView` é definido usando os valores especificados nas [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) Propriedades e [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) anexadas. A posição do [`Label`](xref:Xamarin.Forms.Label) objeto também é definida usando os valores especificados nas `XConstraint` `YConstraint` Propriedades anexadas e. No entanto, os valores de tamanho não são especificados para o `Label` e, portanto, não são restringidos e tamanhos próprios. Em todos os casos, os valores absolutos representam unidades independentes de dispositivo.

As capturas de tela a seguir mostram o layout resultante:

![Filhos colocados em um RelativeLayout usando valores absolutos](relativelayout-images/absolute-values.png)

O código C# equivalente é mostrado abaixo:

```csharp
public class StylishHeaderDemoPageCS : ContentPage
{
    public StylishHeaderDemoPageCS()
    {
        RelativeLayout relativeLayout = new RelativeLayout
        {
            Margin = new Thickness(20)
        };

        relativeLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, () => new Rectangle(0, 10, 200, 5));

        relativeLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, () => new Rectangle(0, 20, 200, 5));

        relativeLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, () => new Rectangle(10, 0, 5, 65));

        relativeLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, () => new Rectangle(20, 0, 5, 65));

        relativeLayout.Children.Add(new Label
        {
            Text = "Stylish Header",
            FontSize = 24
        }, Constraint.Constant(30), Constraint.Constant(25));

        Title = "Stylish header demo";
        Content = relativeLayout;
    }
}
```

Neste exemplo, [`BoxView`](xref:Xamarin.Forms.BoxView) os objetos são adicionados ao [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) usando uma `Add` sobrecarga que exige um `Expression<Func<Rectangle>>` para especificar a posição e o tamanho de cada filho. A posição do [`Label`](xref:Xamarin.Forms.Label) é definida usando uma `Add` sobrecarga que requer objetos opcionais [`Constraint`](xref:Xamarin.Forms.Constraint) , nesse caso, criado pelo [`Constraint.Constant`](xref:Xamarin.Forms.Constraint.Constant*) método.

> [!NOTE]
> Um [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) valor que usa valores absolutos pode posicionar e dimensionar filhos para que eles não caibam nos limites do layout.

## <a name="relative-positioning-and-sizing"></a>Posicionamento e dimensionamento relativos

Um [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) pode posicionar e dimensionar filhos usando valores que são relativos às propriedades do layout ou elementos irmãos. Isso é feito adicionando filhos à `Children` coleção de `RelativeLayout` e definindo as [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) Propriedades,, e [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) anexadas em cada filho para valores relativos usando [`Constraint`](xref:Xamarin.Forms.Constraint) objetos.

As restrições podem ser uma constante, em relação a um pai ou em relação a um irmão. O tipo de restrição é representado pela [`ConstraintType`](xref:Xamarin.Forms.ConstraintType) enumeração, que define os seguintes membros:

- `RelativeToParent`, que indica uma restrição relativa a um pai.
- `RelativeToView`, que indica uma restrição relativa a uma exibição (ou irmão).
- `Constant`, que indica uma restrição de constante.

### <a name="constraint-markup-extension"></a>Extensão de marcação de restrição

Em XAML, um [`Constraint`](xref:Xamarin.Forms.Constraint) objeto pode ser criado pela [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression) extensão de marcação. Essa extensão de marcação normalmente é usada para relacionar a posição e o tamanho de um filho em um [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) para seu pai ou para um irmão.

A [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression) classe define as seguintes propriedades:

- [`Constant`](xref:Xamarin.Forms.ConstraintExpression.Constant), do tipo `double` , que representa o valor constante da restrição.
- [`ElementName`](xref:Xamarin.Forms.ConstraintExpression.ElementName), do tipo `string` , que representa o nome de um elemento de origem em relação ao qual calcular a restrição.
- [`Factor`](xref:Xamarin.Forms.ConstraintExpression.Factor), do tipo `double` , que representa o fator pelo qual dimensionar uma dimensão restrita, em relação ao elemento de origem. O padrão dessa propriedade é 1.
- [`Property`](xref:Xamarin.Forms.ConstraintExpression.Property), do tipo `string` , que representa o nome da propriedade no elemento de origem a ser usado no cálculo da restrição.
- [`Type`](xref:Xamarin.Forms.ConstraintExpression.Type), do tipo [`ConstraintType`](xref:Xamarin.Forms.ConstraintType) , que representa o tipo da restrição.

Para obter mais informações sobre Xamarin.Forms extensões de marcação, consulte [extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md).

O XAML a seguir mostra um [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) cujos filhos são restritos pela [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression) extensão de marcação:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="RelativeLayoutDemos.Views.RelativePositioningAndSizingDemoPage"
             Title="RelativeLayout demo">
    <RelativeLayout>
        <BoxView Color="Red"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=Constant, Constant=0}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=Constant, Constant=0}" />
        <BoxView Color="Green"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Constant=-40}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=Constant, Constant=0}" />
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=Constant, Constant=0}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Constant=-40}" />
        <BoxView Color="Yellow"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Constant=-40}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Constant=-40}" />

        <!-- Centered and 1/3 width and height of parent -->
        <BoxView x:Name="oneThird"
                 Color="Silver"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.33}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0.33}"
                 RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.33}"
                 RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0.33}" />

        <!-- 1/3 width and height of previous -->
        <BoxView Color="Black"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToView, ElementName=oneThird, Property=X}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView, ElementName=oneThird, Property=Y}"
                 RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToView, ElementName=oneThird, Property=Width, Factor=0.33}"
                 RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToView, ElementName=oneThird, Property=Height, Factor=0.33}" />
    </RelativeLayout>
</ContentPage>
```

Neste exemplo, a posição de cada [`BoxView`](xref:Xamarin.Forms.BoxView) objeto é definida pela definição das [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) Propriedades anexadas e. O primeiro `BoxView` tem suas `XConstraint` `YConstraint` Propriedades e anexadas definidas como constantes, que são valores absolutos. Os `BoxView` objetos restantes têm sua posição definida usando pelo menos um valor relativo. Por exemplo, o `BoxView` objeto amarelo define a `XConstraint` Propriedade anexada como a largura de seu pai (o [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) ) menos 40. Da mesma forma, isso `BoxView` define a `YConstraint` Propriedade anexada com a altura de seu pai menos 40. Isso garante que o amarelo `BoxView` seja exibido no canto inferior direito da tela.

> [!NOTE]
> [`BoxView`](xref:Xamarin.Forms.BoxView) os objetos que não especificam um tamanho são dimensionados automaticamente para 40x40 pelo Xamarin.Forms .

O prata [`BoxView`](xref:Xamarin.Forms.BoxView) nomeado `oneThird` é posicionado centralmente, em relação ao seu pai. Ele também é dimensionado em relação ao seu pai, sendo um terço de sua largura e altura. Isso é feito definindo as [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) Propriedades anexadas e com a largura do pai (o [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) ), multiplicado por 0,33. Da mesma forma, as [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) Propriedades e anexadas são definidas com a altura do pai, multiplicada por 0,33.

O preto [`BoxView`](xref:Xamarin.Forms.BoxView) é posicionado e dimensionado em relação ao `oneThird` `BoxView` . Isso é feito definindo suas [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) Propriedades e [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) anexadas aos `X` valores e `Y` , respectivamente, do elemento irmão. Da mesma forma, seu tamanho é definido como um terço da largura e altura de seu elemento irmão. Isso é feito definindo suas [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) Propriedades e [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) anexadas aos `Width` valores e `Height` do elemento irmão, respectivamente, que são multiplicados por 0,33.

A captura de tela a seguir mostra o layout resultante:

![Filhos colocados em um RelativeLayout usando valores relativos](relativelayout-images/relative-values.png)

### <a name="constraint-objects"></a>Objetos de restrição

A [`Constraint`](xref:Xamarin.Forms.Constraint) classe define os seguintes métodos estáticos públicos, que retornam `Constraint` objetos:

- [`Constant`](xref:Xamarin.Forms.Constraint.Constant*), que restringe um filho a um tamanho especificado com um `double` .
- [`FromExpression`](xref:Xamarin.Forms.Constraint.FromExpression*), que restringe um filho usando uma expressão lambda.
- [`RelativeToParent`](xref:Xamarin.Forms.Constraint.RelativeToParent*), que restringe um filho relativo ao tamanho do pai.
- [`RelativeToView`](xref:Xamarin.Forms.Constraint.RelativeToView*), que restringe um filho relativo ao tamanho de uma exibição.

Além disso, a [`BoundsConstraint`](xref:Xamarin.Forms.BoundsConstraint) classe define um único método, [`FromExpression`](xref:Xamarin.Forms.BoundsConstraint.FromExpression*) , que retorna um `BoundsConstraint` que restringe a posição e o tamanho de um filho com um `Expression<Func<Rectangle>>` . Esse método pode ser usado para definir a [`BoundsConstraint`](xref:Xamarin.Forms.RelativeLayout.BoundsConstraintProperty) Propriedade anexada.

O código C# a seguir mostra um [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) cujos filhos são restritos por [`Constraint`](xref:Xamarin.Forms.Constraint) objetos:

```csharp
public class RelativePositioningAndSizingDemoPageCS : ContentPage
{
    public RelativePositioningAndSizingDemoPageCS()
    {
        RelativeLayout relativeLayout = new RelativeLayout();

        // Four BoxView's
        relativeLayout.Children.Add(
            new BoxView { Color = Color.Red },
            Constraint.Constant(0),
            Constraint.Constant(0));

        relativeLayout.Children.Add(
            new BoxView { Color = Color.Green },
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Width - 40;
            }), Constraint.Constant(0));

        relativeLayout.Children.Add(
            new BoxView { Color = Color.Blue },
            Constraint.Constant(0),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Height - 40;
            }));

        relativeLayout.Children.Add(
            new BoxView { Color = Color.Yellow },
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Width - 40;
            }),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Height - 40;
            }));

        // Centered and 1/3 width and height of parent
        BoxView silverBoxView = new BoxView { Color = Color.Silver };
        relativeLayout.Children.Add(
            silverBoxView,
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Width * 0.33;
            }),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Height * 0.33;
            }),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Width * 0.33;
            }),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Height * 0.33;
            }));

        // 1/3 width and height of previous
        relativeLayout.Children.Add(
            new BoxView { Color = Color.Black },
            Constraint.RelativeToView(silverBoxView, (parent, sibling) =>
            {
                return sibling.X;
            }),
            Constraint.RelativeToView(silverBoxView, (parent, sibling) =>
            {
                return sibling.Y;
            }),
            Constraint.RelativeToView(silverBoxView, (parent, sibling) =>
            {
                return sibling.Width * 0.33;
            }),
            Constraint.RelativeToView(silverBoxView, (parent, sibling) =>
            {
                return sibling.Height * 0.33;
            }));

        Title = "RelativeLayout demo";
        Content = relativeLayout;
    }
}
```

Neste exemplo, os filhos são adicionados ao [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) usando a `Add` sobrecarga que exige um objeto opcional `Constraint` para os `x` argumentos, `y` , `width` e `height` .

> [!NOTE]
> Um [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) valor que usa valores relativos pode posicionar e dimensionar filhos para que eles não caibam nos limites do layout.

## <a name="related-links"></a>Links relacionados

- [Demonstrações do RelativeLayout (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-relativelayoutdemos)
- [Xamarin.Forms Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md)
- [Extensões de marcação do XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Escolher um Xamarin.Forms layout](choose-layout.md)
- [Melhorar o Xamarin.Forms desempenho do aplicativo](~/xamarin-forms/deploy-test/performance.md)
