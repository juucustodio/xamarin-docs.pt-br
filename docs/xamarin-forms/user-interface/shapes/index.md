---
title: Xamarin.Forms Existentes
description: Xamarin.Forms As formas são tipos de modos de exibição que permitem desenhar formas na tela.
ms.prod: xamarin
ms.assetid: 4E749FE8-852C-46DA-BB1E-652936106357
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/30/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 07765fdb9f67f21415db495da1b635b0507b7ed1
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559201"
---
# <a name="no-locxamarinforms-shapes"></a>Xamarin.Forms Existentes

![API de pré-lançamento](~/media/shared/preview.png)

Um `Shape` é um tipo de [`View`](xref:Xamarin.Forms.View) que permite desenhar uma forma na tela. `Shape` os objetos podem ser usados nas classes de layout e na maioria dos controles, porque a `Shape` classe deriva da `View` classe.

Xamarin.Forms As formas estão disponíveis no `Xamarin.Forms.Shapes` namespace no Ios, no Android, no MacOS, no plataforma universal do Windows (UWP) e no Windows Presentation Foundation (WPF).

> [!IMPORTANT]
> Xamarin.Forms As formas são experimentais no momento e só podem ser usadas pela definição do `Shapes_Experimental` sinalizador. Para obter mais informações, consulte [sinalizadores experimentais](~/xamarin-forms/internals/experimental-flags.md).

`Shape` define as propriedades a seguir:

- `Aspect`, do tipo `Stretch` , descreve como a forma preenche seu espaço alocado. O valor padrão dessa propriedade é `Stretch.None`.
- `Fill`, do tipo `Brush` , indica o pincel usado para pintar o interior da forma.
- `Stroke`, do tipo `Brush` , indica o pincel usado para pintar o contorno da forma.
- `StrokeDashArray`, do tipo `DoubleCollection` , que representa uma coleção de `double` valores que indicam o padrão de traços e lacunas que são usados para estruturar uma forma.
- `StrokeDashOffset`, do tipo `double` , especifica a distância dentro do padrão de tracejado onde um traço começa. O valor padrão dessa propriedade é 0,0.
- `StrokeLineCap`, do tipo `PenLineCap` , descreve a forma no início e no fim de uma linha ou segmento. O valor padrão dessa propriedade é `PenLineCap.Flat`.
- `StrokeLineJoin`, do tipo `PenLineJoin` , especifica o tipo de junção que é usado nos vértices de uma forma. O valor padrão dessa propriedade é `PenLineJoin.Miter`.
- `StrokeMiterLimit`, do tipo `double` , especifica o limite da proporção do comprimento de Mitre para metade do `StrokeThickness` de uma forma. O valor padrão dessa propriedade é 10,0.
- `StrokeThickness`, do tipo `double` , indica a largura do contorno da forma. O valor padrão dessa propriedade é 0,0.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

Xamarin.Forms define um número de objetos que derivam da `Shape` classe. São eles `Ellipse` , `Line` ,,, `Path` `Polygon` `Polyline` e `Rectangle` .

## <a name="paint-shapes"></a>Formas de pintura

`Brush` os objetos são usados para pintar as formas `Stroke` e `Fill` :

```xaml
<Ellipse Fill="DarkBlue"
         Stroke="Red"
         StrokeThickness="4"
         WidthRequest="150"
         HeightRequest="50"
         HorizontalOptions="Start" />
```

Neste exemplo, o traço e o preenchimento de um `Ellipse` são especificados:

![Formas de pintura](images/ellipse.png "Formas de pintura")

> [!IMPORTANT]
> `Brush` os objetos usam um conversor de tipo que permite que [`Color`](xref:Xamarin.Forms.Color) valores sejam especificados para a `Stroke` propriedade.

Se você não especificar um `Brush` objeto para `Stroke` , ou se você definir `StrokeThickness` como 0, a borda ao redor da forma não será desenhada.

Para obter mais informações sobre `Brush` objetos, consulte [ Xamarin.Forms pincéis](~/xamarin-forms/user-interface/brushes/index.md). Para obter mais informações sobre [`Color`](xref:Xamarin.Forms.Color) valores válidos, consulte [cores Xamarin.Forms em ](~/xamarin-forms/user-interface/colors.md).

## <a name="stretch-shapes"></a>Alongar formas

`Shape` os objetos têm uma `Aspect` propriedade, do tipo `Stretch` . Essa propriedade determina como o `Shape` conteúdo de um objeto é ampliado para preencher o `Shape` espaço de layout do objeto. O `Shape` espaço de layout de um objeto é a quantidade de espaço `Shape` alocada pelo Xamarin.Forms sistema de layout, devido a uma `WidthRequest` configuração explícita `HeightRequest` ou por causa de suas `HorizontalOptions` `VerticalOptions` configurações e.

A enumeração `Stretch` define os seguintes membros:

- `None`, que indica que o conteúdo preserva seu tamanho original. Este é o valor padrão da propriedade `Shape.Aspect`.
- `Fill`, que indica que o conteúdo é redimensionado para preencher as dimensões de destino. A taxa de proporção não é preservada.
- `Uniform`, que indica que o conteúdo é redimensionado para se ajustar às dimensões de destino, preservando a taxa de proporção.
- `UniformToFill`, indica que o conteúdo é redimensionado para preencher as dimensões de destino, preservando a taxa de proporção. Se a taxa de proporção do retângulo de destino for diferente da origem, o conteúdo de origem será cortado para se ajustar às dimensões de destino.

O XAML a seguir mostra como definir a `Aspect` Propriedade:

