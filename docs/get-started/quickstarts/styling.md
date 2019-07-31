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
ms.openlocfilehash: e26a71ad72b557a27841bfee1d26001126e2a2a2
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68654666"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>Estilizar um aplicativo Xamarin. Forms de plataforma cruzada

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)

Neste guia de início rápido, você aprenderá a:

- Estilizar um aplicativo Xamarin. Forms usando estilos XAML.

O guia de início rápido explica como estilizar um aplicativo Xamarin. Forms de plataforma cruzada com estilos XAML. O aplicativo final é mostrado abaixo:

[ ![(styling-images/screenshots1-sml.png " ")]Página de anotações] (styling-images/screenshots1.png#lightbox "Página de anotações") (styling-images/screenshots2.png#lightbox "Página de entrada de observação") da página de entrada do note [ ![(styling-images/screenshots2-sml.png " ")]] 


### <a name="prerequisites"></a>Pré-requisitos

Você deve concluir com êxito o [início rápido anterior](database.md) antes de tentar este guia de início rápido. Como alternativa, baixe o [exemplo anterior de início rápido](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/) e use-o como o ponto de partida para este guia de início rápido.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Atualizar um aplicativo com o Visual Studio

1. Inicie o Visual Studio e abra a solução observações.

2. No **Gerenciador de soluções**, no projeto do Notes, clique duas vezes em **app. XAML** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

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

    Esse código define um [`Thickness`](xref:Xamarin.Forms.Thickness) valor, uma série de [`Color`](xref:Xamarin.Forms.Color) valores [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) e estilos implícitos para e [`ContentPage`](xref:Xamarin.Forms.ContentPage). Observe que esses estilos, que estão no nível [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)do aplicativo, podem ser consumidos em todo o aplicativo. Para obter mais informações sobre o estilo XAML, consulte [estilo](deepdive.md#styling) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    Salve as alterações em **app. XAML** pressionando **Ctrl + S**e feche o arquivo.

3. No **Gerenciador de soluções**, no projeto do Notes, clique duas vezes em **NotesPage. XAML** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

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

    Esse código adiciona um estilo implícito para o [`ListView`](xref:Xamarin.Forms.ListView) no nível [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de página e define a `ListView.Margin` Propriedade como um valor definido no nível `ResourceDictionary`do aplicativo. Observe que o `ListView` estilo implícito foi adicionado ao nível `ResourceDictionary`de página, pois ele só `NotesPage`é consumido pelo. Para obter mais informações sobre o estilo XAML, consulte [estilo](deepdive.md#styling) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    Salve as alterações em **NotesPage. XAML** pressionando **Ctrl + S**e feche o arquivo.

4. No **Gerenciador de soluções**, no projeto do Notes, clique duas vezes em **NoteEntryPage. XAML** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

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

    Esse código adiciona estilos implícitos para [`Editor`](xref:Xamarin.Forms.Editor) as [`Button`](xref:Xamarin.Forms.Button) exibições e para o nível [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de página e define `StackLayout.Margin` a propriedade com um valor definido no nível `ResourceDictionary`do aplicativo. Observe que os `Editor` estilos `Button` implícitos e foram adicionados ao nível `ResourceDictionary`de página `NoteEntryPage`, porque eles são consumidos apenas pelo. Para obter mais informações sobre o estilo XAML, consulte [estilo](deepdive.md#styling) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    Salve as alterações em **NoteEntryPage. XAML** pressionando **Ctrl + S**e feche o arquivo.

5. Compile e execute o projeto em cada plataforma. Para obter mais informações, consulte [criando o início rápido](single-page.md#building-the-quickstart).

    No **NotesPage** , pressione o **+** botão para navegar até o **NoteEntryPage** e insira uma observação. Em cada página, observe como o estilo foi alterado no início rápido anterior.

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

    Esse código define um [`Thickness`](xref:Xamarin.Forms.Thickness) valor, uma série de [`Color`](xref:Xamarin.Forms.Color) valores [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) e estilos implícitos para e [`ContentPage`](xref:Xamarin.Forms.ContentPage). Observe que esses estilos, que estão no nível [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)do aplicativo, podem ser consumidos em todo o aplicativo. Para obter mais informações sobre o estilo XAML, consulte [estilo](deepdive.md#styling) no guia de [início rápido do Xamarin. Forms](deepdive.md).

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

    Esse código adiciona um estilo implícito para o [`ListView`](xref:Xamarin.Forms.ListView) no nível [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de página e define a `ListView.Margin` Propriedade como um valor definido no nível `ResourceDictionary`do aplicativo. Observe que o `ListView` estilo implícito foi adicionado ao nível `ResourceDictionary`de página, pois ele só `NotesPage`é consumido pelo. Para obter mais informações sobre o estilo XAML, consulte [estilo](deepdive.md#styling) no guia de [início rápido do Xamarin. Forms](deepdive.md).

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

    Esse código adiciona estilos implícitos para [`Editor`](xref:Xamarin.Forms.Editor) as [`Button`](xref:Xamarin.Forms.Button) exibições e para o nível [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de página e define `StackLayout.Margin` a propriedade com um valor definido no nível `ResourceDictionary`do aplicativo. Observe que os `Editor` estilos `Button` implícitos e foram adicionados ao nível `ResourceDictionary`de página `NoteEntryPage`, porque eles são consumidos apenas pelo. Para obter mais informações sobre o estilo XAML, consulte [estilo](deepdive.md#styling) no guia de [início rápido do Xamarin. Forms](deepdive.md).

    Salve as alterações em **NoteEntryPage. XAML** escolhendo **arquivo > salvar** (ou pressionando  **&#8984; + S**) e feche o arquivo.

5. Compile e execute o projeto em cada plataforma. Para obter mais informações, consulte [criando o início rápido](single-page.md#building-the-quickstart).

    No **NotesPage** , pressione o **+** botão para navegar até o **NoteEntryPage** e insira uma observação. Em cada página, observe como o estilo foi alterado no início rápido anterior.

::: zone-end


## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

- Estilizar um aplicativo Xamarin. Forms usando estilos XAML.

Para saber mais sobre os conceitos básicos do desenvolvimento de aplicativos usando o Xamarin. Forms, continue com o guia de início rápido aprofundado.

> [!div class="nextstepaction"]
> [Avançar](deepdive.md)

## <a name="related-links"></a>Links relacionados

- [Notas (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)
- [Detalhamento do guia de início rápido do Xamarin. Forms](deepdive.md)
