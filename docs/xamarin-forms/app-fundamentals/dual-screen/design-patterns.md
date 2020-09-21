---
title: Xamarin.Forms Padrões de design de tela dupla
description: Este guia explica como o Xamarin.Forms dá suporte a uma variedade de padrões de design diferentes, otimizados para dispositivos de tela dupla.
ms.prod: xamarin
ms.assetid: 3176d792-6dba-4e00-b463-497c58678ee9
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 74f06f88cd974d774d3b2527505bb45b3db03191
ms.sourcegitcommit: 69bd0fdc698c9b0c0d73217776d7084f32ae88ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90832301"
---
# <a name="no-locxamarinforms-dual-screen-design-patterns"></a>Xamarin.Forms padrões de design de tela dupla

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)

Este guia apresenta nossos padrões de design recomendados para dispositivos de tela dupla com código e amostras para ajudar você a criar interfaces que fornecem experiências de usuário envolventes e úteis.

## <a name="extended-canvas-pattern"></a>Padrão de tela estendida

O padrão de tela estendida trata as duas telas como uma tela grande para exibir um mapa, uma imagem, uma planilha ou outro conteúdo que se beneficia da difusão para consumir o máximo de espaço:

![Exemplo de tela estendida](design-patterns-images/extended-canvas-sample.png)

```xaml
<ContentPage xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples"
             xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:d="http://xamarin.com/schemas/2014/forms/design"
             xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
             mc:Ignorable="d"
             x:Class="Xamarin.Duo.Forms.Samples.ExtendCanvas">
    <Grid>
        <WebView x:Name="webView"
                 HorizontalOptions="FillAndExpand"
                 VerticalOptions="FillAndExpand" />
        <SearchBar x:Name="searchBar"
                   Placeholder="Find a place..."
                   BackgroundColor="DarkGray"
                   Opacity="0.8"
                   HorizontalOptions="FillAndExpand"
                   VerticalOptions="Start" />
    </Grid>
</ContentPage>
```

Neste exemplo. o `Grid` e o conteúdo interno se expandirão para consumir toda a tela disponível, seja com exibição em uma tela ou distribuição em duas telas.

## <a name="master-detail-pattern"></a>Padrão de detalhes mestre

O padrão de detalhes mestre é usado quando o modo de exibição mestre, normalmente uma lista à esquerda, fornece um conteúdo que pode ser selecionado pelo usuário para exibir detalhes sobre este item à direita:

![Exemplo de detalhes mestre](design-patterns-images/master-detail-sample.png)

```xaml
<ContentPage xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples"
             xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen"
             x:Class="Xamarin.Duo.Forms.Samples.MasterDetail">
    <dualScreen:TwoPaneView MinWideModeWidth="4000"
                            MinTallModeHeight="4000">
        <dualScreen:TwoPaneView.Pane1>
            <local:Master x:Name="masterPage" />
        </dualScreen:TwoPaneView.Pane1>
        <dualScreen:TwoPaneView.Pane2>
            <local:Details x:Name="detailsPage" />
        </dualScreen:TwoPaneView.Pane2>
    </dualScreen:TwoPaneView>
</ContentPage>
```

Neste exemplo, você pode usar `TwoPaneView` para definir uma lista em um painel e uma exibição detalhada no outro.

## <a name="two-page-pattern"></a>Padrão de duas páginas

Esse padrão é ideal para o conteúdo destinado a um layout de duas páginas, como um leitor de documento, anotações ou um painel de arte:

![Exemplo de duas páginas](design-patterns-images/two-page-sample.png)

```xaml
<Grid x:Name="layout">
    <CollectionView x:Name="cv"
                    BackgroundColor="LightGray">
        <CollectionView.ItemsLayout>
            <GridItemsLayout SnapPointsAlignment="Start"
                             SnapPointsType="MandatorySingle"
                             Orientation="Horizontal"
                             HorizontalItemSpacing="{Binding Source={x:Reference mainPage}, Path=HingeWidth}" />
        </CollectionView.ItemsLayout>
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Frame BackgroundColor="LightGray"
                       Padding="0"
                       Margin="0"
                       WidthRequest="{Binding Source={x:Reference mainPage}, Path=ContentWidth}"
                       HeightRequest="{Binding Source={x:Reference mainPage}, Path=ContentHeight}">
                    <Frame Margin="20"
                           BackgroundColor="White">
                        <Label FontSize="Large"
                               Text="{Binding .}"
                               VerticalTextAlignment="Center"
                               HorizontalTextAlignment="Center"
                               HorizontalOptions="Center"
                               VerticalOptions="Center" />
                    </Frame>
                </Frame>
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
</Grid>
```

O [`CollectionView`](xref:Xamarin.Forms.CollectionView) , com um layout de grade que se divide na largura da dobradiça, faz uma abordagem ideal para fornecer essa experiência de tela dupla.

## <a name="dual-view-pattern"></a>Padrão de exibição dupla

O padrão de exibição dupla pode ser semelhante à exibição de "duas páginas", mas a diferença está no conteúdo e no cenário do usuário. Nesse padrão, você está comparando o conteúdo lado a lado, talvez para editar um documento ou uma foto, comparar diferentes menus de um restaurante ou para diferenciar um conflito de mesclagem para arquivos de código:

