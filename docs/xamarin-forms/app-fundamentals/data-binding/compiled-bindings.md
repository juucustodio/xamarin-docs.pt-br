---
title: Xamarin. Forms compilado associações
description: Este artigo explica como usar associações compiladas para melhorar o desempenho de associação de dados em aplicativos xamarin. Forms.
ms.prod: xamarin
ms.assetid: ABE6B7F7-875E-4402-A1D2-845CE374402B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2018
ms.openlocfilehash: 0b350082c834076a1d69427644259087d64bf26a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111450"
---
# <a name="xamarinforms-compiled-bindings"></a>Xamarin. Forms compilado associações

_Associações compiladas são resolvidas mais rapidamente do que associações clássicas, melhorando assim o desempenho da ligação de dados em aplicativos xamarin. Forms._

Associações de dados tem dois problemas principais:

1. Não há nenhuma validação de tempo de compilação de expressões de associação. Em vez disso, as associações são resolvidas em tempo de execução. Portanto, quaisquer associações inválidas não são detectadas até o tempo de execução quando o aplicativo não se comporta conforme o esperado ou mensagens de erro são exibidas.
1. Eles não são econômicas. Associações são resolvidas em tempo de execução usando a inspeção de objeto para fins gerais (reflexão) e a sobrecarga de fazer isso varia de uma plataforma para outra.

Associações compiladas melhorar o desempenho de associação de dados em aplicativos xamarin. Forms Resolvendo expressões de associação em tempo de compilação em vez de tempo de execução. Além disso, essa validação de tempo de compilação de expressões de associação permite que um desenvolvedor melhor experiência de solução de problemas porque associações inválidas são relatadas como erros de compilação.

O processo para usar associações compiladas é:

1. Habilite a compilação de XAML. Para obter mais informações sobre a compilação de XAML, consulte [compilação XAML](~/xamarin-forms/xaml/xamlc.md).
1. Definir um `x:DataType` o atributo em uma [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) para o tipo do objeto que o `VisualElement` e seus filhos se ligará a. Observe que esse atributo pode ser definido novamente em qualquer local em uma hierarquia de exibição.

> [!NOTE]
> É recomendável definir a `x:DataType` atributo no mesmo nível na hierarquia de exibição como o [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) está definido.

Todas as expressões de associação inválida no momento da compilação XAML serão relatadas como erros de compilação. No entanto, o compilador XAML só relatará um erro de compilação para a primeira expressão de associação inválida que encontra. As expressões de associação válida que são definidas na `VisualElement` ou seus filhos serão compiladas, independentemente do [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) é definida em XAML ou código. Compilar uma expressão de associação gera o código compilado que obter um valor de uma propriedade na *fonte*e defini-lo na propriedade no *destino* que é especificado na marcação. Além disso, dependendo da expressão de associação, o código gerado pode observar as alterações no valor do *fonte* propriedade e atualizar o *destino* propriedade e pode enviar por push as alterações da *alvo* volta para o *origem*.

> [!IMPORTANT]
> Associações compiladas estão desabilitadas para as expressões de associação que definem os [ `Source` ](xref:Xamarin.Forms.Binding.Source) propriedade. Isso ocorre porque o `Source` propriedade sempre é definida usando o `x:Reference` extensão de marcação, que não pode ser resolvida em tempo de compilação.

## <a name="using-compiled-bindings"></a>Usando associações compiladas

O **seletor de cores compilado** página demonstra como usar associações compiladas entre as propriedades de ViewModel e modos de exibição do xamarin. Forms:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.CompiledColorSelectorPage"
             Title="Compiled Color Selector">
    ...
    <StackLayout x:DataType="local:HslColorViewModel">
        <StackLayout.BindingContext>
            <local:HslColorViewModel Color="Sienna" />
        </StackLayout.BindingContext>
        <BoxView Color="{Binding Color}"
                 ... />
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

A raiz [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) instancia o `HslColorViewModel` e inicializa o `Color` propriedade dentro de marcas de elemento de propriedade para o [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) propriedade. Essa raiz `StackLayout` também define o `x:DataType` quaisquer expressões de associação na raiz do atributo como o tipo ViewModel, indicando que `StackLayout` hierarquia de exibição será compilada. Isso pode ser verificado por meio da alteração de qualquer uma das expressões de associação para associar a uma propriedade de ViewModel inexistente, o que resultará em um erro de compilação.

> [!IMPORTANT]
> O `x:DataType` atributo pode ser definido novamente a qualquer momento em uma hierarquia de exibição.

