---
title: Estilizar um aplicativo de plataforma cruzada Xamarin.Forms
description: Este artigo explica como estilizar um aplicativo de Shell de plataforma cruzada Xamarin.Forms com estilos XAML e usar o Hot recarregamento de XAML para ver essas alterações.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 1C6ED8AF-41B4-4D6C-9BD8-C72D3F05E541
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.custom: contperf-fy21q3
ms.date: 01/26/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0424f3c9cdcde98cef4c414ada33046cd8ce7ee6
ms.sourcegitcommit: 1f391667869a4541dd9b42d78862dc01d69ed160
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99818067"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>Estilizar um aplicativo de plataforma cruzada Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)

Neste guia de início rápido, você aprenderá a:

- Estilizar um Xamarin.Forms aplicativo de shell usando estilos XAML.
- Use o Hot recarregamento de XAML para ver as alterações da interface do usuário sem recompilar seu aplicativo.

O guia de início rápido explica como estilizar um aplicativo de plataforma cruzada Xamarin.Forms com estilos XAML. Além disso, o início rápido usa o Hot recarregamento de XAML para atualizar a interface do usuário do seu aplicativo em execução, sem precisar recompilar o aplicativo. Para obter mais informações sobre o Hot recarregamento de XAML, consulte o [Hot Xamarin.Forms recarregamento de XAML para ](~/xamarin-forms/xaml/hot-reload.md).

O aplicativo final é mostrado abaixo:

