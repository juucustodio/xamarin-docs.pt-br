---
title: Estilos explícitos no xamarin. Forms
description: Um estilo explícito é aquele que é aplicada aos controles seletivamente, definindo suas propriedades de estilo. Este artigo explica como consumir estilos explícitos em um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: C0DF9F8F-B431-4374-A574-325BC3C41A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 5b3d6f486f802fda1a384b7a8e53b104c1251fdd
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53058240"
---
# <a name="explicit-styles-in-xamarinforms"></a>Estilos explícitos no xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)

_Um estilo explícito é aquele que é aplicada aos controles seletivamente, definindo suas propriedades de estilo._

## <a name="creating-an-explicit-style-in-xaml"></a>Criando um estilo explícito em XAML

Para declarar uma [ `Style` ](xref:Xamarin.Forms.Style) no nível da página, uma [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) deve ser adicionado à página e, em seguida, um ou mais `Style` declarações podem ser incluídas no `ResourceDictionary`. Um `Style` é feita *explícito* fornecendo sua declaração de uma `x:Key` atributo, que concede a ele uma chave descritiva no `ResourceDictionary`. *Explícito* estilos, em seguida, devem ser aplicados a elementos visuais específicos definindo seus [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) propriedades.

O seguinte exemplo de código mostra *explícita* estilos declarados em XAML em uma página `ResourceDictionary` e aplicadas para a página [ `Label` ](xref:Xamarin.Forms.Label) instâncias:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="labelRedStyle" TargetType="Label">
                <Setter Property="HorizontalOptions"
                        Value="Center" />
                <Setter Property="VerticalOptions"
                        Value="CenterAndExpand" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
            <Style x:Key="labelGreenStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Green" />
            </Style>
            <Style x:Key="labelBlueStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   Style="{StaticResource labelRedStyle}" />
            <Label Text="are demonstrating"
                   Style="{StaticResource labelGreenStyle}" />
            <Label Text="explicit styles,"
                   Style="{StaticResource labelBlueStyle}" />
            <Label Text="and an explicit style override"
                   Style="{StaticResource labelBlueStyle}"
                   TextColor="Teal" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

O [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) define três *explícita* estilos que são aplicados para a página [ `Label` ](xref:Xamarin.Forms.Label) instâncias. Cada `Style` é usado para exibir texto em uma cor diferente, e também define a fonte de opções de layout de tamanho e horizontal e vertical. Cada `Style` é aplicado a um outro `Label` definindo seu [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) propriedades usando o `StaticResource` extensão de marcação. Isso resulta na exibição mostrada nas capturas de tela seguir:

[![](explicit-images/explicit-styles.png "Exemplo de estilos explícitos")](explicit-images/explicit-styles-large.png#lightbox "exemplo estilos explícitos")

Além disso, o último [ `Label` ](xref:Xamarin.Forms.Label) tem um [ `Style` ](xref:Xamarin.Forms.Style) aplicado a ele, mas também substitui o [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor) propriedade para um diferente `Color`valor.

### <a name="creating-an-explicit-style-at-the-control-level"></a>Criando um estilo explícito para o controle nível

Além de criar *explícita* estilos no nível da página, eles também podem ser criados no nível de controle, conforme mostrado no exemplo de código a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" Icon="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="labelRedStyle" TargetType="Label">
                      ...
                    </Style>
                    ...
                </ResourceDictionary>
            </StackLayout.Resources>
            <Label Text="These labels" Style="{StaticResource labelRedStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Neste exemplo, o *explícita* [ `Style` ](xref:Xamarin.Forms.Style) instâncias são atribuídas para o [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) coleção da [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) controle. Os estilos, em seguida, podem ser aplicados para o controle e seus filhos.

Para obter informações sobre a criação de estilos em um aplicativo [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), consulte [estilos globais](~/xamarin-forms/user-interface/styles/application.md).

## <a name="creating-an-explicit-style-in-c35"></a>Criando um estilo explícito em C&#35;

[`Style`](xref:Xamarin.Forms.Style) instâncias podem ser adicionadas a uma página [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) coleção no c#, criando um novo [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)e, em seguida, adicionando o `Style` para instâncias de `ResourceDictionary`, conforme mostrado no exemplo de código a seguir:

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Red    }
            }
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Green }
            }
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Blue }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("labelRedStyle", labelRedStyle);
        Resources.Add ("labelGreenStyle", labelGreenStyle);
        Resources.Add ("labelBlueStyle", labelBlueStyle);
        ...

        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels",
                            Style = (Style)Resources ["labelRedStyle"] },
                new Label { Text = "are demonstrating",
                            Style = (Style)Resources ["labelGreenStyle"] },
                new Label { Text = "explicit styles,",
                            Style = (Style)Resources ["labelBlueStyle"] },
                new Label {    Text = "and an explicit style override",
                            Style = (Style)Resources ["labelBlueStyle"], TextColor = Color.Teal }
            }
        };
    }
}
```

O construtor define três *explícita* estilos que são aplicados para a página [ `Label` ](xref:Xamarin.Forms.Label) instâncias. Cada *explícita* [ `Style` ](xref:Xamarin.Forms.Style) é adicionado para o [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) usando o [ `Add` ](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) método, especificando um `key` cadeia de caracteres para se referir a `Style` instância. Cada `Style` é aplicado a um outro `Label` definindo suas [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) propriedades.

No entanto, não há nenhuma vantagem em usar um [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) aqui. Em vez disso, [ `Style` ](xref:Xamarin.Forms.Style) instâncias podem ser atribuídas diretamente para o [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) propriedades dos elementos visuais necessários e o `ResourceDictionary` poderá ser removido, conforme mostrado no exemplo a seguir exemplo de código:

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            ...
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            ...
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            ...
        };
        ...
        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels", Style = labelRedStyle },
                new Label { Text = "are demonstrating", Style = labelGreenStyle },
                new Label { Text = "explicit styles,", Style = labelBlueStyle },
                new Label { Text = "and an explicit style override", Style = labelBlueStyle,
                            TextColor = Color.Teal }
            }
        };
    }
}
```

O construtor define três *explícita* estilos que são aplicados para a página [ `Label` ](xref:Xamarin.Forms.Label) instâncias. Cada `Style` é usado para exibir texto em uma cor diferente, e também define a fonte de opções de layout de tamanho e horizontal e vertical. Cada `Style` é aplicado a um outro `Label` definindo seu [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) propriedades. Além disso, o último `Label` tem uma `Style` aplicados a ele, mas também substitui o `TextColor` propriedade em outro `Color` valor.

## <a name="summary"></a>Resumo

Um [ `Style` ](xref:Xamarin.Forms.Style) é feita *explícita* , fornecendo sua declaração de um `x:Key` atributo e, em seguida, seletivamente aplicá-lo a controles, definindo suas [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) propriedades.



## <a name="related-links"></a>Links relacionados

- [Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos básicos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [Trabalhar com estilos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Dicionário de recurso](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
