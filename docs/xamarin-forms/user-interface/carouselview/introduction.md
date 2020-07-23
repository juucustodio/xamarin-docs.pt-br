---
title: Xamarin.FormsIntrodução ao CarouselView
description: CarouselView é uma exibição para apresentar dados em um layout rolável, onde os usuários podem passar o dedo para percorrer uma coleção de itens.
ms.prod: xamarin
ms.assetid: 2a96e4bd-c29b-4658-bb4c-ab00872b0f8f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 49aa0ffcf7e7fa4d22024ee08f8bdf509cafd73a
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937794"
---
# <a name="xamarinforms-carouselview-introduction"></a>Xamarin.FormsIntrodução ao CarouselView

![API de pré-lançamento](~/media/shared/preview.png "Esta API está atualmente em pré-lançamento")

[`CarouselView`](xref:Xamarin.Forms.CarouselView)é uma exibição para apresentar dados em um layout rolável, onde os usuários podem passar o dedo para percorrer uma coleção de itens. Por padrão, `CarouselView` o exibirá seus itens em uma orientação horizontal. Um único item será exibido na tela, com gestos de passar o dedo, resultando em encaminhamentos e navegação retroativos por meio da coleção de itens. Além disso, os indicadores podem ser exibidos para representar cada item no `CarouselView` :

[![Captura de tela de um CarouselView e IndicatorView, no iOS e no Android](populate-data-images/indicators.png "Círculos de IndicatorView")](populate-data-images/indicators-large.png#lightbox "Círculos de IndicatorView")

[`CarouselView`](xref:Xamarin.Forms.CarouselView)está disponível em Xamarin.Forms 4,3. No entanto, ele é experimental e só pode ser usado adicionando a linha de código a seguir à sua `AppDelegate` classe no Ios ou à sua `MainActivity` classe no Android, antes de chamar `Forms.Init` :

```csharp
Forms.SetFlags("CarouselView_Experimental");
```

> [!IMPORTANT]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)está disponível no iOS e no Android, mas algumas funcionalidades só podem estar parcialmente disponíveis no Plataforma Universal do Windows.

[`CarouselView`](xref:Xamarin.Forms.CarouselView)compartilha grande parte de sua implementação com o [`CollectionView`](xref:Xamarin.Forms.CollectionView) . No entanto, os dois controles têm casos de uso diferentes. `CollectionView`normalmente é usado para apresentar listas de dados de qualquer comprimento, enquanto `CarouselView` normalmente é usado para realçar informações em uma lista de comprimento limitado.