![Exemplo de exibição dupla](design-patterns-images/dual-view-sample.png)

```xaml
<ContentPage xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples"
             xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen"
             x:Class="Xamarin.Duo.Forms.Samples.DualViewListPage">
    <dualScreen:TwoPaneView>
        <dualScreen:TwoPaneView.Pane1>
            <CollectionView x:Name="mapList"
                            SelectionMode="Single">
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <Grid Padding="10,5,10,5">
                            <Frame Visual="Material"
                                   BorderColor="LightGray">
                                <StackLayout Padding="5">
                                    <Label FontSize="Title"
                                           Text="{Binding Title}" />
                                </StackLayout>
                            </Frame>
                        </Grid>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </dualScreen:TwoPaneView.Pane1>
        <dualScreen:TwoPaneView.Pane2>
            <local:DualViewMap x:Name="mapPage" />
        </dualScreen:TwoPaneView.Pane2>
    </dualScreen:TwoPaneView>
</ContentPage>
```

## <a name="companion-pattern"></a>Padrão complementar

O padrão complementar demonstra como é possível usar a segunda tela para fornecer um segundo nível de conteúdo relacionado à exibição principal, como no caso de um aplicativo de desenho, um jogo ou uma edição de mídia:

![Exemplo do painel complementar](design-patterns-images/companion-pane-sample.png)

```xaml
<ContentPage xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples"
             xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:dualscreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen"
             x:Name="mainPage"
             x:Class="Xamarin.Duo.Forms.Samples.CompanionPane"
             BackgroundColor="LightGray"
             Visual="Material">
    <dualscreen:TwoPaneView x:Name="twoPaneView"
                            MinWideModeWidth="4000"
                            MinTallModeHeight="4000">
        <dualscreen:TwoPaneView.Pane1>
            <CarouselView x:Name="cv"
                          BackgroundColor="LightGray"
                          IsScrollAnimated="False" >
                <CarouselView.ItemTemplate>
                    <DataTemplate>
                        <Frame BackgroundColor="LightGray"
                               Padding="0"
                               Margin="0"
                               WidthRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane1.Width}"
                               HeightRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane1.Height}">
                            <Frame Margin="20"
                                   BackgroundColor="White">
                                <Label FontSize="Large"
                                       Text="{Binding ., StringFormat='Slide Content {0}'}"
                                       VerticalTextAlignment="Center"
                                       HorizontalTextAlignment="Center"
                                       HorizontalOptions="Center"
                                       VerticalOptions="Center" />
                            </Frame>
                        </Frame>
                    </DataTemplate>
                </CarouselView.ItemTemplate>
            </CarouselView>
        </dualscreen:TwoPaneView.Pane1>
        <dualscreen:TwoPaneView.Pane2>
            <CollectionView x:Name="indicators"
                            SelectionMode="Single"
                            Margin="20, 20, 20, 20"
                            BackgroundColor="LightGray"
                            WidthRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane2.Width}"
                            ItemsSource="{Binding Source={x:Reference cv}, Path=ItemsSource}">
                <CollectionView.Resources>
                    <ResourceDictionary>
                        <Style TargetType="Frame">
                            <Setter Property="VisualStateManager.VisualStateGroups">
                                <VisualStateGroupList>
                                    <VisualStateGroup x:Name="CommonStates">
                                        <VisualState x:Name="Normal">
                                            <VisualState.Setters>
                                                <Setter Property="Padding"
                                                        Value="0" />
                                            </VisualState.Setters>
                                        </VisualState>
                                        <VisualState x:Name="Selected">
                                            <VisualState.Setters>
                                                <Setter Property="BorderColor"
                                                        Value="Green" />
                                                <Setter Property="Padding"
                                                        Value="1" />
                                            </VisualState.Setters>
                                        </VisualState>
                                    </VisualStateGroup>
                                </VisualStateGroupList>
                            </Setter>
                        </Style>
                    </ResourceDictionary>
                </CollectionView.Resources>
                <CollectionView.ItemsLayout>
                    <LinearItemsLayout Orientation="Vertical"
                                       ItemSpacing="10" />
                </CollectionView.ItemsLayout>
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <Frame WidthRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane2.Width}"
                               CornerRadius="10"
                               HeightRequest="60"
                               BackgroundColor="White"
                               Margin="0">
                            <StackLayout HorizontalOptions="Fill"
                                         VerticalOptions="Fill"
                                         Orientation="Horizontal">
                                <Label FontSize="Micro"
                                       Padding="20,0,20,0"
                                       VerticalTextAlignment="Center"
                                       WidthRequest="140" Text="{Binding ., StringFormat='Slide Content {0}'}" />
                                <Label FontSize="Small"
                                       Padding="20,0,20,0"
                                       VerticalTextAlignment="Center"
                                       HorizontalOptions="FillAndExpand"
                                       BackgroundColor="DarkGray"
                                       Grid.Column="1"
                                       Text="{Binding ., StringFormat='Slide {0}'}" />
                            </StackLayout>
                        </Frame>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </dualscreen:TwoPaneView.Pane2>
    </dualscreen:TwoPaneView>
</ContentPage>
```

## <a name="related-links"></a>Links relacionados

- [DualScreen (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)
- [Criar aplicativos para dispositivos de tela dupla](index.md)
