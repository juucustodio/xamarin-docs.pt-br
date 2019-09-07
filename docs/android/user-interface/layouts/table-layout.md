---
title: TableLayout Xamarin. Android
ms.prod: xamarin
ms.assetid: 0C7B9C95-5E5F-A069-BA37-984E49F7DCAD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 0e09bf2364df9b672a9612829eaa7a8ba343b0e9
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758393"
---
# <a name="xamarinandroid-tablelayout"></a>TableLayout Xamarin. Android

[`TableLayout`](xref:Android.Widget.TableLayout)é um[`ViewGroup`](xref:Android.Views.ViewGroup)
que exibe o filho[`View`](xref:Android.Views.View)
elementos em linhas e colunas.

Inicie um novo projeto chamado **HelloTableLayout**.

Abra o arquivo **Resources/layout/Main. axml** e insira o seguinte:

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

Observe como isso se assemelha à estrutura de uma tabela HTML. Dos[`TableLayout`](xref:Android.Widget.TableLayout)
o elemento é como o `<table>` elemento HTML;[`TableRow`](xref:Android.Widget.TableRow)
é como um `<tr>` elemento; mas, para as células, você pode usar qualquer tipo [`View`](xref:Android.Views.View) de elemento. Neste exemplo, um[`TextView`](xref:Android.Widget.TextView)
é usado para cada célula. Entre algumas das linhas, há também um básico [`View`](xref:Android.Views.View), que é usado para desenhar uma linha horizontal.

Verifique se sua atividade **HelloTableLayout** carrega esse layout no[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

O [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)método) carrega o arquivo de layout para [`Activity`](xref:Android.App.Activity)o, especificado pela ID &mdash; `Resource.Layout.Main` do recurso refere-se ao arquivo de layout **Resources/layout/Main. axml** .

Execute o aplicativo. Você deve ver o seguinte:

[![Captura de tela de exemplo do aplicativo TableLayout exibindo várias linhas da tabela](table-layout-images/helloviews3.png)](table-layout-images/helloviews3.png#lightbox)

## <a name="references"></a>Referências

- [`TableLayout`](xref:Android.Widget.TableLayout)
- [`TableRow`](xref:Android.Widget.TableRow)
- [`TextView`](xref:Android.Widget.TextView)

_Partes desta página são modificações com base no trabalho criado e compartilhado pelo projeto de software livre do Android e usadas de acordo com os termos descritos na [licença de atribuição do Creative Commons 2,5](http://creativecommons.org/licenses/by/2.5/)._
