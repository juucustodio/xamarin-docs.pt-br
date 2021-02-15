---
title: Acompanhamento de dedos multitoque no Xamarin. Android
description: Este tópico demonstra como controlar eventos de toque de vários dedos
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 8c2e06d2700cd7b61c16fc993d807ca4d042a063
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455112"
---
# <a name="multi-touch-finger-tracking"></a>Acompanhamento de dedos multitoque

_Este tópico demonstra como controlar eventos de toque de vários dedos_

Há ocasiões em que um aplicativo multitoque precisa acompanhar os dedos individuais à medida que eles se movem simultaneamente na tela. Um aplicativo típico é um programa de pintura a dedos. Você deseja que o usuário seja capaz de desenhar com um único dedo, mas também desenhar com vários dedos de uma só vez. À medida que o programa processa vários eventos de toque, ele precisa distinguir quais eventos correspondem a cada dedo. O Android fornece um código de ID para essa finalidade, mas obter e manipular esse código pode ser um pouco complicado.

Para todos os eventos associados a um dedo específico, o código de ID permanece o mesmo. O código de ID é atribuído quando um dedo entra na tela pela primeira vez e se torna inválido depois que o dedo é levantado da tela.
Esses códigos de ID geralmente são inteiros muito pequenos e o Android os reutiliza para eventos de toque posteriores.

Quase sempre, um programa que controla os dedos individuais mantém um dicionário para acompanhamento de toque. A chave de dicionário é o código de ID que identifica um dedo específico. O valor do dicionário depende do aplicativo. No programa [FingerPaint](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) , cada traço de dedo (de toque para liberação) é associado a um objeto que contém todas as informações necessárias para renderizar a linha desenhada com esse dedo. O programa define uma `FingerPaintPolyline` classe pequena para essa finalidade:

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

Cada polilinha tem uma cor, uma largura de traço e um objeto de gráfico do Android [`Path`](xref:Android.Graphics.Path) para acumular e renderizar vários pontos da linha à medida que ele está sendo desenhado.

O restante do código mostrado abaixo está contido em um `View` nome derivado `FingerPaintCanvasView` . Essa classe mantém um dicionário de objetos do tipo `FingerPaintPolyline` durante o tempo que estão sendo desenhados ativamente por um ou mais dedos:

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

Esse dicionário permite que a exibição Obtenha rapidamente as `FingerPaintPolyline` informações associadas a um dedo específico.

A `FingerPaintCanvasView` classe também mantém um `List` objeto para as polilinhas que foram concluídas:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Os objetos nele `List` estão na mesma ordem em que foram desenhados.

`FingerPaintCanvasView` Substitui dois métodos definidos por `View` : [`OnDraw`](xref:Android.Views.View.OnDraw*)
e [`OnTouchEvent`](xref:Android.Views.View.OnTouchEvent*).
Em sua `OnDraw` substituição, a exibição desenha as polilinhas concluídas e, em seguida, desenha as polilinhas em andamento.

A substituição do `OnTouchEvent` método começa obtendo um `pointerIndex` valor da `ActionIndex` propriedade. Esse `ActionIndex` valor diferencia entre vários dedos, mas não é consistente entre vários eventos. Por esse motivo, você usa o `pointerIndex` para obter o valor do ponteiro `id` do `GetPointerId` método. Essa ID *é* consistente entre vários eventos:

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

Observe que a substituição usa a `ActionMasked` Propriedade na `switch` instrução em vez da `Action` propriedade. Eis o motivo:

Quando você está lidando com o multitoque, a `Action` propriedade tem um valor de `MotionEventsAction.Down` para o primeiro dedo tocar na tela e os valores de `Pointer2Down` e `Pointer3Down` como o segundo e o terceiro dedos também tocam na tela. Como o quarto e o quinto dedos fazem contato, a `Action` propriedade tem valores numéricos que nem mesmo correspondem aos membros da `MotionEventsAction` enumeração! Você precisará examinar os valores dos sinalizadores de bits nos valores para interpretar o que significam.

Da mesma forma, como os dedos deixam contato com a tela, a `Action` propriedade tem valores de `Pointer2Up` e `Pointer3Up` para o segundo e o terceiro dedos e `Up` para o primeiro dedo.

A `ActionMasked` propriedade usa um número menor de valores porque ela deve ser usada em conjunto com a `ActionIndex` propriedade para diferenciar entre vários dedos. Quando os dedos tocam na tela, a propriedade só pode ser igual ao `MotionEventActions.Down` primeiro dedo e `PointerDown` para os dedos subsequentes. Como os dedos saem da tela, `ActionMasked` tem valores de `Pointer1Up` para os dedos subsequentes e `Up` para o primeiro dedo.

Ao usar o `ActionMasked` , o faz `ActionIndex` distinção entre os dedos subsequentes para tocar e sair da tela, mas normalmente não é necessário usar esse valor, exceto como um argumento para outros métodos no `MotionEvent` objeto. Para o multitoque, um dos mais importantes desses métodos é `GetPointerId` chamado no código acima. Esse método retorna um valor que você pode usar para uma chave de dicionário para associar eventos específicos a dedos.

A `OnTouchEvent` substituição no programa [FingerPaint](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) processa os `MotionEventActions.Down` eventos e de `PointerDown` forma idêntica, criando um novo `FingerPaintPolyline` objeto e adicionando-o ao dicionário:

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

Observe que o `pointerIndex` também é usado para obter a posição do dedo dentro da exibição. Todas as informações de toque estão associadas ao `pointerIndex` valor. O `id` identifica exclusivamente os dedos em várias mensagens, portanto, é usado para criar a entrada do dicionário.

Da mesma forma, a `OnTouchEvent` substituição também manipula o `MotionEventActions.Up` e, de maneira `Pointer1Up` idêntica, transferindo a polilinha concluída para a `completedPolylines` coleção para que ela possa ser desenhada durante a `OnDraw` substituição. O código também remove a `id` entrada do dicionário:

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

Entre os eventos down e up, geralmente há muitos `MotionEventActions.Move` eventos. Eles são agrupados em uma única chamada para `OnTouchEvent` e devem ser tratados de forma diferente dos `Down` eventos e `Up` . O `pointerIndex` valor obtido anteriormente da `ActionIndex` propriedade deve ser ignorado. Em vez disso, o método deve obter vários `pointerIndex` valores fazendo um loop entre 0 e a `PointerCount` propriedade e, em seguida, obter um `id` para cada um desses `pointerIndex` valores:

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

Esse tipo de processamento permite que o programa [FingerPaint](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) acompanhe os dedos individuais e desenhe os resultados na tela:

[![Exemplo de captura de tela do exemplo de FingerPaint](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

Agora você viu como é possível acompanhar os dedos individuais na tela e fazer distinção entre eles.

## <a name="related-links"></a>Links Relacionados

- [Guia equivalente do Xamarin iOS](~/ios/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint (exemplo)](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)