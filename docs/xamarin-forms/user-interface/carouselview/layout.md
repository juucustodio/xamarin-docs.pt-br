---
title: Xamarin.FormsLayout de CarouselView
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 44df710df0272afe3c6f6911381af1a88c8cf923
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140277"
---
# <a name="xamarinforms-carouselview-layout"></a>Xamarin.FormsLayout de CarouselView

![](~/media/shared/preview.png "This API is currently pre-release")

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)define as seguintes propriedades que controlam o layout:

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout), do tipo `LinearItemsLayout` , especifica o layout a ser usado.
- `PeekAreaInsets`, do tipo [`Thickness`](xref:Xamarin.Forms.Thickness) , especifica o quanto tornar os itens adjacentes parcialmente visíveis pelo.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

Por padrão, um [`CarouselView`](xref:Xamarin.Forms.CarouselView) exibirá seus itens em uma orientação horizontal. Um único item será exibido na tela, com gestos de passar o dedo, resultando em encaminhamentos e navegação retroativos por meio da coleção de itens. No entanto, também é possível uma orientação vertical. Isso ocorre porque a [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) propriedade é do tipo `LinearItemsLayout` , que é herdado da [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) classe. A `ItemsLayout` classe define as seguintes propriedades:

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation), do tipo [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) , especifica a direção na qual o [`CarouselView`](xref:Xamarin.Forms.CarouselView) expande como itens são adicionados.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), do tipo [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) , especifica como os pontos de ajuste são alinhados com os itens.
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), do tipo [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) , especifica o comportamento dos pontos de ajuste ao rolar.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados. Para obter mais informações sobre pontos de encaixe, consulte [pontos de alinhamento](scrolling.md#snap-points) no guia de [ Xamarin.Forms rolagem CollectionView](scrolling.md) .

A [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) enumeração define os seguintes membros:

- `Vertical`indica que o [`CarouselView`](xref:Xamarin.Forms.CarouselView) será expandido verticalmente à medida que os itens forem adicionados.
- `Horizontal`indica que o [`CarouselView`](xref:Xamarin.Forms.CarouselView) será expandido horizontalmente conforme os itens são adicionados.

A `LinearItemsLayout` classe herda da [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) classe e define uma `ItemSpacing` propriedade, do tipo `double` , que representa o espaço vazio em volta de cada item. O valor padrão dessa propriedade é 0 e seu valor sempre deve ser maior ou igual a 0. A `LinearItemsLayout` classe também define static `Vertical` e `Horizontal` Members. Esses membros podem ser usados para criar listas verticais ou horizontais, respectivamente. Como alternativa, um `LinearItemsLayout` objeto pode ser criado, especificando um [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) membro de enumeração como um argumento.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)usa os mecanismos de layout nativos para executar o layout.

## <a name="horizontal-layout"></a>Layout horizontal

Por padrão, [`CarouselView`](xref:Xamarin.Forms.CarouselView) o exibirá seus itens horizontalmente. Portanto, não é necessário definir a [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) propriedade para usar este layout:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                <Frame HasShadow="True"
                       BorderColor="DarkGray"
                       CornerRadius="5"
                       Margin="20"
                       HeightRequest="300"
                       HorizontalOptions="Center"
                       VerticalOptions="CenterAndExpand">
                    <StackLayout>
                        <Label Text="{Binding Name}"
                               FontAttributes="Bold"
                               FontSize="Large"
                               HorizontalOptions="Center"
                               VerticalOptions="Center" />
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="150"
                               WidthRequest="150"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Location}"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Details}"
                               FontAttributes="Italic"
                               HorizontalOptions="Center"
                               MaxLines="5"
                               LineBreakMode="TailTruncation" />
                    </StackLayout>
                </Frame>
            </StackLayout>
        </DataTemplate>
    </CarouselView.ItemTemplate>
</CarouselView>
```

Como alternativa, esse layout também pode ser feito definindo a [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) propriedade como um `LinearItemsLayout` objeto, especificando o `Horizontal` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) membro de enumeração como o `Orientation` valor da propriedade:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

Este é o código C# equivalente:

```csharp
CarouselView carouselView = new CarouselView
{
    ...
    ItemsLayout = LinearItemsLayout.Horizontal
};
```

Isso resulta em um layout que cresce horizontalmente à medida que novos itens são adicionados:

[![Captura de tela de um layout horizontal CarouselView, no iOS e no Android](layout-images/horizontal.png "Layout horizontal CarouselView")](layout-images/horizontal-large.png#lightbox "Layout horizontal CarouselView")

## <a name="vertical-layout"></a>Layout vertical

[`CarouselView`](xref:Xamarin.Forms.CarouselView)pode exibir seus itens verticalmente definindo a [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) propriedade como um `LinearItemsLayout` objeto, especificando o `Vertical` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) membro de enumeração como o `Orientation` valor da propriedade:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical" />
    </CarouselView.ItemsLayout>
    <CarouselView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                <Frame HasShadow="True"
                       BorderColor="DarkGray"
                       CornerRadius="5"
                       Margin="20"
                       HeightRequest="300"
                       HorizontalOptions="Center"
                       VerticalOptions="CenterAndExpand">
                    <StackLayout>
                        <Label Text="{Binding Name}"
                               FontAttributes="Bold"
                               FontSize="Large"
                               HorizontalOptions="Center"
                               VerticalOptions="Center" />
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="150"
                               WidthRequest="150"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Location}"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Details}"
                               FontAttributes="Italic"
                               HorizontalOptions="Center"
                               MaxLines="5"
                               LineBreakMode="TailTruncation" />
                    </StackLayout>
                </Frame>
            </StackLayout>
        </DataTemplate>
    </CarouselView.ItemTemplate>
</CarouselView>
```

