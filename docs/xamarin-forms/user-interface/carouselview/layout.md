---
title: Layout CarouselView do Xamarin. Forms
description: Por padrão, um CarouselView exibirá seus itens horizontalmente. No entanto, também é possível uma orientação vertical.
ms.prod: xamarin
ms.assetid: fede0382-c972-4023-a4ea-fe5cadec91a6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/14/2019
ms.openlocfilehash: 0149a66fedd98a94f1c9d96bf8e7e57715d1b90b
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488251"
---
# <a name="xamarinforms-carouselview-layout"></a>Layout CarouselView do Xamarin. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define as seguintes propriedades que controlam o layout:

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout), do tipo `LinearItemsLayout`, especifica o layout a ser usado.
- `PeekAreaInsets`, do tipo [`Thickness`](xref:Xamarin.Forms.Thickness), especifica o quanto tornar os itens adjacentes parcialmente visíveis pelo.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados.

Por padrão, um [`CarouselView`](xref:Xamarin.Forms.CarouselView) exibirá seus itens em uma orientação horizontal. Um único item será exibido na tela, com gestos de passar o dedo, resultando em encaminhamentos e navegação retroativos por meio da coleção de itens. No entanto, também é possível uma orientação vertical. Isso ocorre porque a propriedade [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) é do tipo `LinearItemsLayout`, que herda da classe [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) . A classe `ItemsLayout` define as seguintes propriedades:

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation), do tipo [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation), especifica a direção na qual o [`CarouselView`](xref:Xamarin.Forms.CarouselView) se expande à medida que os itens são adicionados.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), do tipo [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment), especifica como os pontos de ajuste são alinhados com os itens.
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), do tipo [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType), especifica o comportamento dos pontos de ajuste ao rolar.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que as propriedades podem ser destinos de associações de dados. Para obter mais informações sobre pontos de encaixe, consulte [pontos de alinhamento](scrolling.md#snap-points) no guia de [rolagem de CollectionView do Xamarin. Forms](scrolling.md) .

A enumeração [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) define os seguintes membros:

- `Vertical` indica que o [`CarouselView`](xref:Xamarin.Forms.CarouselView) será expandido verticalmente à medida que os itens forem adicionados.
- `Horizontal` indica que o [`CarouselView`](xref:Xamarin.Forms.CarouselView) será expandido horizontalmente conforme os itens forem adicionados.

A classe `LinearItemsLayout` herda da classe [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) e define uma propriedade `ItemSpacing`, do tipo `double`, que representa o espaço vazio em volta de cada item. O valor padrão dessa propriedade é 0 e seu valor sempre deve ser maior ou igual a 0. A classe `LinearItemsLayout` também define membros estáticos `Vertical` e `Horizontal`. Esses membros podem ser usados para criar listas verticais ou horizontais, respectivamente. Como alternativa, um objeto `LinearItemsLayout` pode ser criado, especificando um membro de enumeração [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) como um argumento.

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView) usa os mecanismos de layout nativos para executar o layout.

## <a name="horizontal-layout"></a>Layout horizontal

Por padrão, [`CarouselView`](xref:Xamarin.Forms.CarouselView) exibirá seus itens horizontalmente. Portanto, não é necessário definir a propriedade [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) para usar este layout:

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

Como alternativa, esse layout também pode ser feito definindo a propriedade [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) como um objeto `LinearItemsLayout`, especificando o `Horizontal` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) membro de enumeração como o valor da propriedade `Orientation`:

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

Isso resulta em um layout que cresce horizontalmente à medida que novos itens são adicionados.

## <a name="vertical-layout"></a>Layout vertical

[`CarouselView`](xref:Xamarin.Forms.CarouselView) pode exibir seus itens verticalmente definindo a propriedade [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) como um objeto `LinearItemsLayout`, especificando o membro de enumeração de [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) `Vertical` como o valor da propriedade `Orientation`:

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

Isso resulta em um layout que cresce verticalmente à medida que novos itens são adicionados.

## <a name="partially-visible-adjacent-items"></a>Itens adjacentes parcialmente visíveis

Por padrão, [`CarouselView`](xref:Xamarin.Forms.CarouselView) exibe itens completos ao mesmo tempo. No entanto, esse comportamento pode ser alterado definindo a propriedade `PeekAreaInsets` como um valor `Thickness` que especifica o quanto a tornar os itens adjacentes parcialmente visíveis pelo. Isso pode ser útil para indicar aos usuários que há itens adicionais a serem exibidos. O XAML a seguir mostra um exemplo de como definir essa propriedade:

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

O resultado é que os itens adjacentes são parcialmente expostos na tela.

## <a name="item-spacing"></a>Espaçamento de item

Por padrão, cada item em um [`CarouselView`](xref:Xamarin.Forms.CarouselView) não tem espaço vazio em volta dele. Esse comportamento pode ser alterado definindo as propriedades no layout de itens usado pelo `CarouselView`.

Quando um [`CarouselView`](xref:Xamarin.Forms.CarouselView) define sua propriedade [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) como um objeto `LinearItemsLayout`, a propriedade `LinearItemsLayout.ItemSpacing` pode ser definida como um valor de `double` que representa o espaço vazio em volta de cada item:

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
> A propriedade `LinearItemsLayout.ItemSpacing` tem um conjunto de retorno de chamada de validação, que garante que o valor da propriedade seja sempre maior ou igual a 0.

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

Esse código resulta em um layout vertical, que tem um espaçamento de 20 em volta de cada item.

## <a name="dynamic-resizing-of-items"></a>Redimensionamento dinâmico de itens

Os itens em um [`CarouselView`](xref:Xamarin.Forms.CarouselView) podem ser redimensionados dinamicamente em tempo de execução alterando as propriedades relacionadas ao layout de elementos no [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Por exemplo, o exemplo de código a seguir altera as propriedades [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) e [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) de um objeto [`Image`](xref:Xamarin.Forms.Image) e a propriedade `HeightRequest` de sua [`Frame`](xref:Xamarin.Forms.Frame)pai:

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(150) ? 200 : 150;
    Frame frame = ((Frame)image.Parent.Parent);
    frame.HeightRequest = frame.HeightRequest.Equals(300) ? 350 : 300;
}
```

O manipulador de eventos `OnImageTapped` é executado em resposta a um objeto [`Image`](xref:Xamarin.Forms.Image) que está sendo tocado e altera as dimensões da imagem (e seu quadro pai), para que seja exibido mais facilmente.

## <a name="right-to-left-layout"></a>Layout da direita para a esquerda

[`CarouselView`](xref:Xamarin.Forms.CarouselView) pode definir o layout de seu conteúdo em uma direção de fluxo da direita para a esquerda definindo sua propriedade [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) como [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft). No entanto, a propriedade `FlowDirection`, idealmente, deve ser definida em um layout de página ou raiz, o que faz com que todos os elementos dentro da página ou layout raiz respondam à direção do fluxo:

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

O [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) padrão para um elemento com um pai é [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent). Portanto, o [`CarouselView`](xref:Xamarin.Forms.CarouselView) herda o valor da propriedade `FlowDirection` da [`ContentPage`](xref:Xamarin.Forms.ContentPage).

Para obter mais informações sobre a direção do fluxo, consulte [localização da direita para a esquerda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="related-links"></a>Links relacionados

- [CarouselView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Localização da direita para a esquerda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [CarouselView de Xamarin. Forms](scrolling.md)
