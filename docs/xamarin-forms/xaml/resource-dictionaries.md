---
title: Xamarin.Forms dicionários de recursos
description: Xamarin.Forms Os recursos XAML são objetos que podem ser compartilhados e reutilizados em um aplicativo Xamarin.Forms.
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2020
ms.custom: video
ms.openlocfilehash: 8dd3c7f36ddd436a812927816a1326dbb7c48341
ms.sourcegitcommit: ee9e48e2ec643915f42a6c1641077970ae20cb17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80523207"
---
# <a name="xamarinforms-resource-dictionaries"></a>Xamarin.Forms dicionários de recursos

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)

A [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) é um repositório para recursos que são usados por um aplicativo Xamarin.Forms. Recursos típicos armazenados `ResourceDictionary` em [estilos, modelos](~/xamarin-forms/user-interface/styles/index.md) [de controle, modelos](~/xamarin-forms/app-fundamentals/templates/control-template.md) [de dados,](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)cores e conversores.

Em XAML, os recursos [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) armazenados em um podem ser referenciados e aplicados a elementos usando a `StaticResource` extensão ou `DynamicResource` marcação. Em C#, os recursos também `ResourceDictionary` podem ser definidos em um e, em seguida, referenciados e aplicados a elementos usando um indexador baseado em strings. No entanto, há pouca `ResourceDictionary` vantagem em usar um em C#, pois objetos compartilhados podem ser armazenados como campos ou propriedades, e acessados diretamente sem ter que recuperá-los primeiro de um dicionário.

## <a name="create-resources-in-xaml"></a>Criar recursos em XAML

Cada [`VisualElement`](xref:Xamarin.Forms.VisualElement) objeto derivado [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) tem uma propriedade, que é uma [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) que pode conter recursos. Da mesma [`Application`](xref:Xamarin.Forms.Application) forma, um [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) objeto derivado tem [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) uma propriedade, que é uma que pode conter recursos.

Um aplicativo Xamarin.Forms contém apenas [`Application`](xref:Xamarin.Forms.Application)uma classe derivada, mas muitas [`VisualElement`](xref:Xamarin.Forms.VisualElement)vezes faz uso de muitas classes que derivam , incluindo páginas, layouts e controles. Qualquer um desses objetos pode ter sua `Resources` propriedade definida como um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) recurso contendo. Escolher onde colocar `ResourceDictionary` um impacto específico onde os recursos podem ser usados:

- Recursos em `ResourceDictionary` um que é anexado [`Button`](xref:Xamarin.Forms.Button) [`Label`](xref:Xamarin.Forms.Label) a uma visão como ou só pode ser aplicado a esse objeto em particular.
- Recursos em `ResourceDictionary` um layout anexado [`StackLayout`](xref:Xamarin.Forms.StackLayout) [`Grid`](xref:Xamarin.Forms.Grid) a um layout como ou podem ser aplicados ao layout e a todas as crianças desse layout.
- Os recursos `ResourceDictionary` em um nível definido na página podem ser aplicados à página e a todos os seus filhos.
- Os recursos `ResourceDictionary` em um nível de aplicação definido podem ser aplicados ao longo da aplicação.

Com exceção dos estilos implícitos, cada recurso no dicionário de `x:Key` recursos deve ter uma chave de string única definida com o atributo.

O XAML a seguir mostra `ResourceDictionary` recursos definidos em um nível de aplicativo no arquivo **App.xaml:**

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ResourceDictionaryDemo.App">
    <Application.Resources>

        <Thickness x:Key="PageMargin">20</Thickness>

        <!-- Colors -->
        <Color x:Key="AppBackgroundColor">AliceBlue</Color>
        <Color x:Key="NavigationBarColor">#1976D2</Color>
        <Color x:Key="NavigationBarTextColor">White</Color>
        <Color x:Key="NormalTextColor">Black</Color>

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

Neste exemplo, o dicionário de [`Thickness`](xref:Xamarin.Forms.Thickness) recursos [`Color`](xref:Xamarin.Forms.Color) define um recurso, vários recursos e dois recursos implícitos. [`Style`](xref:Xamarin.Forms.Style) Para obter mais `App` informações sobre a classe, consulte [Xamarin.Forms App Class](~/xamarin-forms/app-fundamentals/application-class.md).

> [!NOTE]
> Também é válido colocar todos os `ResourceDictionary` recursos entre tags explícitas. No entanto, como xamarin.forms 3.0, as `ResourceDictionary` tags não são necessárias. Em vez `ResourceDictionary` disso, o objeto é criado automaticamente e `Resources` você pode inserir os recursos diretamente entre as tags de elemento de propriedade.

## <a name="consume-resources-in-xaml"></a>Consumir recursos em XAML

