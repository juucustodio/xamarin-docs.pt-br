---
title: Definir o estilo de um aplicativo Xamarin.Forms multiplataforma
description: Este artigo explica como estilizar um aplicativo Xamarin. Forms de plataforma cruzada com estilos XAML.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: CCCF8E57-D021-4542-8709-5808570FC26A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/02/2019
ms.openlocfilehash: a7e1cdd59b463c38be1a49e962112cb893eed50f
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488862"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>Estilizar um aplicativo Xamarin. Forms de plataforma cruzada

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)

Neste guia de início rápido, você aprenderá a:

- Estilizar um aplicativo Xamarin. Forms usando estilos XAML.

O guia de início rápido explica como estilizar um aplicativo Xamarin. Forms de plataforma cruzada com estilos XAML. O aplicativo final é mostrado abaixo:

[![](styling-images/screenshots1-sml.png "Notes Page")](styling-images/screenshots1.png#lightbox "Notes Page")
[![](styling-images/screenshots2-sml.png "Note Entry Page")](styling-images/screenshots2.png#lightbox "Note Entry Page")

### <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

Você deve concluir com êxito o [início rápido anterior](database.md) antes de tentar este guia de início rápido. Como alternativa, baixe o [exemplo anterior de início rápido](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/) e use-o como o ponto de partida para este guia de início rápido.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Atualizar um aplicativo com o Visual Studio

1. Inicie o Visual Studio e abra a solução observações.

2. No **Gerenciador de soluções**, no projeto do **Notes** , clique duas vezes em **app. XAML** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

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

    Esse código define um valor [`Thickness`](xref:Xamarin.Forms.Thickness) , uma série de valores [`Color`](xref:Xamarin.Forms.Color) e estilos implícitos para o [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) e [`ContentPage`](xref:Xamarin.Forms.ContentPage). Observe que esses estilos, que estão no nível de aplicativo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), podem ser consumidos em todo o aplicativo. Para obter mais informações sobre o estilo XAML, consulte [estilo](deepdive.md#styling) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    Salve as alterações em **app. XAML** pressionando **Ctrl + S**e feche o arquivo.

3. No **Gerenciador de soluções**, no projeto do **Notes** , clique duas vezes em **NotesPage. XAML** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

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

    Esse código adiciona um estilo implícito para o [`ListView`](xref:Xamarin.Forms.ListView) para o nível de página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)e define a propriedade `ListView.Margin` como um valor definido na `ResourceDictionary`de nível de aplicativo. Observe que o `ListView` estilo implícito foi adicionado ao `ResourceDictionary`de nível de página, pois ele só é consumido pelo `NotesPage`. Para obter mais informações sobre o estilo XAML, consulte [estilo](deepdive.md#styling) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    Salve as alterações em **NotesPage. XAML** pressionando **Ctrl + S**e feche o arquivo.

4. No **Gerenciador de soluções**, no projeto do **Notes** , clique duas vezes em **NoteEntryPage. XAML** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

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

    Esse código adiciona estilos implícitos para as exibições [`Editor`](xref:Xamarin.Forms.Editor) e [`Button`](xref:Xamarin.Forms.Button) ao nível de página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)e define a propriedade `StackLayout.Margin` como um valor definido na `ResourceDictionary`de nível de aplicativo. Observe que os estilos implícitos de `Editor` e `Button` foram adicionados ao nível de página `ResourceDictionary`, pois eles só são consumidos pelo `NoteEntryPage`. Para obter mais informações sobre o estilo XAML, consulte [estilo](deepdive.md#styling) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    Salve as alterações em **NoteEntryPage. XAML** pressionando **Ctrl + S**e feche o arquivo.

5. Compile e execute o projeto em cada plataforma. Para obter mais informações, consulte [criando o início rápido](single-page.md#building-the-quickstart).

    No **NotesPage** , pressione o botão **+** para navegar até o **NoteEntryPage** e insira uma observação. Em cada página, observe como o estilo foi alterado no início rápido anterior.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Atualizar um aplicativo com o Visual Studio para Mac

1. Inicie o Visual Studio para Mac e abra o projeto do Notes.

2. No **painel de soluções**, no projeto do **Notes** , clique duas vezes em **app. XAML** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

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

    Esse código define um valor [`Thickness`](xref:Xamarin.Forms.Thickness) , uma série de valores [`Color`](xref:Xamarin.Forms.Color) e estilos implícitos para o [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) e [`ContentPage`](xref:Xamarin.Forms.ContentPage). Observe que esses estilos, que estão no nível de aplicativo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), podem ser consumidos em todo o aplicativo. Para obter mais informações sobre o estilo XAML, consulte [estilo](deepdive.md#styling) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    Salve as alterações em **app. XAML** escolhendo **arquivo > salvar** (ou pressionando  **&#8984; + S**) e feche o arquivo.

3. No **painel de soluções**, no projeto do **Notes** , clique duas vezes em **NotesPage. XAML** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

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

    Esse código adiciona um estilo implícito para o [`ListView`](xref:Xamarin.Forms.ListView) para o nível de página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)e define a propriedade `ListView.Margin` como um valor definido na `ResourceDictionary`de nível de aplicativo. Observe que o `ListView` estilo implícito foi adicionado ao `ResourceDictionary`de nível de página, pois ele só é consumido pelo `NotesPage`. Para obter mais informações sobre o estilo XAML, consulte [estilo](deepdive.md#styling) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    Salve as alterações em **NotesPage. XAML** escolhendo **arquivo > salvar** (ou pressionando  **&#8984; + S**) e feche o arquivo.

4. No **painel de soluções**, no projeto do **Notes** , clique duas vezes em **NoteEntryPage. XAML** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

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

    Esse código adiciona estilos implícitos para as exibições [`Editor`](xref:Xamarin.Forms.Editor) e [`Button`](xref:Xamarin.Forms.Button) ao nível de página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)e define a propriedade `StackLayout.Margin` como um valor definido na `ResourceDictionary`de nível de aplicativo. Observe que os estilos implícitos de `Editor` e `Button` foram adicionados ao nível de página `ResourceDictionary`, pois eles só são consumidos pelo `NoteEntryPage`. Para obter mais informações sobre o estilo XAML, consulte [estilo](deepdive.md#styling) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    Salve as alterações em **NoteEntryPage. XAML** escolhendo **arquivo > salvar** (ou pressionando  **&#8984; + S**) e feche o arquivo.

5. Compile e execute o projeto em cada plataforma. Para obter mais informações, consulte [criando o início rápido](single-page.md#building-the-quickstart).

    No **NotesPage** , pressione o botão **+** para navegar até o **NoteEntryPage** e insira uma observação. Em cada página, observe como o estilo foi alterado no início rápido anterior.

::: zone-end

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Neste guia de início rápido, você aprendeu a:

- Estilizar um aplicativo Xamarin. Forms usando estilos XAML.

Para saber mais sobre os conceitos básicos do desenvolvimento de aplicativos usando o Xamarin. Forms, continue com o guia de início rápido aprofundado.

> [!div class="nextstepaction"]
> [Avançar](deepdive.md)

## <a name="related-links"></a>Links relacionados

- [Notas (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)
- [Detalhamento do guia de início rápido do Xamarin. Forms](deepdive.md)
