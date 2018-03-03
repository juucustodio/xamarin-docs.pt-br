---
title: "Animação"
description: "Xamarin. Forms inclui sua própria infra-estrutura de animação é simples para criar animações simples, enquanto também sendo versátil o suficiente para criar animações complexas."
ms.topic: article
ms.prod: xamarin
ms.assetid: AC0B4127-ECA3-44DA-8A24-A2B10A275083
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: e50419eaa6466e94fc5192a77ffd7cb89ca9d965
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="animation"></a>Animação

_Xamarin. Forms inclui sua própria infra-estrutura de animação é simples para criar animações simples, enquanto também sendo versátil o suficiente para criar animações complexas._

As classes de animação xamarin. Forms destino diferentes propriedades de elementos visuais, com uma animação típica progressivamente alterar uma propriedade de um valor para outro em um período de tempo. Observe que não há nenhuma interface XAML para as classes de animação xamarin. Forms. No entanto, animações podem ser encapsuladas em [comportamentos](~/xamarin-forms/app-fundamentals/behaviors/index.md) e depois referenciado do XAML.

## <a name="simple-animationssimplemd"></a>[Animações simples](simple.md)

O [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) classe fornece métodos de extensão que podem ser usados para construir animações simples que girar, redimensionar, traduzam e desaparecerem [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) instâncias. Este artigo demonstra como criar e Cancelar animações usando o `ViewExtensions` classe.

## <a name="easing-functionseasingmd"></a>[Funções de easing](easing.md)

Xamarin. Forms inclui um [ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/) classe que permite que você especifique uma função de transferência que controla como acelerar animações ou lento que estejam em execução. Este artigo demonstra como utilizar as funções de atenuação predefinidas e como criar funções personalizadas de atenuação.

## <a name="custom-animationscustommd"></a>[Animações personalizadas](custom.md)

O [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) classe é o bloco de construção de todas as animações xamarin. Forms, com os métodos de extensão no [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) classe para criar um ou mais `Animation` objetos. Este artigo demonstra como usar o `Animation` classe para criar e Cancelar animações, sincronizar várias animações e criar animações personalizadas que animar propriedades que não são de animação pelos métodos de animação existente.

