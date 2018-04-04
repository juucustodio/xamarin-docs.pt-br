---
title: Controle de multitoque dedo
description: Este tópico demonstra como controlar os eventos de toque de vários dedos
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 9c0206de17e0c60803252328ff0398cee0997dbb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="multi-touch-finger-tracking"></a>Controle de multitoque dedo

_Este tópico demonstra como controlar os eventos de toque de vários dedos_

Há ocasiões em que um aplicativo de multitoque precisa acompanhar dedos individuais conforme se move simultaneamente na tela. Um aplicativo típico é um programa de finger-paint. Você deseja que o usuário possa desenhar com um único dedo, mas também para desenhar punho vários ao mesmo tempo. Como o programa processa vários eventos de toque, ela precisa distinguir os eventos que correspondem a cada dedo. Android fornece um código de ID para essa finalidade, mas Obtendo e tratamento de que o código podem ser um pouco confuso.

Para todos os eventos associados a um determinado dedo, o código de identificação permanece o mesmo. O código de identificação é atribuído ao primeiro um dedo toca na tela e torna-se inválido após levanta o dedo na tela.
Esses códigos de identificação são geralmente muito pequenos inteiros e Android reutiliza para eventos de toque posteriores.

Quase sempre, um programa que controla os dedos individuais mantém um dicionário para controle de toque. A chave de dicionário é o código de ID que identifica um determinado dedo. O valor do dicionário depende do aplicativo. No [pintura a dedo](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint) programa, cada traço dedo (de toque para liberar) é associado um objeto que contém todas as informações necessárias para processar a linha desenhada com esse dedo. O programa define um pequeno `FingerPaintPolyline` classe para essa finalidade:

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

Cada polilinha tem uma cor, a largura do traço e um gráfico Android [ `Path` ](https://developer.xamarin.com/api/type/Android.Graphics.Path/) objeto acumular e processar vários pontos da linha como ela é desenhada.

O restante do código mostrado a seguir está contido em um `View` derivada denominada `FingerPaintCanvasView`. Classe mantém um dicionário de objetos do tipo `FingerPaintPolyline` durante o tempo que ativamente estão sendo desenhadas por um ou mais dedos:

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

Esse dicionário permite que o modo de exibição obter rapidamente o `FingerPaintPolyline` informações associadas a um determinado dedo.

O `FingerPaintCanvasView` classe também mantém um `List` objeto para os polilinhas que foram concluídas:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Os objetos nesta `List` estão na mesma ordem em que foram feitos.

`FingerPaintCanvasView` substitui os dois métodos definidos por `View`: [ `OnDraw` ](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/p/Android.Graphics.Canvas/) e [ `OnTouchEvent` ](https://developer.xamarin.com/api/member/Android.Views.View.OnTouchEvent/p/Android.Views.MotionEvent/).
No seu `OnDraw` substituição, o modo de exibição desenha as polilinhas concluídas e, em seguida, usa as polilinhas em andamento.

A substituição do `OnTouchEvent` método começa obtendo uma `pointerIndex` valor o `ActionIndex` propriedade. Isso `ActionIndex` valor diferencia entre vários dedos, mas ele não seja consistente em vários eventos. Por esse motivo, você deve usar o `pointerIndex` para obter o ponteiro `id` valor o `GetPointerId` método. Essa ID *é* consistente entre vários eventos:

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

Observe que a substituição usa a `ActionMasked` propriedade o `switch` instrução em vez de `Action` propriedade. Eis o porquê:

Quando você está lidando com multitoque, o `Action` propriedade tem um valor de `MotionEventsAction.Down` para o primeiro dedo tocar a tela e, em seguida, os valores de `Pointer2Down` e `Pointer3Down` como os segundo e terceiro dedos também toque na tela. Como os quarto e quinto dedos fazer contato, o `Action` propriedade tem valores numéricos que ainda não correspondem aos membros do `MotionEventsAction` enumeração! Você precisa examinar os valores de sinalizadores de bit de valores para interpretar o que significam.

Da mesma forma, como os dedos deixam o contato com a tela de `Action` propriedade tem valores de `Pointer2Up` e `Pointer3Up` para os segundo e terceiro dedos, e `Up` para o dedo indicador.

O `ActionMasked` propriedade assume um menor número de valores porque ele tem se destina a ser usado em conjunto com o `ActionIndex` propriedade para diferenciar vários dedos. Quando dedos toque na tela, a propriedade somente pode ser igual `MotionEventActions.Down` para o dedo indicador e `PointerDown` para dedos subsequentes. Como os dedos saia da tela, `ActionMasked` tem valores de `Pointer1Up` para os dedos subsequentes e `Up` para o dedo indicador.

Ao usar `ActionMasked`, o `ActionIndex` faz distinção entre os dedos subsequentes para toque e deixe a tela, mas normalmente você não precisa usar esse valor, exceto como um argumento para outros métodos de `MotionEvent` objeto. Para multitoque, um dos mais importantes desses métodos é `GetPointerId` chamado no código acima. Que método retorna um valor que você pode usar uma chave de dicionário para associar a eventos específicos para dedos.

O `OnTouchEvent` substituir no [pintura a dedo](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint) processos de programa a `MotionEventActions.Down` e `PointerDown` eventos de forma idêntica, criando um novo `FingerPaintPolyline` objeto e adicioná-la ao dicionário:

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

Observe que o `pointerIndex` também é usado para obter a posição do dedo dentro da exibição. Todas as informações de contato estão associadas a `pointerIndex` valor. O `id` identifica exclusivamente dedos em várias mensagens, para que o do usado para criar a entrada de dicionário.

Da mesma forma, o `OnTouchEvent` também substituir identificadores de `MotionEventActions.Up` e `Pointer1Up` identicamente por meio da transferência de polilinha concluída para o `completedPolylines` coleção, para que eles podem ser desenhados durante o `OnDraw` substituir. O código também remove o `id` entrada do dicionário:

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

Agora para a parte complicada.

Entre o suspenso e eventos, geralmente há muitos `MotionEventActions.Move` eventos. Elas serão agrupadas em uma única chamada para `OnTouchEvent`, e devem ser tratados de forma diferente de `Down` e `Up` eventos. O `pointerIndex` valor obtido anteriormente do `ActionIndex` propriedade deve ser ignorada. Em vez disso, o método deve obter vários `pointerIndex` valores por loop entre 0 e o `PointerCount` propriedade e, em seguida, obtenha um `id` para cada um deles `pointerIndex` valores:

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

Esse tipo de processamento permite o [pintura a dedo](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint) programa para controlar os dedos individuais e desenhar os resultados na tela:

[![Captura de tela de exemplo do exemplo de pintura a dedo](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

Agora você viu como você pode controlar individuais dedos na tela e distinguir entre eles.


## <a name="related-links"></a>Links relacionados

- [Guia de iOS equivalente do Xamarin](~/ios/app-fundamentals/touch/touch-tracking.md)
- [Pintura a dedo (exemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)
