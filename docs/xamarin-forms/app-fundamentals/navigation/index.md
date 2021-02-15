---
title: Xamarin.Forms Navega
description: Este guia explica como executar a navegação em Xamarin.Forms aplicativos. Xamarin.Forms fornece várias experiências de navegação de página diferentes, dependendo do tipo de página que está sendo usado.
ms.prod: xamarin
ms.assetid: BC5D0C6C-D5A9-4B12-A492-ED1F570CEC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d976048b15c1fc545e1fbdc6c911e3cb4542d4f2
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97939089"
---
# <a name="no-locxamarinforms-navigation"></a>Xamarin.Forms Navega

_Xamarin.Forms fornece várias experiências de navegação de página diferentes, dependendo do tipo de página que está sendo usado._

![::: no-Loc (Xamarin. Forms)::: tipos de página](images/page-types.png)

Como alternativa, Xamarin.Forms os aplicativos de shell usam uma experiência de navegação baseada em URI que não impõe uma hierarquia de navegação de conjunto. Para obter mais informações, consulte [ Xamarin.Forms navegação do Shell](~/xamarin-forms/app-fundamentals/shell/navigation.md).

## <a name="hierarchical-navigation"></a>[Navegação hierárquica](hierarchical.md)

A [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe fornece uma experiência de navegação hierárquica em que o usuário é capaz de navegar pelas páginas, encaminhamentos e para trás, conforme desejado. A classe implementa a navegação como uma pilha UEPS (último a entrar, primeiro a sair) de [`Page`](xref:Xamarin.Forms.Page) objetos.

## <a name="tabbedpage"></a>[TabbedPage](tabbed-page.md)

O Xamarin.Forms [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) consiste em uma lista de guias e uma área de detalhes maior, com cada guia carregando conteúdo na área de detalhes.

## <a name="carouselpage"></a>[CarouselPage](carousel-page.md)

O Xamarin.Forms [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) é uma página que os usuários podem passar do lado a lado para navegar pelas páginas de conteúdo, como uma galeria.

## <a name="flyoutpage"></a>[FlyoutPage](flyoutpage.md)

O Xamarin.Forms [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) é uma página que gerencia duas páginas de informações relacionadas – uma página de submenu que apresenta itens e uma página de detalhes que apresenta detalhes sobre os itens na página do submenu.

## <a name="modal-pages"></a>[Páginas modais](modal.md)

Xamarin.Forms também fornece suporte para páginas modais. Uma página modal incentiva os usuários a concluir uma tarefa independente da qual não se pode sair via navegação até que essa tarefa seja concluída ou cancelada.
