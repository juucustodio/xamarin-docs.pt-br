---
title: Padrão XAML (visualização)
description: Este artigo explica como começar a explorar a visualização de padrão de XAML no xamarin. Forms.
ms.prod: xamarin
ms.assetid: 24382DF1-BE70-4608-B86F-B79FB23E4A78
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/15/2017
ms.openlocfilehash: 61e0fa2587ce9a8794dbd32ff9de1f13da857342
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245788"
---
# <a name="xaml-standard-preview"></a>Padrão XAML (visualização)

![Visualizar](~/media/shared/preview.png)

Siga estas etapas para fazer experiências com XAML padrão no xamarin. Forms:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Baixe o [visualizar o pacote do NuGet aqui](https://aka.ms/xf-xamlstandard-nuget).
2. Adicionar o **Xamarin.Forms.Alias** pacote NuGet para seus projetos de plataforma e xamarin. Forms .NET padrão.
3. Inicializar o pacote com `Alias.Init()`
4. Adicionar um `xmlns:a` referência `xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"`
5. Use os tipos em XAML - consulte o [referência controles](controls.md) para obter mais informações.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Baixe o [visualizar o pacote do NuGet aqui](https://aka.ms/xf-xamlstandard-nuget).
2. Adicionar o **Xamarin.Forms.Alias** pacote NuGet para seus projetos de plataforma e xamarin. Forms .NET padrão.
3. Inicializar o pacote com `Alias.Init()`
4. Adicionar um `xmlns:a` referência `xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"`
5. Use os tipos em XAML - consulte o [referência controles](controls.md) para obter mais informações.

-----

O XAML a seguir demonstra alguns controles XAML padrão que está sendo usados em um xamarin. Forms `ContentPage`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ContentPage 
    xmlns="http://xamarin.com/schemas/2014/forms" 
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" 
    xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"
    x:Class="XAMLStandardSample.ItemsPage" 
    Title="{Binding Title}" x:Name="BrowseItemsPage">
    <ContentPage.ToolbarItems>
        <ToolbarItem Text="Add" Clicked="AddItem_Clicked" />
    </ContentPage.ToolbarItems>
    <ContentPage.Content>
        <a:StackPanel>
            <ListView x:Name="ItemsListView" ItemsSource="{Binding Items}" VerticalOptions="FillAndExpand" HasUnevenRows="true" RefreshCommand="{Binding LoadItemsCommand}" IsPullToRefreshEnabled="true" IsRefreshing="{Binding IsBusy, Mode=OneWay}" CachingStrategy="RecycleElement" ItemSelected="OnItemSelected">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <ViewCell>
                            <StackLayout Padding="10">
                                <a:TextBlock Text="{Binding Text}" LineBreakMode="NoWrap" Style="{DynamicResource ListItemTextStyle}" FontSize="16" />
                                <a:TextBlock Text="{Binding Description}" LineBreakMode="NoWrap" Style="{DynamicResource ListItemDetailTextStyle}" FontSize="13" />
                            </StackLayout>
                        </ViewCell>
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
        </a:StackPanel>
    </ContentPage.Content>
</ContentPage>
```

> [!NOTE]
> Exigir o xmlns `a:` prefixo em controles XAML padrão é uma limitação de visualização atual.


## <a name="related-links"></a>Links relacionados

- [Visualização NuGet](https://aka.ms/xf-xamlstandard-nuget)
- [Referência de controles](controls.md)
