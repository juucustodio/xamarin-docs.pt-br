---
title: Animação no xamarin. Forms
description: Xamarin. Forms inclui sua própria infraestrutura de animação que é muito simples para a criação de animações simples, embora também seja suficientemente versátil para criar animações complexas.
ms.prod: xamarin
ms.assetid: AC0B4127-ECA3-44DA-8A24-A2B10A275083
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: bebb3e32f298a2079069787dba3453e1817cf64f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61158583"
---
# <a name="animation-in-xamarinforms"></a>Animação no xamarin. Forms

_Xamarin. Forms inclui sua própria infraestrutura de animação que é muito simples para a criação de animações simples, embora também seja suficientemente versátil para criar animações complexas._

As classes de animação do xamarin. Forms diferentes propriedades de elementos visuais, de destino com uma animação típica progressivamente alterar uma propriedade de um valor para outro durante um período de tempo. Observe que não há nenhuma interface XAML para as classes de animação do xamarin. Forms. No entanto, as animações podem ser encapsuladas em [comportamentos](~/xamarin-forms/app-fundamentals/behaviors/index.md) e, em seguida, referenciado do XAML.

## <a name="simple-animationssimplemd"></a>[Animações simples](simple.md)

O [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe fornece métodos de extensão que podem ser usados para construir animações simples que giram, dimensionarem, traduzam e fade [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) instâncias. Este artigo demonstra como criar e cancelar as animações usando o `ViewExtensions` classe.

## <a name="easing-functionseasingmd"></a>[Funções de easing](easing.md)

Xamarin. Forms inclui um [ `Easing` ](xref:Xamarin.Forms.Easing) classe que permite que você especifique uma função de transferência que controla como as animações acelerar ou diminuir como elas estão em execução. Este artigo demonstra como utilizar as funções de easing predefinidas e como criar funções de easing personalizadas.

## <a name="custom-animationscustommd"></a>[Animações personalizadas](custom.md)

O [ `Animation` ](xref:Xamarin.Forms.Animation) classe é o bloco de construção de todas as animações de xamarin. Forms, com os métodos de extensão no [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe criando um ou mais `Animation` objetos. Este artigo demonstra como usar o `Animation` classe para criar e Cancelar animações, sincronizar várias animações e criar animações personalizadas que animar propriedades que não estão animadas pelos métodos de animação existentes.
