---
title: XAML Standard (visualização)
description: Este artigo explica como começar a explorar a visualização padrão de XAML no xamarin. Forms.
ms.prod: xamarin
ms.assetid: 24382DF1-BE70-4608-B86F-B79FB23E4A78
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/15/2017
ms.openlocfilehash: d9fb19fb233c25e5dd525c7c157013ef66f4a4f3
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51562751"
---
# <a name="xaml-standard-preview"></a>XAML Standard (visualização)

![Visualizar](~/media/shared/preview.png)

Siga estas etapas para fazer experiências com o padrão de XAML no xamarin. Forms:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Baixe o [visualizar o pacote do NuGet aqui](https://aka.ms/xf-xamlstandard-nuget).
2. Adicione a **Xamarin.Forms.Alias** pacote do NuGet para seus projetos xamarin. Forms .NET Standard e plataforma.
3. Inicializar o pacote com `Alias.Init()`
4. Adicionar um `xmlns:a` referência `xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"`
5. Use os tipos no XAML - consulte o [referência controles](controls.md) para obter mais informações.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Baixe o [visualizar o pacote do NuGet aqui](https://aka.ms/xf-xamlstandard-nuget).
2. Adicione a **Xamarin.Forms.Alias** pacote do NuGet para seus projetos xamarin. Forms .NET Standard e plataforma.
3. Inicializar o pacote com `Alias.Init()`
4. Adicionar um `xmlns:a` referência `xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"`
5. Use os tipos no XAML - consulte o [referência controles](controls.md) para obter mais informações.

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
> Exigir que o xmlns `a:` prefixo em controles XAML padrão é uma limitação da visualização atual.


## <a name="related-links"></a>Links relacionados

- [NuGet de visualização](https://aka.ms/xf-xamlstandard-nuget)
- [Referência de controles](controls.md)
