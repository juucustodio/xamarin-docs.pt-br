---
title: Consumo de extensões de marcação do XAML
description: Este artigo explica como usar as extensões de marcação XAML do Xamarin. Forms para melhorar o poder e a flexibilidade do XAML, permitindo que os atributos do elemento sejam definidos a partir de uma variedade de fontes.
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/21/2020
ms.openlocfilehash: 7f46bc84543a1838241923ab91809bd01c706f44
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517104"
---
# <a name="consuming-xaml-markup-extensions"></a>Consumo de extensões de marcação do XAML

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

As extensões de marcação XAML ajudam a melhorar o poder e a flexibilidade do XAML, permitindo que os atributos de elemento sejam definidos a partir de uma variedade de fontes. Várias extensões de marcação XAML fazem parte da especificação XAML 2009. Eles aparecem em arquivos XAML com o prefixo de `x` namespace personalizado e são comumente referidos com esse prefixo. Este artigo aborda as seguintes extensões de marcação:

- [`x:Static`](#static)– Propriedades, campos ou membros de enumeração de referência estática.
- [`x:Reference`](#reference)– referência de elementos nomeados na página.
- [`x:Type`](#type)– Defina um atributo para um `System.Type` objeto.
- [`x:Array`](#array)– Construa uma matriz de objetos de um tipo específico.
- [`x:Null`](#null)– Defina um atributo como um `null` valor.
- [`OnPlatform`](#onplatform)– Personalize a aparência da interface do usuário em uma base por plataforma.
- [`OnIdiom`](#onidiom)– Personalize a aparência da interface do usuário com base no idioma do dispositivo em que o aplicativo está sendo executado.
- [`DataTemplate`](#datatemplate-markup-extension)– Converte um tipo em um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate).
- [`FontImage`](#fontimage-markup-extension)– exibir um ícone de fonte em qualquer modo de exibição que `ImageSource`possa exibir um.
- [`OnAppTheme`](#onapptheme-markup-extension)– consuma um recurso com base no tema atual do sistema.

Extensões de marcação XAML adicionais têm suporte historicamente por outras implementações de XAML e também são suportadas pelo Xamarin. Forms. Eles são descritos mais completamente em outros artigos:

- `StaticResource`-objetos de referência de um dicionário de recursos, conforme descrito no artigo [**dicionários de recursos**](~/xamarin-forms/xaml/resource-dictionaries.md).
- `DynamicResource`-responder a alterações em objetos em um dicionário de recursos, conforme descrito no artigo [**estilos dinâmicos**](~/xamarin-forms/user-interface/styles/dynamic.md).
- `Binding`-Estabeleça um vínculo entre as propriedades de dois objetos, conforme descrito no artigo [**vinculação de dados**](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- `TemplateBinding`-executa a vinculação de dados de um modelo de controle, conforme discutido no artigo [**modelos de controle do Xamarin. Forms**](~/xamarin-forms/app-fundamentals/templates/control-template.md).
- `RelativeSource`– define a origem da associação em relação à posição do destino da associação, conforme discutido no artigo [associações relativas](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

O [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) layout usa a extensão [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression)de marcação personalizada. Essa extensão de marcação é descrita no artigo [**RelativeLayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md).

<a name="static" />

## <a name="xstatic-markup-extension"></a>Extensão de marcação x:Static

A `x:Static` [`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension) classe não dá suporte à extensão de marcação. A classe tem uma única propriedade chamada [`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member) do tipo `string` que você define para o nome de uma constante pública, propriedade estática, campo estático ou membro de enumeração.

Uma maneira comum de usar `x:Static` é definir primeiro uma classe com algumas constantes ou variáveis estáticas, como essa pequena `AppConstants` classe no programa [**MarkupExtensions**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions) :

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

A página de **demonstração de x:static** demonstra várias maneiras de `x:Static` usar a extensão de marcação. A abordagem mais detalhada instancia a classe `StaticExtension` entre `Label.FontSize` as marcas de elemento de propriedade:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
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

O analisador XAML também permite que `StaticExtension` a classe seja abreviada como `x:Static`:

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

Isso pode ser simplificado ainda mais, mas a alteração apresenta alguma nova sintaxe: ela consiste em colocar `StaticExtension` a classe e a configuração do membro entre chaves. A expressão resultante é definida diretamente para o `FontSize` atributo:

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

Observe *que não há aspas entre* chaves. A `Member` propriedade de `StaticExtension` não é mais um atributo XML. Em vez disso, ele faz parte da expressão para a extensão de marcação.

Assim como você pode abreviar `x:StaticExtension` `x:Static` ao usá-lo como um elemento Object, você também pode abreviar isso na expressão entre chaves:

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

A `StaticExtension` classe tem um `ContentProperty` atributo que faz referência `Member`à propriedade, que marca essa propriedade como a propriedade de conteúdo padrão da classe. Para extensões de marcação XAML expressas com chaves, você pode eliminar a `Member=` parte da expressão:

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

Essa é a forma mais comum da extensão `x:Static` de marcação.

A página de **demonstração estática** contém dois outros exemplos. A marca raiz do arquivo XAML contém uma declaração de namespace XML para o namespace `System` .net:

```xaml
xmlns:sys="clr-namespace:System;assembly=netstandard"
```

Isso permite que `Label` o tamanho da fonte seja definido como o campo `Math.PI`estático. Isso resulta em um texto pequeno e, portanto `Scale` , a propriedade é `Math.E`definida como:

```xaml
<Label Text="&#x03C0; &#x00D7; E sized text"
       FontSize="{x:Static sys:Math.PI}"
       Scale="{x:Static sys:Math.E}"
       HorizontalOptions="Center" />
```

O exemplo final exibe o `Device.RuntimePlatform` valor. A `Environment.NewLine` propriedade estática é usada para inserir um caractere de nova linha entre os dois `Span` objetos:

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

Este é o exemplo em execução:

[![Demonstração de x:Static](consuming-images/staticdemo-small.png "Demonstração de x:Static")](consuming-images/staticdemo-large.png#lightbox "Demonstração de x:Static")

<a name="reference" />

## <a name="xreference-markup-extension"></a>extensão de marcação x:Reference

A `x:Reference` [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) classe não dá suporte à extensão de marcação. A classe tem uma única propriedade chamada [`Name`](xref:Xamarin.Forms.Xaml.ReferenceExtension.Name) do tipo `string` que você define como o nome de um elemento na página com um nome com `x:Name`. Essa `Name` propriedade é a propriedade de conteúdo `ReferenceExtension`de, `Name=` portanto, não é `x:Reference` necessária quando aparece entre chaves.

A `x:Reference` extensão de marcação é usada exclusivamente com associações de dados, que são descritas mais detalhadamente no artigo [**vinculação de dados**](~/xamarin-forms/app-fundamentals/data-binding/index.md).

A página de **demonstração do x:Reference** mostra dois `x:Reference` usos de com associações de dados, o primeiro em que é usado para `Source` definir a propriedade `Binding` do objeto e o segundo em que é usado para definir a `BindingContext` propriedade para duas associações de dados:

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

As `x:Reference` duas expressões usam a versão abreviada do `ReferenceExtension` nome da classe e eliminam a `Name=` parte da expressão. No primeiro exemplo, a extensão `x:Reference` de marcação é inserida na `Binding` extensão de marcação. Observe que as `Source` configurações `StringFormat` e são separadas por vírgulas. Este é o programa em execução:

[![Demonstração do x:Reference](consuming-images/referencedemo-small.png "Demonstração do x:Reference")](consuming-images/referencedemo-large.png#lightbox "Demonstração do x:Reference")

<a name="type" />

## <a name="xtype-markup-extension"></a>Extensão de marcação x:Type

A `x:Type` extensão de marcação é o equivalente XAML da palavra [`typeof`](/dotnet/csharp/language-reference/keywords/typeof/) -chave C#. Ela é suportada [`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension) pela classe, que define uma propriedade [`TypeName`](xref:Xamarin.Forms.Xaml.TypeExtension.TypeName) chamada do `string` tipo que é definida como um nome de classe ou estrutura. A `x:Type` extensão de marcação retorna [`System.Type`](xref:System.Type) o objeto dessa classe ou estrutura. `TypeName`é a propriedade Content de `TypeExtension`, portanto `TypeName=` , não é necessário `x:Type` quando aparece com chaves.

No Xamarin. Forms, há várias propriedades que têm argumentos do tipo `Type`. Os exemplos incluem [`TargetType`](xref:Xamarin.Forms.Style.TargetType) a propriedade `Style`de e o atributo [x:TypeArguments](~/xamarin-forms/xaml/passing-arguments.md#generic_type_arguments) usado para especificar argumentos em classes genéricas. No entanto, o analisador XAML `typeof` executa a operação automaticamente e `x:Type` a extensão de marcação não é usada nesses casos.

Um local onde `x:Type` *é* necessário é com a `x:Array` extensão de marcação, que é descrita na [próxima seção](#array).

A `x:Type` extensão de marcação também é útil ao construir um menu em que cada item de menu corresponde a um objeto de um tipo específico. Você pode associar um `Type` objeto a cada item de menu e, em seguida, instanciar o objeto quando o item de menu for selecionado.

É assim que o menu de navegação `MainPage` no programa de **extensões de marcação** funciona. O arquivo **MainPage. XAML** contém um `TableView` com cada `TextCell` um correspondente a uma página específica no programa:

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

[![Página Principal](consuming-images/mainpage-small.png "Página Principal")](consuming-images/mainpage-large.png#lightbox "Página Principal")

Cada `CommandParameter` propriedade é definida como uma `x:Type` extensão de marcação que faz referência a uma das outras páginas. A `Command` Propriedade está associada a uma propriedade chamada `NavigateCommand`. Essa propriedade é definida no arquivo `MainPage` code-behind:

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

A `NavigateCommand` propriedade é um `Command` objeto que implementa um comando execute com um argumento do tipo `Type` &mdash; o valor de `CommandParameter`. O método usa `Activator.CreateInstance` para instanciar a página e, em seguida, navega até ela. O Construtor conclui definindo a `BindingContext` da página para si mesmo, o que permite que o `Binding` no `Command` funcione. Consulte o artigo [**ligação de dados**](~/xamarin-forms/app-fundamentals/data-binding/index.md) e, particularmente, o artigo de [**comando**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) para obter mais detalhes sobre esse tipo de código.

A página de **demonstração de x:Type** usa uma técnica semelhante para instanciar elementos Xamarin. Forms e adicioná- `StackLayout`los a um. Inicialmente `Binding` , o arquivo XAML consiste em `Button` três elementos com `Command` suas propriedades definidas como a e `CommandParameter` as propriedades definidas como tipos de três exibições Xamarin. Forms:

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

O arquivo code-behind define e inicializa a `CreateCommand` Propriedade:

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

O método executado quando um `Button` é pressionado cria uma nova instância do argumento, define sua `VerticalOptions` Propriedade e o `StackLayout`adiciona ao. Os três `Button` elementos compartilham a página com exibições criadas dinamicamente:

[![Demonstração de x:Type](consuming-images/typedemo-small.png "Demonstração de x:Type")](consuming-images/typedemo-large.png#lightbox "Demonstração de x:Type")

<a name="array" />

## <a name="xarray-markup-extension"></a>Extensão de marcação x:Array

A `x:Array` extensão de marcação permite que você defina uma matriz na marcação. Ela é suportada [`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension) pela classe, que define duas propriedades:

- `Type`do tipo `Type`, que indica o tipo dos elementos na matriz.
- `Items`do tipo `IList`, que é uma coleção dos próprios itens. Essa é a propriedade de conteúdo `ArrayExtension`de.

A `x:Array` extensão de marcação em si nunca aparece entre chaves. Em vez `x:Array` disso, marcas de início e fim delimitam a lista de itens. Defina a `Type` Propriedade como uma `x:Type` extensão de marcação.

A página de **demonstração do x:array** mostra como `x:Array` usar o para adicionar itens `ListView` a um definindo `ItemsSource` a propriedade para uma matriz:

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

O `ViewCell` cria uma entrada `BoxView` simples para cada cor:

[![Demonstração do x:Array](consuming-images/arraydemo-small.png "Demonstração do x:Array")](consuming-images/arraydemo-large.png#lightbox "Demonstração do x:Array")

Há várias maneiras de especificar os itens individuais `Color` nessa matriz. Você pode usar uma `x:Static` extensão de marcação:

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

Ao definir matrizes de tipos comuns como cadeias de caracteres ou números, use as marcas listadas no artigo [**passando argumentos do Construtor**](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) para delimitar os valores.

<a name="null" />

## <a name="xnull-markup-extension"></a>Extensão de marcação x:Null

A `x:Null` [`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension) classe não dá suporte à extensão de marcação. Ele não tem propriedades e é simplesmente o equivalente XAML da palavra- [`null`](/dotnet/csharp/language-reference/keywords/null/) chave C#.

A `x:Null` extensão de marcação raramente é necessária e raramente usada, mas se você achar que precisa, você ficará feliz por existir.

A página de **demonstração do x:NULL** ilustra um `x:Null` cenário quando pode ser conveniente. Suponha que você defina um implícito `Style` para `Label` que inclua um `Setter` que defina a `FontFamily` Propriedade como um nome de família dependente de plataforma:

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

Em seguida, você descobre que, para `Label` um dos elementos, você deseja todas as configurações de propriedade `Style` no implícito, `FontFamily`exceto para o, que você deseja que seja o valor padrão. Você pode definir outro `Style` para essa finalidade, mas uma abordagem mais simples é simplesmente definir a `FontFamily` Propriedade do específico `Label` para `x:Null`, conforme demonstrado no centro. `Label`

Este é o programa em execução:

[![Demonstração do x:Null](consuming-images/nulldemo-small.png "Demonstração do x:Null")](consuming-images/nulldemo-large.png#lightbox "Demonstração do x:Null")

Observe que quatro dos `Label` elementos têm uma fonte serif, mas o centro `Label` tem a fonte de Sans-Serif padrão.

<a name="onplatform" />

## <a name="onplatform-markup-extension"></a>Extensão de marcação onplatform

A extensão de marcação `OnPlatform` permite que você personalize a aparência da interface do usuário por plataforma. Ele fornece a mesma funcionalidade das [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) classes e [`On`](xref:Xamarin.Forms.On) , mas com uma representação mais concisa.

A `OnPlatform` extensão de marcação é suportada pela [`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension) classe, que define as seguintes propriedades:

- `Default`do tipo `object`, que você define como um valor padrão a ser aplicado às propriedades que representam plataformas.
- `Android`do tipo `object`, que você define para um valor a ser aplicado no Android.
- `GTK`do tipo `object`, que você define para um valor a ser aplicado em plataformas GTK.
- `iOS`do tipo `object`, que você define para um valor a ser aplicado no Ios.
- `macOS`do tipo `object`, que você define para um valor a ser aplicado no MacOS.
- `Tizen`do tipo `object`, que você define para um valor a ser aplicado na plataforma tizen.
- `UWP`do tipo `object`, que você define para um valor a ser aplicado no plataforma universal do Windows.
- `WPF`do tipo `object`, que você define para um valor a ser aplicado na plataforma Windows Presentation Foundation.
- `Converter`do tipo `IValueConverter`, que pode ser definido como uma `IValueConverter` implementação.
- `ConverterParameter`do tipo `object`, que pode ser definido como um valor a ser passado para `IValueConverter` a implementação.

> [!NOTE]
> O analisador XAML permite que [`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension) a classe seja abreviada como `OnPlatform`.

A `Default` propriedade é a propriedade de conteúdo `OnPlatformExtension`de. Portanto, para expressões de marcação XAML expressas com chaves, você pode eliminar a `Default=` parte da expressão desde que ela seja o primeiro argumento. Se a `Default` propriedade não estiver definida, ela usará como [`BindableProperty.DefaultValue`](xref:Xamarin.Forms.BindableProperty.DefaultValue) padrão o valor da propriedade, desde que a extensão de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty)marcação esteja direcionando para um.

> [!IMPORTANT]
> O analisador XAML espera que os valores do tipo correto serão fornecidos às propriedades que consomem a extensão de `OnPlatform` marcação. Se a conversão de tipo for necessária `OnPlatform` , a extensão de marcação tentará executá-la usando os conversores padrão fornecidos pelo Xamarin. Forms. No entanto, há algumas conversões de tipo que não podem ser executadas pelos conversores padrão e, nesses casos `Converter` , a propriedade deve ser definida `IValueConverter` como uma implementação.

A página de **demonstração da onplatform** mostra como usar `OnPlatform` a extensão de marcação:

```xaml
<BoxView Color="{OnPlatform Yellow, iOS=Red, Android=Green, UWP=Blue}"
         WidthRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"  
         HeightRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"
         HorizontalOptions="Center" />
```

Neste exemplo, todas as três `OnPlatform` expressões usam a versão abreviada do nome `OnPlatformExtension` da classe. As três `OnPlatform` [`Color`](xref:Xamarin.Forms.BoxView.Color)extensões de marcação definem [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)as propriedades [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) , e de [`BoxView`](xref:Xamarin.Forms.BoxView) para valores diferentes em Ios, Android e UWP. As extensões de marcação também fornecem valores padrão para essas propriedades nas plataformas que não são especificadas, ao mesmo `Default=` tempo que elimina a parte da expressão. Observe que as propriedades de extensão de marcação definidas são separadas por vírgulas.

Este é o programa em execução:

[![Demonstração da plataforma](consuming-images/onplatformdemo-small.png "Demonstração da plataforma")](consuming-images/onplatformdemo-large.png#lightbox "Demonstração da plataforma")

<a name="onidiom" />

## <a name="onidiom-markup-extension"></a>Extensão de marcação onidioma

A `OnIdiom` extensão de marcação permite que você personalize a aparência da interface do usuário com base no idioma do dispositivo em que o aplicativo está sendo executado. Ele tem suporte da [`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension) classe, que define as seguintes propriedades:

- `Default`do tipo `object`, que você define como um valor padrão a ser aplicado às propriedades que representam os idiomas do dispositivo.
- `Phone`do tipo `object`, que você define para um valor a ser aplicado em telefones.
- `Tablet`do tipo `object`, que você define para um valor a ser aplicado em Tablets.
- `Desktop`do tipo `object`, que você define para um valor a ser aplicado em plataformas de desktop.
- `TV`do tipo `object`, que você define para um valor a ser aplicado em plataformas de TV.
- `Watch`do tipo `object`, que você define para um valor a ser aplicado em plataformas de inspeção.
- `Converter`do tipo `IValueConverter`, que pode ser definido como uma `IValueConverter` implementação.
- `ConverterParameter`do tipo `object`, que pode ser definido como um valor a ser passado para `IValueConverter` a implementação.

> [!NOTE]
> O analisador XAML permite que [`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension) a classe seja abreviada como `OnIdiom`.

A `Default` propriedade é a propriedade de conteúdo `OnIdiomExtension`de. Portanto, para expressões de marcação XAML expressas com chaves, você pode eliminar a `Default=` parte da expressão desde que ela seja o primeiro argumento.

> [!IMPORTANT]
> O analisador XAML espera que os valores do tipo correto serão fornecidos às propriedades que consomem a extensão de `OnIdiom` marcação. Se a conversão de tipo for necessária `OnIdiom` , a extensão de marcação tentará executá-la usando os conversores padrão fornecidos pelo Xamarin. Forms. No entanto, há algumas conversões de tipo que não podem ser executadas pelos conversores padrão e, nesses casos `Converter` , a propriedade deve ser definida `IValueConverter` como uma implementação.

A página de **demonstração do onidioma** mostra como usar `OnIdiom` a extensão de marcação:

```xaml
<BoxView Color="{OnIdiom Yellow, Phone=Red, Tablet=Green, Desktop=Blue}"
         WidthRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HeightRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HorizontalOptions="Center" />
```

Neste exemplo, todas as três `OnIdiom` expressões usam a versão abreviada do nome `OnIdiomExtension` da classe. As três `OnIdiom` [`Color`](xref:Xamarin.Forms.BoxView.Color)extensões de marcação definem [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)as propriedades [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) , e de [`BoxView`](xref:Xamarin.Forms.BoxView) para valores diferentes nos idiomas de telefone, Tablet e área de trabalho. As extensões de marcação também fornecem valores padrão para essas propriedades nos idiomas que não são especificados, ao mesmo tempo `Default=` que elimina a parte da expressão. Observe que as propriedades de extensão de marcação definidas são separadas por vírgulas.

Este é o programa em execução:

[![Demonstração do onidioma](consuming-images/onidiomdemo-small.png "Demonstração do onidioma")](consuming-images/onidiomdemo-large.png#lightbox "Demonstração do onidioma")

## <a name="datatemplate-markup-extension"></a>Extensão de marcação DataTemplate

A `DataTemplate` extensão de marcação permite que você converta um tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)em um. Ela tem `DataTemplateExtension` suporte pela classe, que define uma `TypeName` Propriedade, do tipo `string`, que é definida como o nome do tipo a ser convertido em um. `DataTemplate` A `TypeName` propriedade é a propriedade de conteúdo `DataTemplateExtension`de. Portanto, para expressões de marcação XAML expressas com chaves, você pode eliminar a `TypeName=` parte da expressão.

> [!NOTE]
> O analisador XAML permite que `DataTemplateExtension` a classe seja abreviada como `DataTemplate`.

Um uso típico dessa extensão de marcação está em um aplicativo de Shell, conforme mostrado no exemplo a seguir:

```xaml
<ShellContent Title="Monkeys"
              Icon="monkey.png"
              ContentTemplate="{DataTemplate views:MonkeysPage}" />
```

Neste `MonkeysPage` exemplo, é convertido de [`ContentPage`](xref:Xamarin.Forms.ContentPage) um para um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que é definido como o valor da `ShellContent.ContentTemplate` propriedade. Isso garante que `MonkeysPage` o seja criado somente quando a navegação na página ocorrer, em vez de na inicialização do aplicativo.

Para obter mais informações sobre aplicativos de Shell, consulte [shell do Xamarin. Forms](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="fontimage-markup-extension"></a>Extensão de marcação FontImage

A `FontImage` extensão de marcação permite exibir um ícone de fonte em qualquer modo de exibição que possa `ImageSource`exibir um. Ele fornece a mesma funcionalidade que a `FontImageSource` classe, mas com uma representação mais concisa.

A `FontImage` extensão de marcação é suportada pela `FontImageExtension` classe, que define as seguintes propriedades:

- `FontFamily`do tipo `string`, a família de fontes à qual o ícone de fonte pertence.
- `Glyph`do tipo `string`, o valor do caractere Unicode do ícone de fonte.
- `Color`do tipo [`Color`](xref:Xamarin.Forms.Color), a cor a ser usada ao exibir o ícone de fonte.
- `Size`do tipo `double`, o tamanho, em unidades independentes de dispositivo, do ícone de fonte renderizado. O valor padrão é 30. Além disso, essa propriedade pode ser definida como um tamanho de fonte nomeado.

> [!NOTE]
> O analisador XAML permite que `FontImageExtension` a classe seja abreviada como `FontImage`.

A `Glyph` propriedade é a propriedade de conteúdo `FontImageExtension`de. Portanto, para expressões de marcação XAML expressas com chaves, você pode eliminar a `Glyph=` parte da expressão desde que ela seja o primeiro argumento.

A página de **demonstração do FontImage** mostra como usar `FontImage` a extensão de marcação:

```xaml
<Image BackgroundColor="#D1D1D1"
       Source="{FontImage &#xf30c;, FontFamily={OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=44}" />
```

Neste exemplo, a versão abreviada do nome da `FontImageExtension` classe é usada para exibir um ícone do Xbox, da família de fontes Ionicons, em um [`Image`](xref:Xamarin.Forms.Image). A expressão também usa a `OnPlatform` extensão de marcação para especificar `FontFamily` valores de propriedade diferentes no Ios e no Android. Além disso, a `Glyph=` parte da expressão é eliminada e as propriedades de extensão de marcação definidas são separadas por vírgulas. Observe que, embora o caractere Unicode para o ícone `\uf30c`seja, ele deve ser ignorado em XAML e, portanto `&#xf30c;`, se torna.

Este é o programa em execução:

[![Captura de tela da extensão de marcação FontImage](consuming-images/fontimagedemo.png "Demonstração do FontImage")](consuming-images/fontimagedemo-large.png#lightbox "Demonstração do FontImage")

Para obter informações sobre como exibir ícones de fonte especificando os dados de ícone `FontImageSource` de fonte em um objeto, consulte [exibir ícones de fonte](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons).

## <a name="onapptheme-markup-extension"></a>Extensão de marcação OnAppTheme

A `OnAppTheme` extensão de marcação permite que você especifique um recurso a ser consumido, como uma imagem ou cor, com base no tema atual do sistema. Ele fornece a mesma funcionalidade que a `OnAppTheme<T>` classe, mas com uma representação mais concisa.

> [!IMPORTANT]
> A `OnAppTheme` extensão de marcação tem os requisitos mínimos do sistema operacional. Para obter mais informações, consulte [responder a alterações de tema do sistema em aplicativos Xamarin. Forms](~/xamarin-forms/user-interface/theming/system-theme-changes.md).

A `OnAppTheme` extensão de marcação é suportada pela `OnAppThemeExtension` classe, que define as seguintes propriedades:

- `Default`, do tipo `object`, que você define para o recurso a ser usado por padrão.
- `Light`, do tipo `object`, que você define para o recurso a ser usado quando o dispositivo estiver usando seu tema claro.
- `Dark`, do tipo `object`, que você define para o recurso a ser usado quando o dispositivo estiver usando seu tema escuro.
- `Value`, do tipo `object`, que retorna o recurso que está sendo usado atualmente pela extensão de marcação.
- `Converter`do tipo `IValueConverter`, que pode ser definido como uma `IValueConverter` implementação.
- `ConverterParameter`do tipo `object`, que pode ser definido como um valor a ser passado para `IValueConverter` a implementação.

> [!NOTE]
> O analisador XAML permite que `OnAppThemeExtension` a classe seja abreviada como `OnAppTheme`.

A `Default` propriedade é a propriedade de conteúdo `OnAppThemeExtension`de. Portanto, para expressões de marcação XAML expressas com chaves, você pode eliminar a `Default=` parte da expressão desde que ela seja o primeiro argumento.

A página de **demonstração do OnAppTheme** mostra como usar `OnAppTheme` a extensão de marcação:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="MarkupExtensions.OnAppThemeDemoPage"
             Title="OnAppTheme Demo">
    <ContentPage.Resources>

        <Style x:Key="labelStyle"
               TargetType="Label">
            <Setter Property="TextColor"
                    Value="{OnAppTheme Black, Light=Blue, Dark=Teal}" />
        </Style>

    </ContentPage.Resources>
    <StackLayout Margin="20">
        <Label Text="This text is green in light mode, and red in dark mode."
               TextColor="{OnAppTheme Light=Green, Dark=Red}" />
        <Label Text="This text is black by default, blue in light mode, and teal in dark mode."
               Style="{DynamicResource labelStyle}" />
    </StackLayout>
</ContentPage>
```

Neste exemplo, a cor do texto do primeiro [`Label`](xref:Xamarin.Forms.Label) é definida como verde quando o dispositivo está usando seu tema claro e é definido como vermelho quando o dispositivo está usando seu tema escuro. O segundo `Label` tem sua [`TextColor`](xref:Xamarin.Forms.Label.TextColor) propriedade definida por meio [`Style`](xref:Xamarin.Forms.Style)de um. Isso `Style` define a cor do texto de `Label` para preto por padrão, para azul quando o dispositivo estiver usando seu tema claro e para azul-petróleo quando o dispositivo estiver usando seu tema escuro.

> [!NOTE]
> Um [`Style`](xref:Xamarin.Forms.Style) que consome a extensão `OnAppTheme` de marcação deve ser aplicado a um controle com a `DynamicResource` extensão de marcação, para que a interface do usuário do aplicativo seja atualizada quando o tema do sistema for alterado.

Este é o programa em execução:

![Demonstração do OnAppTheme](consuming-images/onappthemedemo.png "Demonstração do OnAppTheme")

## <a name="define-markup-extensions"></a>Definir extensões de marcação

Se você tiver encontrado uma necessidade de uma extensão de marcação XAML que não esteja disponível no Xamarin. Forms, você poderá [criar a sua própria](creating.md).

## <a name="related-links"></a>Links relacionados

- [Extensões de marcação (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Capítulo extensões de marcação XAML do livro Xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Dicionários de recurso](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilos dinâmicos](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Shell do Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Responder a alterações de tema do sistema em aplicativos Xamarin. Forms](~/xamarin-forms/user-interface/theming/system-theme-changes.md)
