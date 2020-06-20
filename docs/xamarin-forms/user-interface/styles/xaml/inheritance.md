---
title: Herança de estilo emXamarin.Forms
description: Os estilos podem herdar de outros estilos para reduzir a duplicação e habilitar a reutilização. Este artigo explica como executar a herança de estilo em um Xamarin.Forms aplicativo.
ms.prod: xamarin
ms.assetid: 67A3A39C-8CC0-446D-8162-FFA73582D3B8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 80cc419ae098f4a0cbbd782785c0ec5ba03fa703
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138951"
---
# <a name="style-inheritance-in-xamarinforms"></a>Herança de estilo emXamarin.Forms

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Os estilos podem herdar de outros estilos para reduzir a duplicação e habilitar a reutilização._

## <a name="style-inheritance-in-xaml"></a>Herança de estilo em XAML

A herança de estilo é executada definindo a [`Style.BasedOn`](xref:Xamarin.Forms.Style.BasedOn) propriedade como um existente [`Style`](xref:Xamarin.Forms.Style) . Em XAML, isso é feito definindo a `BasedOn` propriedade como uma `StaticResource` extensão de marcação que faz referência a uma criada anteriormente `Style` . Em C#, isso é feito definindo a `BasedOn` propriedade como uma `Style` instância.

Os estilos que herdam de um estilo base podem incluir [`Setter`](xref:Xamarin.Forms.Setter) instâncias para novas propriedades ou usá-las para substituir os estilos do estilo base. Além disso, os estilos que herdam de um estilo base devem ter como destino o mesmo tipo, ou um tipo derivado do tipo de destino pelo estilo base. Por exemplo, se um estilo base se destinar a [`View`](xref:Xamarin.Forms.View) instâncias, os estilos baseados no estilo base poderão ter como destino `View` instâncias ou tipos que derivam da `View` classe, como [`Label`](xref:Xamarin.Forms.Label) e [`Button`](xref:Xamarin.Forms.Button) instâncias.

O código a seguir demonstra a herança de estilo *explícita* em uma página XAML:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" IconImageSource="xaml.png">
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

As `baseStyle` instâncias de destinos [`View`](xref:Xamarin.Forms.View) e define as [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriedades e. O `baseStyle` não é definido diretamente em nenhum controle. Em vez disso, `labelStyle` e `buttonStyle` herdá-la, definindo valores de propriedade vinculáveis adicionais. O `labelStyle` e `buttonStyle` , em seguida, são aplicados às [`Label`](xref:Xamarin.Forms.Label) instâncias e à [`Button`](xref:Xamarin.Forms.Button) instância, definindo suas [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Propriedades. Isso resulta na aparência mostrada nas capturas de tela seguir:

[![](inheritance-images/style-inheritance.png)](inheritance-images/style-inheritance-large.png#lightbox)

> [!NOTE]
> Um estilo implícito pode ser derivado de um estilo explícito, mas um estilo explícito não pode ser derivado de um estilo implícito.

### <a name="respecting-the-inheritance-chain"></a>Respeitando a cadeia de herança

Um estilo só pode herdar de estilos no mesmo nível, ou acima, na hierarquia de exibição. Isso significa que:

- Um recurso de nível de aplicativo só pode herdar de outros recursos de nível de aplicativo.
- Um recurso de nível de página pode herdar de recursos de nível de aplicativo e outros recursos de nível de página.
- Um recurso de nível de controle pode herdar de recursos de nível de aplicativo, recursos de nível de página e outros recursos de nível de controle.

Essa cadeia de herança é demonstrada no exemplo de código a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" IconImageSource="xaml.png">
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

Neste exemplo, `labelStyle` e `buttonStyle` são recursos de nível de controle, enquanto `baseStyle` é um recurso de nível de página. No entanto, embora `labelStyle` e `buttonStyle` herde do `baseStyle` , não é possível `baseStyle` herdar de `labelStyle` ou `buttonStyle` , devido a seus respectivos locais na hierarquia de exibição.

## <a name="style-inheritance-in-c35"></a>Herança de estilo em C&#35;

A página C# equivalente, em que as [`Style`](xref:Xamarin.Forms.Style) instâncias são atribuídas diretamente às [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Propriedades dos controles necessários, é mostrada no exemplo de código a seguir:

```csharp
public class StyleInheritancePageCS : ContentPage
{
    public StyleInheritancePageCS ()
    {
        var baseStyle = new Style (typeof(View)) {
            Setters = {
                new Setter {
                    Property = View.HorizontalOptionsProperty, Value = LayoutOptions.Center    },
                ...
            }
        };

        var labelStyle = new Style (typeof(Label)) {
            BasedOn = baseStyle,
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Teal    }
            }
        };

        var buttonStyle = new Style (typeof(Button)) {
            BasedOn = baseStyle,
            Setters = {
                new Setter { Property = Button.BorderColorProperty, Value =    Color.Lime },
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

As `baseStyle` instâncias de destinos [`View`](xref:Xamarin.Forms.View) e define as [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriedades e. O `baseStyle` não é definido diretamente em nenhum controle. Em vez disso, `labelStyle` e `buttonStyle` herdá-la, definindo valores de propriedade vinculáveis adicionais. O `labelStyle` e `buttonStyle` , em seguida, são aplicados às [`Label`](xref:Xamarin.Forms.Label) instâncias e à [`Button`](xref:Xamarin.Forms.Button) instância, definindo suas [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Propriedades.

## <a name="related-links"></a>Links relacionados

- [Extensões de marcação do XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos básicos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Trabalhando com estilos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
