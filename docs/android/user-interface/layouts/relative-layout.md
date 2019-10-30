---
title: Usando o RelativeLayout no Xamarin. Android
description: Como usar o RelativeLayout em um aplicativo Xamarin. Android
ms.prod: xamarin
ms.assetid: AFD9C849-02C3-E728-BC78-77A563612BC5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/29/2018
ms.openlocfilehash: 6cac771f46242cc0475be0a7ec0d475950f4b4e1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028804"
---
# <a name="xamarinandroid-relativelayout"></a>RelativeLayout Xamarin. Android

[`RelativeLayout`](xref:Android.Widget.RelativeLayout) é um [`ViewGroup`](xref:Android.Views.ViewGroup) que exibe [`View`](xref:Android.Views.View) filho
elementos em posições relativas. A posição de um [`View`](xref:Android.Views.View) pode ser especificada em relação a elementos irmãos (como à esquerda ou abaixo de um determinado elemento) ou em posições relativas à [`RelativeLayout`](xref:Android.Widget.RelativeLayout)
área (como alinhada à parte inferior, esquerda do centro).

Um [`RelativeLayout`](xref:Android.Widget.RelativeLayout) é um utilitário muito potente para criar uma interface do usuário, pois ele pode eliminar [`ViewGroup`](xref:Android.Views.ViewGroup)s aninhados. Se você estiver usando vários [`LinearLayout`](xref:Android.Widget.LinearLayout) aninhados
grupos, você poderá substituí-los por um único [`RelativeLayout`](xref:Android.Widget.RelativeLayout).

Inicie um novo projeto chamado **HelloRelativeLayout**.

Abra o arquivo **Resources/layout/Main. axml** e insira o seguinte:

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

Observe cada um dos atributos de `android:layout_*`, como `layout_below`, `layout_alignParentRight`e `layout_toLeftOf`.
Ao usar um [`RelativeLayout`](xref:Android.Widget.RelativeLayout), você pode usar esses atributos para descrever como você deseja posicionar cada [`View`](xref:Android.Views.View). Cada um desses atributos define um tipo diferente de posição relativa. Alguns atributos usam a ID de recurso de um [`View`](xref:Android.Views.View) irmão para definir sua própria posição relativa. Por exemplo, a última [`Button`](xref:Android.Widget.Button) é definida para estar à esquerda e alinhada com a parte superior da [`View`](xref:Android.Views.View) identificada pela ID `ok` (que é a [`Button`](xref:Android.Widget.Button)anterior).

Todos os atributos de layout disponíveis são definidos em [`RelativeLayout.LayoutParams`](xref:Android.Widget.RelativeLayout.LayoutParams).

Certifique-se de carregar esse layout no [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

O método [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*) carrega o arquivo de layout para o [`Activity`](xref:Android.App.Activity), especificado pela ID de recurso &mdash; `Resource.Layout.Main` refere-se ao arquivo de layout **Resources/layout/Main. axml** .

Execute o aplicativo. Você deverá ver o seguinte layout:

[![captura de tela de um layout relativo com um TextView, EditText e dois botões](relative-layout-images/helloviews2.png)](relative-layout-images/helloviews2.png#lightbox)

## <a name="resources"></a>Recursos

- [`RelativeLayout`](xref:Android.Widget.RelativeLayout)
- [`RelativeLayout.LayoutParams`](xref:Android.Widget.RelativeLayout.LayoutParams)
- [`TextView`](xref:Android.Widget.TextView)
- [`EditText`](xref:Android.Widget.EditText)
- [`Button`](xref:Android.Widget.Button)

_Partes desta página são modificações com base no trabalho criado e compartilhado pelo projeto de software livre do Android e usadas de acordo com os termos descritos na [licença de atribuição do Creative Commons 2,5](https://creativecommons.org/licenses/by/2.5/)._
