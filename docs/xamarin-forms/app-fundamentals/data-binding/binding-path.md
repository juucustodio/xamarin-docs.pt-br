---
title: Caminho de associação do Xamarin.Forms
description: Este artigo explica como usar associações de dados do Xamarin.Forms para acessar subpropriedades e membros da coleção com a propriedade Path da classe Binding.
ms.prod: xamarin
ms.assetid: 3CF721A5-E157-468B-AD3A-DA0A45E58E8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 0c63e33309802f0945ad94a858af45f6b29b2cc4
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53050773"
---
# <a name="xamarinforms-binding-path"></a>Caminho de associação do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)

Em todos os exemplos anteriores de associação de dados, a propriedade [`Path`](xref:Xamarin.Forms.Binding.Path) da classe `Binding` (ou a propriedade [`Path`](xref:Xamarin.Forms.Xaml.BindingExtension.Path) da extensão de marcação `Binding`) foi definida como uma única propriedade. Na verdade, é possível definir `Path` como uma *subpropriedade* (uma propriedade de uma propriedade) ou como um membro de uma coleção.

Por exemplo, suponha que a página contenha um `TimePicker`:

```xaml
<TimePicker x:Name="timePicker">
```

A propriedade `Time` de `TimePicker` é do tipo `TimeSpan`, mas talvez você deseje criar uma associação de dados que referencie a propriedade `TotalSeconds` desse valor `TimeSpan`. Esta é a associação de dados:

```xaml
{Binding Source={x:Reference timePicker},
         Path=Time.TotalSeconds}
```

A propriedade `Time` é do tipo `TimeSpan`, que tem uma propriedade `TotalSeconds`. As propriedades `Time` e `TotalSeconds` são simplesmente conectadas com um ponto. Os itens da cadeia de caracteres `Path` sempre se referem a propriedades e não aos tipos dessas propriedades.

Esse exemplo e vários outros são mostrados na página **Variações de caminho**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:globe="clr-namespace:System.Globalization;assembly=mscorlib"
             x:Class="DataBindingDemos.PathVariationsPage"
             Title="Path Variations"
             x:Name="page">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Label">
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="HorizontalTextAlignment" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <StackLayout Margin="10, 0">
        <TimePicker x:Name="timePicker" />

        <Label Text="{Binding Source={x:Reference timePicker},
                              Path=Time.TotalSeconds,
                              StringFormat='{0} total seconds'}" />

        <Label Text="{Binding Source={x:Reference page},
                              Path=Content.Children.Count,
                              StringFormat='There are {0} children in this StackLayout'}" />

        <Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                              Path=DateTimeFormat.DayNames[3],
                              StringFormat='The middle day of the week is {0}'}" />

        <Label>
            <Label.Text>
                <Binding Path="DateTimeFormat.DayNames[3]"
                         StringFormat="The middle day of the week in France is {0}">
                    <Binding.Source>
                        <globe:CultureInfo>
                            <x:Arguments>
                                <x:String>fr-FR</x:String>
                            </x:Arguments>
                        </globe:CultureInfo>
                    </Binding.Source>
                </Binding>
            </Label.Text>
        </Label>

        <Label Text="{Binding Source={x:Reference page},
                              Path=Content.Children[1].Text.Length,
                              StringFormat='The second Label has {0} characters'}" />
    </StackLayout>
</ContentPage>
```

No segundo `Label`, a origem da associação é a página propriamente dita. A propriedade `Content` é do tipo `StackLayout`, que tem uma propriedade `Children` do tipo `IList<View>`, que, por sua vez, tem uma propriedade `Count` que indica o número de filhos.

## <a name="paths-with-indexers"></a>Caminhos com indexadores

A associação no terceiro `Label` nas páginas **Variações de caminho** referencia a classe [`CultureInfo`](xref:System.Globalization.CultureInfo) no namespace `System.Globalization`:

```xaml
<Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                      Path=DateTimeFormat.DayNames[3],
                      StringFormat='The middle day of the week is {0}'}" />
```

A origem é definida como a propriedade estática `CultureInfo.CurrentCulture`, que é um objeto do tipo `CultureInfo`. Esse classe define uma propriedade chamada `DateTimeFormat` do tipo [`DateTimeFormatInfo`](xref:System.Globalization.DateTimeFormatInfo) que contém uma coleção `DayNames`. O índice seleciona o quarto item.

O quarto `Label` faz algo similar, mas para a cultura associada à França. A propriedade `Source` da associação é definida como o objeto `CultureInfo` com um construtor:

```xaml
<Label>
    <Label.Text>
        <Binding Path="DateTimeFormat.DayNames[3]"
                 StringFormat="The middle day of the week in France is {0}">
            <Binding.Source>
                <globe:CultureInfo>
                    <x:Arguments>
                        <x:String>fr-FR</x:String>
                    </x:Arguments>
                </globe:CultureInfo>
            </Binding.Source>
        </Binding>
    </Label.Text>
</Label>
```

Confira [Passando argumentos de construtor](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) para obter mais detalhes sobre como especificar argumentos de construtor em XAML.

Por fim, o último exemplo é semelhante ao segundo, exceto que ele referencia um dos filhos do `StackLayout`:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content.Children[1].Text.Length,
                      StringFormat='The first Label has {0} characters'}" />
```

Esse filho é um `Label`, que tem uma propriedade `Text` do tipo `String`, que, por sua vez, tem uma propriedade `Length`. O primeiro `Label` relata o `TimeSpan` definido no `TimePicker`; portanto, quando esse texto é alterado, o último `Label` também é alterado.

Este é o programa em execução:

[![Variações de caminho](binding-path-images/pathvariations-small.png "Variações de caminho")](binding-path-images/pathvariations-large.png#lightbox "Variações de caminho")

## <a name="debugging-complex-paths"></a>Caminhos complexos de depuração

Definições de caminhos complexos podem ser difíceis de construir: Você precisa saber o tipo de cada subpropriedade ou o tipo dos itens na coleção para adicionar corretamente a próxima subpropriedade, mas os tipos propriamente ditos não são exibidos no caminho. Uma boa técnica é criar o caminho de forma incremental e examinar os resultados intermediários. Para esse último exemplo, você pode começar sem nenhuma definição de `Path`:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      StringFormat='{0}'}" />
```

Isso exibe o tipo da origem da associação ou `DataBindingDemos.PathVariationsPage`. Você sabe que `PathVariationsPage` deriva de `ContentPage` e, portanto, ele tem uma propriedade `Content`:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content,
                      StringFormat='{0}'}" />
```

O tipo da propriedade `Content` agora é revelado como sendo `Xamarin.Forms.StackLayout`. Adicione a propriedade `Children` ao `Path` e o tipo será `Xamarin.Forms.ElementCollection'1[Xamarin.Forms.View]`, que é uma classe interna do Xamarin.Forms, mas, obviamente, um tipo de coleção. Adicione um índice a ele e o tipo será `Xamarin.Forms.Label`. Continue dessa maneira.

Como o Xamarin.Forms processa o caminho de associação, ele instala um manipulador `PropertyChanged` em qualquer objeto no caminho que implementa a interface `INotifyPropertyChanged`. Por exemplo, a associação final reage a uma alteração no primeiro `Label` porque a propriedade `Text` é alterada.

Se uma propriedade no caminho de associação não implementar `INotifyPropertyChanged`, todas as alterações a essa propriedade serão ignoradas. Algumas alterações podem invalidar por completo o caminho de associação. Portanto, você deverá usar essa técnica somente quando a cadeia de caracteres de propriedades e subpropriedades nunca se tornarem inválidas.



## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de associação de dados do catálogo de Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
