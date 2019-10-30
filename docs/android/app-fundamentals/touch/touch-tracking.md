---
title: Acompanhamento de dedos multitoque no Xamarin. Android
description: Este tópico demonstra como controlar eventos de toque de vários dedos
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: f960c3cec90bd331f5a1433a869c7720b40c9680
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024276"
---
# <a name="multi-touch-finger-tracking"></a>Acompanhamento de dedos multitoque

_Este tópico demonstra como controlar eventos de toque de vários dedos_

Há ocasiões em que um aplicativo multitoque precisa acompanhar os dedos individuais à medida que eles se movem simultaneamente na tela. Um aplicativo típico é um programa de pintura a dedos. Você deseja que o usuário seja capaz de desenhar com um único dedo, mas também desenhar com vários dedos de uma só vez. À medida que o programa processa vários eventos de toque, ele precisa distinguir quais eventos correspondem a cada dedo. O Android fornece um código de ID para essa finalidade, mas obter e manipular esse código pode ser um pouco complicado.

Para todos os eventos associados a um dedo específico, o código de ID permanece o mesmo. O código de ID é atribuído quando um dedo entra na tela pela primeira vez e se torna inválido depois que o dedo é levantado da tela.
Esses códigos de ID geralmente são inteiros muito pequenos e o Android os reutiliza para eventos de toque posteriores.

Quase sempre, um programa que controla os dedos individuais mantém um dicionário para acompanhamento de toque. A chave de dicionário é o código de ID que identifica um dedo específico. O valor do dicionário depende do aplicativo. No programa [FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) , cada traço de dedo (de toque para liberação) é associado a um objeto que contém todas as informações necessárias para renderizar a linha desenhada com esse dedo. O programa define uma pequena classe de `FingerPaintPolyline` para essa finalidade:

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new Path();
    }

    public Color Color { set; get; }

    public float StrokeWidth { set; get; }

    public Path Path { private set; get; }
}
```

Cada polilinha tem uma cor, uma largura de traço e um elemento gráfico do Android [`Path`](xref:Android.Graphics.Path) objeto para acumular e renderizar vários pontos da linha à medida que ele está sendo desenhado.

O restante do código mostrado abaixo está contido em uma derivação `View` chamada `FingerPaintCanvasView`. Essa classe mantém um dicionário de objetos do tipo `FingerPaintPolyline` durante o tempo em que eles estão sendo desenhados ativamente por um ou mais dedos:

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

Esse dicionário permite que a exibição Obtenha rapidamente as informações de `FingerPaintPolyline` associadas a um dedo específico.

A classe `FingerPaintCanvasView` também mantém um objeto `List` para as polilinhas que foram concluídas:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Os objetos neste `List` estão na mesma ordem em que foram desenhados.

`FingerPaintCanvasView` substitui dois métodos definidos por `View`: [`OnDraw`](xref:Android.Views.View.OnDraw*)
e [`OnTouchEvent`](xref:Android.Views.View.OnTouchEvent*).
Em sua `OnDraw` substituir, a exibição desenha as polilinhas concluídas e, em seguida, desenha as polilinhas em andamento.

A substituição do método `OnTouchEvent` começa obtendo um valor de `pointerIndex` da propriedade `ActionIndex`. Esse `ActionIndex` valor diferencia entre vários dedos, mas não é consistente entre vários eventos. Por esse motivo, você usa a `pointerIndex` para obter o ponteiro `id` valor do método `GetPointerId`. Essa ID *é* consistente entre vários eventos:

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // Get the pointer index
    int pointerIndex = args.ActionIndex;

    // Get the id to identify a finger over the course of its progress
    int id = args.GetPointerId(pointerIndex);

    // Use ActionMasked here rather than Action to reduce the number of possibilities
    switch (args.ActionMasked)
    {
        // ...
    }

    // Invalidate to update the view
    Invalidate();

    // Request continued touch input
    return true;
}
```

Observe que a substituição usa a propriedade `ActionMasked` na instrução `switch` em vez da propriedade `Action`. Eis o porquê:

Quando você está lidando com o multitoque, a propriedade `Action` tem um valor de `MotionEventsAction.Down` para o primeiro dedo tocar na tela e os valores de `Pointer2Down` e `Pointer3Down` como o segundo e o terceiro dedos também tocam na tela. Como o quarto e o quinto dedos fazem contato, a propriedade `Action` tem valores numéricos que nem mesmo correspondem aos membros da enumeração `MotionEventsAction`! Você precisará examinar os valores dos sinalizadores de bits nos valores para interpretar o que significam.

Da mesma forma, como os dedos deixam contato com a tela, a propriedade `Action` tem valores de `Pointer2Up` e `Pointer3Up` para o segundo e o terceiro dedos e `Up` para o primeiro dedo.

