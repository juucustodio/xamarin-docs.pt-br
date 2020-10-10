---
title: TableLayout Xamarin. Android
ms.prod: xamarin
ms.assetid: 0C7B9C95-5E5F-A069-BA37-984E49F7DCAD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/08/2020
ms.openlocfilehash: e33dbae13dc9b7af7649777f16bf5d23e11209ad
ms.sourcegitcommit: 124d845f8d2768353e8b7fe1ab1d959a589367f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91872258"
---
# <a name="xamarinandroid-tablelayout"></a>TableLayout Xamarin. Android

[`TableLayout`](xref:Android.Widget.TableLayout) é um [`ViewGroup`](xref:Android.Views.ViewGroup)
que exibe o filho [`View`](xref:Android.Views.View)
elementos em linhas e colunas.

Inicie um novo projeto chamado **HelloTableLayout**.

Abra o arquivo **Resources/layout/content_main.xml** e insira o seguinte:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TableLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:stretchColumns="1">

    <TableRow
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_column="1"
            android:text="Open..."
            android:padding="3dip"/>
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Ctrl-O"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <TableRow
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_column="1"
            android:text="Save..."
            android:padding="3dip"/>
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Ctrl-S"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <TableRow
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_column="1"
            android:text="Save As..."
            android:padding="3dip"/>
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Ctrl-Shift-S"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <View
        android:layout_width="wrap_content"
        android:layout_height="2dip"
        android:background="#FF909090"/>

    <TableRow
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="X"
            android:padding="3dip"/>
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Import..."
            android:padding="3dip"/>
    </TableRow>

    <TableRow
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="X"
            android:padding="3dip"/>
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Export..."
            android:padding="3dip"/>
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Ctrl-E"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <View
        android:layout_width="wrap_content"
        android:layout_height="2dip"
        android:background="#FF909090"/>

    <TableRow
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_column="1"
            android:text="Quit"
            android:padding="3dip"/>
    </TableRow>
</TableLayout>
```

Observe como isso se assemelha à estrutura de uma tabela HTML. O [`TableLayout`](xref:Android.Widget.TableLayout)
o elemento é como o `<table>` elemento HTML; [`TableRow`](xref:Android.Widget.TableRow)
é como um `<tr>` elemento; mas, para as células, você pode usar qualquer tipo de [`View`](xref:Android.Views.View) elemento. Neste exemplo, um [`TextView`](xref:Android.Widget.TextView)
é usado para cada célula. Entre algumas das linhas, há também um básico [`View`](xref:Android.Views.View) , que é usado para desenhar uma linha horizontal.

Verifique se sua atividade **HelloTableLayout** carrega esse layout no [`OnCreate()`](xref:Android.App.Activity.OnCreate*)
forma

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

O [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*) método) carrega o arquivo de layout para o [`Activity`](xref:Android.App.Activity) , especificado pela ID do recurso &mdash; `Resource.Layout.Main` refere-se ao arquivo de layout **Resources/layout/Main. axml** .

Executar o aplicativo. Você deve ver o seguinte:

[![Captura de tela de exemplo do aplicativo TableLayout exibindo várias linhas da tabela](table-layout-images/helloviews3.png)](table-layout-images/helloviews3.png#lightbox)

## <a name="references"></a>Referências

- [`TableLayout`](xref:Android.Widget.TableLayout)
- [`TableRow`](xref:Android.Widget.TableRow)
- [`TextView`](xref:Android.Widget.TextView)

_Partes desta página são modificações com base no trabalho criado e compartilhado pelo projeto de software livre do Android e usadas de acordo com os termos descritos na [licença de atribuição do Creative Commons 2,5](https://creativecommons.org/licenses/by/2.5/)._
