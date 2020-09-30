---
title: 'Xamarin.Forms Formas: geometrias'
description: Xamarin.Forms as classes Geometry permitem descrever a geometria de uma forma 2D.
ms.prod: xamarin
ms.assetid: 07DE3D66-1820-4642-BDDF-84146D40C99D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c6c6fb0ea9d3e2b728072fa36c3cdb2a86ade131
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559318"
---
# <a name="no-locxamarinforms-shapes-geometries"></a>Xamarin.Forms Formas: geometrias

![API de pré-lançamento](~/media/shared/preview.png)

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

A `Geometry` classe e as classes que derivam dela permitem que você descreva a geometria de uma forma 2D. `Geometry` os objetos podem ser simples, como retângulos e círculos, ou compostos, criados a partir de dois ou mais objetos Geometry. Além disso, geometrias mais complexas podem ser criadas, incluindo arcos e curvas.

A `Geometry` classe é a classe pai para várias classes que definem diferentes categorias de geometrias:

- `EllipseGeometry`, que representa a geometria de uma elipse ou de um círculo.
- `GeometryGroup`, que representa um contêiner que pode combinar vários objetos Geometry em um único objeto.
- `LineGeometry`, que representa a geometria de uma linha.
- `PathGeometry`, que representa a geometria de uma forma complexa que pode ser composta de arcos, curvas, elipses, linhas e retângulos.
- `RectangleGeometry`, que representa a geometria de um retângulo ou quadrado.

