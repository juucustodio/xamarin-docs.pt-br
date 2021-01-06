---
title: 'Xamarin.Forms Formas: caminho'
description: A Xamarin.Forms classe path pode ser usada para desenhar curvas e formas complexas.
ms.prod: xamarin
ms.assetid: B29486F4-9A5E-4588-ABDF-7EB1E69B9AE6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b184d56647e47b95bbd809baee4d9cba7360ee2f
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97939986"
---
# <a name="no-locxamarinforms-shapes-path"></a>Xamarin.Forms Formas: caminho

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

A `Path` classe deriva da `Shape` classe e pode ser usada para desenhar curvas e formas complexas. Essas curvas e formas geralmente são descritas usando `Geometry` objetos. Para obter informações sobre as propriedades que a `Path` classe herda da `Shape` classe, consulte [ Xamarin.Forms formas](index.md).

`Path` define as propriedades a seguir:

- `Data`, do tipo `Geometry` , que especifica a forma a ser desenhada.
- `RenderTransform`, do tipo `Transform` , que representa a transformação que é aplicada à geometria de um caminho antes de ser desenhada.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

Para obter mais informações sobre transformações, consulte [ Xamarin.Forms caminho transformações](path-transforms.md).

## <a name="create-a-path"></a>Criar um caminho

Para desenhar um caminho, crie um `Path` objeto e defina sua `Data` propriedade. Há duas técnicas para definir a `Data` Propriedade:

- Você pode definir um valor de cadeia de caracteres para `Data` em XAML, usando a sintaxe de marcação de caminho. Com essa abordagem, o `Path.Data` valor está consumindo um formato de serialização para gráficos. Normalmente, você não edita esse valor de cadeia de caracteres manualmente após sua criação. Em vez disso, você usa ferramentas de design para manipular os dados e exportá-los como um fragmento de cadeia de caracteres que é consumível pela `Data` propriedade.
- Você pode definir a `Data` propriedade como um `Geometry` objeto. Isso pode ser um `Geometry` objeto específico ou um `GeometryGroup` que atue como um contêiner que pode combinar vários objetos Geometry em um único objeto.

### <a name="create-a-path-with-path-markup-syntax"></a>Criar um caminho com a sintaxe de marcação de caminho

O exemplo de XAML a seguir mostra como desenhar um triângulo usando a sintaxe de marcação de caminho:

```xaml
<Path Data="M 10,100 L 100,100 100,50Z"
      Stroke="Black"
      Aspect="Uniform"
      HorizontalOptions="Start" />
```

A `Data` cadeia de caracteres começa com o comando move, indicado por `M` , que estabelece um ponto de partida absoluto para o caminho. `L` é o comando line, que cria uma linha reta a partir do ponto inicial até o ponto de extremidade especificado. `Z` é o comando Close, que cria uma linha que conecta o ponto atual ao ponto de partida. O resultado é um triângulo:

![Triângulo do caminho](path-images/triangle.png "Triângulo do caminho")

Para obter mais informações sobre a sintaxe de marcação de caminho, consulte [ Xamarin.Forms sintaxe de marcação de caminho](path-markup-syntax.md).

### <a name="create-a-path-with-geometry-objects"></a>Criar um caminho com objetos Geometry

As curvas e formas podem ser descritas usando `Geometry` objetos, que são usados para definir a `Path` Propriedade do objeto `Data` . Há uma variedade de `Geometry` objetos a serem escolhidos. As `EllipseGeometry` `LineGeometry` classes, e `RectangleGeometry` descrevem formas relativamente simples. Para criar formas mais complexas ou criar curvas, use um `PathGeometry` .

`PathGeometry` os objetos são compostos de um ou mais `PathFigure` objetos. Cada `PathFigure` objeto representa uma forma diferente. Cada `PathFigure` objeto é composto de um ou mais `PathSegment` objetos, cada um representando uma parte de conexão da forma. Os tipos de segmento incluem as `LineSegment` seguintes `BezierSegment` classes, e `ArcSegment` .

O exemplo de XAML a seguir mostra como desenhar um triângulo usando um `PathGeometry` objeto:

```xaml
<Path Stroke="Black"
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

Neste exemplo, o ponto inicial do triângulo é (10.100). Um segmento de linha é desenhado de (10.100) a (100.100) e de (100.100) a (100, 50). Em seguida, as figuras First e Last Segments são conectadas, pois a `PathFigure.IsClosed` propriedade é definida como `true` . O resultado é um triângulo:

![Triângulo do caminho](path-images/triangle.png "Triângulo do caminho")

Para obter mais informações sobre geometrias, consulte [ Xamarin.Forms geometrias](geometries.md).

## <a name="related-links"></a>Links relacionados

- [ShapeDemos (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Existentes](index.md)
- [Xamarin.Forms Geometrias](geometries.md)
- [Xamarin.Forms Sintaxe de marcação de caminho](path-markup-syntax.md)
- [Xamarin.Forms Transformações de caminho](path-transforms.md)
