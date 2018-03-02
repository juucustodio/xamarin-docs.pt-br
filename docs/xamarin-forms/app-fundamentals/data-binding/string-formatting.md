---
title: "Cadeia de caracteres de formatação"
description: "Usar associações de dados para formatar e exibir objetos como cadeias de caracteres"
ms.topic: article
ms.prod: xamarin
ms.assetid: 978C85B7-CB58-4483-A131-21B381A865E0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 3b54ed876857f0cd04d7a304ff05b9710fbd9e04
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="string-formatting"></a>Cadeia de caracteres de formatação

Às vezes, é conveniente usar associações de dados para exibir a representação de cadeia de caracteres de um objeto ou valor. Por exemplo, talvez você queira usar um `Label` para exibir o valor atual de um `Slider`. Em associação de dados, o `Slider` é a origem e o destino é o `Text` propriedade o `Label`.

Ao exibir cadeias de caracteres no código, a ferramenta mais eficiente é estático [ `String.Format` ](https://developer.xamarin.com/api/member/System.String.Format/p/System.String/System.Object/) método. A cadeia de caracteres de formatação inclui códigos específicos para vários tipos de objetos de formatação, e você pode incluir outro texto junto com os valores que está sendo formatado. Consulte o [tipos de formatação no .NET](/dotnet/standard/base-types/formatting-types/) artigo para obter mais informações sobre a formatação da cadeia de caracteres.

## <a name="the-stringformat-property"></a>A propriedade StringFormat

Esse recurso é transportado para associações de dados: você definir o [ `StringFormat` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingBase.StringFormat/) propriedade de `Binding` (ou o [ `StringFormat` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.BindingExtension.StringFormat/) propriedade o `Binding` extensão de marcação) para um cadeia de caracteres com um espaço reservado de formatação padrão do .NET:

```xaml
<Slider x:Name="slider" />
<Label Text="{Binding Source={x:Reference slider},
                      Path=Value,
                      StringFormat='The slider value is {0:F2}'}" />
```

Observe que a cadeia de caracteres de formatação é delimitada por caracteres de aspas simples (apóstrofo) para evitar o analisador XAML tratando as chaves como outra extensão de marcação XAML. Caso contrário, que de cadeia de caracteres sem o caractere de aspas simples é a mesma cadeia de caracteres que você usaria para exibir um valor de ponto flutuante em uma chamada para `String.Format`. Uma especificação de formatação de `F2` faz com que o valor a ser exibido com duas casas decimais.

O `StringFormat` propriedade só faz sentido quando a propriedade de destino é do tipo `string`, e o modo de associação é `OneWay` ou `TwoWay`. Para associações bidirecionais, o `StringFormat` só é aplicável para valores de passagem da origem para o destino.

Como você verá no próximo artigo sobre o [caminho de associação](binding-path.md), associações de dados podem se tornar muito complexos e complicada. Ao depurar essas associações de dados, você pode adicionar uma `Label` no arquivo XAML com um `StringFormat` para exibir alguns resultados intermediários. Mesmo se você usá-lo somente para exibir um tipo de objeto, que pode ser útil.

O **cadeia de caracteres de formatação** página ilustra vários usos da `StringFormat` propriedade:

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

As associações no `Slider` e `TimePicker` mostram o uso de especificações de formato particular para `double` e `TimeSpan` tipos de dados. O `StringFormat` que exibe o texto do `Entry` exibição demonstra como especificar as aspas duplas na cadeia de formatação com o uso do `&quot;` entidade HTML.

A próxima seção no arquivo XAML é uma `StackLayout` com um `BindingContext` definido como um `x:Static` extensão de marcação que faz referência estática `DateTime.Now` propriedade. A primeira associação não tem propriedades:

```xaml
<Label Text="{Binding}" />
```

Isso exibe apenas o `DateTime` valor o `BindingContext` com formatação padrão. A segunda associação exibe o `Ticks` propriedade `DateTime`, enquanto as outras duas associações exibem o `DateTime` com formatação específica. Observe que isso `StringFormat`:

```xaml
<Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
```

Se você precisar exibir chaves de chaves à direita ou esquerdas em sua cadeia de caracteres de formatação, basta use um par de-los.

Os últimos conjuntos de seção de `BindingContext` para o valor de `Math.PI` e exibe-o com a formatação padrão e dois tipos diferentes de formatação numérica.

Aqui está o programa em execução em todas as três plataformas:

[![Cadeia de caracteres de formatação](string-formatting-images/stringformatting-small.png "formatação da cadeia de caracteres")](string-formatting-images/stringformatting-large.png "formatação da cadeia de caracteres")

## <a name="viewmodels-and-string-formatting"></a>Cadeia de caracteres de formatação e ViewModels

Quando você estiver usando `Label` e `StringFormat` para exibir o valor de uma exibição que também é o destino de um ViewModel, você pode definir a associação do modo de exibição para o `Label` ou de ViewModel para o `Label`. Em geral, a segunda abordagem é recomendada porque ele verifica se as associações entre a exibição e ViewModel estão funcionando.

Essa abordagem é mostrada no **melhor seletor de cores** sample, que usa o mesmo ViewModel como o **seletor de cores simples** programa mostrado no [ **modo associação** ](binding-mode.md) artigo:

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

Agora, há três pares de `Slider` e `Label` elementos que estão associados à mesma fonte de propriedade no `HslColorViewModel` objeto. A única diferença é que `Label` tem um `StringFormat` propriedade para exibir cada `Slider` valor.

[![Seletor de cores melhor](string-formatting-images/bettercolorselector-small.png "melhor o seletor de cores")](string-formatting-images/bettercolorselector-large.png "melhor o seletor de cores")

Você deve estar se perguntando como você pode exibir valores RGB (vermelhos, verdes e azuis) em formato hexadecimal de dois dígitos tradicional. Os valores inteiros não são diretamente disponíveis no `Color` estrutura. Uma solução seria calcular valores de inteiro dos componentes de cor no ViewModel e expô-los como propriedades. Em seguida, você pode formatá-los usando o `X2` especificação de formatação.

Outra abordagem é mais geral: você pode escrever um *conversor de valor de associação* conforme descrito no artigo posterior [ **conversores de valor de associação**](converters.md).

O próximo artigo, no entanto, explora o [ **caminho de associação** ](binding-path.md) mais detalhadamente e mostram como você pode usar para fazer referência a subpropriedades e itens em coleções.


## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de associação de dados do catálogo de xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