Este é o código C# equivalente:

```csharp
CarouselView carouselView = new CarouselView
{
    ...
    ItemsLayout = LinearItemsLayout.Vertical
};
```

Isso resulta em um layout que cresce verticalmente à medida que novos itens são adicionados:

[![Captura de tela de um layout vertical CarouselView, no iOS e no Android](layout-images/vertical.png "Layout vertical CarouselView")](layout-images/vertical-large.png#lightbox "Layout vertical CarouselView")

## <a name="partially-visible-adjacent-items"></a>Itens adjacentes parcialmente visíveis

Por padrão, o [`CarouselView`](xref:Xamarin.Forms.CarouselView) exibe itens completos ao mesmo tempo. No entanto, esse comportamento pode ser alterado definindo a `PeekAreaInsets` propriedade com um `Thickness` valor que especifica o quanto a tornar os itens adjacentes parcialmente visíveis pelo. Isso pode ser útil para indicar aos usuários que há itens adicionais a serem exibidos. O XAML a seguir mostra um exemplo de como definir essa propriedade:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    ...
</CarouselView>
```

Este é o código C# equivalente:

```csharp
CarouselView carouselView = new CarouselView
{
    ...
    PeekAreaInsets = new Thickness(100)
};
```

O resultado é que os itens adjacentes são parcialmente expostos na tela:

[![Captura de tela de um CollectionView com itens adjacentes parcialmente visíveis, no iOS e no Android](layout-images/peek-items.png "Indefinições da área de Peek do CarouselView")](layout-images/peek-items-large.png#lightbox "Indefinições de área de pico do CarouselView")

## <a name="item-spacing"></a>Espaçamento de item

Por padrão, não há nenhum espaço entre cada item em um [`CarouselView`](xref:Xamarin.Forms.CarouselView) . Esse comportamento pode ser alterado definindo a `ItemSpacing` propriedade no layout de itens usado pelo `CarouselView` .

Quando um [`CarouselView`](xref:Xamarin.Forms.CarouselView) define sua [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) propriedade para um `LinearItemsLayout` objeto, a `LinearItemsLayout.ItemSpacing` propriedade pode ser definida como um `double` valor que representa o espaço entre os itens:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           ItemSpacing="20" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

> [!NOTE]
> A `LinearItemsLayout.ItemSpacing` propriedade tem um conjunto de retorno de chamada de validação, que garante que o valor da propriedade seja sempre maior ou igual a 0.

Este é o código C# equivalente:

```csharp
CarouselView carouselView = new CarouselView
{
    ...
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        ItemSpacing = 20
    }
};
```

Esse código resulta em um layout vertical, que tem um espaçamento de 20 entre os itens.

## <a name="dynamic-resizing-of-items"></a>Redimensionamento dinâmico de itens

Os itens em um [`CarouselView`](xref:Xamarin.Forms.CarouselView) podem ser redimensionados dinamicamente em tempo de execução alterando as propriedades relacionadas ao layout de elementos no [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . Por exemplo, o exemplo de código a seguir altera as [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) Propriedades e de um [`Image`](xref:Xamarin.Forms.Image) objeto e a `HeightRequest` propriedade de seu pai [`Frame`](xref:Xamarin.Forms.Frame) :

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(150) ? 200 : 150;
    Frame frame = ((Frame)image.Parent.Parent);
    frame.HeightRequest = frame.HeightRequest.Equals(300) ? 350 : 300;
}
```

O `OnImageTapped` manipulador de eventos é executado em resposta a um [`Image`](xref:Xamarin.Forms.Image) objeto que está sendo tocado e altera as dimensões da imagem (e seu pai `Frame` ), para que seja exibido mais facilmente:

[![Captura de tela de um CarouselView com dimensionamento de item dinâmico, no iOS e no Android](layout-images/runtime-resizing.png "Dimensionamento de item dinâmico CarouselView")](layout-images/runtime-resizing-large.png#lightbox "Dimensionamento de item dinâmico CarouselView")

## <a name="right-to-left-layout"></a>Layout da direita para a esquerda

[`CarouselView`](xref:Xamarin.Forms.CarouselView)pode definir o layout de seu conteúdo em uma direção de fluxo da direita para a esquerda definindo sua [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade como [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft) . No entanto, a `FlowDirection` propriedade deve ser idealmente definida em um layout de página ou raiz, o que faz com que todos os elementos dentro da página ou layout raiz respondam à direção do fluxo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CarouselViewDemos.Views.HorizontalTemplateLayoutRTLPage"
             Title="Horizontal layout (RTL FlowDirection)"
             FlowDirection="RightToLeft">    
    <CarouselView ItemsSource="{Binding Monkeys}">
        ...
    </CarouselView>
</ContentPage>
```

O padrão [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) para um elemento com um pai é [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent) . Portanto, o [`CarouselView`](xref:Xamarin.Forms.CarouselView) herda o `FlowDirection` valor da Propriedade do [`ContentPage`](xref:Xamarin.Forms.ContentPage) .

Para obter mais informações sobre a direção do fluxo, consulte [localização da direita para a esquerda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="related-links"></a>Links relacionados

- [CarouselView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Localização da direita para a esquerda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Xamarin.FormsCarouselView rolagem](scrolling.md)
