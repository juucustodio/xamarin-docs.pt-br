---
title: Xamarin.Forms Hierarquia de classes de controles
description: Os desenvolvedores devem estar familiarizados com a hierarquia dos tipos usados para criar a interface do usuário de um Xamarin.Forms aplicativo.
ms.prod: xamarin
ms.assetid: C89E6B98-464D-4BBE-BF11-13A5FCBBF420
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/07/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0087e2bb81c7c9204a782519a9eeb9891adc297a
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138627"
---
# <a name="xamarinforms-controls-class-hierarchy"></a>Xamarin.Forms Hierarquia de classes de controles

Xamarin.Forms é composto de centenas de tipos, em vários namespaces. Os desenvolvedores devem estar mais familiarizados com a hierarquia dos tipos usados para criar a interface do usuário de um aplicativo Xamarin.Forms, que reside no namespace `Xamarin.Forms`.

Esses tipos podem ser divididos em páginas, layouts, exibições e células. Uma página Xamarin.Forms geralmente ocupa a tela inteira e todos os tipos de página derivam da classe [`Page`](xref:Xamarin.Forms.Page). As páginas geralmente contêm um layout e todos os tipos de layout derivam da classe [`Layout`](xref:Xamarin.Forms.Layout). Um layout geralmente contém exibições e, possivelmente, outros layouts, e todos os tipos de exibição derivam, por fim, da classe [`View`](xref:Xamarin.Forms.View). Finalmente, as células são controles especializados que são usados em dados de exibição nos controles [`TableView`](xref:Xamarin.Forms.TableView) e [`ListView`](xref:Xamarin.Forms.ListView). Páginas, layouts, exibições e células são todas derivadas da classe [`Element`](xref:Xamarin.Forms.Element).

O diagrama de classe a seguir mostra a hierarquia de tipos que normalmente são usados para criar uma interface do usuário no Xamarin.Forms:

[![Xamarin.FormsDiagrama de classes de controles](class-hierarchy-images/class-diagram.png "[! Parar. Diagrama de classe de controles NO-LOC (Xamarin. Forms)]")](class-hierarchy-images/class-diagram-large.png#lightbox "[! Parar. Diagrama de classe de controles NO-LOC (Xamarin. Forms)]")

> [!NOTE]
> Uma versão de alta resolução do diagrama de classe pode ser baixada [aqui](class-hierarchy-images/class-diagram-high-resolution.png). No entanto, observe que o diagrama mostra apenas um único tipo de Shell.

## <a name="related-links"></a>Links relacionados

- [Xamarin.Forms Referência de controles](~/xamarin-forms/user-interface/controls/index.md)
