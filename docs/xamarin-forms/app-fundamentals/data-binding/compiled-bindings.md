---
title: Associações compiladas do Xamarin.Forms
description: Este artigo explica como usar associações compiladas para aprimorar o desempenho da associação de dados em aplicativos Xamarin.Forms.
ms.prod: xamarin
ms.assetid: ABE6B7F7-875E-4402-A1D2-845CE374402B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2019
ms.openlocfilehash: 531d9719eb4bf5c23001ebe4260254e13f9989eb
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "72697150"
---
# <a name="xamarinforms-compiled-bindings"></a>Associações compiladas do Xamarin.Forms

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

_Associações compiladas são resolvidas mais rapidamente do que associações clássicas, melhorando assim o desempenho da associação de dados em aplicativos do Xamarin.Forms._

Associações de dados têm dois problemas principais:

1. Não há validação em tempo de compilação para expressões de associação. Em vez disso, as associações são resolvidas em runtime. Sendo assim, associações inválidas não são detectadas até o runtime, quando o aplicativo não se comporta conforme esperado ou mensagens de erro são exibidas.
1. Elas não são econômicas. Associações são resolvidas em runtime usando a inspeção de objeto para uso geral (reflexão), e a sobrecarga de fazer isso varia de uma plataforma para outra.

Associações compiladas melhoram o desempenho de associação de dados em aplicativos do Xamarin.Forms resolvendo expressões de associação em tempo de compilação, em vez de runtime. Além disso, essa validação em tempo de compilação das expressões de associação permite uma melhor experiência de solução de problemas para o desenvolvedor porque associações inválidas são relatadas como erros de build.

O processo para usar associações compiladas é:

1. Habilitar a compilação de XAML. Para obter mais informações sobre a compilação de XAML, confira [Compilação de XAML](~/xamarin-forms/xaml/xamlc.md).
1. Defina `x:DataType` um [`VisualElement`](xref:Xamarin.Forms.VisualElement) atributo em um ao `VisualElement` tipo de objeto ao que os filhos se vincularão.

