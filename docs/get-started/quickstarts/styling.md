---
title: Definir o estilo de um aplicativo Xamarin.Forms multiplataforma
description: Este artigo explica como aplicar estilo a um aplicativo de plataforma cruzada xamarin. Forms com estilos XAML.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: CCCF8E57-D021-4542-8709-5808570FC26A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/02/2019
ms.openlocfilehash: 1b68afc1f3d3c57a5c336e9d30c97ce2375acb9f
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864339"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>Definir o estilo de um aplicativo xamarin. Forms de plataforma cruzada

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Styled/)

Neste início rápido, você aprenderá como:

- Um aplicativo xamarin. Forms usando os estilos XAML de estilo.

O guia de início rápido explica como definir o estilo de um aplicativo de plataforma cruzada xamarin. Forms com estilos XAML. O aplicativo final é mostrado abaixo:

[![](styling-images/screenshots1-sml.png "Página de anotações")](styling-images/screenshots1.png#lightbox "anotações")
[![](styling-images/screenshots2-sml.png "Observe a página de entrada")](styling-images/screenshots2.png#lightbox "Observação Página de entrada")

### <a name="prerequisites"></a>Pré-requisitos

Você deve concluir com êxito a [guia de início rápido anterior](database.md) antes de tentar este início rápido. Como alternativa, baixe o [exemplo de início rápido anterior](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Database/) e usá-lo como ponto de partida para este início rápido.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Atualizar um aplicativo com o Visual Studio

1. Inicie o Visual Studio e abra a solução de notas.

2. Na **Gerenciador de soluções**, no **notas** do projeto, clique duas vezes em **App. XAML** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">WhiteSmoke</Color>
            <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
            <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
            <Color x:Key="iOSNavigationBarTextColor">Black</Color>
            <Color x:Key="AndroidNavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                           Android={StaticResource AndroidNavigationBarColor}}" />
                 <Setter Property="BarTextColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                           Android={StaticResource AndroidNavigationBarTextColor}}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    Esse código define uma [ `Thickness` ](xref:Xamarin.Forms.Thickness) de valor, uma série de [ `Color` ](xref:Xamarin.Forms.Color) valores e estilos implícitos para o [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) e [ `ContentPage` ](xref:Xamarin.Forms.ContentPage). Observe que esses estilos, que estão no nível do aplicativo [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), podem ser consumidos em todo o aplicativo. Para obter mais informações sobre estilos de XAML, consulte [estilos](deepdive.md#styling) na [aprofundamento de início rápido de xamarin. Forms](deepdive.md).

    Salve as alterações em **App. XAML** pressionando **CTRL + S**e feche o arquivo.

3. Na **Gerenciador de soluções**, no **notas** do projeto, clique duas vezes em **NotesPage.xaml** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type ListView}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>
        </ContentPage.Resources>

        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>

        <ListView x:Name="listView"
                  Margin="{StaticResource PageMargin}"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    Este código adiciona um estilo implícito para o [ `ListView` ](xref:Xamarin.Forms.ListView) para o nível de página [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)e define o `ListView.Margin` propriedade com um valor definido no nível do aplicativo `ResourceDictionary` . Observe que o `ListView` estilo implícito foi adicionado ao nível da página `ResourceDictionary`, porque ele só é consumido pelo `NotesPage`. Para obter mais informações sobre estilos de XAML, consulte [estilos](deepdive.md#styling) na [aprofundamento de início rápido de xamarin. Forms](deepdive.md).

    Salve as alterações em **NotesPage.xaml** pressionando **CTRL + S**e feche o arquivo.

4. Na **Gerenciador de soluções**, no **notas** do projeto, clique duas vezes em **NoteEntryPage.xaml** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NoteEntryPage"
                 Title="Note Entry">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type Editor}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

            <Style TargetType="Button"
                   ApplyToDerivedTypes="True"
                   CanCascade="True">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="LightGray" />
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="5" />
            </Style>
        </ContentPage.Resources>

        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked" />
            </Grid>
        </StackLayout>

    </ContentPage>
    ```

    Esse código adiciona os estilos implícitos para o [ `Editor` ](xref:Xamarin.Forms.Editor) e [ `Button` ](xref:Xamarin.Forms.Button) modos de exibição para o nível de página [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)e define o `StackLayout.Margin` propriedade com um valor definido no nível do aplicativo `ResourceDictionary`. Observe que o `Editor` e `Button` estilos implícitos foram adicionados ao nível da página `ResourceDictionary`, porque eles são consumidos somente pelo `NoteEntryPage`. Para obter mais informações sobre estilos de XAML, consulte [estilos](deepdive.md#styling) na [aprofundamento de início rápido de xamarin. Forms](deepdive.md).

    Salve as alterações em **NoteEntryPage.xaml** pressionando **CTRL + S**e feche o arquivo.

5. Compile e execute o projeto em cada plataforma. Para obter mais informações, consulte [criando o guia de início rápido](single-page.md#building-the-quickstart).

    Sobre o **NotesPage** pressione a **+** botão para navegar para o **NoteEntryPage** e insira uma observação. Em cada página, observe como o estilo foi alterado de início rápido anterior.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Atualizar um aplicativo com o Visual Studio para Mac

1. Inicie o Visual Studio para Mac e abra o projeto de notas.

2. No **painel de soluções**, no **notas** do projeto, clique duas vezes em **App. XAML** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">WhiteSmoke</Color>
            <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
            <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
            <Color x:Key="iOSNavigationBarTextColor">Black</Color>
            <Color x:Key="AndroidNavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                           Android={StaticResource AndroidNavigationBarColor}}" />
                 <Setter Property="BarTextColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                           Android={StaticResource AndroidNavigationBarTextColor}}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    Esse código define uma [ `Thickness` ](xref:Xamarin.Forms.Thickness) de valor, uma série de [ `Color` ](xref:Xamarin.Forms.Color) valores e estilos implícitos para o [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) e [ `ContentPage` ](xref:Xamarin.Forms.ContentPage). Observe que esses estilos, que estão no nível do aplicativo [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), podem ser consumidos em todo o aplicativo. Para obter mais informações sobre estilos de XAML, consulte [estilos](deepdive.md#styling) na [aprofundamento de início rápido de xamarin. Forms](deepdive.md).

    Salve as alterações em **App. XAML** escolhendo **arquivo > Salvar** (ou pressionando  **&#8984; + S**) e feche o arquivo.

3. No **painel de soluções**, no **notas** do projeto, clique duas vezes em **NotesPage.xaml** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type ListView}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>
        </ContentPage.Resources>

        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>

        <ListView x:Name="listView"
                  Margin="{StaticResource PageMargin}"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    Este código adiciona um estilo implícito para o [ `ListView` ](xref:Xamarin.Forms.ListView) para o nível de página [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)e define o `ListView.Margin` propriedade com um valor definido no nível do aplicativo `ResourceDictionary` . Observe que o `ListView` estilo implícito foi adicionado ao nível da página `ResourceDictionary`, porque ele só é consumido pelo `NotesPage`. Para obter mais informações sobre estilos de XAML, consulte [estilos](deepdive.md#styling) na [aprofundamento de início rápido de xamarin. Forms](deepdive.md).

    Salve as alterações em **NotesPage.xaml** escolhendo **arquivo > Salvar** (ou pressionando  **&#8984; + S**) e feche o arquivo.

4. No **painel de soluções**, no **notas** do projeto, clique duas vezes em **NoteEntryPage.xaml** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NoteEntryPage"
                 Title="Note Entry">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type Editor}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

            <Style TargetType="Button"
                   ApplyToDerivedTypes="True"
                   CanCascade="True">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="LightGray" />
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="5" />
            </Style>
        </ContentPage.Resources>

        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked" />
            </Grid>
        </StackLayout>

    </ContentPage>
    ```

    Esse código adiciona os estilos implícitos para o [ `Editor` ](xref:Xamarin.Forms.Editor) e [ `Button` ](xref:Xamarin.Forms.Button) modos de exibição para o nível de página [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)e define o `StackLayout.Margin` propriedade com um valor definido no nível do aplicativo `ResourceDictionary`. Observe que o `Editor` e `Button` estilos implícitos foram adicionados ao nível da página `ResourceDictionary`, porque eles são consumidos somente pelo `NoteEntryPage`. Para obter mais informações sobre estilos de XAML, consulte [estilos](deepdive.md#styling) na [aprofundamento de início rápido de xamarin. Forms](deepdive.md).

    Salve as alterações em **NoteEntryPage.xaml** escolhendo **arquivo > Salvar** (ou pressionando  **&#8984; + S**) e feche o arquivo.

5. Compile e execute o projeto em cada plataforma. Para obter mais informações, consulte [criando o guia de início rápido](single-page.md#building-the-quickstart).

    Sobre o **NotesPage** pressione a **+** botão para navegar para o **NoteEntryPage** e insira uma observação. Em cada página, observe como o estilo foi alterado de início rápido anterior.

::: zone-end


## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como:

- Um aplicativo xamarin. Forms usando os estilos XAML de estilo.

Para saber mais sobre os conceitos básicos do desenvolvimento de aplicativos usando o xamarin. Forms, continue o aprofundamento do guia de início rápido.

> [!div class="nextstepaction"]
> [Avançar](deepdive.md)

## <a name="related-links"></a>Links relacionados

- [Notas (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Styled/)
- [Aprofundamento de início rápido do xamarin. Forms](deepdive.md)
