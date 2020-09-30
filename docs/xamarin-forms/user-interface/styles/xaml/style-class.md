---
title: Xamarin.Forms Classes de estilo
description: Xamarin.Forms as classes de estilo permitem que vários estilos sejam aplicados a um controle, sem reclassificar para a herança de estilo.
ms.prod: xamarin
ms.assetid: 4762401E-2B48-48F1-B6E4-61F7AF8AA46F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f100f98418b7e3cb82939bf67dda61b66cb5864e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557745"
---
# <a name="no-locxamarinforms-style-classes"></a>Xamarin.Forms Classes de estilo

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Xamarin.Forms as classes de estilo permitem que vários estilos sejam aplicados a um controle, sem reclassificar para a herança de estilo._

## <a name="create-style-classes"></a>Criar classes de estilo

Uma classe de estilo pode ser criada definindo a [`Class`](xref:Xamarin.Forms.Style.Class) propriedade em um [`Style`](xref:Xamarin.Forms.Style) para um `string` que representa o nome da classe. A vantagem que isso oferece, ao definir um estilo explícito usando o `x:Key` atributo, é que várias classes de estilo podem ser aplicadas a um [`VisualElement`](xref:Xamarin.Forms.VisualElement) .

> [!IMPORTANT]
> Vários estilos podem compartilhar o mesmo nome de classe, desde que eles tenham como alvo tipos diferentes. Isso habilita várias classes de estilo, que são nomeadas de forma idêntica, para direcionar tipos diferentes.

O exemplo a seguir mostra três [`BoxView`](xref:Xamarin.Forms.BoxView) classes de estilo e uma [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe de estilo:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="BoxView"
               Class="Separator">
            <Setter Property="BackgroundColor"
                    Value="#CCCCCC" />
            <Setter Property="HeightRequest"
                    Value="1" />
        </Style>

        <Style TargetType="BoxView"
               Class="Rounded">
            <Setter Property="BackgroundColor"
                    Value="#1FAECE" />
            <Setter Property="HorizontalOptions"
                    Value="Start" />
            <Setter Property="CornerRadius"
                    Value="10" />
        </Style>    

        <Style TargetType="BoxView"
               Class="Circle">
            <Setter Property="BackgroundColor"
                    Value="#1FAECE" />
            <Setter Property="WidthRequest"
                    Value="100" />
            <Setter Property="HeightRequest"
                    Value="100" />
            <Setter Property="HorizontalOptions"
                    Value="Start" />
            <Setter Property="CornerRadius"
                    Value="50" />
        </Style>

        <Style TargetType="VisualElement"
               Class="Rotated"
               ApplyToDerivedTypes="true">
            <Setter Property="Rotation"
                    Value="45" />
        </Style>        
    </ContentPage.Resources>
</ContentPage>
```

As `Separator` `Rounded` classes de estilo, e `Circle` cada definem [`BoxView`](xref:Xamarin.Forms.BoxView) Propriedades para valores específicos.

A `Rotated` classe de estilo tem um [`TargetType`](xref:Xamarin.Forms.Style.TargetType) de [`VisualElement`](xref:Xamarin.Forms.VisualElement) , o que significa que ela só pode ser aplicada a `VisualElement` instâncias. No entanto, sua [`ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) propriedade é definida como `true` , o que garante que ela possa ser aplicada a todos os controles que derivarem de `VisualElement` , como [`BoxView`](xref:Xamarin.Forms.BoxView) . Para obter mais informações sobre como aplicar um estilo a um tipo derivado, consulte [aplicar um estilo a tipos derivados](implicit.md#apply-a-style-to-derived-types).

Este é o código C# equivalente:

```csharp
var separatorBoxViewStyle = new Style(typeof(BoxView))
{
    Class = "Separator",
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.FromHex("#CCCCCC")
        },
        new Setter
        {
            Property = VisualElement.HeightRequestProperty,
            Value = 1
        }
    }
};

var roundedBoxViewStyle = new Style(typeof(BoxView))
{
    Class = "Rounded",
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.FromHex("#1FAECE")
        },
        new Setter
        {
            Property = View.HorizontalOptionsProperty,
            Value = LayoutOptions.Start
        },
        new Setter
        {
            Property = BoxView.CornerRadiusProperty,
            Value = 10
        }
    }
};

var circleBoxViewStyle = new Style(typeof(BoxView))
{
    Class = "Circle",
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.FromHex("#1FAECE")
        },
        new Setter
        {
            Property = VisualElement.WidthRequestProperty,
            Value = 100
        },
        new Setter
        {
            Property = VisualElement.HeightRequestProperty,
            Value = 100
        },
        new Setter
        {
            Property = View.HorizontalOptionsProperty,
            Value = LayoutOptions.Start
        },
        new Setter
        {
            Property = BoxView.CornerRadiusProperty,
            Value = 50
        }
    }
};

var rotatedVisualElementStyle = new Style(typeof(VisualElement))
{
    Class = "Rotated",
    ApplyToDerivedTypes = true,
    Setters =
    {
        new Setter
        {
            Property = VisualElement.RotationProperty,
            Value = 45
        }
    }
};

Resources = new ResourceDictionary
{
    separatorBoxViewStyle,
    roundedBoxViewStyle,
    circleBoxViewStyle,
    rotatedVisualElementStyle
};
```

## <a name="consume-style-classes"></a>Consumir classes de estilo

As classes de estilo podem ser consumidas definindo a [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) Propriedade do controle, que é do tipo `IList<string>` , para uma lista de nomes de classe de estilo. As classes de estilo serão aplicadas, desde que o tipo do controle corresponda às [`TargetType`](xref:Xamarin.Forms.Style.TargetType) classes de estilo.

O exemplo a seguir mostra três [`BoxView`](xref:Xamarin.Forms.BoxView) instâncias, cada uma definida para classes de estilo diferentes:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        ...
    </ContentPage.Resources>
    <StackLayout Margin="20">
        <BoxView StyleClass="Separator" />       
        <BoxView WidthRequest="100"
                 HeightRequest="100"
                 HorizontalOptions="Center"
                 StyleClass="Rounded, Rotated" />
        <BoxView HorizontalOptions="Center"
                 StyleClass="Circle" />
    </StackLayout>
</ContentPage>    
```

Neste exemplo, o primeiro [`BoxView`](xref:Xamarin.Forms.BoxView) é estilizado para ser um separador de linha, enquanto o terceiro `BoxView` é circular. O segundo `BoxView` tem duas classes Style aplicadas a ela, que dá a ela cantos arredondados e gira 45 graus:

![BoxViews estilo com classes de estilo](style-class-images/boxviews.png)

> [!IMPORTANT]
> Várias classes de estilo podem ser aplicadas a um controle porque a [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) propriedade é do tipo `IList<string>` . Quando isso ocorre, as classes de estilo são aplicadas em ordem de lista crescente. Portanto, quando várias classes de estilo definem propriedades idênticas, a propriedade na classe Style que está na posição da lista mais alta terá precedência.

Este é o código C# equivalente:

```csharp
...
Content = new StackLayout
{
    Children =
    {
        new BoxView { StyleClass = new [] { "Separator" } },
        new BoxView { WidthRequest = 100, HeightRequest = 100, HorizontalOptions = LayoutOptions.Center, StyleClass = new [] { "Rounded", "Rotated" } },
        new BoxView { HorizontalOptions = LayoutOptions.Center, StyleClass = new [] { "Circle" } }
    }
};
```

## <a name="related-links"></a>Links relacionados

- [Estilos básicos (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)