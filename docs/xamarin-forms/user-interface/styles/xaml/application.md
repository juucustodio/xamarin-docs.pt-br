---
title: Estilos globais em Xamarin.Forms
description: Os estilos podem ser disponibilizados globalmente adicionando-os ao dicionário de recursos do aplicativo. Isso ajuda a evitar a duplicação de estilos em páginas ou controles.
ms.prod: xamarin
ms.assetid: BDC65F82-65E0-4C8E-BB91-8E340EB2D15A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8d24300dcee76511466ec97f4944fe0be1278354
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371580"
---
# <a name="global-styles-in-no-locxamarinforms"></a>Estilos globais em Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Os estilos podem ser disponibilizados globalmente adicionando-os ao dicionário de recursos do aplicativo. Isso ajuda a evitar a duplicação de estilos em páginas ou controles._

## <a name="create-a-global-style-in-xaml"></a>Criar um estilo global em XAML

Por padrão, todos os Xamarin.Forms aplicativos criados a partir de um modelo usam a classe **app** para implementar a subclasse [`Application`](xref:Xamarin.Forms.Application) . Para declarar um [`Style`](xref:Xamarin.Forms.Style) no nível do aplicativo, no uso do XAML do aplicativo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , a classe de **aplicativo** padrão deve ser substituída por uma classe de **aplicativo** XAML e code-behind associado. Para obter mais informações, consulte [trabalhando com a classe App](~/xamarin-forms/app-fundamentals/application-class.md).

O exemplo de código a seguir mostra um [`Style`](xref:Xamarin.Forms.Style) declarado no nível do aplicativo:

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

Isso [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) define um único estilo *explícito* , `buttonStyle` , que será usado para definir a aparência de [`Button`](xref:Xamarin.Forms.Button) instâncias. No entanto, os estilos globais podem ser *explícitos* ou *implícitos*.

O exemplo de código a seguir mostra uma página XAML aplicando o `buttonStyle` às [`Button`](xref:Xamarin.Forms.Button) instâncias da página:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Button Text="These buttons" Style="{StaticResource buttonStyle}" />
            <Button Text="are demonstrating" Style="{StaticResource buttonStyle}" />
            <Button Text="application style overrides" Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Isso resulta na aparência mostrada nas capturas de tela seguir:

[![Exemplo de estilos globais](application-images/application-styles-1.png)](application-images/application-styles-1-large.png#lightbox "Exemplo de estilos globais")

Para obter informações sobre como criar estilos em uma página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , consulte [estilos explícitos](~/xamarin-forms/user-interface/styles/explicit.md) e [estilos implícitos](~/xamarin-forms/user-interface/styles/implicit.md).

### <a name="override-styles"></a>Substituir estilos

Estilos inferiores na hierarquia de exibição têm precedência sobre aqueles definidos acima. Por exemplo, definir um [`Style`](xref:Xamarin.Forms.Style) que define [`Button.TextColor`](xref:Xamarin.Forms.Button.TextColor) como `Red` no nível do aplicativo será substituído por um estilo de nível de página que define `Button.TextColor` como `Green` . Da mesma forma, um estilo de nível de página será substituído por um estilo de nível de controle. Além disso, se `Button.TextColor` for definido diretamente em uma propriedade de controle, isso terá precedência sobre todos os estilos. Essa precedência é demonstrada no exemplo de código a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" IconImageSource="xaml.png">
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

O original `buttonStyle` , definido no nível do aplicativo, é substituído pela `buttonStyle` instância definida no nível da página. Além disso, o estilo de nível de página é substituído pelo nível de controle `buttonStyle` . Portanto, as [`Button`](xref:Xamarin.Forms.Button) instâncias são exibidas com texto azul, conforme mostrado nas seguintes capturas de tela:

[![Exemplo de substituição de estilos](application-images/application-styles-2.png)](application-images/application-styles-2-large.png#lightbox "Exemplo de substituição de estilos")

## <a name="create-a-global-style-in-c35"></a>Criar um estilo global em C&#35;

[`Style`](xref:Xamarin.Forms.Style) as instâncias podem ser adicionadas à coleção do aplicativo [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) em C# criando um novo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) e, em seguida, adicionando as `Style` instâncias ao `ResourceDictionary` , conforme mostrado no exemplo de código a seguir:

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

O construtor define um único estilo *explícito* para ser aplicado a [`Button`](xref:Xamarin.Forms.Button) instâncias em todo o aplicativo. *Explícito* [`Style`](xref:Xamarin.Forms.Style) as instâncias são adicionadas ao [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) usando o [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) método, especificando uma `key` cadeia de caracteres para fazer referência à `Style` instância. A `Style` instância pode então ser aplicada a qualquer controle do tipo correto no aplicativo. No entanto, os estilos globais podem ser *explícitos* ou *implícitos*.

O exemplo de código a seguir mostra uma página C# aplicando o `buttonStyle` às [`Button`](xref:Xamarin.Forms.Button) instâncias da página:

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

O `buttonStyle` é aplicado às [`Button`](xref:Xamarin.Forms.Button) instâncias definindo suas [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Propriedades e controla a aparência das `Button` instâncias.

## <a name="related-links"></a>Links relacionados

- [Extensões de marcação do XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilos básicos (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [Trabalhando com estilos (exemplo)](/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [Estilo](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)