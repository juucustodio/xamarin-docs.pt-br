---
title: Funções de easing
description: Xamarin. Forms inclui uma classe de atenuação que permite que você especifique uma função de transferência que controla como animações acelerar ou diminuir conforme estejam em execução. Este artigo demonstra como utilizar as funções de atenuação predefinidas e como criar funções personalizadas de atenuação.
ms.prod: xamarin
ms.assetid: E6F124C7-A161-4C1F-AF40-52F0935E54DE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: e9171b885bdf5958b6969719301a1d7dad51d95b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="easing-functions"></a>Funções de easing

_Xamarin. Forms inclui uma classe de atenuação que permite que você especifique uma função de transferência que controla como animações acelerar ou diminuir conforme estejam em execução. Este artigo demonstra como utilizar as funções de atenuação predefinidas e como criar funções personalizadas de atenuação._


O [ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/) classe define um número de funções de atenuação que podem ser consumidos por animações:

- O [ `BounceIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.BounceIn/) atenuação função devoluções a animação no início.
- O [ `BounceOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.BounceOut/) atenuação função devoluções a animação no final.
- O [ `CubicIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicIn/) atenuação função lentamente acelera a animação.
- O [ `CubicInOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicInOut/) atenuação função acelera a animação no início e será desacelerado a animação no final.
- O [ `CubicOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicOut/) facilitar rapidamente a função será desacelerado a animação.
- O [ `Linear` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.Linear/) facilitando a função usa uma velocidade constante e é o padrão facilitando a função.
- O [ `SinIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinIn/) atenuação função perfeitamente acelera a animação.
- O [ `SinInOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinInOut/) atenuação função perfeitamente acelera a animação no início e suave será desacelerado a animação no final.
- O [ `SinOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinOut/) atenuação função perfeitamente será desacelerado a animação.
- O [ `SpringIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringIn/) facilitando a função faz a animação acelerar rapidamente em direção ao final.
- O [ `SpringOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/) facilitando a função faz a animação ser desacelerar rapidamente em direção ao final.

O `In` e `Out` sufixos indicam se o efeito fornecido pela função atenuação é notável no início da animação, no final, ou ambos.

Além disso, as funções de atenuação personalizadas podem ser criadas. Para obter mais informações, consulte [personalizado atenuação funções](#customeasing).

## <a name="consuming-an-easing-function"></a>Consumindo uma função de Easing

Os métodos de extensão de animação no [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) classe permitem uma função de atenuação seja especificado como o parâmetro de método final, conforme demonstrado no exemplo de código a seguir:

```csharp
await image.TranslateTo(0, 200, 2000, Easing.BounceIn);
await image.ScaleTo(2, 2000, Easing.CubicIn);
await image.RotateTo(360, 2000, Easing.SinInOut);
await image.ScaleTo(1, 2000, Easing.CubicOut);
await image.TranslateTo(0, -200, 2000, Easing.BounceOut);
```

Especificando uma função de easing para uma animação, a velocidade da animação se torna não linear e produz o efeito fornecido pela função de easing. A omissão de uma função de easing ao criar uma animação faz com que a animação usar o padrão [ `Linear` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.Linear/) facilitando a função, que produz uma velocidade linear.

Para obter mais informações sobre como usar os métodos de extensão de animação no [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) de classe, consulte [animações simples](~/xamarin-forms/user-interface/animation/simple.md). Atenuação funções também pode ser consumido pelo [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) classe. Para obter mais informações, consulte [personalizado animações](~/xamarin-forms/user-interface/animation/custom.md).

<a name="customeasing" />

## <a name="custom-easing-functions"></a>Personalizar atenuação funções

Há três abordagens principais para criar uma função personalizada de atenuação:

1. Criar um método que utiliza um `double` argumento e retorna um `double` resultados.
1. Criará um `Func<double, double>`.
1. Especifique a função de easing como o argumento para o [ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/) construtor.

Em todos os três casos, a função de atenuação personalizada deve retornar 0 para um argumento de 0 e 1 para um argumento de 1. No entanto, qualquer valor pode ser retornado entre os valores de argumento de 0 e 1. Por sua vez, cada abordagem agora será discutida.

### <a name="custom-easing-method"></a>Personalizado, facilitando a método

Uma função de atenuação personalizada pode ser definida como um método que utiliza um `double` argumento e retorna um `double` resultar, conforme demonstrado no exemplo de código a seguir:

```csharp
await image.TranslateTo(0, 200, 2000, CustomEase);

double CustomEase (double t)
{
  return t == 0 || t == 1 ? t : (int)(5 * t) / 5.0;
}
```

O `CustomEase` método trunca o valor de entrada para os valores 0, 0.2, 0,4, 0,6, 0.8 e 1. Portanto, o [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) instância é convertida em saltos discretos, em vez de sem problemas.

### <a name="custom-easing-func"></a>Personalizar atenuação Func

Uma função de atenuação personalizada também pode ser definida como um `Func<double, double>`, conforme demonstrado no exemplo de código a seguir:

```csharp
Func<double, double> CustomEase = t => 9 * t * t * t - 13.5 * t * t + 5.5 * t;
await image.TranslateTo(0, 200, 2000, CustomEase));
```

O `CustomEase` `Func` representa uma função de easing começa rápido e lento e inverte o curso e, em seguida, reverte curso novamente para acelerar rapidamente em direção ao final. Portanto, embora a movimentação geral do [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) instância é para baixo, ele também temporariamente inverte o curso no meio da animação.

### <a name="custom-easing-constructor"></a>Personalizar atenuação construtor

Uma função de atenuação personalizada também pode ser definida como o argumento para o [ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/) construtor, conforme demonstrado no exemplo de código a seguir:

```csharp
await image.TranslateTo (0, 200, 2000, new Easing (t => 1 - Math.Cos (10 * Math.PI * t) * Math.Exp (-5 * t)));
```

A função de atenuação personalizada é especificada como um argumento de função lambda para o [ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/) construtor e usa o `Math.Cos` método para criar um efeito lento que é amortecido pelo `Math.Exp` método. Portanto, o [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) instância é convertida para que ele apareça descartar para seu local de repouso final.

## <a name="summary"></a>Resumo

Este artigo demonstrou como utilizar as funções de atenuação predefinidas e como criar funções personalizadas de atenuação. Xamarin. Forms inclui um [ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/) classe que permite que você especifique uma função de transferência que controla como acelerar animações ou lento que estejam em execução.



## <a name="related-links"></a>Links relacionados

- [Visão geral do suporte assíncrono](~/cross-platform/platform/async.md)
- [Atenuação funções (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/easing/)
- [Atenuação](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/)
- [ViewExtensions](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)
