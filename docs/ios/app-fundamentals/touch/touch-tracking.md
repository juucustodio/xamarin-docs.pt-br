---
title: Acompanhamento de dedos multitoque no Xamarin. iOS
description: Este documento descreve como acompanhar os dedos individuais em gestos multitoque em um aplicativo Xamarin. iOS. Ele se concentra em um exemplo de aplicativo de pintura a dedos.
ms.prod: xamarin
ms.assetid: 48E8B20D-0833-43D2-976A-0605DDB386E3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: c3998424c8f4e9482a41e2891e65f0d13d8ac2f3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73009182"
---
# <a name="multi-touch-finger-tracking-in-xamarinios"></a>Acompanhamento de dedos multitoque no Xamarin. iOS

_Este documento demonstra como acompanhar eventos de toque de vários dedos_

Há ocasiões em que um aplicativo multitoque precisa acompanhar os dedos individuais à medida que eles se movem simultaneamente na tela. Um aplicativo típico é um programa de pintura a dedos. Você deseja que o usuário seja capaz de desenhar com um único dedo, mas também desenhar com vários dedos de uma só vez. À medida que o programa processa vários eventos de toque, ele precisa distinguir entre esses dedos.

Quando um dedo toca a tela pela primeira vez, o iOS cria um objeto [`UITouch`](xref:UIKit.UITouch) para esse dedo. Esse objeto permanece o mesmo que o dedo é movido na tela e, em seguida, é levantado da tela e, nesse ponto, o objeto é Descartado. Para manter o controle dos dedos, um programa deve evitar armazenar esse `UITouch` objeto diretamente. Em vez disso, ele pode usar a propriedade [`Handle`](xref:Foundation.NSObject.Handle) do tipo `IntPtr` para identificar exclusivamente esses objetos `UITouch`.

Quase sempre, um programa que controla os dedos individuais mantém um dicionário para acompanhamento de toque. Para um programa iOS, a chave de dicionário é o valor `Handle` que identifica um dedo específico. O valor do dicionário depende do aplicativo. No programa [FingerPaint](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint) , cada traço de dedo (de toque para liberação) é associado a um objeto que contém todas as informações necessárias para renderizar a linha desenhada com esse dedo. O programa define uma pequena classe de `FingerPaintPolyline` para essa finalidade:

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

Cada polilinha tem uma cor, uma largura de traço e um gráfico do iOS [`CGPath`](xref:CoreGraphics.CGPath) objeto para acumular e renderizar vários pontos da linha à medida que ele está sendo desenhado.

Todo o restante do código mostrado abaixo está contido em uma derivação `UIView` `FingerPaintCanvasView`chamada. Essa classe mantém um dicionário de objetos do tipo `FingerPaintPolyline` durante o tempo em que eles estão sendo desenhados ativamente por um ou mais dedos:

```csharp
Dictionary<IntPtr, FingerPaintPolyline> inProgressPolylines = new Dictionary<IntPtr, FingerPaintPolyline>();
```

Esse dicionário permite que a exibição Obtenha rapidamente as informações de `FingerPaintPolyline` associadas a cada dedo com base na propriedade `Handle` do objeto `UITouch`.

A classe `FingerPaintCanvasView` também mantém um objeto `List` para as polilinhas que foram concluídas:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Os objetos neste `List` estão na mesma ordem em que foram desenhados.

`FingerPaintCanvasView` substitui cinco métodos definidos por `View`:

- [`TouchesBegan`](xref:UIKit.UIResponder.TouchesBegan(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesMoved`](xref:UIKit.UIResponder.TouchesMoved(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesEnded`](xref:UIKit.UIResponder.TouchesEnded(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesCancelled`](xref:UIKit.UIResponder.TouchesCancelled(Foundation.NSSet,UIKit.UIEvent))
- [`Draw`](xref:UIKit.UIView.Draw(CoreGraphics.CGRect))

As várias substituições de `Touches` acumulam os pontos que compõem as polilinhas.

A substituição [`Draw`] desenha as polilinhas concluídas e, em seguida, as polilinhas em andamento:

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

Cada uma das `Touches` substitui potencialmente relata as ações de vários dedos, indicadas por um ou mais objetos `UITouch` armazenados no argumento `touches` para o método. O `TouchesBegan` substitui o loop por meio desses objetos. Para cada objeto de `UITouch`, o método cria e inicializa um novo objeto `FingerPaintPolyline`, incluindo o armazenamento do local inicial do dedo obtido do método `LocationInView`. Esse objeto de `FingerPaintPolyline` é adicionado ao dicionário de `InProgressPolylines` usando a propriedade `Handle` do objeto `UITouch` como uma chave de dicionário:

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

O método termina chamando `SetNeedsDisplay` para gerar uma chamada para a substituição de `Draw` e para atualizar a tela.

À medida que o dedo ou os dedos se movem na tela, o `View` Obtém várias chamadas para sua substituição de `TouchesMoved`. Essa substituição faz um loop semelhante por meio dos objetos `UITouch` armazenados no argumento `touches` e adiciona o local atual do dedo ao caminho de gráficos:

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

A coleção de `touches` contém somente os objetos `UITouch` para os dedos que foram movidos desde a última chamada para `TouchesBegan` ou `TouchesMoved`. Se você precisar de `UITouch` objetos correspondentes a *todos* os dedos atualmente em contato com a tela, essas informações estarão disponíveis por meio da propriedade `AllTouches` do argumento `UIEvent` para o método.

A substituição de `TouchesEnded` tem dois trabalhos. Ele deve adicionar o último ponto ao caminho de gráficos e transferir o objeto de `FingerPaintPolyline` do dicionário de `inProgressPolylines` para a lista de `completedPolylines`:

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

A substituição de `TouchesCancelled` é tratada simplesmente abandonando o objeto `FingerPaintPolyline` no dicionário:

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

Totalmente, esse processamento permite que o programa [FingerPaint](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint) acompanhe os dedos individuais e desenhe os resultados na tela:

[![](touch-tracking-images/image01.png "Tracking individual fingers and drawing the results on the screen")](touch-tracking-images/image01.png#lightbox)

Agora você viu como é possível acompanhar os dedos individuais na tela e fazer distinção entre eles.

## <a name="related-links"></a>Links relacionados

- [Guia equivalente do Xamarin Android](~/android/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint)
