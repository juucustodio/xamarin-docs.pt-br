---
title: "Consumindo extensões de marcação XAML"
description: "Use as extensões de marcação XAML disponível em xamarin. Forms"
ms.topic: article
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/05/2018
ms.openlocfilehash: cc4407be9dee7e19dbf1f3cc03b3b88191717e6f
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="consuming-xaml-markup-extensions"></a>Consumindo extensões de marcação XAML

Extensões de marcação XAML ajudam a melhorar a eficiência e a flexibilidade de XAML, permitindo que os atributos do elemento a ser definido de uma variedade de fontes. Várias extensões de marcação XAML são parte da especificação do XAML 2009. Estes aparecem nos arquivos XAML com o habitual `x` prefixo de namespace e são conhecidos com esse prefixo. Elas são descritas nas seções a seguir:

- [`x:Static`](#static) &ndash; fazer referência a propriedades estáticas, campos ou membros de enumeração.
- [`x:Reference`](#reference) &ndash; referência chamada elementos na página.
- [`x:Type`](#type) &ndash; definir um atributo para um `System.Type` objeto.
- [`x:Array`](#array) &ndash; Construa uma matriz de objetos de um tipo específico.
- [`x:Null`](#null) &ndash; definir um atributo para um `null` valor.

Três outras extensões de marcação XAML historicamente são suportados pelas outras implementações de XAML e também são suportadas pelo xamarin. Forms. Elas são descritas mais detalhadamente nos outros artigos:

- `StaticResource` &ndash; referência a objetos de um dicionário de recurso, conforme descrito no artigo [ **dicionários de recursos**](~/xamarin-forms/xaml/resource-dictionaries.md).
- `DynamicResource` &ndash; responder a alterações em objetos em um dicionário de recurso, conforme descrito no artigo [ **estilos dinâmicos**](~/xamarin-forms/user-interface/styles/dynamic.md).
- `Binding` &ndash; estabelecer um vínculo entre propriedades de dois objetos, conforme descrito no artigo [ **associação de dados**](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- `TemplateBinding` &ndash; executa a associação de dados de um modelo de controle, conforme descrito no artigo [**de associação de um modelo de controle**] / guias/xamarin forms/aplicativo fundamentos/modelos/controle-modelos/modelo-vinculação-/)

O [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/) layout usa a extensão de marcação personalizada [ `ConstraintExpression` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ConstraintExpression/). Esta extensão de marcação é descrita no artigo [ **RelativeLayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md).

<a name="static" />

## <a name="xstatic-markup-extension"></a>Extensão de marcação x:Static

O `x:Static` extensão de marcação é suportada pelo [ `StaticExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.StaticExtension/) classe. A classe tem uma propriedade única chamada [ `Member` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.StaticExtension.Member/) do tipo `string` que você defina como o nome de uma constante pública, a propriedade estática, o campo estático ou o membro de enumeração.

Uma maneira comum de usar `x:Static` deve primeiro definir uma classe com algumas constantes ou variáveis estáticas, como nesse pequeno `AppConstants` classe no [ **MarkupExtensions** ](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/) programa:

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

O **X:Static demonstração** página demonstra várias maneiras de usar o `x:Static` extensão de marcação. A abordagem mais detalhada instancia o `StaticExtension` classe entre `Label.FontSize` marcas de elemento de propriedade:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.StaticDemoPage"
             Title="x:Static Demo">
    <StackLayout Margin="10, 0">
        <Label Text="Label No. 1">
            <Label.FontSize>
                <x:StaticExtension Member="local:AppConstants.NormalFontSize" />
            </Label.FontSize>
        </Label>

        ···

    </StackLayout>
</ContentPage>
```

O analisador XAML também permite que o `StaticExtension` classe deve ser abreviado como `x:Static`:

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

Isso pode ser simplificado ainda mais, mas a alteração apresenta uma nova sintaxe: consiste em colocar o `StaticExtension` classe e o membro configuração entre chaves. A expressão resultante é definida diretamente para o `FontSize` atributo:

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

Observe que há *sem* aspas dentro de chaves. O `Member` propriedade `StaticExtension` não é mais um atributo XML. Em vez disso, ele faz parte da expressão para a extensão de marcação.

Assim como você pode abreviar `x:StaticExtension` para `x:Static` quando você usá-lo como um elemento de objeto, você também pode abreviá-lo na expressão entre chaves:

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

O `StaticExtension` classe tiver um `ContentProperty` fazer referência à propriedade de atributo `Member`, que marca esta propriedade como a propriedade da classe padrão conteúdo. Para as extensões de marcação XAML expressadas com chaves, você pode eliminar o `Member=` faz parte da expressão:

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

Este é o formulário mais comum dos `x:Static` extensão de marcação.

O **demonstração estático** página contém dois outros exemplos. A marca raiz do arquivo XAML contém uma declaração de namespace XML para o .NET `System` namespace:

```xaml
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

Isso permite que o `Label` tamanho da fonte a ser definido para o campo estático `Math.PI`. Que resulta em texto pequeno, portanto, o `Scale` está definida como `Math.E`:

```xaml
<Label Text="&#x03C0; &#x00D7; E sized text"
       FontSize="{x:Static sys:Math.PI}"
       Scale="{x:Static sys:Math.E}"
       HorizontalOptions="Center" />
```

O exemplo final exibe o `Device.RuntimePlatform` valor. O `Environment.NewLine` propriedade estática é usada para inserir um caractere de nova linha entre os dois `Span` objetos:

```xaml
<Label HorizontalTextAlignment="Center"
       FontSize="{x:Static local:AppConstants.NormalFontSize}">
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Runtime Platform: " />
            <Span Text="{x:Static sys:Environment.NewLine}" />
            <Span Text="{x:Static Device.RuntimePlatform}" />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

Aqui está o exemplo em execução em todas as três plataformas:

[![Demonstração de X:Static](consuming-images/staticdemo-small.png "X:Static demonstração")](consuming-images/staticdemo-large.png "X:Static demonstração")

<a name="reference" />

## <a name="xreference-markup-extension"></a>Extensão de marcação x:Reference

O `x:Reference` extensão de marcação é suportada pelo [ `ReferenceExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ReferenceExtension/) classe. A classe tem uma propriedade única chamada [ `Name` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.ReferenceExtension.Name/) do tipo `string` que você defina como o nome de um elemento na página que tem um nome com `x:Name`. Isso `Name` é a propriedade de conteúdo do `ReferenceExtension`, portanto `Name=` não é necessário quando `x:Reference` aparece entre chaves.

O `x:Reference` extensão de marcação é usada exclusivamente com associações de dados, que são descritas mais detalhadamente no artigo [ **associação de dados**](~/xamarin-forms/app-fundamentals/data-binding/index.md).

O **x: referência demonstração** página mostra dois usos do `x:Reference` com associações de dados, o primeiro onde ele é usado para definir o `Source` propriedade do `Binding` objeto e o segundo onde ele é usado para definir o `BindingContext` propriedade de associações de dados:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.ReferenceDemoPage"
             x:Name="page"
             Title="x:Reference Demo">

    <StackLayout Margin="10, 0">

        <Label Text="{Binding Source={x:Reference page},
                              StringFormat='The type of this page is {0}'}"
               FontSize="18"
               VerticalOptions="CenterAndExpand"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="slider"
                Maximum="360"
                VerticalOptions="Center" />

        <Label BindingContext="{x:Reference slider}"
               Text="{Binding Value, StringFormat='{0:F0}&#x00B0; rotation'}"
               Rotation="{Binding Value}"
               FontSize="24"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

    </StackLayout>
</ContentPage>
```

Ambos `x:Reference` expressões usam a versão abreviada do `ReferenceExtension` nome da classe e eliminar o `Name=` faz parte da expressão. No primeiro exemplo, o `x:Reference` extensão de marcação é inserida no `Binding` extensão de marcação. Observe que o `Source` e `StringFormat` configurações são separadas por vírgulas. Aqui está o programa em execução em todas as três plataformas:

[![Demonstração de x: referência](consuming-images/referencedemo-small.png "x: referência demonstração")](consuming-images/referencedemo-large.png "demonstração x: referência")

<a name="type" />

## <a name="xtype-markup-extension"></a>Extensão de marcação x:Type

O `x:Type` extensão de marcação é o equivalente XAML do c# [ `typeof` ](/dotnet/csharp/language-reference/keywords/typeof/) palavra-chave. Dá suporte a ele o [ `TypeExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TypeExtension/) classe, que define uma propriedade chamada [ `TypeName` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.TypeExtension.TypeName/) do tipo `string` que é definido como um nome de classe ou estrutura. O `x:Type` retorna da extensão de marcação de [ `System.Type` ](https://developer.xamarin.com/api/type/System.Type/) objeto da classe ou estrutura. `TypeName` é a propriedade de conteúdo de `TypeExtension`, portanto `TypeName=` não é necessário quando `x:Type` é exibida com as chaves.

Em xamarin. Forms, há várias propriedades com argumentos de tipo `Type`. Exemplos incluem o [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/) propriedade `Style`e o [X:TypeArguments](~/xamarin-forms/xaml/passing-arguments.md#generic_type_arguments) atributo usado para especificar os argumentos nas classes genéricas. No entanto, o analisador XAML executa o `typeof` operação automaticamente e o `x:Type` extensão de marcação não é usada nesses casos.

Um único lugar onde `x:Type` *é* solicitado é com o `x:Array` extensão de marcação, que é descrita no [próxima seção](#array).

O `x:Type` extensão de marcação também é útil quando estiver criando um menu onde cada item de menu corresponde a um objeto de um tipo específico. Você pode associar um `Type` com cada item de menu do objeto e, em seguida, criar uma instância de objeto quando o item de menu é selecionado.

Isso é como o menu de navegação em `MainPage` no **extensões de marcação** programa funciona. O **MainPage. XAML** arquivo contém um `TableView` com cada `TextCell` correspondente a uma página específica no programa:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:MarkupExtensions"
             x:Class="MarkupExtensions.MainPage"
             Title="Markup Extensions"
             Padding="10">
    <TableView Intent="Menu">
        <TableRoot>
            <TableSection>
                <TextCell Text="x:Static Demo"
                          Detail="Access constants or statics"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:StaticDemoPage}" />

                <TextCell Text="x:Reference Demo"
                          Detail="Reference named elements on the page"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:ReferenceDemoPage}" />

                <TextCell Text="x:Type Demo"
                          Detail="Associate a Button with a Type"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:TypeDemoPage}" />

                <TextCell Text="x:Array Demo"
                          Detail="Use an array to fill a ListView"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:ArrayDemoPage}" />

                ···                          

        </TableRoot>
    </TableView>
</ContentPage>
```

Aqui está a página principal de abertura em **extensões de marcação**:

[![Principal página](consuming-images/mainpage-small.png "principal página")](consuming-images/mainpage-large.png "principal página")

Cada `CommandParameter` está definida como um `x:Type` extensão de marcação que faz referência a uma das outras páginas. O `Command` propriedade é associada a uma propriedade denominada `NavigateCommand`. Essa propriedade é definida no `MainPage` arquivo code-behind:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(async (Type pageType) =>
        {
            Page page = (Page)Activator.CreateInstance(pageType);
            await Navigation.PushAsync(page);
        });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

O `NavigateCommand` propriedade é um `Command` objeto que implementa um comando execute com um argumento de tipo `Type` &mdash; o valor de `CommandParameter`. Usa o método `Activator.CreateInstance` para instanciar a página e, em seguida, navega para ele. O construtor conclui definindo o `BindingContext` da página para si mesmo, que permite que o `Binding` em `Command` para trabalhar. Consulte o [ **associação de dados** ](~/xamarin-forms/app-fundamentals/data-binding/index.md) artigo e particularmente o [ **Commanding** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) artigo para obter mais detalhes sobre esse tipo de código.

O **x: tipo de demonstração** página usa uma técnica semelhante para instanciar elementos xamarin. Forms e adicioná-las a um `StackLayout`. O arquivo XAML inicialmente consiste em três `Button` elementos com seus `Command` propriedades definidas para um `Binding` e `CommandParameter` propriedades definidas como tipos de três modos de exibição do xamarin. Forms:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.TypeDemoPage"
             Title="x:Type Demo">

    <StackLayout x:Name="stackLayout"
                 Padding="10, 0">

        <Button Text="Create a Slider"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Slider}" />

        <Button Text="Create a Stepper"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Stepper}" />

        <Button Text="Create a Switch"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Command="{Binding CreateCommand}"
                CommandParameter="{x:Type Switch}" />
    </StackLayout>
</ContentPage>
```

O arquivo code-behind define e inicializa o `CreateCommand` propriedade:

```csharp
public partial class TypeDemoPage : ContentPage
{
    public TypeDemoPage()
    {
        InitializeComponent();

        CreateCommand = new Command<Type>((Type viewType) =>
        {
            View view = (View)Activator.CreateInstance(viewType);
            view.VerticalOptions = LayoutOptions.CenterAndExpand;
            stackLayout.Children.Add(view);
        });

        BindingContext = this;
    }

    public ICommand CreateCommand { private set; get; }
}
```

O método que é executado quando um `Button` é pressionado cria uma nova instância do argumento, define seu `VerticalOptions` propriedade e adiciona-o para o `StackLayout`. Os três `Button` elementos, em seguida, compartilhem a página com criado dinamicamente exibições:

[![Demonstração de x: tipo](consuming-images/typedemo-small.png "X:Type demonstração")](consuming-images/typedemo-large.png "x: tipo demonstração")

<a name="array" />

## <a name="xarray-markup-extension"></a>Extensão de marcação x:Array

O `x:Array` extensão de marcação permite que você defina uma matriz na marcação. Dá suporte a ele o [ `ArrayExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.ArrayExtension/) classe que define duas propriedades:

- `Type` tipo `Type`, que indica o tipo dos elementos na matriz.
- `Items` tipo `IList`, que é uma coleção dos itens em si. Essa é a propriedade de conteúdo de `ArrayExtension`.

O `x:Array` extensão de marcação em si nunca aparece entre chaves. Em vez disso, `x:Array` marcas de início e término delimitam a lista de itens. Definir o `Type` propriedade para um `x:Type` extensão de marcação.

O **X:array demonstração** página mostra como usar `x:Array` para adicionar itens a uma `ListView` definindo o `ItemsSource` propriedade em uma matriz:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.ArrayDemoPage"
             Title="x:Array Demo Page">
    <ListView Margin="10">
        <ListView.ItemsSource>
            <x:Array Type="{x:Type Color}">
                <Color>Aqua</Color>
                <Color>Black</Color>
                <Color>Blue</Color>
                <Color>Fuchsia</Color>
                <Color>Gray</Color>
                <Color>Green</Color>
                <Color>Lime</Color>
                <Color>Maroon</Color>
                <Color>Navy</Color>
                <Color>Olive</Color>
                <Color>Pink</Color>
                <Color>Purple</Color>
                <Color>Red</Color>
                <Color>Silver</Color>
                <Color>Teal</Color>
                <Color>White</Color>
                <Color>Yellow</Color>
            </x:Array>
        </ListView.ItemsSource>

        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <BoxView Color="{Binding}"
                             Margin="3" />    
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>        
```

O `ViewCell` cria um simples `BoxView` para cada entrada de cor:

[![Demonstração de X:array](consuming-images/arraydemo-small.png "X:array demonstração")](consuming-images/arraydemo-large.png "X:array demonstração")

Há várias maneiras para especificar o indivíduo `Color` itens nesta matriz. Você pode usar um `x:Static` extensão de marcação:

```xaml
<x:Static Member="Color.Blue" />
```

Ou, você pode usar `StaticResource` para recuperar uma cor de um dicionário de recursos:

```xaml
<StaticResource Key="myColor" />
```

No final deste artigo, você verá uma extensão de marcação XAML personalizada que também cria um novo valor de cor:

```xaml
<local:HslColor H="0.5" S="1.0" L="0.5" />
```

Ao definir as matrizes dos tipos comuns como cadeias de caracteres ou números, use as marcas listadas no [ **passar argumentos de construtor** ](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) artigo para delimitar os valores.

<a name="null" />

## <a name="xnull-markup-extension"></a>Extensão de marcação x:Null

O `x:Null` extensão de marcação é suportada pelo [ `NullExtension` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.NullExtension/) classe. Ele não tem propriedades e equivale a simplesmente XAML do c# [ `null` ](/dotnet/csharp/language-reference/keywords/null/) palavra-chave.

O `x:Null` extensão de marcação é raramente necessário e raramente usada, mas se você encontrar uma necessidade para ele, você estará satisfeitas que ele existe.

O **X:Null demonstração** página ilustra um cenário quando `x:Null` pode ser conveniente. Suponhamos que você defina implícita `Style` para `Label` que inclui um `Setter` que define o `FontFamily` propriedade para um nome de família dependente de plataforma:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.NullDemoPage"
             Title="x:Null Demo">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="48" />
                <Setter Property="FontFamily">
                    <Setter.Value>
                        <OnPlatform x:TypeArguments="x:String">
                            <On Platform="iOS" Value="Times New Roman" />
                            <On Platform="Android" Value="serif" />
                            <On Platform="UWP" Value="Times New Roman" />
                        </OnPlatform>
                    </Setter.Value>
                </Setter>
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <ContentPage.Content>
        <StackLayout Padding="10, 0">
            <Label Text="Text 1" />
            <Label Text="Text 2" />

            <Label Text="Text 3"
                   FontFamily="{x:Null}" />

            <Label Text="Text 4" />
            <Label Text="Text 5" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>   
```

Em seguida, você descobre que para uma da `Label` elementos, você deseja que todas as configurações de propriedade no implícita `Style` exceto para o `FontFamily`, que você deseja o valor padrão. Você pode definir outro `Style` para essa finalidade, mas uma abordagem mais simples é simplesmente para definir o `FontFamily` propriedade particular `Label` para `x:Null`, conforme demonstrado no Centro de `Label`.

Aqui está o programa em execução em plataformas de três:

[![x:Null Demo](consuming-images/nulldemo-small.png "x:Null Demo")](consuming-images/nulldemo-large.png "x:Null Demo")

Observe que quatro do `Label` elementos têm uma fonte serif, mas o centro `Label` tem a fonte de sans-serif padrão.

## <a name="define-your-own-markup-extensions"></a>Definir suas próprias extensões de marcação

Se você encontrou a necessidade de uma extensão de marcação XAML que não está disponível no xamarin. Forms, você pode [criar seus próprios](creating.md).


## <a name="related-links"></a>Links relacionados

- [Extensões de marcação (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [Capítulo de extensões de marcação XAML do catálogo xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Dicionários de recursos](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilos dinâmicos](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md)
