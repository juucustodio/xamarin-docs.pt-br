---
title: Xamarin.FormsStackLayout
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f624674cc6d4ba1bdc34a42fb52fb63ff8a7135a
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137963"
---
# <a name="xamarinforms-stacklayout"></a>Xamarin.FormsStackLayout

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stacklayoutdemos)

[![Xamarin.FormsStackLayout](stacklayout-images/layouts.png "[! Parar. Não-LOC (Xamarin. Forms)] StackLayout")](stacklayout-images/layouts-large.png#lightbox "[! Parar. Não-LOC (Xamarin. Forms)] StackLayout")

Um [`StackLayout`](xref:Xamarin.Forms.StackLayout) organiza exibições filhas em uma pilha unidimensional, tanto horizontal quanto verticalmente. Por padrão, um `StackLayout` é orientado verticalmente. Além disso, um `StackLayout` pode ser usado como um layout pai que contém outros layouts filho.

A [`StackLayout`](xref:Xamarin.Forms.StackLayout) classe define as seguintes propriedades:

- [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation), do tipo [`StackOrientation`](xref:Xamarin.Forms.StackOrientation) representa a direção na qual as exibições filhas são posicionadas. O valor padrão dessa propriedade é `Vertical`.
- [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing), do tipo `double` , indica a quantidade de espaço entre cada exibição filho. O valor padrão dessa propriedade é seis unidades independentes de dispositivo.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados e com estilo.

A [`StackLayout`](xref:Xamarin.Forms.StackLayout) classe deriva da `Layout<T>` classe, que define uma `Children` Propriedade do tipo `IList<T>` . A `Children` propriedade é a `ContentProperty` da `Layout<T>` classe e, portanto, não precisa ser definida explicitamente a partir de XAML.

> [!TIP]
> Para obter o melhor desempenho de layout possível, siga as diretrizes em [otimizar o desempenho do layout](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance).

## <a name="vertical-orientation"></a>Orientação vertical

O XAML a seguir mostra como criar um verticalmente orientado [`StackLayout`](xref:Xamarin.Forms.StackLayout) que contém diferentes exibições filho:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.VerticalStackLayoutPage"
             Title="Vertical StackLayout demo">
    <StackLayout Margin="20">
        <Label Text="Primary colors" />
        <BoxView Color="Red" />
        <BoxView Color="Yellow" />
        <BoxView Color="Blue" />
        <Label Text="Secondary colors" />
        <BoxView Color="Green" />
        <BoxView Color="Orange" />
        <BoxView Color="Purple" />
    </StackLayout>
</ContentPage>
```

Este exemplo cria um [`StackLayout`](xref:Xamarin.Forms.StackLayout) contendo [`Label`](xref:Xamarin.Forms.Label) e objetos verticais [`BoxView`](xref:Xamarin.Forms.BoxView) . Por padrão, há seis unidades de espaço independentes de dispositivo entre as exibições filho:

[![Captura de tela de um StackLayout orientado verticalmente](stacklayout-images/vertical.png "StackLayout orientado verticalmente")](stacklayout-images/vertical-large.png#lightbox "StackLayout orientado verticalmente")

Este é o código C# equivalente:

```csharp
public class VerticalStackLayoutPageCS : ContentPage
{
    public VerticalStackLayoutPageCS()
    {
        Title = "Vertical StackLayout demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children =
            {
                new Label { Text = "Primary colors" },
                new BoxView { Color = Color.Red },
                new BoxView { Color = Color.Yellow },
                new BoxView { Color = Color.Blue },
                new Label { Text = "Secondary colors" },
                new BoxView { Color = Color.Green },
                new BoxView { Color = Color.Orange },
                new BoxView { Color = Color.Purple }
            }
        };
    }
}
```

> [!NOTE]
> O valor da [`Margin`](xref:Xamarin.Forms.View.Margin) propriedade representa a distância entre um elemento e seus elementos adjacentes. Para saber mais, confira [Margens e preenchimento](margin-and-padding.md).

## <a name="horizontal-orientation"></a>Orientação horizontal

O XAML a seguir mostra como criar um orientado horizontalmente [`StackLayout`](xref:Xamarin.Forms.StackLayout) definindo sua [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) propriedade como `Horizontal` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.HorizontalStackLayoutPage"
             Title="Horizontal StackLayout demo">
    <StackLayout Margin="20"
                 Orientation="Horizontal"
                 HorizontalOptions="Center">
        <BoxView Color="Red" />
        <BoxView Color="Yellow" />
        <BoxView Color="Blue" />
        <BoxView Color="Green" />
        <BoxView Color="Orange" />
        <BoxView Color="Purple" />
    </StackLayout>
</ContentPage>
```