> [!NOTE]
> Recomenda-se definir o `x:DataType` atributo no mesmo nível na hierarquia [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de exibição que o conjunto. No entanto, esse atributo pode ser redefinido em qualquer local em uma hierarquia de exibição.

Para usar as vinculações `x:DataType` compiladas, o atributo deve ser `x:Type` definido como um literal de seqüência ou um tipo usando a extensão de marcação. No tempo de compilação de XAML, as expressões de associação inválidas serão relatadas como erros de build. No entanto, o compilador XAML relatará um erro de build somente para a primeira expressão de associação inválida que encontrar. Quaisquer expressões de vinculação `VisualElement` válidas definidas no ou em [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) seus filhos serão compiladas, independentemente de a ser definida em XAML ou código. Compilar uma expressão de associação gera o código compilado que obterá um valor de uma propriedade na *origem* e o definirá na propriedade de *destino* especificada na marcação. Além disso, dependendo da expressão de associação, o código gerado poderá observar alterações no valor da propriedade de *origem* e atualizar a propriedade de *destino*, e pode enviar por push alterações do *destino* para a *origem*.

> [!IMPORTANT]
> As vinculações compiladas estão atualmente desativadas para quaisquer expressões vinculantes que definam a [`Source`](xref:Xamarin.Forms.Binding.Source) propriedade. Isso acontece porque a propriedade `Source` sempre é definida usando a extensão de marcação `x:Reference`, que não pode ser resolvida em tempo de compilação.

## <a name="use-compiled-bindings"></a>Usar associações compiladas

A página **Compilou Color Selector** demonstra o uso de vinculações compiladas entre xamarin.forms visualizações e propriedades do modelo de visualização:

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

A [`StackLayout`](xref:Xamarin.Forms.StackLayout) raiz instancia e `HslColorViewModel` inicializa a propriedade dentro das `Color` etiquetas de elemento de propriedade para a [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) propriedade. Essa `StackLayout` raiz também `x:DataType` define o atributo como o tipo de modelo `StackLayout` de exibição, indicando que quaisquer expressões vinculantes na hierarquia de exibição raiz serão compiladas. Isso pode ser verificado alterando qualquer uma das expressões vinculantes para vincular a uma propriedade de modelo de exibição inexistente, o que resultará em um erro de compilação. Embora este exemplo `x:DataType` defina o atributo para um literal de `x:Type` seqüência, ele também pode ser definido como um tipo com a extensão de marcação. Para obter mais `x:Type` informações sobre a extensão de marcação, consulte [x:Tipo de extensão de marcação](~/xamarin-forms/xaml/markup-extensions/consuming.md#type).

> [!IMPORTANT]
> O atributo `x:DataType` pode ser redefinido em qualquer ponto de uma hierarquia de exibição.

Os [`BoxView`](xref:Xamarin.Forms.BoxView) [`Label`](xref:Xamarin.Forms.Label) elementos [`Slider`](xref:Xamarin.Forms.Slider) e pontos de vista [`StackLayout`](xref:Xamarin.Forms.StackLayout)herdam o contexto vinculante do . Essas visualizações são todos alvos vinculativos que referenciam propriedades de origem no modelo de exibição. Para [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) a propriedade [`Label.Text`](xref:Xamarin.Forms.Label.Text) e a propriedade, `OneWay` as vinculações de dados são – as propriedades na exibição são definidas a partir das propriedades no modelo de exibição. No entanto, [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) `TwoWay` a propriedade usa uma vinculação. Isso permite `Slider` que cada um seja definido a partir do modelo `Slider`de exibição, e também para que o modelo de visualização seja definido a partir de cada um .

Quando o aplicativo é [`BoxView`](xref:Xamarin.Forms.BoxView)executado [`Label`](xref:Xamarin.Forms.Label) pela [`Slider`](xref:Xamarin.Forms.Slider) primeira vez, os elementos e `Color` elementos são definidos a partir do modelo de exibição com base no conjunto de propriedades iniciais quando o modelo de exibição foi instanciado. Isso é mostrado nas seguintes capturas de tela:

[![Seletor de cores compilado](compiled-bindings-images/compiledcolorselector-small.png "Seletor de cores compilado")](compiled-bindings-images/compiledcolorselector-large.png#lightbox "Seletor de cores compilado")

À medida que os controles [`BoxView`](xref:Xamarin.Forms.BoxView) [`Label`](xref:Xamarin.Forms.Label) deslizantes são manipulados, os elementos são atualizados de acordo.

Para obter mais informações sobre o seletor de cores, confira [ViewModels e notificações de alteração de propriedade](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#viewmodels-and-property-change-notifications).

## <a name="use-compiled-bindings-in-a-datatemplate"></a>Use vinculações compiladas em um DataTemplate

As vinculações [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) em a são interpretadas no contexto do objeto que está sendo modelado. Portanto, ao usar associações compiladas em um `DataTemplate`, o `DataTemplate` precisa declarar o tipo de seu objeto de dados usando o atributo `x:DataType`.

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

A [`ListView.ItemsSource`](xref:Xamarin.Forms.ListView) propriedade está definida `NamedColor.All` para a propriedade estática. A `NamedColor` classe usa o reflexo .NET para enumerar todos os campos públicos estáticos da [`Color`](xref:Xamarin.Forms.Color) estrutura `All` e armazená-los com seus nomes em uma coleção acessível a partir da propriedade estática. Portanto, o `ListView` é preenchido com todas as instâncias de `NamedColor`. Para cada item em `ListView`, o contexto de associação para o item é definido como um objeto `NamedColor`. Os [`BoxView`](xref:Xamarin.Forms.BoxView) [`Label`](xref:Xamarin.Forms.Label) elementos [`ViewCell`](xref:Xamarin.Forms.ViewCell) no estão `NamedColor` ligados às propriedades.

Observe que [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) o `x:DataType` atributo define `NamedColor` como sendo o tipo, indicando que quaisquer expressões vinculantes na hierarquia de `DataTemplate` exibição serão compiladas. Isso pode ser verificado alterando qualquer uma das expressões de associação para se associar a uma propriedade `NamedColor` inexistente, o que causará um erro de build.  Embora este exemplo `x:DataType` defina o atributo para um literal de `x:Type` seqüência, ele também pode ser definido como um tipo com a extensão de marcação. Para obter mais `x:Type` informações sobre a extensão de marcação, consulte [x:Tipo de extensão de marcação](~/xamarin-forms/xaml/markup-extensions/consuming.md#type).

Quando o aplicativo é [`ListView`](xref:Xamarin.Forms.ListView) executado pela `NamedColor` primeira vez, o é preenchido com instâncias. Quando um item `ListView` no é [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) selecionado, a propriedade é definida como `ListView`a cor do item selecionado no :

[![Lista de cores compiladas](compiled-bindings-images/compiledcolorlist-small.png "Lista de cores compilada]")](compiled-bindings-images/compiledcolorlist-large.png#lightbox "Lista de cores compiladas")

Selecionando outros itens [`ListView`](xref:Xamarin.Forms.BoxView) nas atualizações a [`BoxView`](xref:Xamarin.Forms.BoxView)cor do .

## <a name="combine-compiled-bindings-with-classic-bindings"></a>Combine amarras compiladas com ligações clássicas

Expressões de associação são compiladas apenas para a hierarquia de exibição em que o atributo `x:DataType` está definido. Por outro lado, exibições em uma hierarquia na qual o atributo `x:DataType` não está definido usarão associações clássicas. Portanto, é possível combinar associações compiladas e associações clássicas em uma página. Por exemplo, na seção anterior, as exibições dentro das [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) vinculações compiladas de uso, enquanto as [`BoxView`](xref:Xamarin.Forms.BoxView) que estão definidas para a cor selecionada no [`ListView`](xref:Xamarin.Forms.ListView) não.

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

A [`StackLayout`](xref:Xamarin.Forms.StackLayout) raiz `x:DataType` define o `HslColorViewModel` atributo como sendo o tipo, `StackLayout` indicando que qualquer expressão vinculante na hierarquia de exibição raiz será compilada. No entanto, o `StackLayout` interno redefine o atributo `x:DataType` como `null` com a expressão de marcação `x:Null`. Portanto, as expressões de associação no `StackLayout` interno usam associações clássicas. Apenas [`BoxView`](xref:Xamarin.Forms.BoxView)o , `StackLayout` dentro da hierarquia de visualização raiz, usa vinculações compiladas.

Para obter mais informações sobre a expressão de marcação `x:Null`, confira [Extensão de marcação x:Null](~/xamarin-forms/xaml/markup-extensions/consuming.md#null).

## <a name="performance"></a>Desempenho

Associações compiladas melhoram o desempenho da associação de dados, com benefícios de desempenho variáveis. Testes de unidade revelam que:

- Uma associação compilada que usa notificação de alteração de propriedade (ou seja, uma associação `OneWay`, `OneWayToSource` ou `TwoWay`) é resolvida aproximadamente oito vezes mais rápido do que uma associação clássica.
- Uma associação compilada que não usa notificação de alteração de propriedade (ou seja, uma associação `OneTime`) é resolvida aproximadamente vinte vezes mais rápido do que uma associação clássica.
- Definir [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) a vinculação em uma vinculação compilada que `OneWay` `OneWayToSource`usa `TwoWay` a notificação de alteração de propriedade `BindingContext` (ou seja, a , ou vinculação) é aproximadamente 5 vezes mais rápida do que definir a vinculação clássica.
- Definir [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) a vinculação em uma vinculação compilada que não `OneTime` usa a notificação de alteração de `BindingContext` propriedade (ou seja, uma vinculação) é aproximadamente 7 vezes mais rápida do que definir a vinculação clássica.

Essas diferenças de desempenho podem aumentar em dispositivos móveis, dependendo da plataforma usada, da versão do sistema operacional usado e do dispositivo no qual o aplicativo está em execução.

## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)
