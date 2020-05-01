---
title: Genéricos no XAML do Xamarin. Forms
description: O XAML do Xamarin. Forms fornece suporte para o consumo de tipos CLR genéricos especificando as restrições genéricas como argumentos de tipo.
ms.prod: xamarin
ms.assetid: 97B73048-4F90-41AD-AB48-8EB804C4998B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/28/2020
ms.openlocfilehash: 9cda08a3bab0e25db2315c9795721e25d47d2429
ms.sourcegitcommit: 154a3e7aec775327565bb54eda1a610976af1d6f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82624703"
---
# <a name="generics-in-xamarinforms-xaml"></a>Genéricos no XAML do Xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-generics/)

O XAML do Xamarin. Forms fornece suporte para o consumo de tipos CLR genéricos especificando as restrições genéricas como argumentos de tipo. Esse suporte é fornecido pela `x:TypeArguments` diretiva, que passa os argumentos de tipo restrito de um genérico para o construtor do tipo genérico.

> [!IMPORTANT]
> Não há suporte para definir classes genéricas no XAML do Xamarin `x:TypeArguments` . Forms, com a diretiva.

Os argumentos de tipo são especificados como uma cadeia de caracteres e, normalmente, são `sys:String` prefixados, como e `sys:Int32`. A prefixação é necessária porque os tipos típicos de restrições genéricas do CLR vêm de bibliotecas que não estão mapeadas para o namespace padrão do Xamarin. Forms. No entanto, os tipos internos XAML 2009, como `x:String` e `x:Int32`, também podem ser especificados como argumentos de tipo, `x` em que é o namespace da linguagem XAML para XAML 2009. Para obter mais informações sobre os tipos internos XAML 2009, consulte [primitivos de linguagem xaml 2009](/dotnet/desktop-wpf/xaml-services/types-for-primitives#xaml-2009-language-primitives).

Vários argumentos de tipo podem ser especificados usando um delimitador de vírgula. Além disso, se uma restrição genérica usar tipos genéricos, os argumentos de tipo de restrição aninhada deverão ser contidos entre parênteses.

> [!NOTE]
> A `x:Type` extensão de marcação fornece uma referência de tipo CLR para um tipo genérico e tem uma função semelhante para `typeof` o operador em C#. Para obter mais informações, consulte a [extensão de marcação x:Type](~/xamarin-forms/xaml/markup-extensions/consuming.md#type).

## <a name="single-primitive-type-argument"></a>Argumento de tipo primitivo único

Um único argumento de tipo primitivo pode ser especificado como um argumento de cadeia de caracteres `x:TypeArguments` prefixado usando a diretiva:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:scg="clr-namespace:System.Collections.Generic;assembly=netstandard"
             ...>
    <CollectionView>
        <CollectionView.ItemsSource>
            <scg:List x:TypeArguments="x:String">
                <x:String>Baboon</x:String>
                <x:String>Capuchin Monkey</x:String>
                <x:String>Blue Monkey</x:String>
                <x:String>Squirrel Monkey</x:String>
                <x:String>Golden Lion Tamarin</x:String>
                <x:String>Howler Monkey</x:String>
                <x:String>Japanese Macaque</x:String>
            </scg:List>
        </CollectionView.ItemsSource>
    </CollectionView>
</ContentPage>
```

Neste exemplo, `System.Collections.Generic` é definido como o `scg` namespace XAML. A `CollectionView.ItemsSource` propriedade é definida como uma `List<T>` instanciada com um `string` argumento de tipo, usando o `x:String` tipo interno XAML 2009. A `List<string>` coleção é inicializada com `string` vários itens.

Como alternativa, mas de forma equivalente, `List<T>` a coleção pode ser instanciada com o `String` tipo CLR:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:scg="clr-namespace:System.Collections.Generic;assembly=netstandard"
             xmlns:sys="clr-namespace:System;assembly=netstandard"
             ...>
    <CollectionView>
        <CollectionView.ItemsSource>
            <scg:List x:TypeArguments="sys:String">
                <sys:String>Baboon</sys:String>
                <sys:String>Capuchin Monkey</sys:String>
                <sys:String>Blue Monkey</sys:String>
                <sys:String>Squirrel Monkey</sys:String>
                <sys:String>Golden Lion Tamarin</sys:String>
                <sys:String>Howler Monkey</sys:String>
                <sys:String>Japanese Macaque</sys:String>
            </scg:List>
        </CollectionView.ItemsSource>
    </CollectionView>
</ContentPage>
```

## <a name="single-object-type-argument"></a>Argumento de tipo de objeto único

Um argumento de tipo de objeto único pode ser especificado como um argumento de cadeia de `x:TypeArguments` caracteres prefixado usando a diretiva:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:models="clr-namespace:GenericsDemo.Models"
             xmlns:scg="clr-namespace:System.Collections.Generic;assembly=netstandard"
             ...>
    <CollectionView>
        <CollectionView.ItemsSource>
            <scg:List x:TypeArguments="models:Monkey">
                <models:Monkey Name="Baboon"
                               Location="Africa and Asia"
                               ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg" />
                <models:Monkey Name="Capuchin Monkey"
                               Location="Central and South America"
                               ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg" />
                <models:Monkey Name="Blue Monkey"
                               Location="Central and East Africa"
                               ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg" />
            </scg:List>
        </CollectionView.ItemsSource>
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Grid Padding="10">
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="Auto" />
                    </Grid.ColumnDefinitions>
                    <Image Grid.RowSpan="2"
                           Source="{Binding ImageUrl}"
                           Aspect="AspectFill"
                           HeightRequest="60"
                           WidthRequest="60" />
                    <Label Grid.Column="1"
                           Text="{Binding Name}"
                           FontAttributes="Bold" />
                    <Label Grid.Row="1"
                           Grid.Column="1"
                           Text="{Binding Location}"
                           FontAttributes="Italic"
                           VerticalOptions="End" />
                </Grid>
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
</ContentPage>
```

Neste `GenericsDemo.Models` exemplo, é definido como `models` o namespace XAML e `System.Collections.Generic` é definido como o `scg` namespace XAML. A `CollectionView.ItemsSource` propriedade é definida como uma `List<T>` instanciada com um `Monkey` argumento de tipo. A `List<Monkey>` coleção é inicializada com `Monkey` vários itens e um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que define a aparência de cada `Monkey` objeto é definido como o `ItemTemplate` do [`CollectionView`](xref:Xamarin.Forms.CollectionView).

## <a name="multiple-type-arguments"></a>Argumentos de vários tipos

Vários argumentos de tipo podem ser especificados como argumentos de cadeia de Caracteres prefixados, delimitados `x:TypeArguments` por uma vírgula, usando a diretiva. Quando uma restrição genérica usa tipos genéricos, os argumentos de tipo de restrição aninhada são contidos entre parênteses:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:models="clr-namespace:GenericsDemo.Models"
             xmlns:scg="clr-namespace:System.Collections.Generic;assembly=netstandard"
             ...>
    <CollectionView>
        <CollectionView.ItemsSource>
            <scg:List x:TypeArguments="scg:KeyValuePair(x:String,models:Monkey)">
                <scg:KeyValuePair x:TypeArguments="x:String,models:Monkey">
                    <x:Arguments>
                        <x:String>Baboon</x:String>
                        <models:Monkey Location="Africa and Asia"
                                       ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg" />
                    </x:Arguments>
                </scg:KeyValuePair>
                <scg:KeyValuePair x:TypeArguments="x:String,models:Monkey">
                    <x:Arguments>
                        <x:String>Capuchin Monkey</x:String>
                        <models:Monkey Location="Central and South America"
                                       ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg" />   
                    </x:Arguments>
                </scg:KeyValuePair>
                <scg:KeyValuePair x:TypeArguments="x:String,models:Monkey">
                    <x:Arguments>
                        <x:String>Blue Monkey</x:String>
                        <models:Monkey Location="Central and East Africa"
                                       ImageUrl="https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg" />
                    </x:Arguments>
                </scg:KeyValuePair>
            </scg:List>
        </CollectionView.ItemsSource>
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Grid Padding="10">
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto" />
                        <ColumnDefinition Width="Auto" />
                    </Grid.ColumnDefinitions>
                    <Image Grid.RowSpan="2"
                           Source="{Binding Value.ImageUrl}"
                           Aspect="AspectFill"
                           HeightRequest="60"
                           WidthRequest="60" />
                    <Label Grid.Column="1"
                           Text="{Binding Key}"
                           FontAttributes="Bold" />
                    <Label Grid.Row="1"
                           Grid.Column="1"
                           Text="{Binding Value.Location}"
                           FontAttributes="Italic"
                           VerticalOptions="End" />
                </Grid>
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
</ContentPage    
```

Neste `GenericsDemo.Models` exemplo, é definido como `models` o namespace XAML e `System.Collections.Generic` é definido como o `scg` namespace XAML. A `CollectionView.ItemsSource` propriedade é definida como uma `List<T>` instanciada com uma `KeyValuePair<TKey, TValue>` restrição, com os argumentos `string` de tipo de restrição interna e `Monkey`. A `List<KeyValuePair<string,Monkey>>` coleção é inicializada com `KeyValuePair` vários itens, usando o construtor não `KeyValuePair` padrão e [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) um que define a aparência de `Monkey` cada objeto é definido como o `ItemTemplate` do. [`CollectionView`](xref:Xamarin.Forms.CollectionView) Para obter informações sobre como passar argumentos para um construtor não padrão, consulte [passando argumentos de Construtor](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments).

## <a name="related-links"></a>Links relacionados

- [Genéricos em XAML (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-generics/)
- [Primitivos de linguagem XAML 2009](/dotnet/desktop-wpf/xaml-services/types-for-primitives#xaml-2009-language-primitives)
- [extensão de marcação x:Type](~/xamarin-forms/xaml/markup-extensions/consuming.md#type)
- [Passando argumentos de Construtor](~/xamarin-forms/xaml/passing-arguments.md#constructor_arguments)
