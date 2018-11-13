---
title: Passando argumentos em XAML
description: Este artigo demonstra como usar os atributos XAML que podem ser usados para passar argumentos para o construtor não padrão, para chamar métodos de fábrica e para especificar o tipo de um argumento genérico.
ms.prod: xamarin
ms.assetid: 8F3B267F-499E-4D79-9193-FCA99F199519
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2016
ms.openlocfilehash: 60ca55999033adc375a139195c9c3e62da6d0184
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563752"
---
# <a name="passing-arguments-in-xaml"></a>Passando argumentos em XAML

_Este artigo demonstra como usar os atributos XAML que podem ser usados para passar argumentos para o construtor não padrão, para chamar métodos de fábrica e para especificar o tipo de um argumento genérico._

## <a name="overview"></a>Visão geral

Ele geralmente é necessário instanciar objetos com construtores que requerem argumentos, ou chamando um método de criação estáticos. Isso pode ser obtido em XAML usando o `x:Arguments` e `x:FactoryMethod` atributos:

- O `x:Arguments` atributo é usado para especificar argumentos de construtor para um construtor não padrão ou para uma declaração de objeto do método de fábrica. Para obter mais informações, consulte [passar argumentos de construtor](#constructor_arguments).
- O `x:FactoryMethod` atributo é usado para especificar um método de fábrica que pode ser usado para inicializar um objeto. Para obter mais informações, consulte [chamando métodos de fábrica](#factory_methods).

Além disso, o `x:TypeArguments` atributo pode ser usado para especificar os argumentos de tipo genérico para o construtor de um tipo genérico. Para obter mais informações, consulte [especificando um argumento de tipo genérico](#generic_type_arguments).

<a name="constructor_arguments" />

## <a name="passing-constructor-arguments"></a>Passando argumentos de construtor

Argumentos podem ser passados para um construtor não padrão usando o `x:Arguments` atributo. Cada argumento de construtor deve ser delimitado dentro de um elemento XML que representa o tipo do argumento. Xamarin. Forms dá suporte aos seguintes elementos para tipos básicos:

- `x:Object`
- `x:Boolean`
- `x:Byte`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Single`
- `x:Double`
- `x:Decimal`
- `x:Char`
- `x:String`
- `x:TimeSpan`
- `x:Array`
- `x:DateTime`

O exemplo de código a seguir demonstra como usar o `x:Arguments` atributo com três [ `Color` ](xref:Xamarin.Forms.Color) construtores:

```xaml
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.9</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.25</x:Double>
        <x:Double>0.5</x:Double>
        <x:Double>0.75</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color>
      <x:Arguments>
        <x:Double>0.8</x:Double>
        <x:Double>0.5</x:Double>
        <x:Double>0.2</x:Double>
        <x:Double>0.5</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
```

O número de elementos dentro de `x:Arguments` marca e os tipos desses elementos devem corresponder a um dos [ `Color` ](xref:Xamarin.Forms.Color) construtores. O `Color` [construtor](xref:Xamarin.Forms.Color.%23ctor(System.Double)) com um único parâmetro requer um valor de escala de cinza de 0 (preto) a 1 (branco). O `Color` [construtor](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double)) com três parâmetros requer um valor de vermelho, verde e azul, variando de 0 a 1. O `Color` [construtor](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double)) com quatro parâmetros adiciona um canal alfa, como o quarto parâmetro.

As capturas de tela a seguir mostram o resultado da chamada a cada [ `Color` ](xref:Xamarin.Forms.Color) construtor com os valores de argumento especificado:

![](passing-arguments-images/passing-arguments.png "BoxView.Color especificado com argumentos de x:")

<a name="factory_methods" />

## <a name="calling-factory-methods"></a>Chamar métodos de fábrica

Métodos de fábrica podem ser chamados em XAML, especificando o método nome usando o `x:FactoryMethod` atributo e seus argumentos usando o `x:Arguments` atributo. Um método de fábrica é um `public static` método que retorna os valores do mesmo tipo que a classe ou estrutura que define os métodos ou objetos.

O [ `Color` ](xref:Xamarin.Forms.Color) estrutura define vários métodos de fábrica e o exemplo de código a seguir demonstra a chamada três argumentos:

```xaml
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromRgba">
      <x:Arguments>
        <x:Int32>192</x:Int32>
        <x:Int32>75</x:Int32>
        <x:Int32>150</x:Int32>                        
        <x:Int32>128</x:Int32>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromHsla">
      <x:Arguments>
        <x:Double>0.23</x:Double>
        <x:Double>0.42</x:Double>
        <x:Double>0.69</x:Double>
        <x:Double>0.7</x:Double>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
<BoxView HeightRequest="150" WidthRequest="150" HorizontalOptions="Center">
  <BoxView.Color>
    <Color x:FactoryMethod="FromHex">
      <x:Arguments>
        <x:String>#FF048B9A</x:String>
      </x:Arguments>
    </Color>
  </BoxView.Color>
</BoxView>
```

O número de elementos dentro de `x:Arguments` marca e os tipos desses elementos devem coincidir com os argumentos de método de fábrica que está sendo chamado. O [ `FromRgba` ](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) método de fábrica exige quatro [ `Int32` ](https://docs.microsoft.com/dotnet/api/system.int32) parâmetros, que representam os valores de vermelhos, verdes, azuis e alfabéticos, variando de 0 a 255, respectivamente. O [ `FromHsla` ](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) método de fábrica exige quatro [ `Double` ](https://docs.microsoft.com/dotnet/api/system.double) parâmetros, que representam o matiz, saturação, luminosidade e valores alfa, variando de 0 a 1, respectivamente. O [ `FromHex` ](xref:Xamarin.Forms.Color.FromHex(System.String)) método de fábrica exige uma [ `String` ](https://docs.microsoft.com/dotnet/api/system.string) que representa o hexadecimal (A) de cor RGB.

As capturas de tela a seguir mostram o resultado da chamada a cada [ `Color` ](xref:Xamarin.Forms.Color) método de fábrica com os valores de argumento especificado:

![](passing-arguments-images/factory-methods.png "BoxView.Color especificado com X:factorymethod e argumentos de x:")

<a name="generic_type_arguments" />

## <a name="specifying-a-generic-type-argument"></a>Especificando um argumento de tipo genérico

Argumentos de tipo genérico para o construtor de um tipo genérico podem ser especificados usando o `x:TypeArguments` de atributo, conforme demonstrado no exemplo de código a seguir:

```xaml
<ContentPage ...>
  <StackLayout>
    <StackLayout.Margin>
      <OnPlatform x:TypeArguments="Thickness">
        <On Platform="iOS" Value="0,20,0,0" />
        <On Platform="Android" Value="5, 10" />
        <On Platform="UWP" Value="10" />
      </OnPlatform>
    </StackLayout.Margin>
  </StackLayout>
</ContentPage>
```

O [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) classe é uma classe genérica e deve ser instanciado com um `x:TypeArguments` atributo que corresponde ao tipo de destino. No [ `On` ](xref:Xamarin.Forms.On) classe, o [ `Platform` ](xref:Xamarin.Forms.On.Platform) atributo pode aceitar um único `string` valor ou vários delimitada por vírgula `string` valores. Neste exemplo, o [ `StackLayout.Margin` ](xref:Xamarin.Forms.View.Margin) estiver definida como uma plataforma específica [ `Thickness` ](xref:Xamarin.Forms.Thickness).

## <a name="summary"></a>Resumo

Este artigo demonstrou usando os atributos XAML que podem ser usados para passar argumentos para o construtor não padrão, para chamar métodos de fábrica e para especificar o tipo de um argumento genérico.


## <a name="related-links"></a>Links relacionados

- [Namespaces XAML](~/xamarin-forms/xaml/namespaces.md)
- [Passando argumentos de construtor (amostra)](https://developer.xamarin.com/samples/xamarin-forms/xaml/passingconstructorarguments/)
- [Chamar métodos de fábrica (amostra)](https://developer.xamarin.com/samples/xamarin-forms/xaml/callingfactorymethods/)
