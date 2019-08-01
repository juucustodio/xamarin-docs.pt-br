---
title: Associações compiladas do Xamarin.Forms
description: Este artigo explica como usar associações compiladas para aprimorar o desempenho da associação de dados em aplicativos Xamarin.Forms.
ms.prod: xamarin
ms.assetid: ABE6B7F7-875E-4402-A1D2-845CE374402B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2018
ms.openlocfilehash: 8effabc66b51b1cf32e3be455428c44eeea3f26d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656222"
---
# <a name="xamarinforms-compiled-bindings"></a>Associações compiladas do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

_Associações compiladas são resolvidas mais rapidamente do que associações clássicas, melhorando assim o desempenho da associação de dados em aplicativos do Xamarin.Forms._

Associações de dados têm dois problemas principais:

1. Não há validação em tempo de compilação para expressões de associação. Em vez disso, as associações são resolvidas em tempo de execução. Sendo assim, associações inválidas não são detectadas até o tempo de execução, quando o aplicativo não se comporta conforme esperado ou mensagens de erro são exibidas.
1. Elas não são econômicas. Associações são resolvidas em tempo de execução usando a inspeção de objeto para uso geral (reflexão), e a sobrecarga de fazer isso varia de uma plataforma para outra.

Associações compiladas melhoram o desempenho de associação de dados em aplicativos do Xamarin.Forms resolvendo expressões de associação em tempo de compilação, em vez de tempo de execução. Além disso, essa validação em tempo de compilação das expressões de associação permite uma melhor experiência de solução de problemas para o desenvolvedor porque associações inválidas são relatadas como erros de build.

O processo para usar associações compiladas é:

1. Habilitar a compilação de XAML. Para obter mais informações sobre a compilação de XAML, confira [Compilação de XAML](~/xamarin-forms/xaml/xamlc.md).
1. Definir um atributo `x:DataType` em um [`VisualElement`](xref:Xamarin.Forms.VisualElement) como o tipo do objeto ao qual `VisualElement` e seus filhos se associarão. Observe que esse atributo pode ser redefinido em qualquer localização de uma hierarquia de exibição.

> [!NOTE]
> É recomendável definir o atributo `x:DataType` no mesmo nível da hierarquia de exibição em que [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) está definido.

No tempo de compilação de XAML, as expressões de associação inválidas serão relatadas como erros de build. No entanto, o compilador XAML relatará um erro de build somente para a primeira expressão de associação inválida que encontrar. Expressões de associação válidas definidas no `VisualElement` ou em seus filhos serão compiladas, independentemente de [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) estar definido no XAML ou no código. Compilar uma expressão de associação gera o código compilado que obterá um valor de uma propriedade na *origem* e o definirá na propriedade de *destino* especificada na marcação. Além disso, dependendo da expressão de associação, o código gerado poderá observar alterações no valor da propriedade de *origem* e atualizar a propriedade de *destino*, e pode enviar por push alterações do *destino* para a *origem*.

> [!IMPORTANT]
> Atualmente, associações compiladas estão desabilitadas para expressões de associação que definem a propriedade [`Source`](xref:Xamarin.Forms.Binding.Source). Isso acontece porque a propriedade `Source` sempre é definida usando a extensão de marcação `x:Reference`, que não pode ser resolvida em tempo de compilação.

## <a name="using-compiled-bindings"></a>Usando associações compiladas

A página **Seletor de Cores Compilado** demonstra como usar associações compiladas entre as exibições do Xamarin.Forms e as propriedades de ViewModel:

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

O [`StackLayout`](xref:Xamarin.Forms.StackLayout) raiz instancia o `HslColorViewModel` e inicializa a propriedade `Color` dentro de marcas de elemento de propriedade para a propriedade [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext). Esse `StackLayout` raiz também define o atributo `x:DataType` como o tipo ViewModel, indicando que as expressões de associação na hierarquia de exibição do `StackLayout` raiz serão compiladas. Isso pode ser verificado alterando qualquer uma das expressões de associação para se associar a uma propriedade de ViewModel inexistente, o que causará um erro de build.

> [!IMPORTANT]
> O atributo `x:DataType` pode ser redefinido em qualquer ponto de uma hierarquia de exibição.

Os elementos [`BoxView`](xref:Xamarin.Forms.BoxView), [`Label`](xref:Xamarin.Forms.Label) e as exibições [`Slider`](xref:Xamarin.Forms.Slider) herdam o contexto de associação da [`StackLayout`](xref:Xamarin.Forms.StackLayout). Essas exibições são destinos de associação que referenciam as propriedades de origem no ViewModel. Para as propriedades [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) e [`Label.Text`](xref:Xamarin.Forms.Label.Text), as associações de dados são `OneWay` – as propriedades na exibição são definidas nas propriedades do ViewModel. No entanto, a propriedade [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) usa uma associação `TwoWay`. Isso permite que cada `Slider` seja definido no ViewModel e também que o ViewModel seja definido em cada `Slider`.

Quando o aplicativo é executado pela primeira vez, os elementos [`BoxView`](xref:Xamarin.Forms.BoxView), [`Label`](xref:Xamarin.Forms.Label) e [`Slider`](xref:Xamarin.Forms.Slider) são definidos no ViewModel com base na propriedade `Color` inicial definida quando foi criada uma instância do ViewModel. Isso é mostrado nas seguintes capturas de tela:

