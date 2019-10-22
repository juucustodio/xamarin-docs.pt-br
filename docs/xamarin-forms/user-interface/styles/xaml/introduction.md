---
title: Introdução aos estilos do Xamarin. Forms
description: Os estilos permitem que a aparência de elementos visuais seja personalizada. Os estilos são definidos para um tipo específico e contêm valores para as propriedades disponíveis nesse tipo.
ms.prod: xamarin
ms.assetid: 3FF899C0-6CFB-4C1D-837D-9E9E10181967
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 35f8dad3590c07ceb3c93aa735b8c02d75098498
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70228162"
---
# <a name="introduction-to-xamarinforms-styles"></a>Introdução aos estilos do Xamarin. Forms

_Os estilos permitem que a aparência de elementos visuais seja personalizada. Os estilos são definidos para um tipo específico e contêm valores para as propriedades disponíveis nesse tipo._

Os aplicativos Xamarin. Forms geralmente contêm vários controles que têm uma aparência idêntica. Por exemplo, um aplicativo pode ter várias instâncias de [`Label`](xref:Xamarin.Forms.Label) que têm as mesmas opções de fonte e opções de layout, conforme mostrado no exemplo de código XAML a seguir:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="Styles.NoStylesPage"
    Title="No Styles"
    IconImageSource="xaml.png">
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
        IconImageSource = "csharp.png";
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

Cada instância de [`Label`](xref:Xamarin.Forms.Label) tem valores de propriedade idênticos para controlar a aparência do texto exibido pelo `Label`. Isso resulta na aparência mostrada nas capturas de tela seguir:

[Aparência de ![Label sem estilos](introduction-images/no-styles.png)](introduction-images/no-styles-large.png#lightbox)

Definir a aparência de cada controle individual pode ser repetitivo e propenso a erros. Em vez disso, um estilo pode ser criado para definir a aparência e, em seguida, aplicado aos controles necessários.

## <a name="create-a-style"></a>Criar um estilo

A classe [`Style`](xref:Xamarin.Forms.Style) agrupa uma coleção de valores de propriedade em um objeto que pode então ser aplicado a várias instâncias de elementos visuais. Isso ajuda a reduzir a marcação repetitiva e permite que a aparência de aplicativos seja alterada com mais facilidade.

Embora os estilos tenham sido projetados principalmente para aplicativos baseados em XAML, eles também podem ser C#criados em:

- [`Style`](xref:Xamarin.Forms.Style) instâncias criadas em XAML normalmente são definidas em uma [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) atribuída à coleção de [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) de um controle, página ou à coleção de [`Resources`](xref:Xamarin.Forms.Application.Resources) do aplicativo.
- [`Style`](xref:Xamarin.Forms.Style) instâncias criadas no C# normalmente são definidas na classe da página ou em uma classe que pode ser acessada globalmente.

Escolher onde definir um [`Style`](xref:Xamarin.Forms.Style) afeta onde ele pode ser usado:

- [`Style`](xref:Xamarin.Forms.Style) instâncias definidas no nível de controle só podem ser aplicadas ao controle e a seus filhos.
- [`Style`](xref:Xamarin.Forms.Style) instâncias definidas no nível de página só podem ser aplicadas à página e a seus filhos.
- [`Style`](xref:Xamarin.Forms.Style) instâncias definidas no nível do aplicativo podem ser aplicadas em todo o aplicativo.

Cada instância de [`Style`](xref:Xamarin.Forms.Style) contém uma coleção de um ou mais objetos [`Setter`](xref:Xamarin.Forms.Setter) , sendo que cada `Setter` tem uma [`Property`](xref:Xamarin.Forms.Setter.Property) e uma [`Value`](xref:Xamarin.Forms.Setter.Value). O `Property` é o nome da propriedade vinculável do elemento ao qual o estilo é aplicado e o `Value` é o valor que é aplicado à propriedade.

Cada instância de [`Style`](xref:Xamarin.Forms.Style) pode ser *explícita*ou *implícita*:

- Uma instância de [`Style`](xref:Xamarin.Forms.Style) *explícita* é definida especificando um [`TargetType`](xref:Xamarin.Forms.Style.TargetType) e um valor de `x:Key` e definindo a propriedade [`Style`](xref:Xamarin.Forms.NavigableElement.Style) do elemento de destino como a referência `x:Key`. Para obter mais informações sobre estilos *explícitos* , consulte [estilos explícitos](~/xamarin-forms/user-interface/styles/explicit.md).
- Uma instância de [`Style`](xref:Xamarin.Forms.Style) *implícita* é definida especificando-se apenas um [`TargetType`](xref:Xamarin.Forms.Style.TargetType). A instância de `Style` será aplicada automaticamente a todos os elementos desse tipo. Observe que as subclasses da `TargetType` não têm automaticamente a `Style` aplicada. Para obter mais informações sobre estilos *implícitos* , consulte [estilos implícitos](~/xamarin-forms/user-interface/styles/implicit.md).

Ao criar um [`Style`](xref:Xamarin.Forms.Style), a propriedade [`TargetType`](xref:Xamarin.Forms.Style.TargetType) é sempre necessária. O exemplo de código a seguir mostra um estilo *explícito* (observe o `x:Key`) criado em XAML:

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

Para aplicar um `Style`, o objeto de destino deve ser um [`VisualElement`](xref:Xamarin.Forms.VisualElement) que corresponda ao valor da propriedade [`TargetType`](xref:Xamarin.Forms.Style.TargetType) da `Style`, conforme mostrado no exemplo de código XAML a seguir:

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

Estilos inferiores na hierarquia de exibição têm precedência sobre aqueles definidos acima. Por exemplo, definir um [`Style`](xref:Xamarin.Forms.Style) que define [`Label.TextColor`](xref:Xamarin.Forms.Label.TextColor) como `Red` no nível do aplicativo será substituído por um estilo de nível de página que define `Label.TextColor` para `Green`. Da mesma forma, um estilo de nível de página será substituído por um estilo de nível de controle. Além disso, se `Label.TextColor` for definido diretamente em uma propriedade de controle, isso terá precedência sobre os estilos.

Os artigos nesta seção demonstram e explicam como criar e aplicar estilos *explícitos* e *implícitos* , como criar estilos globais, herança de estilo, como responder a alterações de estilo em tempo de execução e como usar os estilos internos incluídos no Xamarin. Forms.

> [!NOTE]
> **O que é StyleID?**
>
> Antes do Xamarin. Forms 2,2, a propriedade [`StyleId`](xref:Xamarin.Forms.Element.StyleId) foi usada para identificar elementos individuais em um aplicativo para identificação no teste de interface do usuário e em mecanismos de tema como Pixate. No entanto, o Xamarin. Forms 2,2 introduziu a propriedade [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) , que substituiu a propriedade [`StyleId`](xref:Xamarin.Forms.Element.StyleId) .

## <a name="related-links"></a>Links relacionados

- [Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilo](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
