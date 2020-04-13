---
title: Navegação do Xamarin.Forms
description: Este guia explica como navegar em aplicativos Xamarin.Forms. O Xamarin.Forms oferece uma série de experiências de navegação de página diferentes, dependendo do tipo de página sendo usado.
ms.prod: xamarin
ms.assetid: BC5D0C6C-D5A9-4B12-A492-ED1F570CEC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 682e3bd0ac4cdd651203496dd28586db2cef3165
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "66835260"
---
# <a name="xamarinforms-navigation"></a>Navegação do Xamarin.Forms

_Xamarin.Forms fornece uma série de experiências diferentes de navegação de página, dependendo do tipo de página que está sendo usado._

![](images/page-types.png "Xamarin.Forms Page Types")

Como alternativa, os aplicativos de Shell Xamarin.Forms usam uma experiência de navegação baseada em URI que não impõe uma hierarquia de navegação de conjunto. Saiba mais sobre a [Navegação do Shell do Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/navigation.md).

## <a name="hierarchical-navigation"></a>[Navegação hierárquica](hierarchical.md)

A [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe oferece uma experiência de navegação hierárquica onde o usuário é capaz de navegar através de páginas, para frente e para trás, conforme desejado. A classe implementa a navegação como uma pilha de [`Page`](xref:Xamarin.Forms.Page) objetos de última entrada e primeira saída (LIFO).

## <a name="tabbedpage"></a>[TabbedPage](tabbed-page.md)

O Xamarin.Forms [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) consiste em uma lista de guias e uma área de detalhes maior, com cada conteúdo de carregamento de guias na área de detalhes.

## <a name="carouselpage"></a>[CarouselPage](carousel-page.md)

O Xamarin.Forms [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) é uma página que os usuários podem deslizar de um lado para o outro para navegar através de páginas de conteúdo, como uma galeria.

## <a name="masterdetailpage"></a>[MasterDetailPage](master-detail-page.md)

O Xamarin.Forms [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) é uma página que gerencia duas páginas de informações relacionadas – uma página-mestre que apresenta itens e uma página de detalhes que apresenta detalhes sobre itens na página-mestre.

## <a name="modal-pages"></a>[Páginas modais](modal.md)

O Xamarin.Forms também dá suporte a páginas modais. Uma página modal incentiva os usuários a concluir uma tarefa independente da qual não se pode sair via navegação até que essa tarefa seja concluída ou cancelada.