Página de entrada de observação da página de [ ![ anotações](styling-images/screenshots1-sml.png)](styling-images/screenshots1.png#lightbox)sobre a 
 [ ![ ](styling-images/screenshots2-sml.png)](styling-images/screenshots2.png#lightbox) 
 [ ![ página](styling-images/screenshots3-sml.png)](styling-images/screenshots3.png#lightbox)

### <a name="prerequisites"></a>Pré-requisitos

Você deve concluir com êxito o [início rápido anterior](database.md) antes de tentar este guia de início rápido. Como alternativa, baixe o [exemplo de início rápido anterior](/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/) e use-o como o ponto de partida para este guia de início rápido.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Atualizar um aplicativo com o Visual Studio

1. Inicie o Visual Studio e abra a solução Notes.

2. Compile e execute o projeto em sua plataforma escolhida. Para obter mais informações, confira [Como criar o guia de início rápido](app.md#building-the-quickstart).

    Deixe o aplicativo em execução e retorne ao Visual Studio.

3. No **Gerenciador de soluções**, no projeto do **Notes** , abra **app. XAML**. Em seguida, substitua o código existente pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8" ?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">

        <!-- Resources used by multiple pages in the application -->         
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppPrimaryColor">#1976D2</Color>
            <Color x:Key="AppBackgroundColor">AliceBlue</Color>
            <Color x:Key="PrimaryColor">Black</Color>
            <Color x:Key="SecondaryColor">White</Color>
            <Color x:Key="TertiaryColor">Silver</Color>

            <!-- Implicit styles -->
            <Style TargetType="ContentPage"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

            <Style TargetType="Button">
                <Setter Property="FontSize"
                        Value="Medium" />
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppPrimaryColor}" />
                <Setter Property="TextColor"
                        Value="{StaticResource SecondaryColor}" />
                <Setter Property="CornerRadius"
                        Value="5" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    Esse código define um [`Thickness`](xref:Xamarin.Forms.Thickness) valor, uma série de [`Color`](xref:Xamarin.Forms.Color) valores e estilos implícitos para os [`ContentPage`](xref:Xamarin.Forms.ContentPage) [`Button`](xref:Xamarin.Forms.Button) tipos e. Observe que esses estilos, que estão no nível do aplicativo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , podem ser consumidos em todo o aplicativo. Para obter mais informações sobre o estilo XAML, consulte [estilos](deepdive.md#styling) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

    Salve as alterações em **app. XAML** pressionando **Ctrl + S**. O Hot recarregamento de XAML atualizará a interface do usuário do aplicativo em execução, sem recompilar o aplicativo. Especificamente, a cor do plano de fundo de cada página será alterada.

4. No **Gerenciador de soluções**, no projeto do **Notes** , abra **AppShell. XAML**. Em seguida, substitua o código existente pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <Shell xmlns="http://xamarin.com/schemas/2014/forms"
           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
           xmlns:views="clr-namespace:Notes.Views"
           x:Class="Notes.AppShell">

        <Shell.Resources>
            <!-- Style Shell elements -->
            <Style x:Key="BaseStyle"
                   TargetType="Element">
                <Setter Property="Shell.BackgroundColor"
                        Value="{StaticResource AppPrimaryColor}" />
                <Setter Property="Shell.ForegroundColor"
                        Value="{StaticResource SecondaryColor}" />
                <Setter Property="Shell.TitleColor"
                        Value="{StaticResource SecondaryColor}" />
                <Setter Property="Shell.TabBarUnselectedColor"
                        Value="#95FFFFFF"/>
            </Style>
            <Style TargetType="TabBar"
                   BasedOn="{StaticResource BaseStyle}" />
        </Shell.Resources>

        <!-- Display a bottom tab bar containing two tabs -->   
        <TabBar>
            <ShellContent Title="Notes"
                          Icon="icon_feed.png"
                          ContentTemplate="{DataTemplate views:NotesPage}" />
            <ShellContent Title="About"
                          Icon="icon_about.png"
                          ContentTemplate="{DataTemplate views:AboutPage}" />
        </TabBar>
    </Shell>
    ```

    Esse código adiciona dois estilos ao `Shell` dicionário de recursos, que definem uma série de [`Color`](xref:Xamarin.Forms.Color) valores usados pelo aplicativo.

    Salve as alterações em **AppShell. XAML** pressionando **Ctrl + S**. O Hot recarregamento de XAML atualizará a interface do usuário do aplicativo em execução, sem recompilar o aplicativo. Especificamente, a cor do plano de fundo do Chrome do shell será alterada.

5. No **Gerenciador de soluções**, no projeto do **Notes** , abra **NotesPage. XAML** na pasta **views** . Em seguida, substitua o código existente pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NotesPage"
                 Title="Notes">

        <ContentPage.Resources>
            <!-- Define a visual state for the Selected state of the CollectionView -->
            <Style TargetType="StackLayout">
                <Setter Property="VisualStateManager.VisualStateGroups">
                    <VisualStateGroupList>
                        <VisualStateGroup x:Name="CommonStates">
                            <VisualState x:Name="Normal" />
                            <VisualState x:Name="Selected">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor"
                                            Value="{StaticResource AppPrimaryColor}" />
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>
                    </VisualStateGroupList>
                </Setter>
            </Style>
        </ContentPage.Resources>

        <!-- Add an item to the toolbar -->
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="Add"
                         Clicked="OnAddClicked" />
        </ContentPage.ToolbarItems>

        <!-- Display notes in a list -->
        <CollectionView x:Name="collectionView"
                        Margin="{StaticResource PageMargin}"
                        SelectionMode="Single"
                        SelectionChanged="OnSelectionChanged">
            <CollectionView.ItemsLayout>
                <LinearItemsLayout Orientation="Vertical"
                                   ItemSpacing="10" />
            </CollectionView.ItemsLayout>
            <!-- Define the appearance of each item in the list -->
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    <StackLayout>
                        <Label Text="{Binding Text}"
                               FontSize="Medium" />
                        <Label Text="{Binding Date}"
                               TextColor="{StaticResource TertiaryColor}"
                               FontSize="Small" />
                    </StackLayout>
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </ContentPage>
    ```

    Esse código adiciona um estilo implícito para o [`StackLayout`](xref:Xamarin.Forms.StackLayout) que define a aparência de cada item selecionado no [`CollectionView`](xref:Xamarin.Forms.CollectionView) , ao nível de página e [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) define a `CollectionView.Margin` `Label.TextColor` propriedade e como valores definidos no nível do aplicativo `ResourceDictionary` . Observe que o estilo implícito `StackLayout` foi adicionado ao nível de página `ResourceDictionary`, pois ele só é consumido pelo `NotesPage`.

    Salve as alterações em **NotesPage. XAML** pressionando **Ctrl + S**. O Hot recarregamento de XAML atualizará a interface do usuário do aplicativo em execução, sem recompilar o aplicativo. Especificamente, a cor dos itens selecionados no [`CollectionView`](xref:Xamarin.Forms.CollectionView) será alterada.

6. No **Gerenciador de soluções**, no projeto do **Notes** , abra **NoteEntryPage. XAML** na pasta **views** . Em seguida, substitua o código existente pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NoteEntryPage"
                 Title="Note Entry">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type Editor}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>         
        </ContentPage.Resources>

        <!-- Layout children vertically -->
        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
                    HeightRequest="100" />
            <Grid ColumnDefinitions="*,*">
                <!-- Layout children in two columns -->
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    Esse código adiciona um estilo implícito para o no [`Editor`](xref:Xamarin.Forms.Editor) nível de página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) e define a `StackLayout.Margin` propriedade como um valor definido no nível do aplicativo `ResourceDictionary` . Observe que os `Editor` estilos implícitos foram adicionados ao nível de página `ResourceDictionary` porque são consumidos apenas pelo `NoteEntryPage` .

7. No aplicativo em execução, navegue até o `NoteEntryPage` .

8. No Visual Studio, salve as alterações em **NoteEntryPage. XAML** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**).

    O Hot recarregamento de XAML atualizará a interface do usuário do aplicativo, sem recriá-lo. Especificamente, a cor do plano de fundo do [`Editor`](xref:Xamarin.Forms.Editor) será alterada no aplicativo em execução, assim como a aparência dos [`Button`](xref:Xamarin.Forms.Button) objetos.

9. No **Gerenciador de soluções**, no projeto do **Notes** , abra **AboutPage. XAML** na pasta **views** . Em seguida, substitua o código existente pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.AboutPage"
                 Title="About">
        <!-- Layout children in two rows -->
        <Grid RowDefinitions="Auto,*">
            <Image Source="xamarin_logo.png"
                   BackgroundColor="{StaticResource AppPrimaryColor}"
                   Opacity="0.85"
                   VerticalOptions="Center"
                   HeightRequest="64" />
            <!-- Layout children vertically -->       
            <StackLayout Grid.Row="1"
                         Margin="{StaticResource PageMargin}"
                         Spacing="20">
                <Label FontSize="22">
                    <Label.FormattedText>
                        <FormattedString>
                            <FormattedString.Spans>
                                <Span Text="Notes"
                                      FontAttributes="Bold"
                                      FontSize="22" />
                                <Span Text=" v1.0" />
                            </FormattedString.Spans>
                        </FormattedString>
                    </Label.FormattedText>
                </Label>
                <Label Text="This app is written in XAML and C# with the Xamarin Platform." />
                <Button Text="Learn more"
                        Clicked="OnButtonClicked" />
            </StackLayout>
        </Grid>
    </ContentPage>
    ```

    Esse código define as `Image.BackgroundColor` `StackLayout.Margin` Propriedades e como valores definidos no nível do aplicativo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) .

10. No aplicativo em execução, navegue até o `AboutPage` .

11. No Visual Studio, salve as alterações em **AboutPage. XAML**  pressionando **Ctrl + S**.

    O Hot recarregamento de XAML atualizará a interface do usuário do aplicativo, sem recriá-lo. Especificamente, a cor do plano de fundo do [`Image`](xref:Xamarin.Forms.Editor) será alterada no aplicativo em execução.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Atualizar um aplicativo com o Visual Studio para Mac

1. Inicie o Visual Studio para Mac e abra o projeto Notes.

2. Compile e execute o projeto em sua plataforma escolhida. Para obter mais informações, confira [Como criar o guia de início rápido](app.md#building-the-quickstart).

    Deixe o aplicativo em execução e retorne para Visual Studio para Mac.

3. No **painel de soluções**, no projeto do **Notes** , abra **app. XAML**. Em seguida, substitua o código existente pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="utf-8" ?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">

        <!-- Resources used by multiple pages in the application -->                 
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppPrimaryColor">#1976D2</Color>
            <Color x:Key="AppBackgroundColor">AliceBlue</Color>
            <Color x:Key="PrimaryColor">Black</Color>
            <Color x:Key="SecondaryColor">White</Color>
            <Color x:Key="TertiaryColor">Silver</Color>

            <!-- Implicit styles -->
            <Style TargetType="ContentPage"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

            <Style TargetType="Button">
                <Setter Property="FontSize"
                        Value="Medium" />
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppPrimaryColor}" />
                <Setter Property="TextColor"
                        Value="{StaticResource SecondaryColor}" />
                <Setter Property="CornerRadius"
                        Value="5" />
            </Style>  
        </Application.Resources>
    </Application>
    ```

    Esse código define um [`Thickness`](xref:Xamarin.Forms.Thickness) valor, uma série de [`Color`](xref:Xamarin.Forms.Color) valores e estilos implícitos para os [`ContentPage`](xref:Xamarin.Forms.ContentPage) [`Button`](xref:Xamarin.Forms.Button) tipos e. Observe que esses estilos, que estão no nível do aplicativo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , podem ser consumidos em todo o aplicativo. Para obter mais informações sobre o estilo XAML, consulte [estilos](deepdive.md#styling) no guia de [ Xamarin.Forms início rápido](deepdive.md)aprofundamento.

    Salve as alterações em **app. XAML** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**). O Hot recarregamento de XAML atualizará a interface do usuário do aplicativo em execução, sem recompilar o aplicativo. Especificamente, a cor do plano de fundo de cada página será alterada.

4. No **painel de soluções**, no projeto do **Notes** , abra **AppShell. XAML**. Em seguida, substitua o código existente pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <Shell xmlns="http://xamarin.com/schemas/2014/forms"
           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
           xmlns:views="clr-namespace:Notes.Views"
           x:Class="Notes.AppShell">

        <Shell.Resources>
            <!-- Style Shell elements -->
            <Style x:Key="BaseStyle"
                   TargetType="Element">
                <Setter Property="Shell.BackgroundColor"
                        Value="{StaticResource AppPrimaryColor}" />
                <Setter Property="Shell.ForegroundColor"
                        Value="{StaticResource SecondaryColor}" />
                <Setter Property="Shell.TitleColor"
                        Value="{StaticResource SecondaryColor}" />
                <Setter Property="Shell.TabBarUnselectedColor"
                        Value="#95FFFFFF"/>
            </Style>
            <Style TargetType="TabBar"
                   BasedOn="{StaticResource BaseStyle}" />
        </Shell.Resources>

        <!-- Display a bottom tab bar containing two tabs -->
        <TabBar>
            <ShellContent Title="Notes"
                          Icon="icon_feed.png"
                          ContentTemplate="{DataTemplate views:NotesPage}" />
            <ShellContent Title="About"
                          Icon="icon_about.png"
                          ContentTemplate="{DataTemplate views:AboutPage}" />
        </TabBar>
    </Shell>
    ```

    Esse código adiciona dois estilos ao `Shell` dicionário de recursos, que definem uma série de [`Color`](xref:Xamarin.Forms.Color) valores usados pelo aplicativo.

    Salve as alterações em **AppShell. XAML** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**). O Hot recarregamento de XAML atualizará a interface do usuário do aplicativo em execução, sem recompilar o aplicativo. Especificamente, a cor do plano de fundo do Chrome do shell será alterada.

5. No **painel de soluções**, no projeto do **Notes** , abra **NotesPage. XAML** na pasta **views** . Em seguida, substitua o código existente pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NotesPage"
                 Title="Notes">

        <ContentPage.Resources>
            <!-- Define a visual state for the Selected state of the CollectionView -->
            <Style TargetType="StackLayout">
                <Setter Property="VisualStateManager.VisualStateGroups">
                    <VisualStateGroupList>
                        <VisualStateGroup x:Name="CommonStates">
                            <VisualState x:Name="Normal" />
                            <VisualState x:Name="Selected">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor"
                                            Value="{StaticResource AppPrimaryColor}" />
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>
                    </VisualStateGroupList>
                </Setter>
            </Style>
        </ContentPage.Resources>

        <!-- Add an item to the toolbar -->
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="Add"
                         Clicked="OnAddClicked" />
        </ContentPage.ToolbarItems>

        <!-- Display notes in a list -->
        <CollectionView x:Name="collectionView"
                        Margin="{StaticResource PageMargin}"
                        SelectionMode="Single"
                        SelectionChanged="OnSelectionChanged">
            <CollectionView.ItemsLayout>
                <LinearItemsLayout Orientation="Vertical"
                                   ItemSpacing="10" />
            </CollectionView.ItemsLayout>
            <!-- Define the appearance of each item in the list -->
            <CollectionView.ItemTemplate>
                <DataTemplate>
                    <StackLayout>
                        <Label Text="{Binding Text}"
                               FontSize="Medium" />
                        <Label Text="{Binding Date}"
                               TextColor="{StaticResource TertiaryColor}"
                               FontSize="Small" />
                    </StackLayout>
                </DataTemplate>
            </CollectionView.ItemTemplate>
        </CollectionView>
    </ContentPage>
    ```

    Esse código adiciona um estilo implícito para o [`StackLayout`](xref:Xamarin.Forms.StackLayout) que define a aparência de cada item selecionado no [`CollectionView`](xref:Xamarin.Forms.CollectionView) , ao nível de página e [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) define a `CollectionView.Margin` `Label.TextColor` propriedade e como valores definidos no nível do aplicativo `ResourceDictionary` . Observe que o estilo implícito `StackLayout` foi adicionado ao nível de página `ResourceDictionary`, pois ele só é consumido pelo `NotesPage`.

    Salve as alterações em **NotesPage. XAML** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**). O Hot recarregamento de XAML atualizará a interface do usuário do aplicativo em execução, sem recompilar o aplicativo. Especificamente, a cor dos itens selecionados no [`CollectionView`](xref:Xamarin.Forms.CollectionView) será alterada.

