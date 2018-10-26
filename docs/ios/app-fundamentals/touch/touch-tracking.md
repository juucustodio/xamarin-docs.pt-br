---
title: Dedo multitoque acompanhamento no xamarin. IOS
description: Este documento descreve como controlar os dedos individuais em gestos multitoque em um aplicativo xamarin. IOS. Ele gira em torno de um exemplo de aplicativo para pintura com dedo.
ms.prod: xamarin
ms.assetid: 48E8B20D-0833-43D2-976A-0605DDB386E3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: a1ddcda84d51b5a8a9220558ddaf9476a2321ee8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105045"
---
# <a name="multi-touch-finger-tracking-in-xamarinios"></a>Dedo multitoque acompanhamento no xamarin. IOS

_Este documento demonstra como controlar os eventos de toque de vários dedos_

Há vezes quando um aplicativo multitoque necessárias para acompanhar os dedos individuais quando eles passam simultaneamente na tela. Um aplicativo típico é um programa de finger-paint. Você deseja que o usuário seja capaz de desenhar com um único dedo, mas também para desenhar com vários dedos, ao mesmo tempo. Como seu programa processa vários eventos de toque, ele precisa distinguir entre esses dedos.

Quando um dedo toca na tela primeiro, o iOS cria uma [ `UITouch` ](https://developer.xamarin.com/api/type/UIKit.UITouch/) objeto para desse dedo. Esse objeto permanece o mesmo como o dedo se move na tela e depois se afasta da tela, no ponto em que o objeto é descartado. Para manter o controle de dedos, um programa deve evitar armazenar isso `UITouch` diretamente do objeto. Em vez disso, ele pode usar o [ `Handle` ](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/) propriedade do tipo `IntPtr` exclusivamente para identificar esses `UITouch` objetos.

Quase sempre, um programa que acompanha os dedos individuais mantém um dicionário para acompanhamento de toque. Um programa de iOS, a chave do dicionário é o `Handle` valor que identifica um dedo específico. O valor do dicionário depende do aplicativo. No [pintura a dedo](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint) programa, cada traço do dedo (desde toque a versão) é associado um objeto que contém todas as informações necessárias para renderizar a linha desenhada desse dedo. O programa define um pequeno `FingerPaintPolyline` classe para essa finalidade:

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

Cada polilinha tem uma cor, uma largura de traço e um gráfico de iOS [ `CGPath` ](https://developer.xamarin.com/api/type/CoreGraphics.CGPath/) objeto acumular e renderizar os vários pontos da linha conforme ela é desenhada.


Todo o resto do código mostrado a seguir está contido em uma `UIView` derivativo chamado `FingerPaintCanvasView`. Se a classe mantém um dicionário de objetos do tipo `FingerPaintPolyline` durante a hora em que eles estão sendo desenhados ativamente por um ou mais dedos:

```csharp
Dictionary<IntPtr, FingerPaintPolyline> inProgressPolylines = new Dictionary<IntPtr, FingerPaintPolyline>();
```

Esse dicionário permite que o modo de exibição obter rapidamente o `FingerPaintPolyline` informações associadas com cada dedo com base no `Handle` propriedade do `UITouch` objeto.

O `FingerPaintCanvasView` classe também mantém um `List` objeto para as polilinhas que foram concluídas:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Os objetos nesta `List` estão na mesma ordem que foram desenhadas.

`FingerPaintCanvasView` substitui os cinco métodos definidos pelo `View`:

- [`TouchesBegan`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesBegan/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesMoved`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesMoved/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesEnded`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesEnded/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesCancelled`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesCancelled/p/Foundation.NSSet/UIKit.UIEvent/)
- [`Draw`](https://developer.xamarin.com/api/member/UIKit.UIView.Draw/p/CoreGraphics.CGRect/)

Os vários `Touches` substituições acumulam os pontos que compõem as polilinhas.

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

Cada um dos `Touches` substituições potencialmente relata as ações de vários dedos, indicados por um ou mais `UITouch` objetos armazenados no `touches` argumento para o método. O `TouchesBegan` substituições loop através desses objetos. Para cada `UITouch` do objeto, o método cria e inicializa uma nova `FingerPaintPolyline` objeto, inclusive armazenar o local inicial do dedo obtido o `LocationInView` método. Isso `FingerPaintPolyline` objeto é adicionado ao `InProgressPolylines` dicionário usando o `Handle` propriedade do `UITouch` objeto como uma chave de dicionário:

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

À medida que o dedo ou dedos move na tela, o `View` obtém várias chamadas para seus `TouchesMoved` substituir. Essa substituição é da mesma forma percorre os `UITouch` objetos armazenados no `touches` argumento e adiciona o local atual do dedo para o caminho de gráficos:

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

O `touches` coleção contém somente aqueles `UITouch` objetos para os dedos que mudaram desde a última chamada para `TouchesBegan` ou `TouchesMoved`. Se você precisar `UITouch` objetos correspondentes aos *todos os* os dedos atualmente em contato com a tela, essa informação está disponível por meio de `AllTouches` propriedade do `UIEvent` argumento para o método.

O `TouchesEnded` substituição tiver dois trabalhos. Ele deve adicionar o último ponto para o caminho de gráficos e transferir a `FingerPaintPolyline` do objeto do `inProgressPolylines` dicionário para o `completedPolylines` lista:

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

O `TouchesCancelled` substituição é tratada pelo simplesmente abandonar o `FingerPaintPolyline` objeto no dicionário:

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

Completamente, esse processamento permite que o [pintura a dedo](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint) programa para acompanhar os dedos individuais e desenhar os resultados na tela:

[![](touch-tracking-images/image01.png "Controle de dedos individuais e os resultados de desenho na tela")](touch-tracking-images/image01.png#lightbox)

Agora você já viu como controle de dedos individuais na tela e distinguir entre elas.



## <a name="related-links"></a>Links relacionados

- [Guia do Xamarin Android equivalente](~/android/app-fundamentals/touch/touch-tracking.md)
- [Pintura a dedo (amostra)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)
