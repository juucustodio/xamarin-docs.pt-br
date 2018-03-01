---
title: Controles de desgaste Android
ms.topic: article
ms.prod: xamarin
ms.assetid: 5B62A5F8-5E55-4B3C-BFC4-E21CDB27C08B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 867246b3784fb244171058c7f4c6ec54092e813b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="android-wear-controls"></a>Controles de desgaste Android

Aplicativos do Android desgaste poderá usar muitos dos mesmos controles já em uso para aplicativos Android regulares, incluindo `Button`, `TextView`e drawables a imagem. Controles de layout incluindo `ScrollView`, `LinearLayout`, e `RelativateLayout` também pode ser usado.

Esta página vinculada aos controles específico do Android-desgaste do [vestir biblioteca de interface do usuário](https://developer.android.com/training/wearables/apps/layouts.html#UiLibrary) disponível em projetos do Xamarin via o [vestir suporte](http://www.nuget.org/packages/Xamarin.Android.Wear/) pacote NuGet. Esses controles incluem o seguinte:

-   **GridViewPager** &ndash; criar uma interface de navegação bidimensional, em que o usuário rolar para baixo e à direita para fazer uma seleção (para obter mais informações, consulte [GridViewPager](~/android/wear/user-interface/controls/gridviewpager.md)):

    ![Captura de tela de exemplo de um GridViewPager](images/gridviewpager.png)

Outros controles importantes para aplicativos de desgaste incluem:

* `BoxInsetLayout` (consulte [trabalhando com tamanhos de telas](~/android/wear/screen-sizes.md)),

* `WatchViewStub` (consulte [trabalhando com tamanhos de telas](~/android/wear/screen-sizes.md)),

* `CardFrame` (consulte [Android cartões criando](https://developer.android.com/training/wearables/ui/cards.html)),

* `CardScrollView` (consulte [Android cartões criando](https://developer.android.com/training/wearables/ui/cards.html)),

* `WearableListView` (consulte [Android criar lista](https://developer.android.com/training/wearables/ui/lists.html)).


## <a name="related-links"></a>Links relacionados

- [Documentos Android.Support.Wearable](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
