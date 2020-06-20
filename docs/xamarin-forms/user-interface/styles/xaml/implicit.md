---
title: Estilos implícitos emXamarin.Forms
description: Um estilo implícito é aquele usado por todos os controles do mesmo TargetType, sem a necessidade de cada controle para referenciar o estilo.
ms.prod: xamarin
ms.assetid: 02A75F3B-4389-49D4-A2F4-AFD473A4A161
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3fb6ea40ced93103ec9cc92fa707f68c674d7826
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139003"
---
# <a name="implicit-styles-in-xamarinforms"></a>Estilos implícitos emXamarin.Forms

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Um estilo implícito é aquele usado por todos os controles do mesmo TargetType, sem a necessidade de cada controle para referenciar o estilo._

## <a name="create-an-implicit-style-in-xaml"></a>Criar um estilo implícito em XAML

Para declarar um [`Style`](xref:Xamarin.Forms.Style) no nível da página, um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) deve ser adicionado à página e uma ou mais `Style` declarações podem ser incluídas no `ResourceDictionary` . Um `Style` é tornado *implícito* por não especificar um `x:Key` atributo. Em seguida, o estilo será aplicado aos elementos visuais que correspondem `TargetType` exatamente, mas não aos elementos que são derivados do `TargetType` valor.

O exemplo de código a seguir mostra um estilo *implícito* declarado em XAML em uma página `ResourceDictionary` e aplicado às instâncias da página [`Entry`](xref:Xamarin.Forms.Entry) :

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

O [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) define um único estilo *implícito* que é aplicado às instâncias da página [`Entry`](xref:Xamarin.Forms.Entry) . O `Style` é usado para exibir texto azul em um plano de fundo amarelo, enquanto também define outras opções de aparência. O `Style` é adicionado à página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) sem especificar um `x:Key` atributo. Portanto, o `Style` é aplicado a todas as `Entry` instâncias implicitamente, pois elas correspondem à [`TargetType`](xref:Xamarin.Forms.Style.TargetType) Propriedade do `Style` exatamente. No entanto, o `Style` não é aplicado à `CustomEntry` instância, que é uma subclasse `Entry` . Isso resulta na aparência mostrada nas capturas de tela seguir:

[![Exemplo de estilos implícitos](implicit-images/implicit-styles.png)](implicit-images/implicit-styles-large.png#lightbox)

Além disso, a quarta [`Entry`](xref:Xamarin.Forms.Entry) substitui as [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) Propriedades e do estilo implícito por valores diferentes `Color` .

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

Neste exemplo, o *implícito* [`Style`](xref:Xamarin.Forms.Style) é atribuído à [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) coleção do [`StackLayout`](xref:Xamarin.Forms.StackLayout) controle. O estilo *implícito* pode ser aplicado ao controle e a seus filhos.

Para obter informações sobre como criar estilos em um aplicativo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , consulte [estilos globais](~/xamarin-forms/user-interface/styles/application.md).

## <a name="create-an-implicit-style-in-c35"></a>Criar um estilo implícito em C&#35;

[`Style`](xref:Xamarin.Forms.Style)as instâncias podem ser adicionadas à coleção de uma página [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) em C# criando um novo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) e, em seguida, adicionando as `Style` instâncias `ResourceDictionary` ao, conforme mostrado no exemplo de código a seguir:

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

O construtor define um único estilo *implícito* que é aplicado às instâncias da página [`Entry`](xref:Xamarin.Forms.Entry) . O `Style` é usado para exibir texto azul em um plano de fundo amarelo, enquanto também define outras opções de aparência. O `Style` é adicionado à página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) sem especificar uma cadeia de `key` caracteres. Portanto, o `Style` é aplicado a todas as `Entry` instâncias implicitamente, pois elas correspondem à [`TargetType`](xref:Xamarin.Forms.Style.TargetType) Propriedade do `Style` exatamente. No entanto, o `Style` não é aplicado à `CustomEntry` instância, que é uma subclasse `Entry` .

## <a name="apply-a-style-to-derived-types"></a>Aplicar um estilo a tipos derivados

A [`Style.ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) propriedade permite que um estilo seja aplicado a controles que são derivados do tipo base referenciado pela [`TargetType`](xref:Xamarin.Forms.Style.TargetType) propriedade. Portanto, definir essa propriedade como `true` permite que um único estilo direcione vários tipos, desde que os tipos derivem do tipo base especificado na `TargetType` propriedade.

O exemplo a seguir mostra um estilo implícito que define a cor do plano de fundo de [`Button`](xref:Xamarin.Forms.Button) instâncias como vermelho:

```xaml
<Style TargetType="Button"
       ApplyToDerivedTypes="True">
    <Setter Property="BackgroundColor"
            Value="Red" />
</Style>
```

Colocar esse estilo em nível de página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) resultará na aplicação de todas as [`Button`](xref:Xamarin.Forms.Button) instâncias na página e também aos controles que derivam de `Button` . No entanto, se a [`ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) Propriedade permanecesse desdefinida, o estilo só seria aplicado a `Button` instâncias.

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

- [Extensões de marcação do XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos básicos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Trabalhando com estilos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
