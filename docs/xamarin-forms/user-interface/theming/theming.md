---
title: Aplicar tema a um Xamarin.Forms aplicativo
description: Eles podem ser implementados em Xamarin.Forms aplicativos criando um ResourceDictionary para cada tema e, em seguida, carregando os recursos com a extensão de marcação DynamicResource.
ms.prod: xamarin
ms.assetId: B7B17F66-4E37-4B50-9A57-351B62BE4FED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 123a0868ce098f8c54e6805a1b2b9a6efb04c238
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375389"
---
# <a name="theme-a-no-locxamarinforms-application"></a>Aplicar tema a um Xamarin.Forms aplicativo

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-theming/)

Xamarin.Forms os aplicativos podem responder a alterações de estilo dinamicamente em tempo de execução usando a `DynamicResource` extensão de marcação. Essa extensão de marcação é semelhante à `StaticResource` extensão de marcação, pois ambas usam uma chave de dicionário para buscar um valor de a [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . No entanto, enquanto a `StaticResource` extensão de marcação executa uma única pesquisa de dicionário, a `DynamicResource` extensão de marcação mantém um link para a chave de dicionário. Portanto, se o valor associado à chave for substituído, a alteração será aplicada ao [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Isso permite que os tempos de execução sejam implementados em Xamarin.Forms aplicativos.

O processo para implementar o tempo de execução em um Xamarin.Forms aplicativo é o seguinte:

1. Defina os recursos para cada tema em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) .
1. Consuma recursos de tema no aplicativo, usando a `DynamicResource` extensão de marcação.
1. Defina um tema padrão no arquivo **app. XAML** do aplicativo.
1. Adicione código para carregar um tema em tempo de execução.

> [!IMPORTANT]
> Use a `StaticResource` extensão de marcação se você não precisar alterar o tema do aplicativo em tempo de execução.

As capturas de tela a seguir mostram as páginas com tema, com o aplicativo iOS usando uma clara Theme e o aplicativo do Android usando um tema escuro:

[![Captura de tela da página principal de um aplicativo com tema, no Ios e no Android](theming-images/main-page-both-themes.png "Página principal do aplicativo com tema")](theming-images/main-page-both-themes-large.png#lightbox "Página principal do aplicativo com tema") 
 [ ![Captura de tela da página de detalhes de um aplicativo com tema, no Ios e no Android](theming-images/detail-page-both-themes.png "Página de detalhes do aplicativo com tema")](theming-images/detail-page-both-themes-large.png#lightbox "Página de detalhes do aplicativo com tema")

> [!NOTE]
> A alteração de um tema em tempo de execução requer o uso de estilos XAML e não é possível usar o CSS no momento.

## <a name="define-themes"></a>Definir temas

Um tema é definido como uma coleção de objetos de recurso armazenados em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) .

O exemplo a seguir mostra o `LightTheme` do aplicativo de exemplo:

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                    x:Class="ThemingDemo.LightTheme">
    <Color x:Key="PageBackgroundColor">White</Color>
    <Color x:Key="NavigationBarColor">WhiteSmoke</Color>
    <Color x:Key="PrimaryColor">WhiteSmoke</Color>
    <Color x:Key="SecondaryColor">Black</Color>
    <Color x:Key="PrimaryTextColor">Black</Color>
    <Color x:Key="SecondaryTextColor">White</Color>
    <Color x:Key="TertiaryTextColor">Gray</Color>
    <Color x:Key="TransparentColor">Transparent</Color>
</ResourceDictionary>
```

O exemplo a seguir mostra o `DarkTheme` do aplicativo de exemplo:

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                    x:Class="ThemingDemo.DarkTheme">
    <Color x:Key="PageBackgroundColor">Black</Color>
    <Color x:Key="NavigationBarColor">Teal</Color>
    <Color x:Key="PrimaryColor">Teal</Color>
    <Color x:Key="SecondaryColor">White</Color>
    <Color x:Key="PrimaryTextColor">White</Color>
    <Color x:Key="SecondaryTextColor">White</Color>
    <Color x:Key="TertiaryTextColor">WhiteSmoke</Color>
    <Color x:Key="TransparentColor">Transparent</Color>
</ResourceDictionary>
```

Cada [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) contém [`Color`](xref:Xamarin.Forms.Color) recursos que definem seus respectivos temas, com cada um `ResourceDictionary` usando valores de chave idênticos. Para obter mais informações sobre dicionários de recursos, consulte [dicionários de recursos](~/xamarin-forms/xaml/resource-dictionaries.md).

> [!IMPORTANT]
> Um arquivo code-behind é necessário para cada um `ResourceDictionary` , que chama o `InitializeComponent` método. Isso é necessário para que um objeto CLR que representa o tema escolhido possa ser criado em tempo de execução.

## <a name="set-a-default-theme"></a>Definir um tema padrão

Um aplicativo requer um tema padrão, para que os controles tenham valores para os recursos que consomem. Um tema padrão pode ser definido mesclando o tema [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) no nível de aplicativo `ResourceDictionary` que é definido em **app. XAML** :

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ThemingDemo.App">
    <Application.Resources>
        <ResourceDictionary Source="Themes/LightTheme.xaml" />
    </Application.Resources>
</Application>
```

Para obter mais informações sobre como mesclar dicionários de recursos, consulte [dicionários de Recursos mesclados](~/xamarin-forms/xaml/resource-dictionaries.md#merged-resource-dictionaries).

## <a name="consume-theme-resources"></a>Consumir recursos de tema

Quando um aplicativo deseja consumir um recurso que é armazenado em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) que representa um tema, ele deve fazer isso com a `DynamicResource` extensão de marcação. Isso garante que, se um tema diferente for selecionado em tempo de execução, os valores do novo tema serão aplicados.

O exemplo a seguir mostra três estilos do aplicativo de exemplo que podem ser aplicados a [`Label`](xref:Xamarin.Forms.Label) objetos:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ThemingDemo.App">
    <Application.Resources>

        <Style x:Key="LargeLabelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{DynamicResource SecondaryTextColor}" />
            <Setter Property="FontSize"
                    Value="30" />
        </Style>

        <Style x:Key="MediumLabelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{DynamicResource PrimaryTextColor}" />
            <Setter Property="FontSize"
                    Value="25" />
        </Style>

        <Style x:Key="SmallLabelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{DynamicResource TertiaryTextColor}" />
            <Setter Property="FontSize"
                    Value="15" />
        </Style>

    </Application.Resources>
</Application>
```

Esses estilos são definidos no dicionário de recursos de nível de aplicativo, para que possam ser consumidos por várias páginas. Cada estilo consome recursos de tema com a `DynamicResource` extensão de marcação.

Esses estilos são então consumidos por páginas:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ThemingDemo"
             x:Class="ThemingDemo.UserSummaryPage"
             Title="User Summary"
             BackgroundColor="{DynamicResource PageBackgroundColor}">
    ...
    <ScrollView>
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="200" />
                <RowDefinition Height="120" />
                <RowDefinition Height="70" />
            </Grid.RowDefinitions>
            <Grid BackgroundColor="{DynamicResource PrimaryColor}">
                <Label Text="Face-Palm Monkey"
                       VerticalOptions="Center"
                       Margin="15"
                       Style="{StaticResource MediumLabelStyle}" />
                ...
            </Grid>
            <StackLayout Grid.Row="1"
                         Margin="10">
                <Label Text="This monkey reacts appropriately to ridiculous assertions and actions."
                       Style="{StaticResource SmallLabelStyle}" />
                <Label Text="  &#x2022; Cynical but not unfriendly."
                       Style="{StaticResource SmallLabelStyle}" />
                <Label Text="  &#x2022; Seven varieties of grimaces."
                       Style="{StaticResource SmallLabelStyle}" />
                <Label Text="  &#x2022; Doesn't laugh at your jokes."
                       Style="{StaticResource SmallLabelStyle}" />
            </StackLayout>
            ...
        </Grid>
    </ScrollView>
</ContentPage>
```

Quando um recurso de tema é consumido diretamente, ele deve ser consumido com a `DynamicResource` extensão de marcação. No entanto, quando um estilo que usa a `DynamicResource` extensão de marcação é consumido, ele deve ser consumido com a `StaticResource` extensão de marcação.

Para obter mais informações sobre estilos, consulte [estilizando Xamarin.Forms aplicativos usando estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md). Para obter mais informações sobre a `DynamicResource` extensão de marcação, consulte [estilos Xamarin.Forms dinâmicos em ](~/xamarin-forms/user-interface/styles/xaml/dynamic.md).

## <a name="load-a-theme-at-runtime"></a>Carregar um tema em tempo de execução

Quando um tema é selecionado em tempo de execução, o aplicativo deve:

1. Remova o tema atual do aplicativo. Isso é feito desmarcando a [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) Propriedade do nível do aplicativo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) .
2. Carregue o tema selecionado. Isso é obtido com a adição de uma instância do tema selecionado à `MergedDictionaries` Propriedade do nível do aplicativo `ResourceDictionary` .

Todos os [`VisualElement`](xref:Xamarin.Forms.VisualElement) objetos que definirem Propriedades com a `DynamicResource` extensão de marcação aplicarão os novos valores de tema. Isso ocorre porque a `DynamicResource` extensão de marcação mantém um link para as chaves de dicionário. Portanto, quando os valores associados às chaves são substituídos, as alterações são aplicadas aos `VisualElement` objetos.

No aplicativo de exemplo, um tema é selecionado por meio de uma página modal que contém um [`Picker`](xref:Xamarin.Forms.Picker) . O código a seguir mostra o `OnPickerSelectionChanged` método, que é executado quando o tema selecionado é alterado:

```csharp
void OnPickerSelectionChanged(object sender, EventArgs e)
{
    Picker picker = sender as Picker;
    Theme theme = (Theme)picker.SelectedItem;

    ICollection<ResourceDictionary> mergedDictionaries = Application.Current.Resources.MergedDictionaries;
    if (mergedDictionaries != null)
    {
        mergedDictionaries.Clear();

        switch (theme)
        {
            case Theme.Dark:
                mergedDictionaries.Add(new DarkTheme());
                break;
            case Theme.Light:
            default:
                mergedDictionaries.Add(new LightTheme());
                break;
        }
    }
}
```

## <a name="related-links"></a>Links relacionados

- [Temas (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-theming/)
- [Responder às alterações do tema do sistema](system-theme-changes.md)
- [Dicionários de recurso](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilos dinâmicos em Xamarin.Forms](~/xamarin-forms/user-interface/styles/xaml/dynamic.md)
- [Estilizando Xamarin.Forms aplicativos usando estilos XAML](~/xamarin-forms/user-interface/styles/xaml/index.md)