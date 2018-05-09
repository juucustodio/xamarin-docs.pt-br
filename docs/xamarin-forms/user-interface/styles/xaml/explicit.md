---
title: Estilos explícitos
description: Um estilo explícito é aquele que é aplicada seletivamente a controles, definindo suas propriedades de estilo.
ms.prod: xamarin
ms.assetid: C0DF9F8F-B431-4374-A574-325BC3C41A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 53f87fe9dfbf8284055d28fd87bab7bad02c1fd8
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="explicit-styles"></a>Estilos explícitos

_Um estilo explícito é aquele que é aplicada seletivamente a controles, definindo suas propriedades de estilo._

## <a name="creating-an-explicit-style-in-xaml"></a>Criação de um estilo explícito em XAML

Para declarar um [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) no nível da página, um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) devem ser adicionados para a página e, em seguida, um ou mais `Style` declarações podem ser incluídas no `ResourceDictionary`. Um `Style` é feita *explícita* , fornecendo sua declaração um `x:Key` atributo, que dá a ele uma chave descritiva no `ResourceDictionary`. *Explícita* estilos, em seguida, devem ser aplicados a elementos visuais específicos definindo seus [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propriedades.

O seguinte exemplo de código mostra *explícita* estilos declarado em XAML em uma página `ResourceDictionary` e aplicada na página [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instâncias:

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

O [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) define três *explícita* estilos que são aplicados para a página [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instâncias. Cada `Style` é usado para exibir texto em uma cor diferente, e também define a fonte de opções de layout de tamanho e horizontal e vertical. Cada `Style` é aplicada a outro `Label` definindo seu [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propriedades usando o `StaticResource` extensão de marcação. Isso resulta na exibição mostrada nas capturas de tela seguir:

[![](explicit-images/explicit-styles.png "Exemplo de estilos explícita")](explicit-images/explicit-styles-large.png#lightbox "estilos explícita de exemplo")

Além disso, o último [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) tem um [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) aplicada a ele, mas também substitui o [ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/) propriedade para um diferentes`Color`valor.

### <a name="creating-an-explicit-style-at-the-control-level"></a>Criando um estilo explícito no controle de nível

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

Neste exemplo, o *explícita* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instâncias recebem o [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) coleção do [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) controle. Os estilos, em seguida, podem ser aplicados para o controle e seus filhos.

Para obter informações sobre como criar estilos em um aplicativo [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/), consulte [estilos globais](~/xamarin-forms/user-interface/styles/application.md).

## <a name="creating-an-explicit-style-in-c35"></a>Criando um estilo explícito em C&#35;

[`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instâncias podem ser adicionadas a uma página [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) coleção em c#, criando um novo [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)e, em seguida, adicionando o `Style` instâncias para o `ResourceDictionary`, conforme mostrado no exemplo de código a seguir:

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Red  }
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
                new Label { Text = "and an explicit style override",
                            Style = (Style)Resources ["labelBlueStyle"], TextColor = Color.Teal }
            }
        };
    }
}
```

O construtor define três *explícita* estilos que são aplicados para a página [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instâncias. Cada *explícita* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) é adicionada para o [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) usando o [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ResourceDictionary.Add/p/System.String/System.Object/) método, especificando um `key` cadeia de caracteres para referir-se a `Style` instância. Cada `Style` é aplicada a outro `Label` definindo suas [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propriedades.

No entanto, não há nenhuma vantagem em usar um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) aqui. Em vez disso, [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instâncias podem ser atribuídas diretamente para o [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propriedades dos elementos necessários visual e o `ResourceDictionary` poderá ser removido, conforme mostrado a seguir exemplo de código:

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

O construtor define três *explícita* estilos que são aplicados para a página [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instâncias. Cada `Style` é usado para exibir texto em uma cor diferente, e também define a fonte de opções de layout de tamanho e horizontal e vertical. Cada `Style` é aplicada a outro `Label` definindo seu [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propriedades. Além disso, o último `Label` tem um `Style` aplicada a ele, mas também substitui o `TextColor` propriedade para outro `Color` valor.

## <a name="summary"></a>Resumo

Um [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) é feita *explícita* , fornecendo sua declaração um `x:Key` de atributos e, em seguida, seletivamente aplicá-lo a controles, definindo suas [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propriedades.



## <a name="related-links"></a>Links relacionados

- [Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos básicos (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [Trabalhando com estilos (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Dicionário de recurso](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [Estilo](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Setter](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
