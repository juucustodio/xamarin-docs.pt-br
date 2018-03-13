---
title: "Caminho de associação"
description: "Usar associações de dados para membros da coleção e acesso subpropriedades"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3CF721A5-E157-468B-AD3A-DA0A45E58E8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: fb385a9c7d1dfd01d95691b77122cdbb84d814e5
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="binding-path"></a>Caminho de associação

Em todos os exemplos de associação de dados da anteriores, o [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Binding.Path/) propriedade o `Binding` classe (ou o [ `Path` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Xaml.BindingExtension.Path/) propriedade do `Binding` extensão de marcação) foi definida para uma única propriedade. Ele é, na verdade, é possível definir `Path` para um *subpropriedade* (uma propriedade de uma propriedade), ou a um membro de uma coleção.

Por exemplo, suponha que a página contiver uma `TimePicker`:

```xaml
<TimePicker x:Name="timePicker">
```

O `Time` propriedade de `TimePicker` é do tipo `TimeSpan`, mas talvez você queira criar uma associação de dados que faz referência a `TotalSeconds` propriedade que `TimeSpan` valor. Aqui está a associação de dados:

```xaml
{Binding Source={x:Reference timePicker},
         Path=Time.TotalSeconds}
```
         
O `Time` é de propriedade do tipo `TimeSpan`, que tem um `TotalSeconds` propriedade. O `Time` e `TotalSeconds` propriedades são simply conectadas com um ponto. Os itens a `Path` cadeia de caracteres sempre se referem às propriedades e não aos tipos dessas propriedades.

Exemplo e vários outros são mostrados no **variações de caminho** página:

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
                              StringFormat='The first Label has {0} characters'}" />
    </StackLayout>
</ContentPage>
```

No segundo `Label`, a fonte da associação é a página propriamente dita. O `Content` é de propriedade do tipo `StackLayout`, que tem um `Children` propriedade do tipo `IList<View>`, que tem um `Count` propriedade que indica o número de filhos.

## <a name="paths-with-indexers"></a>Caminhos de indexadores

A associação no terceiro `Label` no **variações de caminho** páginas referências a [ `CultureInfo` ](https://developer.xamarin.com/api/type/System.Globalization.CultureInfo/) classe no `System.Globalization` namespace:

```xaml
<Label Text="{Binding Source={x:Static globe:CultureInfo.CurrentCulture},
                      Path=DateTimeFormat.DayNames[3],
                      StringFormat='The middle day of the week is {0}'}" />
```

A origem é definida como estático `CultureInfo.CurrentCulture` propriedade, que é um objeto do tipo `CultureInfo`. Classe define uma propriedade chamada `DateTimeFormat` do tipo [ `DateTimeFormatInfo` ](https://developer.xamarin.com/api/type/System.Globalization.DateTimeFormatInfo/) que contém um `DayNames` coleção. O índice seleciona o quarto item.

O quarto `Label` faz algo semelhante, mas, para a cultura associada França. O `Source` propriedade da associação está definida como `CultureInfo` objeto com um construtor:

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

Consulte [passar argumentos de construtor](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments) para obter mais detalhes sobre como especificar argumentos de construtor em XAML.

Por fim, o último exemplo é semelhante para o segundo, exceto que ele faz referência a um dos filhos do `StackLayout`:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content.Children[1].Text.Length,
                      StringFormat='The first Label has {0} characters'}" />
```

Esse filho é um `Label`, que tem um `Text` propriedade do tipo `String`, que tem um `Length` propriedade. A primeira `Label` relatórios o `TimeSpan` definido `TimePicker`, portanto, quando esse texto é alterado, o último `Label` as alterações.

Aqui está o programa em execução em todas as três plataformas:

[![Variações de caminho](binding-path-images/pathvariations-small.png "variações de caminho")](binding-path-images/pathvariations-large.png#lightbox "variações de caminho")

## <a name="debugging-complex-paths"></a>Caminhos complexos de depuração

Definições de caminho complexas podem ser difíceis de construir: você precisa saber o tipo de cada propriedade sub ou o tipo de itens na coleção para adicionar corretamente a próxima propriedade sub, mas os tipos em si não aparecem no caminho. Uma boa técnica é criar o caminho de forma incremental e examinar os resultados intermediários. Esse último exemplo, você pode começar com nenhum `Path` definição em todos os:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      StringFormat='{0}'}" />
```

Que exibe o tipo de fonte de associação, ou `DataBindingDemos.PathVariationsPage`. Você sabe `PathVariationsPage` deriva `ContentPage`; portanto, tem um `Content` propriedade:

```xaml
<Label Text="{Binding Source={x:Reference page},
                      Path=Content,
                      StringFormat='{0}'}" />
```

O tipo do `Content` propriedade agora é revelada ser `Xamarin.Forms.StackLayout`. Adicionar o `Children` propriedade para o `Path` e o tipo é `Xamarin.Forms.ElementCollection'1[Xamarin.Forms.View]`, que é uma classe interna para xamarin. Forms, mas, obviamente, um tipo de coleção. Adicionar um índice para que e o tipo é `Xamarin.Forms.Label`. Continue dessa maneira.

Como xamarin. Forms processa o caminho de associação, ele instala um `PropertyChanged` manipulador em qualquer objeto no caminho que implementa o `INotifyPropertyChanged` interface. Por exemplo, a associação final reage a uma alteração na primeira `Label` porque o `Text` alterações de propriedade. 

Se uma propriedade no caminho de associação não implementa `INotifyPropertyChanged`, as alterações a essa propriedade serão ignoradas. Algumas alterações inteiramente podem invalidar o caminho de associação, então você deve usar essa técnica somente quando a cadeia de caracteres de propriedades e subpropriedades nunca se tornam inválidos.



## <a name="related-links"></a>Links relacionados

- [Demonstrações de associação de dados (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capítulo de associação de dados do catálogo de xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
