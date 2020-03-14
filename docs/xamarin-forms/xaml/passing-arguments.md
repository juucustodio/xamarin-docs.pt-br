---
title: Passando argumentos em XAML
description: Este artigo demonstra como usar os atributos XAML que podem ser usados para passar argumentos para o construtor não padrão, para chamar métodos de fábrica e para especificar o tipo de um argumento genérico.
ms.prod: xamarin
ms.assetid: 8F3B267F-499E-4D79-9193-FCA99F199519
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2016
ms.openlocfilehash: 80f332e45d6c46ad49543923e85cbb2eceadb378
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305475"
---
# <a name="passing-arguments-in-xaml"></a>Passando argumentos em XAML

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)

_Este artigo demonstra como usar os atributos XAML que podem ser usados para passar argumentos para construtores não padrão, para chamar métodos de fábrica e para especificar o tipo de um argumento genérico._

## <a name="overview"></a>Visão geral

Ele geralmente é necessário instanciar objetos com construtores que requerem argumentos, ou chamando um método de criação estáticos. Isso pode ser obtido em XAML usando os atributos `x:Arguments` e `x:FactoryMethod`:

- O atributo `x:Arguments` é usado para especificar argumentos de construtor para um construtor não padrão ou para uma declaração de objeto de método de fábrica. Para obter mais informações, consulte [passando argumentos de Construtor](#constructor_arguments).
- O atributo `x:FactoryMethod` é usado para especificar um método de fábrica que pode ser usado para inicializar um objeto. Para obter mais informações, consulte [chamando métodos de fábrica](#factory_methods).

Além disso, o atributo `x:TypeArguments` pode ser usado para especificar os argumentos de tipo genérico para o construtor de um tipo genérico. Para obter mais informações, consulte [especificando um argumento de tipo genérico](#generic_type_arguments).

<a name="constructor_arguments" />

## <a name="passing-constructor-arguments"></a>Passando argumentos de construtor

Os argumentos podem ser passados para um construtor não padrão usando o atributo `x:Arguments`. Cada argumento de construtor deve ser delimitado dentro de um elemento XML que representa o tipo do argumento. Xamarin. Forms dá suporte aos seguintes elementos para tipos básicos:

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

O exemplo de código a seguir demonstra o uso do atributo `x:Arguments` com três construtores [`Color`](xref:Xamarin.Forms.Color) :

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

O número de elementos dentro da marca de `x:Arguments` e os tipos desses elementos devem corresponder a um dos construtores de [`Color`](xref:Xamarin.Forms.Color) . O [Construtor](xref:Xamarin.Forms.Color.%23ctor(System.Double)) de `Color` com um único parâmetro requer um valor de escala de cinza de 0 (preto) para 1 (branco). O [Construtor](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double)) de `Color` com três parâmetros requer um valor vermelho, verde e azul que varia de 0 a 1. O [Construtor](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double)) de `Color` com quatro parâmetros adiciona um canal alfa como o quarto parâmetro.

As capturas de tela a seguir mostram o resultado da chamada de cada Construtor de [`Color`](xref:Xamarin.Forms.Color) com os valores de argumento especificados:

![BoxView. Color especificada com x:Arguments](passing-arguments-images/passing-arguments.png)

<a name="factory_methods" />

## <a name="calling-factory-methods"></a>Chamar métodos de fábrica

Os métodos de fábrica podem ser chamados em XAML especificando o nome do método usando o atributo `x:FactoryMethod` e seus argumentos usando o atributo `x:Arguments`. Um método de fábrica é um método `public static` que retorna objetos ou valores do mesmo tipo que a classe ou estrutura que define os métodos.

A estrutura de [`Color`](xref:Xamarin.Forms.Color) define um número de métodos de fábrica e o exemplo de código a seguir demonstra como chamar três deles:

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

O número de elementos dentro da marca de `x:Arguments` e os tipos desses elementos devem corresponder aos argumentos do método de fábrica que está sendo chamado. O método de fábrica [`FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) requer quatro parâmetros [`Int32`](https://docs.microsoft.com/dotnet/api/system.int32) , que representam os valores vermelho, verde, azul e alfa, variando de 0 a 255, respectivamente. O método de fábrica de [`FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) requer quatro parâmetros [`Double`](https://docs.microsoft.com/dotnet/api/system.double) , que representam os valores de matiz, saturação, luminosidade e alfa, variando de 0 a 1, respectivamente. O método de fábrica [`FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String)) requer uma [`String`](https://docs.microsoft.com/dotnet/api/system.string) que represente A cor HEXADECIMAL (a) RGB.

As capturas de tela a seguir mostram o resultado da chamada de cada método de fábrica de [`Color`](xref:Xamarin.Forms.Color) com os valores de argumento especificados:

![BoxView. Color especificada com x:FactoryMethod e x:Arguments](passing-arguments-images/factory-methods.png)

<a name="generic_type_arguments" />

## <a name="specifying-a-generic-type-argument"></a>Especificando um argumento de tipo genérico

Argumentos de tipo genérico para o construtor de um tipo genérico podem ser especificados usando o atributo `x:TypeArguments`, conforme demonstrado no exemplo de código a seguir:

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

A classe [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) é uma classe genérica e deve ser instanciada com um atributo `x:TypeArguments` que corresponda ao tipo de destino. Na classe [`On`](xref:Xamarin.Forms.On) , o atributo [`Platform`](xref:Xamarin.Forms.On.Platform) pode aceitar um único valor de `string` ou vários valores de `string` delimitados por vírgula. Neste exemplo, a propriedade [`StackLayout.Margin`](xref:Xamarin.Forms.View.Margin) é definida como uma [`Thickness`](xref:Xamarin.Forms.Thickness)específica da plataforma.

## <a name="summary"></a>Resumo

Este artigo demonstrou usando os atributos XAML que podem ser usados para passar argumentos para o construtor não padrão, para chamar métodos de fábrica e para especificar o tipo de um argumento genérico.

## <a name="related-links"></a>Links Relacionados

- [Namespaces XAML](~/xamarin-forms/xaml/namespaces.md)
- [Passando argumentos de Construtor (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-passingconstructorarguments)
- [Chamando métodos de fábrica (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-callingfactorymethods)
