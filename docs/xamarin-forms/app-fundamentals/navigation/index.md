---
title: Xamarin.FormsNavega
description: Este guia explica como executar a navegação em Xamarin.Forms aplicativos. Xamarin.Formsfornece várias experiências de navegação de página diferentes, dependendo do tipo de página que está sendo usado.
ms.prod: xamarin
ms.assetid: BC5D0C6C-D5A9-4B12-A492-ED1F570CEC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8c907cd8a4a1d14b936dee309610bffc67ef363f
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137833"
---
# <a name="xamarinforms-navigation"></a>Xamarin.FormsNavega

_O Xamarin. Forms fornece várias experiências de navegação de página diferentes, dependendo do tipo de página que está sendo usado._

![](images/page-types.png "Xamarin.Forms Page Types")

Como alternativa, Xamarin.Forms os aplicativos de shell usam uma experiência de navegação baseada em URI que não impõe uma hierarquia de navegação de conjunto. Para obter mais informações, consulte [ Xamarin.Forms navegação do Shell](~/xamarin-forms/app-fundamentals/shell/navigation.md).

## <a name="hierarchical-navigation"></a>[Navegação hierárquica](hierarchical.md)

A [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe fornece uma experiência de navegação hierárquica em que o usuário é capaz de navegar pelas páginas, encaminhamentos e para trás, conforme desejado. A classe implementa a navegação como uma pilha UEPS (último a entrar, primeiro a sair) de [`Page`](xref:Xamarin.Forms.Page) objetos.

## <a name="tabbedpage"></a>[TabbedPage](tabbed-page.md)

O Xamarin.Forms [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) consiste em uma lista de guias e uma área de detalhes maior, com cada guia carregando conteúdo na área de detalhes.

## <a name="carouselpage"></a>[CarouselPage](carousel-page.md)

O Xamarin.Forms [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) é uma página que os usuários podem passar do lado a lado para navegar pelas páginas de conteúdo, como uma galeria.

## <a name="masterdetailpage"></a>[MasterDetailPage](master-detail-page.md)

O Xamarin.Forms [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) é uma página que gerencia duas páginas de informações relacionadas – uma página mestra que apresenta itens e uma página de detalhes que apresenta detalhes sobre os itens na página mestra.

## <a name="modal-pages"></a>[Páginas modais](modal.md)

Xamarin.Formstambém fornece suporte para páginas modais. Uma página modal incentiva os usuários a concluir uma tarefa independente da qual não se pode sair via navegação até que essa tarefa seja concluída ou cancelada.