Este exemplo cria um [`StackLayout`](xref:Xamarin.Forms.StackLayout) objeto contendo horizontal [`BoxView`](xref:Xamarin.Forms.BoxView) , com seis unidades de espaço independentes de dispositivo entre as exibições filho:

[![Captura de tela de um StackLayout orientado horizontalmente](stacklayout-images/horizontal.png "StackLayout orientado horizontalmente")](stacklayout-images/horizontal-large.png#lightbox "StackLayout orientado horizontalmente")

Este é o código C# equivalente:

```csharp
public HorizontalStackLayoutPageCS()
{
    Title = "Horizontal StackLayout demo";
    Content = new StackLayout
    {
        Margin = new Thickness(20),
        Orientation = StackOrientation.Horizontal,
        HorizontalOptions = LayoutOptions.Center,
        Children =
        {
            new BoxView { Color = Color.Red },
            new BoxView { Color = Color.Yellow },
            new BoxView { Color = Color.Blue },
            new BoxView { Color = Color.Green },
            new BoxView { Color = Color.Orange },
            new BoxView { Color = Color.Purple }
        }
    };
}
```

## <a name="space-between-child-views"></a>Espaço entre exibições filho

O espaçamento entre exibições filho em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) pode ser alterado definindo a [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) propriedade como um `double` valor:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.StackLayoutSpacingPage"
             Title="StackLayout Spacing demo">
    <StackLayout Margin="20"
                 Spacing="0">
        <Label Text="Primary colors" />
        <BoxView Color="Red" />
        <BoxView Color="Yellow" />
        <BoxView Color="Blue" />
        <Label Text="Secondary colors" />
        <BoxView Color="Green" />
        <BoxView Color="Orange" />
        <BoxView Color="Purple" />
    </StackLayout>
