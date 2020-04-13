---
title: Definir o estilo de um aplicativo Xamarin.Forms multiplataforma
description: Este artigo explica como definir o estilo de um aplicativo do Xamarin.Forms multiplataforma com estilos XAML.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: CCCF8E57-D021-4542-8709-5808570FC26A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/07/2020
ms.openlocfilehash: fbd957c68d7a9aa2f8e44c91fab6174d8ed72014
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "77068727"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>Definir o estilo de um aplicativo Xamarin.Forms multiplataforma

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)

Neste guia de início rápido, você aprenderá a:

- Definir o estilo de um aplicativo Xamarin.Forms usando estilos XAML.

Este início rápido explica como definir o estilo de um aplicativo do Xamarin.Forms multiplataforma com estilos XAML. O aplicativo final é mostrado abaixo:

[![](styling-images/screenshots1-sml.png "Notes Page")](styling-images/screenshots1.png#lightbox "Notes Page")
[![](styling-images/screenshots2-sml.png "Note Entry Page")](styling-images/screenshots2.png#lightbox "Note Entry Page")

### <a name="prerequisites"></a>Pré-requisitos

Você deve concluir com êxito o [início rápido anterior](database.md) antes de tentar este guia de início rápido. Como alternativa, baixe o [exemplo de início rápido anterior](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/) e use-o como o ponto de partida para este guia de início rápido.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Atualizar um aplicativo com o Visual Studio

1. Inicie o Visual Studio e abra a solução Notes.

2. No **Gerenciador de Soluções**, no projeto **Notes**, clique duas vezes em **App.xaml** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">AliceBlue</Color>
            <Color x:Key="NavigationBarColor">#1976D2</Color>
            <Color x:Key="NavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{StaticResource NavigationBarColor}" />
                 <Setter Property="BarTextColor"
                        Value="{StaticResource NavigationBarTextColor}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    Este código define [`Thickness`](xref:Xamarin.Forms.Thickness) um valor, [`Color`](xref:Xamarin.Forms.Color) uma série de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) valores e estilos implícitos para o e [`ContentPage`](xref:Xamarin.Forms.ContentPage). Observe que esses estilos, que estão [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)no nível do aplicativo, podem ser consumidos em toda a aplicação. Para obter mais informações sobre o estilo de XAML, confira [Estilo](deepdive.md#styling) no [Aprofundamento do Guia de Início Rápido do Xamarin.Forms](deepdive.md).

    Salve as alterações em **App.xaml** pressionando **CTRL + S** e feche o arquivo.

3. No **Gerenciador de Soluções**, no projeto **Notes**, clique duas vezes em **NotesPage.xaml** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

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
                              TextColor="Black"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    Esse código adiciona um [`ListView`](xref:Xamarin.Forms.ListView) estilo implícito [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)para o `ListView.Margin` nível de página e define `ResourceDictionary`a propriedade como um valor definido no nível de aplicação . Observe que o estilo implícito `ListView` foi adicionado ao nível de página `ResourceDictionary`, pois ele só é consumido pelo `NotesPage`. Para obter mais informações sobre o estilo de XAML, confira [Estilo](deepdive.md#styling) no [Aprofundamento do Guia de Início Rápido do Xamarin.Forms](deepdive.md).

    Salve as alterações em **NotesPage.xaml** ao pressionar **CTRL+S** e feche o arquivo.

4. No **Gerenciador de Soluções**, no projeto **Notes**, clique duas vezes em **NoteEntryPage.xaml** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

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
                <Setter Property="BackgroundColor" Value="#1976D2" />
                <Setter Property="TextColor" Value="White" />
                <Setter Property="CornerRadius" Value="5" />
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

    Esse código adiciona estilos [`Editor`](xref:Xamarin.Forms.Editor) [`Button`](xref:Xamarin.Forms.Button) implícitos para [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)o e `StackLayout.Margin` visualiza ções ao nível de `ResourceDictionary`página e define a propriedade como um valor definido no nível do aplicativo . Observe que os estilos implícitos `Editor` e `Button` foram adicionados ao `ResourceDictionary` no nível de página, pois eles só são consumidos pelo `NoteEntryPage`. Para obter mais informações sobre o estilo de XAML, confira [Estilo](deepdive.md#styling) no [Aprofundamento do Guia de Início Rápido do Xamarin.Forms](deepdive.md).

    Salve as alterações em **NoteEntryPage.xaml** ao pressionar **CTRL+S** e feche o arquivo.

5. Compile e execute o projeto em cada plataforma. Para obter mais informações, confira [Como criar o guia de início rápido](single-page.md#building-the-quickstart).

    Na **NotesPage**, pressione o botão **+** para navegar até a **NoteEntryPage** e inserir uma observação. Em cada página, observe como o estilo foi alterado no início rápido anterior.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Atualizar um aplicativo com o Visual Studio para Mac

1. Inicie o Visual Studio para Mac e abra o projeto Notes.

2. No **Painel de Soluções**, no projeto **Notes**, clique duas vezes em **App.xaml** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">AliceBlue</Color>
            <Color x:Key="NavigationBarColor">#1976D2</Color>
            <Color x:Key="NavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{StaticResource NavigationBarColor}" />
                 <Setter Property="BarTextColor"
                        Value="{StaticResource NavigationBarTextColor}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    Este código define [`Thickness`](xref:Xamarin.Forms.Thickness) um valor, [`Color`](xref:Xamarin.Forms.Color) uma série de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) valores e estilos implícitos para o e [`ContentPage`](xref:Xamarin.Forms.ContentPage). Observe que esses estilos, que estão [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)no nível do aplicativo, podem ser consumidos em toda a aplicação. Para obter mais informações sobre o estilo de XAML, confira [Estilo](deepdive.md#styling) no [Aprofundamento do Guia de Início Rápido do Xamarin.Forms](deepdive.md).

    Salve as alterações em **App.xaml** escolhendo **Arquivo > Salvar** (ou pressionando **& #8984; + S**) e feche o arquivo.

3. No **Painel de Soluções**, no projeto **Notes**, clique duas vezes em **NotesPage.xaml** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

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
                              TextColor="Black"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    Esse código adiciona um [`ListView`](xref:Xamarin.Forms.ListView) estilo implícito [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)para o `ListView.Margin` nível de página e define `ResourceDictionary`a propriedade como um valor definido no nível de aplicação . Observe que o estilo implícito `ListView` foi adicionado ao nível de página `ResourceDictionary`, pois ele só é consumido pelo `NotesPage`. Para obter mais informações sobre o estilo de XAML, confira [Estilo](deepdive.md#styling) no [Aprofundamento do Guia de Início Rápido do Xamarin.Forms](deepdive.md).

    Salve as alterações em **NotesPage.xaml** ao escolher **Arquivo > Salvar** (ou ao pressionar **& #8984; + S**) e feche o arquivo.

4. No **Painel de Soluções**, no projeto **Notes**, clique duas vezes em **NoteEntryPage.xaml** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

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
                <Setter Property="BackgroundColor" Value="#1976D2" />
                <Setter Property="TextColor" Value="White" />
                <Setter Property="CornerRadius" Value="5" />
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

    Esse código adiciona estilos [`Editor`](xref:Xamarin.Forms.Editor) [`Button`](xref:Xamarin.Forms.Button) implícitos para [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)o e `StackLayout.Margin` visualiza ções ao nível de `ResourceDictionary`página e define a propriedade como um valor definido no nível do aplicativo . Observe que os estilos implícitos `Editor` e `Button` foram adicionados ao `ResourceDictionary` no nível de página, pois eles só são consumidos pelo `NoteEntryPage`. Para obter mais informações sobre o estilo de XAML, confira [Estilo](deepdive.md#styling) no [Aprofundamento do Guia de Início Rápido do Xamarin.Forms](deepdive.md).

    Salve as alterações em **NoteEntryPage.xaml** ao escolher **Arquivo > Salvar** (ou ao pressionar **& #8984; + S**) e feche o arquivo.

5. Compile e execute o projeto em cada plataforma. Para obter mais informações, confira [Como criar o guia de início rápido](single-page.md#building-the-quickstart).

    Na **NotesPage**, pressione o botão **+** para navegar até a **NoteEntryPage** e inserir uma observação. Em cada página, observe como o estilo foi alterado no início rápido anterior.

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

- Definir o estilo de um aplicativo Xamarin.Forms usando estilos XAML.

Para saber mais sobre os conceitos básicos do desenvolvimento de aplicativos usando o Xamarin.Forms, continue com o guia de início rápido aprofundado.

> [!div class="nextstepaction"]
> [Avançar](deepdive.md)

## <a name="related-links"></a>Links relacionados

- [Notas (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)
- [Aprofundamento do Guia de Início Rápido do Xamarin.Forms](deepdive.md)