O [ `BoxView` ](xref:Xamarin.Forms.BoxView), [ `Label` ](xref:Xamarin.Forms.Label) elementos, e [ `Slider` ](xref:Xamarin.Forms.Slider) exibições herdam o contexto de associação do [ `StackLayout` ](xref:Xamarin.Forms.StackLayout). Essas exibições são todos os destinos de associação que fazem referência a propriedades da fonte no ViewModel. Para o [ `BoxView.Color` ](xref:Xamarin.Forms.BoxView.Color) propriedade e o [ `Label.Text` ](xref:Xamarin.Forms.Label.Text) propriedade, as associações de dados são `OneWay` – configurar as propriedades no modo de exibição das propriedades no ViewModel. No entanto, o [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propriedade usa um `TwoWay` associação. Isso permite que cada `Slider` a ser definido no ViewModel e também para o ViewModel seja definido de cada `Slider`.

Quando o aplicativo é executado pela primeira vez, o [ `BoxView` ](xref:Xamarin.Forms.BoxView), [ `Label` ](xref:Xamarin.Forms.Label) elementos, e [ `Slider` ](xref:Xamarin.Forms.Slider) elementos são definidos no ViewModel com base em inicial `Color` propriedade definida quando o ViewModel foi instanciado. Isso é mostrado nas capturas de tela seguir:

[![Compilado seletor de cores](compiled-bindings-images/compiledcolorselector-small.png "compilado seletor de cores")](compiled-bindings-images/compiledcolorselector-large.png#lightbox "compilado seletor de cores")

Como os controles deslizantes são manipulados, o [ `BoxView` ](xref:Xamarin.Forms.BoxView) e [ `Label` ](xref:Xamarin.Forms.Label) elementos sejam atualizados de acordo.

Para obter mais informações sobre o seletor de cor, consulte [ViewModels e notificações de alteração de propriedade](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#viewmodels-and-property-change-notifications).

## <a name="using-compiled-bindings-in-a-datatemplate"></a>Usando associações compiladas em um DataTemplate

Associações em um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) são interpretadas no contexto de objeto que está sendo modelado. Portanto, quando usando compilado associações em um `DataTemplate`, o `DataTemplate` precisa declarar o tipo de seu objeto de dados usando o `x:DataType` atributo.

O **lista de cores compilado** página demonstra como usar associações compiladas em um [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.CompiledColorListPage"
             Title="Compiled Color List">
    <Grid>
        ...
        <ListView x:Name="colorListView"
                  ItemsSource="{x:Static local:NamedColor.All}"
                  ... >
            <ListView.ItemTemplate>
                <DataTemplate x:DataType="local:NamedColor">
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <BoxView Color="{Binding Color}"
                                     ... />
                            <Label Text="{Binding FriendlyName}"
                                   ... />
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
        <!-- The BoxView doesn't use compiled bindings -->
        <BoxView Color="{Binding Source={x:Reference colorListView}, Path=SelectedItem.Color}"
                 ... />
    </Grid>
</ContentPage>
```

O [ `ListView.ItemsSource` ](xref:Xamarin.Forms.ListView) estiver definida como estática `NamedColor.All` propriedade. O `NamedColor` classe usa a reflexão do .NET para enumerar todos os campos públicos estáticos em de [ `Color` ](xref:Xamarin.Forms.Color) estrutura e para armazená-los com seus nomes em uma coleção que é acessível de estático `All` propriedade. Portanto, o `ListView` é preenchida com todos os `NamedColor` instâncias. Para cada item de `ListView`, o contexto de associação para o item é definido como um `NamedColor` objeto. O [ `BoxView` ](xref:Xamarin.Forms.BoxView) e [ `Label` ](xref:Xamarin.Forms.Label) elementos no [ `ViewCell` ](xref:Xamarin.Forms.ViewCell) são associados a `NamedColor` propriedades.

Observe que o [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) define o `x:DataType` atributo a ser o `NamedColor` tipo, que indica que qualquer expressões de associação a `DataTemplate` hierarquia de exibição será compilada. Isso pode ser verificado por meio da alteração de qualquer uma das expressões de associação para associar a um inexistente `NamedColor` propriedade, o que resultará em um erro de compilação.

Quando o aplicativo é executado pela primeira vez, o [ `ListView` ](xref:Xamarin.Forms.ListView) é preenchida com `NamedColor` instâncias. Quando um item na `ListView` estiver selecionada, o [ `BoxView.Color` ](xref:Xamarin.Forms.BoxView.Color) estiver definida como a cor do item selecionado no `ListView`:

[![Compilada a lista de cores](compiled-bindings-images/compiledcolorlist-small.png "compilado lista Cor]")](compiled-bindings-images/compiledcolorlist-large.png#lightbox "Compiled Color List")

Seleção de outros itens do [ `ListView` ](xref:Xamarin.Forms.BoxView) atualiza a cor do [ `BoxView` ](xref:Xamarin.Forms.BoxView).

## <a name="combining-compiled-bindings-with-classic-bindings"></a>Associações de combinação compilada com associações clássicas

Expressões de associação só são compiladas para a hierarquia de exibição que o `x:DataType` atributo está definido em. Por outro lado, todas as exibições em uma hierarquia na qual o `x:DataType` atributo não está definido usará associações clássicas. Portanto, é possível combinar compiladas associações e associações clássicas em uma página. Por exemplo, anteriormente na seção modos de exibição dentro de [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) usar associações compiladas, enquanto o [ `BoxView` ](xref:Xamarin.Forms.BoxView) que é definido como a cor selecionada no [ `ListView` ](xref:Xamarin.Forms.ListView) não faz isso.

Estruturando cuidadoso de `x:DataType` atributos, portanto, podem levar a uma página usando associações compiladas e clássicas. Como alternativa, o `x:DataType` atributo pode ser definido novamente a qualquer momento em uma hierarquia de exibição `null` usando o `x:Null` extensão de marcação. Isso indica que qualquer expressões de associação de dentro da hierarquia de exibição usará associações clássicas. O *associações misto* página demonstra essa abordagem:

```xaml
<StackLayout x:DataType="local:HslColorViewModel">
    <StackLayout.BindingContext>
        <local:HslColorViewModel Color="Sienna" />
    </StackLayout.BindingContext>
    <BoxView Color="{Binding Color}"
             VerticalOptions="FillAndExpand" />
    <StackLayout x:DataType="{x:Null}"
                 Margin="10, 0">
        <Label Text="{Binding Name}" />
        <Slider Value="{Binding Hue}" />
        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}" />
        <Slider Value="{Binding Saturation}" />
        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}" />
        <Slider Value="{Binding Luminosity}" />
        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}" />
    </StackLayout>
