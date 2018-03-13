---
title: "Herança de estilo"
description: "Estilos podem herdar de outros estilos para reduzir a duplicação e habilitar a reutilização."
ms.topic: article
ms.prod: xamarin
ms.assetid: 67A3A39C-8CC0-446D-8162-FFA73582D3B8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: e57f19d1eb66e22badb418d4584f5654904c7ade
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="style-inheritance"></a>Herança de estilo

_Estilos podem herdar de outros estilos para reduzir a duplicação e habilitar a reutilização._

## <a name="style-inheritance-in-xaml"></a>Herança de estilo em XAML

Herança de estilo é efetuada, definindo a [ `Style.BasedOn` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BasedOn/) propriedade um existente [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/). Em XAML, isso é feito definindo o `BasedOn` propriedade para um `StaticResource` extensão de marcação que faz referência a um criado anteriormente `Style`. Em c#, isso é feito definindo o `BasedOn` propriedade para um `Style` instância.

Estilos de herdam de um estilo de base podem incluir [ `Setter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/) instâncias para novas propriedades, ou usá-los para substituir os estilos do estilo de base. Além disso, estilos que herdam de um estilo de base devem ter como destino o mesmo tipo ou um tipo derivado do tipo de destino do estilo de base. Por exemplo, se um destino de estilo base [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) instâncias, estilos que são baseados no estilo de base podem direcionar `View` instâncias ou tipos que derivam de `View` classe, como [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) e [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) instâncias.

O código a seguir demonstra *explícita* herança de estilo em uma página XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
                <Setter Property="HorizontalOptions"
                        Value="Center" />
                <Setter Property="VerticalOptions"
                        Value="CenterAndExpand" />
            </Style>
            <Style x:Key="labelStyle" TargetType="Label"
                   BasedOn="{StaticResource baseStyle}">
                ...
                <Setter Property="TextColor" Value="Teal" />
            </Style>
            <Style x:Key="buttonStyle" TargetType="Button"
                   BasedOn="{StaticResource baseStyle}">
                <Setter Property="BorderColor" Value="Lime" />
                ...
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   Style="{StaticResource labelStyle}" />
            ...
            <Button Text="So is the button"
                    Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

O `baseStyle` destinos [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) instâncias e define o [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) e [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propriedades. O `baseStyle` não definida diretamente em qualquer controle. Em vez disso, `labelStyle` e `buttonStyle` herdam, definir valores de propriedade associável adicionais. O `labelStyle` e `buttonStyle` , em seguida, são aplicadas para o [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instâncias e [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) instância, definindo suas [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propriedades. Isso resulta na exibição mostrada nas capturas de tela seguir:

[![](inheritance-images/style-inheritance.png)](inheritance-images/style-inheritance-large.png#lightbox)

> [!NOTE]
> Um estilo implícito pode ser derivado de um estilo explícito, mas um estilo explícito não pode ser derivado de um estilo implícito.

### <a name="respecting-the-inheritance-chain"></a>Respeitando a cadeia de herança

Um estilo só pode herdar de estilos no mesmo nível ou acima na hierarquia do modo de exibição. Isso significa que:

- Um recurso de nível de aplicativo só pode herdar de outros recursos de nível de aplicativo.
- Um recurso de nível de página pode herdar de recursos de nível de aplicativo e outros recursos de nível de página.
- Um recurso de nível de controle pode herdar de recursos de nível de aplicativo, recursos de nível de página e outros recursos de nível de controle.

Esta cadeia de herança é demonstrada no exemplo de código a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="labelStyle" TargetType="Label" BasedOn="{StaticResource baseStyle}">
                      ...
                    </Style>
                    <Style x:Key="buttonStyle" TargetType="Button" BasedOn="{StaticResource baseStyle}">
                      ...
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Neste exemplo, `labelStyle` e `buttonStyle` é recursos de nível de controle enquanto `baseStyle` é um recurso de nível de página. No entanto, enquanto `labelStyle` e `buttonStyle` herdam `baseStyle`, não é possível `baseStyle` herde `labelStyle` ou `buttonStyle`, devido a seus respectivos locais na hierarquia do modo de exibição.

## <a name="style-inheritance-in-c35"></a>Herança de estilo em C&#35;

Equivalente c# página, onde [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instâncias são atribuídas diretamente para o [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propriedades de controles necessários, é mostrado no exemplo de código a seguir:

```csharp
public class StyleInheritancePageCS : ContentPage
{
    public StyleInheritancePageCS ()
    {
        var baseStyle = new Style (typeof(View)) {
            Setters = {
                new Setter {
                    Property = View.HorizontalOptionsProperty, Value = LayoutOptions.Center },
                ...
            }
        };

        var labelStyle = new Style (typeof(Label)) {
            BasedOn = baseStyle,
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Teal }
            }
        };

        var buttonStyle = new Style (typeof(Button)) {
            BasedOn = baseStyle,
            Setters = {
                new Setter { Property = Button.BorderColorProperty, Value = Color.Lime },
                ...
            }
        };
        ...

        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels", Style = labelStyle },
                ...
                new Button { Text = "So is the button", Style = buttonStyle }
            }
        };
    }
}
```

O `baseStyle` destinos [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/) instâncias e define o [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) e [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propriedades. O `baseStyle` não definida diretamente em qualquer controle. Em vez disso, `labelStyle` e `buttonStyle` herdam, definir valores de propriedade associável adicionais. O `labelStyle` e `buttonStyle` , em seguida, são aplicadas para o [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instâncias e [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) instância, definindo suas [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propriedades.

## <a name="summary"></a>Resumo

Estilos podem herdar de outros estilos para reduzir a duplicação e habilitar a reutilização. Herança de estilo é efetuada, definindo a [ `Style.BasedOn` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BasedOn/) propriedade um existente [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/).


## <a name="related-links"></a>Links relacionados

- [Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos básicos (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [Trabalhando com estilos (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [Style](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Setter](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
