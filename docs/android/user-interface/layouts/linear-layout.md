---
title: LinearLayout Xamarin. Android
ms.prod: xamarin
ms.assetid: B49D129C-AF24-3C5A-C833-5A34AFBB2442
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/07/2018
ms.openlocfilehash: 14e9b352a309de94a374b52141e3fd61715d8f75
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764379"
---
# <a name="xamarinandroid-linearlayout"></a>LinearLayout Xamarin. Android

[`LinearLayout`](xref:Android.Widget.LinearLayout)é um[`ViewGroup`](xref:Android.Views.ViewGroup)
que exibe o filho[`View`](xref:Android.Views.View)
elementos em uma direção linear, tanto vertical quanto horizontalmente.

Você deve ter cuidado ao usar o [`LinearLayout`](xref:Android.Widget.LinearLayout).
Se você começar a aninhar [`LinearLayout`](xref:Android.Widget.LinearLayout)vários s, talvez queira considerar o uso de um[`RelativeLayout`](xref:Android.Widget.RelativeLayout)
Stead.

Inicie um novo projeto chamado **HelloLinearLayout**.

Abra **Resources/layout/Main. axml** e insira o seguinte:

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

Inspecione cuidadosamente este XML. Há uma raiz[`LinearLayout`](xref:Android.Widget.LinearLayout)
Isso define sua orientação como vertical &ndash; todos os s filhos [`View`](xref:Android.Views.View)(dos quais ele tem dois) serão empilhados verticalmente. O primeiro filho é outro[`LinearLayout`](xref:Android.Widget.LinearLayout)
que usa uma orientação horizontal e o segundo filho é um[`LinearLayout`](xref:Android.Widget.LinearLayout)
que usa uma orientação vertical. Cada um desses s [`LinearLayout`](xref:Android.Widget.LinearLayout)aninhados contém vários[`TextView`](xref:Android.Widget.TextView)
elementos, que são orientados entre si da maneira definida por seu pai [`LinearLayout`](xref:Android.Widget.LinearLayout).

Agora, abra **HelloLinearLayout.cs** e verifique se ele carrega o layout de **recursos/layout/Main. axml** no[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

O [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)método) carrega o arquivo de layout para [`Activity`](xref:Android.App.Activity)o, especificado pela ID &ndash; `Resources.Layout.Main` do recurso refere-se ao arquivo de layout **Resources/layout/Main. axml** .

Execute o aplicativo. Você deve ver o seguinte:

[![Captura de tela do aplicativo primeiro LinearLayout organizada horizontalmente, segundo verticalmente](linear-layout-images/helloviews1.png)](linear-layout-images/helloviews1.png#lightbox)

Observe como os atributos XML definem o comportamento de cada exibição. Tente experimentar com valores diferentes para `android:layout_weight` ver como o espaço da tela é distribuído com base no peso de cada elemento. Consulte o documento [objetos de layout comuns](https://developer.android.com/guide/topics/ui/declaring-layout.html) para saber mais sobre como[`LinearLayout`](xref:Android.Widget.LinearLayout)
manipula o `android:layout_weight` atributo.

## <a name="references"></a>Referências

- [`LinearLayout`](xref:Android.Widget.LinearLayout)
- [`TextView`](xref:Android.Widget.TextView)

_Partes desta página são modificações com base no trabalho criado e compartilhado pelo projeto de software livre do Android e usadas de acordo com os termos descritos na [licença de atribuição do Creative Commons 2,5](http://creativecommons.org/licenses/by/2.5/)._
