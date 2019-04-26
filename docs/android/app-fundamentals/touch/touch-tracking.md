---
title: Dedo multitoque acompanhamento no xamarin. Android
description: Este tópico demonstra como controlar os eventos de toque de vários dedos
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 34a9d2d9b8acb05a1b978a70e85038507032faaa
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61011774"
---
# <a name="multi-touch-finger-tracking"></a>Acompanhamento de multitoque dedo

_Este tópico demonstra como controlar os eventos de toque de vários dedos_

Há vezes quando um aplicativo multitoque necessárias para acompanhar os dedos individuais quando eles passam simultaneamente na tela. Um aplicativo típico é um programa de finger-paint. Você deseja que o usuário seja capaz de desenhar com um único dedo, mas também para desenhar com vários dedos, ao mesmo tempo. Como seu programa processa vários eventos de toque, ele precisa distinguir os eventos que correspondem a cada dedo. Android fornece um código de ID para essa finalidade, mas a obtenção e tratamento de que o código podem ser um pouco complicados.

Todos os eventos associados com um dedo específico, o código de ID permanece a mesma. O código de identificação é atribuído quando o primeiro um dedo toca na tela e torna-se inválidos depois que o dedo levanta na tela.
Esses códigos de identificação são inteiros geralmente muito pequenos e Android reutiliza-os para eventos de toque posteriores.

Quase sempre, um programa que acompanha os dedos individuais mantém um dicionário para acompanhamento de toque. A chave de dicionário é o código de ID que identifica um dedo específico. O valor do dicionário depende do aplicativo. No [pintura a dedo](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint) programa, cada traço do dedo (desde toque a versão) é associado um objeto que contém todas as informações necessárias para renderizar a linha desenhada desse dedo. O programa define um pequeno `FingerPaintPolyline` classe para essa finalidade:

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

Cada polilinha tem uma cor, uma largura de traço e um Android gráficos [ `Path` ](https://developer.xamarin.com/api/type/Android.Graphics.Path/) objeto acumular e renderizar os vários pontos da linha conforme ela é desenhada.

O restante do código mostrado a seguir está contido em uma `View` derivativo chamado `FingerPaintCanvasView`. Se a classe mantém um dicionário de objetos do tipo `FingerPaintPolyline` durante a hora em que eles estão sendo desenhados ativamente por um ou mais dedos:

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

Esse dicionário permite que o modo de exibição obter rapidamente o `FingerPaintPolyline` informações associadas com um dedo específico.

O `FingerPaintCanvasView` classe também mantém um `List` objeto para as polilinhas que foram concluídas:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Os objetos nesta `List` estão na mesma ordem que foram desenhadas.

`FingerPaintCanvasView` substitui os dois métodos definidos pelo `View`: [`OnDraw`](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/p/Android.Graphics.Canvas/)
e [ `OnTouchEvent` ](https://developer.xamarin.com/api/member/Android.Views.View.OnTouchEvent/p/Android.Views.MotionEvent/).
No seu `OnDraw` substituição, o modo de exibição desenha as polilinhas concluídas e, em seguida, desenha as polilinhas em andamento.

A substituição do `OnTouchEvent` método começa obtendo uma `pointerIndex` o valor do `ActionIndex` propriedade. Isso `ActionIndex` valor faz distinção entre vários dedos, mas ele não seja consistente em vários eventos. Por esse motivo, você deve usar o `pointerIndex` para obter o ponteiro `id` o valor do `GetPointerId` método. Essa identificação *é* consistente entre vários eventos:

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

Observe que a substituição usa a `ActionMasked` propriedade no `switch` instrução em vez de `Action` propriedade. Eis o porquê:

Quando você está lidando com multitoque, a `Action` propriedade tem um valor de `MotionEventsAction.Down` para o primeiro dedo a tocar a tela e, em seguida, valores de `Pointer2Down` e `Pointer3Down` conforme os dedos segundo e terceiro também toque na tela. Conforme os dedos quarto e quintas faz contato, o `Action` propriedade tem valores numéricos que ainda não correspondem aos membros do `MotionEventsAction` enumeração! Você precisa examinar os valores dos sinalizadores de bits nos valores de interpretar o que significam.

Da mesma forma, conforme os dedos saem contato com a tela, o `Action` propriedade tem valores de `Pointer2Up` e `Pointer3Up` para os dedos segundo e terceiro, e `Up` para primeiro dedo a tocar.

O `ActionMasked` propriedade assume um número menor número de valores, porque ele foi projetado ser usado em conjunto com o `ActionIndex` propriedade diferenciar entre vários dedos. Quando os dedos tocam a tela, a propriedade só pode ser igual `MotionEventActions.Down` para a primeiro dedo a tocar e `PointerDown` para dedos subsequentes. Conforme os dedos saem da tela, `ActionMasked` tem valores de `Pointer1Up` para os dedos subsequentes e `Up` para primeiro dedo a tocar.

Ao usar `ActionMasked`, o `ActionIndex` distingue entre os dedos subsequentes para toque e deixe a tela, mas você normalmente não precisa usar esse valor, exceto como um argumento para outros métodos de `MotionEvent` objeto. Multitoque, um dos mais importantes desses métodos é `GetPointerId` chamado no código acima. Que método retorna um valor que você pode usar para uma chave de dicionário para associar eventos em particular a dedos.

O `OnTouchEvent` substituir em de [pintura a dedo](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint) processos de programa a `MotionEventActions.Down` e `PointerDown` eventos de forma idêntica, criando um novo `FingerPaintPolyline` objeto e adicioná-lo ao dicionário:

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

Observe que o `pointerIndex` também é usado para obter a posição do dedo dentro da exibição. Todas as informações de contato são associadas com o `pointerIndex` valor. O `id` identifica exclusivamente dedos em várias mensagens, para que o usado para criar a entrada de dicionário do.

Da mesma forma, o `OnTouchEvent` também substituir identificadores a `MotionEventActions.Up` e `Pointer1Up` identicamente transferindo polilinha concluída para o `completedPolylines` coleção, para que eles podem ser desenhados durante o `OnDraw` substituir. O código também remove o `id` entrada do dicionário:

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

Entre a busca e eventos, geralmente há muitos `MotionEventActions.Move` eventos. Eles são agrupados em uma única chamada para `OnTouchEvent`, e devem ser tratados de maneira diferente do `Down` e `Up` eventos. O `pointerIndex` valor obtido anteriormente o `ActionIndex` propriedade deve ser ignorada. Em vez disso, o método deve obter vários `pointerIndex` valores fazendo um loop entre 0 e o `PointerCount` propriedade e, em seguida, obter uma `id` para cada um desses `pointerIndex` valores:

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

Esse tipo de processamento a permite que o [pintura a dedo](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint) programa para acompanhar os dedos individuais e desenhar os resultados na tela:

[![Captura de tela de exemplo do exemplo de pintura a dedo](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

Agora você já viu como controle de dedos individuais na tela e distinguir entre elas.


## <a name="related-links"></a>Links relacionados

- [Guia do Xamarin iOS equivalentes](~/ios/app-fundamentals/touch/touch-tracking.md)
- [Pintura a dedo (amostra)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)
