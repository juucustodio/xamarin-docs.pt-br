---
title: Dicionários de recursos
description: Recursos XAML são objetos que podem ser compartilhados e reutilizados em todo um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/15/2019
ms.custom: video
ms.openlocfilehash: a9b9b2d12193161e0cb4514600381c3a7a38495a
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69529325"
---
# <a name="resource-dictionaries"></a>Dicionários de recursos

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)

_Os recursos XAML são definições de objetos que podem ser compartilhados e reutilizados em um aplicativo Xamarin. Forms. Esses objetos de recurso são armazenados em um dicionário de recursos._

Um [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) é um repositório para os recursos que são usados por um aplicativo xamarin. Forms. Recursos comuns que são armazenados em uma `ResourceDictionary` incluem [estilos](~/xamarin-forms/user-interface/styles/index.md), [modelos de controle](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md), [modelos de dados](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md), cores e conversores.

No XAML, recursos que são armazenados em uma `ResourceDictionary` podem ser recuperadas e aplicadas aos elementos usando o `StaticResource` extensão de marcação. No c#, os recursos também podem ser definidos em um `ResourceDictionary` e, em seguida, recuperados e aplicados aos elementos usando um indexador de cadeia de caracteres. No entanto, há pouca vantagem em usar um `ResourceDictionary` no c#, como objetos compartilhados podem simplesmente ser armazenados como campos ou propriedades e acessados diretamente sem ter que primeiro recuperá-los de um dicionário.

## <a name="create-and-consume-a-resourcedictionary"></a>Criar e consumir um ResourceDictionary

Recursos são definidos em uma [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) isto é, em seguida, definido como um dos seguintes `Resources` propriedades:

- O [ `Resources` ](xref:Xamarin.Forms.Application.Resources) propriedade de qualquer classe que deriva de [`Application`](xref:Xamarin.Forms.Application)
- O [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) propriedade de qualquer classe que deriva de [`VisualElement`](xref:Xamarin.Forms.Application)

Um programa do xamarin. Forms contém apenas uma classe que deriva de `Application` , mas geralmente faz uso de muitas classes que derivam de `VisualElement`, incluindo páginas, layouts e controles. Qualquer um desses objetos podem ter seus `Resources` propriedade definida como um `ResourceDictionary`. Escolher onde colocar um determinado `ResourceDictionary` impactos onde os recursos podem ser usados:

- Recursos em um `ResourceDictionary` que está anexado a um modo de exibição, como `Button` ou `Label` só pode ser aplicado a esse objeto específico, portanto, isso não é muito útil.
- Recursos em um `ResourceDictionary` anexados a um layout, como `StackLayout` ou `Grid` podem ser aplicadas a todos os filhos do que o layout e de layout.
- Recursos em um `ResourceDictionary` definidos na página do nível pode ser aplicado à página e para todos os seus filhos.
- Recursos em um `ResourceDictionary` definidos no aplicativo de nível pode ser aplicado em todo o aplicativo.

O XAML a seguir mostra os recursos definidos no nível do aplicativo `ResourceDictionary` no **App. XAML** arquivo criado como parte do programa do xamarin. Forms padrão:

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

Isso `ResourceDictionary` define três [ `Color` ](xref:Xamarin.Forms.Color) recursos e uma [ `Style` ](xref:Xamarin.Forms.Style) recursos. Para obter mais informações sobre o `App` classe, consulte [classe App](~/xamarin-forms/app-fundamentals/application-class.md).

A partir do xamarin. Forms 3.0, explícito `ResourceDictionary` marcas não são necessárias. O `ResourceDictionary` objeto é criado automaticamente e você pode inserir os recursos diretamente entre o `Resources` marcas de elemento de propriedade:

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

Cada recurso tem uma chave que é especificada usando o `x:Key` atributo, que se torna a chave de dicionário no `ResourceDictionary`. A chave é usada para recuperar um recurso do `ResourceDictionary` pelo [ `StaticResource` ](xref:Xamarin.Forms.Xaml.StaticResourceExtension) extensão de marcação, conforme demonstrado no seguinte exemplo de código XAML que mostra os recursos adicionais definidos dentro de `StackLayout`:

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