</ContentPage>
```

Este exemplo cria um [`StackLayout`](xref:Xamarin.Forms.StackLayout) contendo [`Label`](xref:Xamarin.Forms.Label) e objetos verticais [`BoxView`](xref:Xamarin.Forms.BoxView) que não têm nenhum espaçamento entre eles:

[![Captura de tela de um StackLayout sem nenhum espaçamento](stacklayout-images/spacing.png "StackLayout sem nenhum espaçamento")](stacklayout-images/spacing-large.png#lightbox "StackLayout sem nenhum espaçamento")

> [!TIP]
> A [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) propriedade pode ser definida como valores negativos para que as exibições filhas sejam sobrepostas.

Este é o código C# equivalente:

```csharp
public class StackLayoutSpacingPageCS : ContentPage
{
    public StackLayoutSpacingPageCS()
    {
        Title = "StackLayout Spacing demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Spacing = 0,
            Children =
            {
                new Label { Text = "Primary colors" },
                new BoxView { Color = Color.Red },
                new BoxView { Color = Color.Yellow },
                new BoxView { Color = Color.Blue },
                new Label { Text = "Secondary colors" },
                new BoxView { Color = Color.Green },
                new BoxView { Color = Color.Orange },
                new BoxView { Color = Color.Purple }
            }
        };
    }
}
```

## <a name="position-and-size-of-child-views"></a>Posição e tamanho das exibições filho

O tamanho e a posição das exibições filhas dentro de uma [`StackLayout`](xref:Xamarin.Forms.StackLayout) depende dos valores das exibições [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) e propriedades filho e dos [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) valores de suas [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriedades e. Em uma vertical [`StackLayout`](xref:Xamarin.Forms.StackLayout) , as exibições filhas se expandem para preencher a largura disponível quando seu tamanho não é definido explicitamente. Da mesma forma, em uma `StackLayout` exibição horizontal, as exibições filho se expandem para preencher a altura disponível quando seu tamanho não é definido explicitamente.

As [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriedades e de a [`StackLayout`](xref:Xamarin.Forms.StackLayout) , e suas exibições filho, podem ser definidas como campos da [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) estrutura, que encapsula duas preferências de layout:

- O *alinhamento* determina a posição e o tamanho de uma exibição filho dentro de seu layout pai.
- A *expansão* indica se a exibição filho deve usar espaço extra, se estiver disponível.

> [!TIP]
> Não defina as [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriedades e de a [`StackLayout`](xref:Xamarin.Forms.StackLayout) , a menos que você precise. Os valores padrão de `LayoutOptions.Fill` e `LayoutOptions.FillAndExpand` permitem a melhor otimização de layout. A alteração dessas propriedades tem um custo e consome memória, mesmo ao configurá-las de volta para os valores padrão.

### <a name="alignment"></a>Alinhamento

O exemplo XAML a seguir define as preferências de alinhamento em cada exibição filho no [`StackLayout`](xref:Xamarin.Forms.StackLayout) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.AlignmentPage"
             Title="Alignment demo">
    <StackLayout Margin="20">
        <Label Text="Start"
               BackgroundColor="Gray"
               HorizontalOptions="Start" />
        <Label Text="Center"
               BackgroundColor="Gray"
               HorizontalOptions="Center" />
        <Label Text="End"
               BackgroundColor="Gray"
               HorizontalOptions="End" />
        <Label Text="Fill"
               BackgroundColor="Gray"
               HorizontalOptions="Fill" />
    </StackLayout>
</ContentPage>
```

Neste exemplo, as preferências de alinhamento são definidas nos [`Label`](xref:Xamarin.Forms.Label) objetos para controlar sua posição dentro do [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Os [`Start`](xref:Xamarin.Forms.LayoutOptions.Start) [`Center`](xref:Xamarin.Forms.LayoutOptions.Center) campos,, e [`End`](xref:Xamarin.Forms.LayoutOptions.End) [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) são usados para definir o alinhamento dos [`Label`](xref:Xamarin.Forms.Label) objetos dentro do pai `StackLayout` :

[![Captura de tela de um StackLayout com opções de alinhamento definidas](stacklayout-images/alignment.png "StackLayout com opções de alinhamento")](stacklayout-images/alignment-large.png#lightbox "StackLayout com opções de alinhamento")

Um [`StackLayout`](xref:Xamarin.Forms.StackLayout) respeita somente as preferências de alinhamento em exibições filho que estão na direção oposta à orientação `StackLayout`. Portanto, as exibições filho de [`Label`](xref:Xamarin.Forms.Label) dentro do `StackLayout` orientado verticalmente definem suas propriedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) como um dos campos de alinhamento:

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start), que posiciona o [`Label`](xref:Xamarin.Forms.Label) no lado esquerdo do [`StackLayout`](xref:Xamarin.Forms.StackLayout) .
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center), que centraliza o [`Label`](xref:Xamarin.Forms.Label) no [`StackLayout`](xref:Xamarin.Forms.StackLayout).
- [`End`](xref:Xamarin.Forms.LayoutOptions.End), que posiciona o [`Label`](xref:Xamarin.Forms.Label) no lado direito do [`StackLayout`](xref:Xamarin.Forms.StackLayout) .
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill), que garante que o [`Label`](xref:Xamarin.Forms.Label) preencha a largura do [`StackLayout`](xref:Xamarin.Forms.StackLayout).

