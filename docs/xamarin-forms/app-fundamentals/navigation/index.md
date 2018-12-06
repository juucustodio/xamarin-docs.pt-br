---
title: Navegação do Xamarin.Forms
description: Este guia explica como navegar em aplicativos Xamarin.Forms. O Xamarin.Forms oferece uma série de experiências de navegação de página diferentes, dependendo do tipo de página sendo usado.
ms.prod: xamarin
ms.assetid: BC5D0C6C-D5A9-4B12-A492-ED1F570CEC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 202f044ebd7dd5b110b94d2aa60eeb7151150607
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994722"
---
# <a name="xamarinforms-navigation"></a>Navegação do Xamarin.Forms

_O Xamarin.Forms oferece uma série de experiências de navegação de página diferentes, dependendo do tipo de página sendo usado._

![](images/page-types.png "Tipos de página do Xamarin.Forms")

## <a name="hierarchical-navigationhierarchicalmd"></a>[Navegação hierárquica](hierarchical.md)

A classe [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) oferece uma experiência de Navegação hierárquica em que o usuário é capaz de navegar pelas páginas para frente e para trás, conforme desejado. A classe implementa navegação como uma pilha UEPS (último a entrar, primeiro a sair) de objetos [`Page`](xref:Xamarin.Forms.Page).

## <a name="tabbedpagetabbed-pagemd"></a>[TabbedPage](tabbed-page.md)

O [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) do Xamarin.Forms consiste em uma lista de guias e uma área de detalhes maior, com cada guia carregando conteúdo na área de detalhes.

## <a name="carouselpagecarousel-pagemd"></a>[CarouselPage](carousel-page.md)

O [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) do Xamarin.Forms é uma página em que os usuários podem passar o dedo de um lado ao outro para navegar por páginas de conteúdo, assim como uma galeria.

## <a name="masterdetailpagemaster-detail-pagemd"></a>[MasterDetailPage](master-detail-page.md)

O [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) do Xamarin.Forms é uma página que gerencia duas páginas de informações relacionadas – uma página mestra que apresenta itens e uma página de detalhes que apresenta detalhes sobre os itens na página mestra.

## <a name="modal-pagesmodalmd"></a>[Páginas modais](modal.md)

O Xamarin.Forms também dá suporte a páginas modais. Uma página modal incentiva os usuários a concluir uma tarefa independente da qual não se pode sair via navegação até que essa tarefa seja concluída ou cancelada.

## <a name="displaying-pop-upspop-upsmd"></a>[Exibição de pop-ups](pop-ups.md)

O Xamarin.Forms fornece dois elementos de interface do usuário semelhantes a pop-up: um alerta e uma folha de ações. Esses elementos de interface podem ser usados para fazer perguntas simples a usuários e para orientar os usuários por meio de tarefas.
