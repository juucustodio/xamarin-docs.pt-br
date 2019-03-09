---
title: Layouts com guias
description: Uma visão geral dos Layouts com guias no Android
ms.prod: xamarin
ms.assetid: 1CFF590A-AC86-C3B3-36CA-A70248BC7F97
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/08/2017
---

# <a name="tabbed-layouts"></a>Layouts com guias


## <a name="overview"></a>Visão geral

As guias são um padrão de interface do usuário populares em aplicativos móveis devido à sua simplicidade e a usabilidade. Eles fornecem uma maneira consistente e fácil de navegar entre várias telas em um aplicativo. O Android tem várias APIs para interfaces com guias: 

-   **ActionBar** &ndash; isso faz parte de um novo conjunto de API que foi introduzido no Android 3.0 (API nível 11) com o objetivo de fornecer uma consistente interface de alternar para modo de exibição e navegação. Ele foi movido volta para o Android 2.2 (API nível 8) com o [biblioteca de suporte ao Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/). 

-   **PagerTabStrip** &ndash; indica as páginas anteriores e posteriores de atual, de um `ViewPager`. `ViewPager` está disponível somente via [biblioteca de suporte ao Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/).
     Para obter mais informações sobre `PagerTabStrip`, consulte [ViewPager](~/android/user-interface/controls/view-pager/index.md).

-   **Barra de ferramentas** &ndash; `Toolbar` é um componente de barra de ação mais recente e mais flexível que substitui `ActionBar`. `Toolbar` está disponível no Android 5.0 Lollipop ou posterior, e também está disponível para versões mais antigas do Android por meio de [biblioteca de suporte ao Android v7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) pacote do NuGet. 
    `Toolbar` no momento é o componente de barra de ação recomendada para uso em aplicativos Android.
    Para obter mais informações, consulte [barra de ferramentas](~/android/user-interface/controls/tool-bar/index.md). 



## <a name="related-links"></a>Links relacionados

- [Guias de Design de material -](https://material.io/guidelines/components/tabs.html)- [ActionBar](https://developer.android.com/guide/topics/ui/actionbar.html)
- [Pacote do NuGet do suporte do Android biblioteca v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
- [biblioteca de appcompat v7](https://developer.android.com/tools/support-library/features.html#v7-appcompat)
