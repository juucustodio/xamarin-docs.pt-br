---
title: Xamarin.Forms dicionários de recursos
description: Xamarin.Forms Os recursos XAML são objetos que podem ser compartilhados e reutilizados em um Xamarin.Forms aplicativo.
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/10/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.custom: video
ms.openlocfilehash: 60d16183e1a2ea162c97bbf8b30636a5a9999204
ms.sourcegitcommit: f2942b518f51317acbb263be5bc0c91e66239f50
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94590246"
---
# <a name="no-locxamarinforms-resource-dictionaries"></a>Xamarin.Forms dicionários de recursos

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)

Um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) é um repositório de recursos que são usados por um Xamarin.Forms aplicativo. Os recursos típicos que são armazenados em um `ResourceDictionary` incluem [estilos](~/xamarin-forms/user-interface/styles/index.md), [modelos de controle](~/xamarin-forms/app-fundamentals/templates/control-template.md), [modelos de dados](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md), cores e conversores.

Em XAML, os recursos que são armazenados em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) podem ser referenciados e aplicados aos elementos usando `StaticResource` a `DynamicResource` extensão de marcação ou. No C#, os recursos também podem ser definidos em um `ResourceDictionary` e, em seguida, referenciados e aplicados a elementos usando um indexador baseado em cadeia de caracteres. No entanto, há pouca vantagem em usar um `ResourceDictionary` em C#, pois objetos compartilhados podem ser armazenados como campos ou propriedades e acessados diretamente sem precisar primeiro recuperá-los de um dicionário.

## <a name="create-resources-in-xaml"></a>Criar recursos em XAML

Cada [`VisualElement`](xref:Xamarin.Forms.VisualElement) objeto derivado tem uma [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) propriedade, que é um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) que pode conter recursos. Da mesma forma, um [`Application`](xref:Xamarin.Forms.Application) objeto derivado tem uma [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) propriedade, que é um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) que pode conter recursos.

Um Xamarin.Forms aplicativo contém apenas a classe derivada de [`Application`](xref:Xamarin.Forms.Application) , mas geralmente usa muitas classes que derivam de [`VisualElement`](xref:Xamarin.Forms.VisualElement) , incluindo páginas, layouts e controles. Qualquer um desses objetos pode ter sua `Resources` propriedade definida como um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) recurso que os contém. Escolhendo onde colocar um impacto específico `ResourceDictionary` em que os recursos podem ser usados:

- Os recursos em um `ResourceDictionary` que são anexados a uma exibição como [`Button`](xref:Xamarin.Forms.Button) ou [`Label`](xref:Xamarin.Forms.Label) só podem ser aplicados a esse objeto específico.
- Recursos em um `ResourceDictionary` anexado a um layout, como [`StackLayout`](xref:Xamarin.Forms.StackLayout) ou [`Grid`](xref:Xamarin.Forms.Grid) podem ser aplicados ao layout e a todos os filhos desse layout.
- Os recursos em um `ResourceDictionary` definido no nível da página podem ser aplicados à página e a todos os seus filhos.
- Os recursos em um `ResourceDictionary` definido no nível do aplicativo podem ser aplicados em todo o aplicativo.

Com exceção dos estilos implícitos, cada recurso no dicionário de recursos deve ter uma chave de cadeia de caracteres exclusiva definida com o `x:Key` atributo.

O XAML a seguir mostra os recursos definidos em um nível de aplicativo `ResourceDictionary` no arquivo **app. XAML** :

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

Neste exemplo, o dicionário de recursos define um [`Thickness`](xref:Xamarin.Forms.Thickness) recurso, vários [`Color`](xref:Xamarin.Forms.Color) recursos e dois recursos implícitos [`Style`](xref:Xamarin.Forms.Style) . Para obter mais informações sobre a `App` classe, consulte [ Xamarin.Forms App class](~/xamarin-forms/app-fundamentals/application-class.md).

> [!NOTE]
> Também é válido posicionar todos os recursos entre marcas explícitas `ResourceDictionary` . No entanto, como Xamarin.Forms 3,0, as `ResourceDictionary` marcas não são necessárias. Em vez disso, o `ResourceDictionary` objeto é criado automaticamente e você pode inserir os recursos diretamente entre as `Resources` marcas de elemento de propriedade.

## <a name="consume-resources-in-xaml"></a>Consumir recursos em XAML

Cada recurso tem uma chave que é especificada usando o `x:Key` atributo, que se torna sua chave de dicionário no `ResourceDictionary` . A chave é usada para fazer referência a um recurso do [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) com [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) a [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) extensão de marcação ou.

