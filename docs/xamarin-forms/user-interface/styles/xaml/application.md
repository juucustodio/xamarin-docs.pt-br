---
title: Estilos globais
description: Estilos podem ser disponibilizados globalmente adicionando-as ao dicionário de recurso do aplicativo. Isso ajuda a evitar a duplicação de estilos em páginas ou controles.
ms.prod: xamarin
ms.assetid: BDC65F82-65E0-4C8E-BB91-8E340EB2D15A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: a505720e5fef8fe9e9ef82d03e53370210772f45
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="global-styles"></a>Estilos globais

_Estilos podem ser disponibilizados globalmente adicionando-as ao dicionário de recurso do aplicativo. Isso ajuda a evitar a duplicação de estilos em páginas ou controles._

## <a name="creating-a-global-style-in-xaml"></a>Criando um estilo Global em XAML

Por padrão, todos os aplicativos xamarin. Forms criados a partir de um modelo usam a **aplicativo** classe para implementar o [ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/) subclasse. Para declarar um [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) no nível do aplicativo, o aplicativo [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) usando XAML, o padrão **aplicativo** classe deve ser substituída por uma XAML **Aplicativo** classe e o code-behind associado. Para obter mais informações, consulte [trabalhar com a classe de aplicativo](~/xamarin-forms/app-fundamentals/application-class.md).

O seguinte exemplo de código mostra uma [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) declarado no nível do aplicativo:

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

Isso [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) define um único *explícita* estilo, `buttonStyle`, que será usado para definir a aparência de [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) instâncias. No entanto, os estilos globais podem ser *explícita* ou *implícita*.

O exemplo de código a seguir mostra uma página do XAML aplicando o `buttonStyle` para a página [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) instâncias:

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

Para obter informações sobre como criar estilos em uma página [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/), consulte [estilos explícita](~/xamarin-forms/user-interface/styles/explicit.md) e [estilos implícitos](~/xamarin-forms/user-interface/styles/implicit.md).

### <a name="overriding-styles"></a>Substituindo estilos

Estilos inferiores na hierarquia de exibição têm precedência sobre aquelas definidas superior para cima. Por exemplo, se você definir um [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) que define [ `Button.TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.TextColor/) para `Red` no aplicativo de nível será substituído por um estilo de nível de página que define `Button.TextColor` para `Green`. Da mesma forma, um estilo de nível de página será substituído por um estilo de controle. Além disso, se `Button.TextColor` está definida diretamente em uma propriedade de controle, isso terá precedência sobre todos os estilos. Essa precedência é demonstrada no exemplo de código a seguir:

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

O original `buttonStyle`, definidas no nível do aplicativo, é substituído pelo `buttonStyle` instância definida no nível da página. Além disso, o estilo de nível de página será substituído pelo nível de controle `buttonStyle`. Portanto, o [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) instâncias são exibidas com texto azul, conforme mostrado nas capturas de tela seguir:

[![](application-images/application-styles-2.png "Substituindo o exemplo de estilos")](application-images/application-styles-2-large.png#lightbox "substituindo estilos de exemplo")

## <a name="creating-a-global-style-in-c35"></a>Criando um estilo Global em C&#35;

[`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instâncias podem ser adicionadas para o aplicativo [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) coleção em c#, criando um novo [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)e, em seguida, adicionando o `Style` instâncias para o `ResourceDictionary`, como como mostrado no exemplo de código a seguir:

```csharp
public class App : Application
{
    public App ()
    {
        var buttonStyle = new Style (typeof(Button)) {
            Setters = {
                ...
                new Setter { Property = Button.TextColorProperty,   Value = Color.Teal }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("buttonStyle", buttonStyle);
        ...
    }
    ...
}
```

O construtor define um único *explícita* estilo para aplicar a [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) instâncias em todo o aplicativo. *Explícita* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instâncias são adicionadas para o [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) usando o [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ResourceDictionary.Add/p/System.String/System.Object/) método, especificando um `key`cadeia de caracteres para referir-se a `Style` instância. O `Style` instância, em seguida, pode ser aplicada para os controles do tipo correto no aplicativo. No entanto, os estilos globais podem ser *explícita* ou *implícita*.

O exemplo de código a seguir mostra um c# página aplicar o `buttonStyle` para a página [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) instâncias:

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

O `buttonStyle` é aplicada para o [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) instâncias definindo seus [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propriedades e controla a aparência do `Button` instâncias.

## <a name="summary"></a>Resumo

Estilos podem ser disponibilizados globalmente ao adicioná-los para o aplicativo [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/). Isso ajuda a evitar a duplicação de estilos em páginas ou controles.



## <a name="related-links"></a>Links relacionados

- [Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos básicos (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [Trabalhando com estilos (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Dicionário de recurso](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [Estilo](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Setter](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