```xaml
<Path Aspect="Uniform"
      Stroke="Yellow"
      StrokeThickness="1"
      Fill="Red"
      BackgroundColor="LightGray"
      HorizontalOptions="Start"
      HeightRequest="100"
      WidthRequest="100">
    <Path.Data>
        <!-- Path data goes here -->
    </Path.Data>  
</Path>      
```

Neste exemplo, um `Path` objeto desenha um coração. As `Path` `WidthRequest` Propriedades e do objeto `HeightRequest` são definidas como 100 unidades independentes do dispositivo e sua `Aspect` propriedade é definida como `Uniform` . Como resultado, o conteúdo do objeto é redimensionado para se ajustar às dimensões de destino, preservando a taxa de proporção:

![Alongar formas](images/aspect.png "Alongar formas")

## <a name="draw-dashed-shapes"></a>Desenhar formas tracejadas

`Shape` os objetos têm uma `StrokeDashArray` propriedade, do tipo `DoubleCollection` . Essa propriedade representa uma coleção de `double` valores que indicam o padrão de traços e lacunas que são usados para estruturar uma forma. Um `DoubleCollection` é um `ObservableCollection` dos `double` valores. Cada `double` uma na coleção especifica o comprimento de um traço ou intervalo. O primeiro item na coleção, localizado no índice 0, especifica o comprimento de um traço. O segundo item na coleção, localizado no índice 1, especifica o comprimento de uma lacuna. Portanto, os objetos com um valor de índice par especificam traços, enquanto os objetos com um valor de índice ímpar especificam lacunas.

`Shape` os objetos também têm uma `StrokeDashOffset` propriedade, do tipo `double` , que especifica a distância dentro do padrão de tracejado onde um traço começa. A falha ao definir essa propriedade resultará na `Shape` existência de uma estrutura de tópicos sólida.

Formas tracejadas podem ser desenhadas definindo as `StrokeDashArray` `StrokeDashOffset` Propriedades e. A `StrokeDashArray` propriedade deve ser definida como um ou mais `double` valores, com cada par delimitado por uma única vírgula e/ou um ou mais espaços. Por exemplo, "0,5 1,0" e "0.5, 1,0" são válidos.

O exemplo de XAML a seguir mostra como desenhar um retângulo tracejado:

```xaml
<Rectangle Fill="DarkBlue"
           Stroke="Red"
           StrokeThickness="4"
           StrokeDashArray="1,1"
           StrokeDashOffset="6"
           WidthRequest="150"
           HeightRequest="50"
           HorizontalOptions="Start" />
```

Neste exemplo, um retângulo preenchido com um traço tracejado é desenhado:

![Retângulo tracejado](images/dashed-rectangle.png "Linha tracejada")

## <a name="control-line-ends"></a>Extremidades de linha de controle

Uma linha tem três partes: início, corpo da linha e ponta da extremidade. As arremates inicial e final descrevem a forma no início e no fim de uma linha ou segmento.

`Shape` os objetos têm uma `StrokeLineCap` propriedade, do tipo `PenLineCap` , que descreve a forma no início e no fim de uma linha, ou segmento. A enumeração `PenLineCap` define os seguintes membros:

- `Flat`, que representa um limite que não ultrapassa o último ponto da linha. Isso é comparável a sem limite de linha e é o valor padrão da `StrokeLineCap` propriedade.
- `Square`, que representa um retângulo com uma altura igual à espessura da linha e um comprimento igual à metade da espessura da linha.
- `Round`, que representa um semicírculo que tem um diâmetro igual à espessura da linha.

> [!IMPORTANT]
> A `StrokeLineCap` propriedade não terá efeito se você defini-la em uma forma que não tem nenhum ponto inicial ou final. Por exemplo, essa propriedade não terá efeito se você defini-la em um `Ellipse` , ou `Rectangle` .

O XAML a seguir mostra como definir a `StrokeLineCap` Propriedade:

```xaml
<Line X1="0"
      Y1="20"
      X2="300"
      Y2="20"
      StrokeLineCap="Round"
      Stroke="Red"
      StrokeThickness="12" />
```

Neste exemplo, a linha vermelha é arredondada no início e no final da linha:

![Limites de linha](images/linecap.png "Limites de linha")

## <a name="control-line-joins"></a>Junções de linha de controle

`Shape` os objetos têm uma `StrokeLineJoin` propriedade, do tipo `PenLineJoin` , que especifica o tipo de junção que é usado nos vértices da forma. A enumeração `PenLineJoin` define os seguintes membros:

- `Miter`, que representa vértices angulares regulares. Este é o valor padrão da propriedade `StrokeLineJoin`.
- `Bevel`, que representa vértices chanfrados.
- `Round`, que representa vértices arredondados.

> [!NOTE]
> Quando a `StrokeLineJoin` propriedade é definida como `Miter` , a `StrokeMiterLimit` propriedade pode ser definida como um `double` para limitar o comprimento de Mitre das junções de linha na forma.

O XAML a seguir mostra como definir a `StrokeLineJoin` Propriedade:

```xaml
<Polyline Points="20 20,250 50,20 120"
          Stroke="DarkBlue"
          StrokeThickness="20"
          StrokeLineJoin="Round" />
```

Neste exemplo, a polilinha azul escura tem junções arredondadas em seus vértices:

![Junções de linha](images/linejoin.png "Junções de linha")

## <a name="related-links"></a>Links relacionados

- [ShapeDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Pincéis](~/xamarin-forms/user-interface/brushes/index.md)
- [Cores em Xamarin.Forms](~/xamarin-forms/user-interface/colors.md)