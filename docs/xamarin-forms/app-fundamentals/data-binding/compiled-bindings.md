---
title: Xamarin.FormsAssociações compiladas
description: Este artigo explica como usar associações compiladas para melhorar o desempenho de vinculação de dados em Xamarin.Forms aplicativos.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1f811de95009900016bb8b442265a9a079e0f612
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139731"
---
# <a name="xamarinforms-compiled-bindings"></a>Xamarin.FormsAssociações compiladas

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

_As associações compiladas são resolvidas mais rapidamente do que as associações clássicas, portanto, melhorando o desempenho da vinculação de dados em Xamarin.Forms aplicativos._

Associações de dados têm dois problemas principais:

1. Não há validação em tempo de compilação para expressões de associação. Em vez disso, as associações são resolvidas em runtime. Sendo assim, associações inválidas não são detectadas até o runtime, quando o aplicativo não se comporta conforme esperado ou mensagens de erro são exibidas.
1. Elas não são econômicas. Associações são resolvidas em runtime usando a inspeção de objeto para uso geral (reflexão), e a sobrecarga de fazer isso varia de uma plataforma para outra.

As associações compiladas melhoram o desempenho de vinculação de dados em Xamarin.Forms aplicativos resolvendo expressões de associação em tempo de compilação em vez de tempo de execução. Além disso, essa validação em tempo de compilação das expressões de associação permite uma melhor experiência de solução de problemas para o desenvolvedor porque associações inválidas são relatadas como erros de build.

O processo para usar associações compiladas é:

1. Habilitar a compilação de XAML. Para obter mais informações sobre a compilação de XAML, confira [Compilação de XAML](~/xamarin-forms/xaml/xamlc.md).
1. Defina um `x:DataType` atributo em um [`VisualElement`](xref:Xamarin.Forms.VisualElement) para o tipo do objeto ao qual o `VisualElement` e seus filhos se associarão.

> [!NOTE]
> É recomendável definir o `x:DataType` atributo no mesmo nível na hierarquia de exibição que o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) está definido. No entanto, esse atributo pode ser redefinido em qualquer local em uma hierarquia de exibição.

Para usar associações compiladas, o `x:DataType` atributo deve ser definido como um literal de cadeia de caracteres ou um tipo usando a `x:Type` extensão de marcação. No tempo de compilação de XAML, as expressões de associação inválidas serão relatadas como erros de build. No entanto, o compilador XAML relatará um erro de build somente para a primeira expressão de associação inválida que encontrar. Todas as expressões de associação válidas definidas no `VisualElement` ou seus filhos serão compiladas, independentemente de o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) ser definido no XAML ou no código. Compilar uma expressão de associação gera o código compilado que obterá um valor de uma propriedade na *origem* e o definirá na propriedade de *destino* especificada na marcação. Além disso, dependendo da expressão de associação, o código gerado poderá observar alterações no valor da propriedade de *origem* e atualizar a propriedade de *destino*, e pode enviar por push alterações do *destino* para a *origem*.

> [!IMPORTANT]
> As associações compiladas estão atualmente desabilitadas para todas as expressões de associação que definem a [`Source`](xref:Xamarin.Forms.Binding.Source) propriedade. Isso acontece porque a propriedade `Source` sempre é definida usando a extensão de marcação `x:Reference`, que não pode ser resolvida em tempo de compilação.

## <a name="use-compiled-bindings"></a>Usar associações compiladas

A página **seletor de cores compilada** demonstra o uso de associações compiladas entre Xamarin.Forms exibições e propriedades do ViewModel:

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

