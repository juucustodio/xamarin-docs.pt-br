---
title: GridLayout
ms.prod: xamarin
ms.assetid: B69A4BF5-9CFB-443A-9F7B-062D1E498F61
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: bbc764adc204a1f5b9ef4674a183473995be55c1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115153"
---
# <a name="gridlayout"></a>GridLayout

O `GridLayout` é um novo `ViewGroup` subclasse que dá suporte a layout de modos de exibição em uma grade 2D, semelhante a uma tabela HTML, conforme mostrado abaixo:

 [![Cortada GridLayout exibindo quatro células](grid-layout-images/21-gridlayoutcropped.png)](grid-layout-images/21-gridlayoutcropped.png#lightbox)

 `GridLayout` funciona com uma hierarquia de exibição simples, onde exibições filho defina suas localizações na grade especificando as linhas e colunas devem ser. Dessa forma, o *GridLayout* é capaz de posicionar os modos de exibição na grade sem a necessidade de que todas as exibições de intermediárias fornecem uma estrutura de tabela, como visto nas linhas de tabela usadas no TableLayout. Mantendo uma hierarquia plana, *GridLayout* é capaz de layout com mais rapidez suas exibições filha. Vamos dar uma olhada em um exemplo para ilustrar esse conceito, na verdade, significado no código.


## <a name="creating-a-grid-layout"></a>Criando um Layout de grade

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

O layout ajustará os tamanhos de linha e coluna para que as células podem ajustar seu conteúdo, conforme ilustrado pelo diagrama a seguir:

 [![Diagrama de layout mostrando duas células à esquerda inferior à direita](grid-layout-images/gridlayout-cells.png)](grid-layout-images/gridlayout-cells.png#lightbox)

Isso resulta na interface do usuário seguir quando executado em um aplicativo:

 [![Aplicativo de captura de tela de GridLayoutDemo exibindo quatro células](grid-layout-images/01-gridlayout.png)](grid-layout-images/01-gridlayout.png#lightbox)



## <a name="specifying-orientation"></a>Especificar a orientação

Observe no XML acima, cada `TextView` não especifica uma linha ou coluna. Quando eles não forem especificados, o `GridLayout` atribui cada exibição filho em ordem, com base na orientação. Por exemplo, vamos alterar orientação da GridLayout do padrão, que é horizontal, vertical para como este:

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



### <a name="specifying-explicit-position"></a>Especificando posição explícitos

Se você deseja controlar explicitamente as posições dos modos de exibição filho na `GridLayout`, podemos definir seus `layout_row` e `layout_column` atributos. Por exemplo, o XML a seguir resultará no layout mostrado na primeira captura de tela (mostrada acima), independentemente da orientação.

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



### <a name="specifying-spacing"></a>Especificação de espaçamento

Temos algumas opções que fornecerá o espaçamento entre o filho modos de exibição da `GridLayout`. Podemos usar o `layout_margin` atributo para configurar a margem de cada exibição filho diretamente, conforme mostrado abaixo

```xml
<TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0"
            android:layout_margin="10dp" />
```

Além disso, no Android 4, uma nova exibição de espaçamento para fins gerais chamado `Space` agora está disponível. Para usá-lo, basta adicioná-lo como um modo de exibição filho.
Por exemplo, o XML a seguir adiciona uma linha adicional para o `GridLayout` definindo seu `rowcount` 3 e adiciona uma `Space` modo de exibição que fornece o espaçamento entre o `TextViews`.

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

Este XML cria um espaçamento no `GridLayout` conforme mostrado abaixo:

 [![Captura de tela de GridLayoutDemo ilustrando células maiores com espaçamento](grid-layout-images/03-gridlayout.png)](grid-layout-images/03-gridlayout.png#lightbox)

A vantagem de usar o novo `Space` exibição é que ele permite que o espaçamento e não requer que definir atributos em cada exibição filho.



### <a name="spanning-columns-and-rows"></a>Abrangendo linhas e colunas

O `GridLayout` também dá suporte a células que abrangem várias colunas e linhas. Por exemplo, digamos que podemos adicionar outra linha que contém um botão para o `GridLayout` conforme mostrado abaixo:

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

[![Captura de tela de GridLayoutDemo com o botão que abrange apenas a primeira coluna](grid-layout-images/04-gridlayout.png)](grid-layout-images/04-gridlayout.png#lightbox)

Para impedir que a primeira coluna alongamento, podemos definir o botão tenha duas colunas, definindo seu columnspan como este:

```xml
<Button
    android:id="@+id/myButton"
    android:text="@string/hello"       
    android:layout_row="3"
    android:layout_column="0"
    android:layout_columnSpan="2" />
```

Isso resulta em um layout para o `TextViews` que é semelhante ao layout, tínhamos anteriormente, com o botão adicionado na parte inferior do `GridLayout` conforme mostrado abaixo:

 [![Captura de tela de GridLayoutDemo com abrangência de ambas as colunas de botão](grid-layout-images/05-gridlayout.png)](grid-layout-images/05-gridlayout.png#lightbox)


## <a name="related-links"></a>Links relacionados

- [GridLayoutDemo (amostra)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/GridLayoutDemo/)
- [Apresentando o Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
