---
title: Classes de estilo do xamarin. Forms
description: Classes de estilo do xamarin. Forms permitem que vários estilos a ser aplicado a um controle, sem recorrer à herança de estilo.
ms.prod: xamarin
ms.assetid: 4762401E-2B48-48F1-B6E4-61F7AF8AA46F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
ms.openlocfilehash: a3ef0f96bcc955dcac4231f9eb9cf1ab16ee61aa
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65925281"
---
# <a name="xamarinforms-style-classes"></a>Classes de estilo do xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)

_Classes de estilo do xamarin. Forms permitem que vários estilos a ser aplicado a um controle, sem recorrer à herança de estilo._

## <a name="create-style-classes"></a>Criar classes de estilo

Uma classe de estilo que pode ser criada, definindo o [ `Class` ](xref:Xamarin.Forms.Style.Class) propriedade em uma [ `Style` ](xref:Xamarin.Forms.Style) para um `string` que representa o nome de classe. A vantagem que oferece, sobre a definição de um estilo explícito usando o `x:Key` de atributo, é que várias classes de estilo podem ser aplicadas a um [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).

> [!IMPORTANT]
> Vários estilos podem compartilhar o mesmo nome de classe, desde que eles se destinam a diferentes tipos. Isso permite que várias classes de estilo, que são nomeadas de maneira idêntica, para tipos diferentes de destino.

O exemplo a seguir mostra três [ `BoxView` ](xref:Xamarin.Forms.BoxView) classes, estilo e uma [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) classe de estilo:

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

O `Separator`, `Rounded`, e `Circle` cada conjunto de classes do estilo [ `BoxView` ](xref:Xamarin.Forms.BoxView) propriedades com valores específicos.

O `Rotated` classe de estilo tem uma [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) dos [ `VisualElement` ](xref:Xamarin.Forms.VisualElement), que significa que ele só pode ser aplicado a `VisualElement` instâncias. No entanto, sua [ `ApplyToDerivedTypes` ](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) estiver definida como `true`, que garante que ele pode ser aplicado a todos os controles que derivam `VisualElement`, como [ `BoxView` ](xref:Xamarin.Forms.BoxView). Para obter mais informações sobre como aplicar um estilo a um tipo derivado, consulte [aplicar um estilo a tipos derivados](implicit.md#apply-a-style-to-derived-types).

O código C# equivalente é:

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

Classes de estilo que podem ser consumidas, definindo o [ `StyleClass` ](xref:Xamarin.Forms.NavigableElement.StyleClass) propriedade do controle, que é do tipo `IList<string>`, para obter uma lista dos nomes de classe de estilo. As classes de estilo serão aplicadas, desde que o tipo do controle corresponde a [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) das classes de estilo.

O exemplo a seguir mostra três [ `BoxView` ](xref:Xamarin.Forms.BoxView) instâncias, cada conjunto às classes de estilo diferente:

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

Neste exemplo, a primeira [ `BoxView` ](xref:Xamarin.Forms.BoxView) estilo é feito para ser um separador de linha, enquanto o terceiro `BoxView` é circular. O segundo `BoxView` tem duas classes do estilo aplicado a ele, que dê cantos arredondado de it e girá-lo 45 graus:

![](style-class-images/boxviews.png "BoxViews estilizada com classes de estilo")

> [!IMPORTANT]
> Várias classes de estilo podem ser aplicadas a um controle porque o [ `StyleClass` ](xref:Xamarin.Forms.NavigableElement.StyleClass) propriedade é do tipo `IList<string>`. Quando isso ocorre, as classes de estilo são aplicadas na lista ordem crescente. Portanto, quando várias classes de estilo define as propriedades idênticas, a propriedade na classe de estilo que está em posição mais elevada lista terá precedência.

O código C# equivalente é:

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

- [Estilos básicos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