A `StaticResource` extensão de marcação é semelhante à `DynamicResource` extensão de marcação, pois ambas usam uma chave de dicionário para fazer referência a um valor de um dicionário de recursos. No entanto, enquanto a `StaticResource` extensão de marcação executa uma única pesquisa de dicionário, a `DynamicResource` extensão de marcação mantém um link para a chave de dicionário. Portanto, se a entrada de dicionário associada à chave for substituída, a alteração será aplicada ao elemento visual. Isso permite que as alterações de recursos de tempo de execução sejam feitas em um aplicativo. Para obter mais informações sobre extensões de marcação, consulte [extensões de marcação XAML](~/xamarin-forms/xaml/markup-extensions/index.md).

O exemplo de XAML a seguir mostra como consumir recursos e também define recursos adicionais em um [`StackLayout`](xref:Xamarin.Forms.StackLayout) :

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

Neste exemplo, o [`ContentPage`](xref:Xamarin.Forms.ContentPage) objeto consome o estilo implícito definido no dicionário de recursos de nível de aplicativo. O [`StackLayout`](xref:Xamarin.Forms.StackLayout) objeto consome o `PageMargin` recurso definido no dicionário de recursos de nível de aplicativo, enquanto o [`Button`](xref:Xamarin.Forms.Button) objeto consome o estilo implícito definido no [`StackLayout`](xref:Xamarin.Forms.StackLayout) dicionário de recursos. Isso resulta na aparência mostrada nas capturas de tela seguir:

[![Consumindo recursos de ResourceDictionary](resource-dictionaries-images/consuming.png "Consumindo recursos de ResourceDictionary")](resource-dictionaries-images/consuming-large.png#lightbox "Consumindo recursos de ResourceDictionary")

> [!IMPORTANT]
> Recursos que são específicos de uma única página não devem ser incluídos em um dicionário de recursos de nível de aplicativo, pois esses recursos serão analisados na inicialização do aplicativo, em vez de quando exigidos por uma página. Para obter mais informações, consulte [reduzir o tamanho do dicionário de recursos do aplicativo](~/xamarin-forms/deploy-test/performance.md).

## <a name="resource-lookup-behavior"></a>Comportamento de pesquisa de recursos

O seguinte processo de pesquisa ocorre quando um recurso é referenciado com a [`StaticResource`](xref:Xamarin.Forms.Xaml.StaticResourceExtension) [`DynamicResource`](xref:Xamarin.Forms.Xaml.DynamicResourceExtension) extensão de marcação ou:

- A chave solicitada é verificada no dicionário de recursos, se existir, para o elemento que define a propriedade. Se a chave solicitada for encontrada, seu valor é retornado e o processo de pesquisa é encerrado.
- Se uma correspondência não for encontrada, o processo de pesquisa pesquisará a árvore visual para cima, verificando o dicionário de recursos de cada elemento pai. Se a chave solicitada for encontrada, seu valor é retornado e o processo de pesquisa é encerrado. Caso contrário, o processo continuará até o elemento raiz ser atingido.
- Se uma correspondência não for encontrada no elemento raiz, o dicionário de recursos de nível de aplicativo será examinado.
- Se uma correspondência ainda não for encontrada, um `XamlParseException` será lançado.

Portanto, quando o analisador XAML encontra uma `StaticResource` `DynamicResource` extensão de marcação ou, ele procura uma chave correspondente viajando pela árvore visual, usando a primeira correspondência encontrada. Se essa pesquisa terminar na página e a chave ainda não tiver sido encontrada, o analisador XAML pesquisará o `ResourceDictionary` objeto anexado `App` . Se a chave ainda não for encontrada, uma exceção será lançada.

## <a name="override-resources"></a>Substituir recursos

Quando os recursos compartilharem chaves, os recursos definidos na parte inferior da árvore visual terão precedência sobre aquelas definidas acima. Por exemplo, a definição de um `AppBackgroundColor` recurso para `AliceBlue` no nível do aplicativo será substituída por um `AppBackgroundColor` conjunto de recursos de nível de página como `Teal` . Da mesma forma, um recurso de nível de página `AppBackgroundColor` será substituído por um recurso de nível de controle `AppBackgroundColor` .

## <a name="stand-alone-resource-dictionaries"></a>Dicionários de recursos autônomos

Uma classe derivada de [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) também pode estar em um arquivo XAML autônomo. O arquivo XAML pode ser compartilhado entre aplicativos.

Para criar esse arquivo, adicione um novo **modo de exibição de conteúdo** ou item de página de **conteúdo** ao projeto (mas não a uma **exibição de conteúdo** ou a uma página de **conteúdo** com apenas um arquivo C#). Exclua o arquivo code-behind e, no arquivo XAML, altere o nome da classe base de [`ContentView`](xref:Xamarin.Forms.ContentView) ou [`ContentPage`](xref:Xamarin.Forms.ContentPage) para [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . Além disso, remova o `x:Class` atributo da marca raiz do arquivo.

O exemplo de XAML a seguir mostra um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) chamado **myresourcedictionary. XAML** :

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

Neste exemplo, o [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) contém um único recurso, que é um objeto do tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . **Myresourcedictionary. XAML** pode ser consumido mesclando-o em outro dicionário de recursos.

Por padrão, o vinculador removerá arquivos XAML autônomos de builds de versão quando o comportamento do vinculador for definido para vincular todos os assemblies. Para garantir que os arquivos XAML autônomos permaneçam em uma compilação de versão:

1. Adicione um `Preserve` atributo personalizado ao assembly que contém os arquivos XAML autônomos. Para obter mais informações, consulte [preservando código](~/ios/deploy-test/linker.md).
1. Defina o `Preserve` atributo no nível do assembly:

    ```csharp
    [assembly:Preserve(AllMembers = true)]
    ```

Para obter mais informações sobre vinculação, consulte [vinculando aplicativos Xamarin. Ios](~/ios/deploy-test/linker.md) e [vinculando no Android](~/android/deploy-test/linker.md).

## <a name="merged-resource-dictionaries"></a>Dicionários de recursos mesclados

Os dicionários de recurso mesclados combinam um ou mais [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) objetos em outro `ResourceDictionary` .

### <a name="merge-local-resource-dictionaries"></a>Mesclar dicionários de recursos locais

Um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) arquivo local pode ser mesclado em outro `ResourceDictionary` criando um `ResourceDictionary` objeto cuja [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) propriedade está definida como o nome do arquivo XAML com os recursos:

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

