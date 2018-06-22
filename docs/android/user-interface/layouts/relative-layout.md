---
title: Usando o RelativeLayout em xamarin
ms.prod: xamarin
ms.assetid: AFD9C849-02C3-E728-BC78-77A563612BC5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/25/2018
ms.openlocfilehash: cd2d7537036978e30c97b5776155e429178b6dac
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
ms.locfileid: "32435992"
---
# <a name="relativelayout"></a>RelativeLayout

[`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) é um [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/) que exibe filho [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) elementos em posições relativas. A posição de um [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) pode ser especificado como relativo ao elementos irmãos (por exemplo, para a esquerda do ou abaixo de um determinado elemento) ou em posições relativas para o [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) área (como alinhado à parte inferior, à esquerda do centro).

Um [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/) é um utilitário muito poderoso para criar uma interface do usuário porque ele pode eliminar aninhados [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)s. Se você estiver usando vários aninhadas [ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/) grupos, você poderá substituí-los com um único [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/).

Iniciar um novo projeto denominado **HelloRelativeLayout**.

Abra o **Resources/Layout/Main.axml** de arquivo e insira o seguinte:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <TextView
        android:id="@+id/label"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="Type here:"/>
    <EditText
        android:id="@+id/entry"
        android:layout_width="fill_parent"
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

Observe que cada uma da `android:layout_*` atributos, como `layout_below`, `layout_alignParentRight`, e `layout_toLeftOf`.
Ao usar um [ `RelativeLayout` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/), você pode usar esses atributos para descrever como você deseja posicionar cada [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/). Cada um desses atributos definem um tipo diferente de posição relativa. Alguns atributos usarão a ID de recurso do irmão [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) para definir a posição relativa. Por exemplo, a última [ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/) está definido para ajustar-se à esquerda do e alinhado-com-a-top do [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) identificado pela ID de `ok` (que é o anterior[`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)).

Todos os atributos de layout disponíveis são definidos no [ `RelativeLayout.LayoutParams` ](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/).

Certifique-se de carregar este layout no [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) método:

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

O [ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/p/System.Int32/) método carrega o arquivo de layout para o [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/), especificado pela ID de recurso &mdash; `Resource.Layout.Main` refere-se ao **recursos/Layout / Main.axml** arquivo de layout.

Execute o aplicativo. Você deve ver o layout do seguinte:

[![Captura de tela de um layout relativo com um TextView EditText e dois botões](relative-layout-images/helloviews2.png)](relative-layout-images/helloviews2.png#lightbox)


## <a name="resources"></a>Recursos

-   [`RelativeLayout`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout/)
-   [`RelativeLayout.LayoutParams`](https://developer.xamarin.com/api/type/Android.Widget.RelativeLayout+LayoutParams/)
-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/)
-   [`EditText`](https://developer.xamarin.com/api/type/Android.Widget.EditText/)
-   [`Button`](https://developer.xamarin.com/api/type/Android.Widget.Button/)


*Partes desta página são modificações com base no trabalho criado e compartilhado pelo Android Abrir projeto de origem e usada de acordo com condições descritas no*
[*Creative Commons 2.5 atribuição de licença* ](http://creativecommons.org/licenses/by/2.5/).
