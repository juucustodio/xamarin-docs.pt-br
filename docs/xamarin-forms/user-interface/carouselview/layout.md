---
title: CarouselView Xamarin. Forms
description: Por padrão, um CarouselView exibirá seus itens em uma lista horizontal. No entanto, ele também tem acesso aos mesmos layouts que CollectionView, incluindo uma orientação vertical.
ms.prod: xamarin
ms.assetid: fede0382-c972-4023-a4ea-fe5cadec91a6
ms.technology: xamarin-forms
author: pauldipietro
ms.author: padipi
ms.date: 09/09/2019
ms.openlocfilehash: 5bbaeead524089ea604cfd9a9fd7f3a85d04e724
ms.sourcegitcommit: e83035c746f165ee6d03f2e9fd0066ee4f20a9fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70908278"
---
# <a name="xamarinforms-carouselview-layouts"></a>Layouts CarouselView do Xamarin. Forms

![](~/media/shared/preview.png "Esta API está atualmente em pré-lançamento")

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CarouselViewDemos/)

## <a name="introduction"></a>Introdução

Grande parte da funcionalidade de layout disponível para CarouselView provém de CollectionView. Você pode consultar a [documentação de layout](../collectionview/layout.md) de CollectionView para ver o uso de vários layouts.

## <a name="differences-from-collectionview"></a>Diferenças de CollectionView

Por padrão, os itens em um CarouselView serão orientados horizontalmente, assim como a função típica de um carrossel em aplicativos.

O CarouselView também fornece algumas propriedades adicionais:

> [!IMPORTANT]
> As propriedades adicionais para CarouselView ainda estão em desenvolvimento e essa lista ainda não foi concluída.

| API | Função |
|---|---|---|
| NumberOfSideItems | Define o número de itens que aparecem para cada lado do item atual. O padrão é 0.
| PeekAreaInsets | Fornece uma maneira de indicar visivelmente ao usuário que o CarouselView tem itens adicionais para rolar, ajustando seu nível de visibilidade adjacente ao item atual.

## <a name="setting-the-number-of-fully-visible-items"></a>Definindo o número de itens totalmente visíveis

Por padrão, o CarouselView exibe um item em sua totalidade na tela. Os usuários podem definir `NumberOfSideItems` a propriedade para permitir que mais itens sejam exibidos adjacentes ao item atual. Observe que qualquer valor definido como `PeekAreaInsets` ainda será aplicado.

```xaml
<StackLayout Margin="20">
    <CarouselView ItemsSource="{Binding Monkeys}" HeightRequest="125" NumberOfSideItems="1">
        <CarouselView.ItemTemplate>
            <DataTemplate>
                <Frame BorderColor="Black">
                    <Grid>
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="Auto" />
                        </Grid.RowDefinitions>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto" />
                            <ColumnDefinition Width="Auto" />
                        </Grid.ColumnDefinitions>
                        <Label Grid.Column="1"
                            Text="{Binding Name}"
                            FontAttributes="Bold"
                            FontSize="14"/>
                        <Label Grid.Row="1"
                            Grid.Column="1"
                            Text="{Binding Location}"
                            FontAttributes="Italic"
                            FontSize="12"
                            VerticalOptions="End" />
                    </Grid>
                </Frame>
            </DataTemplate>
        </CarouselView.ItemTemplate>
    </CarouselView>
</StackLayout>
```

[ ![Captura de tela de um CarouselView com itens laterais, em](carouselview-images/side-items.png "itens do lado") do Android CarouselView] (carouselview-images/side-items-large.png#lightbox "Itens do lado do CarouselView")

## <a name="making-adjacent-items-partially-visible"></a>Tornando os itens adjacentes parcialmente visíveis

Ao usar um CarouselView em seu aplicativo, pode ser útil indicar ao usuário que o CarouselView funciona de forma a definir a `PeekAreaInsets` Propriedade com um valor diferente de zero (o padrão), que os expõe parcialmente na tela.

```xaml
<StackLayout Margin="20">
  <CarouselView ItemsSource="{Binding Monkeys}" HeightRequest="125" PeekAreaInsets="100">
      <CarouselView.ItemTemplate>
          <DataTemplate>
              <Frame BorderColor="Black">
                  <Grid>
                      <Grid.RowDefinitions>
                          <RowDefinition Height="Auto" />
                          <RowDefinition Height="Auto" />
                      </Grid.RowDefinitions>
                      <Grid.ColumnDefinitions>
                          <ColumnDefinition Width="Auto" />
                          <ColumnDefinition Width="Auto" />
                      </Grid.ColumnDefinitions>
                      <Label Grid.Column="1"
                          Text="{Binding Name}"
                          FontAttributes="Bold"
                          FontSize="14"/>
                      <Label Grid.Row="1"
                          Grid.Column="1"
                          Text="{Binding Location}"
                          FontAttributes="Italic"
                          FontSize="12"
                          VerticalOptions="End" />
                  </Grid>
              </Frame>
          </DataTemplate>
      </CarouselView.ItemTemplate>
  </CarouselView>
</StackLayout>
```

[ ![Captura de tela de um CarouselView com itens laterais, em](carouselview-images/peek-area-insets.png "itens do lado") do Android CarouselView] (carouselview-images/peek-area-insets-large.png#lightbox "Itens do lado do CarouselView")

## <a name="related-links"></a>Links relacionados

- [CollectionView (exemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CarouselViewDemos/)
- [Documentação do layout CollectionView](../collectionview/layout.md)
