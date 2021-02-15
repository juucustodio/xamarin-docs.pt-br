---
title: Genéricos em Xamarin.Forms XAML
description: Xamarin.Forms O XAML fornece suporte para o consumo de tipos CLR genéricos especificando as restrições genéricas como argumentos de tipo.
ms.prod: xamarin
ms.assetid: 97B73048-4F90-41AD-AB48-8EB804C4998B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 05d0917ac748ca27350f43ae225f1cb1d1aad84f
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375376"
---
# <a name="generics-in-no-locxamarinforms-xaml"></a>Genéricos em Xamarin.Forms XAML

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/xaml-generics/)

Xamarin.Forms O XAML fornece suporte para o consumo de tipos CLR genéricos especificando as restrições genéricas como argumentos de tipo. Esse suporte é fornecido pela `x:TypeArguments` diretiva, que passa os argumentos de tipo restrito de um genérico para o construtor do tipo genérico.

> [!IMPORTANT]
> Xamarin.FormsNão há suporte para definir classes genéricas em XAML, com a `x:TypeArguments` diretiva.

Os argumentos de tipo são especificados como uma cadeia de caracteres e, normalmente, são prefixados, como `sys:String` e `sys:Int32` . A prefixação é necessária porque os tipos típicos de restrições genéricas do CLR são provenientes de bibliotecas que não são mapeadas para o Xamarin.Forms namespace padrão. No entanto, os tipos internos XAML 2009, como `x:String` e `x:Int32` , também podem ser especificados como argumentos de tipo, em que `x` é o namespace da linguagem xaml para XAML 2009. Para obter mais informações sobre os tipos internos XAML 2009, consulte [primitivos de linguagem xaml 2009](/dotnet/desktop-wpf/xaml-services/types-for-primitives#xaml-2009-language-primitives).

Vários argumentos de tipo podem ser especificados usando um delimitador de vírgula. Além disso, se uma restrição genérica usar tipos genéricos, os argumentos de tipo de restrição aninhada deverão ser contidos entre parênteses.

> [!NOTE]
> A `x:Type` extensão de marcação fornece uma referência de tipo CLR para um tipo genérico e tem uma função semelhante para o `typeof` operador em C#. Para obter mais informações, consulte a [extensão de marcação x:Type](~/xamarin-forms/xaml/markup-extensions/consuming.md#xtype-markup-extension).

## <a name="single-primitive-type-argument"></a>Argumento de tipo primitivo único

Um único argumento de tipo primitivo pode ser especificado como um argumento de cadeia de caracteres prefixado usando a `x:TypeArguments` diretiva:

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

Neste exemplo, `System.Collections.Generic` é definido como o `scg` namespace XAML. A `CollectionView.ItemsSource` propriedade é definida como uma `List<T>` instanciada com um argumento de `string` tipo, usando o tipo interno XAML 2009 `x:String` . A `List<string>` coleção é inicializada com vários `string` itens.

Como alternativa, mas de forma equivalente, a `List<T>` coleção pode ser instanciada com o `String` tipo CLR:

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

Um argumento de tipo de objeto único pode ser especificado como um argumento de cadeia de caracteres prefixado usando a `x:TypeArguments` diretiva:

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

Neste exemplo, `GenericsDemo.Models` é definido como o `models` namespace XAML e `System.Collections.Generic` é definido como o `scg` namespace XAML. A `CollectionView.ItemsSource` propriedade é definida como uma `List<T>` instanciada com um argumento de `Monkey` tipo. A `List<Monkey>` coleção é inicializada com vários `Monkey` itens e um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que define a aparência de cada `Monkey` objeto é definido como o `ItemTemplate` do [`CollectionView`](xref:Xamarin.Forms.CollectionView) .

## <a name="multiple-type-arguments"></a>Argumentos de vários tipos

Vários argumentos de tipo podem ser especificados como argumentos de cadeia de Caracteres prefixados, delimitados por uma vírgula, usando a `x:TypeArguments` diretiva. Quando uma restrição genérica usa tipos genéricos, os argumentos de tipo de restrição aninhada são contidos entre parênteses:

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

Neste exemplo, `GenericsDemo.Models` é definido como o `models` namespace XAML e `System.Collections.Generic` é definido como o `scg` namespace XAML. A `CollectionView.ItemsSource` propriedade é definida como uma `List<T>` instanciada com uma `KeyValuePair<TKey, TValue>` restrição, com os argumentos de tipo de restrição interna `string` e `Monkey` . A `List<KeyValuePair<string,Monkey>>` coleção é inicializada com vários `KeyValuePair` itens, usando o construtor não padrão `KeyValuePair` e um [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que define a aparência de cada `Monkey` objeto é definido como o `ItemTemplate` do [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Para obter informações sobre como passar argumentos para um construtor não padrão, consulte [passando argumentos de Construtor](~/xamarin-forms/xaml/passing-arguments.md#passing-constructor-arguments).

## <a name="related-links"></a>Links relacionados

- [Genéricos em XAML (exemplo)](/samples/xamarin/xamarin-forms-samples/xaml-generics/)
- [Primitivos de linguagem XAML 2009](/dotnet/desktop-wpf/xaml-services/types-for-primitives#xaml-2009-language-primitives)
- [extensão de marcação x:Type](~/xamarin-forms/xaml/markup-extensions/consuming.md#xtype-markup-extension)
- [Passando argumentos de Construtor](~/xamarin-forms/xaml/passing-arguments.md#passing-constructor-arguments)