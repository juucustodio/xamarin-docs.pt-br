---
title: Facilitando funções no Xamarin.Forms
description: Xamarin.Forms inclui uma classe de atenuação que permite especificar uma função de transferência que controla como as animações se aceleram ou diminuem enquanto estão em execução. Este artigo demonstra como consumir as funções de atenuação predefinidas e como criar funções de atenuação personalizadas.
ms.prod: xamarin
ms.assetid: E6F124C7-A161-4C1F-AF40-52F0935E54DE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6ec4c16249aadce668b9fe33dad661e1f7e7ee9e
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97939011"
---
# <a name="easing-functions-in-no-locxamarinforms"></a>Facilitando funções no Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)

_Xamarin.Forms inclui uma classe de atenuação que permite especificar uma função de transferência que controla como as animações se aceleram ou diminuem enquanto estão em execução. Este artigo demonstra como consumir as funções de atenuação predefinidas e como criar funções de atenuação personalizadas._

A [`Easing`](xref:Xamarin.Forms.Easing) classe define uma série de funções de atenuação que podem ser consumidas por animações:

- A [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn) função de atenuação salta a animação no início.
- A [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut) função de atenuação salta a animação no final.
- A [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn) função de atenuação acelera lentamente a animação.
- A [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut) função de atenuação acelera a animação no início e desacelera a animação no final.
- A [`CubicOut`](xref:Xamarin.Forms.Easing.CubicOut) função de atenuação acelera rapidamente a animação.
- A [`Linear`](xref:Xamarin.Forms.Easing.Linear) função de atenuação usa uma velocidade constante e é a função de atenuação padrão.
- A [`SinIn`](xref:Xamarin.Forms.Easing.SinIn) função de atenuação acelera a animação sem problemas.
- A [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut) função de atenuação acelera suavemente a animação no início e reduz suavemente a animação no final.
- A [`SinOut`](xref:Xamarin.Forms.Easing.SinOut) função de atenuação acelera suavemente a animação.
- A [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) função de atenuação faz com que a animação Acelere rapidamente até o fim.
- A [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) função de atenuação faz com que a animação seja desacelerada rapidamente até o fim.

Os `In` `Out` sufixos e indicam se o efeito fornecido pela função de atenuação é perceptível no início da animação, no final ou em ambos.

