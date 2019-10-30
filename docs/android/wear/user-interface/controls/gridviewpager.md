---
title: GridViewPager
ms.prod: xamarin
ms.assetid: A1CDD5F0-049B-4DFA-A268-8A875D26A675
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/02/2018
ms.openlocfilehash: 77c5eb65601e5bfdac2cbaad2e196a8ae2813981
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030314"
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

Criar um [`GridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/GridPagerAdapter.html)
(ou subclasse, como [`FragmentGridPagerAdapter`](https://developer.android.com/reference/android/support/wearable/view/FragmentGridPagerAdapter.html)
para fornecer exibições a serem exibidas à medida que o usuário navega.

O [adaptador de exemplo](https://github.com/xamarin/monodroid-samples/blob/master/wear/GridViewPager/GridViewPager/SimpleGridPagerAdapter.cs) mostra como implementar os métodos necessários, incluindo substituições para `RowCount`, `GetColumnCount`, `GetBackground`e `GetFragment`

Conecte o adaptador conforme mostrado:

```csharp
pager.Adapter = new SimpleGridPagerAdapter (this, FragmentManager);
```

## <a name="related-links"></a>Links relacionados

- [Documento do seletor 2D do Google](https://developer.android.com/training/wearables/ui/2d-picker.html)
- [documentos do Android. support. portátil](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)
- [GridViewPager (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-gridviewpager)