A primeira [ `Label` ](xref:Xamarin.Forms.Label) instância recupera e consome os `LabelPageHeadingStyle` recursos definidos no nível do aplicativo `ResourceDictionary`, com a segunda `Label` instância recuperando e consumindo o `LabelNormalStyle`definido no nível de controle de recurso `ResourceDictionary`. Da mesma forma, o [ `Button` ](xref:Xamarin.Forms.Button) instância recupera e consome os `NormalTextColor` recursos definidos no nível do aplicativo `ResourceDictionary`e o `MediumBoldText` definido no nível de controle de recurso `ResourceDictionary`. Isso resulta na exibição mostrada nas capturas de tela seguir:

[![](resource-dictionaries-images/screenshots-sml.png "Consumindo recursos ResourceDictionary")](resource-dictionaries-images/screenshots.png#lightbox "consumindo recursos ResourceDictionary")

> [!NOTE]
> Recursos que são específicos a uma única página não devem ser incluídos em um aplicativo nível dicionário de recursos, como tal, recursos, em seguida, serão analisados na inicialização do aplicativo em vez de quando exigido por uma página. Para obter mais informações, consulte [reduzir o tamanho de dicionário de recursos do aplicativo](~/xamarin-forms/deploy-test/performance.md).

## <a name="override-resources"></a>Substituir recursos

Quando `ResourceDictionary` compartilham recursos `x:Key` valores de atributo definidos inferior na hierarquia de exibição de recursos terá precedência sobre aquelas definidas superior para cima. Por exemplo, definindo a `PageBackgroundColor` recursos para `Blue` no aplicativo de nível será substituído por um nível de página `PageBackgroundColor` conjunto de recursos `Yellow`. Da mesma forma, um nível de página `PageBackgroundColor` recurso será substituído por um nível de controle `PageBackgroundColor` recursos. Essa precedência é demonstrada pelo exemplo de código XAML a seguir:

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

O original `PageBackgroundColor` e `NormalTextColor` instâncias, definidas no nível do aplicativo, são substituídas pela `PageBackgroundColor` e `NormalTextColor` instâncias definidas no nível da página. Portanto, a cor de plano de fundo da página torna-se azul e o texto na página se torna amarelo, conforme demonstrado nas capturas de tela seguir:

[![](resource-dictionaries-images/overridding-screenshots-sml.png "Substituindo recursos ResourceDictionary")](resource-dictionaries-images/overridding-screenshots.png#lightbox "substituindo ResourceDictionary recursos")

No entanto, observe que a barra de plano de fundo do [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) ainda está amarela, porque o [ `BarBackgroundColor` ](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) propriedade é definida como o valor da `PageBackgroundColor` recursos definidos no aplicativo nível de `ResourceDictionary`.

Esta é outra maneira de pensar `ResourceDictionary` na precedência: Quando o analisador XAML encontra um `StaticResource`, ele procura uma chave correspondente viajando pela árvore visual, usando a primeira correspondência encontrada. Se essa pesquisa termina na página e a chave ainda não foi encontrada, o analisador XAML pesquisará o `ResourceDictionary` anexado ao `App` objeto. Se a chave ainda não for encontrada, uma exceção é gerada.

## <a name="stand-alone-resource-dictionaries"></a>Dicionários de recursos autônomos

Uma classe derivada de `ResourceDictionary` também pode estar em um arquivo autônomo separado. (Mais precisamente, uma classe derivada de `ResourceDictionary` geralmente requer um _par_ de arquivos porque os recursos são definidos em um arquivo XAML, mas um arquivo code-behind com um `InitializeComponent` chamada também é necessária.) O arquivo resultante, em seguida, pode ser compartilhado entre aplicativos.

Para criar esse arquivo, adicione uma nova **exibição de conteúdo** ou **página de conteúdo** item ao projeto (mas não um **exibição de conteúdo** ou **página de conteúdo** com apenas um arquivo c#). No arquivo XAML e para arquivos c#, altere o nome da classe base da `ContentView` ou `ContentPage` para `ResourceDictionary`. No arquivo XAML, o nome da classe base é o elemento de nível superior.

A exemplo XAML a seguir mostra uma [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) denominada `MyResourceDictionary`:

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

Você pode instanciar `MyResourceDictionary` colocando-o entre um par de `Resources` elemento de propriedade de marcas, por exemplo, em um `ContentPage`:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <local:MyResourceDictionary />
    </ContentPage.Resources>
    ...
</ContentPage>
```

Uma instância do `MyResourceDictionary` é definido como o `Resources` propriedade do `ContentPage` objeto.

No entanto, essa abordagem tem algumas limitações: A `Resources` propriedade `ResourceDictionary`de faz referência apenas a essa. `ContentPage` Na maioria dos casos, você deseja que a opção de incluir outros `ResourceDictionary` instâncias e talvez outros recursos também.

Esta tarefa exige dicionários de recursos mesclados.

## <a name="merged-resource-dictionaries"></a>Dicionários de Recursos mesclados

Os dicionários de recurso mesclados combinam um ou `ResourceDictionary`mais [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) objetos em outro.

### <a name="merge-local-resource-dictionaries"></a>Mesclar dicionários de recursos locais

Um local [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) pode ser mesclado em outro `ResourceDictionary` definindo a [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) Propriedade como o nome do arquivo XAML com os recursos:

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

Essa sintaxe não instancia a `MyResourceDictionary` classe. Em vez disso, ele faz referência ao arquivo XAML. Por esse motivo, ao definir a [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) Propriedade, o arquivo code-behind (**MyResourceDictionary.XAML.cs**) não é necessário e o `x:Class` atributo pode ser removido da marca raiz do arquivo myresourcedictionary **. XAML** . Além disso, ao mesclar os dicionários de recursos usando essa abordagem, o Xamarin. [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)Forms criará automaticamente `ResourceDictionary` a instância do, portanto, as marcas externas não serão necessárias.

> [!IMPORTANT]
> A [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) propriedade só pode ser definida a partir de XAML.

### <a name="merge-resource-dictionaries-from-other-assemblies"></a>Mesclar dicionários de recurso de outros assemblies

Um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) também pode ser mesclado em outro `ResourceDictionary` adicionando-o [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) `ResourceDictionary`à propriedade do. Essa técnica permite que os dicionários de recursos sejam mesclados, independentemente do assembly no qual residem.

> [!IMPORTANT]
> A [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) classe também define uma [`MergedWith`](xref:Xamarin.Forms.ResourceDictionary.MergedWith) propriedade. No entanto, essa propriedade foi preterida e não deve mais ser usada.

O exemplo de código a `MyResourceDictionary` seguir mostra a adição [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) à coleção de um nível [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de página:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:ResourceDictionaryDemo">
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

Este exemplo mostra uma instância do `MyResourceDictionary`, que reside no mesmo assembly, sendo adicionada [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)ao. Além disso, você também pode adicionar dicionários de recursos de outros assemblies `ResourceDictionary` , outros objetos [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) dentro das marcas de elemento de propriedade e outros recursos fora dessas marcas:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:ResourceDictionaryDemo"
             xmlns:theme="clr-namespace:MyThemes;assembly=MyThemes">
    <ContentPage.Resources>
        <ResourceDictionary>
            <!-- Add more resources here -->
            <ResourceDictionary.MergedDictionaries>
                <!-- Add more resource dictionaries here -->
                <local:MyResourceDictionary />
                <theme:LightTheme />
                <!-- Add more resource dictionaries here -->
            </ResourceDictionary.MergedDictionaries>
            <!-- Add more resources here -->
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

> [!IMPORTANT]
> Pode haver apenas uma `MergedDictionaries` marca de elemento de propriedade em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), mas você pode colocar quantos `ResourceDictionary` objetos desejar.

Quando mescladas [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) recursos compartilham idênticos `x:Key` valores de atributo, xamarin. Forms usa a precedência de recursos a seguir:

1. Os recursos locais para o dicionário de recursos.
1. Os recursos contidos nos dicionários de recursos que foram mesclados por meio de `MergedDictionaries` coleção, na ordem inversa, eles são listados no `MergedDictionaries` propriedade.

> [!NOTE]
> Dicionários de recursos de pesquisa pode ser uma tarefa de computação intensa, se um aplicativo contém vários dicionários de recursos grandes. Portanto, para evitar pesquisas desnecessárias, você deve garantir que cada página em um aplicativo só usa dicionários de recursos que são apropriados para a página.

## <a name="related-links"></a>Links relacionados

- [Dicionários de recursos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)
- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [Dicionário de recurso](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
