---
title: GridLayout
ms.prod: xamarin
ms.assetid: B69A4BF5-9CFB-443A-9F7B-062D1E498F61
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 53f2ba8ff14a4338310e02244acdbfd7fa9bc13c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30768500"
---
# <a name="gridlayout"></a>GridLayout

O `GridLayout` é um novo `ViewGroup` subclasse que dá suporte a layout de modos de exibição em uma grade 2D, semelhante a uma tabela HTML, conforme mostrado abaixo:

 [![Cortada GridLayout exibe as quatro células](grid-layout-images/21-gridlayoutcropped.png)](grid-layout-images/21-gridlayoutcropped.png#lightbox)

 `GridLayout` funciona com uma hierarquia do modo de exibição simples, onde exibições filho defina seus locais na grade, especificando as linhas e colunas devem estar no. Dessa forma, o *GridLayout* pode posicionar os modos de exibição na grade sem a necessidade de qualquer exibição intermediária fornece uma estrutura de tabela, como visto nas linhas da tabela usadas no TableLayout. Mantendo uma hierarquia simples, *GridLayout* é capaz de layout mais rapidamente seus modos de exibição do filho. Vamos dar uma olhada em um exemplo para ilustrar esse conceito realmente significado no código.


## <a name="creating-a-grid-layout"></a>Criar um Layout de grade

O XML a seguir adiciona várias `TextView` controles a uma *GridLayout*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 2"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip" />
</GridLayout>
```

O layout será ajustar os tamanhos de linha e coluna para que as células podem se ajustar a seu conteúdo, conforme ilustrado pelo diagrama a seguir:

 [![Diagrama de layout mostrando duas células à esquerda inferior à direita](grid-layout-images/gridlayout-cells.png)](grid-layout-images/gridlayout-cells.png#lightbox)

Isso resulta na interface do usuário seguintes quando executado em um aplicativo:

 [![Captura de tela de GridLayoutDemo aplicativo exibe as quatro células](grid-layout-images/01-gridlayout.png)](grid-layout-images/01-gridlayout.png#lightbox)



## <a name="specifying-orientation"></a>Especificar a orientação

Observe no XML acima, cada `TextView` não especifica uma linha ou coluna. Quando eles não forem especificados, o `GridLayout` atribui cada exibição filho em ordem, dependendo da orientação. Por exemplo, vamos alterar orientação da GridLayout do padrão, que é horizontal, vertical para como este:

```xml
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2"
        android:orientation="vertical">
</GridLayout>
```

Agora, o `GridLayout` posicionará as células de cima para baixo em cada coluna, em vez da esquerda para a direita, conforme mostrado abaixo:

 [![Diagrama que ilustra como as células são posicionadas na orientação vertical](grid-layout-images/gridlayoutorientation.png)](grid-layout-images/gridlayoutorientation.png#lightbox)

Isso resulta na interface do usuário em tempo de execução:

 [![Captura de tela de GridLayoutDemo com células posicionadas na orientação vertical](grid-layout-images/02-gridlayout.png)](grid-layout-images/02-gridlayout.png#lightbox)



### <a name="specifying-explicit-position"></a>Especificação de posição explícitos

Se queremos controlar explicitamente as posições das exibições filho no `GridLayout`, podemos definir seus `layout_row` e `layout_column` atributos. Por exemplo, o seguinte XML resultará no layout mostrado na primeira captura de tela (mostrada acima), independentemente da orientação.

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="1" />
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="1"
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"
            android:layout_row="1"
            android:layout_column="1"  />
</GridLayout>
```



### <a name="specifying-spacing"></a>Especifica o espaçamento

Temos algumas opções que fornecerá o espaçamento entre o filho modos de exibição da `GridLayout`. Podemos usar o `layout_margin` atributo para definir a margem de cada exibição filho diretamente, conforme mostrado abaixo

```xml
<TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0"
            android:layout_margin="10dp" />
```

Além disso, no Android 4, uma nova exibição de finalidade geral espaçamento chamado `Space` agora está disponível. Para usá-lo, basta adicioná-lo como um modo de exibição do filho.
Por exemplo, o XML a seguir adiciona uma linha adicional para o `GridLayout` definindo seu `rowcount` para 3 e adiciona um `Space` exibição que fornece o espaçamento entre o `TextViews`.

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="3"
        android:columnCount="2"
        android:orientation="vertical">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"        
            android:layout_column="1" />
     <Space
            android:layout_row="1"
            android:layout_column="0"
            android:layout_width="50dp"         
            android:layout_height="50dp" />    
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="1" />
</GridLayout>
```

Esse XML cria um espaçamento no `GridLayout` conforme mostrado abaixo:

 [![Captura de tela de GridLayoutDemo ilustrando células maiores com espaçamento](grid-layout-images/03-gridlayout.png)](grid-layout-images/03-gridlayout.png#lightbox)

A vantagem de usar o novo `Space` exibição é que ele permite espaçamento e não requer definir atributos em cada exibição filho.



### <a name="spanning-columns-and-rows"></a>A abrangência de colunas e linhas

O `GridLayout` também oferece suporte a células que abrangem várias colunas e linhas. Por exemplo, digamos que podemos adicionar outra linha que contém um botão para o `GridLayout` conforme mostrado abaixo:

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="4"
        android:columnCount="2"
        android:orientation="vertical">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"        
            android:layout_column="1" />
     <Space
            android:layout_row="1"
            android:layout_column="0"
            android:layout_width="50dp"        
            android:layout_height="50dp" />   
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"        
            android:layout_row="2"        
            android:layout_column="1" />
     <Button
            android:id="@+id/myButton"
            android:text="@string/hello"        
            android:layout_row="3"
            android:layout_column="0" />
</GridLayout>
```

Isso resultará na primeira coluna do `GridLayout` sendo ampliada para acomodar o tamanho do botão, como vemos aqui:

[![Captura de tela de GridLayoutDemo com o botão abrangência apenas a primeira coluna](grid-layout-images/04-gridlayout.png)](grid-layout-images/04-gridlayout.png#lightbox)

Para impedir que a primeira coluna alongando, podemos definir o botão tenha duas colunas definindo seu columnspan assim:

```xml
<Button
    android:id="@+id/myButton"
    android:text="@string/hello"       
    android:layout_row="3"
    android:layout_column="0"
    android:layout_columnSpan="2" />
```

Isso resulta em um layout para o `TextViews` que é semelhante ao layout tivemos anteriormente, com o botão adicionado à parte inferior da `GridLayout` conforme mostrado abaixo:

 [![Captura de tela de GridLayoutDemo com ambas as colunas a abrangência de botão](grid-layout-images/05-gridlayout.png)](grid-layout-images/05-gridlayout.png#lightbox)


## <a name="related-links"></a>Links relacionados

- [GridLayoutDemo (exemplo)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/GridLayoutDemo/)
- [Introdução ao sabor de sorvete Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
