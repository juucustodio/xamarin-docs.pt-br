---
title: "Dicionários de recursos"
description: "Recursos XAML são definições de objetos que podem ser usados mais de uma vez. Um dicionário de recurso permite que os recursos a serem definidos em um único local e reutilizadas em um aplicativo xamarin. Forms. Este artigo explica como criar e consumir um dicionário de recurso e como mesclar dicionários de recursos."
ms.topic: article
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/17/2017
ms.openlocfilehash: 9602e4d99e8f5c004fe75ab724bb3746aca46003
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="resource-dictionaries"></a>Dicionários de recursos

_Recursos XAML são definições de objetos que podem ser usados mais de uma vez. Um dicionário de recurso permite que os recursos a serem definidos em um único local e reutilizadas em um aplicativo xamarin. Forms. Este artigo explica como criar e consumir um dicionário de recurso e como mesclar dicionários de recursos._

## <a name="overview"></a>Visão geral

Um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) é um repositório de recursos que são usados por um aplicativo xamarin. Forms. Recursos comuns que são armazenados em um `ResourceDictionary` incluem [estilos](~/xamarin-forms/user-interface/styles/index.md), [modelos de controle](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md), [modelos de dados](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md), cores e conversores.

Em XAML, os recursos são definidos em um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) e, em seguida, recuperados e aplicados aos elementos usando o `StaticResource` extensão de marcação. No c#, os recursos são definidos em um `ResourceDictionary` recuperadas e aplicadas a elementos por meio de um indexador de cadeia de caracteres. No entanto, há pouca vantagem em usar um `ResourceDictionary` em c#, como recursos podem facilmente ser atribuídos diretamente a propriedades de elementos visuais sem precisar primeiro recuperá-los de um `ResourceDictionary`.

## <a name="creating-and-consuming-a-resourcedictionary"></a>Criação e consumo de um dicionário de recurso

Recursos podem ser definidos em um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) que está associada a [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) coleção de um controle ou página, ou para o [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Resources/) coleção do aplicativo. Escolhendo onde definir um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) impactos onde ele pode ser usado:

- Recursos em um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) definido no controle nível só pode ser aplicado para o controle e seus filhos.
- Recursos em um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) definidos na página de nível só pode ser aplicado à página e a seus filhos.
- Recursos em um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) definidos no aplicativo de nível pode ser aplicado em todo o aplicativo.

O exemplo de código XAML a seguir mostra os recursos definidos no nível do aplicativo [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/):

```xaml
<Application ...>
    <Application.Resources>
        <ResourceDictionary>
            <Color x:Key="PageBackgroundColor">Yellow</Color>
            <Color x:Key="HeadingTextColor">Black</Color>
            <Color x:Key="NormalTextColor">Blue</Color>
            <Style x:Key="LabelPageHeadingStyle" TargetType="Label">
                <Setter Property="FontAttributes" Value="Bold" />
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="TextColor" Value="{StaticResource HeadingTextColor}" />
            </Style>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

Isso [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) define três [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) recursos e um [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) recursos. Para obter mais informações sobre como criar uma XAML `App` de classe, consulte [classe App](~/xamarin-forms/app-fundamentals/application-class.md).

Cada recurso tem uma chave que é especificada usando o `x:Key` atributo, que dá a ele uma chave descritiva no `ResourceDictionary`. A chave é usada para recuperar um recurso do [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) pelo `StaticResource` extensão de marcação, como demonstrado no seguinte exemplo de código XAML que mostra os recursos adicionais definidos em um controle de nível `ResourceDictionary`:

```xaml
<StackLayout Margin="0,20,0,0">
  <StackLayout.Resources>
    <ResourceDictionary>
      <Style x:Key="LabelNormalStyle" TargetType="Label">
        <Setter Property="TextColor" Value="{StaticResource NormalTextColor}" />
      </Style>
      <Style x:Key="MediumBoldText" TargetType="Button">
        <Setter Property="FontSize" Value="Medium" />
        <Setter Property="FontAttributes" Value="Bold" />
      </Style>
    </ResourceDictionary>
  </StackLayout.Resources>
  <Label Text="ResourceDictionary Demo" Style="{StaticResource LabelPageHeadingStyle}" />
    <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries."
           Margin="10,20,10,0"
           Style="{StaticResource LabelNormalStyle}" />
    <Button Text="Navigate"
            Clicked="OnNavigateButtonClicked"
            TextColor="{StaticResource NormalTextColor}"
            Margin="0,20,0,0"
            HorizontalOptions="Center"
            Style="{StaticResource MediumBoldText}" />
