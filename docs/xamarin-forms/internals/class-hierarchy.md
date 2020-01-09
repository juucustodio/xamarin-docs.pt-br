---
title: Hierarquia de classes de controles Xamarin. Forms
description: Os desenvolvedores devem estar familiarizados com a hierarquia dos tipos usados para criar a interface do usuário de um aplicativo Xamarin. Forms.
ms.prod: xamarin
ms.assetid: C89E6B98-464D-4BBE-BF11-13A5FCBBF420
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/07/2020
ms.openlocfilehash: 7c55ed3082a031352a8eefb8ef579060a9dd6404
ms.sourcegitcommit: 4899cf4aaa73aa56c48f7ee2339c0af8112e1feb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75717282"
---
# <a name="xamarinforms-controls-class-hierarchy"></a>Hierarquia de classes de controles Xamarin. Forms

O Xamarin. Forms é composto por centenas de tipos, em vários namespaces. Os desenvolvedores devem estar mais familiarizados com a hierarquia de tipos usados para criar a interface do usuário de um aplicativo Xamarin. Forms, que reside no namespace `Xamarin.Forms`.

Esses tipos podem ser divididos em páginas, layouts, exibições e células. Uma página Xamarin. Forms geralmente ocupa a tela inteira e todos os tipos de página derivam da classe [`Page`](xref:Xamarin.Forms.Page) . As páginas geralmente contêm um layout e todos os tipos de layout derivam da classe [`Layout`](xref:Xamarin.Forms.Layout) . Um layout geralmente contém exibições e, possivelmente, outros layouts, e todos os tipos de exibição derivam, por fim, da classe [`View`](xref:Xamarin.Forms.View) . Finalmente, as células são controles especializados que são usados em dados de exibição nos controles [`TableView`](xref:Xamarin.Forms.TableView) e [`ListView`](xref:Xamarin.Forms.ListView) . Páginas, layouts, exibições e células são todas derivadas da classe [`Element`](xref:Xamarin.Forms.Element) .

O diagrama de classe a seguir mostra a hierarquia de tipos que normalmente são usados para criar uma interface do usuário no Xamarin. Forms:

[![Diagrama de classes de controles Xamarin. Forms](class-hierarchy-images/class-diagram.png "Diagrama de classes de controles Xamarin. Forms")](class-hierarchy-images/class-diagram-large.png#lightbox "Diagrama de classes de controles Xamarin. Forms")

> [!NOTE]
> Uma versão de alta resolução do diagrama de classe pode ser baixada [aqui](class-hierarchy-images/class-diagram-high-resolution.png). No entanto, observe que o diagrama mostra apenas um único tipo de Shell.

## <a name="related-links"></a>Links relacionados

- [Referência de controles do Xamarin. Forms](~/xamarin-forms/user-interface/controls/index.md)
