---
title: GridViewPager
ms.topic: article
ms.prod: xamarin
ms.assetid: A1CDD5F0-049B-4DFA-A268-8A875D26A675
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/02/2018
ms.openlocfilehash: f156d802d807c4087331ca5796b046f8f5f2fa0d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
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
