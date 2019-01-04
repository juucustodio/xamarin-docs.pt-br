---
title: Formatação de cadeia de caracteres do Xamarin.Forms
description: Este artigo explica como usar associações de dados do Xamarin.Forms para formatar e exibir objetos como cadeias de caracteres. Isso é feito com a definição de StringFormat de Binding como uma cadeia de caracteres de formatação padrão do .NET com um espaço reservado.
ms.prod: xamarin
ms.assetid: 978C85B7-CB58-4483-A131-21B381A865E0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 2dd7efb9f295143775961afb97e70b5f241d1337
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53056117"
---
# <a name="xamarinforms-string-formatting"></a>Formatação de cadeia de caracteres do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)

Às vezes, é conveniente usar associações de dados para exibir a representação de cadeia de caracteres de um objeto ou um valor. Por exemplo, talvez você deseje usar um `Label` para exibir o valor atual de um `Slider`. Nesta associação de dados, o `Slider` é a origem e o destino é a propriedade `Text` do `Label`.

Ao exibir cadeias de caracteres no código, a ferramenta mais avançada é o método estático [`String.Format`](xref:System.String.Format(System.String,System.Object)). A cadeia de caracteres de formatação inclui códigos de formatação para diversos tipos de objetos, sendo que você pode incluir outros textos junto com os valores que estão sendo formatados. Confira o artigo [Formatando tipos no .NET](/dotnet/standard/base-types/formatting-types/) para obter mais informações sobre a formatação da cadeia de caracteres.

## <a name="the-stringformat-property"></a>A propriedade StringFormat

Este recurso é levado para as associações de dados: Você define a propriedade [`StringFormat`](xref:Xamarin.Forms.BindingBase.StringFormat) de `Binding` (ou a propriedade [`StringFormat`](xref:Xamarin.Forms.Xaml.BindingExtension.StringFormat) da extensão de marcação `Binding`) como uma cadeia de caracteres de formatação padrão do .NET com um espaço reservado:

```xaml
<Slider x:Name="slider" />
<Label Text="{Binding Source={x:Reference slider},
                      Path=Value,
                      StringFormat='The slider value is {0:F2}'}" />
```

Observe que a cadeia de caracteres de formatação é delimitada por caracteres de aspas simples (apóstrofo) para ajudar o analisador de XAML a evitar tratar as chaves como outra extensão de marcação XAML. De outra forma, essa cadeia de caracteres sem o caractere de aspas simples é a mesma cadeia de caracteres usada para exibir um valor de ponto flutuante em uma chamada a `String.Format`. Uma especificação de formatação de `F2` faz com que o valor seja exibido com duas casas decimais.

A propriedade `StringFormat` só faz sentido quando a propriedade de destino é do tipo `string` e o modo de associação é `OneWay` ou `TwoWay`. Para vinculações bidirecionais, o `StringFormat` só é aplicável a valores que passam da origem para o destino.

Como você verá no próximo artigo sobre o [Caminho de associação](binding-path.md), as associações de dados podem se tornar muito complexas e complicadas. Ao depurar essas associações de dados, você pode adicionar um `Label` ao arquivo XAML com um `StringFormat` para exibir alguns resultados intermediários. Mesmo se você usá-lo somente para exibir o tipo de um objeto, isso pode ser útil.

A página **Formatação da cadeia de caracteres** ilustra vários usos da propriedade `StringFormat`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             x:Class="DataBindingDemos.StringFormattingPage"
             Title="String Formatting">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>

            <Style TargetType="BoxView">
                <Setter Property="Color" Value="Blue" />
                <Setter Property="HeightRequest" Value="2" />
                <Setter Property="Margin" Value="0, 5" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10">
        <Slider x:Name="slider" />
        <Label Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='The slider value is {0:F2}'}" />

        <BoxView />

        <TimePicker x:Name="timePicker" />
        <Label Text="{Binding Source={x:Reference timePicker},
                              Path=Time,
                              StringFormat='The TimeSpan is {0:c}'}" />

        <BoxView />

        <Entry x:Name="entry" />
        <Label Text="{Binding Source={x:Reference entry},
                              Path=Text,
                              StringFormat='The Entry text is &quot;{0}&quot;'}" />

        <BoxView />

        <StackLayout BindingContext="{x:Static sys:DateTime.Now}">
            <Label Text="{Binding}" />
            <Label Text="{Binding Path=Ticks,
                                  StringFormat='{0:N0} ticks since 1/1/1'}" />
            <Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
            <Label Text="{Binding StringFormat='The long date is {0:D}'}" />
        </StackLayout>

        <BoxView />

        <StackLayout BindingContext="{x:Static sys:Math.PI}">
            <Label Text="{Binding}" />
            <Label Text="{Binding StringFormat='PI to 4 decimal points = {0:F4}'}" />
            <Label Text="{Binding StringFormat='PI in scientific notation = {0:E7}'}" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

