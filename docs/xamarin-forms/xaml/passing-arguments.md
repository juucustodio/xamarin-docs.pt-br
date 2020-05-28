---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f314f3145b3573184cb8cdf7370394c975c66859
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84127069"
---
# <a name="passing-arguments-in-xaml"></a>Passando argumentos em XAML

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)

_Este artigo demonstra como usar os atributos XAML que podem ser usados para passar argumentos para construtores não padrão, para chamar métodos de fábrica e para especificar o tipo de um argumento genérico._

## <a name="overview"></a>Visão geral

Geralmente, é necessário criar uma instância de objetos com construtores que exigem argumentos ou chamando um método de criação estático. Isso pode ser obtido em XAML usando os `x:Arguments` atributos e `x:FactoryMethod` :

- O `x:Arguments` atributo é usado para especificar argumentos de construtor para um construtor não padrão ou para uma declaração de objeto de método de fábrica. Para obter mais informações, consulte [passando argumentos de Construtor](#constructor_arguments).
- O `x:FactoryMethod` atributo é usado para especificar um método de fábrica que pode ser usado para inicializar um objeto. Para obter mais informações, consulte [chamando métodos de fábrica](#factory_methods).

Além disso, o `x:TypeArguments` atributo pode ser usado para especificar os argumentos de tipo genérico para o construtor de um tipo genérico. Para obter mais informações, consulte [especificando um argumento de tipo genérico](#generic_type_arguments).

<a name="constructor_arguments" />

## <a name="passing-constructor-arguments"></a>Passando argumentos de Construtor

Os argumentos podem ser passados para um construtor não padrão usando o `x:Arguments` atributo. Cada argumento de construtor deve ser delimitado em um elemento XML que representa o tipo do argumento. Xamarin.Formso oferece suporte aos seguintes elementos para tipos básicos:

- `x:Array`
- `x:Boolean`
- `x:Byte`
- `x:Char`
- `x:DateTime`
- `x:Decimal`
- `x:Double`
- `x:Int16`
- `x:Int32`
- `x:Int64`
- `x:Object`
- `x:Single`
- `x:String`
- `x:TimeSpan`

O exemplo de código a seguir demonstra como usar o `x:Arguments` atributo com três [`Color`](xref:Xamarin.Forms.Color) construtores:

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

O número de elementos dentro da `x:Arguments` marca e os tipos desses elementos devem corresponder a um dos [`Color`](xref:Xamarin.Forms.Color) construtores. O `Color` [Construtor](xref:Xamarin.Forms.Color.%23ctor(System.Double)) com um único parâmetro requer um valor de escala de cinza de 0 (preto) para 1 (branco). O `Color` [Construtor](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double)) com três parâmetros requer um valor vermelho, verde e azul, que varia de 0 a 1. O `Color` [Construtor](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double)) com quatro parâmetros adiciona um canal alfa como o quarto parâmetro.

As capturas de tela a seguir mostram o resultado da chamada de cada [`Color`](xref:Xamarin.Forms.Color) Construtor com os valores de argumento especificados:

![BoxView. Color especificada com x:Arguments](passing-arguments-images/passing-arguments.png)

<a name="factory_methods" />

## <a name="calling-factory-methods"></a>Chamando métodos de fábrica

Os métodos de fábrica podem ser chamados em XAML especificando o nome do método usando o `x:FactoryMethod` atributo e seus argumentos usando o `x:Arguments` atributo. Um método de fábrica é um `public static` método que retorna objetos ou valores do mesmo tipo da classe ou estrutura que define os métodos.

A [`Color`](xref:Xamarin.Forms.Color) estrutura define um número de métodos de fábrica e o exemplo de código a seguir demonstra como chamar três deles:

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

O número de elementos dentro da `x:Arguments` marca e os tipos desses elementos devem corresponder aos argumentos do método de fábrica que está sendo chamado. O [`FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) método de fábrica requer quatro [`Int32`](https://docs.microsoft.com/dotnet/api/system.int32) parâmetros, que representam os valores vermelho, verde, azul e alfa, variando de 0 a 255, respectivamente. O [`FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) método de fábrica requer quatro [`Double`](https://docs.microsoft.com/dotnet/api/system.double) parâmetros, que representam os valores de matiz, saturação, luminosidade e alfa, variando de 0 a 1, respectivamente. O [`FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String)) método de fábrica requer um [`String`](https://docs.microsoft.com/dotnet/api/system.string) que represente a cor hexadecimal (a) RGB.

As capturas de tela a seguir mostram o resultado da chamada de cada [`Color`](xref:Xamarin.Forms.Color) método de fábrica com os valores de argumento especificados:

![BoxView. Color especificada com x:FactoryMethod e x:Arguments](passing-arguments-images/factory-methods.png)

<a name="generic_type_arguments" />

## <a name="specifying-a-generic-type-argument"></a>Especificando um argumento de tipo genérico

Argumentos de tipo genérico para o construtor de um tipo genérico podem ser especificados usando o `x:TypeArguments` atributo, conforme demonstrado no exemplo de código a seguir:

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

A [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) classe é uma classe genérica e deve ser instanciada com um `x:TypeArguments` atributo que corresponda ao tipo de destino. Na [`On`](xref:Xamarin.Forms.On) classe, o [`Platform`](xref:Xamarin.Forms.On.Platform) atributo pode aceitar um único `string` valor ou vários valores delimitados por vírgula `string` . Neste exemplo, a [`StackLayout.Margin`](xref:Xamarin.Forms.View.Margin) propriedade é definida como uma plataforma específica [`Thickness`](xref:Xamarin.Forms.Thickness) .

Para obter mais informações sobre argumentos de tipo genérico, consulte [genéricos em Xamarin.Forms XAML](generics.md).

## <a name="related-links"></a>Links relacionados

- [Passando argumentos de Construtor (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)
- [Chamando métodos de fábrica (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-callingfactorymethods)
- [Namespaces XAML](~/xamarin-forms/xaml/namespaces.md)
- [Genéricos em Xamarin.Forms XAML](generics.md)
