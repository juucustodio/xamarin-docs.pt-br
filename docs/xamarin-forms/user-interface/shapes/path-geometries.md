---
title: 'Xamarin.FormsFormas: geometrias de caminho'
description: Xamarin.FormsClasses de geometria de caminho permitem descrever a geometria de uma forma 2D.
ms.prod: xamarin
ms.assetid: 07DE3D66-1820-4642-BDDF-84146D40C99D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5718b0594581928e6f00e11a15163d176615378f
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101846"
---
# <a name="xamarinforms-shapes-path-geometries"></a>Xamarin.FormsFormas: geometrias de caminho

![](~/media/shared/preview.png "This API is currently pre-release")

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

A `Geometry` classe e as classes que derivam dela permitem que você descreva a geometria de uma forma 2D. `Geometry`os objetos podem ser simples, como retângulos e círculos, ou compostos, criados a partir de dois ou mais objetos Geometry. Geometrias mais complexas podem ser criadas usando a `PathGeometry` classe, que permite que você descreva arcos e curvas.

As `Geometry` `Shape` classes e parecem semelhantes, pois ambas descrevem formas 2D, mas têm uma diferença importante. A `Geometry` classe deriva da `BindableObject` classe, enquanto a `Shape` classe deriva da `View` classe. Portanto, os `Shape` objetos podem ser renderizados e participar do sistema de layout, enquanto os `Geometry` objetos não podem. Embora os objetos `Shape` sejam mais prontamente utilizáveis do que `Geometry` objetos, os `Geometry` objetos são mais versáteis. Embora um `Shape` objeto seja usado para renderizar gráficos 2D, um `Geometry` objeto pode ser usado para definir a região geométrica para gráficos 2D e definir uma região para recorte.

A classe base para todas as geometrias é a classe abstrata `Geometry` . As classes que derivam dessa classe podem ser agrupadas em três categorias: geometrias simples, geometrias de caminho e geometrias compostas.

## <a name="simple-geometries"></a>Geometrias simples

As classes de geometria simples são `EllipseGeometry` , `LineGeometry` e `RectangleGeometry` . Eles são usados para criar formas geométricas básicas, como círculos, linhas e retângulos. Essas mesmas formas, bem como formas mais complexas, podem ser criadas usando um `PathGeometry` ou combinando objetos Geometry juntos, mas essas classes fornecem um meio mais simples de produzir essas formas geométricas básicas.

### <a name="ellipsegeometry"></a>EllipseGeometry

Uma geometria de elipse representa a geometria ou uma elipse ou um círculo e é definida por um ponto central, um raio x e um y-raio.

A `EllipseGeometry` classe define as seguintes propriedades:

- `Center`, do tipo `Point` , que representa o ponto central da geometria.
- `RadiusX`, do tipo `double` , que representa o valor de raio x da geometria. O valor padrão dessa propriedade é 0,0.
- `RadiusY`, do tipo `double` , que representa o valor de raio y da geometria. O valor padrão dessa propriedade é 0,0.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

O exemplo a seguir mostra como criar e renderizar um `EllipseGeometry` :

```xaml
<Path Fill="Blue"
      Stroke="Red"
      StrokeThickness="1">
  <Path.Data>
    <EllipseGeometry Center="50,50"
                     RadiusX="50"
                     RadiusY="50" />
  </Path.Data>
</Path>
```

Neste exemplo, o centro do `EllipseGeometry` é definido como (50, 50) e o raio x e y são definidos como 50. Isso cria um círculo com um diâmetro de 100, cujo interior é pintado azul.

### <a name="linegeometry"></a>LineGeometry

Uma geometria de linha representa a geometria de uma linha e é definida especificando o ponto inicial da linha e o ponto de extremidade.

A `LimeGeometry` classe define as seguintes propriedades:

- `StartPoint`, do tipo `Point` , que representa o ponto inicial da linha.
- `EndPoint`, do tipo `Point` , que representa o ponto final da linha.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

O exemplo a seguir mostra como criar e renderizar um `LineGeometry` :

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
  <Path.Data>
    <LineGeometry StartPoint="10,20"
                  EndPoint="100,130" />
  </Path.Data>
