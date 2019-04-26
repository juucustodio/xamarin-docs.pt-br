---
title: LinearLayout
ms.prod: xamarin
ms.assetid: B49D129C-AF24-3C5A-C833-5A34AFBB2442
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/07/2018
ms.openlocfilehash: f3d0394f6b2388918f728bd5a25e9e809a832ca6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61187081"
---
# <a name="linearlayout"></a>LinearLayout

[`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) is a [`ViewGroup`](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)
que exibe o filho [`View`](https://developer.xamarin.com/api/type/Android.Views.View/)
os elementos em uma direção linear, vertical ou horizontalmente.

Você deve ter cuidado em excesso usando o [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/).
Se você começar o aninhamento múltiplo [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)s, você talvez queira considerar o uso de um [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
em vez disso.

Iniciar um novo projeto chamado **HelloLinearLayout**.

Abra **Resources/Layout/Main.axml** e insira o seguinte:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation=    "vertical"
    android:layout_width=    "match_parent"
    android:layout_height=    "match_parent"    >

  <LinearLayout
      android:orientation=    "horizontal"
      android:layout_width=    "match_parent"
      android:layout_height=    "match_parent"
      android:layout_weight=    "1"    >
      <TextView
          android:text=    "red"
          android:gravity=    "center_horizontal"
          android:background=    "#aa0000"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "green"
          android:gravity=    "center_horizontal"
          android:background=    "#00aa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "blue"
          android:gravity=    "center_horizontal"
          android:background=    "#0000aa"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "yellow"
          android:gravity=    "center_horizontal"
          android:background=    "#aaaa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
  </LinearLayout>
        
  <LinearLayout
    android:orientation=    "vertical"
    android:layout_width=    "match_parent"
    android:layout_height=    "match_parent"
    android:layout_weight=    "1"    >
    <TextView
        android:text=    "row one"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row two"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row three"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row four"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
       android:layout_weight=    "1"    />
  </LinearLayout>

</LinearLayout>
```

Inspecione cuidadosamente esse XML. Há uma raiz [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
que define sua orientação para ser vertical &ndash; todos os filhos [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)s (dos quais ele tem duas) serão empilhados verticalmente. O primeiro filho é outra [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
que usa uma orientação horizontal e o segundo filho é um [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
que usa uma orientação vertical. Cada um deles aninhado [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)s contêm vários [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/)
elementos, que são orientados uns com os outros da forma definida por seu pai [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/).

Agora, abra **HelloLinearLayout.cs** e certifique-se de que ele carrega o **Resources/Layout/Main.axml** layout no [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)
método:

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

O [ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32)) método carrega o arquivo de layout para o [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/), especificado pela ID de recurso &ndash; `Resources.Layout.Main` refere-se ao **recursos/Layout / Main. axml** arquivo de layout.

Execute o aplicativo. Você verá o seguinte:

[![Captura de tela do aplicativo primeiro LinearLayout organizado horizontalmente, verticalmente segundo](linear-layout-images/helloviews1.png)](linear-layout-images/helloviews1.png#lightbox)

Observe como os atributos XML definem o comportamento do cada modo de exibição. Faça experiências com valores diferentes para `android:layout_weight` ver como o real da tela é distribuído com base no peso de cada elemento. Consulte a [objetos comuns do Layout](https://developer.android.com/guide/topics/ui/declaring-layout.html) documento para obter mais informações sobre como [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
identificadores de `android:layout_weight` atributo.


## <a name="references"></a>Referências

-   [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) 
-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/) 

*Partes desta página são modificações com base no trabalho criado e compartilhado por Android Open Source Project e usadas de acordo com os termos descritos na*
[*2.5 atribuição de licença da Creative Commons* ](http://creativecommons.org/licenses/by/2.5/).

