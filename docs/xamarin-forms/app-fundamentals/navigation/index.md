---
title: Navegação
description: Xamarin. Forms fornece um número de experiências de navegação de página diferentes, dependendo do tipo de página que está sendo usado.
ms.prod: xamarin
ms.assetid: BC5D0C6C-D5A9-4B12-A492-ED1F570CEC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 1a184e1ebfd9d87ba82642ebdfc30a8d3f92cce1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="navigation"></a>Navegação

_Xamarin. Forms fornece um número de experiências de navegação de página diferentes, dependendo do tipo de página que está sendo usado._

![](images/page-types.png "Tipos de página xamarin. Forms")

## <a name="hierarchical-navigationhierarchicalmd"></a>[Navegação hierárquica](hierarchical.md)

A classe [`NavigationPage`](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) oferece uma experiência de Navegação hierárquica em que o usuário é capaz de navegar pelas páginas para frente e para trás, conforme desejado. A classe implementa navegação como uma pilha UEPS (último a entrar, primeiro a sair) de objetos [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/).

## <a name="tabbedpagetabbed-pagemd"></a>[TabbedPage](tabbed-page.md)

O xamarin. Forms [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) consiste em uma lista de guias e uma área de detalhes maior, com cada guia Carregando conteúdo na área de detalhes.

## <a name="carouselpagecarousel-pagemd"></a>[CarouselPage](carousel-page.md)

O xamarin. Forms [ `CarouselPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/) é uma página que os usuários podem passar de lado a lado para navegar pelas páginas de conteúdo, como uma galeria.

## <a name="masterdetailpagemaster-detail-pagemd"></a>[MasterDetailPage](master-detail-page.md)

O xamarin. Forms [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) é uma página que gerencia as duas páginas de informações relacionadas a uma página mestra que apresenta itens e uma página de detalhes que apresenta detalhes sobre os itens na página mestra.

## <a name="modal-pagesmodalmd"></a>[Páginas modais](modal.md)

Xamarin. Forms também oferece suporte para páginas modais. Uma página modal incentiva os usuários a concluir uma tarefa independente da qual não se pode sair via navegação até que essa tarefa seja concluída ou cancelada.

## <a name="displaying-pop-upspop-upsmd"></a>[Exibição de pop-ups](pop-ups.md)

Xamarin. Forms fornece dois elementos de interface do usuário pop o tipo: um alerta e uma folha de ação. Esses elementos de interface podem ser usados para solicitar aos usuários perguntas simples e orientar os usuários por meio de tarefas.

