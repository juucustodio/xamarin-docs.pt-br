---
title: Xamarin.FormsIndicatorView
description: O IndicatorView é um controle que exibe os indicadores que representam o número de itens e a posição atual em um CarouselView.
ms.prod: xamarin
ms.assetId: BBCC223B-4B02-46B7-80BB-EE0E86A67CE2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0e6d223fd10e7b792f2d145a7cdd417865a095bb
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101437"
---
# <a name="xamarinforms-indicatorview"></a>Xamarin.FormsIndicatorView

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)

O `IndicatorView` é um controle que exibe os indicadores que representam o número de itens e a posição atual, em um `CarouselView` :

[![Captura de tela de um CarouselView e IndicatorView, no iOS e no Android](indicatorview-images/circles.png "Círculos de IndicatorView")](indicatorview-images/circles-large.png#lightbox "Círculos de IndicatorView")

`IndicatorView` define as propriedades a seguir:

- `Count`, do tipo `int` , o número de indicadores.
- `HideSingle`, do tipo `bool` , indica se o indicador deve estar oculto quando apenas um existe. O valor padrão é `true`.
- `IndicatorColor`, do tipo `Color` , a cor dos indicadores.
- `IndicatorSize`, do tipo `double` , o tamanho dos indicadores. O valor padrão é 6,0.
- `IndicatorLayout`, do tipo `Layout<View>` , define a classe de layout usada para renderizar o `IndicatorView` . Essa propriedade é definida por Xamarin.Forms e normalmente não precisa ser definida pelos desenvolvedores.
- `IndicatorTemplate`, do tipo `DataTemplate` , o modelo que define a aparência de cada indicador.
- `IndicatorsShape`, do tipo `IndicatorShape` , a forma de cada indicador.
- `ItemsSource`, do tipo `IEnumerable` , a coleção para a qual os indicadores serão exibidos. Essa propriedade será definida automaticamente quando a `CarouselView.IndicatorView` propriedade for definida.
- `MaximumVisible`, do tipo `int` , o número máximo de indicadores visíveis. O valor padrão é `int.MaxValue`.
- `Position`, do tipo `int` , o índice do indicador selecionado no momento. Essa propriedade usa uma `TwoWay` associação. Essa propriedade será definida automaticamente quando a `CarouselView.IndicatorView` propriedade for definida.
- `SelectedIndicatorColor`, do tipo `Color` , a cor do indicador que representa o item atual no `CarouselView` .

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

## <a name="create-an-indicatorview"></a>Criar um IndicatorView

O exemplo a seguir mostra como criar uma instância de `IndicatorView` em XAML:

```xaml
<StackLayout>
    <CarouselView ItemsSource="{Binding Monkeys}"
                  IndicatorView="indicatorView">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView x:Name="indicatorView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="DarkGray"
                   HorizontalOptions="Center" />
</StackLayout>
```

Neste exemplo, o `IndicatorView` é renderizado abaixo do `CarouselView` , com um indicador para cada item no `CarouselView` . O `IndicatorView` é preenchido com dados definindo a `CarouselView.IndicatorView` propriedade para o `IndicatorView` objeto. Cada indicador é um círculo cinza claro, enquanto o indicador que representa o item atual no `CarouselView` é cinza escuro.

> [!IMPORTANT]
> Definir a `CarouselView.IndicatorView` Propriedade resulta na `IndicatorView.Position` Associação de propriedade à `CarouselView.Position` propriedade e a associação de `IndicatorView.ItemsSource` Propriedade à `CarouselView.ItemsSource` propriedade.

## <a name="change-indicator-shape"></a>Alterar forma do indicador

A `IndicatorView` classe tem uma `IndicatorsShape` propriedade, que determina a forma dos indicadores. Essa propriedade pode ser definida como um dos `IndicatorShape` membros da enumeração:

- `Circle`Especifica que as formas de indicador serão circulares. Este é o valor padrão da propriedade `IndicatorView.IndicatorsShape`.
- `Square`indica que as formas de indicador serão quadradas.

O exemplo a seguir mostra um `IndicatorView` configurado para usar indicadores quadrados:

```xaml
<IndicatorView x:Name="indicatorView"
               IndicatorsShape="Square"
               IndicatorColor="LightGray"
               SelectedIndicatorColor="DarkGray" />
```

## <a name="change-indicator-size"></a>Alterar tamanho do indicador

A `IndicatorView` classe tem uma `IndicatorSize` propriedade, do tipo `double` , que determina o tamanho dos indicadores em unidades independentes do dispositivo. O valor padrão dessa propriedade é 6,0.

O exemplo a seguir mostra um `IndicatorView` configurado para exibir indicadores maiores:

```xaml
<IndicatorView x:Name="indicatorView"
               IndicatorSize="18" />
```

## <a name="limit-the-number-of-indicators-displayed"></a>Limitar o número de indicadores exibidos

A `IndicatorView` classe tem uma `MaximumVisible` propriedade, do tipo `int` , que determina o número máximo de indicadores visíveis.

O exemplo a seguir mostra um `IndicatorView` configurado para exibir um máximo de seis indicadores:

```xaml
<IndicatorView x:Name="indicatorView"
               MaximumVisible="6" />
```

## <a name="define-indicator-appearance"></a>Definir aparência do indicador

A aparência de cada indicador pode ser definida definindo a `IndicatorView.IndicatorTemplate` propriedade como a [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

```xaml
<StackLayout>
    <CarouselView ItemsSource="{Binding Monkeys}"
                  IndicatorView="indicatorView">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView x:Name="indicatorView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="Black"
                   HorizontalOptions="Center">
        <IndicatorView.IndicatorTemplate>
            <DataTemplate>
                <Image Source="{FontImage &#xf30c;, FontFamily={OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=12}" />
            </DataTemplate>
        </IndicatorView.IndicatorTemplate>
    </IndicatorView>
</StackLayout>
```

Os elementos especificados no [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) definem a aparência de cada indicador. Neste exemplo, cada indicador é um [`Image`](xref:Xamarin.Forms.Image) que exibe um ícone de fonte usando a `FontImage` extensão de marcação.

As capturas de tela a seguir mostram indicadores renderizados usando um ícone de fonte:

[![Captura de tela de um modelo IndicatorView, no iOS e no Android](indicatorview-images/templated.png "Modelo IndicatorView")](indicatorview-images/templated-large.png#lightbox "Modelo IndicatorView")

Para obter mais informações sobre a `FontImage` extensão de marcação, consulte [FontImage Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#fontimage-markup-extension).

## <a name="related-links"></a>Links relacionados

- [IndicatorView (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)
- [Extensão de marcação FontImage](~/xamarin-forms/xaml/markup-extensions/consuming.md#fontimage-markup-extension)