Cada recurso tem uma chave especificada usando o `x:Key` atributo, `ResourceDictionary`que se torna sua chave de dicionário no . A chave é usada para [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) referenciar [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) um recurso da extensão com ou marcação.

A `StaticResource` extensão de marcação é semelhante à extensão de `DynamicResource` marcação em que ambos usam uma chave de dicionário para referenciar um valor de um dicionário de recursos. No entanto, enquanto a extensão `StaticResource` de `DynamicResource` marcação executa uma única olhada no dicionário, a extensão de marcação mantém um link para a tecla dicionário. Portanto, se a entrada do dicionário associada à chave for substituída, a alteração será aplicada ao elemento visual. Isso permite que alterações de recurso em tempo de execução sejam feitas em um aplicativo. Para obter mais informações sobre extensões de marcação, consulte [Extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md).

O exemplo XAML a seguir mostra como consumir recursos [`StackLayout`](xref:Xamarin.Forms.StackLayout)e também define recursos adicionais em:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ResourceDictionaryDemo.HomePage"
             Title="Home Page">
    <StackLayout Margin="{StaticResource PageMargin}">
        <StackLayout.Resources>
            <!-- Implicit style -->
            <Style TargetType="Button">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="#1976D2" />
                <Setter Property="TextColor" Value="White" />
                <Setter Property="CornerRadius" Value="5" />
            </Style>
        </StackLayout.Resources>

        <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries." />
        <Button Text="Navigate"
                Clicked="OnNavigateButtonClicked" />
    </StackLayout>
</ContentPage>
```

Neste exemplo, [`ContentPage`](xref:Xamarin.Forms.ContentPage) o objeto consome o estilo implícito definido no dicionário de recursos de nível de aplicação. O [`StackLayout`](xref:Xamarin.Forms.StackLayout) objeto consome o `PageMargin` recurso definido no dicionário [`Button`](xref:Xamarin.Forms.Button) de recursos de nível [`StackLayout`](xref:Xamarin.Forms.StackLayout) de aplicativo, enquanto o objeto consome o estilo implícito definido no dicionário de recursos. Isso resulta na aparência mostrada nas capturas de tela seguir:

[![Recursos de consumo de recursos](resource-dictionaries-images/consuming.png "Consumindo recursos do ResourceDictionary")](resource-dictionaries-images/consuming-large.png#lightbox "Consumindo recursos do ResourceDictionary")

> [!IMPORTANT]
> Os recursos específicos de uma única página não devem ser incluídos em um dicionário de recursos de nível de aplicativo, pois esses recursos serão analisados na inicialização do aplicativo em vez de quando exigido por uma página. Para obter mais informações, consulte [Reduzir o tamanho do dicionário de recursos do aplicativo](~/xamarin-forms/deploy-test/performance.md).

## <a name="resource-lookup-behavior"></a>Comportamento de análise de recursos

O seguinte processo de consulta ocorre quando [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) um [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) recurso é referenciado com a extensão ou marcação:

- A chave solicitada é verificada no dicionário de recursos, se ela existe, para o elemento que define a propriedade. Se a chave solicitada for encontrada, seu valor será devolvido e o processo de pesquisa será encerrado.
- Se uma correspondência não for encontrada, o processo de pesquisa procurará a árvore visual para cima, verificando o dicionário de recursos de cada elemento pai. Se a chave solicitada for encontrada, seu valor será devolvido e o processo de pesquisa será encerrado. Caso contrário, o processo continua para cima até que o elemento raiz seja alcançado.
- Se uma correspondência não for encontrada no elemento raiz, o dicionário de recursos de nível de aplicativo será examinado.
- Se um fósforo ainda não `XamlParseException` for encontrado, um é jogado.

Portanto, quando o analisador XAML `StaticResource` `DynamicResource` encontra uma extensão ou marcação, ele procura uma chave correspondente viajando pela árvore visual, usando a primeira correspondência que encontra. Se essa pesquisa terminar na página e a chave ainda não tiver sido `ResourceDictionary` encontrada, `App` o analisador XAML procurará o anexo ao objeto. Se a chave ainda não for encontrada, uma exceção será jogada.

## <a name="override-resources"></a>Substituir recursos

Quando os recursos compartilham chaves, os recursos definidos mais baixos na árvore visual terão precedência sobre aqueles definidos mais acima. Por exemplo, `AppBackgroundColor` a `AliceBlue` definição de um recurso no nível do `AppBackgroundColor` aplicativo `Teal`será substituída por um recurso de nível de página definido para . Da mesma forma, `AppBackgroundColor` um recurso de nível de `AppBackgroundColor` página será substituído por um recurso de nível de controle.

## <a name="stand-alone-resource-dictionaries"></a>Dicionários de recursos autônomos

Uma classe derivada também [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) pode estar em um arquivo independente separado. O arquivo resultante pode então ser compartilhado entre aplicativos.

Para criar esse arquivo, adicione um novo item **de Exibição de Conteúdo** ou Página de **Conteúdo** ao projeto (mas não uma **exibição de conteúdo** ou página de **conteúdo** com apenas um arquivo C#). Exclua o arquivo de código atrás e no arquivo XAML [`ContentView`](xref:Xamarin.Forms.ContentView) [`ContentPage`](xref:Xamarin.Forms.ContentPage) altere o nome da classe base de ou para [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). Além disso, `x:Class` remova o atributo da tag raiz do arquivo.

O exemplo XAML [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) a seguir mostra um chamado **MyResourceDictionary.xaml**:

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml">
    <DataTemplate x:Key="PersonDataTemplate">
        <ViewCell>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.5*" />
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.3*" />
                </Grid.ColumnDefinitions>
                <Label Text="{Binding Name}"
                       TextColor="{StaticResource NormalTextColor}"
                       FontAttributes="Bold" />
                <Label Grid.Column="1"
                       Text="{Binding Age}"
                       TextColor="{StaticResource NormalTextColor}" />
                <Label Grid.Column="2"
                       Text="{Binding Location}"
                       TextColor="{StaticResource NormalTextColor}"
                       HorizontalTextAlignment="End" />
            </Grid>
        </ViewCell>
    </DataTemplate>
</ResourceDictionary>
```