Este é o código C# equivalente:

```csharp
public class AlignmentPageCS : ContentPage
{
    public AlignmentPageCS()
    {
        Title = "Alignment demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children =
            {
                new Label { Text = "Start", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Start },
                new Label { Text = "Center", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Center },
                new Label { Text = "End", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.End },
                new Label { Text = "Fill", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Fill }
            }
        };
    }
}
```

### <a name="expansion"></a>Expansões

O exemplo XAML a seguir define as preferências de expansão em cada [`Label`](xref:Xamarin.Forms.Label) uma das seguintes opções [`StackLayout`](xref:Xamarin.Forms.StackLayout) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.ExpansionPage"
             Title="Expansion demo">
    <StackLayout Margin="20">
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="Start"
               BackgroundColor="Gray"
               VerticalOptions="StartAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="Center"
               BackgroundColor="Gray"
               VerticalOptions="CenterAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="End"
               BackgroundColor="Gray"
               VerticalOptions="EndAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="Fill"
               BackgroundColor="Gray"
               VerticalOptions="FillAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
    </StackLayout>
</ContentPage>
```

Neste exemplo, as preferências de expansão são definidas nos [`Label`](xref:Xamarin.Forms.Label) objetos para controlar seu tamanho no [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Os [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand) [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand) campos,, [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand) e [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) são usados para definir a preferência de alinhamento e se o `Label` ocupará mais espaço se estiver disponível no pai `StackLayout` :

[![Captura de tela de um StackLayout com opções de expansão definidas](stacklayout-images/expansion.png "StackLayout com opções de expansão")](stacklayout-images/expansion-large.png#lightbox "StackLayout com opções de expansão")

Um [`StackLayout`](xref:Xamarin.Forms.StackLayout) só pode expandir exibições filho na direção de sua orientação. Portanto, o `StackLayout` orientado verticalmente pode expandir exibições filho de [`Label`](xref:Xamarin.Forms.Label) que definem suas propriedades [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) como um dos campos de expansão. Isso significa que, para o alinhamento vertical, cada `Label` ocupa a mesma quantidade de espaço dentro do `StackLayout`. No entanto, somente o último `Label`, que define sua propriedade [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) como [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) tem um tamanho diferente.

> [!TIP]
> Ao usar um [`StackLayout`](xref:Xamarin.Forms.StackLayout) , verifique se apenas uma exibição filho está definida como [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) . Essa propriedade garante que o filho especificado ocupe o maior espaço que o `StackLayout` pode dar a ele e é um desperdício executar esses cálculos mais de uma vez.

Este é o código C# equivalente:

```csharp
public ExpansionPageCS()
{
    Title = "Expansion demo";
    Content = new StackLayout
    {
        Margin = new Thickness(20),
        Children =
        {
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "StartAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.StartAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "CenterAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.CenterAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "EndAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.EndAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "FillAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.FillAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 }
        }
    };
}
```

> [!IMPORTANT]
> Quando todo o espaço em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) é usado, as preferências de expansão não têm nenhum efeito.

Para obter mais informações sobre alinhamento e expansão, consulte [Opções de Xamarin.Forms layout em ](layout-options.md).

## <a name="nested-stacklayout-objects"></a>Objetos StackLayout aninhados

Um [`StackLayout`](xref:Xamarin.Forms.StackLayout) pode ser usado como um layout pai que contém objetos filho aninhados `StackLayout` ou outros layouts filho.

O XAML a seguir mostra um exemplo de aninhamento de [`StackLayout`](xref:Xamarin.Forms.StackLayout) objetos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.CombinedStackLayoutPage"
             Title="Combined StackLayouts demo">
    <StackLayout Margin="20">
        ...
        <Frame BorderColor="Black"
               Padding="5">
            <StackLayout Orientation="Horizontal"
                         Spacing="15">
                <BoxView Color="Red" />
                <Label Text="Red"
                       FontSize="Large"
                       VerticalOptions="Center" />
            </StackLayout>
        </Frame>
        <Frame BorderColor="Black"
               Padding="5">
            <StackLayout Orientation="Horizontal"
                         Spacing="15">
                <BoxView Color="Yellow" />
                <Label Text="Yellow"
                       FontSize="Large"
                       VerticalOptions="Center" />
            </StackLayout>
        </Frame>
        <Frame BorderColor="Black"
               Padding="5">
            <StackLayout Orientation="Horizontal"
                         Spacing="15">
                <BoxView Color="Blue" />
                <Label Text="Blue"
                       FontSize="Large"
                       VerticalOptions="Center" />
            </StackLayout>
        </Frame>
        ...
    </StackLayout>
</ContentPage>
```

