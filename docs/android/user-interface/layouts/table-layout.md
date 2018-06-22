---
title: TableLayout
ms.prod: xamarin
ms.assetid: 0C7B9C95-5E5F-A069-BA37-984E49F7DCAD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: f9a77655091e4b552bd4a9d440f50b6a3cbeabcc
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763641"
---
# <a name="tablelayout"></a>TableLayout

[`TableLayout`](https://developer.xamarin.com/api/type/Android.Widget.TableLayout/) é um [ `ViewGroup` ](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/) que exibe filho [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) elementos nas linhas e colunas.

Iniciar um novo projeto denominado **HelloTableLayout**.

Abra o **Resources/Layout/Main.axml** de arquivo e insira o seguinte:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TableLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:stretchColumns="1">

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Open..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-O"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Save..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-S"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Save As..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-Shift-S"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <View
        android:layout_height="2dip"
        android:background="#FF909090"/>

    <TableRow>
        <TextView
            android:text="X"
            android:padding="3dip"/>
        <TextView
            android:text="Import..."
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:text="X"
            android:padding="3dip"/>
        <TextView
            android:text="Export..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-E"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <View
        android:layout_height="2dip"
        android:background="#FF909090"/>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Quit"
            android:padding="3dip"/>
    </TableRow>
</TableLayout>
```

Observe como isso é semelhante a estrutura de uma tabela HTML. O [ `TableLayout` ](https://developer.xamarin.com/api/type/Android.Widget.TableLayout/) elemento é como o HTML `<table>` elemento; [ `TableRow` ](https://developer.xamarin.com/api/type/Android.Widget.TableRow/) é como uma `<tr>` elemento; mas para as células, você pode usar qualquer tipo de [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/) elemento. Neste exemplo, um [ `TextView` ](https://developer.xamarin.com/api/type/Android.Widget.TextView/) é usado para cada célula. Entre algumas das linhas, também há um basic [ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/), que é usado para desenhar uma linha horizontal.

Certifique-se de sua **HelloTableLayout** atividade carrega esse layout no [ `OnCreate()` ](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/) método:

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

O [ `SetContentView(int)` ](https://developer.xamarin.com/api/member/Android.App.Activity.SetContentView/(System.Int32)) método carrega o arquivo de layout para o [ `Activity` ](https://developer.xamarin.com/api/type/Android.App.Activity/), especificado pela ID de recurso &mdash; `Resource.Layout.Main` refere-se ao **recursos/Layout / Main.axml** arquivo de layout.

Execute o aplicativo. Você deve ver o seguinte:

[![Captura de tela de exemplo de aplicativo TableLayout exibindo várias linhas de tabela](table-layout-images/helloviews3.png)](table-layout-images/helloviews3.png#lightbox)



## <a name="references"></a>Referências

-   [`TableLayout`](https://developer.xamarin.com/api/type/Android.Widget.TableLayout/) 

-   [`TableRow`](https://developer.xamarin.com/api/type/Android.Widget.TableRow/) 

-   [`TextView`](https://developer.xamarin.com/api/type/Android.Widget.TextView/) 

*Partes desta página são modificações com base no trabalho criado e compartilhado pelo Android Abrir projeto de origem e usada de acordo com condições descritas no*
[*Creative Commons 2.5 atribuição de licença* ](http://creativecommons.org/licenses/by/2.5/).
