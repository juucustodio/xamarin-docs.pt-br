---
title: Hierarquia de classes de controles Xamarin. Forms
description: Os desenvolvedores devem estar familiarizados com a hierarquia dos tipos usados para criar a interface do usuário de um aplicativo Xamarin. Forms.
ms.prod: xamarin
ms.assetid: C89E6B98-464D-4BBE-BF11-13A5FCBBF420
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2019
ms.openlocfilehash: f08146d4439ff1fc22edea71ab1cbb337f64c037
ms.sourcegitcommit: 41a029c69925e3a9d2de883751ebfd649e8747cd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68984388"
---
# <a name="xamarinforms-controls-class-hierarchy"></a>Hierarquia de classes de controles Xamarin. Forms

O Xamarin. Forms é composto por centenas de tipos, em vários namespaces. Os desenvolvedores devem estar mais familiarizados com a hierarquia de tipos usados para criar a interface do usuário de um aplicativo Xamarin. Forms, que reside `Xamarin.Forms` no namespace.

Esses tipos podem ser divididos em páginas, layouts, exibições e células. Uma página Xamarin. Forms geralmente ocupa a tela inteira e todos os tipos de página derivam da [`Page`](xref:Xamarin.Forms.Page) classe. As páginas geralmente contêm um layout e todos os tipos de layout derivam [`Layout`](xref:Xamarin.Forms.Layout) da classe. Um layout geralmente contém exibições e, possivelmente, outros layouts, e todos os tipos de [`View`](xref:Xamarin.Forms.View) exibição derivam da classe. Finalmente, as células são controles especializados que são usados em dados de exibição [`TableView`](xref:Xamarin.Forms.TableView) nos [`ListView`](xref:Xamarin.Forms.ListView) controles e. Páginas, layouts, exibições e células são todas derivadas da [`Element`](xref:Xamarin.Forms.Element) classe.

O diagrama de classe a seguir mostra a hierarquia de tipos que normalmente são usados para criar uma interface do usuário no Xamarin. Forms:

Os [ ![controles Xamarin. Forms]controlam o diagrama de classes de(class-hierarchy-images/class-diagram.png "controles xamarin. Forms") ] (class-hierarchy-images/class-diagram-large.png#lightbox "Diagrama de classes de controles Xamarin. Forms")

> [!NOTE]
> Uma versão de alta resolução do diagrama de classe pode ser baixada [aqui](class-hierarchy-images/class-diagram-high-resolution.png). No entanto, observe que o diagrama não mostra os `CarouselView` tipos `CollectionView` e atualmente. Eles serão adicionados quando os controles estiverem fora de visualização. Além disso, o diagrama mostra apenas um único tipo de Shell.

## <a name="related-links"></a>Links relacionados

- [Referência de controles do Xamarin. Forms](~/xamarin-forms/user-interface/controls/index.md)
