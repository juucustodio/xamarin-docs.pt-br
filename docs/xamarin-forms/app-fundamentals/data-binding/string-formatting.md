---
title: Xamarin. Forms de cadeia de caracteres de formatação
description: Este artigo explica como usar associações de dados do xamarin. Forms para formatar e exibir objetos como cadeias de caracteres. Isso é feito definindo o StringFormat da associação para uma cadeia de formatação .NET standard com um espaço reservado.
ms.prod: xamarin
ms.assetid: 978C85B7-CB58-4483-A131-21B381A865E0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 8efd93204b848113e0ed95c8066a5506eb517ac6
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52170943"
---
# <a name="xamarinforms-string-formatting"></a>Xamarin. Forms de cadeia de caracteres de formatação

Às vezes é conveniente usar associações de dados para exibir a representação de cadeia de caracteres de um objeto ou valor. Por exemplo, você talvez queira usar um `Label` para exibir o valor atual de um `Slider`. Nesta associação de dados, o `Slider` é a origem e o destino é o `Text` propriedade do `Label`.

Ao exibir cadeias de caracteres no código, a ferramenta mais poderosa é estática [ `String.Format` ](xref:System.String.Format(System.String,System.Object)) método. A cadeia de caracteres de formatação que inclui códigos específicos para diversos tipos de objetos de formatação, e você pode incluir outro texto junto com os valores que está sendo formatado. Consulte a [tipos de formatação no .NET](/dotnet/standard/base-types/formatting-types/) artigo para obter mais informações sobre a formatação da cadeia de caracteres.

## <a name="the-stringformat-property"></a>A propriedade StringFormat

Esse recurso é transportado para associações de dados: você definir a [ `StringFormat` ](xref:Xamarin.Forms.BindingBase.StringFormat) propriedade do `Binding` (ou o [ `StringFormat` ](xref:Xamarin.Forms.Xaml.BindingExtension.StringFormat) propriedade do `Binding` extensão de marcação) para um formatação de cadeia de caracteres com um espaço reservado padrão do .NET:

```xaml
<Slider x:Name="slider" />
<Label Text="{Binding Source={x:Reference slider},
                      Path=Value,
                      StringFormat='The slider value is {0:F2}'}" />
```

Observe que a cadeia de caracteres de formatação é delimitada por caracteres de aspas simples (apóstrofo) para evitar o tratamento de chaves como outra extensão de marcação XAML o analisador XAML. Caso contrário, essa cadeia de caracteres sem o caractere de aspas simples é a mesma cadeia de caracteres que você usaria para exibir um valor de ponto flutuante em uma chamada para `String.Format`. Uma especificação de formatação de `F2` faz com que o valor a ser exibido com duas casas decimais.

O `StringFormat` propriedade só faz sentido quando a propriedade de destino é do tipo `string`, e é o modo de ligação `OneWay` ou `TwoWay`. Para associações bidirecionais, o `StringFormat` é aplicável somente para valores passando da origem ao destino.

Como você verá no próximo artigo sobre o [caminho de associação](binding-path.md), associações de dados podem se tornar muito complexos e complicados. Ao depurar essas associações de dados, você pode adicionar um `Label` no arquivo XAML com um `StringFormat` exibir algum resultado intermediário. Mesmo se você usá-lo somente para exibir o tipo de um objeto, que pode ser útil.

O **cadeia de caracteres de formatação** página ilustra vários usos do `StringFormat` propriedade:

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

As associações na `Slider` e `TimePicker` mostram o uso de especificações de formato específicas `double` e `TimeSpan` tipos de dados. O `StringFormat` que exibe o texto do `Entry` exibição demonstra como especificar as aspas duplas na cadeia de caracteres de formatação com o uso do `&quot;` entidade HTML.

A próxima seção no arquivo XAML é uma `StackLayout` com um `BindingContext` definido como um `x:Static` extensão de marcação que faz referência estática `DateTime.Now` propriedade. A primeira associação não tem nenhuma propriedade:

```xaml
<Label Text="{Binding}" />
```

Isso simplesmente exibe a `DateTime` valor da `BindingContext` com formatação padrão. A segunda associação exibe a `Ticks` propriedade de `DateTime`, enquanto as outras duas associações exibem o `DateTime` com formatação específica. Observe que isso `StringFormat`:

```xaml
<Label Text="{Binding StringFormat='The {{0:MMMM}} specifier produces {0:MMMM}'}" />
```

Se você precisar exibir chaves de chaves à direita ou esquerdas em sua cadeia de caracteres de formatação, basta use um par de-los.

Os últimos conjuntos de seção de `BindingContext` ao valor de `Math.PI` e o exibe com a formatação padrão e dois tipos diferentes de formatação numérica.

Aqui está o programa em execução:

[![Formatação de cadeia de caracteres](string-formatting-images/stringformatting-small.png "formatação de cadeia de caracteres")](string-formatting-images/stringformatting-large.png#lightbox "formatação de cadeia de caracteres")

## <a name="viewmodels-and-string-formatting"></a>Cadeia de caracteres de formatação e ViewModels

Quando você estiver usando `Label` e `StringFormat` para exibir o valor de uma exibição que também é o destino de um ViewModel, você pode definir a associação do modo de exibição para o `Label` ou do ViewModel para a `Label`. Em geral, a segunda abordagem é melhor porque verifica se as associações entre o View e ViewModel estão funcionando.

Essa abordagem é mostrada na **melhor seletor de cor** exemplo, que usa o mesmo ViewModel como o **seletor de cor simples** programa mostrado na [ **modo associação** ](binding-mode.md) artigo:

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

Agora há três pares de `Slider` e `Label` elementos que são associados à mesma propriedade em da fonte a `HslColorViewModel` objeto. A única diferença é que `Label` tem uma `StringFormat` propriedade para exibir cada `Slider` valor.

[![Seletor de cores melhor](string-formatting-images/bettercolorselector-small.png "melhor o seletor de cores")](string-formatting-images/bettercolorselector-large.png#lightbox "melhor o seletor de cores")

Você deve estar se perguntando como você pode exibir valores RGB (vermelhos, verdes, azuis) em formato hexadecimal de dois dígitos tradicional. Esses valores inteiros não estão diretamente disponíveis no `Color` estrutura. Uma solução seria calcular valores de inteiro dos componentes de cor dentro do ViewModel e expô-los como propriedades. Você pode, em seguida, formatá-los usando o `X2` especificação de formatação.

Outra abordagem é mais geral: você pode escrever uma *conversor de valor de associação* conforme discutido no artigo posterior [ **conversores de valor de associação**](converters.md).

O próximo artigo, no entanto, explora os [ **caminho de associação** ](binding-path.md) em mais detalhe e mostram como você pode usar para fazer referência a subpropriedades e itens em coleções.


## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de associação de dados do catálogo de xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