As `Geometry` `Shape` classes e parecem semelhantes, pois ambas descrevem formas 2D, mas têm uma diferença importante. A `Geometry` classe deriva da [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe, enquanto a `Shape` classe deriva da [`View`](xref:Xamarin.Forms.View) classe. Portanto, os `Shape` objetos podem ser renderizados e participar do sistema de layout, enquanto os `Geometry` objetos não podem. Embora os objetos `Shape` sejam mais prontamente utilizáveis do que `Geometry` objetos, os `Geometry` objetos são mais versáteis. Embora um `Shape` objeto seja usado para renderizar gráficos 2D, um `Geometry` objeto pode ser usado para definir a região geométrica para gráficos 2D e definir uma região para recorte.

As classes a seguir têm propriedades que podem ser definidas como `Geometry` objetos:

- A `Path` classe usa um `Geometry` para descrever seu conteúdo. Você pode renderizar um `Geometry` definindo a `Path.Data` propriedade para um `Geometry` objeto e definindo as `Path` `Fill` Propriedades e do objeto `Stroke` .
- A [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe tem uma `Clip` propriedade, do tipo `Geometry` , que define o contorno do conteúdo de um elemento. Quando a `Clip` propriedade é definida como um `Geometry` objeto, somente a área que está dentro da região do `Geometry` será visível. Para obter mais informações, consulte [cortar com uma geometria](#clip-with-a-geometry).

As classes que derivam da `Geometry` classe podem ser agrupadas em três categorias: geometrias simples, geometrias de caminho e geometrias compostas.

## <a name="simple-geometries"></a>Geometrias simples

As classes de geometria simples são `EllipseGeometry` , `LineGeometry` e `RectangleGeometry` . Eles são usados para criar formas geométricas básicas, como círculos, linhas e retângulos. Essas mesmas formas, bem como formas mais complexas, podem ser criadas usando um `PathGeometry` ou combinando objetos Geometry juntos, mas essas classes fornecem uma abordagem mais simples para produzir essas formas geométricas básicas.

### <a name="ellipsegeometry"></a>EllipseGeometry

Uma geometria de elipse representa a geometria ou uma elipse ou um círculo e é definida por um ponto central, um raio x e um y-raio.

A `EllipseGeometry` classe define as seguintes propriedades:

- `Center`, do tipo [`Point`](xref:Xamarin.Forms.Point) , que representa o ponto central da geometria.
- `RadiusX`, do tipo `double` , que representa o valor de raio x da geometria. O valor padrão dessa propriedade é 0,0.
- `RadiusY`, do tipo `double` , que representa o valor de raio y da geometria. O valor padrão dessa propriedade é 0,0.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

O exemplo a seguir mostra como criar e renderizar um `EllipseGeometry` em um `Path` objeto:

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

Neste exemplo, o centro do `EllipseGeometry` é definido como (50, 50) e o raio x e y são definidos como 50. Isso cria um círculo vermelho com um diâmetro de 100 unidades independentes de dispositivo, cujo interior é pintado azul:

![EllipseGeometry](geometry-images/ellipse.png "EllipseGeometry")

### <a name="linegeometry"></a>LineGeometry

Uma geometria de linha representa a geometria de uma linha e é definida especificando o ponto inicial da linha e o ponto de extremidade.

A `LineGeometry` classe define as seguintes propriedades:

- `StartPoint`, do tipo [`Point`](xref:Xamarin.Forms.Point) , que representa o ponto inicial da linha.
- `EndPoint`, do tipo [`Point`](xref:Xamarin.Forms.Point) , que representa o ponto final da linha.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

O exemplo a seguir mostra como criar e renderizar um `LineGeometry` em um `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
  <Path.Data>
    <LineGeometry StartPoint="10,20"
                  EndPoint="100,130" />
  </Path.Data>
</Path>
```

Neste exemplo, um `LineGeometry` é desenhado de (10, 20) a (100.130):

![LineGeometry](geometry-images/line.png "LineGeometry")

> [!NOTE]
> Definir a `Fill` propriedade de a `Path` que renderiza um `LineGeometry` não terá nenhum efeito, porque uma linha não tem interior.

### <a name="rectanglegeometry"></a>RectangleGeometry

Uma geometria de retângulo representa a geometria de um retângulo ou quadrado e é definida com uma `Rect` estrutura que especifica sua posição relativa e sua altura e largura.

A `RectangleGeometry` classe define a `Rect` propriedade, do tipo `Rect` , que representa as dimensões do retângulo. Essa propriedade é apoiada por um [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, o que significa que ele pode ser o destino de associações de dados e com estilo.

O exemplo a seguir mostra como criar e renderizar um `RectangleGeometry` em um `Path` objeto:

```xaml
<Path Fill="Blue"
      Stroke="Red"
      StrokeThickness="1">
  <Path.Data>
    <RectangleGeometry Rect="10,10,150,100" />
  </Path.Data>
</Path>
```

A posição e as dimensões do retângulo são definidas por uma `Rect` estrutura. Neste exemplo, a posição é (10, 10), a largura é 150 e a altura é de 100 unidades independentes de dispositivo:

![RectangleGeometry](geometry-images/rectangle.png "RectangleGeometry")

## <a name="path-geometries"></a>Geometrias de caminho

Uma geometria de caminho descreve uma forma complexa que pode ser composta de arcos, curvas, elipses, linhas e retângulos.

A `PathGeometry` classe define as seguintes propriedades:

- `Figures`, do tipo `PathFigureCollection` , que representa a coleção de `PathFigure` objetos que descrevem o conteúdo do caminho.
- `FillRule`, do tipo `FillRule` , que determina como as áreas de interseção contidas na geometria são combinadas. O valor padrão dessa propriedade é `FillRule.EvenOdd`.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

Para obter mais informações sobre a `FillRule` enumeração, consulte [ Xamarin.Forms formas: regras de preenchimento](fillrules.md).

> [!NOTE]
> A `Figures` propriedade é a `ContentProperty` da `PathGeometry` classe e, portanto, não precisa ser definida explicitamente a partir de XAML.

Um `PathGeometry` é composto de uma coleção de `PathFigure` objetos, com cada `PathFigure` um deles descrevendo uma forma na geometria. Cada `PathFigure` um deles é composto de um ou mais `PathSegment` objetos, cada um deles descreve um segmento da forma. Há muitos tipos de segmentos:

- `ArcSegment`, que cria um arco elíptico entre dois pontos.
- `BezierSegment`, que cria uma curva de Bézier cúbica entre dois pontos.
- `LineSegment`, que cria uma linha entre dois pontos.
- `PolyBezierSegment`, que cria uma série de curvas Bézier cúbicas.
- `PolyLineSegment`, que cria uma série de linhas.
- `PolyQuadraticBezierSegment`, que cria uma série de curvas Bézier quadráticas.
- `QuadraticBezierSegment`, que cria uma curva de Bézier quadrática.

Todas as classes acima derivam da `PathSegment` classe abstrata.

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

### <a name="create-an-arcsegment"></a>Criar um ArcSegment

Um `ArcSegment` cria um arco elíptico entre dois pontos. Um arco elíptico é definido por seus pontos inicial e final, x-e y-raio, fator de rotação do eixo x, um valor que indica se o arco deve ser maior que 180 graus e um valor que descreve a direção na qual o arco é desenhado.

A `ArcSegment` classe define as seguintes propriedades:

- `Point`, do tipo [`Point`](xref:Xamarin.Forms.Point) , que representa o ponto de extremidade do arco elíptico. O valor padrão dessa propriedade é (0, 0).
- `Size`, do tipo [`Size`](xref:Xamarin.Forms.Size) , que representa o raio x e y do arco. O valor padrão dessa propriedade é (0, 0).
- `RotationAngle`, do tipo `double` , que representa a quantidade em graus pela qual a elipse é girada ao lado do eixo x. O valor padrão dessa propriedade é 0.
- `SweepDirection`, do tipo `SweepDirection` , que especifica a direção na qual o arco é desenhado. O valor padrão dessa propriedade é `SweepDirection.CounterClockwise`.
- `IsLargeArc`, do tipo `bool` , que indica se o arco deve ser maior que 180 graus. O valor padrão dessa propriedade é `false`.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

> [!NOTE]
> A `ArcSegment` classe não contém uma propriedade para o ponto inicial do arco. Ele só define o ponto final do arco que ele representa. O ponto inicial do arco é o ponto atual do `PathFigure` ao qual o `ArcSegment` é adicionado.

A enumeração `SweepDirection` define os seguintes membros:

- `CounterClockwise`, que especifica que os arcos são desenhados em uma direção horária.
- `Clockwise`, que especifica que os arcos são desenhados em uma direção horária do contador.

O exemplo a seguir mostra como criar e renderizar um `ArcSegment` em um `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,100">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <ArcSegment Size="100,50"
                                            RotationAngle="45"
                                            IsLargeArc="True"
                                            SweepDirection="CounterClockwise"
                                            Point="200,100" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

Neste exemplo, um arco elíptico é desenhado de (10.100) a (200.100).

### <a name="create-a-beziersegment"></a>Criar um BezierSegment

Um `BezierSegment` cria uma curva de Bézier cúbica entre dois pontos. Uma curva de Bézier cúbica é definida por quatro pontos: um ponto de partida, um ponto de extremidade e dois pontos de controle.

A `BezierSegment` classe define as seguintes propriedades:

- `Point1`, do tipo [`Point`](xref:Xamarin.Forms.Point) , que representa o primeiro ponto de controle da curva. O valor padrão dessa propriedade é (0, 0).
- `Point2`, do tipo [`Point`](xref:Xamarin.Forms.Point) , que representa o segundo ponto de controle da curva. O valor padrão dessa propriedade é (0, 0).
- `Point3`, do tipo [`Point`](xref:Xamarin.Forms.Point) , que representa o ponto final da curva. O valor padrão dessa propriedade é (0, 0).

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

> [!NOTE]
> A `BezierSegment` classe não contém uma propriedade para o ponto inicial da curva. O ponto inicial da curva é o ponto atual do `PathFigure` ao qual o `BezierSegment` é adicionado.

Os dois pontos de controle de uma curva de Bézier cúbica se comportam como os ímãs, atraindo partes do que, de outra forma, seriam uma linha reta em direção a si mesmos e produzindo uma curva. O primeiro ponto de controle afeta a parte inicial da curva. O segundo ponto de controle afeta a parte final da curva. A curva não é necessariamente transmitida por nenhum dos pontos de controle. Em vez disso, cada ponto de controle move sua parte da linha para si mesmo, mas não por si só.

O exemplo a seguir mostra como criar e renderizar um `BezierSegment` em um `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <BezierSegment Point1="100,0"
                                               Point2="200,200"
                                               Point3="300,10" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

Neste exemplo, uma curva de Bézier cúbica é desenhada de (10, 10) para (300, 10). A curva tem dois pontos de controle em (100, 0) e (200.200):

![BezierSegment](geometry-images/beziersegment.png "BezierSegment")

### <a name="create-a-linesegment"></a>Criar um LineSegment

Um `LineSegment` cria uma linha entre dois pontos.

A `LineSegment` classe define a `Point` propriedade, do tipo [`Point`](xref:Xamarin.Forms.Point) , que representa o ponto final do segmento de linha. O valor padrão dessa propriedade é (0, 0) e é apoiado por um [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, o que significa que ele pode ser o destino de associações de dados e com estilo.

> [!NOTE]
> A `LineSegment` classe não contém uma propriedade para o ponto inicial da linha. Ele só define o ponto de extremidade. O ponto inicial da linha é o ponto atual do `PathFigure` ao qual o `LineSegment` é adicionado.

O exemplo a seguir mostra como criar e renderizar `LineSegment` objetos em um `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Start">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure IsClosed="True"
                                StartPoint="10,100">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <LineSegment Point="100,100" />
                                <LineSegment Point="100,50" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

Neste exemplo, um segmento de linha é desenhado de (10.100) a (100.100) e de (100.100) a (100, 50). Além disso, o `PathFigure` é fechado porque sua `IsClosed` propriedade é definida como `true` . Isso resulta em um triângulo que está sendo desenhado:

![LineSegments](geometry-images/linesegments.png "LineSegments")

### <a name="create-a-polybeziersegment"></a>Criar um PolyBezierSegment

Um `PolyBezierSegment` cria uma ou mais curvas Bézier cúbicas.

A `PolyBezierSegment` classe define a `Points` propriedade, do tipo `PointCollection` , que representa os pontos que definem o `PolyBezierSegment` . Um `PointCollection` é um `ObservableCollection` dos [`Point`](xref:Xamarin.Forms.Point) objetos. Essa propriedade é apoiada por um [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, o que significa que ele pode ser o destino de associações de dados e com estilo.

> [!NOTE]
> A `PolyBezierSegment` classe não contém uma propriedade para o ponto inicial da curva. O ponto inicial da curva é o ponto atual do `PathFigure` ao qual o `PolyBezierSegment` é adicionado.

O exemplo a seguir mostra como criar e renderizar um `PolyBezierSegment` em um `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <PolyBezierSegment Points="0,0 100,0 150,100 150,0 200,0 300,10" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

Neste exemplo, o `PolyBezierSegment` especifica duas curvas Bézier cúbicas. A primeira curva é de (10, 10) a (150.100) com um ponto de controle de (0, 0) e outro ponto de controle de (100, 0). A segunda curva é de (150.100) a (300, 10) com um ponto de controle de (150, 0) e outro ponto de controle de (200, 0):

![PolyBezierSegment](geometry-images/polybeziersegment.png "PolyBezierSegment")

### <a name="create-a-polylinesegment"></a>Criar um PolyLineSegment

Um `PolyLineSegment` cria um ou mais segmentos de linha.

A `PolyLineSegment` classe define a `Points` propriedade, do tipo `PointCollection` , que representa os pontos que definem o `PolyLineSegment` . Um `PointCollection` é um `ObservableCollection` dos [`Point`](xref:Xamarin.Forms.Point) objetos. Essa propriedade é apoiada por um [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, o que significa que ele pode ser o destino de associações de dados e com estilo.

> [!NOTE]
> A `PolyLineSegment` classe não contém uma propriedade para o ponto inicial da linha. O ponto inicial da linha é o ponto atual do `PathFigure` ao qual o `PolyLineSegment` é adicionado.

O exemplo a seguir mostra como criar e renderizar um `PolyLineSegment` em um `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigure StartPoint="10,10">
                    <PathFigure.Segments>
                        <PolyLineSegment Points="50,10 50,50" />
                    </PathFigure.Segments>
                </PathFigure>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

Neste exemplo, o `PolyLineSegment` especifica duas linhas. A primeira linha é de (10, 10) a (50, 10) e a segunda linha é de (50, 10) a (50, 50):

![PolyLineSegment](geometry-images/polylinesegment.png "PolyLineSegment")

### <a name="create-a-polyquadraticbeziersegment"></a>Criar um PolyQuadraticBezierSegment

Um `PolyQuadraticBezierSegment` cria uma ou mais curvas Bézier quadráticas.

A `PolyQuadraticBezierSegment` classe define a `Points` propriedade, do tipo `PointCollection` , que representa os pontos que definem o `PolyQuadraticBezierSegment` . Um `PointCollection` é um `ObservableCollection` dos [`Point`](xref:Xamarin.Forms.Point) objetos. Essa propriedade é apoiada por um [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, o que significa que ele pode ser o destino de associações de dados e com estilo.

> [!NOTE]
> A `PolyQuadraticBezierSegment` classe não contém uma propriedade para o ponto inicial da curva. O ponto inicial da curva é o ponto atual do `PathFigure` ao qual o `PolyQuadraticBezierSegment` é adicionado.

O exemplo a seguir mostra como criar e renderizar um `PolyQuadraticBezierSegment` em um `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <PolyQuadraticBezierSegment Points="100,100 150,50 0,100 15,200" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

Neste exemplo, o `PolyQuadraticBezierSegment` especifica duas curvas de Bézier. A primeira curva é de (10, 10) a (150, 50) com um ponto de controle em (100.100). A segunda curva é de (100.100) a (15.200) com um ponto de controle em (0100):

![PolyQuadraticBezierSegment](geometry-images/polyquadraticbeziersegment.png "PolyQuadraticBezierSegment")

### <a name="create-a-quadraticbeziersegment"></a>Criar um QuadraticBezierSegment

Um `QuadraticBezierSegment` cria uma curva de Bezier quadrática entre dois pontos.

A `QuadraticBezierSegment` classe define as seguintes propriedades:

- `Point1`, do tipo [`Point`](xref:Xamarin.Forms.Point) , que representa o ponto de controle da curva. O valor padrão dessa propriedade é (0, 0).
- `Point2`, do tipo [`Point`](xref:Xamarin.Forms.Point) , que representa o ponto final da curva. O valor padrão dessa propriedade é (0, 0).

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

> [!NOTE]
> A `QuadraticBezierSegment` classe não contém uma propriedade para o ponto inicial da curva. O ponto inicial da curva é o ponto atual do `PathFigure` ao qual o `QuadraticBezierSegment` é adicionado.

O exemplo a seguir mostra como criar e renderizar um `QuadraticBezierSegment` em um `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <QuadraticBezierSegment Point1="200,200"
                                                        Point2="300,10" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

Neste exemplo, uma curva de Bezier quadrática é desenhada de (10, 10) para (300, 10). A curva tem um ponto de controle em (200.200):

![QuadraticBezierSegment](geometry-images/quadraticbeziersegment.png "QuadraticBezierSegment")

### <a name="create-complex-geometries"></a>Criar geometrias complexas

Geometrias mais complexas podem ser criadas usando uma combinação de `PathSegment` objetos. O exemplo a seguir cria uma forma usando um `BezierSegment` , um `LineSegment` e um `ArcSegment` :

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigure StartPoint="10,50">
                    <PathFigure.Segments>
                        <BezierSegment Point1="100,0"
                                       Point2="200,200"
                                       Point3="300,100"/>
                        <LineSegment Point="400,100" />
                        <ArcSegment Size="50,50"
                                    RotationAngle="45"
                                    IsLargeArc="True"
                                    SweepDirection="Clockwise"
                                    Point="200,100"/>
                    </PathFigure.Segments>
                </PathFigure>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

Neste exemplo, um `BezierSegment` é definido primeiro usando quatro pontos. Em seguida, o exemplo adiciona um `LineSegment` , que é desenhado entre o ponto de extremidade de `BezierSegment` até o ponto especificado pelo `LineSegment` . Por fim, um `ArcSegment` é desenhado a partir do ponto de extremidade do `LineSegment` até o ponto especificado pelo `ArcSegment` .

Geometrias ainda mais complexas podem ser criadas usando vários `PathFigure` objetos dentro de um `PathGeometry` . O exemplo a seguir cria um `PathGeometry` de sete `PathFigure` objetos, alguns dos quais contêm vários `PathSegment` objetos:

```xaml
<Path Stroke="Red"
      StrokeThickness="12"
      StrokeLineJoin="Round">
    <Path.Data>
        <PathGeometry>
            <!-- H -->
            <PathFigure StartPoint="0,0">
                <LineSegment Point="0,100" />
            </PathFigure>
            <PathFigure StartPoint="0,50">
                <LineSegment Point="50,50" />
            </PathFigure>
            <PathFigure StartPoint="50,0">
                <LineSegment Point="50,100" />
            </PathFigure>

            <!-- E -->
            <PathFigure StartPoint="125, 0">
                <BezierSegment Point1="60, -10"
                               Point2="60, 60"
                               Point3="125, 50" />
                <BezierSegment Point1="60, 40"
                               Point2="60, 110"
                               Point3="125, 100" />
            </PathFigure>

            <!-- L -->
            <PathFigure StartPoint="150, 0">
                <LineSegment Point="150, 100" />
                <LineSegment Point="200, 100" />
            </PathFigure>

            <!-- L -->
            <PathFigure StartPoint="225, 0">
                <LineSegment Point="225, 100" />
                <LineSegment Point="275, 100" />
            </PathFigure>

            <!-- O -->
            <PathFigure StartPoint="300, 50">
                <ArcSegment Size="25, 50"
                            Point="300, 49.9"
                            IsLargeArc="True" />
            </PathFigure>
        </PathGeometry>
    </Path.Data>
</Path>
```

Neste exemplo, a palavra "Olá" é desenhada usando uma combinação `LineSegment` de `BezierSegment` objetos e, juntamente com um único `ArcSegment` objeto:

![Vários objetos PathFigure](geometry-images/multiple-pathfigures.png "Vários objetos PathFigure")

## <a name="composite-geometries"></a>Geometrias compostas

Objetos de geometria composta podem ser criados usando um `GeometryGroup` . A `GeometryGroup` classe cria uma geometria composta de um ou mais `Geometry` objetos. Qualquer número de `Geometry` objetos pode ser adicionado a um `GeometryGroup` .

A `GeometryGroup` classe define as seguintes propriedades:

- `Children`, do tipo `GeometryCollection` , que especifica os objetos que definem o `GeomtryGroup` . Um `GeometryCollection` é um `ObservableCollection` dos `Geometry` objetos.
- `FillRule`, do tipo `FillRule` , que especifica como as áreas de interseção no `GeometryGroup` são combinadas. O valor padrão dessa propriedade é `FillRule.EvenOdd`.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

> [!NOTE]
> A `Children` propriedade é a `ContentProperty` da `GeometryGroup` classe e, portanto, não precisa ser definida explicitamente a partir de XAML.

Para obter mais informações sobre a `FillRule` enumeração, consulte [ Xamarin.Forms formas: regras de preenchimento](fillrules.md).

Para desenhar uma geometria composta, defina os `Geometry` objetos necessários como filhos de um `GeometryGroup` e exiba-os com um `Path` objeto. O XAML a seguir mostra um exemplo disso:

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

Neste exemplo, quatro `EllipseGeometry` objetos com coordenadas x-RADIUS e y-RADIUS idênticas, mas com diferentes coordenadas do centro, são combinados. Isso cria quatro círculos sobrepostos, cujos interiores são preenchidos em laranja devido à `EvenOdd` regra de preenchimento padrão:

![GeometryGroup](geometry-images/geometrygroup.png "GeometryGroup")

## <a name="clip-with-a-geometry"></a>Cortar com uma geometria

A [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe tem uma `Clip` propriedade, do tipo `Geometry` , que define o contorno do conteúdo de um elemento. Quando a `Clip` propriedade é definida como um `Geometry` objeto, somente a área que está dentro da região do `Geometry` será visível.

O exemplo a seguir mostra como usar um `Geometry` objeto como a região do clipe para um [`Image`](xref:Xamarin.Forms.Image) :

```xaml
<Image Source="monkeyface.png">
    <Image.Clip>
        <EllipseGeometry RadiusX="100"
                         RadiusY="100"
                         Center="180,180" />
    </Image.Clip>
</Image>
```

Neste exemplo, um `EllipseGeometry` com `RadiusX` e `RadiusY` valores de 100, e um `Center` valor de (180.180) é definido como a `Clip` propriedade de um [`Image`](xref:Xamarin.Forms.Image) . Somente a parte da imagem que está dentro da área da elipse será exibida:

![Recortar uma imagem com um EllipseGeometry](geometries-images/clip-ellipsegeometry.png "Recortar uma imagem com um EllipseGeometry")

> [!NOTE]
> Geometrias simples, geometrias de caminho e geometrias compostas podem ser usadas para recortar [`VisualElement`](xref:Xamarin.Forms.VisualElement) objetos.

## <a name="other-features"></a>Outros recursos

A `GeometryHelper` classe fornece os seguintes métodos auxiliares:

- `FlattenGeometry`, que achata um `Geometry` em um `PathGeometry` .
- `FlattenCubicBezier`, que nivela uma curva de Bézier cúbica em uma `List<Point>` coleção.
- `FlattenQuadraticBezier`, que nivela uma curva de Bézier quadrática em uma `List<Point>` coleção.
- `FlattenArc`, que achata um arco elíptico em uma `List<Point>` coleção.

## <a name="related-links"></a>Links relacionados

- [ShapeDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Existentes](index.md)
- [Xamarin.Forms Formas: regras de preenchimento](fillrules.md)