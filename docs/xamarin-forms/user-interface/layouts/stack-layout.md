---
title: StackLayout do xamarin. Forms
description: Este artigo explica como usar a classe StackLayout do xamarin. Forms para apresentar as coleções de modos de exibição ao longo de uma dimensão.
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/25/2015
ms.openlocfilehash: ad20ba50b8ff0f7dcbba3e8d297b2281544a373b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657081"
---
# <a name="xamarinforms-stacklayout"></a>StackLayout do xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[StackLayout](xref:Xamarin.Forms.StackLayout) organiza as exibições em uma linha unidimensional ("Stack"), tanto horizontal quanto verticalmente. Exibições em um `StackLayout` pode ser dimensionado com base no espaço no layout usando as opções de layout. Posicionamento é determinada pela ordem de modos de exibição foram adicionados para o layout e as opções de layout dos modos de exibição.

[![](stack-layout-images/layouts-sml.png "Xamarin. Forms Layouts")](stack-layout-images/layouts.png#lightbox "Layouts do xamarin. Forms")

## <a name="purpose"></a>Finalidade

`StackLayout` é menos complexo do que outros modos de exibição. Interfaces de lineares simples podem ser criadas simplesmente adicionando modos de exibição para um `StackLayout`e mais complexas interfaces criadas, aninhando-los.

## <a name="usage--behavior"></a>Uso e comportamento

### <a name="spacing"></a>Espaçamento

Por padrão, `StackLayout` será adicionada uma margem de 6px entre modos de exibição. Isso pode ser controlado ou definido como não ter nenhuma margem, definindo o `Spacing` propriedade StackLayout. O exemplo a seguir demonstra como definir o espaçamento e o efeito de opções de espaçamento diferentes:

No XAML:

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
- **EndAndExpand** &ndash; coloca o modo de exibição no final do layout (inferior ou limite à direita) e se expande para ocupar tanto espaço quanto o layout será dê a ele.
- **FillAndExpand** &ndash; coloca o modo de exibição para que ele não tem nenhum preenchimento e ocupa espaço quanto o layout será dê a ele.
- **StartAndExpand** &ndash; coloca o modo de exibição no início do layout e ocupa tanto espaço quanto fornecerá o pai.

Para obter mais informações, consulte [expansão](~/xamarin-forms/user-interface/layouts/layout-options.md#expansion).

### <a name="positioning"></a>Posicionamento

Modos de exibição em um StackLayout podem ser posicionados e dimensionados usando `LayoutOptions`. Cada modo de exibição pode ser dada `VerticalOptions` e `HorizontalOptions`, definindo como os modos de exibição irá se posicionar em relação ao layout. O seguinte predefinidos `LayoutOptions` estão disponíveis:

- **Centro** &ndash; centraliza a exibição no layout.
- **Término** &ndash; coloca o modo de exibição no final do layout (inferior ou limite à direita).
- **Preencher** &ndash; coloca o modo de exibição para que ele não tem nenhum preenchimento.
- **Inicie** &ndash; coloca o modo de exibição no início do layout.

O código a seguir demonstra as opções de layout de configuração:

No XAML:

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

## <a name="exploring-a-complex-layout"></a>Explorando um Layout complexo

Cada um dos layouts têm vantagens e desvantagens para a criação de layouts específicos. Em toda esta série de artigos de layout, um aplicativo de exemplo foi criado com o mesmo layout de página implementado usando três layouts diferentes.

Considere o seguinte XAML:

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

Observe que `StackLayouts`s forem aninhadas, pois em alguns casos layouts de aninhamento pode ser mais fácil do que apresentar todos os elementos do layout do mesmo. Observe também que, porque `StackLayout` não dá suporte itens sobrepostos, a página não tem algumas iguarias o layout encontrados nas páginas para os outros layouts.



## <a name="related-links"></a>Links relacionados

- [LayoutOptions](~/xamarin-forms/user-interface/layouts/layout-options.md)
- [Layout (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Exemplo de BusinessTumble (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
