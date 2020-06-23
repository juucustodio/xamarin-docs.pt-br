---
title: 'Xamarin.FormsFormas: caminho'
description: A Xamarin.Forms classe path pode ser usada para desenhar curvas e formas complexas.
ms.prod: xamarin
ms.assetid: B29486F4-9A5E-4588-ABDF-7EB1E69B9AE6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 384dcef3c2b480166f17e91d547f8cda39c83dc0
ms.sourcegitcommit: 7fc658bbdcb8130cd9d611e55e79a1830fc5d5a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85132964"
---
# <a name="xamarinforms-shapes-path"></a>Xamarin.FormsFormas: caminho

![](~/media/shared/preview.png "This API is currently pre-release")

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

A `Path` classe deriva da `Shape` classe e pode ser usada para desenhar curvas e formas complexas. Essas curvas e formas geralmente são descritas usando `Geometry` objetos. Para obter informações sobre as propriedades que a `Path` classe herda da `Shape` classe, consulte [ Xamarin.Forms formas](index.md).

`Path` define as propriedades a seguir:

- `Data`, do tipo `Geometry` , que especifica a forma a ser desenhada.
- `RenderTransform`, do tipo `Transform` , que representa a transformação que é aplicada à geometria de um caminho antes de ser desenhada.

Essas propriedades são apoiadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, o que significa que eles podem ser destinos de associações de dados e com estilo.

Para obter mais informações sobre transformações, consulte [ Xamarin.Forms caminho transformações](path-transforms.md).

## <a name="create-a-path"></a>Criar um caminho

O exemplo de XAML a seguir mostra como desenhar um polígono usando uma sintaxe abreviada especial conhecida como sintaxe de marcação de caminho:

```xaml
<Path Data="M 10,50 L 200,70"
      Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Start"
      HeightRequest="100"
      WidthRequest="100" />
```

A `Data` cadeia de caracteres começa com o comando "MoveTo", indicado por `M` , que estabelece um ponto de partida para o caminho. `L`é o comando line, que cria uma linha reta a partir do ponto inicial até o ponto de extremidade especificado.

> [!NOTE]
> A sintaxe de marcação de caminho só está disponível em XAML.

Para obter mais informações sobre a sintaxe de marcação de caminho, consulte [ Xamarin.Forms sintaxe de marcação de caminho](path-markup-syntax.md).

## <a name="path-geometry"></a>Geometria do caminho

As curvas e formas podem ser descritas usando `Geometry` objetos, que são usados para definir a `Path` Propriedade do objeto `Data` .

Há uma variedade de `Geometry` objetos a serem escolhidos. As `EllipseGeometry` `LineGeometry` classes, e `RectangleGeometry` descrevem formas relativamente simples. Para criar formas mais complexas ou criar curvas, use um `PathGeometry` .

`PathGeometry`os objetos são compostos de um ou mais `PathFigure` objetos. Cada `PathFigure` objeto representa uma forma diferente. Cada `PathFigure` objeto é composto de um ou mais `PathSegment` objetos, cada um representando uma parte de conexão da forma. Os tipos de segmento incluem o seguinte: `LineSegment` , `BezierSegment` e `ArcSegment` .

No exemplo a seguir, um `Path` é usado para desenhar um triângulo:

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Start"
      HeightRequest="100"
      WidthRequest="100">
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

Para obter mais informações sobre geometrias, consulte [ Xamarin.Forms geometrias](geometries.md).

## <a name="related-links"></a>Links relacionados

- [ShapeDemos (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsExistentes](index.md)
- [Xamarin.FormsGeometrias](geometries.md)
- [Xamarin.FormsSintaxe de marcação de caminho](path-markup-syntax.md)
- [Xamarin.FormsTransformações de caminho](path-transforms.md)