A raiz [`StackLayout`](xref:Xamarin.Forms.StackLayout) instancia o `HslColorViewModel` e inicializa a `Color` Propriedade nas marcas de elemento de propriedade da [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) propriedade. Essa raiz `StackLayout` também define o `x:DataType` atributo como o tipo ViewModel, indicando que todas as expressões de associação na `StackLayout` hierarquia de exibição raiz serão compiladas. Isso pode ser verificado com a alteração de qualquer uma das expressões de associação para associar a uma propriedade ViewModel não existente, o que resultará em um erro de compilação. Embora este exemplo defina o `x:DataType` atributo como um literal de cadeia de caracteres, ele também pode ser definido como um tipo com a `x:Type` extensão de marcação. Para obter mais informações sobre a `x:Type` extensão de marcação, consulte a [extensão de marcação x:Type](~/xamarin-forms/xaml/markup-extensions/consuming.md#type).

> [!IMPORTANT]
> O atributo `x:DataType` pode ser redefinido em qualquer ponto de uma hierarquia de exibição.

Os [`BoxView`](xref:Xamarin.Forms.BoxView) [`Label`](xref:Xamarin.Forms.Label) elementos, e [`Slider`](xref:Xamarin.Forms.Slider) exibições herdam o contexto de associação do [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Essas exibições são todos os destinos de associação que fazem referência às propriedades de origem no ViewModel. Para a [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) propriedade e a [`Label.Text`](xref:Xamarin.Forms.Label.Text) propriedade, as associações de dados são `OneWay` – as propriedades na exibição são definidas a partir das propriedades no ViewModel. No entanto, a [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) propriedade usa uma `TwoWay` associação. Isso permite que cada um `Slider` seja definido a partir do ViewModel e também que o ViewModel seja definido de cada um `Slider` .

Quando o aplicativo é executado pela primeira vez, os elementos [`BoxView`](xref:Xamarin.Forms.BoxView) , [`Label`](xref:Xamarin.Forms.Label) e, [`Slider`](xref:Xamarin.Forms.Slider) são todos definidos a partir do ViewModel com base na `Color` propriedade inicial definida quando o ViewModel foi instanciado. Isso é mostrado nas seguintes capturas de tela:

[![Seletor de cor compilado](compiled-bindings-images/compiledcolorselector-small.png "Seletor de cor compilado")](compiled-bindings-images/compiledcolorselector-large.png#lightbox "Seletor de cor compilado")

À medida que os controles deslizantes são manipulados, os [`BoxView`](xref:Xamarin.Forms.BoxView) [`Label`](xref:Xamarin.Forms.Label) elementos e são atualizados de acordo.

Para obter mais informações sobre o seletor de cores, confira [ViewModels e notificações de alteração de propriedade](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#viewmodels-and-property-change-notifications).

## <a name="use-compiled-bindings-in-a-datatemplate"></a>Usar associações compiladas em um DataTemplate

Associações em um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) são interpretadas no contexto do objeto que está sendo modelado. Portanto, ao usar associações compiladas em um `DataTemplate`, o `DataTemplate` precisa declarar o tipo de seu objeto de dados usando o atributo `x:DataType`.

A página **Lista de Cores Compilada** demonstra como usar associações compiladas em um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

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

A [`ListView.ItemsSource`](xref:Xamarin.Forms.ListView) propriedade é definida como a `NamedColor.All` propriedade estática. A `NamedColor` classe usa a reflexão do .net para enumerar todos os campos públicos estáticos na [`Color`](xref:Xamarin.Forms.Color) estrutura e para armazená-los com seus nomes em uma coleção que é acessível por meio da `All` propriedade estática. Portanto, o `ListView` é preenchido com todas as instâncias de `NamedColor`. Para cada item em `ListView`, o contexto de associação para o item é definido como um objeto `NamedColor`. Os [`BoxView`](xref:Xamarin.Forms.BoxView) [`Label`](xref:Xamarin.Forms.Label) elementos e no [`ViewCell`](xref:Xamarin.Forms.ViewCell) são associados a `NamedColor` Propriedades.

Observe que o [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) define o `x:DataType` atributo como o `NamedColor` tipo, indicando que todas as expressões de associação na `DataTemplate` hierarquia de exibição serão compiladas. Isso pode ser verificado alterando qualquer uma das expressões de associação para se associar a uma propriedade `NamedColor` inexistente, o que causará um erro de build.  Embora este exemplo defina o `x:DataType` atributo como um literal de cadeia de caracteres, ele também pode ser definido como um tipo com a `x:Type` extensão de marcação. Para obter mais informações sobre a `x:Type` extensão de marcação, consulte a [extensão de marcação x:Type](~/xamarin-forms/xaml/markup-extensions/consuming.md#type).

Quando o aplicativo é executado pela primeira vez, o [`ListView`](xref:Xamarin.Forms.ListView) é preenchido com `NamedColor` instâncias. Quando um item no `ListView` é selecionado, a [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) propriedade é definida como a cor do item selecionado no `ListView` :

[![Lista de cores compilada](compiled-bindings-images/compiledcolorlist-small.png "Lista de cores compilada]")](compiled-bindings-images/compiledcolorlist-large.png#lightbox "Lista de cores compilada")

A seleção de outros itens no [`ListView`](xref:Xamarin.Forms.BoxView) atualiza a cor do [`BoxView`](xref:Xamarin.Forms.BoxView) .

## <a name="combine-compiled-bindings-with-classic-bindings"></a>Combinar associações compiladas com associações clássicas

Expressões de associação são compiladas apenas para a hierarquia de exibição em que o atributo `x:DataType` está definido. Por outro lado, exibições em uma hierarquia na qual o atributo `x:DataType` não está definido usarão associações clássicas. Portanto, é possível combinar associações compiladas e associações clássicas em uma página. Por exemplo, na seção anterior, as exibições em [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) usar associações compiladas, enquanto a [`BoxView`](xref:Xamarin.Forms.BoxView) que está definida para a cor selecionada no não [`ListView`](xref:Xamarin.Forms.ListView) .

Estruturar cuidadosamente os atributos `x:DataType`, portanto, pode levar a uma página que usa associações compiladas e clássicas. Como alternativa, o atributo `x:DataType` pode ser redefinido a qualquer momento em uma hierarquia de exibição como `null` usando a extensão de marcação `x:Null`. Fazer isso indica que qualquer expressão de associação de dentro da hierarquia de exibição usará associações clássicas. A página *Associações Mistas* demonstra essa abordagem:

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

A raiz [`StackLayout`](xref:Xamarin.Forms.StackLayout) define o `x:DataType` atributo como o `HslColorViewModel` tipo, indicando que qualquer expressão de associação na hierarquia de `StackLayout` exibição raiz será compilada. No entanto, o `StackLayout` interno redefine o atributo `x:DataType` como `null` com a expressão de marcação `x:Null`. Portanto, as expressões de associação no `StackLayout` interno usam associações clássicas. Somente o [`BoxView`](xref:Xamarin.Forms.BoxView) , dentro da `StackLayout` hierarquia de exibição raiz, usa associações compiladas.

Para obter mais informações sobre a expressão de marcação `x:Null`, confira [Extensão de marcação x:Null](~/xamarin-forms/xaml/markup-extensions/consuming.md#null).

## <a name="performance"></a>Desempenho

Associações compiladas melhoram o desempenho da associação de dados, com benefícios de desempenho variáveis. Testes de unidade revelam que:

- Uma associação compilada que usa notificação de alteração de propriedade (ou seja, uma associação `OneWay`, `OneWayToSource` ou `TwoWay`) é resolvida aproximadamente oito vezes mais rápido do que uma associação clássica.
- Uma associação compilada que não usa notificação de alteração de propriedade (ou seja, uma associação `OneTime`) é resolvida aproximadamente vinte vezes mais rápido do que uma associação clássica.
- Definir o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) em uma associação compilada que usa a notificação de alteração de propriedade (ou seja `OneWay` , a, `OneWayToSource` ou `TwoWay` associação) é aproximadamente 5 vezes mais rápido do que definir o `BindingContext` em uma associação clássica.
- Definir o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) em uma associação compilada que não usa a notificação de alteração de propriedade (ou seja, uma `OneTime` associação) é aproximadamente 7 vezes mais rápido do que definir o `BindingContext` em uma associação clássica.

Essas diferenças de desempenho podem aumentar em dispositivos móveis, dependendo da plataforma usada, da versão do sistema operacional usado e do dispositivo no qual o aplicativo está em execução.

## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
