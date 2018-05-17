---
title: Layouts com guias
description: Uma visão geral dos Layouts com guias no Android
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/08/2017
ms.openlocfilehash: 53ed5f91583d43839e96388194aea8c0d6ac5315
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
---
# <a name="tabbed-layouts"></a>Layouts com guias


## <a name="overview"></a>Visão geral

Guias são um padrão de interface de usuário populares em aplicativos móveis devido à sua simplicidade e facilidade de uso. Eles fornecem uma maneira fácil e consistente para navegar entre várias telas em um aplicativo. Android tem várias APIs para interfaces com guias: 

-   **Barra de ação** &ndash; isso faz parte de um novo conjunto de API que foi introduzido no Android 3.0 (API nível 11) com o objetivo de fornecer uma consistente interface de alternância de exibição e navegação. Ela volta portada para Android 2.2 (API nível 8) com o [biblioteca de suporte Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/). 

-   **PagerTabStrip** &ndash; indica as páginas de atual, próxima e anterior de um `ViewPager`. `ViewPager` está disponível apenas via [biblioteca de suporte Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).
     Para obter mais informações sobre `PagerTabStrip`, consulte [ViewPager](~/android/user-interface/controls/view-pager/index.md).

-   **Barra de ferramentas** &ndash; `Toolbar` é um componente de barra de ação mais flexíveis e mais recentes que substitui `ActionBar`. `Toolbar` está disponível no Android pirulito de 5.0 ou posterior, e também está disponível para versões mais antigas do Android por meio de [biblioteca de suporte Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) pacote NuGet. 
    `Toolbar` no momento é o componente de barra de ação recomendada para usar em aplicativos Android.
    Para obter mais informações, consulte [barra de ferramentas](~/android/user-interface/controls/tool-bar/index.md). 



## <a name="related-links"></a>Links relacionados

- [Guias de Design material -](https://material.io/guidelines/components/tabs.html)- [barra de ação](http://developer.android.com/guide/topics/ui/actionbar.html)
- [Pacote do NuGet do suporte do Android biblioteca v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [biblioteca de appcompat v7](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
