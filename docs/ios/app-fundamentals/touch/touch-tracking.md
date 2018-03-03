---
title: Controle de multitoque dedo
description: "Este documento demonstra como controlar os eventos de toque de vários dedos"
ms.topic: article
ms.prod: xamarin
ms.assetid: 48E8B20D-0833-43D2-976A-0605DDB386E3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: c096e211bc29e94dbff0202c50ca69780cb6849e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="multi-touch-finger-tracking"></a>Controle de multitoque dedo

_Este documento demonstra como controlar os eventos de toque de vários dedos_

Há ocasiões em que um aplicativo de multitoque precisa acompanhar dedos individuais conforme se move simultaneamente na tela. Um aplicativo típico é um programa de finger-paint. Você deseja que o usuário possa desenhar com um único dedo, mas também para desenhar punho vários ao mesmo tempo. Como o programa processa vários eventos de toque, ela precisa distinguir entre essas dedos.

Quando um dedo toca primeiro na tela, o iOS cria um [ `UITouch` ](https://developer.xamarin.com/api/type/UIKit.UITouch/) objeto para que o dedo. Esse objeto permanece o mesmo como o dedo move na tela e, em seguida, levante na tela, no ponto em que o objeto é descartado. Para manter o controle dos dedos, um programa deve evitar armazenar esses dados `UITouch` diretamente do objeto. Em vez disso, ele pode usar o [ `Handle` ](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/) propriedade do tipo `IntPtr` para identificar esses `UITouch` objetos.

Quase sempre, um programa que controla os dedos individuais mantém um dicionário para controle de toque. Para um programa do iOS, a chave de dicionário é o `Handle` valor que identifica um determinado dedo. O valor do dicionário depende do aplicativo. No [pintura a dedo](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint) programa, cada traço dedo (de toque para liberar) é associado um objeto que contém todas as informações necessárias para processar a linha desenhada com esse dedo. O programa define um pequeno `FingerPaintPolyline` classe para essa finalidade:

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new CGPath();
    }

    public CGColor Color { set; get; }

    public float StrokeWidth { set; get; }

    public CGPath Path { private set; get; }
}
```

Cada polilinha tem uma cor, a largura do traço e um gráfico de iOS [ `CGPath` ](https://developer.xamarin.com/api/type/CoreGraphics.CGPath/) objeto acumular e processar vários pontos da linha como ela é desenhada.


Todo o restante do código mostrado a seguir está contido em um `UIView` derivada denominada `FingerPaintCanvasView`. Classe mantém um dicionário de objetos do tipo `FingerPaintPolyline` durante o tempo que ativamente estão sendo desenhadas por um ou mais dedos:

```csharp
Dictionary<IntPtr, FingerPaintPolyline> inProgressPolylines = new Dictionary<IntPtr, FingerPaintPolyline>();
```

Este dicionário permite que o modo de exibição obter rapidamente o `FingerPaintPolyline` informações associadas a cada dedo com base no `Handle` propriedade o `UITouch` objeto.

O `FingerPaintCanvasView` classe também mantém um `List` objeto para os polilinhas que foram concluídas:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Os objetos nesta `List` estão na mesma ordem em que foram feitos.

`FingerPaintCanvasView` substitui os cinco métodos definidos pelo `View`:

- [`TouchesBegan`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesBegan/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesMoved`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesMoved/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesEnded`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesEnded/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesCancelled`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesCancelled/p/Foundation.NSSet/UIKit.UIEvent/)
- [`Draw`](https://developer.xamarin.com/api/member/UIKit.UIView.Draw/p/CoreGraphics.CGRect/)

Vários `Touches` substituições acumulam os pontos que compõem as polilinhas.

O [`Draw`] substituição desenha as polilinhas concluídas e, em seguida, as polilinhas em andamento:

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    using (CGContext context = UIGraphics.GetCurrentContext())
    {
        // Stroke settings
        context.SetLineCap(CGLineCap.Round);
        context.SetLineJoin(CGLineJoin.Round);

        // Draw the completed polylines
        foreach (FingerPaintPolyline polyline in completedPolylines)
        {
            context.SetStrokeColor(polyline.Color);
            context.SetLineWidth(polyline.StrokeWidth);
            context.AddPath(polyline.Path);
            context.DrawPath(CGPathDrawingMode.Stroke);
        }

        // Draw the in-progress polylines
        foreach (FingerPaintPolyline polyline in inProgressPolylines.Values)
        {
            context.SetStrokeColor(polyline.Color);
            context.SetLineWidth(polyline.StrokeWidth);
            context.AddPath(polyline.Path);
            context.DrawPath(CGPathDrawingMode.Stroke);
        }
    }
}
```

Cada uma da `Touches` substituições potencialmente relata as ações de vários dedos, indicados por um ou mais `UITouch` objetos armazenados no `touches` argumento do método. O `TouchesBegan` substituições loop através desses objetos. Para cada `UITouch` do objeto, o método cria e inicializa uma nova `FingerPaintPolyline` objeto, incluindo a armazenar o local inicial do dedo obtido o `LocationInView` método. Isso `FingerPaintPolyline` objeto é adicionado ao `InProgressPolylines` dicionário usando o `Handle` propriedade o `UITouch` objeto como uma chave de dicionário:

```csharp
public override void TouchesBegan(NSSet touches, UIEvent evt)
{
    base.TouchesBegan(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Create a FingerPaintPolyline, set the initial point, and store it
        FingerPaintPolyline polyline = new FingerPaintPolyline
        {
            Color = StrokeColor,
            StrokeWidth = StrokeWidth,
        };

        polyline.Path.MoveToPoint(touch.LocationInView(this));
        inProgressPolylines.Add(touch.Handle, polyline);
    }
    SetNeedsDisplay();
}
```

O método é concluído chamando `SetNeedsDisplay` para gerar uma chamada para o `Draw` substituir e atualizar a tela.

Como o dedo ou dedos movem na tela, o `View` obtém várias chamadas para seu `TouchesMoved` substituir. Essa substituição é da mesma forma percorre o `UITouch` objetos armazenados no `touches` argumento e adiciona o local atual do dedo para o caminho de gráficos:

```csharp
public override void TouchesMoved(NSSet touches, UIEvent evt)
{
    base.TouchesMoved(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Add point to path
        inProgressPolylines[touch.Handle].Path.AddLineToPoint(touch.LocationInView(this));
    }
    SetNeedsDisplay();
}
```

O `touches` coleção contém apenas aqueles `UITouch` objetos para os dedos que mudaram desde a última chamada a `TouchesBegan` ou `TouchesMoved`. Se você alguma vez precisar `UITouch` objetos correspondentes aos *todos os* dedos atualmente em contato com a tela, essa informação está disponível por meio de `AllTouches` propriedade do `UIEvent` argumento para o método.

O `TouchesEnded` substituição tem dois trabalhos. Adicione o último ponto para o caminho de gráficos e transferir a `FingerPaintPolyline` de objeto o `inProgressPolylines` dicionário para a `completedPolylines` lista:

```csharp
public override void TouchesEnded(NSSet touches, UIEvent evt)
{
    base.TouchesEnded(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Get polyline from dictionary and remove it from dictionary
        FingerPaintPolyline polyline = inProgressPolylines[touch.Handle];
        inProgressPolylines.Remove(touch.Handle);

        // Add final point to path and save with completed polylines
        polyline.Path.AddLineToPoint(touch.LocationInView(this));
        completedPolylines.Add(polyline);
    }
    SetNeedsDisplay();
}
```

O `TouchesCancelled` substituição é tratada pelo simplesmente abandonar a `FingerPaintPolyline` objeto no dicionário:

```csharp
public override void TouchesCancelled(NSSet touches, UIEvent evt)
{
    base.TouchesCancelled(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        inProgressPolylines.Remove(touch.Handle);
    }
    SetNeedsDisplay();
}
```

Esse processamento permite que o [pintura a dedo](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint) programa para controlar os dedos individuais e desenhar os resultados na tela:

[ ![](touch-tracking-images/image01.png "Controle dedos individuais e os resultados na tela de desenho")](touch-tracking-images/image01.png)

Agora você viu como você pode controlar individuais dedos na tela e distinguir entre eles.



## <a name="related-links"></a>Links relacionados

- [Guia de Xamarin Android equivalente](~/android/app-fundamentals/touch/touch-tracking.md)
- [Pintura a dedo (exemplo)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)