Além disso, as funções de atenuação personalizadas podem ser criadas. Para obter mais informações, consulte [funções de atenuação personalizadas](#custom-easing-functions).

## <a name="consuming-an-easing-function"></a>Consumindo uma função de atenuação

Os métodos de extensão de animação na [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe permitem que uma função de atenuação seja especificada como o parâmetro de método final, conforme demonstrado no exemplo de código a seguir:

```csharp
await image.TranslateTo(0, 200, 2000, Easing.BounceIn);
await image.ScaleTo(2, 2000, Easing.CubicIn);
await image.RotateTo(360, 2000, Easing.SinInOut);
await image.ScaleTo(1, 2000, Easing.CubicOut);
await image.TranslateTo(0, -200, 2000, Easing.BounceOut);
```

Ao especificar uma função de atenuação para uma animação, a velocidade de animação se torna não linear e produz o efeito fornecido pela função de atenuação. Omitir uma função de atenuação ao criar uma animação faz com que a animação use a [`Linear`](xref:Xamarin.Forms.Easing.Linear) função de atenuação padrão, que produz uma velocidade linear.

> [!NOTE]
> Xamarin.Forms 5,0 inclui um conversor de tipo que converte uma representação de cadeia de caracteres de uma função de atenuação para o [`Easing`](xref:Xamarin.Forms.Easing) membro de enumeração apropriado. Esse conversor de tipo é invocado automaticamente em qualquer Propriedade do tipo `Easing` que esteja definida em XAML.

Para obter mais informações sobre como usar os métodos de extensão de animação na [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe, consulte [animações simples](~/xamarin-forms/user-interface/animation/simple.md). As funções de atenuação também podem ser consumidas pela [`Animation`](xref:Xamarin.Forms.Animation) classe. Para obter mais informações, consulte [animações personalizadas](~/xamarin-forms/user-interface/animation/custom.md).

## <a name="custom-easing-functions"></a>Funções de atenuação personalizadas

Há três abordagens principais para criar uma função de atenuação personalizada:

1. Crie um método que aceite um `double` argumento e retorne um `double` resultado.
1. Criará um `Func<double, double>`.
1. Especifique a função de atenuação como o argumento para o [`Easing`](xref:Xamarin.Forms.Easing) Construtor.

Em todos os três casos, a função de atenuação personalizada deve retornar 0 para um argumento de 0 e 1 para um argumento de 1. No entanto, qualquer valor pode ser retornado entre os valores de argumento de 0 e 1. Cada abordagem agora será discutida por sua vez.

### <a name="custom-easing-method"></a>Método de atenuação personalizada

Uma função de atenuação personalizada pode ser definida como um método que usa um `double` argumento e retorna um `double` resultado, conforme demonstrado no exemplo de código a seguir:

```csharp
double CustomEase (double t)
{
  return t == 0 || t == 1 ? t : (int)(5 * t) / 5.0;
}

await image.TranslateTo(0, 200, 2000, (Easing)CustomEase);
```

O `CustomEase` método trunca o valor de entrada para os valores 0, 0,2, 0,4, 0,6, 0,8 e 1. Portanto, a [`Image`](xref:Xamarin.Forms.Image) instância é traduzida em saltos discretos, em vez de tranqüilamente.

### <a name="custom-easing-func"></a>Funcde de atenuação personalizada

Uma função de atenuação personalizada também pode ser definida como um `Func<double, double>` , conforme demonstrado no exemplo de código a seguir:

```csharp
Func<double, double> CustomEaseFunc = t => 9 * t * t * t - 13.5 * t * t + 5.5 * t;
await image.TranslateTo(0, 200, 2000, CustomEaseFunc);
```

O `CustomEaseFunc` representa uma função de atenuação que começa rápida, diminui e reverte o curso e, em seguida, reverte o curso novamente para acelerar o fim. Portanto, embora a movimentação geral da [`Image`](xref:Xamarin.Forms.Image) instância esteja abaixo, ela também reverte temporariamente o curso na metade da animação.

### <a name="custom-easing-constructor"></a>Construtor de atenuação personalizada

Uma função de atenuação personalizada também pode ser definida como o argumento para o [`Easing`](xref:Xamarin.Forms.Easing) Construtor, conforme demonstrado no exemplo de código a seguir:

```csharp
await image.TranslateTo (0, 200, 2000, new Easing (t => 1 - Math.Cos (10 * Math.PI * t) * Math.Exp (-5 * t)));
```

A função de atenuação personalizada é especificada como um argumento de função lambda para o [`Easing`](xref:Xamarin.Forms.Easing) Construtor e usa o `Math.Cos` método para criar um efeito Descartado lento que é retardado pelo `Math.Exp` método. Portanto, a [`Image`](xref:Xamarin.Forms.Image) instância é traduzida para que pareça ser descartada para o local final de repouso.

## <a name="summary"></a>Resumo

Este artigo demonstrou como consumir as funções de atenuação predefinidas e como criar funções de atenuação personalizadas. Xamarin.Forms inclui uma [`Easing`](xref:Xamarin.Forms.Easing) classe que permite especificar uma função de transferência que controla como as animações se aceleram ou diminuem enquanto estão em execução.

## <a name="related-links"></a>Links Relacionados

- [Visão geral do suporte assíncrono](~/cross-platform/platform/async.md)
- [Funções de atenuação (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-animation-easing)
- [Facilitar](xref:Xamarin.Forms.Easing)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)