---
title: Dicionários de recursos
description: Recursos XAML são objetos que podem ser compartilhados e reutilizados em todo um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/02/2018
ms.openlocfilehash: ee3e4c984072fc019fe3719aab650a44d3899911
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="resource-dictionaries"></a>Dicionários de recursos

_Recursos XAML são definições de objetos que podem ser compartilhados e reutilizados em todo um aplicativo xamarin. Forms._

Esses objetos de recursos são armazenados em um dicionário de recursos. Este artigo descreve como criar e consumir um dicionário de recursos e como mesclar dicionários de recursos.

## <a name="overview"></a>Visão geral

Um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) é um repositório de recursos que são usados por um aplicativo xamarin. Forms. Recursos comuns que são armazenados em um `ResourceDictionary` incluem [estilos](~/xamarin-forms/user-interface/styles/index.md), [modelos de controle](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md), [modelos de dados](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md), cores e conversores.

Em XAML, recursos que estão armazenados em uma `ResourceDictionary` podem ser recuperados e aplicados aos elementos usando o `StaticResource` extensão de marcação. No c#, recursos podem ser definidos em um `ResourceDictionary` recuperadas e aplicadas a elementos por meio de um indexador de cadeia de caracteres. No entanto, há pouca vantagem em usar um `ResourceDictionary` em c#, como objetos compartilhados podem simplesmente ser armazenados como campos ou propriedades e acessados diretamente sem precisar primeiro recuperá-los de um dicionário.

## <a name="creating-and-consuming-a-resourcedictionary"></a>Criação e consumo de um dicionário de recurso

Os recursos são definidos em um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) isto é, em seguida, definido como um dos seguintes `Resources` propriedades:

- O [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Resources/) propriedade de qualquer classe que deriva de [`Application`](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/)
- O [ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) propriedade de qualquer classe que deriva de ['VisualElement'](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/)

Um programa xamarin. Forms contém apenas uma classe que deriva de `Application` , mas geralmente faz uso de várias classes que derivam de `VisualElement`, incluindo páginas, layouts e controles. Qualquer um desses objetos pode ter seu `Resources` propriedade definida como um `ResourceDictionary`. Escolher onde colocar um determinado `ResourceDictionary` impactos onde os recursos podem ser usados:

- Recursos em um `ResourceDictionary` que está anexado a um modo de exibição, como `Button` ou `Label` só pode ser aplicado a esse objeto específico, para que isso não é muito útil.
- Recursos em um `ResourceDictionary` anexado a um layout, como `StackLayout` ou `Grid` podem ser aplicadas para o layout e todos os filhos do que o layout. 
- Recursos em um `ResourceDictionary` definido na página de nível pode ser aplicado para a página e todos os seus filhos.
- Recursos em um `ResourceDictionary` definidos no aplicativo de nível pode ser aplicado em todo o aplicativo.

O XAML a seguir mostra os recursos definidos no nível do aplicativo `ResourceDictionary` no **App** arquivo criado como parte do programa xamarin. Forms padrão:

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

Isso `ResourceDictionary` define três [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) recursos e um [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) recursos. Para obter mais informações sobre o `App` de classe, consulte [classe App](~/xamarin-forms/app-fundamentals/application-class.md).

A partir do xamarin. Forms 3.0, explícita `ResourceDictionary` marcas não são necessárias. O `ResourceDictionary` objeto é criado automaticamente e você pode inserir os recursos diretamente entre o `Resources` marcas de elemento de propriedade:

```xaml
<Application ...>
    <Application.Resources>
        <Color x:Key="PageBackgroundColor">Yellow</Color>
        <Color x:Key="HeadingTextColor">Black</Color>
        <Color x:Key="NormalTextColor">Blue</Color>
        <Style x:Key="LabelPageHeadingStyle" TargetType="Label">
            <Setter Property="FontAttributes" Value="Bold" />
            <Setter Property="HorizontalOptions" Value="Center" />
            <Setter Property="TextColor" Value="{StaticResource HeadingTextColor}" />
        </Style>
    </Application.Resources>
</Application>
```

Cada recurso tem uma chave que é especificada usando o `x:Key` atributo, que se torna a chave de dicionário no `ResourceDictionary`. A chave é usada para recuperar um recurso do `ResourceDictionary` pelo [ `StaticResource` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticResourceExtension/) extensão de marcação, como demonstrado no seguinte exemplo de código XAML que mostra os recursos adicionais definidos dentro de `StackLayout`:

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

