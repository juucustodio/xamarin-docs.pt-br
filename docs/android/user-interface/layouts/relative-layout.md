---
title: Usando o RelativeLayout no Xamarin. Android
description: Como usar o RelativeLayout em um aplicativo Xamarin. Android
ms.prod: xamarin
ms.assetid: AFD9C849-02C3-E728-BC78-77A563612BC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/29/2018
ms.openlocfilehash: af74ae3c7c87f501bff519bcfa361264205ca3f1
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522379"
---
# <a name="xamarinandroid-relativelayout"></a>RelativeLayout Xamarin. Android

[`RelativeLayout`](xref:Android.Widget.RelativeLayout)é um [`ViewGroup`](xref:Android.Views.ViewGroup) que exibe filho[`View`](xref:Android.Views.View)
elementos em posições relativas. A posição de um [`View`](xref:Android.Views.View) pode ser especificada como relativa a elementos irmãos (como à esquerda ou abaixo de um determinado elemento) ou em posições relativas ao[`RelativeLayout`](xref:Android.Widget.RelativeLayout)
área (como alinhada à parte inferior, esquerda do centro).

Um [`RelativeLayout`](xref:Android.Widget.RelativeLayout) é um utilitário muito potente para criar uma interface do usuário, pois ele pode [`ViewGroup`](xref:Android.Views.ViewGroup)eliminar s aninhados. Se você mesmo se encontrar usando vários aninhados[`LinearLayout`](xref:Android.Widget.LinearLayout)
grupos, você poderá substituí-los por um único [`RelativeLayout`](xref:Android.Widget.RelativeLayout).

Inicie um novo projeto chamado **HelloRelativeLayout**.

Abra o arquivo Resources **/layout/Main. axml** e insira o seguinte:

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

Observe cada um dos `android:layout_*` atributos, `layout_below`como, `layout_alignParentRight`e `layout_toLeftOf`.
Ao usar um [`RelativeLayout`](xref:Android.Widget.RelativeLayout), você pode usar esses atributos para descrever como deseja posicionar cada um [`View`](xref:Android.Views.View)deles. Cada um desses atributos define um tipo diferente de posição relativa. Alguns atributos usam a ID de recurso de um [`View`](xref:Android.Views.View) irmão para definir sua própria posição relativa. Por exemplo, o último [`Button`](xref:Android.Widget.Button) é definido para estar à esquerda e alinhado com a parte superior [`View`](xref:Android.Views.View) do identificado pela ID `ok` (que é a anterior [`Button`](xref:Android.Widget.Button)).

Todos os atributos de layout disponíveis são definidos em [`RelativeLayout.LayoutParams`](xref:Android.Widget.RelativeLayout.LayoutParams).

Certifique-se de carregar esse layout no[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

O [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*) método carrega o arquivo de layout para [`Activity`](xref:Android.App.Activity)o, especificado pela ID &mdash; `Resource.Layout.Main` do recurso refere-se ao arquivo de layout Resources **/layout/Main. axml** .

Execute o aplicativo. Você deverá ver o seguinte layout:

[![Captura de tela de um layout relativo com um TextView, EditText e dois botões](relative-layout-images/helloviews2.png)](relative-layout-images/helloviews2.png#lightbox)

## <a name="resources"></a>Recursos

- [`RelativeLayout`](xref:Android.Widget.RelativeLayout)
- [`RelativeLayout.LayoutParams`](xref:Android.Widget.RelativeLayout.LayoutParams)
- [`TextView`](xref:Android.Widget.TextView)
- [`EditText`](xref:Android.Widget.EditText)
- [`Button`](xref:Android.Widget.Button)

_Partes desta página são modificações com base no trabalho criado e compartilhado pelo projeto de software livre do Android e usadas de acordo com os termos descritos na [licença de atribuição do Creative Commons 2,5](http://creativecommons.org/licenses/by/2.5/)._
