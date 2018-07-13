---
title: Funções de easing no xamarin. Forms
description: Xamarin. Forms inclui uma classe de atenuação que permite que você especifique uma função de transferência que controla como as animações aceleram ou diminuir como elas estão em execução. Este artigo demonstra como utilizar as funções de easing predefinidas e como criar funções de easing personalizadas.
ms.prod: xamarin
ms.assetid: E6F124C7-A161-4C1F-AF40-52F0935E54DE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 1c75771173d94a18c7c1cc5100c64d45bdc32078
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998115"
---
# <a name="easing-functions-in-xamarinforms"></a>Funções de easing no xamarin. Forms

_Xamarin. Forms inclui uma classe de atenuação que permite que você especifique uma função de transferência que controla como as animações aceleram ou diminuir como elas estão em execução. Este artigo demonstra como utilizar as funções de easing predefinidas e como criar funções de easing personalizadas._


O [ `Easing` ](xref:Xamarin.Forms.Easing) classe define um número de funções de easing que podem ser consumidos por animações:

- O [ `BounceIn` ](xref:Xamarin.Forms.Easing.BounceIn) função de easing bounces a animação no início.
- O [ `BounceOut` ](xref:Xamarin.Forms.Easing.BounceOut) função de easing bounces a animação no final.
- O [ `CubicIn` ](xref:Xamarin.Forms.Easing.CubicIn) função de easing lentamente acelera a animação.
- O [ `CubicInOut` ](xref:Xamarin.Forms.Easing.CubicInOut) função de easing acelera a animação no início e desacelerada a animação no final.
- O [ `CubicOut` ](xref:Xamarin.Forms.Easing.CubicOut) função de easing rapidamente desacelerada a animação.
- O [ `Linear` ](xref:Xamarin.Forms.Easing.Linear) função de easing usa uma velocidade constante e é o padrão a função de easing.
- O [ `SinIn` ](xref:Xamarin.Forms.Easing.SinIn) função de easing suavemente acelera a animação.
- O [ `SinInOut` ](xref:Xamarin.Forms.Easing.SinInOut) função de easing suavemente acelera a animação no início e desacelerada suavemente a animação no final.
- O [ `SinOut` ](xref:Xamarin.Forms.Easing.SinOut) função de easing suavemente desacelerada a animação.
- O [ `SpringIn` ](xref:Xamarin.Forms.Easing.SpringIn) função de easing faz com que a animação acelerar rapidamente até o final.
- O [ `SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut) função de easing faz com que a animação de desacelerar rapidamente até o final.

O `In` e `Out` sufixos que indicam se o efeito fornecido pela função de easing é perceptível no início da animação, no final, ou ambos.

Além disso, as funções de easing personalizadas podem ser criadas. Para obter mais informações, consulte [funções de Easing personalizada](#customeasing).

## <a name="consuming-an-easing-function"></a>Consumindo uma função de Easing

Os métodos de extensão de animação na [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe permitem que uma função de easing a ser especificado como o parâmetro de método final, conforme demonstrado no exemplo de código a seguir:

```csharp
await image.TranslateTo(0, 200, 2000, Easing.BounceIn);
await image.ScaleTo(2, 2000, Easing.CubicIn);
await image.RotateTo(360, 2000, Easing.SinInOut);
await image.ScaleTo(1, 2000, Easing.CubicOut);
await image.TranslateTo(0, -200, 2000, Easing.BounceOut);
```

Especificando uma função de easing para uma animação, a velocidade da animação se torna não lineares e produz o efeito fornecido pela função de easing. A omissão de uma função de easing durante a criação de uma animação faz com que a animação usar o padrão [ `Linear` ](xref:Xamarin.Forms.Easing.Linear) facilitando a função, que produz uma velocidade linear.

Para obter mais informações sobre como usar os métodos de extensão de animação na [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) da classe, consulte [animações simples](~/xamarin-forms/user-interface/animation/simple.md). Funções de easing também podem ser consumidas pela [ `Animation` ](xref:Xamarin.Forms.Animation) classe. Para obter mais informações, consulte [animações personalizadas](~/xamarin-forms/user-interface/animation/custom.md).

<a name="customeasing" />

## <a name="custom-easing-functions"></a>Funções de Easing personalizado

Há três abordagens principais para a criação de uma função de easing personalizada:

1. Criar um método que usa um `double` argumento e retorna um `double` resultado.
1. Criará um `Func<double, double>`.
1. Especifica a função de easing como o argumento para o [ `Easing` ](xref:Xamarin.Forms.Easing) construtor.

Em todos os três casos, a função de easing personalizada deve retornar 0 para um argumento de 0 e 1 para um argumento de 1. No entanto, qualquer valor pode ser retornado entre os valores de argumento de 0 e 1. Cada abordagem agora será discutida detalhadamente.

### <a name="custom-easing-method"></a>Método de Easing personalizado

Uma função de easing personalizada pode ser definida como um método que usa um `double` argumento e retorna um `double` resultar, conforme demonstrado no exemplo de código a seguir:

```csharp
await image.TranslateTo(0, 200, 2000, CustomEase);

double CustomEase (double t)
{
  return t == 0 || t == 1 ? t : (int)(5 * t) / 5.0;
}
```

O `CustomEase` método truncará o valor de entrada para os valores 0, 0.2, 0,4, 0,6, 0,8 e 1. Portanto, o [ `Image` ](xref:Xamarin.Forms.Image) instância é convertida em pequenos saltos, em vez de sem problemas.

### <a name="custom-easing-func"></a>Easing Func personalizado

Uma função de easing personalizada também pode ser definida como um `Func<double, double>`, conforme demonstrado no exemplo de código a seguir:

```csharp
Func<double, double> CustomEase = t => 9 * t * t * t - 13.5 * t * t + 5.5 * t;
await image.TranslateTo(0, 200, 2000, CustomEase));
```

O `CustomEase` `Func` representa uma função de easing começa rápidos e mais lento e inverte o curso e, em seguida, reverte curso novamente para acelerar rapidamente até o final. Portanto, embora a movimentação geral do [ `Image` ](xref:Xamarin.Forms.Image) instância é para baixo, ele também temporariamente inverte o curso no meio da animação.

### <a name="custom-easing-constructor"></a>Construtor de Easing personalizado

Uma função de easing personalizada também pode ser definida como o argumento para o [ `Easing` ](xref:Xamarin.Forms.Easing) construtor, conforme demonstrado no exemplo de código a seguir:

```csharp
await image.TranslateTo (0, 200, 2000, new Easing (t => 1 - Math.Cos (10 * Math.PI * t) * Math.Exp (-5 * t)));
```

A função de easing personalizada é especificada como um argumento de função lambda para o [ `Easing` ](xref:Xamarin.Forms.Easing) construtor e usa o `Math.Cos` método para criar um efeito de soltar lento é amortecido pelo `Math.Exp` método. Portanto, o [ `Image` ](xref:Xamarin.Forms.Image) instância é convertida para que ele apareça descartar ao seu local de repouso final.

## <a name="summary"></a>Resumo

Este artigo demonstrou como utilizar as funções de easing predefinidas e como criar funções de easing personalizadas. Xamarin. Forms inclui um [ `Easing` ](xref:Xamarin.Forms.Easing) classe que permite que você especifique uma função de transferência que controla como as animações acelerar ou diminuir como elas estão em execução.



## <a name="related-links"></a>Links relacionados

- [Visão geral do suporte assíncrono](~/cross-platform/platform/async.md)
- [Funções de easing (amostra)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/easing/)
- [Atenuação](xref:Xamarin.Forms.Easing)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
