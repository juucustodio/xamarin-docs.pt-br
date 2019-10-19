---
title: Consumo de extensões de marcação do XAML
description: Este artigo explica como usar as extensões de marcação XAML do Xamarin. Forms para melhorar o poder e a flexibilidade do XAML, permitindo que os atributos do elemento sejam definidos a partir de uma variedade de fontes.
ms.prod: xamarin
ms.assetid: CE686893-609C-4EC3-9225-6C68D2A9F79C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2019
ms.openlocfilehash: 03aaf471479a5113aade6bd3f34034afadfb538c
ms.sourcegitcommit: dad4dfcd194b63ec9e903363351b6d9e543d4888
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2019
ms.locfileid: "69887894"
---
# <a name="consuming-xaml-markup-extensions"></a>Consumo de extensões de marcação do XAML

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

As extensões de marcação XAML ajudam a melhorar o poder e a flexibilidade do XAML, permitindo que os atributos de elemento sejam definidos a partir de uma variedade de fontes. Várias extensões de marcação XAML fazem parte da especificação XAML 2009. Eles aparecem em arquivos XAML com o prefixo de namespace de `x` personalizado e são comumente referidos com esse prefixo. Este artigo aborda as seguintes extensões de marcação:

- [`x:Static`](#static) – Propriedades, campos ou membros de enumeração estáticos de referência.
- [`x:Reference`](#reference) – elementos nomeados de referência na página.
- [`x:Type`](#type) – defina um atributo para um objeto `System.Type`.
- [`x:Array`](#array) – Construa uma matriz de objetos de um tipo específico.
- [`x:Null`](#null) – defina um atributo para um valor de `null`.
- [`OnPlatform`](#onplatform) – Personalize a aparência da interface do usuário em uma base por plataforma.
- [`OnIdiom`](#onidiom) – Personalize a aparência da interface do usuário com base no idioma do dispositivo em que o aplicativo está sendo executado.
- [`DataTemplate`](#datatemplate-markup-extension) – converte um tipo em um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate).
- [`FontImage`](#fontimage-markup-extension) -exibir um ícone de fonte em qualquer modo de exibição que possa exibir um `ImageSource`.

Extensões de marcação XAML adicionais têm suporte historicamente por outras implementações de XAML e também são suportadas pelo Xamarin. Forms. Eles são descritos mais completamente em outros artigos:

- `StaticResource` &ndash; objetos de referência de um dicionário de recursos, conforme descrito no artigo [**dicionários de recursos**](~/xamarin-forms/xaml/resource-dictionaries.md).
- `DynamicResource` &ndash; responder a alterações em objetos em um dicionário de recursos, conforme descrito no artigo [**estilos dinâmicos**](~/xamarin-forms/user-interface/styles/dynamic.md).
- `Binding` &ndash; estabelecer um vínculo entre as propriedades de dois objetos, conforme descrito no artigo [**vinculação de dados**](~/xamarin-forms/app-fundamentals/data-binding/index.md).
- `TemplateBinding` &ndash; executa a vinculação de dados de um modelo de controle, conforme discutido no artigo [**Associação de um modelo de controle**](~/xamarin-forms/app-fundamentals/templates/control-templates/template-binding.md).

O layout de [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) usa a extensão de marcação personalizada [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression). Essa extensão de marcação é descrita no artigo [**RelativeLayout**](~/xamarin-forms/user-interface/layouts/relative-layout.md).

<a name="static" />

## <a name="xstatic-markup-extension"></a>Extensão de marcação x:Static

A extensão de marcação de `x:Static` é suportada pela classe [`StaticExtension`](xref:Xamarin.Forms.Xaml.StaticExtension) . A classe tem uma única propriedade chamada [`Member`](xref:Xamarin.Forms.Xaml.StaticExtension.Member) do tipo `string` que você define como o nome de uma constante pública, propriedade estática, campo estático ou membro de enumeração.

Uma maneira comum de usar `x:Static` é definir primeiro uma classe com algumas constantes ou variáveis estáticas, como essa pequena classe `AppConstants` no programa [**MarkupExtensions**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions) :

```csharp
static class AppConstants
{
    public static double NormalFontSize = 18;
}
```

A página de **demonstração x:static** demonstra várias maneiras de usar a extensão de marcação `x:Static`. A abordagem mais detalhada instancia a classe de `StaticExtension` entre `Label.FontSize` marcas de elemento de propriedade:

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

O analisador XAML também permite que a classe `StaticExtension` seja abreviada como `x:Static`:

```xaml
<Label Text="Label No. 2">
    <Label.FontSize>
        <x:Static Member="local:AppConstants.NormalFontSize" />
    </Label.FontSize>
</Label>
```

Isso pode ser simplificado ainda mais, mas a alteração introduz alguma nova sintaxe: ela consiste em colocar a classe `StaticExtension` e a configuração do membro entre chaves. A expressão resultante é definida diretamente para o atributo `FontSize`:

```xaml
<Label Text="Label No. 3"
       FontSize="{x:StaticExtension Member=local:AppConstants.NormalFontSize}" />
```

Observe *que não há aspas entre* chaves. A propriedade `Member` de `StaticExtension` não é mais um atributo XML. Em vez disso, ele faz parte da expressão para a extensão de marcação.

Assim como você pode abreviar `x:StaticExtension` para `x:Static` ao usá-lo como um elemento de objeto, você também pode abreviar isso na expressão entre chaves:

```xaml
<Label Text="Label No. 4"
       FontSize="{x:Static Member=local:AppConstants.NormalFontSize}" />
```

A classe `StaticExtension` tem um atributo `ContentProperty` referenciando a propriedade `Member`, que marca essa propriedade como a propriedade de conteúdo padrão da classe. Para extensões de marcação XAML expressas com chaves, você pode eliminar a `Member=` parte da expressão:

```xaml
<Label Text="Label No. 5"
       FontSize="{x:Static local:AppConstants.NormalFontSize}" />
```

Essa é a forma mais comum da extensão de marcação de `x:Static`.

A página de **demonstração estática** contém dois outros exemplos. A marca raiz do arquivo XAML contém uma declaração de namespace XML para o namespace .NET `System`:

```xaml
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

Isso permite que o tamanho da fonte de `Label` seja definido como o campo estático `Math.PI`. Isso resulta em um texto pequeno, portanto, a propriedade `Scale` é definida como `Math.E`:

```xaml
<Label Text="&#x03C0; &#x00D7; E sized text"
       FontSize="{x:Static sys:Math.PI}"
       Scale="{x:Static sys:Math.E}"
       HorizontalOptions="Center" />
```

O exemplo final exibe o valor `Device.RuntimePlatform`. A propriedade estática `Environment.NewLine` é usada para inserir um caractere de nova linha entre os dois objetos `Span`:

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

## <a name="xreference-markup-extension"></a>Extensão de marcação x:Reference

A extensão de marcação de `x:Reference` é suportada pela classe [`ReferenceExtension`](xref:Xamarin.Forms.Xaml.ReferenceExtension) . A classe tem uma única propriedade chamada [`Name`](xref:Xamarin.Forms.Xaml.ReferenceExtension.Name) do tipo `string` que você define como o nome de um elemento na página que recebeu um nome com `x:Name`. Essa propriedade de `Name` é a propriedade Content de `ReferenceExtension`, portanto, `Name=` não é necessário quando `x:Reference` aparece entre chaves.

A extensão de marcação de `x:Reference` é usada exclusivamente com associações de dados, que são descritas mais detalhadamente no artigo [**vinculação de dados**](~/xamarin-forms/app-fundamentals/data-binding/index.md).

A página de **demonstração do x:Reference** mostra dois usos de `x:Reference` com associações de dados, o primeiro em que é usado para definir a propriedade `Source` do objeto `Binding` e o segundo em que é usado para definir a propriedade `BindingContext` para duas associações de dados :

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

Ambas as expressões `x:Reference` usam a versão abreviada do nome da classe `ReferenceExtension` e eliminam a parte `Name=` da expressão. No primeiro exemplo, a extensão de marcação de `x:Reference` é inserida na extensão de marcação `Binding`. Observe que as configurações de `Source` e `StringFormat` são separadas por vírgulas. Este é o programa em execução:

[![Demonstração do x:Reference](consuming-images/referencedemo-small.png "Demonstração do x:Reference")](consuming-images/referencedemo-large.png#lightbox "Demonstração do x:Reference")

<a name="type" />

## <a name="xtype-markup-extension"></a>Extensão de marcação x:Type

A extensão de marcação de `x:Type` é o equivalente em C# XAML da palavra-chave [`typeof`](/dotnet/csharp/language-reference/keywords/typeof/) . Ele é compatível com a classe [`TypeExtension`](xref:Xamarin.Forms.Xaml.TypeExtension) , que define uma propriedade chamada [`TypeName`](xref:Xamarin.Forms.Xaml.TypeExtension.TypeName) do tipo `string` que é definido como um nome de classe ou estrutura. A extensão de marcação `x:Type` retorna o objeto [`System.Type`](xref:System.Type) dessa classe ou estrutura. `TypeName` é a propriedade Content de `TypeExtension`, portanto `TypeName=` não é necessário quando `x:Type` aparece com chaves.

No Xamarin. Forms, há várias propriedades que têm argumentos do tipo `Type`. Os exemplos incluem a propriedade [`TargetType`](xref:Xamarin.Forms.Style.TargetType) de `Style` e o atributo [x:TypeArguments](~/xamarin-forms/xaml/passing-arguments.md#generic_type_arguments) usado para especificar argumentos em classes genéricas. No entanto, o analisador XAML executa a operação de `typeof` automaticamente e a extensão de marcação `x:Type` não é usada nesses casos.

Um local em que `x:Type` *é* necessário é com a extensão de marcação `x:Array`, que é descrita na [próxima seção](#array).

A extensão de marcação de `x:Type` também é útil ao construir um menu em que cada item de menu corresponde a um objeto de um tipo específico. Você pode associar um objeto de `Type` a cada item de menu e, em seguida, instanciar o objeto quando o item de menu for selecionado.

É assim que o menu de navegação em `MainPage` no programa de **extensões de marcação** funciona. O arquivo **MainPage. XAML** contém um `TableView` com cada `TextCell` correspondente a uma página específica no programa:

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

Cada propriedade de `CommandParameter` é definida como uma extensão de marcação de `x:Type` que faz referência a uma das outras páginas. A propriedade `Command` está associada a uma propriedade chamada `NavigateCommand`. Essa propriedade é definida no arquivo de `MainPage` code-behind:

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

A propriedade `NavigateCommand` é um objeto `Command` que implementa um comando execute com um argumento do tipo `Type` &mdash; o valor de `CommandParameter`. O método usa `Activator.CreateInstance` para instanciar a página e, em seguida, navega até ela. O Construtor conclui definindo o `BindingContext` da página para si mesmo, o que permite que o `Binding` em `Command` funcione. Consulte o artigo [**ligação de dados**](~/xamarin-forms/app-fundamentals/data-binding/index.md) e, particularmente, o artigo de [**comando**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md) para obter mais detalhes sobre esse tipo de código.

A página de **demonstração de x:Type** usa uma técnica semelhante para instanciar elementos Xamarin. Forms e adicioná-los a um `StackLayout`. Inicialmente, o arquivo XAML consiste em três elementos `Button` com suas propriedades `Command` definidas como uma `Binding` e as propriedades `CommandParameter` definidas como tipos de três exibições Xamarin. Forms:

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

O arquivo code-behind define e inicializa a propriedade `CreateCommand`:

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

O método executado quando um `Button` é pressionado cria uma nova instância do argumento, define sua propriedade `VerticalOptions` e a adiciona ao `StackLayout`. Os três elementos `Button` compartilham a página com exibições criadas dinamicamente:

[![Demonstração de x:Type](consuming-images/typedemo-small.png "Demonstração de x:Type")](consuming-images/typedemo-large.png#lightbox "Demonstração de x:Type")

<a name="array" />

## <a name="xarray-markup-extension"></a>Extensão de marcação x:Array

A extensão de marcação `x:Array` permite que você defina uma matriz na marcação. Ele é compatível com a classe [`ArrayExtension`](xref:Xamarin.Forms.Xaml.ArrayExtension) , que define duas propriedades:

- `Type` do tipo `Type`, que indica o tipo dos elementos na matriz.
- `Items` do tipo `IList`, que é uma coleção dos próprios itens. Essa é a propriedade de conteúdo de `ArrayExtension`.

A extensão de marcação de `x:Array` em si nunca aparece entre chaves. Em vez disso, `x:Array` marca de início e fim delimitam a lista de itens. Defina a propriedade `Type` como uma extensão de marcação `x:Type`.

A página de **demonstração do x:array** mostra como usar `x:Array` para adicionar itens a um `ListView` Configurando a propriedade `ItemsSource` para uma matriz:

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

O `ViewCell` cria um `BoxView` simples para cada entrada de cor:

[![Demonstração do x:Array](consuming-images/arraydemo-small.png "Demonstração do x:Array")](consuming-images/arraydemo-large.png#lightbox "Demonstração do x:Array")

Há várias maneiras de especificar os itens de `Color` individuais nessa matriz. Você pode usar uma extensão de marcação de `x:Static`:

```xaml
<x:Static Member="Color.Blue" />
```

Ou você pode usar `StaticResource` para recuperar uma cor de um dicionário de recursos:

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

A extensão de marcação de `x:Null` é suportada pela classe [`NullExtension`](xref:Xamarin.Forms.Xaml.NullExtension) . Ele não tem propriedades e é simplesmente o equivalente em XAML da C# palavra-chave [`null`](/dotnet/csharp/language-reference/keywords/null/) .

A extensão de marcação de `x:Null` raramente é necessária e raramente usada, mas se você achar que precisa dela, ficará feliz por existir.

A página de **demonstração do x:NULL** ilustra um cenário quando `x:Null` pode ser conveniente. Suponha que você defina um `Style` implícito para `Label` que inclua uma `Setter` que defina a propriedade `FontFamily` como um nome de família dependente de plataforma:

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

Em seguida, você descobre que, para um dos elementos de `Label`, você deseja todas as configurações de propriedade no `Style` implícito, exceto para o `FontFamily`, que você deseja que seja o valor padrão. Você pode definir outro `Style` para essa finalidade, mas uma abordagem mais simples é simplesmente definir a propriedade `FontFamily` do `Label` específico como `x:Null`, conforme demonstrado na `Label` central.

Este é o programa em execução:

[![Demonstração do x:Null](consuming-images/nulldemo-small.png "Demonstração do x:Null")](consuming-images/nulldemo-large.png#lightbox "Demonstração do x:Null")

Observe que quatro dos elementos `Label` têm uma fonte serif, mas o `Label` Center tem a fonte sans-serif padrão.

<a name="onplatform" />

## <a name="onplatform-markup-extension"></a>Extensão de marcação onplatform

A extensão de marcação de `OnPlatform` permite que você personalize a aparência da interface do usuário por plataforma. Ele fornece a mesma funcionalidade que as classes [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) e [`On`](xref:Xamarin.Forms.On) , mas com uma representação mais concisa.

A extensão de marcação de `OnPlatform` é suportada pela classe [`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension) , que define as seguintes propriedades:

- `Default` do tipo `object`, que você define como um valor padrão a ser aplicado às propriedades que representam plataformas.
- `Android` do tipo `object`, que você define para um valor a ser aplicado no Android.
- `GTK` do tipo `object`, que você define para um valor a ser aplicado em plataformas GTK.
- `iOS` do tipo `object`, que você define para um valor a ser aplicado no iOS.
- `macOS` do tipo `object`, que você define para um valor a ser aplicado no macOS.
- `Tizen` do tipo `object`, que você define para um valor a ser aplicado na plataforma tizen.
- `UWP` do tipo `object`, que você define para um valor a ser aplicado no Plataforma Universal do Windows.
- `WPF` do tipo `object`, que você define para um valor a ser aplicado na plataforma Windows Presentation Foundation.
- `Converter` do tipo `IValueConverter`, que você define para uma implementação de `IValueConverter`.
- `ConverterParameter` do tipo `object`, que você define para um valor a ser passado para a implementação do `IValueConverter`.

> [!NOTE]
> O analisador XAML permite que a classe [`OnPlatformExtension`](xref:Xamarin.Forms.Xaml.OnPlatformExtension) seja abreviada como `OnPlatform`.

A propriedade `Default` é a propriedade Content de `OnPlatformExtension`. Portanto, para expressões de marcação XAML expressas com chaves, você pode eliminar a `Default=` parte da expressão desde que ela seja o primeiro argumento.

> [!IMPORTANT]
> O analisador XAML espera que os valores do tipo correto sejam fornecidos para propriedades que consomem a extensão de marcação `OnPlatform`. Se a conversão de tipo for necessária, a extensão de marcação `OnPlatform` tentará executá-la usando os conversores padrão fornecidos pelo Xamarin. Forms. No entanto, há algumas conversões de tipo que não podem ser executadas pelos conversores padrão e, nesses casos, a propriedade `Converter` deve ser definida como uma implementação de `IValueConverter`.

A página de **demonstração da onplatform** mostra como usar a extensão de marcação de `OnPlatform`:

```xaml
<BoxView Color="{OnPlatform Yellow, iOS=Red, Android=Green, UWP=Blue}"
         WidthRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"  
         HeightRequest="{OnPlatform 250, iOS=200, Android=300, UWP=400}"
         HorizontalOptions="Center" />
```

Neste exemplo, todas as três expressões `OnPlatform` usam a versão abreviada do nome da classe `OnPlatformExtension`. As três `OnPlatform` extensões de marcação definem as propriedades [`Color`](xref:Xamarin.Forms.BoxView.Color), [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) do [`BoxView`](xref:Xamarin.Forms.BoxView) com valores diferentes em Ios, Android e UWP. As extensões de marcação também fornecem valores padrão para essas propriedades nas plataformas que não são especificadas, ao mesmo tempo que elimina a `Default=` parte da expressão. Observe que as propriedades de extensão de marcação definidas são separadas por vírgulas.

Este é o programa em execução:

[![Demonstração da plataforma](consuming-images/onplatformdemo-small.png "Demonstração da plataforma")](consuming-images/onplatformdemo-large.png#lightbox "Demonstração da plataforma")

<a name="onidiom" />

## <a name="onidiom-markup-extension"></a>Extensão de marcação onidioma

A extensão de marcação de `OnIdiom` permite que você personalize a aparência da interface do usuário com base no idioma do dispositivo em que o aplicativo está sendo executado. Há suporte para a classe [`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension) , que define as seguintes propriedades:

- `Default` do tipo `object`, que você define como um valor padrão a ser aplicado às propriedades que representam os idiomas do dispositivo.
- `Phone` do tipo `object`, que você define para um valor a ser aplicado em telefones.
- `Tablet` do tipo `object`, que você define para um valor a ser aplicado nos tablets.
- `Desktop` do tipo `object`, que você define para um valor a ser aplicado em plataformas de desktop.
- `TV` do tipo `object`, que você define para um valor a ser aplicado em plataformas de TV.
- `Watch` do tipo `object`, que você define para um valor a ser aplicado em plataformas de inspeção.
- `Converter` do tipo `IValueConverter`, que você define para uma implementação de `IValueConverter`.
- `ConverterParameter` do tipo `object`, que você define para um valor a ser passado para a implementação do `IValueConverter`.

> [!NOTE]
> O analisador XAML permite que a classe [`OnIdiomExtension`](xref:Xamarin.Forms.Xaml.OnIdiomExtension) seja abreviada como `OnIdiom`.

A propriedade `Default` é a propriedade Content de `OnIdiomExtension`. Portanto, para expressões de marcação XAML expressas com chaves, você pode eliminar a `Default=` parte da expressão desde que ela seja o primeiro argumento.

> [!IMPORTANT]
> O analisador XAML espera que os valores do tipo correto sejam fornecidos para propriedades que consomem a extensão de marcação `OnIdiom`. Se a conversão de tipo for necessária, a extensão de marcação `OnIdiom` tentará executá-la usando os conversores padrão fornecidos pelo Xamarin. Forms. No entanto, há algumas conversões de tipo que não podem ser executadas pelos conversores padrão e, nesses casos, a propriedade `Converter` deve ser definida como uma implementação de `IValueConverter`.

A página de **demonstração do onidioma** mostra como usar a extensão de marcação de `OnIdiom`:

```xaml
<BoxView Color="{OnIdiom Yellow, Phone=Red, Tablet=Green, Desktop=Blue}"
         WidthRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HeightRequest="{OnIdiom 100, Phone=200, Tablet=300, Desktop=400}"
         HorizontalOptions="Center" />
```

Neste exemplo, todas as três expressões `OnIdiom` usam a versão abreviada do nome da classe `OnIdiomExtension`. As três `OnIdiom` extensões de marcação definem as propriedades [`Color`](xref:Xamarin.Forms.BoxView.Color), [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)e [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) do [`BoxView`](xref:Xamarin.Forms.BoxView) com valores diferentes nos idiomas de telefone, Tablet e área de trabalho. As extensões de marcação também fornecem valores padrão para essas propriedades nos idiomas que não são especificados, ao mesmo tempo que elimina a `Default=` parte da expressão. Observe que as propriedades de extensão de marcação definidas são separadas por vírgulas.

Este é o programa em execução:

[![Demonstração do onidioma](consuming-images/onidiomdemo-small.png "Demonstração do onidioma")](consuming-images/onidiomdemo-large.png#lightbox "Demonstração do onidioma")

## <a name="datatemplate-markup-extension"></a>Extensão de marcação DataTemplate

A extensão de marcação de `DataTemplate` permite converter um tipo em um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Há suporte para a classe `DataTemplateExtension`, que define uma propriedade `TypeName`, do tipo `string`, que é definida como o nome do tipo a ser convertido em um `DataTemplate`. A propriedade `TypeName` é a propriedade Content de `DataTemplateExtension`. Portanto, para expressões de marcação XAML expressas com chaves, você pode eliminar a `TypeName=` parte da expressão.

> [!NOTE]
> O analisador XAML permite que a classe `DataTemplateExtension` seja abreviada como `DataTemplate`.

Um uso típico dessa extensão de marcação está em um aplicativo de Shell, conforme mostrado no exemplo a seguir:

```xaml
<ShellContent Title="Monkeys"
              Icon="monkey.png"
              ContentTemplate="{DataTemplate views:MonkeysPage}" />
```

Neste exemplo, `MonkeysPage` é convertido de um [`ContentPage`](xref:Xamarin.Forms.ContentPage) para um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), que é definido como o valor da propriedade `ShellContent.ContentTemplate`. Isso garante que `MonkeysPage` seja criada somente quando a navegação na página ocorrer, em vez de na inicialização do aplicativo.

Para obter mais informações sobre aplicativos de Shell, consulte [shell do Xamarin. Forms](~/xamarin-forms/app-fundamentals/shell/index.md).

## <a name="fontimage-markup-extension"></a>Extensão de marcação FontImage

A extensão de marcação `FontImage` permite exibir um ícone de fonte em qualquer modo de exibição que possa exibir um `ImageSource`. Ele fornece a mesma funcionalidade que a classe `FontImageSource`, mas com uma representação mais concisa.

A extensão de marcação de `FontImage` é suportada pela classe `FontImageExtension`, que define as seguintes propriedades:

- `FontFamily` do tipo `string`, a família de fontes à qual o ícone de fonte pertence.
- `Glyph` do tipo `string`, o valor do caractere Unicode do ícone de fonte.
- `Color` do tipo `Color`, a cor a ser usada ao exibir o ícone de fonte.
- `Size` do tipo `double`, o tamanho, em unidades independentes de dispositivo, do ícone de fonte renderizado.

> [!NOTE]
> O analisador XAML permite que a classe `FontImageExtension` seja abreviada como `FontImage`.

A propriedade `Glyph` é a propriedade Content de `FontImageExtension`. Portanto, para expressões de marcação XAML expressas com chaves, você pode eliminar a `Glyph=` parte da expressão desde que ela seja o primeiro argumento.

A página de **demonstração do FontImage** mostra como usar a extensão de marcação de `FontImage`:

```xaml
<Image BackgroundColor="#D1D1D1"
       Source="{FontImage &#xf30c;, FontFamily={OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=44}" />
```

Neste exemplo, a versão abreviada do nome da classe `FontImageExtension` é usada para exibir um ícone do XBox, da família de fontes Ionicons, em um [`Image`](xref:Xamarin.Forms.Image). A expressão também usa a extensão de marcação `OnPlatform` para especificar diferentes valores de propriedade `FontFamily` no iOS e no Android. Além disso, a parte `Glyph=` da expressão é eliminada e as propriedades de extensão de marcação definidas são separadas por vírgulas. Observe que, embora o caractere Unicode para o ícone seja `\uf30c`, ele deve ser ignorado em XAML e, portanto, se torna `&#xf30c;`.

Este é o programa em execução:

[![Captura de tela da extensão de marcação FontImage](consuming-images/fontimagedemo.png "Demonstração do FontImage")](consuming-images/fontimagedemo-large.png#lightbox "Demonstração do FontImage")

Para obter informações sobre como exibir ícones de fonte especificando os dados de ícone de fonte em um objeto `FontImageSource`, consulte [exibir ícones de fonte](~/xamarin-forms/user-interface/text/fonts.md#display-font-icons).

## <a name="define-your-own-markup-extensions"></a>Definir suas próprias extensões de marcação

Se você tiver encontrado uma necessidade de uma extensão de marcação XAML que não esteja disponível no Xamarin. Forms, você poderá [criar a sua própria](creating.md).

## <a name="related-links"></a>Links relacionados

- [Extensões de marcação (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Capítulo extensões de marcação XAML do livro Xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Dicionários de recurso](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilos dinâmicos](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Shell do Xamarin. Forms](~/xamarin-forms/app-fundamentals/shell/index.md).
