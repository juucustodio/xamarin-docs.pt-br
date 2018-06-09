---
title: Introdução ao xamarin. Forms estilos
description: Estilos permitem a aparência de elementos visuais para serem personalizados. Estilos definidos para um tipo específico e contenham valores para as propriedades disponíveis nesse tipo.
ms.prod: xamarin
ms.assetid: 3FF899C0-6CFB-4C1D-837D-9E9E10181967
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 071cfa2ba145775c179bc85dce4fac29ba0bd8fa
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245814"
---
# <a name="introduction-to-xamarinforms-styles"></a>Introdução ao xamarin. Forms estilos

_Estilos permitem a aparência de elementos visuais para serem personalizados. Estilos definidos para um tipo específico e contenham valores para as propriedades disponíveis nesse tipo._

Aplicativos xamarin. Forms geralmente contêm vários controles que têm uma aparência idêntica. Por exemplo, um aplicativo pode ter vários [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instâncias que têm as mesmas opções de fonte e as opções de layout, conforme mostrado no exemplo de código XAML a seguir:

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

O exemplo de código a seguir mostra a página equivalente criada em c#:

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

Cada [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instância tem valores de propriedade idênticas para controlar a aparência do texto exibido pelo `Label`. Isso resulta na exibição mostrada nas capturas de tela seguir:

[![](introduction-images/no-styles.png "Rótulo aparência sem estilos")](introduction-images/no-styles-large.png#lightbox "aparência sem estilos de rótulo")

Definir a aparência de cada controle individual pode ser repetitiva e propenso a erros. Em vez disso, pode ser criado um estilo que define a aparência e, em seguida, são aplicadas aos controles necessários.

## <a name="creating-a-style"></a>Criando um estilo

O [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) classe agrupa um conjunto de valores de propriedade em um objeto que pode então ser aplicado a várias instâncias de elemento visual. Isso ajuda a reduzir marcações repetitivas e permite uma aparência de aplicativos a ser alterado mais facilmente.

Embora os estilos foram criados principalmente para aplicativos baseados em XAML, também podem ser criadas em c#:

- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instâncias criadas em XAML geralmente são definidas em um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) que é atribuído ao [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) coleção de um controle de página, ou o [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Resources/) coleção do aplicativo.
- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instâncias criadas no c# costumam ser definidas na classe da página ou em uma classe que pode ser acessada globalmente.

Escolhendo onde definir um [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) impactos onde ele pode ser usado:

- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instâncias definidas no nível de controle só podem ser aplicadas ao controle e a seus filhos.
- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instâncias definidas no nível de página só podem ser aplicadas à página e a seus filhos.
- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instâncias definidas no nível do aplicativo podem ser aplicadas em todo o aplicativo.

Cada [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instância contém uma coleção de um ou mais [ `Setter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/) objetos, com cada `Setter` tendo um [ `Property` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Property/) e um [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Value/). O `Property` é o nome da propriedade associável do elemento de estilo é aplicado, e o `Value` é o valor que é aplicado à propriedade.

Cada [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instância pode ser *explícita*, ou *implícita*:

- Um *explícita* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instância é definida especificando uma [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) e um `x:Key` valor e, em seguida, definindo o elemento de destino [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propriedade para o `x:Key` referência. Para obter mais informações sobre *explícita* estilos, consulte [estilos explícita](~/xamarin-forms/user-interface/styles/explicit.md).
- Um *implícita* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) instância é definida pela especificação de apenas um [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/). O `Style` instância serão então aplicada automaticamente a todos os elementos desse tipo. Observe que as subclasses do `TargetType` automaticamente não tem o `Style` aplicado. Para obter mais informações sobre *implícita* estilos, consulte [estilos implícitos](~/xamarin-forms/user-interface/styles/implicit.md).

Ao criar um [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/), o [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) propriedade sempre é necessária. O seguinte exemplo de código mostra uma *explícita* estilo (Observe o `x:Key`) criados em XAML:

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

Para aplicar um `Style`, o objeto de destino deve ser um [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) que corresponde a [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) valor da propriedade a `Style`, conforme mostrado no exemplo de código XAML a seguir:

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

Estilos inferiores na hierarquia de exibição têm precedência sobre aquelas definidas superior para cima. Por exemplo, se você definir um [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) que define [ `Label.TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/) para `Red` no aplicativo de nível será substituído por um estilo de nível de página que define `Label.TextColor` para `Green`. Da mesma forma, um estilo de nível de página será substituído por um estilo de controle. Além disso, se `Label.TextColor` está definida diretamente em uma propriedade de controle, isso tem precedência sobre todos os estilos.

Os artigos nesta seção demonstram e explicam como criar e aplicar *explícita* e *implícita* estilos, como criar estilos globais, definir o estilo de herança, como responder a alterações de estilo em tempo de execução, e como usar os estilos interno incluídos no xamarin. Forms.

> [!NOTE]
> **O que é StyleId?**
>
> Antes de xamarin. Forms 2.2, o [ `StyleId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.StyleId/) propriedade foi usada para identificar elementos individuais em um aplicativo para identificação no teste de interface do usuário e nos mecanismos de tema como Pixate. No entanto, xamarin. Forms 2.2 introduziu o [ `AutomationId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.AutomationId/) propriedade, que substituiu o [ `StyleId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.StyleId/) propriedade. Para obter mais informações, consulte [xamarin. Forms automatizar testes com Xamarin.UITest e Test Cloud](~/xamarin-forms/deploy-test/uitest-and-test-cloud.md).

## <a name="summary"></a>Resumo

Aplicativos xamarin. Forms geralmente contêm vários controles que têm uma aparência idêntica. Definir a aparência de cada controle individual pode ser repetitiva e propenso a erros. Em vez disso, estilos podem ser criados que personalizam a aparência do controle de agrupamento e propriedades de configurações disponíveis no tipo de controle.


## <a name="related-links"></a>Links relacionados

- [Extensões de marcação XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Estilo](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Setter](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
