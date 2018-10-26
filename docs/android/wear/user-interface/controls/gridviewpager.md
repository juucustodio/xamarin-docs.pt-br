---
title: GridViewPager
ms.prod: xamarin
ms.assetid: A1CDD5F0-049B-4DFA-A268-8A875D26A675
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/02/2018
ms.openlocfilehash: 1cb71fa2c73b9ab151555559b22def4be1cf5c73
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112761"
---
# <a name="gridviewpager"></a>GridViewPager

O [GridViewPager](https://developer.xamarin.com/samples/GridViewPager/) que demonstra como implementar o padrão de navegação do seletor 2D para o Android Wear.

![Captura de tela do exemplo de GridViewPager em uma tela quadrada](gridviewpager-images/gridviewpager.png)

Primeiro adicione a [suporte do Xamarin Android Wear](http://www.nuget.org/packages/Xamarin.Android.Wear/) pacote NuGet ao seu projeto.

O layout XML tem esta aparência:

```xml
<android.support.wearable.view.GridViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:keepScreenOn="true" />
```

Criar um [`GridPagerAdapter`](http://developer.android.com/reference/android/support/wearable/view/GridPagerAdapter.html)
(ou subclasse como [`FragmentGridPagerAdapter`](http://developer.android.com/reference/android/support/wearable/view/FragmentGridPagerAdapter.html)
para fornecer modos de exibição a ser exibido como o usuário navega.

O [adaptador de amostra](https://github.com/xamarin/monodroid-samples/blob/master/wear/GridViewPager/GridViewPager/SimpleGridPagerAdapter.cs) mostra como implementar os métodos necessários, incluindo substituições para `RowCount`, `GetColumnCount`, `GetBackground`, e `GetFragment`

Conectar o adaptador conforme mostrado:

```csharp
pager.Adapter = new SimpleGridPagerAdapter (this, FragmentManager);
```



## <a name="related-links"></a>Links relacionados

- [Documento de seletor 2D do Google](https://developer.android.com/training/wearables/ui/2d-picker.html)
- [Android.support.wearable docs](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [GridViewPager (amostra)](https://developer.xamarin.com/samples/GridViewPager/)
