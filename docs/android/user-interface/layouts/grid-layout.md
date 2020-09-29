---
title: GridLayout
ms.prod: xamarin
ms.assetid: B69A4BF5-9CFB-443A-9F7B-062D1E498F61
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 3b2b6a3f1aa575553964e71be3a64cec16bc616f
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457322"
---
# <a name="xamarinandroid-gridlayout"></a>GridLayout do Xamarin. Android

O `GridLayout` é uma nova `ViewGroup` subclasse que dá suporte ao layout de exibições em uma grade 2D, semelhante a uma tabela HTML, como mostrado abaixo:

 [![GridLayout cortada exibindo quatro células](grid-layout-images/21-gridlayoutcropped.png)](grid-layout-images/21-gridlayoutcropped.png#lightbox)

 `GridLayout` funciona com uma hierarquia de exibição plana, em que as exibições filhas definem seus locais na grade especificando as linhas e colunas nas quais elas devem estar. Dessa forma, a *GridLayout* é capaz de posicionar modos de exibição na grade sem exigir que as exibições intermediárias forneçam uma estrutura de tabela, como visto nas linhas da tabela usadas no TableLayout. Ao manter uma hierarquia plana, a *GridLayout* é capaz de fazer o layout mais rápido de suas exibições filhas. Vamos dar uma olhada em um exemplo para ilustrar o que esse conceito realmente significa no código.

## <a name="creating-a-grid-layout"></a>Criando um layout de grade

O XML a seguir adiciona vários `TextView` controles a uma *GridLayout*.

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

O layout ajustará os tamanhos de linha e coluna para que as células possam ajustar seu conteúdo, conforme ilustrado pelo diagrama a seguir:

 [![Diagrama de layout mostrando duas células à esquerda menores do que à direita](grid-layout-images/gridlayout-cells.png)](grid-layout-images/gridlayout-cells.png#lightbox)

Isso resulta na seguinte interface do usuário quando executado em um aplicativo:

 [![Captura de tela do aplicativo GridLayoutDemo exibindo quatro células](grid-layout-images/01-gridlayout.png)](grid-layout-images/01-gridlayout.png#lightbox)

## <a name="specifying-orientation"></a>Especificando a orientação

Observe no XML acima, cada `TextView` um não especifica uma linha ou coluna. Quando não forem especificados, o `GridLayout` atribuirá cada exibição filho em ordem, com base na orientação. Por exemplo, vamos alterar a orientação da GridLayout do padrão, que é horizontal, para vertical assim:

```xml
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2"
        android:orientation="vertical">
</GridLayout>
```

Agora, o `GridLayout` irá posicionar as células de cima para baixo em cada coluna, em vez da esquerda para a direita, conforme mostrado abaixo:

 [![Diagrama ilustrando como as células são posicionadas na orientação vertical](grid-layout-images/gridlayoutorientation.png)](grid-layout-images/gridlayoutorientation.png#lightbox)

Isso resulta na seguinte interface do usuário em tempo de execução:

 [![Captura de tela de GridLayoutDemo com células posicionadas na orientação vertical](grid-layout-images/02-gridlayout.png)](grid-layout-images/02-gridlayout.png#lightbox)

### <a name="specifying-explicit-position"></a>Especificando a posição explícita

Se quisermos controlar explicitamente as posições das exibições filho no `GridLayout` , podemos definir seus `layout_row` `layout_column` atributos e. Por exemplo, o XML a seguir resultará no layout mostrado na primeira captura de tela (mostrada acima), independentemente da orientação.

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

### <a name="specifying-spacing"></a>Especificando o espaçamento

Temos algumas opções que fornecerão o espaçamento entre as exibições filhas do `GridLayout` . Podemos usar o `layout_margin` atributo para definir a margem em cada exibição filho diretamente, conforme mostrado abaixo

```xml
<TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0"
            android:layout_margin="10dp" />
```

Além disso, no Android 4, uma nova exibição de espaçamento de uso geral chamada `Space` agora está disponível. Para usá-lo, basta adicioná-lo como uma exibição filho.
Por exemplo, o XML abaixo adiciona uma linha adicional ao `GridLayout` definindo seu `rowcount` como 3 e adiciona uma `Space` exibição que fornece o espaçamento entre o `TextViews` .

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

Esse XML cria espaçamento no `GridLayout` conforme mostrado abaixo:

 [![Captura de tela de GridLayoutDemo ilustrando células maiores com espaçamento](grid-layout-images/03-gridlayout.png)](grid-layout-images/03-gridlayout.png#lightbox)

O benefício de usar o novo `Space` modo de exibição é que ele permite o espaçamento e não exige a definição de atributos em cada exibição filho.

### <a name="spanning-columns-and-rows"></a>Abrangendo colunas e linhas

O `GridLayout` também dá suporte a células que abrangem várias colunas e linhas. Por exemplo, digamos que adicionamos outra linha contendo um botão ao `GridLayout` conforme mostrado abaixo:

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

Isso fará com que a primeira coluna do `GridLayout` seja ampliada para acomodar o tamanho do botão, como vemos aqui:

[![Captura de tela de GridLayoutDemo com botão que abrange apenas a primeira coluna](grid-layout-images/04-gridlayout.png)](grid-layout-images/04-gridlayout.png#lightbox)

Para manter a primeira coluna de alongamento, podemos definir o botão para abranger duas colunas definindo seu ColumnSpan como este:

```xml
<Button
    android:id="@+id/myButton"
    android:text="@string/hello"       
    android:layout_row="3"
    android:layout_column="0"
    android:layout_columnSpan="2" />
```

Fazer isso resulta em um layout para o `TextViews` que é semelhante ao layout que tínhamos anteriormente, com o botão adicionado à parte inferior do, `GridLayout` conforme mostrado abaixo:

 [![Captura de tela de GridLayoutDemo com botão abrangendo ambas as colunas](grid-layout-images/05-gridlayout.png)](grid-layout-images/05-gridlayout.png#lightbox)

## <a name="related-links"></a>Links Relacionados

- [GridLayoutDemo (exemplo)](/samples/xamarin/monodroid-samples/gridlayoutdemo)