As associações no `Slider` e no `TimePicker` mostram o uso de especificações de formato específicas aos tipos de dados `double` e `TimeSpan`. O `StringFormat` que exibe o texto na exibição `Entry` demonstra como especificar aspas duplas na cadeia de caracteres de formatação com o uso da entidade HTML `&quot;`.

A próxima seção do arquivo XAML é um `StackLayout` com um `BindingContext` definido como uma extensão de marcação `x:Static` que referencia a propriedade estática `DateTime.Now`. A primeira associação não tem nenhuma propriedade:

```xaml
<Label Text="{Binding}" />
```

Isso apenas exibe o valor `DateTime` do `BindingContext` com a formatação padrão. A segunda associação exibe a propriedade `Ticks` de `DateTime`, enquanto as outras duas associações exibem o próprio `DateTime` com formatação específica. Observe este `StringFormat`:

```xaml
<Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
```

Caso precise exibir chaves direitas ou esquerdas na cadeia de caracteres de formatação, basta usar um par de chaves.

A última seção define o `BindingContext` com valor de `Math.PI` e o exibe com a formatação padrão e dois tipos diferentes de formatação numérica.

Este é o programa em execução:

[![Formatação de cadeia de caracteres](string-formatting-images/stringformatting-small.png "Formatação de cadeia de caracteres")](string-formatting-images/stringformatting-large.png#lightbox "Formatação de cadeia de caracteres")

## <a name="viewmodels-and-string-formatting"></a>ViewModels e formatação de cadeia de caracteres

Quando estiver usando `Label` e `StringFormat` para exibir o valor de uma exibição que também é o destino de um ViewModel, você poderá definir a associação na exibição como o `Label` ou no ViewModel como a `Label`. Em geral, a segunda abordagem é melhor porque verifica se as associações entre o View e o ViewModel estão funcionando.

Essa abordagem é apresentada na amostra **Melhor Seletor de Cor**, que usa o mesmo ViewModel do programa **Seletor de Cor Simples** mostrado no artigo [**Modo de associação**](binding-mode.md):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.BetterColorSelectorPage"
             Title="Better Color Selector">

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="HorizontalTextAlignment" Value="Center" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout>
        <StackLayout.BindingContext>
            <local:HslColorViewModel Color="Sienna" />
        </StackLayout.BindingContext>

        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <StackLayout Margin="10, 0">
            <Label Text="{Binding Name}" />

            <Slider Value="{Binding Hue}" />
            <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />

            <Slider Value="{Binding Saturation}" />
            <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />

            <Slider Value="{Binding Luminosity}" />
            <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
        </StackLayout>
    </StackLayout>
</ContentPage>    
```

Agora há três pares de elementos `Slider` e `Label` associados à mesma propriedade de origem no objeto `HslColorViewModel`. A única diferença é que `Label` tem uma propriedade `StringFormat` para exibir cada valor `Slider`.

[![Melhor Seletor de Cores](string-formatting-images/bettercolorselector-small.png "Melhor Seletor de Cores")](string-formatting-images/bettercolorselector-large.png#lightbox "Melhor Seletor de Cores")

Você deve estar se perguntando como poderá exibir valores RGB (vermelho, verde, azul) em formato hexadecimal tradicional de dois dígitos. Esses valores inteiros não estão diretamente disponíveis na estrutura `Color`. Uma solução é calcular valores inteiros dos componentes de cor dentro do ViewModel e expô-los como propriedades. Em seguida, você pode formatá-los usando a especificação de formatação `X2`.

Outra abordagem é mais geral: Você pode gravar um *conversor de valor de associação* como abordado no artigo posterior, [**Conversores de valor de associação**](converters.md).

No entanto, o próximo artigo explora o [**Caminho de associação**](binding-path.md) com mais detalhes e mostra como você pode usá-lo para referenciar subpropriedades e itens em coleções.


## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de associação de dados do catálogo de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