Neste exemplo, [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) o contém um único recurso, [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)que é um objeto de tipo . **MyResourceDictionary.xaml** pode ser consumido mesclando-o em outro dicionário de recursos.

## <a name="merged-resource-dictionaries"></a>Dicionários de recursos mesclados

Dicionários de recursos mesclados [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) combinam um `ResourceDictionary`ou mais objetos em outro .

### <a name="merge-local-resource-dictionaries"></a>Mesclar dicionários de recursos locais

Um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) arquivo local pode ser `ResourceDictionary` mesclado `ResourceDictionary` em [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) outro criando um objeto cuja propriedade está definida como o nome de arquivo do arquivo XAML com os recursos:

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

Essa sintaxe não `MyResourceDictionary` instancia a classe. Em vez disso, ele faz referência ao arquivo XAML. Por essa razão, [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) ao definir a propriedade, um arquivo de `x:Class` código atrás não é necessário e o atributo pode ser removido da tag raiz do arquivo **MyResourceDictionary.xaml.**

> [!IMPORTANT]
> A [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) propriedade só pode ser definida a partir de XAML.

### <a name="merge-resource-dictionaries-from-other-assemblies"></a>Mesclar dicionários de recursos de outras assembléias

A [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) também pode ser `ResourceDictionary` fundido em outro [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) adicionando-o à propriedade do `ResourceDictionary`. Esta técnica permite que os dicionários de recursos sejam mesclados, independentemente da montagem em que residem. A fusão de dicionários de [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) recursos de conjuntos externos requer que a ação de compilação seja definida como **EmbeddedResource,** para ter um arquivo de código atrás e para definir o atributo `x:Class` na tag raiz do arquivo.

> [!WARNING]
> A [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) classe também [`MergedWith`](xref:Xamarin.Forms.ResourceDictionary.MergedWith) define uma propriedade. No entanto, esta propriedade foi preterida e não deve mais ser usada.

O exemplo de código a seguir mostra [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) dois dicionários [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de recursos sendo adicionados à coleção de um nível de página :

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

Neste exemplo, um dicionário de recursos da mesma montagem e um dicionário de recursos de uma montagem externa são mesclados no dicionário de recursos de nível de página. Além disso, você também `ResourceDictionary` pode [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) adicionar outros objetos dentro das tags de elemento de propriedade e outros recursos fora dessas tags.

> [!IMPORTANT]
> Pode haver apenas `MergedDictionaries` uma tag de [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)elemento de propriedade `ResourceDictionary` em um , mas você pode colocar quantos objetos lá dentro for necessário.

Quando os [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) recursos `x:Key` mesclados compartilham valores de atributoidênticos, o Xamarin.Forms usa a seguinte precedência de recursos:

1. Os recursos locais para o dicionário de recursos.
1. Os recursos contidos nos dicionários de recursos `MergedDictionaries` que foram mesclados via cobrança, na ordem inversa estão listados no `MergedDictionaries` imóvel.

> [!NOTE]
> Pesquisar dicionários de recursos pode ser uma tarefa computacionalmente intensiva se um aplicativo contiver vários dicionários de recursos grandes. Portanto, para evitar pesquisas desnecessárias, você deve garantir que cada página em um aplicativo use apenas dicionários de recursos apropriados para a página.

## <a name="related-links"></a>Links relacionados

- [Dicionários de recursos (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)
- [Extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Estilos do Xamarin.Forms](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
