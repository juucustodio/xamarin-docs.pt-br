---
title: Estilos implícitos no xamarin. Forms
description: Um estilo implícito é aquele que é usado por todos os controles de TargetType mesmo, sem a necessidade de cada controle para fazer referência ao estilo.
ms.prod: xamarin
ms.assetid: 02A75F3B-4389-49D4-A2F4-AFD473A4A161
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 277be51c242521f52e9b1e162226ae8137e7b133
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995508"
---
# <a name="implicit-styles-in-xamarinforms"></a>Estilos implícitos no xamarin. Forms

_Um estilo implícito é aquele que é usado por todos os controles de TargetType mesmo, sem a necessidade de cada controle para fazer referência ao estilo._

## <a name="creating-an-implicit-style-in-xaml"></a>Criando um estilo implícito no XAML

Para declarar uma [ `Style` ](xref:Xamarin.Forms.Style) no nível da página, uma [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) deve ser adicionado à página e, em seguida, um ou mais `Style` declarações podem ser incluídas no `ResourceDictionary`. Um `Style` é feita *implícita* não especificando um `x:Key` atributo. O estilo será aplicado, em seguida, para elementos visuais que correspondem a `TargetType` exatamente, mas não para elementos que são derivados da `TargetType` valor.

O seguinte exemplo de código mostra uma *implícita* estilo declarado em XAML em uma página `ResourceDictionary`e aplicadas para a página [ `Entry` ](xref:Xamarin.Forms.Entry) instâncias:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" Icon="xaml.png">
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

O [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) define um único *implícita* estilo é aplicado para a página [ `Entry` ](xref:Xamarin.Forms.Entry) instâncias. O `Style` é usado para exibir o texto azul em um plano de fundo amarelo, enquanto também definir outras opções de aparência. O `Style` é adicionado para a página [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) sem especificar um `x:Key` atributo. Portanto, o `Style` é aplicada a todos os a `Entry` instâncias implicitamente, conforme eles correspondem a [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) propriedade do `Style` exatamente. No entanto, o `Style` não é aplicada para o `CustomEntry` instância, que é uma subclasse `Entry`. Isso resulta na exibição mostrada nas capturas de tela seguir:

[![](implicit-images/implicit-styles.png "Exemplo de estilos implícitos")](implicit-images/implicit-styles-large.png#lightbox "estilos implícitos de exemplo")

Além disso, o quarto [ `Entry` ](xref:Xamarin.Forms.Entry) substitui o [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor) e [ `TextColor` ](xref:Xamarin.Forms.Entry.TextColor) propriedades do estilo implícito para diferentes `Color`valores.

### <a name="creating-an-implicit-style-at-the-control-level"></a>Criando um estilo implícito no controle de nível

Além de criar *implícita* estilos no nível da página, eles também podem ser criados no nível de controle, conforme mostrado no exemplo de código a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" Icon="xaml.png">
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

Neste exemplo, o *implícita* [ `Style` ](xref:Xamarin.Forms.Style) é atribuído para o [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) coleção da [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)controle. O *implícita* estilo, em seguida, pode ser aplicado ao controle e seus filhos.

Para obter informações sobre a criação de estilos em um aplicativo [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), consulte [estilos globais](~/xamarin-forms/user-interface/styles/application.md).

## <a name="creating-an-implicit-style-in-c35"></a>Criando um estilo implícito em C&#35;

[`Style`](xref:Xamarin.Forms.Style) instâncias podem ser adicionadas a uma página [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) coleção no c#, criando um novo [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)e, em seguida, adicionando o `Style` para instâncias de `ResourceDictionary`, conforme mostrado no exemplo de código a seguir:

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

O construtor define uma única *implícita* estilo é aplicado para a página [ `Entry` ](xref:Xamarin.Forms.Entry) instâncias. O `Style` é usado para exibir o texto azul em um plano de fundo amarelo, enquanto também definir outras opções de aparência. O `Style` é adicionado para a página [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) sem especificar um `key` cadeia de caracteres. Portanto, o `Style` é aplicada a todos os a `Entry` instâncias implicitamente, conforme eles correspondem a [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) propriedade do `Style` exatamente. No entanto, o `Style` não é aplicada para o `CustomEntry` instância, que é uma subclasse `Entry`.

## <a name="summary"></a>Resumo

Uma *implícita* estilo é aquele que é usado por todos os elementos visuais do mesmo [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType), sem a necessidade de cada controle para fazer referência ao estilo. Um `Style` é feita *implícita* não especificando um `x:Key` atributo. Em vez disso, o `x:Key` atributo se tornará automaticamente o valor de [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) propriedade.



## <a name="related-links"></a>Links relacionados

- [Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos básicos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [Trabalhar com estilos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Dicionário de recurso](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
