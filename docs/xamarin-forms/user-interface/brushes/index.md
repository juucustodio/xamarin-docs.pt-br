---
title: Xamarin.Forms Pincéis
description: A Xamarin.Forms classe Brush é uma classe abstrata que pinta uma área com sua saída.
ms.prod: xamarin
ms.assetid: 44420FC2-304C-4175-8654-76769F79A813
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e0bb8b5e1668e683fa8b15f752b77d865cb317b5
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940389"
---
# <a name="no-locxamarinforms-brushes"></a>Xamarin.Forms Pincéis

Um pincel permite que você pinte uma área, como o plano de fundo de um controle, usando abordagens diferentes. O suporte a pincel no Xamarin.Forms está disponível no `Xamarin.Forms` namespace no Ios, no Android, no MacOS, no plataforma universal do Windows (UWP) e no Windows Presentation Foundation (WPF).

A `Brush` classe é uma classe abstrata que pinta uma área com sua saída. As classes que derivam de `Brush` descrevem diferentes maneiras de pintar uma área. A lista a seguir descreve os diferentes tipos de pincel disponíveis no Xamarin.Forms :

- `SolidColorBrush`, que pinta uma área com uma cor sólida. Para obter mais informações, consulte [ Xamarin.Forms pincéis: cores sólidas](solidcolor.md).
- `LinearGradientBrush`, que pinta uma área com um gradiente linear. Para obter mais informações, consulte [ Xamarin.Forms pincéis: gradientes lineares](lineargradient.md).
- `RadialGradientBrush`, que pinta uma área com um gradiente radial. Para obter mais informações, consulte [ Xamarin.Forms pincéis: gradientes radiais](radialgradient.md).

As instâncias desses tipos de pincel podem ser atribuídas `Stroke` às `Fill` Propriedades e de a e `Shape` à `Background` propriedade de um [`VisualElement`](xref:Xamarin.Forms.VisualElement) .

> [!NOTE]
> A `VisualElement.Background` propriedade permite que os pincéis sejam usados como o plano de fundo em qualquer controle.

A `Brush` classe também tem um `IsNullOrEmpty` método que retorna um `bool` que representa se o pincel é definido ou não.