</StackLayout>
```

A primeira [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instância recupera e consome o `LabelPageHeadingStyle` recursos definidos no nível do aplicativo [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/), com a segunda `Label` instância Recuperando e consumir o `LabelNormalStyle` definido no nível de controle de recurso `ResourceDictionary`. Da mesma forma, o [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) instância recupera e consome o `NormalTextColor` recursos definidos no nível do aplicativo `ResourceDictionary`e o `MediumBoldText` definido no nível de controle de recurso `ResourceDictionary`. Isso resulta na exibição mostrada nas capturas de tela seguir:

[![](resource-dictionaries-images/screenshots-sml.png "Consumindo recursos ResourceDictionary")](resource-dictionaries-images/screenshots.png#lightbox "consumindo recursos ResourceDictionary")

> [!NOTE]
> Recursos que são específicos para uma única página não podem ser incluídos em um aplicativo nível dicionário de recursos, como tal, recursos, em seguida, serão analisados na inicialização do aplicativo em vez de quando necessário por uma página. Para obter mais informações, consulte [reduzir o tamanho de dicionário de recurso de aplicativo](~/xamarin-forms/deploy-test/performance.md).

## <a name="overriding-resources"></a>Substituição de recursos

Quando [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) compartilham recursos `x:Key` valores de atributo, recursos definidos inferior na hierarquia do modo de exibição têm precedência sobre aquelas definidas superior para cima. Por exemplo, se você definir o `PageBackgroundColor` recurso `Blue` no aplicativo de nível será substituído por um nível de página `PageBackgroundColor` do conjunto de recursos `Yellow`. Da mesma forma, um nível de página `PageBackgroundColor` recurso será substituído por um nível de controle `PageBackgroundColor` recursos. Essa precedência é demonstrada pelo exemplo de código XAML a seguir:

```xaml
<ContentPage ... BackgroundColor="{StaticResource PageBackgroundColor}">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Color x:Key="PageBackgroundColor">Blue</Color>
            <Color x:Key="NormalTextColor">Yellow</Color>
        </ResourceDictionary>
    </ContentPage.Resources>
    <StackLayout Margin="0,20,0,0">
        ...
        <Label Text="ResourceDictionary Demo" Style="{StaticResource LabelPageHeadingStyle}" />
        <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries."
               Margin="10,20,10,0"
               Style="{StaticResource LabelNormalStyle}" />
        <Button Text="Navigate"
                Clicked="OnNavigateButtonClicked"
                TextColor="{StaticResource NormalTextColor}"
                Margin="0,20,0,0"
                HorizontalOptions="Center"
                Style="{StaticResource MediumBoldText}" />
    </StackLayout>
</ContentPage>
```

O original `PageBackgroundColor` e `NormalTextColor` instâncias, definidas no nível do aplicativo, são substituídas pelo `PageBackgroundColor` e `NormalTextColor` instâncias definidas no nível da página. Portanto, a cor de plano de fundo da página se tornará azul e o texto da página se torna amarelo, conforme demonstrado nas capturas de tela seguir:

[![](resource-dictionaries-images/overridding-screenshots-sml.png "Substituindo recursos ResourceDictionary")](resource-dictionaries-images/overridding-screenshots.png#lightbox "substituindo ResourceDictionary recursos")

No entanto, observe que a barra de plano de fundo do [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) ainda amarelo, porque o [ `BarBackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.BarBackgroundColor/) propriedade é definida como o valor do `PageBackgroundColor` recursos definidos no aplicativo nível de [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/).

