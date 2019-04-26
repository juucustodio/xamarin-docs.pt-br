---
title: Usando o RelativeLayout no xamarin. Android
description: Como usar o RelativeLayout em um aplicativo xamarin. Android
ms.prod: xamarin
ms.assetid: AFD9C849-02C3-E728-BC78-77A563612BC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/29/2018
ms.openlocfilehash: af2972ecc92435836a75013e6203ba47c2c04627
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61303611"
---
# <a name="relativelayout"></a>RelativeLayout

[`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) é um [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/) que exibe o filho [`View`](https://developer.xamarin.com/api/type/Android.Views.View/)
elementos em posições relativas. A posição de um [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) podem ser especificados como relativos a elementos irmãos (por exemplo, como para a esquerda do ou abaixo de um determinado elemento) ou em posições relativas para a [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
área (como alinhado à parte inferior, à esquerda do centro).

Um [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) é um utilitário muito poderoso para o aninhamento de criação de uma interface de usuário porque ele pode eliminar [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)s. Se você estiver usando vários aninhadas [`LinearLayout`](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)
grupos, você poderá substituí-los com um único [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/).

Iniciar um novo projeto chamado **HelloRelativeLayout**.

Abra o **Resources/Layout/Main.axml** de arquivo e insira o seguinte:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <TextView
        android:id="@+id/label"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Type here:"/>
    <EditText
        android:id="@+id/entry"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@android:drawable/editbox_background"
        android:layout_below="@id/label"/>
    <Button
        android:id="@+id/ok"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@id/entry"
        android:layout_alignParentRight="true"
        android:layout_marginLeft="10dip"
        android:text="OK" />
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toLeftOf="@id/ok"
        android:layout_alignTop="@id/ok"
        android:text="Cancel" />
</RelativeLayout>
```

Observe que cada um dos `android:layout_*` atributos, como `layout_below`, `layout_alignParentRight`, e `layout_toLeftOf`.
Ao usar um [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/), você pode usar esses atributos para descrever como você deseja posicionar cada [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/). Cada um desses atributos definem um tipo diferente de posição relativa. Alguns atributos usam a ID do recurso de um irmão [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) para definir sua própria posição relativa. Por exemplo, a última [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) é definido para ficar à esquerda do e alinhado-com-the-top dos [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) identificada pela ID `ok` (que é o anterior [`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)).

Todos os atributos de layout disponíveis são definidos no [ `RelativeLayout.LayoutParams` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/).

Verifique se você carregar esse layout na [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)
método:

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

O [ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/p/System.Int32/) método carrega o arquivo de layout para o [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/), especificado pela ID de recurso &mdash; `Resource.Layout.Main` refere-se ao **recursos/Layout / Main. axml** arquivo de layout.

Execute o aplicativo. Você deve ver o seguinte layout:

[![Captura de tela de um layout relativo com um TextView EditText e dois botões](relative-layout-images/helloviews2.png)](relative-layout-images/helloviews2.png#lightbox)


## <a name="resources"></a>Recursos

-   [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
-   [`RelativeLayout.LayoutParams`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/)
-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/)
-   [`EditText`](https://developer.xamarin.com/api/type/Android.Widget.EditText/)
-   [`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)


*Partes desta página são modificações com base no trabalho criado e compartilhado por Android Open Source Project e usadas de acordo com os termos descritos na*
[*2.5 atribuição de licença da Creative Commons* ](http://creativecommons.org/licenses/by/2.5/).
