---
title: GridViewPager
ms.prod: xamarin
ms.assetid: A1CDD5F0-049B-4DFA-A268-8A875D26A675
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/02/2018
ms.openlocfilehash: 5bdff0aee39375d8de296849056660f69a7d907f
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198529"
---
# <a name="gridviewpager"></a>GridViewPager

O exemplo [GridViewPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-gridviewpager) demonstra como implementar o padrão de navegação do seletor 2D para o desgaste do Android.

![Captura de tela de exemplo de GridViewPager em uma exibição quadrada](gridviewpager-images/gridviewpager.png)

Primeiro, adicione o pacote NuGet do [Xamarin Android que dá suporte](https://www.nuget.org/packages/Xamarin.Android.Wear/) ao seu projeto.

O XML de layout tem a seguinte aparência:

```xml
<android.support.wearable.view.GridViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:keepScreenOn="true" />
```

Criar um[`GridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/GridPagerAdapter.html)
(ou subclasse como[`FragmentGridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/FragmentGridPagerAdapter.html)
para fornecer exibições a serem exibidas à medida que o usuário navega.

O [adaptador de exemplo](https://github.com/xamarin/monodroid-samples/blob/master/wear/GridViewPager/GridViewPager/SimpleGridPagerAdapter.cs) mostra como implementar os métodos necessários, incluindo substituições `RowCount`para `GetColumnCount`, `GetBackground`, e`GetFragment`

Conecte o adaptador conforme mostrado:

```csharp
pager.Adapter = new SimpleGridPagerAdapter (this, FragmentManager);
```



## <a name="related-links"></a>Links relacionados

- [Documento do seletor 2D do Google](https://developer.android.com/training/wearables/ui/2d-picker.html)
- [documentos do Android. support. portátil](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [GridViewPager (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-gridviewpager)
