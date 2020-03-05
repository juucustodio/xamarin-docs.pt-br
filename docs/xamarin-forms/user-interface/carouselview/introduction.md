---
title: Introdução ao CarouselView do Xamarin. Forms
description: CarouselView é uma exibição para apresentar dados em um layout rolável, onde os usuários podem passar o dedo para percorrer uma coleção de itens.
ms.prod: xamarin
ms.assetid: 2a96e4bd-c29b-4658-bb4c-ab00872b0f8f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
ms.openlocfilehash: 2c3e15ce68ad1507318a1d8155f9ab03095ea409
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2020
ms.locfileid: "78291604"
---
# <a name="xamarinforms-carouselview-introduction"></a>Introdução ao CarouselView do Xamarin. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

[`CarouselView`](xref:Xamarin.Forms.CarouselView) é uma exibição para apresentar dados em um layout rolável, onde os usuários podem passar o dedo para percorrer uma coleção de itens. Por padrão, `CarouselView` exibirá seus itens em uma orientação horizontal. Um único item será exibido na tela, com gestos de passar o dedo, resultando em encaminhamentos e navegação retroativos por meio da coleção de itens. Além disso, os indicadores podem ser exibidos para representar cada item no `CarouselView`:

[![Captura de tela de um CarouselView e IndicatorView, no iOS e no Android](populate-data-images/indicators.png "Círculos de IndicatorView")](populate-data-images/indicators-large.png#lightbox "Círculos de IndicatorView")

[`CarouselView`](xref:Xamarin.Forms.CarouselView) está disponível no Xamarin. Forms 4,3. No entanto, ele é experimental e só pode ser usado adicionando a linha de código a seguir à sua classe `AppDelegate` no iOS ou à sua classe `MainActivity` no Android, antes de chamar `Forms.Init`:

```csharp
Forms.SetFlags("CarouselView_Experimental");
```

> [!IMPORTANT]
> o [`CarouselView`](xref:Xamarin.Forms.CarouselView) está disponível no Ios e no Android, mas algumas funcionalidades só podem estar parcialmente disponíveis no plataforma universal do Windows.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) compartilha grande parte de sua implementação com [`CollectionView`](xref:Xamarin.Forms.CollectionView). No entanto, os dois controles têm casos de uso diferentes. `CollectionView` normalmente é usado para apresentar listas de dados de qualquer comprimento, enquanto `CarouselView` normalmente é usado para realçar informações em uma lista de comprimento limitado.
