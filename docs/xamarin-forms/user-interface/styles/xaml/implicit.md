---
title: Estilos implícitos no xamarin. Forms
description: Um estilo implícito é aquele que é usado por todos os controles de TargetType mesmo, sem a necessidade de cada controle para fazer referência ao estilo.
ms.prod: xamarin
ms.assetid: 02A75F3B-4389-49D4-A2F4-AFD473A4A161
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
ms.openlocfilehash: d58ba81596cccf470b7246514d71f35968599880
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78918129"
---
# <a name="implicit-styles-in-xamarinforms"></a>Estilos implícitos no xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Um estilo implícito é aquele usado por todos os controles do mesmo TargetType, sem a necessidade de cada controle para referenciar o estilo._

## <a name="create-an-implicit-style-in-xaml"></a>Criar um estilo implícito em XAML

Para declarar um [`Style`](xref:Xamarin.Forms.Style) no nível de página, um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) deve ser adicionado à página e, em seguida, uma ou mais declarações de `Style` podem ser incluídas no `ResourceDictionary`. Uma `Style` torna-se *implícita* por não especificar um atributo `x:Key`. Em seguida, o estilo será aplicado aos elementos visuais que correspondem exatamente ao `TargetType`, mas não aos elementos derivados do valor de `TargetType`.

O exemplo de código a seguir mostra um estilo *implícito* declarado em XAML no `ResourceDictionary`de uma página e aplicado às instâncias de [`Entry`](xref:Xamarin.Forms.Entry) da página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Entry">
                <Setter Property="HorizontalOptions" Value="Fill" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                <Setter Property="BackgroundColor" Value="Yellow" />
                <Setter Property="FontAttributes" Value="Italic" />
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Entry Text="These entries" />
            <Entry Text="are demonstrating" />
            <Entry Text="implicit styles," />
            <Entry Text="and an implicit style override" BackgroundColor="Lime" TextColor="Red" />
            <local:CustomEntry Text="Subclassed Entry is not receiving the style" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

O [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) define um único estilo *implícito* que é aplicado às instâncias de [`Entry`](xref:Xamarin.Forms.Entry) da página. O `Style` é usado para exibir texto azul em um plano de fundo amarelo, enquanto também define outras opções de aparência. O `Style` é adicionado à [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) da página sem especificar um atributo `x:Key`. Portanto, a `Style` é aplicada a todas as instâncias de `Entry` implicitamente, pois correspondem à propriedade [`TargetType`](xref:Xamarin.Forms.Style.TargetType) do `Style` exatamente. No entanto, o `Style` não é aplicado à instância de `CustomEntry`, que é uma `Entry`de subclasse. Isso resulta na aparência mostrada nas capturas de tela seguir:

[Exemplo de ![estilos implícitos](implicit-images/implicit-styles.png)](implicit-images/implicit-styles-large.png#lightbox)

Além disso, a quarta [`Entry`](xref:Xamarin.Forms.Entry) substitui as propriedades [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) e [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) do estilo implícito por diferentes valores de `Color`.

### <a name="create-an-implicit-style-at-the-control-level"></a>Criar um estilo implícito no nível de controle

Além de criar estilos *implícitos* no nível de página, eles também podem ser criados no nível de controle, conforme mostrado no exemplo de código a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style TargetType="Entry">
                        <Setter Property="HorizontalOptions" Value="Fill" />
                        ...
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            <Entry Text="These entries" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Neste exemplo, o`Style`*implícito* [`Style`](xref:Xamarin.Forms.Style) é atribuído à coleção de [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) do controle de [`StackLayout`](xref:Xamarin.Forms.StackLayout) . O estilo *implícito* pode ser aplicado ao controle e a seus filhos.

Para obter informações sobre como criar estilos no [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de um aplicativo, consulte [estilos globais](~/xamarin-forms/user-interface/styles/application.md).

## <a name="create-an-implicit-style-in-c35"></a>Criar um estilo implícito em C&#35;

[`Style`](xref:Xamarin.Forms.Style) instâncias podem ser adicionadas à coleção de [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) de uma C# página no criando um novo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)e, em seguida, adicionando as instâncias de `Style` à `ResourceDictionary`, conforme mostrado no exemplo de código a seguir:

```csharp
public class ImplicitStylesPageCS : ContentPage
{
    public ImplicitStylesPageCS ()
    {
        var entryStyle = new Style (typeof(Entry)) {
            Setters = {
                ...
                new Setter { Property = Entry.TextColorProperty, Value = Color.Blue }
            }
        };

        ...
        Resources = new ResourceDictionary ();
        Resources.Add (entryStyle);

        Content = new StackLayout {
            Children = {
                new Entry { Text = "These entries" },
                new Entry { Text = "are demonstrating" },
                new Entry { Text = "implicit styles," },
                new Entry { Text = "and an implicit style override", BackgroundColor = Color.Lime, TextColor = Color.Red },
                new CustomEntry  { Text = "Subclassed Entry is not receiving the style" }
            }
        };
    }
}
```

O construtor define um único estilo *implícito* que é aplicado às instâncias de [`Entry`](xref:Xamarin.Forms.Entry) da página. O `Style` é usado para exibir texto azul em um plano de fundo amarelo, enquanto também define outras opções de aparência. O `Style` é adicionado à [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) da página sem especificar uma cadeia de caracteres de `key`. Portanto, a `Style` é aplicada a todas as instâncias de `Entry` implicitamente, pois correspondem à propriedade [`TargetType`](xref:Xamarin.Forms.Style.TargetType) do `Style` exatamente. No entanto, o `Style` não é aplicado à instância de `CustomEntry`, que é uma `Entry`de subclasse.

## <a name="apply-a-style-to-derived-types"></a>Aplicar um estilo a tipos derivados

A propriedade [`Style.ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) permite que um estilo seja aplicado a controles que são derivados do tipo base referenciado pela propriedade [`TargetType`](xref:Xamarin.Forms.Style.TargetType) . Portanto, definir essa propriedade como `true` permite que um único estilo direcione vários tipos, desde que os tipos derivem do tipo base especificado na propriedade `TargetType`.

O exemplo a seguir mostra um estilo implícito que define a cor do plano de fundo das instâncias de [`Button`](xref:Xamarin.Forms.Button) como vermelho:

```xaml
<Style TargetType="Button"
       ApplyToDerivedTypes="True">
    <Setter Property="BackgroundColor"
            Value="Red" />
</Style>
```

Colocar esse estilo em um nível de página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) resultará na aplicação de todas as instâncias de [`Button`](xref:Xamarin.Forms.Button) na página e também aos controles que derivam de `Button`. No entanto, se a propriedade [`ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) permanecesse desdefinida, o estilo só seria aplicado a instâncias de `Button`.

Este é o código C# equivalente:

```csharp
var buttonStyle = new Style(typeof(Button))
{
    ApplyToDerivedTypes = true,
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.Red
        }
    }
};

Resources = new ResourceDictionary { buttonStyle };
```

## <a name="related-links"></a>Links relacionados

- [Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos básicos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Trabalhando com estilos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
