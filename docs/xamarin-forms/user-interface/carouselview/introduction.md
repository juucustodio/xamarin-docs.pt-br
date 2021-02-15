---
title: Xamarin.Forms Introdução ao CarouselView
description: CarouselView é uma exibição para apresentar dados em um layout rolável, onde os usuários podem passar o dedo para percorrer uma coleção de itens.
ms.prod: xamarin
ms.assetid: 2a96e4bd-c29b-4658-bb4c-ab00872b0f8f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2aa33b0bd2e11d854f4f4dcfe03258a621301395
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97939019"
---
# <a name="no-locxamarinforms-carouselview-introduction"></a>Xamarin.Forms Introdução ao CarouselView

[`CarouselView`](xref:Xamarin.Forms.CarouselView) é uma exibição para apresentar dados em um layout rolável, onde os usuários podem passar o dedo para percorrer uma coleção de itens. Por padrão, `CarouselView` o exibirá seus itens em uma orientação horizontal. Um único item será exibido na tela, com gestos de passar o dedo, resultando em encaminhamentos e navegação retroativos por meio da coleção de itens. Além disso, os indicadores podem ser exibidos para representar cada item no `CarouselView` :

[![Captura de tela de um CarouselView e IndicatorView, no iOS e no Android](populate-data-images/indicators.png "Círculos de IndicatorView")](populate-data-images/indicators-large.png#lightbox "Círculos de IndicatorView")

Por padrão, [`CarouselView`](xref:Xamarin.Forms.CarouselView) o fornece acesso em loop à sua coleção de itens. Portanto, o dedo para trás do primeiro item na coleção exibirá o último item da coleção. Da mesma forma, o encaminhamento do último item da coleção retornará ao primeiro item da coleção.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) compartilha grande parte de sua implementação com o [`CollectionView`](xref:Xamarin.Forms.CollectionView) . No entanto, os dois controles têm casos de uso diferentes. `CollectionView` normalmente é usado para apresentar listas de dados de qualquer comprimento, enquanto `CarouselView` normalmente é usado para realçar informações em uma lista de comprimento limitado.
