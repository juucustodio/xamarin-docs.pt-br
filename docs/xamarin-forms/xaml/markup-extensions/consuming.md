---
title: Consumo de extensões de marcação XAML
description: Este artigo explica como usar extensões de marcação de XAML de xamarin. Forms para aprimorar a potência e flexibilidade de XAML, permitindo que os atributos do elemento a ser definido de uma variedade de fontes.
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2019
ms.openlocfilehash: 03aaf471479a5113aade6bd3f34034afadfb538c
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2019
ms.locfileid: "69887894"
---
# <a name="consuming-xaml-markup-extensions"></a>Consumo de extensões de marcação XAML

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

Extensões de marcação XAML ajudar a aprimorar o poder e flexibilidade do XAML, permitindo que os atributos do elemento a ser definido de uma variedade de fontes. Várias extensões de marcação XAML são parte da especificação do XAML 2009. Eles aparecem nos arquivos XAML com o habitual `x` prefixo de namespace e são normalmente chamados para com esse prefixo. Este artigo discute as extensões de marcação a seguir:

- [`x:Static`](#static) – fazer referência a propriedades estáticas, campos ou membros de enumeração.
- [`x:Reference`](#reference) – chamada elementos na página de referência.
- [`x:Type`](#type) – definir um atributo para um `System.Type` objeto.
- [`x:Array`](#array) – construir uma matriz de objetos de um tipo específico.
- [`x:Null`](#null) – definir um atributo para um `null` valor.
- [`OnPlatform`](#onplatform) – Personalizar a aparência da interface do usuário em uma base por plataforma.
- [`OnIdiom`](#onidiom) – Personalizar a aparência da interface do usuário com base no idioma do dispositivo em que o aplicativo está sendo executado.
- [`DataTemplate`](#datatemplate-markup-extension)– Converte um tipo em um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate).
- [`FontImage`](#fontimage-markup-extension)-exibir um ícone de fonte em qualquer modo de exibição que `ImageSource`possa exibir um.

Extensões de marcação XAML adicionais historicamente são suportadas pelas outras implementações de XAML e também são suportadas pelo xamarin. Forms. Elas são descritas mais detalhadamente em outros artigos:

- `StaticResource` &ndash; fazer referência a objetos de um dicionário de recursos, conforme descrito no artigo [ **dicionários de recursos**](~/xamarin-forms/xaml/resource-dictionaries.md).
- `DynamicResource` &ndash; responder a alterações em objetos em um dicionário de recursos, conforme descrito no artigo [ **estilos dinâmicos**](~/xamarin-forms/user-interface/styles/dynamic.md).
- `Binding` &ndash; estabelecer um vínculo entre as propriedades de dois objetos, conforme descrito no artigo [ **associação de dados**](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- `TemplateBinding` &ndash; executa a associação de dados de um modelo de controle, conforme discutido no artigo [ **de um modelo de controle de associação**](~/xamarin-forms/app-fundamentals/templates/control-templates/template-binding.md).

O [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout) layout faz uso da extensão de marcação personalizada [ `ConstraintExpression` ](xref:Xamarin.Forms.ConstraintExpression). Esta extensão de marcação é descrita no artigo [ **RelativeLayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md).

<a name="static" />

## <a name="xstatic-markup-extension"></a>Extensão de marcação x:Static

O `x:Static` extensão de marcação dá suporte a [ `StaticExtension` ](xref:Xamarin.Forms.Xaml.StaticExtension) classe. A classe tem uma única propriedade chamada [ `Member` ](xref:Xamarin.Forms.Xaml.StaticExtension.Member) do tipo `string` que você defina como o nome de uma constante pública, a propriedade estática, o campo estático ou o membro de enumeração.

Uma maneira comum de usar `x:Static` deve primeiro definir uma classe com algumas constantes ou variáveis estáticas, como nesse pequeno `AppConstants` classe a [ **MarkupExtensions** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions) programa:

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

O **demonstração X:Static** página demonstra várias maneiras de usar o `x:Static` extensão de marcação. A abordagem mais detalhada cria uma instância de `StaticExtension` classe entre `Label.FontSize` marcas de elemento de propriedade:

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

O analisador XAML também permite que o `StaticExtension` classe deverá ser abreviado como `x:Static`:

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

Isso pode ser simplificado ainda mais, mas a alteração apresenta algumas novas sintaxes: Ele consiste em colocar a `StaticExtension` classe e a configuração de membro entre chaves. A expressão resultante é definida diretamente para o `FontSize` atributo:

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

Observe que há *nenhum* aspas dentro das chaves. O `Member` propriedade de `StaticExtension` não é mais um atributo XML. Em vez disso, ele é parte da expressão para a extensão de marcação.

Assim como você pode abreviar `x:StaticExtension` para `x:Static` quando você usá-lo como um elemento de objeto, você também pode abreviá-lo na expressão entre chaves:

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

O `StaticExtension` classe tem um `ContentProperty` referenciar a propriedade de atributo `Member`, que marca essa propriedade como propriedade de conteúdo de padrão da classe. Para extensões de marcação XAML expressadas com chaves, você pode eliminar o `Member=` faz parte da expressão:

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

Isso é a forma mais comum dos `x:Static` extensão de marcação.

O **demonstração estático** página contém dois outros exemplos. A marca de raiz do arquivo XAML contém uma declaração de namespace XML para o .NET `System` namespace:

```xaml
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

Isso permite que o `Label` tamanho da fonte a ser definido para o campo estático `Math.PI`. Que resulta em texto em vez disso, pequeno, portanto, o `Scale` estiver definida como `Math.E`:

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

Aqui está o exemplo em execução:

[![demonstração de x:static](consuming-images/staticdemo-small.png "demonstração de x:static")](consuming-images/staticdemo-large.png#lightbox "Demonstração de x:Static")

<a name="reference" />

## <a name="xreference-markup-extension"></a>Extensão de marcação x:Reference

O `x:Reference` extensão de marcação dá suporte a [ `ReferenceExtension` ](xref:Xamarin.Forms.Xaml.ReferenceExtension) classe. A classe tem uma única propriedade chamada [ `Name` ](xref:Xamarin.Forms.Xaml.ReferenceExtension.Name) do tipo `string` que você defina como o nome de um elemento na página que tem um nome com `x:Name`. Isso `Name` é a propriedade de conteúdo do `ReferenceExtension`, então `Name=` não é necessário quando `x:Reference` aparece entre chaves.

O `x:Reference` extensão de marcação é usada exclusivamente com associações de dados, que são descritas mais detalhadamente no artigo [ **Data Binding**](~/xamarin-forms/app-fundamentals/data-binding/index.md).

O **demonstração X:Reference** página mostra dois usos do `x:Reference` com associações de dados, primeiro onde ele é usado para definir a `Source` propriedade do `Binding` objeto e o segundo em que ele é usado para definir o `BindingContext` propriedade associações de dados de dois:

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

Ambos `x:Reference` expressões usam a versão abreviada do `ReferenceExtension` nome da classe e eliminar o `Name=` faz parte da expressão. No primeiro exemplo, o `x:Reference` extensão de marcação é inserida no `Binding` extensão de marcação. Observe que o `Source` e `StringFormat` configurações são separadas por vírgulas. Este é o programa em execução:

[![demonstração do x:Reference](consuming-images/referencedemo-small.png "demonstração do x:Reference")](consuming-images/referencedemo-large.png#lightbox "Demonstração do x:Reference")

<a name="type" />

## <a name="xtype-markup-extension"></a>Extensão de marcação x:Type

O `x:Type` extensão de marcação é o equivalente em XAML do c# [ `typeof` ](/dotnet/csharp/language-reference/keywords/typeof/) palavra-chave. Ele dá suporte a [ `TypeExtension` ](xref:Xamarin.Forms.Xaml.TypeExtension) classe, que define uma propriedade chamada [ `TypeName` ](xref:Xamarin.Forms.Xaml.TypeExtension.TypeName) do tipo `string` que é definido como um nome de classe ou estrutura. O `x:Type` retorna da extensão de marcação a [ `System.Type` ](xref:System.Type) objeto dessa classe ou estrutura. `TypeName` é a propriedade de conteúdo do `TypeExtension`, portanto `TypeName=` não é necessário quando `x:Type` é exibida com as chaves.

Dentro do xamarin. Forms, há várias propriedades que têm argumentos de tipo `Type`. Os exemplos incluem o [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType) propriedade do `Style`e o [X:TypeArguments](~/xamarin-forms/xaml/passing-arguments.md#generic_type_arguments) atributo usado para especificar argumentos em classes genéricas. No entanto, o analisador XAML executa o `typeof` operação automaticamente e o `x:Type` extensão de marcação não é usada nesses casos.

Um único lugar onde `x:Type` *é* é necessário com o `x:Array` extensão de marcação, que é descrito no [próxima seção](#array).

O `x:Type` extensão de marcação também é útil ao construir um menu no qual cada item de menu corresponde a um objeto de um tipo específico. Você pode associar um `Type` de objeto com cada item de menu e, em seguida, instancie o objeto quando o item de menu é selecionado.

Isso é como o menu de navegação em `MainPage` no **extensões de marcação** programa funciona. O **MainPage. XAML** arquivo contém uma `TableView` com cada `TextCell` correspondente a uma página específica no programa:

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

Aqui está a página principal de abertura **extensões de marcação**:

[![Página principal](consuming-images/mainpage-small.png "Página principal")](consuming-images/mainpage-large.png#lightbox "Página Principal")

Cada `CommandParameter` estiver definida como um `x:Type` extensão de marcação que faz referência a uma das outras páginas. O `Command` propriedade é associada a uma propriedade chamada `NavigateCommand`. Essa propriedade é definida no `MainPage` arquivo code-behind:

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

O `NavigateCommand` propriedade é um `Command` objeto que implementa um comando execute com um argumento do tipo `Type` &mdash; o valor de `CommandParameter`. Usa o método `Activator.CreateInstance` para instanciar a página e, em seguida, navega até ela. O construtor é concluído, definindo o `BindingContext` da página para si mesmo, que permite que o `Binding` em `Command` funcione. Consulte a [ **associação de dados** ](~/xamarin-forms/app-fundamentals/data-binding/index.md) artigo e principalmente o [ **Commanding** ](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) artigo para obter mais detalhes sobre esse tipo de código.

O **demonstração X:Type** página usa uma técnica semelhante para instanciar os elementos de xamarin. Forms e adicioná-los para um `StackLayout`. O arquivo XAML inicialmente consiste em três `Button` elementos com seus `Command` as propriedades definidas para um `Binding` e o `CommandParameter` propriedades definidas como tipos de três modos de exibição do xamarin. Forms:

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

O método que é executado quando um `Button` é pressionado cria uma nova instância do argumento, define sua `VerticalOptions` propriedade e adiciona-o para o `StackLayout`. Os três `Button` elementos, em seguida, compartilhem a página com modos de exibição criados dinamicamente:

[![demonstração de x:Type](consuming-images/typedemo-small.png "demonstração de x:Type")](consuming-images/typedemo-large.png#lightbox "Demonstração de x:Type")

<a name="array" />

## <a name="xarray-markup-extension"></a>Extensão de marcação x:Array

O `x:Array` extensão de marcação permite que você defina uma matriz na marcação. Ele dá suporte a [ `ArrayExtension` ](xref:Xamarin.Forms.Xaml.ArrayExtension) classe, que define duas propriedades:

- `Type` do tipo `Type`, que indica o tipo dos elementos na matriz.
- `Items` do tipo `IList`, que é uma coleção de itens em si. Essa é a propriedade de conteúdo de `ArrayExtension`.

O `x:Array` extensão de marcação em si nunca aparece entre chaves. Em vez disso, `x:Array` marcas de início e término delimitam a lista de itens. Defina as `Type` propriedade para um `x:Type` extensão de marcação.

O **demonstração X:array** página mostra como usar `x:Array` para adicionar itens a um `ListView` definindo o `ItemsSource` propriedade para uma matriz:

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

[![demonstração do x:array](consuming-images/arraydemo-small.png "demonstração do x:array")](consuming-images/arraydemo-large.png#lightbox "Demonstração do x:Array")

Há várias maneiras para especificar o indivíduo `Color` itens nessa matriz. Você pode usar um `x:Static` extensão de marcação:

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

Ao definir matrizes de tipos comuns, como cadeias de caracteres ou números, use as marcas listadas na [ **passar argumentos de construtor** ](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) artigo para delimitar os valores.

<a name="null" />

## <a name="xnull-markup-extension"></a>Extensão de marcação x:Null

O `x:Null` extensão de marcação dá suporte a [ `NullExtension` ](xref:Xamarin.Forms.Xaml.NullExtension) classe. Ele não tem propriedades e é simplesmente o equivalente XAML do c# [ `null` ](/dotnet/csharp/language-reference/keywords/null/) palavra-chave.

O `x:Null` extensão de marcação é raramente necessário e raramente usada, mas se você encontrar uma necessidade para ele, você ficará feliz por isso ele existe.

O **demonstração X:Null** página ilustra um cenário quando `x:Null` talvez seja conveniente. Suponhamos que você defina implícito `Style` para `Label` que inclui um `Setter` que define o `FontFamily` propriedade para um nome de família dependente de plataforma:

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

Em seguida, você descobre que, para uma da `Label` elementos, você deseja que todas as configurações de propriedade na implícito `Style` , exceto para o `FontFamily`, que você deseja ser o valor padrão. Você pode definir outra `Style` para essa finalidade, mas uma abordagem mais simples é simplesmente definir o `FontFamily` propriedade de determinada `Label` ao `x:Null`, conforme demonstrado no Centro de `Label`.

Este é o programa em execução:

[![demonstração do x:NULL](consuming-images/nulldemo-small.png "demonstração do x:NULL")](consuming-images/nulldemo-large.png#lightbox "Demonstração do x:Null")

Observe que quatro dos `Label` elementos têm uma fonte com serifa, mas o centro `Label` tem a fonte sem serifa padrão.

<a name="onplatform" />

## <a name="onplatform-markup-extension"></a>Extensão de marcação OnPlatform

O `OnPlatform` extensão de marcação permite que você personalize a aparência da interface do usuário em uma base por plataforma. Ele fornece a mesma funcionalidade que o [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) e [ `On` ](xref:Xamarin.Forms.On) classes, mas com uma representação mais concisa.

O `OnPlatform` extensão de marcação dá suporte a [ `OnPlatformExtension` ](xref:Xamarin.Forms.Xaml.OnPlatformExtension) classe, que define as propriedades a seguir:

- `Default` do tipo `object`, que você defina como um valor padrão a ser aplicado às propriedades que representam as plataformas.
- `Android` do tipo `object`, que é definido como um valor a ser aplicado no Android.
- `GTK` do tipo `object`, que é definido como um valor a ser aplicado em plataformas GTK.
- `iOS` do tipo `object`, que é definido como um valor a ser aplicado no iOS.
- `macOS` do tipo `object`, que é definido como um valor a ser aplicado no macOS.
- `Tizen` do tipo `object`, que é definido como um valor a ser aplicado na plataforma Tizen.
- `UWP` do tipo `object`, que é definido como um valor a ser aplicado na plataforma Universal do Windows.
- `WPF` do tipo `object`, que é definido como um valor a ser aplicado na plataforma Windows Presentation Foundation.
- `Converter` do tipo `IValueConverter`, que podem ser definidas um `IValueConverter` implementação.
- `ConverterParameter` do tipo `object`, que é definido como um valor para passar para o `IValueConverter` implementação.

> [!NOTE]
> O analisador XAML permite que o [ `OnPlatformExtension` ](xref:Xamarin.Forms.Xaml.OnPlatformExtension) classe deverá ser abreviado como `OnPlatform`.

O `Default` é a propriedade de conteúdo do `OnPlatformExtension`. Portanto, para expressões de marcação XAML expressadas com chaves, você pode eliminar o `Default=` faz parte da expressão fornecida que é o primeiro argumento.

> [!IMPORTANT]
> O analisador XAML espera que os valores do tipo correto serão fornecidos às propriedades consumindo o `OnPlatform` extensão de marcação. Se a conversão de tipo for necessário, o `OnPlatform` extensão de marcação tentará executá-lo usando os conversores de padrão fornecidos pelo xamarin. Forms. No entanto, há algumas conversões de tipo não podem ser executadas pelos conversores padrão e nesses casos o `Converter` propriedade deve ser definida como um `IValueConverter` implementação.

O **demonstração OnPlatform** página mostra como usar o `OnPlatform` extensão de marcação:

```xaml
<BoxView Color="{OnPlatform Yellow, iOS=Red, Android=Green, UWP=Blue}"
         WidthRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"  
         HeightRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"
         HorizontalOptions="Center" />
```

Neste exemplo, todos os três `OnPlatform` expressões usam a versão abreviada do `OnPlatformExtension` nome de classe. Os três `OnPlatform` conjunto de extensões de marcação a [ `Color` ](xref:Xamarin.Forms.BoxView.Color), [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest), e [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) propriedades do [ `BoxView` ](xref:Xamarin.Forms.BoxView) como valores diferentes em iOS, Android e UWP. As extensões de marcação também fornecem valores padrão para essas propriedades nas plataformas que não forem especificadas, eliminando o `Default=` faz parte da expressão. Observe que as propriedades de extensão de marcação que são definidas são separadas por vírgulas.

Este é o programa em execução:

[![Demonstração da plataforma](consuming-images/onplatformdemo-small.png "Demonstração da plataforma")](consuming-images/onplatformdemo-large.png#lightbox "Demonstração da plataforma")

<a name="onidiom" />

## <a name="onidiom-markup-extension"></a>Extensão de marcação OnIdiom

A `OnIdiom` extensão de marcação permite que você personalize a aparência da interface do usuário com base no idioma do dispositivo em que o aplicativo está sendo executado. Ele dá suporte a [ `OnIdiomExtension` ](xref:Xamarin.Forms.Xaml.OnIdiomExtension) classe, que define as propriedades a seguir:

- `Default` do tipo `object`, que você defina como um valor padrão a ser aplicado às propriedades que representam as expressões de dispositivo.
- `Phone` do tipo `object`, que é definido como um valor a ser aplicado em telefones.
- `Tablet` do tipo `object`, que é definido como um valor a ser aplicado em tablets.
- `Desktop` do tipo `object`, que é definido como um valor a ser aplicado em plataformas de desktop.
- `TV` do tipo `object`, que é definido como um valor a ser aplicado em plataformas de TV.
- `Watch` do tipo `object`, que é definido como um valor a ser aplicado em plataformas de inspeção.
- `Converter` do tipo `IValueConverter`, que podem ser definidas um `IValueConverter` implementação.
- `ConverterParameter` do tipo `object`, que é definido como um valor para passar para o `IValueConverter` implementação.

> [!NOTE]
> O analisador XAML permite que o [ `OnIdiomExtension` ](xref:Xamarin.Forms.Xaml.OnIdiomExtension) classe deverá ser abreviado como `OnIdiom`.

O `Default` é a propriedade de conteúdo do `OnIdiomExtension`. Portanto, para expressões de marcação XAML expressadas com chaves, você pode eliminar o `Default=` faz parte da expressão fornecida que é o primeiro argumento.

> [!IMPORTANT]
> O analisador XAML espera que os valores do tipo correto serão fornecidos às propriedades consumindo o `OnIdiom` extensão de marcação. Se a conversão de tipo for necessário, o `OnIdiom` extensão de marcação tentará executá-lo usando os conversores de padrão fornecidos pelo xamarin. Forms. No entanto, há algumas conversões de tipo não podem ser executadas pelos conversores padrão e nesses casos o `Converter` propriedade deve ser definida como um `IValueConverter` implementação.

O **demonstração OnIdiom** página mostra como usar o `OnIdiom` extensão de marcação:

```xaml
<BoxView Color="{OnIdiom Yellow, Phone=Red, Tablet=Green, Desktop=Blue}"
         WidthRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HeightRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HorizontalOptions="Center" />
```

Neste exemplo, todos os três `OnIdiom` expressões usam a versão abreviada do `OnIdiomExtension` nome de classe. Os três `OnIdiom` conjunto de extensões de marcação a [ `Color` ](xref:Xamarin.Forms.BoxView.Color), [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest), e [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) propriedades do [ `BoxView` ](xref:Xamarin.Forms.BoxView) como valores diferentes em telefone, tablet e expressões da área de trabalho. As extensões de marcação também fornecem valores padrão para essas propriedades em que as linguagens que não forem especificadas, eliminando o `Default=` faz parte da expressão. Observe que as propriedades de extensão de marcação que são definidas são separadas por vírgulas.

Este é o programa em execução:

[![Demonstração do Onidioma](consuming-images/onidiomdemo-small.png "Demonstração do Onidioma")](consuming-images/onidiomdemo-large.png#lightbox "Demonstração do onidioma")

## <a name="datatemplate-markup-extension"></a>Extensão de marcação DataTemplate

A `DataTemplate` extensão de marcação permite que você converta um tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)em um. Ela `DataTemplateExtension` tem suporte pela classe, que define uma `TypeName` Propriedade, do tipo `string`, que é definida como o nome do tipo a ser convertido em um `DataTemplate`. O `TypeName` é a propriedade de conteúdo do `DataTemplateExtension`. Portanto, para expressões de marcação XAML expressas com chaves, você pode eliminar a `TypeName=` parte da expressão.

> [!NOTE]
> O analisador XAML permite que `DataTemplateExtension` a classe seja abreviada como `DataTemplate`.

Um uso típico dessa extensão de marcação está em um aplicativo de Shell, conforme mostrado no exemplo a seguir:

```xaml
<ShellContent Title="Monkeys"
              Icon="monkey.png"
              ContentTemplate="{DataTemplate views:MonkeysPage}" />
```

Neste exemplo, `MonkeysPage` é convertido de um [`ContentPage`](xref:Xamarin.Forms.ContentPage) para um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), `ShellContent.ContentTemplate` que é definido como o valor da propriedade. Isso garante que `MonkeysPage` o seja criado somente quando a navegação na página ocorrer, em vez de na inicialização do aplicativo.

Para obter mais informações sobre aplicativos de Shell, consulte [shell do Xamarin. Forms](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="fontimage-markup-extension"></a>Extensão de marcação FontImage

A `FontImage` extensão de marcação permite exibir um ícone de fonte em qualquer modo de exibição que possa `ImageSource`exibir um. Ele fornece a mesma funcionalidade que a `FontImageSource` classe, mas com uma representação mais concisa.

A extensão de `FontImageExtension` marcaçãoésuportadapelaclasse,quedefine`FontImage` as seguintes propriedades:

- `FontFamily`do tipo `string`, a família de fontes à qual o ícone de fonte pertence.
- `Glyph`do tipo `string`, o valor do caractere Unicode do ícone de fonte.
- `Color`do tipo `Color`, a cor a ser usada ao exibir o ícone de fonte.
- `Size`do tipo `double`, o tamanho, em unidades independentes de dispositivo, do ícone de fonte renderizado.

> [!NOTE]
> O analisador XAML permite que `FontImageExtension` a classe seja abreviada como `FontImage`.

O `Glyph` é a propriedade de conteúdo do `FontImageExtension`. Portanto, para expressões de marcação XAML expressadas com chaves, você pode eliminar o `Glyph=` faz parte da expressão fornecida que é o primeiro argumento.

A página de **demonstração do FontImage** mostra como usar `FontImage` a extensão de marcação:

```xaml
<Image BackgroundColor="#D1D1D1"
       Source="{FontImage &#xf30c;, FontFamily={OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=44}" />
```

Neste exemplo, a versão abreviada do `FontImageExtension` nome da classe é usada para exibir um ícone do Xbox, da família de fontes Ionicons, em um. [`Image`](xref:Xamarin.Forms.Image) A expressão também usa a `OnPlatform` extensão de marcação para especificar `FontFamily` valores de propriedade diferentes no Ios e no Android. Além disso, a `Glyph=` parte da expressão é eliminada e as propriedades de extensão de marcação definidas são separadas por vírgulas. Observe que, embora o caractere Unicode para o ícone `\uf30c`seja, ele deve ser ignorado em XAML e, portanto `&#xf30c;`, se torna.

Este é o programa em execução:

[![Captura de tela da extensão de marcação FontImage](consuming-images/fontimagedemo.png "Demonstração do FontImage")](consuming-images/fontimagedemo-large.png#lightbox "Demonstração do FontImage")

Para obter informações sobre como exibir ícones de fonte especificando os dados de ícone `FontImageSource` de fonte em um objeto, consulte [exibir ícones de fonte](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons).

## <a name="define-your-own-markup-extensions"></a>Definir suas próprias extensões de marcação

Se você tiver encontrado uma necessidade de uma extensão de marcação XAML que não está disponível no xamarin. Forms, você pode [criar seu próprio](creating.md).

## <a name="related-links"></a>Links relacionados

- [Extensões de marcação (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Capítulo de extensões de marcação XAML do xamarin. Forms book](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Dicionários de recurso](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilos dinâmicos](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Shell do Xamarin. Forms](~/xamarin-forms/app-fundamentals/shell/index.md).
