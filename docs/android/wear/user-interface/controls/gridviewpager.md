---
title: GridViewPager
ms.prod: xamarin
ms.assetid: A1CDD5F0-049B-4DFA-A268-8A875D26A675
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/02/2018
ms.openlocfilehash: 3a0b1ec9359b1c6067c253b4d04126dbdd726cc5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763430"
---
# <a name="gridviewpager"></a>GridViewPager

O [GridViewPager](https://developer.xamarin.com/samples/GridViewPager/) demonstra como implementar o padrão de navegação de seletor 2D para desgaste Android.

![Captura de tela de exemplo de GridViewPager em uma exibição de quadrado](gridviewpager-images/gridviewpager.png)

Primeiro, adicione o [Xamarin Android desgaste suporte](http://www.nuget.org/packages/Xamarin.Android.Wear/) pacote NuGet ao seu projeto.

O layout do XML tem esta aparência:

```xml
<android.support.wearable.view.GridViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:keepScreenOn="true" />
```

Criar um [ `GridPagerAdapter` ](http://developer.android.com/reference/android/support/wearable/view/GridPagerAdapter.html) (ou subclasse como [ `FragmentGridPagerAdapter` ](http://developer.android.com/reference/android/support/wearable/view/FragmentGridPagerAdapter.html) para fornecer exibições para exibir como o usuário navega.

O [adaptador de exemplo](https://github.com/xamarin/monodroid-samples/blob/master/wear/GridViewPager/GridViewPager/SimpleGridPagerAdapter.cs) mostra como implementar os métodos necessários, incluindo substituições para `RowCount`, `GetColumnCount`, `GetBackground`, e `GetFragment`

Conectar o adaptador conforme mostrado:

```csharp
pager.Adapter = new SimpleGridPagerAdapter (this, FragmentManager);
```



## <a name="related-links"></a>Links relacionados

- [Doc de seletor 2D do Google](https://developer.android.com/training/wearables/ui/2d-picker.html)
- [documentos Android.support.wearable](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [GridViewPager (sample)](https://developer.xamarin.com/samples/GridViewPager/)
