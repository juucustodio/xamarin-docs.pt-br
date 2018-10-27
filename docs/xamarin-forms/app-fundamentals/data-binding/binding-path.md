---
title: Caminho de associação do xamarin. Forms
description: Este artigo explica como usar associações de dados do xamarin. Forms para acessar subpropriedades e membros da coleção com a propriedade de caminho de classe de associação.
ms.prod: xamarin
ms.assetid: 3CF721A5-E157-468B-AD3A-DA0A45E58E8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 8340e952d30e21a5249edd0fa3319462bbd5ff8b
ms.sourcegitcommit: 913763498b5d23fa4a92e877760c51164bf1aa41
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50150230"
---
# <a name="xamarinforms-binding-path"></a>Caminho de associação do xamarin. Forms

Em todos os exemplos de vinculação de dados da anteriores, o [ `Path` ](xref:Xamarin.Forms.Binding.Path) propriedade do `Binding` classe (ou o [ `Path` ](xref:Xamarin.Forms.Xaml.BindingExtension.Path) propriedade do `Binding` extensão de marcação) foi definido para uma única propriedade. Ele é, na verdade, é possível definir `Path` para um *subpropriedade* (uma propriedade de uma propriedade), ou a um membro de uma coleção.

Por exemplo, suponha que sua página contém um `TimePicker`:

```xaml
<TimePicker x:Name="timePicker">
```

O `Time` propriedade de `TimePicker` é do tipo `TimeSpan`, mas talvez você queira criar uma associação de dados que faz referência a `TotalSeconds` propriedade do que `TimeSpan` valor. Aqui está a vinculação de dados:

```xaml
{Binding Source={x:Reference timePicker},
         Path=Time.TotalSeconds}
```

O `Time` propriedade é do tipo `TimeSpan`, que tem um `TotalSeconds` propriedade. O `Time` e `TotalSeconds` propriedades são simplesmente conectadas com um período. Os itens a `Path` cadeia de caracteres sempre se referem a propriedades e não aos tipos dessas propriedades.

Que o exemplo e várias outras são mostradas com o **variações de caminho** página:

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

No segundo `Label`, a origem da associação é a página propriamente dita. O `Content` propriedade é do tipo `StackLayout`, que tem um `Children` propriedade do tipo `IList<View>`, que tem um `Count` propriedade que indica o número de filhos.

## <a name="paths-with-indexers"></a>Caminhos com indexadores

A associação no terceiro `Label` no **variações de caminho** páginas referências a [ `CultureInfo` ](xref:System.Globalization.CultureInfo) classe no `System.Globalization` namespace:

```xaml
<Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                      Path=DateTimeFormat.DayNames[3],
                      StringFormat='The middle day of the week is {0}'}" />
```

A origem é definida como estático `CultureInfo.CurrentCulture` propriedade, que é um objeto do tipo `CultureInfo`. Classe define uma propriedade chamada `DateTimeFormat` do tipo [ `DateTimeFormatInfo` ](xref:System.Globalization.DateTimeFormatInfo) que contém um `DayNames` coleção. O índice seleciona o item quarto.

O quarto `Label` faz algo similar, mas para a cultura associada com França. O `Source` propriedade da associação é definida como `CultureInfo` objeto com um construtor:

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

Ver [passar argumentos de construtor](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) para obter mais detalhes sobre como especificar argumentos de construtor em XAML.

Por fim, o último exemplo é semelhante ao segundo, exceto que ele faz referência a um dos filhos a `StackLayout`:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content.Children[1].Text.Length,
                      StringFormat='The first Label has {0} characters'}" />
```

Esse filho é um `Label`, que tem um `Text` propriedade do tipo `String`, que tem um `Length` propriedade. A primeira `Label` relatórios a `TimeSpan` definida `TimePicker`, portanto, quando esse texto é alterado, o último `Label` alterações também.

Aqui está o programa em execução em todas as três plataformas:

[![Variações de caminho](binding-path-images/pathvariations-small.png "variações de caminho")](binding-path-images/pathvariations-large.png#lightbox "variações de caminho")

## <a name="debugging-complex-paths"></a>Caminhos complexos de depuração

Definições de caminho complexo podem ser difícil construir: você precisa saber o tipo de cada propriedade sub ou o tipo dos itens na coleção para adicionar corretamente a subpropriedade próxima, mas os tipos em si não aparecem no caminho. É uma boa técnica criar o caminho de forma incremental e examinar os resultados intermediários. Para esse último exemplo, você pode começar com nenhum `Path` definição todos:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      StringFormat='{0}'}" />
```

Que exibe o tipo da origem da associação, ou `DataBindingDemos.PathVariationsPage`. Você sabe `PathVariationsPage` deriva `ContentPage`, portanto, ele tem um `Content` propriedade:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content,
                      StringFormat='{0}'}" />
```

O tipo dos `Content` propriedade agora é revelada ser `Xamarin.Forms.StackLayout`. Adicione a `Children` propriedade para o `Path` e o tipo é `Xamarin.Forms.ElementCollection'1[Xamarin.Forms.View]`, que é uma classe interna para xamarin. Forms, mas, obviamente, um tipo de coleção. Adicionar um índice a ele e o tipo é `Xamarin.Forms.Label`. Continue dessa maneira.

Como o xamarin. Forms processa o caminho de associação, ele instala um `PropertyChanged` manipulador em qualquer objeto no caminho que implementa o `INotifyPropertyChanged` interface. Por exemplo, a associação final reage a uma alteração no primeiro `Label` porque o `Text` alterações de propriedade.

Se uma propriedade no caminho de associação não implementa `INotifyPropertyChanged`, todas as alterações a essa propriedade serão ignoradas. Algumas alterações inteiramente poderia invalidar o caminho de associação, você deve usar essa técnica somente quando a cadeia de caracteres de propriedades e subpropriedades nunca serão invalidadas.



## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (amostra)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de associação de dados do catálogo de xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
