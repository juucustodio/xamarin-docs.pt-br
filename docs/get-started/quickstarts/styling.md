---
title: Estilizar um aplicativo de plataforma cruzada Xamarin.Forms
description: Este artigo explica como estilizar um aplicativo de plataforma cruzada Xamarin.Forms com estilos XAML.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: CCCF8E57-D021-4542-8709-5808570FC26A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/07/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 099c95af190fc9b43c8e8497eebdeec44aed36cd
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368434"
---
# <a name="style-a-cross-platform-no-locxamarinforms-application"></a>Estilizar um aplicativo de plataforma cruzada Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)

Neste guia de início rápido, você aprenderá a:

- Estilizar um Xamarin.Forms aplicativo usando estilos XAML.

O guia de início rápido explica como estilizar um aplicativo de plataforma cruzada Xamarin.Forms com estilos XAML. O aplicativo final é mostrado abaixo:

Página de entrada de observação [ ![ da página de anotações](styling-images/screenshots1-sml.png)](styling-images/screenshots1.png#lightbox "Página de Observações") 
 [ ![ ](styling-images/screenshots2-sml.png)](styling-images/screenshots2.png#lightbox "Página de Entrada de Observação")

### <a name="prerequisites"></a>Pré-requisitos

Você deve concluir com êxito o [início rápido anterior](database.md) antes de tentar este guia de início rápido. Como alternativa, baixe o [exemplo de início rápido anterior](/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/) e use-o como o ponto de partida para este guia de início rápido.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Atualizar um aplicativo com o Visual Studio

1. Inicie o Visual Studio e abra a solução Notes.

2. No **Gerenciador de Soluções** , no projeto **Notes** , clique duas vezes em **App.xaml** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

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

    Esse código define um [`Thickness`](xref:Xamarin.Forms.Thickness) valor, uma série de [`Color`](xref:Xamarin.Forms.Color) valores e estilos implícitos para [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) e [`ContentPage`](xref:Xamarin.Forms.ContentPage) . Observe que esses estilos, que estão no nível do aplicativo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , podem ser consumidos em todo o aplicativo. Para obter mais informações sobre o estilo XAML, consulte [estilos](deepdive.md#styling) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

    Salve as alterações em **App.xaml** pressionando **CTRL + S** e feche o arquivo.

3. No **Gerenciador de Soluções** , no projeto **Notes** , clique duas vezes em **NotesPage.xaml** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

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

    Esse código adiciona um estilo implícito para o no [`ListView`](xref:Xamarin.Forms.ListView) nível de página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) e define a `ListView.Margin` propriedade como um valor definido no nível do aplicativo `ResourceDictionary` . Observe que o estilo implícito `ListView` foi adicionado ao nível de página `ResourceDictionary`, pois ele só é consumido pelo `NotesPage`. Para obter mais informações sobre o estilo XAML, consulte [estilos](deepdive.md#styling) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

    Salve as alterações em **NotesPage.xaml** ao pressionar **CTRL+S** e feche o arquivo.

4. No **Gerenciador de Soluções** , no projeto **Notes** , clique duas vezes em **NoteEntryPage.xaml** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

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

    Esse código adiciona estilos implícitos para [`Editor`](xref:Xamarin.Forms.Editor) as [`Button`](xref:Xamarin.Forms.Button) exibições e para o nível de página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) e define a `StackLayout.Margin` propriedade com um valor definido no nível do aplicativo `ResourceDictionary` . Observe que os estilos implícitos `Editor` e `Button` foram adicionados ao `ResourceDictionary` no nível de página, pois eles só são consumidos pelo `NoteEntryPage`. Para obter mais informações sobre o estilo XAML, consulte [estilos](deepdive.md#styling) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

    Salve as alterações em **NoteEntryPage.xaml** ao pressionar **CTRL+S** e feche o arquivo.

5. Compile e execute o projeto em cada plataforma. Para obter mais informações, confira [Como criar o guia de início rápido](single-page.md#building-the-quickstart).

    Na **NotesPage** , pressione o botão **+** para navegar até a **NoteEntryPage** e inserir uma observação. Em cada página, observe como o estilo foi alterado no início rápido anterior.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Atualizar um aplicativo com o Visual Studio para Mac

1. Inicie o Visual Studio para Mac e abra o projeto Notes.

2. No **Painel de Soluções** , no projeto **Notes** , clique duas vezes em **App.xaml** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

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

    Esse código define um [`Thickness`](xref:Xamarin.Forms.Thickness) valor, uma série de [`Color`](xref:Xamarin.Forms.Color) valores e estilos implícitos para [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) e [`ContentPage`](xref:Xamarin.Forms.ContentPage) . Observe que esses estilos, que estão no nível do aplicativo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , podem ser consumidos em todo o aplicativo. Para obter mais informações sobre o estilo XAML, consulte [estilos](deepdive.md#styling) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

    Salve as alterações em **App.xaml** escolhendo **Arquivo > Salvar** (ou pressionando **& #8984; + S** ) e feche o arquivo.

3. No **Painel de Soluções** , no projeto **Notes** , clique duas vezes em **NotesPage.xaml** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

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

    Esse código adiciona um estilo implícito para o no [`ListView`](xref:Xamarin.Forms.ListView) nível de página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) e define a `ListView.Margin` propriedade como um valor definido no nível do aplicativo `ResourceDictionary` . Observe que o estilo implícito `ListView` foi adicionado ao nível de página `ResourceDictionary`, pois ele só é consumido pelo `NotesPage`. Para obter mais informações sobre o estilo XAML, consulte [estilos](deepdive.md#styling) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

    Salve as alterações em **NotesPage.xaml** ao escolher **Arquivo > Salvar** (ou ao pressionar **& #8984; + S** ) e feche o arquivo.

4. No **Painel de Soluções** , no projeto **Notes** , clique duas vezes em **NoteEntryPage.xaml** para abri-lo. Em seguida, substitua o código existente pelo código a seguir:

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

    Esse código adiciona estilos implícitos para [`Editor`](xref:Xamarin.Forms.Editor) as [`Button`](xref:Xamarin.Forms.Button) exibições e para o nível de página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) e define a `StackLayout.Margin` propriedade com um valor definido no nível do aplicativo `ResourceDictionary` . Observe que os estilos implícitos `Editor` e `Button` foram adicionados ao `ResourceDictionary` no nível de página, pois eles só são consumidos pelo `NoteEntryPage`. Para obter mais informações sobre o estilo XAML, consulte [estilos](deepdive.md#styling) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

    Salve as alterações em **NoteEntryPage.xaml** ao escolher **Arquivo > Salvar** (ou ao pressionar **& #8984; + S** ) e feche o arquivo.

5. Compile e execute o projeto em cada plataforma. Para obter mais informações, confira [Como criar o guia de início rápido](single-page.md#building-the-quickstart).

    Na **NotesPage** , pressione o botão **+** para navegar até a **NoteEntryPage** e inserir uma observação. Em cada página, observe como o estilo foi alterado no início rápido anterior.

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

- Estilizar um Xamarin.Forms aplicativo usando estilos XAML.

Para saber mais sobre os conceitos básicos do desenvolvimento de aplicativos usando Xamarin.Forms o, continue com o guia de início rápido aprofundado.

> [!div class="nextstepaction"]
> [Próximo](deepdive.md)

## <a name="related-links"></a>Links relacionados

- [Notas (exemplo)](/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)
- [Xamarin.Forms Aprofundamento do guia de início rápido](deepdive.md)