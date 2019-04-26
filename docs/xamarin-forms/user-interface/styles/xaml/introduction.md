---
title: Introdução ao xamarin. Forms estilos
description: Estilos permitem que a aparência dos elementos visuais para serem personalizados. Os estilos são definidos para um tipo específico e contêm valores para as propriedades disponíveis nesse tipo.
ms.prod: xamarin
ms.assetid: 3FF899C0-6CFB-4C1D-837D-9E9E10181967
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 4048ec78d48b810b39d46fbcb7708860c478cce3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023413"
---
# <a name="introduction-to-xamarinforms-styles"></a>Introdução ao xamarin. Forms estilos

_Estilos permitem que a aparência dos elementos visuais para serem personalizados. Os estilos são definidos para um tipo específico e contêm valores para as propriedades disponíveis nesse tipo._

Aplicativos xamarin. Forms geralmente contêm vários controles que têm uma aparência idêntica. Por exemplo, um aplicativo pode ter vários [ `Label` ](xref:Xamarin.Forms.Label) instâncias que têm as mesmas opções de fonte e opções de layout, conforme mostrado no exemplo de código XAML a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="Styles.NoStylesPage"
    Title="No Styles"
    Icon="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
            <Label Text="are not"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
            <Label Text="using styles"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

O exemplo de código a seguir mostra a página equivalente criada em C#:

```csharp
public class NoStylesPageCS : ContentPage
{
    public NoStylesPageCS ()
    {
        Title = "No Styles";
        Icon = "csharp.png";
        Padding = new Thickness (0, 20, 0, 0);

        Content = new StackLayout {
            Children = {
                new Label {
                    Text = "These labels",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                },
                new Label {
                    Text = "are not",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                },
                new Label {
                    Text = "using styles",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                }
            }
        };
    }
}
```

Cada [ `Label` ](xref:Xamarin.Forms.Label) instância tem valores de propriedade idênticas para controlar a aparência do texto exibido pelo `Label`. Isso resulta na aparência mostrada nas capturas de tela seguir:

[![](introduction-images/no-styles.png "Rotular aparência sem estilos")](introduction-images/no-styles-large.png#lightbox "aparência sem estilos de rótulo")

Definir a aparência de cada controle individual pode ser repetitiva e sujeito a erros. Em vez disso, pode ser criado um estilo que define a aparência e, em seguida, são aplicados aos controles necessários.

## <a name="create-a-style"></a>Criar um estilo

O [ `Style` ](xref:Xamarin.Forms.Style) classe agrupa uma coleção de valores de propriedade em um único objeto, em seguida, pode ser aplicado a várias instâncias do elemento visual. Isso ajuda a reduzir as marcações repetitivas e permite uma aparência de aplicativos a ser alterado com mais facilidade.

Embora os estilos foram projetados principalmente para aplicativos baseados em XAML, eles também podem ser criados em c#:

- [`Style`](xref:Xamarin.Forms.Style) instâncias criadas no XAML geralmente são definidas em uma [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) que é atribuído ao [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) coleção de um controle, página, ou para o [ `Resources` ](xref:Xamarin.Forms.Application.Resources) coleção do aplicativo.
- [`Style`](xref:Xamarin.Forms.Style) as instâncias criadas na linguagem c# normalmente são definidas na classe da página, ou em uma classe que pode ser acessada globalmente.

Escolher onde definir um [`Style`](xref:Xamarin.Forms.Style) afeta onde ele pode ser usado:

- [`Style`](xref:Xamarin.Forms.Style) só podem ser aplicadas a instâncias definidas no nível de controle para o controle e seus filhos.
- [`Style`](xref:Xamarin.Forms.Style) só podem ser aplicadas a instâncias definidas no nível da página para a página e seus filhos.
- [`Style`](xref:Xamarin.Forms.Style) instâncias definidas no nível do aplicativo podem ser aplicadas em todo o aplicativo.

Cada [ `Style` ](xref:Xamarin.Forms.Style) instância contém uma coleção de um ou mais [ `Setter` ](xref:Xamarin.Forms.Setter) objetos, com cada `Setter` tendo um [ `Property` ](xref:Xamarin.Forms.Setter.Property) e uma [`Value`](xref:Xamarin.Forms.Setter.Value). O `Property` é o nome da propriedade associável do elemento de estilo é aplicado, e o `Value` é o valor que é aplicado à propriedade.

Cada [ `Style` ](xref:Xamarin.Forms.Style) instância pode ser *explícita*, ou *implícita*:

- Uma *explícita* [ `Style` ](xref:Xamarin.Forms.Style) instância é definida especificando uma [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) e um `x:Key` valor e, em seguida, definindo o elemento de destino [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) propriedade para o `x:Key` referência. Para obter mais informações sobre *explícita* estilos, consulte [estilos explícitos](~/xamarin-forms/user-interface/styles/explicit.md).
- Uma *implícita* [ `Style` ](xref:Xamarin.Forms.Style) instância é definida especificando-se apenas um [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType). O `Style` instância, em seguida, serão automaticamente aplicada a todos os elementos desse tipo. Observe que pode efetuar subclasses do `TargetType` não tem automaticamente o `Style` aplicado. Para obter mais informações sobre *implícita* estilos, consulte [estilos implícitos](~/xamarin-forms/user-interface/styles/implicit.md).

Ao criar uma [ `Style` ](xref:Xamarin.Forms.Style), o [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) propriedade sempre é necessária. O seguinte exemplo de código mostra uma *explícita* estilo (Observe o `x:Key`) criado em XAML:

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

Para aplicar uma `Style`, o objeto de destino deve ser um [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) que corresponde a [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) valor da propriedade do `Style`, conforme mostrado no exemplo de código XAML a seguir:

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

Estilos inferiores na hierarquia de exibição têm precedência sobre aquelas definidas superior para cima. Por exemplo, definir uma [ `Style` ](xref:Xamarin.Forms.Style) que define [ `Label.TextColor` ](xref:Xamarin.Forms.Label.TextColor) para `Red` no aplicativo de nível será substituído por um estilo de nível de página que define `Label.TextColor` para `Green`. Da mesma forma, um estilo de nível de página será substituído por um estilo de controle. Além disso, se `Label.TextColor` for definido diretamente em uma propriedade de controle, isso tem precedência sobre todos os estilos.

Os artigos nesta seção demonstram e explicam como criar e aplicar *explícita* e *implícita* estilos, como criar estilos globais, herança, de estilo como responder a alterações de estilo em tempo de execução e como usar os estilos internos incluídos no xamarin. Forms.

> [!NOTE]
> **O que é StyleId?**
>
> Antes de xamarin. Forms 2.2, o [ `StyleId` ](xref:Xamarin.Forms.Element.StyleId) propriedade foi usada para identificar elementos individuais em um aplicativo para identificação em testes de interface do usuário e em mecanismos de tema como Pixate. No entanto, o xamarin. Forms 2.2 introduziu o [ `AutomationId` ](xref:Xamarin.Forms.Element.AutomationId) propriedade, que substituiu o [ `StyleId` ](xref:Xamarin.Forms.Element.StyleId) propriedade.

## <a name="related-links"></a>Links relacionados

- [Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilo](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
