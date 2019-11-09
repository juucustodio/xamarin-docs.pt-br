---
title: Facilitando funções no Xamarin. Forms
description: O Xamarin. Forms inclui uma classe de atenuação que permite especificar uma função de transferência que controla como as animações se aceleram ou diminuem enquanto estão em execução. Este artigo demonstra como consumir as funções de atenuação predefinidas e como criar funções de atenuação personalizadas.
ms.prod: xamarin
ms.assetid: E6F124C7-A161-4C1F-AF40-52F0935E54DE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 56ea31d1e1be8bbad4a27dd7ffd844aa03f75bbb
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73842824"
---
# <a name="easing-functions-in-xamarinforms"></a>Facilitando funções no Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)

_O Xamarin. Forms inclui uma classe de atenuação que permite especificar uma função de transferência que controla como as animações se aceleram ou diminuem enquanto estão em execução. Este artigo demonstra como consumir as funções de atenuação predefinidas e como criar funções de atenuação personalizadas._

A classe [`Easing`](xref:Xamarin.Forms.Easing) define uma série de funções de atenuação que podem ser consumidas por animações:

- A função de [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn) de atenuação salta a animação no início.
- A função de [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut) de atenuação salta a animação no final.
- A função de atenuação [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn) acelera lentamente a animação.
- A função de [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut) atenuação acelera a animação no início e reduz a animação no final.
- A função de [`CubicOut`](xref:Xamarin.Forms.Easing.CubicOut) atenuação acelera rapidamente a animação.
- A função de atenuação [`Linear`](xref:Xamarin.Forms.Easing.Linear) usa uma velocidade constante e é a função de atenuação padrão.
- A [`SinIn`](xref:Xamarin.Forms.Easing.SinIn) facilitar a função acelera a animação.
- O [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut) facilitar a função acelera a animação no início e, sem interrupções, a animação no final.
- A [`SinOut`](xref:Xamarin.Forms.Easing.SinOut) facilitando a função, desacelerando suavemente a animação.
- A função de atenuação [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) faz com que a animação Acelere rapidamente até o fim.
- A função de atenuação [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) faz com que a animação seja desacelerada rapidamente até o fim.

Os sufixos `In` e `Out` indicam se o efeito fornecido pela função de atenuação é perceptível no início da animação, no final ou em ambos.

Além disso, as funções de atenuação personalizadas podem ser criadas. Para obter mais informações, consulte [funções de atenuação personalizadas](#customeasing).

## <a name="consuming-an-easing-function"></a>Consumindo uma função de atenuação

Os métodos de extensão de animação na classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) permitem que uma função de atenuação seja especificada como o parâmetro de método final, conforme demonstrado no exemplo de código a seguir:

```csharp
await image.TranslateTo(0, 200, 2000, Easing.BounceIn);
await image.ScaleTo(2, 2000, Easing.CubicIn);
await image.RotateTo(360, 2000, Easing.SinInOut);
await image.ScaleTo(1, 2000, Easing.CubicOut);
await image.TranslateTo(0, -200, 2000, Easing.BounceOut);
```

Ao especificar uma função de atenuação para uma animação, a velocidade de animação se torna não linear e produz o efeito fornecido pela função de atenuação. Omitir uma função de atenuação ao criar uma animação faz com que a animação use a função de [`Linear`](xref:Xamarin.Forms.Easing.Linear) padrão de atenuação, que produz uma velocidade linear.

Para obter mais informações sobre como usar os métodos de extensão de animação na classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) , consulte [animações simples](~/xamarin-forms/user-interface/animation/simple.md). As funções de atenuação também podem ser consumidas pela classe [`Animation`](xref:Xamarin.Forms.Animation) . Para obter mais informações, consulte [animações personalizadas](~/xamarin-forms/user-interface/animation/custom.md).

<a name="customeasing" />

## <a name="custom-easing-functions"></a>Funções de atenuação personalizadas

Há três abordagens principais para criar uma função de atenuação personalizada:

1. Crie um método que usa um argumento `double` e retorna um resultado de `double`.
1. Criará um `Func<double, double>`.
1. Especifique a função de atenuação como o argumento para o construtor de [`Easing`](xref:Xamarin.Forms.Easing) .

Em todos os três casos, a função de atenuação personalizada deve retornar 0 para um argumento de 0 e 1 para um argumento de 1. No entanto, qualquer valor pode ser retornado entre os valores de argumento de 0 e 1. Cada abordagem agora será discutida por sua vez.

### <a name="custom-easing-method"></a>Método de atenuação personalizada

Uma função de atenuação personalizada pode ser definida como um método que usa um `double` argumento e retorna um resultado de `double`, conforme demonstrado no exemplo de código a seguir:

```csharp
double CustomEase (double t)
{
  return t == 0 || t == 1 ? t : (int)(5 * t) / 5.0;
}

await image.TranslateTo(0, 200, 2000, (Easing)CustomEase);
```

O método `CustomEase` trunca o valor de entrada para os valores 0, 0,2, 0,4, 0,6, 0,8 e 1. Portanto, a instância de [`Image`](xref:Xamarin.Forms.Image) é traduzida em saltos discretos, em vez de tranqüilamente.

### <a name="custom-easing-func"></a>Funcde de atenuação personalizada

Uma função de atenuação personalizada também pode ser definida como uma `Func<double, double>`, conforme demonstrado no exemplo de código a seguir:

```csharp
Func<double, double> CustomEaseFunc = t => 9 * t * t * t - 13.5 * t * t + 5.5 * t;
await image.TranslateTo(0, 200, 2000, CustomEaseFunc);
```

A `CustomEaseFunc` representa uma função de atenuação que começa rápida, diminui e reverte o curso e, em seguida, reverte o curso novamente para acelerar o processo. Portanto, embora a movimentação geral da instância de [`Image`](xref:Xamarin.Forms.Image) esteja abaixo, ela também reverte temporariamente o curso em meio à animação.

### <a name="custom-easing-constructor"></a>Construtor de atenuação personalizada

Uma função de atenuação personalizada também pode ser definida como o argumento para o construtor [`Easing`](xref:Xamarin.Forms.Easing) , conforme demonstrado no exemplo de código a seguir:

```csharp
await image.TranslateTo (0, 200, 2000, new Easing (t => 1 - Math.Cos (10 * Math.PI * t) * Math.Exp (-5 * t)));
```

A função de atenuação personalizada é especificada como um argumento de função lambda para o construtor [`Easing`](xref:Xamarin.Forms.Easing) e usa o método `Math.Cos` para criar um efeito Descartado lento que é retardado pelo método `Math.Exp`. Portanto, a instância de [`Image`](xref:Xamarin.Forms.Image) é traduzida para que pareça ser descartada para o local final de repouso.

## <a name="summary"></a>Resumo

Este artigo demonstrou como consumir as funções de atenuação predefinidas e como criar funções de atenuação personalizadas. O Xamarin. Forms inclui uma classe [`Easing`](xref:Xamarin.Forms.Easing) que permite que você especifique uma função de transferência que controla como as animações se aceleram ou diminuem enquanto estão em execução.

## <a name="related-links"></a>Links relacionados

- [Visão geral do suporte assíncrono](~/cross-platform/platform/async.md)
- [Funções de atenuação (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)
- [Facilitar](xref:Xamarin.Forms.Easing)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
