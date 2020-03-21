---
title: Dicionários de recurso
description: Recursos XAML são objetos que podem ser compartilhados e reutilizados em todo um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2020
ms.custom: video
ms.openlocfilehash: 496251ec9596b9ef76fb34149acca184b5934c37
ms.sourcegitcommit: 6c60914b380ff679bbffd7790edd4d5e18005d0a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2020
ms.locfileid: "80070389"
---
# <a name="resource-dictionaries"></a>Dicionários de recurso

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)

_Os recursos XAML são definições de objetos que podem ser compartilhados e reutilizados em um aplicativo Xamarin. Forms. Esses objetos de recurso são armazenados em um dicionário de recursos._

Uma [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) é um repositório de recursos que são usados por um aplicativo Xamarin. Forms. Os recursos típicos que são armazenados em um `ResourceDictionary` incluem [estilos](~/xamarin-forms/user-interface/styles/index.md), [modelos de controle](~/xamarin-forms/app-fundamentals/templates/control-template.md), [modelos de dados](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md), cores e conversores.

Em XAML, os recursos armazenados em um `ResourceDictionary` podem ser recuperados e aplicados aos elementos usando a extensão de marcação `StaticResource`. No C#, os recursos também podem ser definidos em um `ResourceDictionary` e, em seguida, recuperados e aplicados aos elementos usando um indexador baseado em cadeia de caracteres. No entanto, há pouca vantagem em usar um `ResourceDictionary` C#no, pois objetos compartilhados podem simplesmente ser armazenados como campos ou propriedades e acessados diretamente sem precisar primeiro recuperá-los de um dicionário.

## <a name="create-and-consume-a-resourcedictionary"></a>Criar e consumir um ResourceDictionary

Os recursos são definidos em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) que é então definido como uma das seguintes propriedades de `Resources`:

- A propriedade [`Resources`](xref:Xamarin.Forms.Application.Resources) de qualquer classe derivada de [`Application`](xref:Xamarin.Forms.Application)
- A propriedade [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) de qualquer classe derivada de [`VisualElement`](xref:Xamarin.Forms.Application)

Um programa Xamarin. Forms contém apenas uma classe derivada de `Application`, mas geralmente usa muitas classes que derivam de `VisualElement`, incluindo páginas, layouts e controles. Qualquer um desses objetos pode ter sua propriedade `Resources` definida como um `ResourceDictionary`. Escolher onde colocar um determinado `ResourceDictionary` afeta o local em que os recursos podem ser usados:

- Recursos em um `ResourceDictionary` que é anexado a uma exibição como `Button` ou `Label` só podem ser aplicados a esse objeto específico, portanto, isso não é muito útil.
- Os recursos em um `ResourceDictionary` anexado a um layout como `StackLayout` ou `Grid` podem ser aplicados ao layout e a todos os filhos desse layout.
- Os recursos em um `ResourceDictionary` definidos no nível de página podem ser aplicados à página e a todos os seus filhos.
- Os recursos em um `ResourceDictionary` definidos no nível do aplicativo podem ser aplicados em todo o aplicativo.

O XAML a seguir mostra os recursos definidos em um nível de aplicativo `ResourceDictionary` no arquivo **app. XAML** criado como parte do programa Xamarin. Forms padrão:

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

Este `ResourceDictionary` define três recursos de [`Color`](xref:Xamarin.Forms.Color) e um recurso de [`Style`](xref:Xamarin.Forms.Style) . Para obter mais informações sobre a classe `App`, consulte [App class](~/xamarin-forms/app-fundamentals/application-class.md).

A partir do Xamarin. Forms 3,0, as marcas de `ResourceDictionary` explícitas não são necessárias. O objeto `ResourceDictionary` é criado automaticamente e você pode inserir os recursos diretamente entre as marcas de elemento de propriedade `Resources`:

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

Cada recurso tem uma chave que é especificada usando o atributo `x:Key`, que se torna a chave de dicionário na `ResourceDictionary`. A chave é usada para recuperar um recurso do `ResourceDictionary` pela extensão de marcação [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) , conforme demonstrado no exemplo de código XAML a seguir que mostra recursos adicionais definidos no `StackLayout`:

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

