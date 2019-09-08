---
title: Layouts com guias
description: Uma visão geral dos layouts com guias no Android
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/08/2017
ms.openlocfilehash: 1e0632eb921b25fef40b8f0483ab80d62c9e1235
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762191"
---
# <a name="tabbed-layouts"></a>Layouts com guias

## <a name="overview"></a>Visão geral

As guias são um padrão popular de interface do usuário em aplicativos móveis devido à sua simplicidade e usabilidade. Eles fornecem uma maneira consistente e fácil de navegar entre várias telas em um aplicativo. O Android tem várias APIs para interfaces com guias: 

- **ActionBar** &ndash; Isso faz parte de um novo conjunto de APIs que foi introduzido no Android 3,0 (API nível 11) com o objetivo de fornecer uma interface consistente de navegação e de alternância de exibição. Foi feito o back Port para Android 2,2 (API nível 8) com a [biblioteca de suporte do Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/). 

- **PagerTabStrip** Indica as páginas atual, próxima e anterior de a `ViewPager`. &ndash; `ViewPager`está disponível somente por meio da [biblioteca de suporte do Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).
     Para obter mais informações `PagerTabStrip`sobre o, consulte [ViewPager](~/android/user-interface/controls/view-pager/index.md).

- **Barra de ferramentas** é um componente de barra de ação mais novo e flexível `ActionBar`que substitui. &ndash; `Toolbar` `Toolbar`está disponível no Android 5,0 pirulito ou posterior, e também está disponível para versões mais antigas do Android por meio do pacote NuGet da [biblioteca de suporte do Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) . 
    `Toolbar`Atualmente é o componente de barra de ação recomendado a ser usado em aplicativos Android.
    Para obter mais informações, consulte [barra de ferramentas](~/android/user-interface/controls/tool-bar/index.md). 

## <a name="related-links"></a>Links relacionados

- [Design de material-guias](https://material.io/guidelines/components/tabs.html)- [ActionBar](https://developer.android.com/guide/topics/ui/actionbar.html)
- [Biblioteca de suporte do Android v7 AppCompat do pacote NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [biblioteca do v7 AppCompat](https://developer.android.com/tools/support-library/features.html#v7-appcompat)