Essa sintaxe não instancia a `MyResourceDictionary` classe. Em vez disso, ele faz referência ao arquivo XAML. Por esse motivo, ao definir a [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) propriedade, um arquivo code-behind não é necessário e o `x:Class` atributo pode ser removido da marca raiz do arquivo **myresourcedictionary. XAML** .

> [!IMPORTANT]
> A [`Source`](xref:Xamarin.Forms.ResourceDictionary.Source) propriedade só pode ser definida a partir de XAML.

### <a name="merge-resource-dictionaries-from-other-assemblies"></a>Mesclar dicionários de recurso de outros assemblies

Um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) também pode ser mesclado em outro `ResourceDictionary` adicionando-o à [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) Propriedade do `ResourceDictionary` . Essa técnica permite que os dicionários de recursos sejam mesclados, independentemente do assembly no qual residem. A mesclagem de dicionários de recursos de assemblies externos exige que o [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) tenha uma ação de compilação definida como **EmbeddedResource** , tenha um arquivo code-behind e defina o `x:Class` atributo na marca raiz do arquivo.

> [!WARNING]
> A [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) classe também define uma [`MergedWith`](xref:Xamarin.Forms.ResourceDictionary.MergedWith) propriedade. No entanto, essa propriedade foi preterida e não deve mais ser usada.

O exemplo de código a seguir mostra dois dicionários de recursos sendo adicionados à [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) coleção de um nível de página [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) :

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

Neste exemplo, um dicionário de recursos do mesmo assembly e um dicionário de recursos de um assembly externo são mesclados no dicionário de recursos de nível de página. Além disso, você também pode adicionar outros `ResourceDictionary` objetos dentro das [`MergedDictionaries`](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries) marcas de elemento de propriedade e outros recursos fora dessas marcas.

> [!IMPORTANT]
> Pode haver apenas uma `MergedDictionaries` marca de elemento de propriedade em um [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , mas você pode colocar quantos `ResourceDictionary` objetos existirem ali, conforme necessário.

Quando [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) os recursos mesclados compartilham `x:Key` valores de atributo idênticos, Xamarin.Forms o usa a seguinte precedência de recurso:

1. Os recursos locais para o dicionário de recursos.
1. Os recursos contidos nos dicionários de recursos que foram mesclados por meio da `MergedDictionaries` coleção, na ordem inversa, são listados na `MergedDictionaries` propriedade.

> [!NOTE]
> Pesquisar dicionários de recursos pode ser uma tarefa computacionalmente intensiva se um aplicativo contiver vários dicionários de recursos grandes. Portanto, para evitar a pesquisa desnecessária, você deve garantir que cada página em um aplicativo use apenas os dicionários de recursos apropriados para a página.

## <a name="related-links"></a>Links relacionados

- [Dicionários de recursos (exemplo)](/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)
- [Extensões de marcação do XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin.Forms Estilos](~/xamarin-forms/user-interface/styles/index.md)
- [Como vincular aplicativos do Xamarin.iOS](~/ios/deploy-test/linker.md)
- [Vinculação no Android](~/android/deploy-test/linker.md)
- [API ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
