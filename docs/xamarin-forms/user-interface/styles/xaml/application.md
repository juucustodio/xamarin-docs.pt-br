---
title: Estilos globais no xamarin. Forms
description: Estilos podem se tornar disponíveis globalmente ao adicioná-los a dicionário de recursos do aplicativo. Isso ajuda a evitar a duplicação de estilos em páginas ou controles.
ms.prod: xamarin
ms.assetid: BDC65F82-65E0-4C8E-BB91-8E340EB2D15A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: e7b2a37b868ea03ca626ffd2dcddb006a235b0cc
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995395"
---
# <a name="global-styles-in-xamarinforms"></a>Estilos globais no xamarin. Forms

_Estilos podem se tornar disponíveis globalmente ao adicioná-los a dicionário de recursos do aplicativo. Isso ajuda a evitar a duplicação de estilos em páginas ou controles._

## <a name="creating-a-global-style-in-xaml"></a>Criando um estilo Global em XAML

Por padrão, todos os aplicativos xamarin. Forms criados de um modelo usam a **App** classe para implementar o [ `Application` ](xref:Xamarin.Forms.Application) subclasse. Para declarar uma [ `Style` ](xref:Xamarin.Forms.Style) no nível do aplicativo, na caixa de diálogo [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) usando XAML, o padrão **aplicativo** classe deve ser substituído por um XAML **Aplicativo** classe e code-behind associado. Para obter mais informações, consulte [trabalhando com a classe App](~/xamarin-forms/app-fundamentals/application-class.md).

O seguinte exemplo de código mostra uma [ `Style` ](xref:Xamarin.Forms.Style) declarado no nível do aplicativo:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.App">
    <Application.Resources>
        <ResourceDictionary>
            <Style x:Key="buttonStyle" TargetType="Button">
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                <Setter Property="BorderColor" Value="Lime" />
                <Setter Property="BorderRadius" Value="5" />
                <Setter Property="BorderWidth" Value="5" />
                <Setter Property="WidthRequest" Value="200" />
                <Setter Property="TextColor" Value="Teal" />
            </Style>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

Isso [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) define um único *explícita* estilo, `buttonStyle`, que será usado para definir a aparência da [ `Button` ](xref:Xamarin.Forms.Button) instâncias. No entanto, os estilos globais podem ser *explícita* ou *implícita*.

O exemplo de código a seguir mostra uma página do XAML aplicando o `buttonStyle` para a página [ `Button` ](xref:Xamarin.Forms.Button) instâncias:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" Icon="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Button Text="These buttons" Style="{StaticResource buttonStyle}" />
            <Button Text="are demonstrating" Style="{StaticResource buttonStyle}" />
            <Button Text="application style overrides" Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Isso resulta na exibição mostrada nas capturas de tela seguir:

[![](application-images/application-styles-1.png "Exemplo de estilos globais")](application-images/application-styles-1-large.png#lightbox "exemplo estilos globais")

Para obter informações sobre a criação de estilos em uma página [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), consulte [estilos explícitos](~/xamarin-forms/user-interface/styles/explicit.md) e [estilos implícitos](~/xamarin-forms/user-interface/styles/implicit.md).

### <a name="overriding-styles"></a>Substituindo estilos

Estilos inferiores na hierarquia de exibição têm precedência sobre aquelas definidas superior para cima. Por exemplo, definir uma [ `Style` ](xref:Xamarin.Forms.Style) que define [ `Button.TextColor` ](xref:Xamarin.Forms.Button.TextColor) para `Red` no aplicativo de nível será substituído por um estilo de nível de página que define `Button.TextColor` para `Green`. Da mesma forma, um estilo de nível de página será substituído por um estilo de controle. Além disso, se `Button.TextColor` for definido diretamente em uma propriedade de controle, isso terá precedência sobre todos os estilos. Essa precedência é demonstrada no exemplo de código a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="buttonStyle" TargetType="Button">
                ...
                <Setter Property="TextColor" Value="Red" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="buttonStyle" TargetType="Button">
                        ...
                        <Setter Property="TextColor" Value="Blue" />
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            <Button Text="These buttons" Style="{StaticResource buttonStyle}" />
            <Button Text="are demonstrating" Style="{StaticResource buttonStyle}" />
            <Button Text="application style overrides" Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

O original `buttonStyle`, definidas no nível do aplicativo, é substituído pelo `buttonStyle` instância definida no nível da página. Além disso, o estilo de nível de página é substituído pelo nível de controle `buttonStyle`. Portanto, o [ `Button` ](xref:Xamarin.Forms.Button) instâncias são exibidas com o texto azul, conforme mostrado nas capturas de tela seguir:

[![](application-images/application-styles-2.png "Substituindo o exemplo de estilos")](application-images/application-styles-2-large.png#lightbox "substituindo os estilos de exemplo")

## <a name="creating-a-global-style-in-c35"></a>Criando um estilo Global em C&#35;

[`Style`](xref:Xamarin.Forms.Style) instâncias podem ser adicionadas para o aplicativo [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) coleção no c#, criando um novo [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)e, em seguida, adicionando o `Style` para instâncias de `ResourceDictionary`, como como mostrado no exemplo de código a seguir:

```csharp
public class App : Application
{
    public App ()
    {
        var buttonStyle = new Style (typeof(Button)) {
            Setters = {
                ...
                new Setter { Property = Button.TextColorProperty,    Value = Color.Teal }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("buttonStyle", buttonStyle);
        ...
    }
    ...
}
```

O construtor define uma única *explícita* estilo para a aplicação [ `Button` ](xref:Xamarin.Forms.Button) instâncias em todo o aplicativo. *Explícito* [ `Style` ](xref:Xamarin.Forms.Style) instâncias são adicionadas para o [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) usando o [ `Add` ](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) método, especificando um `key`cadeia de caracteres para se referir a `Style` instância. O `Style` instância, em seguida, pode ser aplicada a todos os controles do tipo correto no aplicativo. No entanto, os estilos globais podem ser *explícita* ou *implícita*.

O exemplo de código a seguir mostra um c# página aplicando o `buttonStyle` para a página [ `Button` ](xref:Xamarin.Forms.Button) instâncias:

```csharp
public class ApplicationStylesPageCS : ContentPage
{
    public ApplicationStylesPageCS ()
    {
        ...
        Content = new StackLayout {
            Children = {
                new Button { Text = "These buttons", Style = (Style)Application.Current.Resources ["buttonStyle"] },
                new Button { Text = "are demonstrating", Style = (Style)Application.Current.Resources ["buttonStyle"] },
                new Button { Text = "application styles", Style = (Style)Application.Current.Resources ["buttonStyle"]
                }
            }
        };
    }
}
```

O `buttonStyle` é aplicado ao [ `Button` ](xref:Xamarin.Forms.Button) instâncias, definindo suas [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) propriedades e controla a aparência do `Button` instâncias.

## <a name="summary"></a>Resumo

Estilos podem ser disponibilizados globalmente ao adicioná-los para o aplicativo [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Isso ajuda a evitar a duplicação de estilos em páginas ou controles.



## <a name="related-links"></a>Links relacionados

- [Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos básicos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [Trabalhar com estilos (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Dicionário de recurso](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