[![Seletor de Cores Compilado](compiled-bindings-images/compiledcolorselector-small.png "Seletor de Cores Compilado")](compiled-bindings-images/compiledcolorselector-large.png#lightbox "Seletor de Cores Compilado")

Conforme os controles deslizantes são manipulados, os elementos [`BoxView`](xref:Xamarin.Forms.BoxView) e [`Label`](xref:Xamarin.Forms.Label) são atualizados.

Para obter mais informações sobre o seletor de cores, confira [ViewModels e notificações de alteração de propriedade](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#viewmodels-and-property-change-notifications).

## <a name="using-compiled-bindings-in-a-datatemplate"></a>Usando associações compiladas em um DataTemplate

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

A propriedade [`ListView.ItemsSource`](xref:Xamarin.Forms.ListView) é definida como a propriedade estática `NamedColor.All`. A classe `NamedColor` usa a reflexão do .NET para enumerar todos os campos públicos estáticos na estrutura [`Color`](xref:Xamarin.Forms.Color) e armazená-los com seus nomes em uma coleção acessível na propriedade estática `All`. Portanto, o `ListView` é preenchido com todas as instâncias de `NamedColor`. Para cada item em `ListView`, o contexto de associação para o item é definido como um objeto `NamedColor`. Os elementos [`BoxView`](xref:Xamarin.Forms.BoxView) e [`Label`](xref:Xamarin.Forms.Label) no [`ViewCell`](xref:Xamarin.Forms.ViewCell) estão associados às propriedades em `NamedColor`.

Observe que o [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) define o atributo `x:DataType` como o tipo `NamedColor`, indicando que as expressões de associação na hierarquia de exibição `DataTemplate` serão compiladas. Isso pode ser verificado alterando qualquer uma das expressões de associação para se associar a uma propriedade `NamedColor` inexistente, o que causará um erro de build.

Quando o aplicativo é executado pela primeira vez, o [`ListView`](xref:Xamarin.Forms.ListView) é preenchido com instâncias de `NamedColor`. Quando um item no `ListView` é selecionado, a propriedade [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) é definida como a cor do item selecionado no `ListView`:

[![Lista de Cores Compilada](compiled-bindings-images/compiledcolorlist-small.png "Lista de Cores Compilada]")](compiled-bindings-images/compiledcolorlist-large.png#lightbox "Compiled Color List")

Selecionar outros itens no [`ListView`](xref:Xamarin.Forms.BoxView) atualiza a cor do [`BoxView`](xref:Xamarin.Forms.BoxView).

## <a name="combining-compiled-bindings-with-classic-bindings"></a>Combinando associações compiladas com associações clássicas

Expressões de associação são compiladas apenas para a hierarquia de exibição em que o atributo `x:DataType` está definido. Por outro lado, exibições em uma hierarquia na qual o atributo `x:DataType` não está definido usarão associações clássicas. Portanto, é possível combinar associações compiladas e associações clássicas em uma página. Por exemplo, na seção anterior, os modos de exibição dentro do [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) usam associações compiladas, enquanto o [`BoxView`](xref:Xamarin.Forms.BoxView) definido como a cor selecionada no [`ListView`](xref:Xamarin.Forms.ListView) não faz isso.

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

O [`StackLayout`](xref:Xamarin.Forms.StackLayout) raiz define o atributo `x:DataType` como o tipo `HslColorViewModel`, indicando que as expressões de associação na hierarquia de exibição `StackLayout` raiz serão compiladas. No entanto, o `StackLayout` interno redefine o atributo `x:DataType` como `null` com a expressão de marcação `x:Null`. Portanto, as expressões de associação no `StackLayout` interno usam associações clássicas. Somente o [`BoxView`](xref:Xamarin.Forms.BoxView), dentro da hierarquia de exibição `StackLayout` raiz, usa associações compiladas.

Para obter mais informações sobre a expressão de marcação `x:Null`, confira [Extensão de marcação x:Null](~/xamarin-forms/xaml/markup-extensions/consuming.md#null).

## <a name="performance"></a>Desempenho

Associações compiladas melhoram o desempenho da associação de dados, com benefícios de desempenho variáveis. Testes de unidade revelam que:

- Uma associação compilada que usa notificação de alteração de propriedade (ou seja, uma associação `OneWay`, `OneWayToSource` ou `TwoWay`) é resolvida aproximadamente oito vezes mais rápido do que uma associação clássica.
- Uma associação compilada que não usa notificação de alteração de propriedade (ou seja, uma associação `OneTime`) é resolvida aproximadamente vinte vezes mais rápido do que uma associação clássica.
- Definir o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) em uma associação compilada que usa notificação de alteração de propriedade (ou seja, uma associação `OneWay`, `OneWayToSource` ou `TwoWay`) é aproximadamente cinco vezes mais rápido do que definir o `BindingContext` em uma associação clássica.
- Definir o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) em uma associação compilada que não usa notificação de alteração de propriedade (ou seja, uma associação `OneTime`), é aproximadamente sete vezes mais rápido do que definir o `BindingContext` em uma associação clássica.

Essas diferenças de desempenho podem aumentar em dispositivos móveis, dependendo da plataforma usada, da versão do sistema operacional usado e do dispositivo no qual o aplicativo está em execução.

## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
