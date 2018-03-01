---
title: LinearLayout
ms.topic: article
ms.prod: xamarin
ms.assetid: B49D129C-AF24-3C5A-C833-5A34AFBB2442
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 3cc5db39280c72f0de9dbdae07a49b56416c90a5
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="linearlayout"></a>LinearLayout

[`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) é um [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/) que exibe filho [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) elementos em uma direção linear, vertical ou horizontalmente.

Você deve ter cuidado usando excesso de [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/).
Se você começar a aninhamento vários [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)s, você talvez queira considerar o uso de um [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) em vez disso.

Iniciar um novo projeto denominado **HelloLinearLayout**.

Abra **Resources/Layout/Main.axml** e insira o seguinte:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation=    "vertical"
    android:layout_width=    "fill_parent"
    android:layout_height=    "fill_parent"    >

  <LinearLayout
      android:orientation=    "horizontal"
      android:layout_width=    "fill_parent"
      android:layout_height=    "fill_parent"
      android:layout_weight=    "1"    >
      <TextView
          android:text=    "red"
          android:gravity=    "center_horizontal"
          android:background=    "#aa0000"
          android:layout_width=    "wrap_content"
          android:layout_height=    "fill_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "green"
          android:gravity=    "center_horizontal"
          android:background=    "#00aa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "fill_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "blue"
          android:gravity=    "center_horizontal"
          android:background=    "#0000aa"
          android:layout_width=    "wrap_content"
          android:layout_height=    "fill_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "yellow"
          android:gravity=    "center_horizontal"
          android:background=    "#aaaa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "fill_parent"
          android:layout_weight=    "1"    />
  </LinearLayout>
        
  <LinearLayout
    android:orientation=    "vertical"
    android:layout_width=    "fill_parent"
    android:layout_height=    "fill_parent"
    android:layout_weight=    "1"    >
    <TextView
        android:text=    "row one"
        android:textSize=    "15pt"
        android:layout_width=    "fill_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row two"
        android:textSize=    "15pt"
        android:layout_width=    "fill_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row three"
        android:textSize=    "15pt"
        android:layout_width=    "fill_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row four"
        android:textSize=    "15pt"
        android:layout_width=    "fill_parent"
        android:layout_height=    "wrap_content"
       android:layout_weight=    "1"    />
  </LinearLayout>

</LinearLayout>
```

Inspecione cuidadosamente esse XML. Há uma raiz [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) que define sua orientação vertical &ndash; todos os filhos [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)s (do qual ele tem dois) serão empilhadas verticalmente. O primeiro filho é outra [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) que usa uma orientação horizontal e segundo filho é um [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) que usa uma orientação vertical. Cada uma dessas aninhados [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)s contêm vários [ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/) elementos que estão familiarizados com o outro da forma definida por seu pai, [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/).

Agora, abra **HelloLinearLayout.cs** e certifique-se de que ele carrega o **Resources/Layout/Main.axml** layout no [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) método:

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

O [ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32)) método carrega o arquivo de layout para o [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/), especificado pela ID de recurso &ndash; `Resources.Layout.Main` refere-se ao **recursos/Layout / Main.axml** arquivo de layout.

Execute o aplicativo. Você deve ver o seguinte:

[ ![Captura de tela do aplicativo LinearLayout primeiro organizado horizontalmente, verticalmente segundo](linear-layout-images/helloviews1.png)](linear-layout-images/helloviews1.png)

Observe como os atributos XML definem o comportamento de cada modo de exibição. Faça experiências com valores diferentes para `android:layout_weight` ver como o espaço de tela é distribuído com base no peso de cada elemento. Consulte o [objetos comuns de Layout](http://developer.android.com/guide/topics/ui/declaring-layout.html) documento para obter mais informações sobre como [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) identificadores de `android:layout_weight` atributo.

<a name="References" />

## <a name="references"></a>Referências

-   [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) 
-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/) 

*Partes desta página são modificações com base no trabalho criado e compartilhado pelo Android Abrir projeto de origem e usada de acordo com condições descritas no*
[*Creative Commons 2.5 atribuição de licença* ](http://creativecommons.org/licenses/by/2.5/).