## <a name="merged-resource-dictionaries"></a>Dicionários de recursos mesclados

Dicionários de recursos mesclados combinam uma ou mais [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) instâncias em outro. Isso é feito definindo o `ResourceDictionary.MergedDictionaries` propriedade para um ou mais dicionários de recursos que serão mesclados no nível do aplicativo, página ou controle `ResourceDictionary`.

> [!IMPORTANT]
> O [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) tipo também tem um [ `MergedWith` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ResourceDictionary.MergedWith/) propriedade que pode ser usada para mesclar uma única `ResourceDictionary` em outro, com o `ResourceDictionary` especificado como o valor da `MergedWith`propriedade sendo mesclada para a atual `ResourceDictionary` instância. Ao mesclar por meio de `MergedWith` propriedade, todos os recursos no atual `ResourceDictionary` que compartilham `x:Key` valores com recursos de atributo a `ResourceDictionary` a serem mescladas, serão substituída. No entanto, o `MergedWith` propriedade será substituída em uma versão futura do xamarin. Forms. Portanto, é recomendável para usar o `MergedDictionaries` propriedade mesclar `ResourceDictionary` instâncias.

O exemplo do código XAML seguinte mostra um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) chamado `MyResourceDictionary` que contém um único recurso:

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                    x:Class="ResourceDictionaryDemo.MyResourceDictionary">
    <DataTemplate x:Key="PersonDataTemplate">
        <ViewCell>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.5*" />
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.3*" />
                </Grid.ColumnDefinitions>
                <Label Text="{Binding Name}" TextColor="{StaticResource NormalTextColor}" FontAttributes="Bold" />
                <Label Grid.Column="1" Text="{Binding Age}" TextColor="{StaticResource NormalTextColor}" />
                <Label Grid.Column="2" Text="{Binding Location}" TextColor="{StaticResource NormalTextColor}" HorizontalTextAlignment="End" />
            </Grid>
        </ViewCell>
    </DataTemplate>
</ResourceDictionary>
```

`MyResourceDictionary` podem ser mesclados em qualquer aplicativo, a página ou o nível de controle `ResourceDictionary`. O exemplo de código XAML a seguir mostra que estão sendo mesclados em um nível de página `ResourceDictionary` usando o `MergedDictionaries` propriedade:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <local:MyResourceDictionary />
                <!-- Add more resource dictionaries here -->
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Quando for mesclado [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) recursos compartilhamento idênticos `x:Key` valores de atributo, xamarin. Forms usa a precedência de recursos a seguir:

1. Os recursos locais para o dicionário de recursos.
1. Os recursos contidos no dicionário de recurso que foi mesclado por meio de [ `MergedWith` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ResourceDictionary.MergedWith/) propriedade.
1. Os recursos contidos nos dicionários de recursos que foram mesclados por meio de `MergedDictionaries` coleção, na ordem em que eles estão listados no `MergedDictionaries` propriedade.

> [!NOTE]
> Pesquisar dicionários de recursos pode ser uma tarefa de computação intensa, se um aplicativo contém vários dicionários de recursos grandes. Portanto, certifique-se de que cada página em um aplicativo usa apenas os dicionários de recursos que são apropriados para a página, para evitar pesquisas desnecessárias.

## <a name="summary"></a>Resumo

Este artigo explicou como criar e consumir um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)e como mesclar dicionários de recursos. Um `ResourceDictionary` permite que os recursos a serem definidos em um único local e reutilizadas em um aplicativo xamarin. Forms.


## <a name="related-links"></a>Links relacionados

- [Dicionários de recurso (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/xaml/resourcedictionaries/)
- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