6. No **painel de soluções**, no projeto do **Notes** , abra **NoteEntryPage. XAML** na pasta **views** . Em seguida, substitua o código existente pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.NoteEntryPage"
                 Title="Note Entry">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type Editor}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>       
        </ContentPage.Resources>

        <!-- Layout children vertically -->
        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
                    HeightRequest="100" />
            <!-- Layout children in two columns -->
            <Grid ColumnDefinitions="*,*">
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked"/>
            </Grid>
        </StackLayout>
    </ContentPage>
    ```

    Esse código adiciona estilos implícitos para o no [`Editor`](xref:Xamarin.Forms.Editor) nível de página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) e define a `StackLayout.Margin` propriedade com um valor definido no nível do aplicativo `ResourceDictionary` . Observe que o `Editor` estilo implícito foi adicionado ao nível de página `ResourceDictionary` porque ele só é consumido pelo `NoteEntryPage` .

7. No aplicativo em execução, navegue até o `NoteEntryPage` .

8. Em Visual Studio para Mac, salve as alterações em **NoteEntryPage. XAML** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**).

    O Hot recarregamento de XAML atualizará a interface do usuário do aplicativo, sem recriá-lo. Especificamente, a cor do plano de fundo do [`Editor`](xref:Xamarin.Forms.Editor) será alterada no aplicativo em execução, assim como a aparência dos [`Button`](xref:Xamarin.Forms.Button) objetos.

9. No **painel de soluções**, no projeto do **Notes** , abra **AboutPage. XAML** na pasta **views** . Em seguida, substitua o código existente pelo código a seguir:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.Views.AboutPage"
                 Title="About">
        <!-- Layout children in two rows -->
        <Grid RowDefinitions="Auto,*">
            <Image Source="xamarin_logo.png"
                   BackgroundColor="{StaticResource AppPrimaryColor}"
                   Opacity="0.85"
                   VerticalOptions="Center"
                   HeightRequest="64" />
            <!-- Layout children vertically -->
            <StackLayout Grid.Row="1"
                         Margin="{StaticResource PageMargin}"
                         Spacing="20">
                <Label FontSize="22">
                    <Label.FormattedText>
                        <FormattedString>
                            <FormattedString.Spans>
                                <Span Text="Notes"
                                      FontAttributes="Bold"
                                      FontSize="22" />
                                <Span Text=" v1.0" />
                            </FormattedString.Spans>
                        </FormattedString>
                    </Label.FormattedText>
                </Label>
                <Label Text="This app is written in XAML and C# with the Xamarin Platform." />
                <Button Text="Learn more"
                        Clicked="OnButtonClicked" />
            </StackLayout>
        </Grid>
    </ContentPage>
    ```

    Esse código define as `Image.BackgroundColor` `StackLayout.Margin` Propriedades e como valores definidos no nível do aplicativo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) .

10. No aplicativo em execução, navegue até o `AboutPage` .

11. Em Visual Studio para Mac, salve as alterações em **AboutPage. XAML** escolhendo **arquivo > salvar** (ou pressionando **&#8984; + S**).

    O Hot recarregamento de XAML atualizará a interface do usuário do aplicativo, sem recriá-lo. Especificamente, a cor do plano de fundo do [`Image`](xref:Xamarin.Forms.Editor) será alterada no aplicativo em execução.

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

- Estilizar um Xamarin.Forms aplicativo de shell usando estilos XAML.
- Use o Hot recarregamento de XAML para ver as alterações da interface do usuário sem recompilar seu aplicativo.

Para saber mais sobre os conceitos básicos do desenvolvimento de aplicativos usando o Xamarin.Forms shell, continue com o guia de início rápido aprofundamento.

> [!div class="nextstepaction"]
> [Próximo](deepdive.md)

## <a name="related-links"></a>Links relacionados

- [Notas (exemplo)](/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)
- [Hot recarregamento de XAML para Xamarin.Forms](~/xamarin-forms/xaml/hot-reload.md)
- [Xamarin.Forms Aprofundamento do guia de início rápido](deepdive.md)
