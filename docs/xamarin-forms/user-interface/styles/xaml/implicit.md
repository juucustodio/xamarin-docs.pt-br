---
title: Estilos implícitos
description: Um estilo implícito é aquele que é usado por todos os controles de TargetType o mesmo, sem a necessidade de cada controle para referenciar o estilo.
ms.prod: xamarin
ms.assetid: 02A75F3B-4389-49D4-A2F4-AFD473A4A161
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: d9c9f8816ea45ac122829739ad5134cc740263df
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="implicit-styles"></a>Estilos implícitos

_Um estilo implícito é aquele que é usado por todos os controles de TargetType o mesmo, sem a necessidade de cada controle para referenciar o estilo._

## <a name="creating-an-implicit-style-in-xaml"></a>Criando um estilo implícito em XAML

Para declarar um [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) no nível da página, um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) devem ser adicionados para a página e, em seguida, um ou mais `Style` declarações podem ser incluídas no `ResourceDictionary`. Um `Style` é feita *implícita* não especificando um `x:Key` atributo. O estilo, em seguida, será aplicado a elementos visuais que correspondem a `TargetType` exatamente, mas não para elementos que são derivados do `TargetType` valor.

O seguinte exemplo de código mostra uma *implícita* estilo declarado em XAML em uma página `ResourceDictionary`e aplicada na página [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) instâncias:

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

O [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) define um único *implícita* estilo aplicado à página de [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) instâncias. O `Style` é usado para exibir texto azul em um plano de fundo amarelo, e também define outras opções de aparência. O `Style` é adicionado para a página [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) sem especificar um `x:Key` atributo. Portanto, o `Style` é aplicado a todos os a `Entry` instâncias implicitamente como eles correspondem a [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) propriedade do `Style` exatamente. No entanto, o `Style` não é aplicada para o `CustomEntry` instância, que é uma subclasse `Entry`. Isso resulta na exibição mostrada nas capturas de tela seguir:

[![](implicit-images/implicit-styles.png "Exemplo de estilos implícitos")](implicit-images/implicit-styles-large.png#lightbox "estilos implícitos exemplo")

Além disso, o quarto [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) substitui o [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/) e [ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.TextColor/) propriedades do estilo implícita para diferentes `Color`valores.

### <a name="creating-an-implicit-style-at-the-control-level"></a>Criando um estilo implícito no controle nível

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

Neste exemplo, o *implícita* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) é atribuído para o [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) coleção do [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)controle. O *implícita* estilo pode então ser aplicado ao controle e seus filhos.

Para obter informações sobre como criar estilos em um aplicativo [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/), consulte [estilos globais](~/xamarin-forms/user-interface/styles/application.md).

## <a name="creating-an-implicit-style-in-c35"></a>Criando um estilo implícito em C&#35;

[`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instâncias podem ser adicionadas a uma página [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) coleção em c#, criando um novo [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)e, em seguida, adicionando o `Style` instâncias para o `ResourceDictionary`, conforme mostrado no exemplo de código a seguir:

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

O construtor define um único *implícita* estilo aplicado à página de [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) instâncias. O `Style` é usado para exibir texto azul em um plano de fundo amarelo, e também define outras opções de aparência. O `Style` é adicionado para a página [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) sem especificar um `key` cadeia de caracteres. Portanto, o `Style` é aplicado a todos os a `Entry` instâncias implicitamente como eles correspondem a [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) propriedade do `Style` exatamente. No entanto, o `Style` não é aplicada para o `CustomEntry` instância, que é uma subclasse `Entry`.

## <a name="summary"></a>Resumo

Um *implícita* estilo é aquele que é usado por todos os elementos visuais do mesmo [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/), sem a necessidade de cada controle para referenciar o estilo. Um `Style` é feita *implícita* não especificando um `x:Key` atributo. Em vez disso, o `x:Key` atributo se tornará automaticamente o valor de [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) propriedade.



## <a name="related-links"></a>Links relacionados

- [Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos básicos (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [Trabalhando com estilos (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Dicionário de recurso](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [Estilo](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Setter](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
