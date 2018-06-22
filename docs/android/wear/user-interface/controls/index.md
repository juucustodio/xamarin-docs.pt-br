---
title: Controles de desgaste Android
ms.prod: xamarin
ms.assetid: 5B62A5F8-5E55-4B3C-BFC4-E21CDB27C08B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 2adae250d2f59e8eee0558a92c3845c5592aa01a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30774831"
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