A primeira instância de [`Label`](xref:Xamarin.Forms.Label) recupera e consome o recurso de `LabelPageHeadingStyle` definido no nível de aplicativo `ResourceDictionary`, com a segunda instância de `Label` recuperando e consumindo o recurso de `LabelNormalStyle` definido no `ResourceDictionary`de nível de controle. Da mesma forma, a instância de [`Button`](xref:Xamarin.Forms.Button) recupera e consome o recurso de `NormalTextColor` definido no nível de aplicativo `ResourceDictionary`e o recurso de `MediumBoldText` definido no `ResourceDictionary`de nível de controle. Isso resulta na aparência mostrada nas capturas de tela seguir:

[![consumir recursos de ResourceDictionary](resource-dictionaries-images/screenshots-sml.png)](resource-dictionaries-images/screenshots.png#lightbox)

> [!NOTE]
> Recursos que são específicos a uma única página não devem ser incluídos em um aplicativo nível dicionário de recursos, como tal, recursos, em seguida, serão analisados na inicialização do aplicativo em vez de quando exigido por uma página. Para obter mais informações, consulte [reduzir o tamanho do dicionário de recursos do aplicativo](~/xamarin-forms/deploy-test/performance.md).

## <a name="override-resources"></a>Substituir recursos

Quando `ResourceDictionary` recursos compartilham `x:Key` valores de atributo, os recursos definidos mais abaixo na hierarquia de exibição terão precedência sobre aqueles definidos acima. Por exemplo, definir o recurso de `PageBackgroundColor` como `Blue` no nível do aplicativo será substituído por um nível de página `PageBackgroundColor` conjunto de recursos como `Yellow`. Da mesma forma, um recurso de `PageBackgroundColor` de nível de página será substituído por um nível de controle `PageBackgroundColor` recurso. Essa precedência é demonstrada pelo exemplo de código XAML a seguir:

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

As instâncias originais `PageBackgroundColor` e `NormalTextColor`, definidas no nível do aplicativo, são substituídas pelas instâncias `PageBackgroundColor` e `NormalTextColor` definidas no nível da página. Portanto, a cor de plano de fundo da página torna-se azul e o texto na página se torna amarelo, conforme demonstrado nas capturas de tela seguir:

[![substituindo recursos de ResourceDictionary](resource-dictionaries-images/overridding-screenshots-sml.png)](resource-dictionaries-images/overridding-screenshots.png#lightbox)

No entanto, observe que a barra de plano de fundo da [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) ainda é amarela, porque a propriedade [`BarBackgroundColor`](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor) é definida como o valor do recurso `PageBackgroundColor` definido na `ResourceDictionary`de nível do aplicativo.

Aqui está outra maneira de pensar sobre a precedência de `ResourceDictionary`: quando o analisador XAML encontra uma `StaticResource`, ele procura uma chave correspondente viajando pela árvore visual, usando a primeira correspondência encontrada. Se essa pesquisa terminar na página e a chave ainda não tiver sido encontrada, o analisador XAML pesquisará o `ResourceDictionary` anexado ao objeto `App`. Se a chave ainda não for encontrada, uma exceção é gerada.

## <a name="stand-alone-resource-dictionaries"></a>Dicionários de recursos autônomos

Uma classe derivada de `ResourceDictionary` também pode estar em um arquivo autônomo separado. O arquivo resultante, em seguida, pode ser compartilhado entre aplicativos.

Para criar esse arquivo, adicione um novo **modo de exibição de conteúdo** ou item de página de **conteúdo** ao projeto (mas não a uma **exibição de conteúdo** ou a uma página de **conteúdo** com apenas um C# arquivo). No arquivo XAML e C# no arquivo, altere o nome da classe base de `ContentView` ou `ContentPage` para `ResourceDictionary`. No arquivo XAML, o nome da classe base é o elemento de nível superior.

O exemplo de XAML a seguir mostra um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) chamado `MyResourceDictionary`:

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

Esse `ResourceDictionary` contém um único recurso, que é um objeto do tipo `DataTemplate`.

Você pode instanciar `MyResourceDictionary` colocando-o entre um par de marcas de elemento de propriedade `Resources`, por exemplo, em um `ContentPage`:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <local:MyResourceDictionary />
    </ContentPage.Resources>
    ...
</ContentPage>
```

Uma instância de `MyResourceDictionary` é definida como a propriedade `Resources` do objeto `ContentPage`.

No entanto, essa abordagem tem algumas limitações: a propriedade `Resources` da `ContentPage` faz referência apenas a essa `ResourceDictionary`. Na maioria dos casos, você quer a opção de incluir outras instâncias de `ResourceDictionary` e talvez outros recursos também.

Esta tarefa exige dicionários de recursos mesclados.

## <a name="merged-resource-dictionaries"></a>Dicionários de Recursos mesclados

Os dicionários de recurso mesclados combinam um ou mais objetos [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) em outro `ResourceDictionary`.

### <a name="merge-local-resource-dictionaries"></a>Mesclar dicionários de recursos locais

Um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) local pode ser mesclado em outro `ResourceDictionary` definindo a propriedade [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) como o nome do arquivo XAML com os recursos:

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

Essa sintaxe não instancia a classe `MyResourceDictionary`. Em vez disso, ele faz referência ao arquivo XAML. Por esse motivo, ao definir a propriedade [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) , um arquivo code-behind não é necessário e o atributo `x:Class` pode ser removido da marca raiz do arquivo **myresourcedictionary. XAML** . Além disso, ao mesclar os dicionários de recursos usando essa abordagem, o Xamarin. Forms irá instanciar automaticamente o [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), portanto, as marcas de `ResourceDictionary` externas não são necessárias.

> [!IMPORTANT]
> A propriedade [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) só pode ser definida a partir de XAML.

### <a name="merge-resource-dictionaries-from-other-assemblies"></a>Mesclar dicionários de recurso de outros assemblies

Um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) também pode ser mesclado em outro `ResourceDictionary` adicionando-o à propriedade [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) do `ResourceDictionary`. Essa técnica permite que os dicionários de recursos sejam mesclados, independentemente do assembly no qual residem.

> [!IMPORTANT]
> A classe [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) também define uma propriedade [`MergedWith`](xref:Xamarin.Forms.ResourceDictionary.MergedWith) . No entanto, essa propriedade foi preterida e não deve mais ser usada.

O exemplo de código a seguir mostra `MyResourceDictionary` sendo adicionado à coleção [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) de um nível de página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary):

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

Este exemplo mostra uma instância de `MyResourceDictionary`, que reside no mesmo assembly, sendo adicionada ao [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). Além disso, você também pode adicionar dicionários de recursos de outros assemblies, outros `ResourceDictionary` objetos dentro das marcas de elemento de propriedade de [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) e outros recursos fora dessas marcas:

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

Ao colocar um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) em um assembly externo, certifique-se de que sua ação de Build esteja definida como **EmbeddedResource**. Além disso, verifique se ele tem um arquivo code-behind.

> [!IMPORTANT]
> Pode haver apenas uma `MergedDictionaries` marca de elemento de propriedade em uma [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary), mas você pode colocar quantos objetos `ResourceDictionary` ali desejar.

Quando mescladas [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) recursos compartilham valores de atributo `x:Key` idênticos, o Xamarin. Forms usa a seguinte precedência de recursos:

1. Os recursos locais para o dicionário de recursos.
1. Os recursos contidos nos dicionários de recursos que foram mesclados por meio da coleção de `MergedDictionaries`, na ordem inversa, são listados na propriedade `MergedDictionaries`.

> [!NOTE]
> Dicionários de recursos de pesquisa pode ser uma tarefa de computação intensa, se um aplicativo contém vários dicionários de recursos grandes. Portanto, para evitar pesquisas desnecessárias, você deve garantir que cada página em um aplicativo só usa dicionários de recursos que são apropriados para a página.

## <a name="related-links"></a>Links relacionados

- [Dicionários de recursos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)
- [Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