A propriedade `ActionMasked` usa um número menor de valores porque ela deve ser usada em conjunto com a propriedade `ActionIndex` para diferenciar entre vários dedos. Quando os dedos tocam na tela, a propriedade só pode ser igual `MotionEventActions.Down` para o primeiro dedo e `PointerDown` para os dedos subsequentes. Como os dedos saem da tela, `ActionMasked` tem valores de `Pointer1Up` para os dedos subseqüentes e `Up` para o primeiro dedo.

Ao usar `ActionMasked`, o `ActionIndex` distingue entre os dedos subsequentes para tocar e sair da tela, mas normalmente não é necessário usar esse valor, exceto como um argumento para outros métodos no objeto `MotionEvent`. Para multitoque, um dos métodos mais importantes é `GetPointerId` chamado no código acima. Esse método retorna um valor que você pode usar para uma chave de dicionário para associar eventos específicos a dedos.

A substituição de `OnTouchEvent` no programa [FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) processa os `MotionEventActions.Down` e `PointerDown` eventos de forma idêntica ao criar um novo objeto `FingerPaintPolyline` e adicioná-lo ao dicionário:

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // Get the pointer index
    int pointerIndex = args.ActionIndex;

    // Get the id to identify a finger over the course of its progress
    int id = args.GetPointerId(pointerIndex);

    // Use ActionMasked here rather than Action to reduce the number of possibilities
    switch (args.ActionMasked)
    {
        case MotionEventActions.Down:
        case MotionEventActions.PointerDown:

            // Create a Polyline, set the initial point, and store it
            FingerPaintPolyline polyline = new FingerPaintPolyline
            {
                Color = StrokeColor,
                StrokeWidth = StrokeWidth
            };

            polyline.Path.MoveTo(args.GetX(pointerIndex),
                                 args.GetY(pointerIndex));

            inProgressPolylines.Add(id, polyline);
            break;
        // ...
    }
    // ...        
}
```

Observe que a `pointerIndex` também é usada para obter a posição do dedo dentro da exibição. Todas as informações de toque estão associadas ao valor de `pointerIndex`. O `id` identifica exclusivamente os dedos em várias mensagens, portanto, é usado para criar a entrada do dicionário.

Da mesma forma, a substituição de `OnTouchEvent` também manipula o `MotionEventActions.Up` e `Pointer1Up` de maneira idêntica ao transferir a polilinha concluída para a coleção de `completedPolylines` para que elas possam ser desenhadas durante a substituição de `OnDraw`. O código também remove a entrada `id` do dicionário:

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // ...
    switch (args.ActionMasked)
    {
        // ...
        case MotionEventActions.Up:
        case MotionEventActions.Pointer1Up:

            inProgressPolylines[id].Path.LineTo(args.GetX(pointerIndex),
                                                args.GetY(pointerIndex));

            // Transfer the in-progress polyline to a completed polyline
            completedPolylines.Add(inProgressPolylines[id]);
            inProgressPolylines.Remove(id);
            break;

        case MotionEventActions.Cancel:
            inProgressPolylines.Remove(id);
            break;
    }
    // ...        
}
```

Agora, para a parte complicada.

Entre os eventos down e up, geralmente há muitos eventos de `MotionEventActions.Move`. Eles são agrupados em uma única chamada para `OnTouchEvent`e devem ser tratados de forma diferente dos eventos `Down` e `Up`. O valor de `pointerIndex` obtido anteriormente da propriedade `ActionIndex` deve ser ignorado. Em vez disso, o método deve obter vários valores de `pointerIndex` fazendo um loop entre 0 e a propriedade `PointerCount` e, em seguida, obter uma `id` para cada um desses valores de `pointerIndex`:

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // ...
    switch (args.ActionMasked)
    {
        // ...
        case MotionEventActions.Move:

            // Multiple Move events are bundled, so handle them differently
            for (pointerIndex = 0; pointerIndex < args.PointerCount; pointerIndex++)
            {
                id = args.GetPointerId(pointerIndex);

                inProgressPolylines[id].Path.LineTo(args.GetX(pointerIndex),
                                                    args.GetY(pointerIndex));
            }
            break;
        // ...
    }
    // ...        
}
```

Esse tipo de processamento permite que o programa [FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) acompanhe os dedos individuais e desenhe os resultados na tela:

[![exemplo de captura de tela do exemplo de FingerPaint](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

Agora você viu como é possível acompanhar os dedos individuais na tela e fazer distinção entre eles.

## <a name="related-links"></a>Links relacionados

- [Guia equivalente do Xamarin iOS](~/ios/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)
