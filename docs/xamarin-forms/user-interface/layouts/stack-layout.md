---
title: StackLayout
description: "Use StackLayout para apresentar as coleções de modos de exibição em uma dimensão."
ms.topic: article
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/25/2015
ms.openlocfilehash: 14982fc5cb3e05243c819cbc8b37349d872bd24c
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="stacklayout"></a>StackLayout

`StackLayout` Organiza os modos de exibição em uma linha unidimensional ("pilha"), horizontal ou verticalmente. Exibições em um `StackLayout` pode ser dimensionado com base no espaço no layout usando opções de layout. Posicionamento é determinado pela ordem dos modos de exibição foram adicionados para o layout e as opções de layout dos modos de exibição.

[![](stack-layout-images/layouts-sml.png "Layouts de xamarin. Forms")](stack-layout-images/layouts.png#lightbox "xamarin. Forms Layouts")

## <a name="purpose"></a>Finalidade

`StackLayout` é menos complexo do que outros modos de exibição. Interfaces lineares simples podem ser criados, apenas adicionar modos de exibição para um `StackLayout`e as interfaces mais complexas criadas aninhando-los.

## <a name="usage--behavior"></a>Uso e comportamento

### <a name="spacing"></a>Espaçamento

Por padrão, `StackLayout` adicionará uma margem de 6px entre modos de exibição. Isso pode ser controlado ou definido para não ter nenhuma margem definindo o `Spacing` propriedade StackLayout. O exemplo a seguir demonstra como definir espaçamento e os efeitos das opções de espaçamento diferentes:

Em XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.StackLayoutDemo"
Title="StackLayout Demo">
  <ContentPage.Content>
    <StackLayout Spacing="10" x:Name="layout">
      <Button Text="StackLayout" VerticalOptions="Start"
        HorizontalOptions="FillAndExpand" />
      <BoxView Color="Yellow" VerticalOptions="FillAndExpand"
        HorizontalOptions="FillAndExpand" />
            <BoxView Color="Green" VerticalOptions="FillAndExpand"
        HorizontalOptions="FillAndExpand" />
            <BoxView HeightRequest="75" Color="Blue" VerticalOptions="End"
        HorizontalOptions="FillAndExpand" />
    </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

No C#:

```csharp
public class StackLayoutCode : ContentPage
{
  public StackLayoutCode ()
  {
    var layout = new StackLayout ();
    var button = new Button { Text = "StackLayout", VerticalOptions = LayoutOptions.Start,
     HorizontalOptions = LayoutOptions.FillAndExpand };
    var yellowBox = new BoxView { Color = Color.Yellow, VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };
    var greenBox = new BoxView { Color = Color.Green, VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };
    var blueBox = new BoxView { Color = Color.Blue, VerticalOptions = LayoutOptions.FillAndExpand,
      HorizontalOptions = LayoutOptions.FillAndExpand, HeightRequest = 75 };

    layout.Children.Add(button);
    layout.Children.Add(yellowBox);
    layout.Children.Add(greenBox);
    layout.Children.Add(blueBox);
    layout.Spacing = 10;
    Content = layout;
  }
}
```

Espaçamento = 0:

![](stack-layout-images/spacing-zero.png "StackLayout com espaçamento = 0")

Espaçamento de dez:

![](stack-layout-images/spacing-ten.png "StackLayout com espaçamento = 10")

### <a name="sizing"></a>Dimensionamento

O tamanho de uma exibição em um StackLayout depende das solicitações de altura e largura e as opções de layout. `StackLayout` irá impor o preenchimento. O seguinte `LayoutOption`s fará com que os modos de exibição ocupar o espaço disponível do layout:

- **CenterAndExpand** &ndash; centraliza a exibição no layout e expande para ocupar tanto espaço quanto o layout será dê a ele.
- **EndAndExpand** &ndash; coloca o modo de exibição no final do layout (inferior ou limite mais à direita) e se expande para ocupar tanto espaço quanto o layout será dê a ele.
- **FillAndExpand** &ndash; coloca o modo de exibição para que ele tem nenhum preenchimento e ocupa tanto espaço quanto o layout será dê a ele.
- **StartAndExpand** &ndash; coloca o modo de exibição no início do layout e usa a quantidade de espaço dará o pai.

Para obter mais informações, consulte [expansão](~/xamarin-forms/user-interface/layouts/layout-options.md#expansion).

### <a name="positioning"></a>Posicionamento

Modos de exibição em um StackLayout podem ser posicionados e dimensionados usando `LayoutOptions`. Cada modo de exibição pode ser dado `VerticalOptions` e `HorizontalOptions`, definindo como os modos de exibição irá se posicionar em relação ao layout. O seguinte predefinidos `LayoutOptions` estão disponíveis:

- **Centro de** &ndash; centraliza a exibição no layout.
- **Final** &ndash; coloca o modo de exibição no final do layout (inferior ou limite mais à direita).
- **Preencher** &ndash; coloca o modo de exibição para que não tenha nenhum preenchimento.
- **Iniciar** &ndash; coloca o modo de exibição no início do layout.

O código a seguir demonstra as opções de layout de configuração:

Em XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.StackLayoutDemo"
Title="StackLayout Demo">
  <ContentPage.Content>
    <StackLayout x:Name="layout">
      <Button VerticalOptions="Start"
        HorizontalOptions="FillAndExpand" />
      <BoxView VerticalOptions="FillAndExpand"
        HorizontalOptions="FillAndExpand" />
            <BoxView VerticalOptions="FillAndExpand"
        HorizontalOptions="FillAndExpand" />
            <BoxView HeightRequest="75" VerticalOptions="End"
        HorizontalOptions="FillAndExpand" />
    </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

No C#:

```csharp
public class StackLayoutCode : ContentPage
{
  public StackLayoutCode ()
  {
    var layout = new StackLayout ();
    var button = new Button { VerticalOptions = LayoutOptions.Start,
     HorizontalOptions = LayoutOptions.FillAndExpand };
    var oneBox = new BoxView { VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };
    var twoBox = new BoxView {  VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };
    var threeBox = new BoxView {  VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };

    layout.Children.Add(button);
    layout.Children.Add(oneBox);
    layout.Children.Add(twoBox);
    layout.Children.Add(threeBox);
    Content = layout;
  }
}
```

Para obter mais informações, consulte [alinhamento](~/xamarin-forms/user-interface/layouts/layout-options.md#alignment).

## <a name="exploring-a-complex-layout"></a>Explorar um Layout complexo

Cada um dos layouts têm vantagens e desvantagens para a criação de layouts específicos. Em toda esta série de artigos de layout, um aplicativo de exemplo foi criado com o mesmo layout de página implementado usando três layouts diferentes.

Considere o XAML a seguir:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TheBusinessTumble.StackLayoutPage"
BackgroundColor="Maroon"
Title="StackLayouts">
    <ContentPage.Content>
    <ScrollView>
      <StackLayout Spacing="0" Padding="0" BackgroundColor="Maroon">
        <BoxView HorizontalOptions="FillAndExpand" HeightRequest="100"
          VerticalOptions="Start" Color="Gray" />
        <Button BorderRadius="30" HeightRequest="60" WidthRequest="60"
          BackgroundColor="Red" HorizontalOptions="Center" VerticalOptions="Start" />
        <StackLayout HeightRequest="100" VerticalOptions="Start" HorizontalOptions="FillAndExpand" Spacing="20" BackgroundColor="Maroon">
          <Label Text="User Name" FontSize="28" HorizontalOptions="Center"
            VerticalOptions="Center" FontAttributes="Bold" />
          <Entry Text="Bio + Hashtags" TextColor="White"
            BackgroundColor="Maroon" HorizontalOptions="FillAndExpand" VerticalOptions="CenterAndExpand" />
        </StackLayout>
        <StackLayout Orientation="Horizontal" HeightRequest="50" BackgroundColor="White" Padding="5">
          <StackLayout Spacing="0" BackgroundColor="White" Orientation="Horizontal" HorizontalOptions="Start">
            <BoxView BackgroundColor="Black" WidthRequest="40" HeightRequest="40"  HorizontalOptions="StartAndExpand" VerticalOptions="Center" />
            <Label FontSize="14" TextColor="Black" Text="Accent Color" HorizontalOptions="StartAndExpand" VerticalOptions="Center" />
          </StackLayout>
          <StackLayout Spacing="0" BackgroundColor="White" Orientation="Horizontal" HorizontalOptions="EndAndExpand">
            <BoxView BackgroundColor="Maroon" WidthRequest="40" HeightRequest="40" HorizontalOptions="Start" VerticalOptions="Center" />
            <Label FontSize="14" TextColor="Black" Text="Primary Color" HorizontalOptions="StartAndExpand" VerticalOptions="Center" />
          </StackLayout>
        </StackLayout>
        <StackLayout Orientation="Horizontal">
          <Label FontSize="14" Text="Age:" TextColor="White" HorizontalOptions="Start" VerticalOptions="Center" WidthRequest="100" />
          <Entry HorizontalOptions="FillAndExpand" Text="35" TextColor="White" BackgroundColor="Maroon" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
          <Label FontSize="14" Text="Interests:" TextColor="White"
          HorizontalOptions="Start" VerticalOptions="Center" WidthRequest="100" />
          <Entry HorizontalOptions="FillAndExpand" Text="Xamarin.Forms" TextColor="White" BackgroundColor="Maroon" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
          <Label FontSize="14" Text="Ask me about:" TextColor="White"
          HorizontalOptions="Start" VerticalOptions="Center" WidthRequest="100"/>
          <Entry HorizontalOptions="FillAndExpand" Text="Xamarin, C#, .NET, Mono..." TextColor="White" BackgroundColor="Maroon" />
        </StackLayout>
      </StackLayout>
    </ScrollView>
    </ContentPage.Content>
</ContentPage>

```

O código acima resulta no layout a seguir:

![](stack-layout-images/stack.png "StackLayout complexo")

Observe que, devido a uma diferença em como os botões são renderizados pelo Windows Phone, alguns dos círculos foram substituídas por boxviews na captura de tela do Windows Phone.

Observe que `StackLayouts`s estiverem aninhados, como em alguns casos aninhamento layouts pode ser mais fácil do que apresentar todos os elementos do layout do mesmo. Observe também que, como `StackLayout` não oferece suporte para itens sobrepostos, a página não tem alguns iguarias o layout encontrada nas páginas de layouts de.



## <a name="related-links"></a>Links relacionados

- [LayoutOptions](~/xamarin-forms/user-interface/layouts/layout-options.md)
- [Layout (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Exemplo de BusinessTumble (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