</StackLayout>   
```

A raiz [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) define o `x:DataType` atributo a ser o `HslColorViewModel` tipo, que indica que qualquer expressão de associação na raiz `StackLayout` hierarquia de exibição será compilada. No entanto, interno `StackLayout` redefine o `x:DataType` atributo `null` com o `x:Null` expressão de marcação. Portanto, as expressões de associação dentro interno `StackLayout` usar associações clássicas. Somente o [ `BoxView` ](xref:Xamarin.Forms.BoxView), dentro da raiz `StackLayout` exibir a hierarquia, associações de usos compilados.

Para obter mais informações sobre o `x:Null` expressão de marcação, consulte [extensão de marcação X:Null](~/xamarin-forms/xaml/markup-extensions/consuming.md#null).

## <a name="performance"></a>Desempenho

Associações compiladas melhorar o desempenho, com o benefício de desempenho diferentes de vinculação de dados. Teste de unidade revela que:

- Uma associação compilada que usa a notificação de alteração de propriedade (ou seja, uma `OneWay`, `OneWayToSource`, ou `TwoWay` associação) é resolvido aproximadamente 8 vezes mais rápidos do que uma associação clássica.
- Uma associação compilada que não usa a notificação de alteração de propriedade (ou seja, um `OneTime` associação) é resolvido aproximadamente 20 vezes mais rápidos do que uma associação clássica.
- Definindo o [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) em uma associação compilada que usa a propriedade de notificação de alteração (ou seja, uma `OneWay`, `OneWayToSource`, ou `TwoWay` associação) é de aproximadamente 5 vezes mais rápido do que definir o `BindingContext`em uma associação clássica.
- Definindo o [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) notificação de alteração em uma associação compilada que não usa a propriedade (ou seja, um `OneTime` associação) é de aproximadamente 7 vezes mais rápido do que a configuração de `BindingContext` em uma associação clássica.

Essas diferenças de desempenho podem ser aumentadas em dispositivos móveis, de acordo com a plataforma que está sendo usada, a versão do sistema operacional que está sendo usado e o dispositivo no qual o aplicativo está em execução.

## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