A primeira [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instância recupera e consome o `LabelPageHeadingStyle` recursos definidos no nível do aplicativo `ResourceDictionary`, com a segunda `Label` instância recuperando e consumindo o `LabelNormalStyle`definido no nível de controle de recurso `ResourceDictionary`. Da mesma forma, o [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) instância recupera e consome o `NormalTextColor` recursos definidos no nível do aplicativo `ResourceDictionary`e o `MediumBoldText` definido no nível de controle de recurso `ResourceDictionary`. Isso resulta na exibição mostrada nas capturas de tela seguir:

[![](resource-dictionaries-images/screenshots-sml.png "Consumindo recursos ResourceDictionary")](resource-dictionaries-images/screenshots.png#lightbox "consumindo recursos ResourceDictionary")

> [!NOTE]
> Recursos que são específicos para uma única página não podem ser incluídos em um aplicativo nível dicionário de recursos, como tal, recursos, em seguida, serão analisados na inicialização do aplicativo em vez de quando necessário por uma página. Para obter mais informações, consulte [reduzir o tamanho de dicionário de recurso de aplicativo](~/xamarin-forms/deploy-test/performance.md).

## <a name="overriding-resources"></a>Substituição de recursos

Quando `ResourceDictionary` compartilham recursos `x:Key` valores de atributo, recursos definidos inferior na hierarquia do modo de exibição têm precedência sobre aquelas definidas superior para cima. Por exemplo, se você definir o `PageBackgroundColor` recurso `Blue` no aplicativo de nível será substituído por um nível de página `PageBackgroundColor` do conjunto de recursos `Yellow`. Da mesma forma, um nível de página `PageBackgroundColor` recurso será substituído por um nível de controle `PageBackgroundColor` recursos. Essa precedência é demonstrada pelo exemplo de código XAML a seguir:

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

No entanto, observe que a barra de plano de fundo do [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) ainda amarelo, porque o [ `BarBackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.NavigationPage.BarBackgroundColor/) propriedade é definida como o valor do `PageBackgroundColor` recursos definidos no aplicativo nível de `ResourceDictionary`.

Esta é outra maneira de pensar sobre `ResourceDictionary` precedência: analisador de XAML o quando encontra um `StaticResource`, ele procura por uma chave correspondente por viajando backup por meio da árvore visual, usando a primeira correspondência que ele encontra. Se a pesquisa termina na página e a chave ainda não foi encontrada, o analisador XAML procura o `ResourceDictionary` anexado para o `App` objeto. Se a chave ainda não for encontrada, uma exceção será gerada.

## <a name="stand-alone-resource-dictionaries"></a>Dicionários de recurso autônomo

Uma classe derivada de `ResourceDictionary` também podem estar em um arquivo autônomo separado. (Mais precisamente, uma classe derivada de `ResourceDictionary` geralmente requer um _par_ de arquivos porque os recursos são definidos em um arquivo XAML, mas um arquivo de code-behind com um `InitializeComponent` chamada também é necessária.) O arquivo resultante pode ser compartilhado entre aplicativos.

Para criar esse arquivo, adicione um novo **exibição de conteúdo** ou **página de conteúdo** item ao projeto (mas não um **exibição de conteúdo** ou **página de conteúdo** com somente um arquivo c#). O arquivo XAML tanto no arquivo c#, altere o nome da classe base de `ContentView` ou `ContentPage` para `ResourceDictionary`. O arquivo XAML, o nome da classe base é o elemento de nível superior.

A exemplo XAML a seguir mostra um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) chamado `MyResourceDictionary`:

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

Isso `ResourceDictionary` contém um único recurso, que é um objeto do tipo `DataTemplate`.

Você pode instanciar `MyResourceDictionary` colocando-o entre um par de `Resources` elemento de propriedade marcas, por exemplo, em um `ContentPage`:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <local:MyResourceDictionary />
    </ContentPage.Resources>
    ...
</ContentPage>  
```

Uma instância de `MyResourceDictionary` é definido como o `Resources` propriedade o `ContentPage` objeto.

No entanto, essa abordagem tem algumas limitações: O `Resources` propriedade o `ContentPage` faz referência a apenas este um `ResourceDictionary`. Na maioria dos casos, você deseja que a opção de incluir outros `ResourceDictionary` instâncias e talvez outros recursos também.

Esta tarefa exige dicionários de recursos mesclados.

## <a name="merged-resource-dictionaries"></a>Dicionários de recursos mesclados

Dicionários de recursos mesclados combinam uma ou mais `ResourceDictionary` instâncias em outro `ResourceDictionary`. Você pode fazer isso em um arquivo XAML, definindo o [ `MergedDictionaries` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ResourceDictionary.MergedDictionaries/) propriedade para um ou mais dicionários de recursos que serão mesclados no nível do aplicativo, página ou controle `ResourceDictionary`.

> [!IMPORTANT]
> `ResourceDictionary` também define uma [ `MergedWith` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ResourceDictionary.MergedWith/) propriedade. Não use essa propriedade; ele foi substituído a partir do xamarin. Forms 3.0.

E a instância do `MyResourceDictionary` podem ser mesclados em qualquer aplicativo, a página ou o nível de controle `ResourceDictionary`. O exemplo de código XAML a seguir mostra que estão sendo mesclados em um nível de página `ResourceDictionary` usando o `MergedDictionaries` propriedade:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <local:MyResourceDictionary />
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Essa marcação mostra apenas uma instância do `MyResourceDictionary` que está sendo adicionado para o `ResourceDictionary` , mas você também pode referenciar outros `ResourceDictionary` instâncias no `MergedDictionary` marcas de elemento de propriedade e outros recursos fora essas marcas:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ResourceDictionary>

            <!-- Add more resources here -->

            <ResourceDictionary.MergedDictionaries>

                <!-- Add more resource dictionaries here -->

                <local:MyResourceDictionary />

                <!-- Add more resource dictionaries here -->

            </ResourceDictionary.MergedDictionaries>

            <!-- Add more resources here -->

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Pode haver apenas um `MergedDictionaries` seção um `ResourceDictionary`, mas você pode colocar quantos `ResourceDictionary` instâncias lá conforme desejado.

Quando for mesclado [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) recursos compartilhamento idênticos `x:Key` valores de atributo, xamarin. Forms usa a precedência de recursos a seguir:

1. Os recursos locais para o dicionário de recursos.
1. Os recursos contidos no dicionário de recurso que foi mesclado via preterido [ `MergedWith` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ResourceDictionary.MergedWith/) propriedade.
1. Os recursos contidos nos dicionários de recursos que foram mesclados por meio de `MergedDictionaries` coleção, na ordem em que eles estão listados no `MergedDictionaries` propriedade.

> [!NOTE]
> Pesquisar dicionários de recursos pode ser uma tarefa de computação intensa, se um aplicativo contém vários dicionários de recursos grandes. Portanto, para evitar pesquisas desnecessárias, você deve garantir que cada página em um aplicativo usa apenas os dicionários de recursos que são apropriados para a página.

## <a name="merging-dictionaries-in-xamarinforms-30"></a>Mesclando dicionários no xamarin. Forms 3.0

Começando com o xamarin. Forms 3.0, o processo de mesclagem `ResourceDictionaries` se tornou um pouco mais fácil e mais flexível. O `MergedDictionaries` marcas de elemento de propriedade não são mais necessárias. Em vez disso, você adicionar ao dicionário de recursos que outro `ResourceDictionary` marca com o novo [ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ResourceDictionary.Source/) propriedade definida como o nome do arquivo XAML com os recursos:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ResourceDictionary>

            <!-- Add more resources here -->

            <ResourceDictionary Source="MyResourceDictionary.xaml" />

            <!-- Add more resources here -->

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Porque o xamarin. Forms 3.0 cria automaticamente o `ResourceDictionary`, esses dois externos `ResourceDictionary` marcas não são mais necessárias:

```xaml
<ContentPage ...>
    <ContentPage.Resources>

        <!-- Add more resources here -->

        <ResourceDictionary Source="MyResourceDictionary.xaml" />

        <!-- Add more resources here -->

    </ContentPage.Resources>
    ...
</ContentPage>
```

Essa nova sintaxe _não_ instanciar o `MyResourceDictionary` classe. Em vez disso, ele faz referência ao arquivo XAML. Por que razão, o arquivo code-behind (**MyResourceDictionary.xaml.cs**) não é mais necessário. Você também pode remover o `x:Class` atributo na marca raiz do **MyResourceDictionary.xaml** arquivo. 

## <a name="summary"></a>Resumo

Este artigo explicou como criar e consumir um [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)e como mesclar dicionários de recursos. Um `ResourceDictionary` permite que os recursos a serem definidos em um único local e reutilizadas em um aplicativo xamarin. Forms.

## <a name="related-links"></a>Links relacionados

- [Dicionários de recurso (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/xaml/resourcedictionaries/)
- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [Dicionário de recurso](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
