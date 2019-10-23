---
title: Introdução ao CarouselView do Xamarin. Forms
description: CarouselView é uma exibição para apresentar dados em um layout rolável, onde os usuários podem passar o dedo para percorrer uma coleção de itens.
ms.prod: xamarin
ms.assetid: 2a96e4bd-c29b-4658-bb4c-ab00872b0f8f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
ms.openlocfilehash: 2fe4d984f36880493a9a04d99b63876551366477
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696971"
---
# <a name="xamarinforms-carouselview-introduction"></a>Introdução ao CarouselView do Xamarin. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

[`CarouselView`](xref:Xamarin.Forms.CarouselView) é uma exibição para apresentar dados em um layout rolável, onde os usuários podem passar o dedo para percorrer uma coleção de itens. Por padrão, `CarouselView` exibirá seus itens em uma orientação horizontal. Um único item será exibido na tela, com gestos de passar o dedo, resultando em encaminhamentos e navegação retroativos por meio da coleção de itens.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) está disponível no Xamarin. Forms 4,3. No entanto, ele é experimental e só pode ser usado adicionando a linha de código a seguir à sua classe `AppDelegate` no iOS ou à sua classe `MainActivity` no Android, antes de chamar `Forms.Init`:

```csharp
Forms.SetFlags("CarouselView_Experimental");
```

> [!IMPORTANT]
> o [`CarouselView`](xref:Xamarin.Forms.CarouselView) está disponível no Ios e no Android, mas algumas funcionalidades só podem estar parcialmente disponíveis no plataforma universal do Windows.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) compartilha grande parte de sua implementação com [`CollectionView`](xref:Xamarin.Forms.CollectionView). No entanto, os dois controles têm casos de uso diferentes. `CollectionView` normalmente é usado para apresentar listas de dados, de qualquer comprimento, enquanto `CarouselView` normalmente é usado para realçar informações em uma lista de comprimento limitado.