Neste exemplo, o pai [`StackLayout`](xref:Xamarin.Forms.StackLayout) contém objetos aninhados `StackLayout` dentro de [`Frame`](xref:Xamarin.Forms.Frame) objetos. O pai `StackLayout` é orientado verticalmente, enquanto os `StackLayout` objetos filho são orientados horizontalmente:

[![Captura de tela de objetos StackLayout aninhados](stacklayout-images/combined.png "StackLayouts aninhado")](stacklayout-images/combined-large.png#lightbox "StackLayouts aninhado")

> [!IMPORTANT]
> Quanto mais fundo você aninhar [`StackLayout`](xref:Xamarin.Forms.StackLayout) objetos e outros layouts, mais os layouts aninhados afetarão o desempenho. Para obter mais informações, consulte [escolher o layout correto](~/xamarin-forms/deploy-test/performance.md#choose-the-correct-layout).

Este é o código C# equivalente:

```csharp
public class CombinedStackLayoutPageCS : ContentPage
{
    public CombinedStackLayoutPageCS()
    {
        Title = "Combined StackLayouts demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children =
            {
                new Label { Text = "Primary colors" },
                new Frame
                {
                    BorderColor = Color.Black,
                    Padding = new Thickness(5),
                    Content = new StackLayout
                    {
                        Orientation = StackOrientation.Horizontal,
                        Spacing = 15,
                        Children =
                        {
                            new BoxView { Color = Color.Red },
                            new Label { Text = "Red", FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)), VerticalOptions = LayoutOptions.Center }
                        }
                    }
                },
                new Frame
                {
                    BorderColor = Color.Black,
                    Padding = new Thickness(5),
                    Content = new StackLayout
                    {
                        Orientation = StackOrientation.Horizontal,
                        Spacing = 15,
                        Children =
                        {
                            new BoxView { Color = Color.Yellow },
                            new Label { Text = "Yellow", FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)), VerticalOptions = LayoutOptions.Center }
                        }
                    }
                },
                new Frame
                {
                    BorderColor = Color.Black,
                    Padding = new Thickness(5),
                    Content = new StackLayout
                    {
                        Orientation = StackOrientation.Horizontal,
                        Spacing = 15,
                        Children =
                        {
                            new BoxView { Color = Color.Blue },
                            new Label { Text = "Blue", FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)), VerticalOptions = LayoutOptions.Center }
                        }
                    }
                },
                // ...
            }
        };
    }
}
```

## <a name="related-links"></a>Links relacionados

- [Demonstrações do StackLayout (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stacklayoutdemos)
- [Opções de layout emXamarin.Forms](layout-options.md)
- [Escolher um Xamarin.Forms layout](choose-layout.md)
- [Melhorar o Xamarin.Forms desempenho do aplicativo](~/xamarin-forms/deploy-test/performance.md)