</Path>
```

Neste exemplo, um `LineGeometry` é desenhado de (10, 20) a (100.130).

### <a name="rectanglegeometry"></a>RectangleGeometry

Uma geometria de retângulo representa um retângulo e é definida com uma `Rect` estrutura que especifica sua posição relativa e sua altura e largura.

A `RectangleGeometry` classe define as seguintes propriedades:

- `Rect`, do tipo `FormsRect` , que representa as dimensões do retângulo.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

O exemplo a seguir mostra como criar e renderizar um `RectangleGeometry` :

```xaml
<Path Fill="Blue"
      Stroke="Black"
      StrokeThickness="1">
  <Path.Data>
    <RectangleGeometry Rect="50,50,25,25" />
  </Path.Data>
</Path>
```

A posição e as dimensões do retângulo são definidas por uma `Rect` estrutura. Neste exemplo, a posição é (50, 50) e a altura e a largura são 25, o que cria um quadrado.

## <a name="path-geometries"></a>Geometrias de caminho

Uma geometria de caminho descreve uma forma complexa que pode ser composta de arcos, curvas, elipses, linhas e retângulos.

A `PathGeometry` classe define as seguintes propriedades:

- `Figures`, do tipo `PathFigureCollection` , que representa a coleção de `PathFigure` objetos que descrevem o conteúdo do caminho.
- `FillRule`, do tipo `FillRule` , que determina como as áreas de interseção contidas na geometria são combinadas. O valor padrão dessa propriedade é `FillRule.EvenOdd`.

> [!NOTE]
> A `Figures` propriedade é a `ContentProperty` da `PathGeometry` classe e, portanto, não precisa ser definida explicitamente a partir de XAML.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

Um `PathGeometry` é composto de uma coleção de `PathFigure` objetos, com cada `PathFigure` um deles descrevendo uma forma na geometria. Cada `PathFigure` um deles é composto de um ou mais `PathSegment` objetos, cada um deles descreve um segmento da forma. Há muitos tipos de segmentos:

- `ArcSegment`, que cria um arco elíptico entre dois pontos.
- `BezierSegment`, que cria uma curva de Bézier cúbica entre dois pontos.
- `LineSegment`, que cria uma linha entre dois pontos.
- `PolyBezierSegment`, que cria uma série de curvas Bézier cúbicas.
- `PolyLineSegment`, que cria uma série de linhas.
- `PolyQuadraticBezierSegment`, que cria uma série de curvas Bézier quadráticas.
- `QuadraticBezierSegment`, que cria uma curva de Bézier quadrática.

Os segmentos em um `PathFigure` são combinados em uma única forma geométrica com o ponto de extremidade de cada segmento sendo o ponto inicial do próximo segmento. A `StartPoint` propriedade de um `PathFigure` especifica o ponto do qual o primeiro segmento é desenhado. Cada segmento subsequente começa no ponto de extremidade do segmento anterior. Por exemplo, uma linha vertical de `10,50` para `10,150` pode ser definida definindo a `StartPoint` propriedade como `10,50` e criando uma `LineSegment` com uma `Point` configuração de propriedade de `10,150` :

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,50">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <LineSegment Point="10,150" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

Geometrias mais complexas podem ser criadas usando uma combinação de `PathSegment` objetos e usando vários `PathFigure` objetos dentro de um `PathGeometry` .

## <a name="composite-geometries"></a>Geometrias compostas

Objetos de geometria composta podem ser criados usando um `GeometryGroup` . A `GeometryGroup` classe cria uma geometria composta de um ou mais `Geometry` objetos. Qualquer número de `Geometry` objetos pode ser adicionado a um `GeometryGroup` .

O exemplo a seguir mostra como combinar geometrias em um `GeometryGroup` :

```xaml
<Path Stroke="Green"
      StrokeThickness="2"
      Fill="Orange">
    <Path.Data>
        <GeometryGroup>
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="150,150" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="250,150" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="150,250" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="250,250" />
        </GeometryGroup>
    </Path.Data>
</Path>
```

Neste exemplo, quatro `EllipseGeometry` objetos com coordenadas x-RADIUS e y-RADIUS idênticas, mas com diferentes coordenadas do centro, são combinados. Isso cria quatro círculos sobrepostos, cujos interiores são preenchidos em laranja com a `EvenOdd` regra de preenchimento.

## <a name="other-features"></a>Outros recursos

A `GeometryHelper` classe fornece os seguintes métodos auxiliares:

- `FlattenGeometry`
- `FlattenCubicBezier`
- `FlattenQuadraticBezier`
- `FlattenArc`

## <a name="related-links"></a>Links relacionados

- [ShapeDemos (exemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.FormsExistentes](index.md)
