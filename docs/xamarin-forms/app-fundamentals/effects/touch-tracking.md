---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 892bffa4027a1a61d6c22cc26d1556fb007432d8
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136967"
---
# <a name="invoking-events-from-effects"></a>Invocando eventos por meio de efeitos

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)

_Um efeito pode definir e invocar um evento, sinalizando alterações na exibição nativa subjacente. Este artigo mostra como implementar o acompanhamento de dedos com vários toques de nível baixo e como gerar eventos que sinalizam atividade de toque._

O efeito descrito neste artigo fornece acesso aos eventos de toque de nível baixo. Esses eventos de nível baixo não estão disponíveis por meio das classes `GestureRecognizer` existentes, mas elas são vitais para alguns tipos de aplicativos. Por exemplo, um aplicativo de pintura a dedo precisa acompanhar dedos individuais à medida que eles se movem na tela. Um teclado musical precisa detectar toques e liberações em teclas individuais, bem como o deslize de um dedo de uma tecla a outra em um glissando.

Um efeito é ideal para o acompanhamento de dedos multitoque porque ele pode ser anexado a qualquer Xamarin.Forms elemento.

## <a name="platform-touch-events"></a>Eventos de toque da plataforma

O iOS, o Android e a Plataforma Universal do Windows incluem uma API de nível baixo que permite que os aplicativos detectem a atividade de toque. Essas plataformas todas distinguem entre três tipos básicos de eventos de toque:

- *Pressionado*, quando um dedo toca a tela
- *Movido*, quando um dedo que toca a tela se move
- *Liberado*, quando o dedo é liberado da tela

Em um ambiente multitoque, vários dedos podem tocar a tela ao mesmo tempo. As várias plataformas incluem um número de ID (identificação) que os aplicativos podem usar para distinguir entre vários dedos.

No iOS, a classe `UIView` define três métodos substituíveis, `TouchesBegan`, `TouchesMoved` e `TouchesEnded`, correspondentes a esses três eventos básicos. O artigo [Acompanhamento de dedos multitoque](~/ios/app-fundamentals/touch/touch-tracking.md) descreve como usar esses métodos. No entanto, um programa do iOS não precisa substituir uma classe que deriva de `UIView` para usar esses métodos. O `UIGestureRecognizer` do iOS também define esses mesmos três métodos, sendo que você pode anexar uma instância de uma classe que deriva de `UIGestureRecognizer` a qualquer objeto `UIView`.

No Android, a classe `View` define um método substituível chamado `OnTouchEvent` para processar todas as atividades de toque. O tipo da atividade de toque é definido pelos membros de enumeração `Down`, `PointerDown`, `Move`, `Up` e `PointerUp`, conforme descrito no artigo [Acompanhamento de dedos multitoque](~/android/app-fundamentals/touch/touch-tracking.md). O `View` do Android também define um evento chamado `Touch` que permite que um manipulador de eventos seja anexado a qualquer objeto `View`.

No UWP (Plataforma Universal do Windows), a classe `UIElement` define eventos chamados `PointerPressed`, `PointerMoved` e `PointerReleased`. Eles são descritos no artigo [Manipular a entrada de ponteiro no MSDN](/windows/uwp/input-and-devices/handle-pointer-input/) e na documentação da API para a classe [`UIElement`](/uwp/api/windows.ui.xaml.uielement/).

A API do `Pointer` na Plataforma Universal do Windows se destina a unificar o mouse, o toque e a entrada à caneta. Por esse motivo, o evento `PointerMoved` é invocado quando o cursor do mouse passa sobre um elemento, mesmo que o botão do mouse não seja pressionado. O objeto `PointerRoutedEventArgs` que acompanha esses eventos tem uma propriedade chamada `Pointer` que tem uma propriedade chamada `IsInContact`, indicando se um botão do mouse é pressionado ou um dedo está em contato com a tela.

Além disso, o UWP define mais dois eventos chamados `PointerEntered` e `PointerExited`. Eles indicam quando um mouse ou o dedo e move de um elemento para outro. Por exemplo, considere dois elementos adjacentes, chamados A e B. Ambos os elementos têm manipuladores instalados para os eventos de ponteiro. Quando um dedo pressiona A, o evento `PointerPressed` é invocado. Conforme o dedo se move, A invoca eventos `PointerMoved`. Se o dedo se move de A para B, A invoca um evento `PointerExited` e B invoca um evento `PointerEntered`. Se o dedo é então liberado, B invoca um evento `PointerReleased`.

As plataformas iOS e Android são diferentes do UWP: a exibição que primeiro obtém a chamada a `TouchesBegan` ou `OnTouchEvent` quando um dedo toca a exibição continua obtendo todas as atividades de toque, mesmo se o dedo se move para diferentes exibições. O UWP pode se comportar da mesma forma se o aplicativo captura o ponteiro: no manipulador de eventos `PointerEntered`, o elemento chama `CapturePointer` e, em seguida, obtém todas as atividades de toque desse dedo.

A abordagem do UWP prova ser muito útil para alguns tipos de aplicativos, por exemplo, um teclado musical. Cada tecla pode manipular os eventos de toque dessa tecla e detectar quando um dedo deslizou de uma tecla para outra usando os eventos `PointerEntered` e `PointerExited`.

Por esse motivo, o efeito do acompanhamento de toque descrito neste artigo implementa a abordagem do UWP.

## <a name="the-touch-tracking-effect-api"></a>A API de efeito do acompanhamento de toque

A amostra [**Demonstrações do efeito de acompanhamento de toque**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/) contém as classes (e uma enumeração) que implementam o acompanhamento de toque de nível baixo. Esses tipos pertencem ao namespace `TouchTracking` e começam com a palavra `Touch`. O projeto **TouchTrackingEffectDemos** da biblioteca do .NET Standard inclui a enumeração `TouchActionType` para o tipo de eventos de toque:

```csharp
public enum TouchActionType
{
    Entered,
    Pressed,
    Moved,
    Released,
    Exited,
    Cancelled
}
```

Todas as plataformas também incluem um evento que indica que o evento de toque foi cancelado.

A classe `TouchEffect` na biblioteca do .NET Standard deriva de `RoutingEffect` e define um evento chamado `TouchAction` e um método chamado `OnTouchAction` que invoca o evento `TouchAction`:

```csharp
public class TouchEffect : RoutingEffect
{
    public event TouchActionEventHandler TouchAction;

    public TouchEffect() : base("XamarinDocs.TouchEffect")
    {
    }

    public bool Capture { set; get; }

    public void OnTouchAction(Element element, TouchActionEventArgs args)
    {
        TouchAction?.Invoke(element, args);
    }
}
```

Observe também a propriedade `Capture`. Para capturar eventos de toque, um aplicativo precisa definir essa propriedade como `true` anterior a um evento `Pressed`. Caso contrário, os eventos de toque se comportam como aqueles mostrados na Plataforma Universal do Windows.

A classe `TouchActionEventArgs` na biblioteca do .NET Standard contém todas as informações que acompanham cada evento:

```csharp
public class TouchActionEventArgs : EventArgs
{
    public TouchActionEventArgs(long id, TouchActionType type, Point location, bool isInContact)
    {
        Id = id;
        Type = type;
        Location = location;
        IsInContact = isInContact;
    }

    public long Id { private set; get; }

    public TouchActionType Type { private set; get; }

    public Point Location { private set; get; }

    public bool IsInContact { private set; get; }
}
```

Um aplicativo pode usar a propriedade `Id` para acompanhar dedos individuais. Observe a propriedade `IsInContact`. Essa propriedade é sempre `true` para eventos `Pressed` e `false` para eventos `Released`. Ela também é sempre `true` para eventos `Moved` no iOS e no Android. A propriedade `IsInContact` pode ser `false` para eventos `Moved` na Plataforma Universal do Windows quando o programa está em execução na área de trabalho e o ponteiro do mouse se move sem um botão pressionado.

Você pode usar a `TouchEffect` classe em seus próprios aplicativos, incluindo o arquivo no projeto de biblioteca de .net Standard da solução e adicionando uma instância à `Effects` coleção de qualquer Xamarin.Forms elemento. Anexe um manipulador ao evento `TouchAction` para obter os eventos de toque.

Para usar `TouchEffect` em seu próprio aplicativo, você também precisará das implementações de plataforma incluídas na solução **TouchTrackingEffectDemos**.

## <a name="the-touch-tracking-effect-implementations"></a>As implementações do efeito de acompanhamento de toque

As implementações do iOS, do Android e do UWP do `TouchEffect` são descritas abaixo começando com a implementação mais simples (UWP) e terminando com a implementação do iOS, porque ela é estruturalmente mais complexa do que as outras.

### <a name="the-uwp-implementation"></a>A implementação do UWP

A implementação do UWP do `TouchEffect` é a mais simples. Como de costume, a classe deriva de `PlatformEffect` e inclui dois atributos de assembly:

```csharp
[assembly: ResolutionGroupName("XamarinDocs")]
[assembly: ExportEffect(typeof(TouchTracking.UWP.TouchEffect), "TouchEffect")]

namespace TouchTracking.UWP
{
    public class TouchEffect : PlatformEffect
    {
        ...
    }
}
```

A substituição `OnAttached` salva algumas informações como campos e anexa manipuladores a todos os eventos de ponteiro:

```csharp
public class TouchEffect : PlatformEffect
{
    FrameworkElement frameworkElement;
    TouchTracking.TouchEffect effect;
    Action<Element, TouchActionEventArgs> onTouchAction;

    protected override void OnAttached()
    {
        // Get the Windows FrameworkElement corresponding to the Element that the effect is attached to
        frameworkElement = Control == null ? Container : Control;

        // Get access to the TouchEffect class in the .NET Standard library
        effect = (TouchTracking.TouchEffect)Element.Effects.
                    FirstOrDefault(e => e is TouchTracking.TouchEffect);

        if (effect != null && frameworkElement != null)
        {
            // Save the method to call on touch events
            onTouchAction = effect.OnTouchAction;

            // Set event handlers on FrameworkElement
            frameworkElement.PointerEntered += OnPointerEntered;
            frameworkElement.PointerPressed += OnPointerPressed;
            frameworkElement.PointerMoved += OnPointerMoved;
            frameworkElement.PointerReleased += OnPointerReleased;
            frameworkElement.PointerExited += OnPointerExited;
            frameworkElement.PointerCanceled += OnPointerCancelled;
        }
    }
    ...
}    
```

O manipulador `OnPointerPressed` invoca o evento de efeito chamando o campo `onTouchAction` no método `CommonHandler`:

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    void OnPointerPressed(object sender, PointerRoutedEventArgs args)
    {
        CommonHandler(sender, TouchActionType.Pressed, args);

        // Check setting of Capture property
        if (effect.Capture)
        {
            (sender as FrameworkElement).CapturePointer(args.Pointer);
        }
    }
    ...
    void CommonHandler(object sender, TouchActionType touchActionType, PointerRoutedEventArgs args)
    {
        PointerPoint pointerPoint = args.GetCurrentPoint(sender as UIElement);
        Windows.Foundation.Point windowsPoint = pointerPoint.Position;  

        onTouchAction(Element, new TouchActionEventArgs(args.Pointer.PointerId,
                                                        touchActionType,
                                                        new Point(windowsPoint.X, windowsPoint.Y),
                                                        args.Pointer.IsInContact));
    }
}
```

`OnPointerPressed` também verifica o valor da propriedade `Capture` na classe de efeito na biblioteca do .NET Standard e chama `CapturePointer` se ele é `true`.

 Os outros manipuladores de eventos do UWP são ainda mais simples:

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    void OnPointerEntered(object sender, PointerRoutedEventArgs args)
    {
        CommonHandler(sender, TouchActionType.Entered, args);
    }
    ...
}
```

### <a name="the-android-implementation"></a>A implementação do Android

As implementações do Android e do iOS são necessariamente mais complexas, porque precisam implementar os eventos `Exited` e `Entered` quando um dedo se move de um elemento para outro. Ambas as implementações são estruturadas de forma semelhante.

A classe `TouchEffect` do Android instala um manipulador para o evento `Touch`:

```csharp
view = Control == null ? Container : Control;
...
view.Touch += OnTouch;
```

A classe também define dois dicionários estáticos:

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    static Dictionary<Android.Views.View, TouchEffect> viewDictionary =
        new Dictionary<Android.Views.View, TouchEffect>();

    static Dictionary<int, TouchEffect> idToEffectDictionary =
        new Dictionary<int, TouchEffect>();
    ...
```

O `viewDictionary` obtém uma nova entrada sempre que a substituição `OnAttached` é chamada:

```csharp
viewDictionary.Add(view, this);
```

A entrada é removida do dicionário em `OnDetached`. Todas as instâncias de `TouchEffect` são associadas a uma exibição específica à qual o efeito é anexado. O dicionário estático permite que qualquer instância `TouchEffect` enumere todas as outras exibições e suas instâncias `TouchEffect` correspondentes. Isso é necessário para permitir a transferência dos eventos de uma exibição para outra.

O Android atribui um código de ID a eventos de toque, que permite que um aplicativo acompanhe dedos individuais. O `idToEffectDictionary` associa esse código de ID a uma instância `TouchEffect`. Um item é adicionado a esse dicionário quando o manipulador `Touch` é chamado para um pressionamento de dedo:

```csharp
void OnTouch(object sender, Android.Views.View.TouchEventArgs args)
{
    ...
    switch (args.Event.ActionMasked)
    {
        case MotionEventActions.Down:
        case MotionEventActions.PointerDown:
            FireEvent(this, id, TouchActionType.Pressed, screenPointerCoords, true);

            idToEffectDictionary.Add(id, this);

            capture = libTouchEffect.Capture;
            break;

```

O item é removido do `idToEffectDictionary` quando o dedo é liberado da tela. O método `FireEvent` apenas acumula todas as informações necessárias para chamar o método `OnTouchAction`:

```csharp
void FireEvent(TouchEffect touchEffect, int id, TouchActionType actionType, Point pointerLocation, bool isInContact)
{
    // Get the method to call for firing events
    Action<Element, TouchActionEventArgs> onTouchAction = touchEffect.libTouchEffect.OnTouchAction;

    // Get the location of the pointer within the view
    touchEffect.view.GetLocationOnScreen(twoIntArray);
    double x = pointerLocation.X - twoIntArray[0];
    double y = pointerLocation.Y - twoIntArray[1];
    Point point = new Point(fromPixels(x), fromPixels(y));

    // Call the method
    onTouchAction(touchEffect.formsElement,
        new TouchActionEventArgs(id, actionType, point, isInContact));
}
```

Todos os outros tipos de toque são processados de duas maneiras diferentes: se a propriedade `Capture` é `true`, o evento de toque é uma tradução muito simples das informações do `TouchEffect`. Isso fica mais complicado quando `Capture` é `false` porque os eventos de toque talvez precisem ser movidos de uma exibição para outra. Essa é a responsabilidade do método `CheckForBoundaryHop`, que é chamado durante eventos de movimentação. Esse método faz uso de ambos os dicionários estáticos. Ele enumera pelo `viewDictionary` para determinar a exibição que o dedo está tocando no momento e, em seguida, ele usa `idToEffectDictionary` para armazenar a instância `TouchEffect` atual (e, portanto, a exibição atual) associado a uma ID específica:

```csharp
void CheckForBoundaryHop(int id, Point pointerLocation)
{
    TouchEffect touchEffectHit = null;

    foreach (Android.Views.View view in viewDictionary.Keys)
    {
        // Get the view rectangle
        try
        {
            view.GetLocationOnScreen(twoIntArray);
        }
        catch // System.ObjectDisposedException: Cannot access a disposed object.
        {
            continue;
        }
        Rectangle viewRect = new Rectangle(twoIntArray[0], twoIntArray[1], view.Width, view.Height);

        if (viewRect.Contains(pointerLocation))
        {
            touchEffectHit = viewDictionary[view];
        }
    }

    if (touchEffectHit != idToEffectDictionary[id])
    {
        if (idToEffectDictionary[id] != null)
        {
            FireEvent(idToEffectDictionary[id], id, TouchActionType.Exited, pointerLocation, true);
        }
        if (touchEffectHit != null)
        {
            FireEvent(touchEffectHit, id, TouchActionType.Entered, pointerLocation, true);
        }
        idToEffectDictionary[id] = touchEffectHit;
    }
}
```

Se há uma alteração no `idToEffectDictionary`, o método potencialmente chama `FireEvent` para `Exited` e `Entered` para a transferência de uma exibição para outra. No entanto, o dedo pode ter sido movido para uma área ocupada por uma exibição sem um `TouchEffect` anexado ou dessa área para uma exibição com o efeito anexado.

Observe que o `try` e o `catch` são bloqueados quando a exibição é acessada. Em uma página na qual o usuário navega e, em seguida, navega novamente para a home page, o método `OnDetached` não é chamado e os itens permanecem no `viewDictionary`, mas o Android os considera descartados.

### <a name="the-ios-implementation"></a>A implementação do iOS

A implementação do iOS é semelhante à implementação do Android, exceto que a classe `TouchEffect` do iOS precisa criar uma instância de um derivado do `UIGestureRecognizer`. Essa é uma classe no projeto do iOS chamada `TouchRecognizer`. Essa classe mantém dois dicionários estáticos que armazenam instâncias `TouchRecognizer`:

```csharp
static Dictionary<UIView, TouchRecognizer> viewDictionary =
    new Dictionary<UIView, TouchRecognizer>();

static Dictionary<long, TouchRecognizer> idToTouchDictionary =
    new Dictionary<long, TouchRecognizer>();
```

Grande parte da estrutura desta classe `TouchRecognizer` é semelhante à classe `TouchEffect` do Android.

> [!IMPORTANT]
> Muitas das exibições na `UIKit` não têm toque habilitado por padrão. O toque pode ser habilitado adicionando `view.UserInteractionEnabled = true;` para substituir `OnAttached` na classe `TouchEffect` no projeto do iOS. Isso deve ocorrer após a obtenção do `UIView`, que corresponde ao elemento ao qual o efeito está anexado.

## <a name="putting-the-touch-effect-to-work"></a>Colocando o efeito de toque para funcionar

O programa [**TouchTrackingEffectDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/) contém cinco páginas que testam o efeito de acompanhamento de toque para tarefas comuns.

A página **Arrastando BoxView** permite que você adicione elementos `BoxView` a um `AbsoluteLayout` e, em seguida, arraste-os na tela. O [arquivo XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/BoxViewDraggingPage.xaml) cria uma instância de duas exibições `Button` para adicionar elementos `BoxView` ao `AbsoluteLayout` e desmarcar o `AbsoluteLayout`.

O método no [arquivo code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/BoxViewDraggingPage.xaml.cs) que adiciona uma nova `BoxView` ao `AbsoluteLayout` também adiciona um objeto `TouchEffect` à `BoxView` e anexa um manipulador de eventos ao efeito:

```csharp
void AddBoxViewToLayout()
{
    BoxView boxView = new BoxView
    {
        WidthRequest = 100,
        HeightRequest = 100,
        Color = new Color(random.NextDouble(),
                          random.NextDouble(),
                          random.NextDouble())
    };

    TouchEffect touchEffect = new TouchEffect();
    touchEffect.TouchAction += OnTouchEffectAction;
    boxView.Effects.Add(touchEffect);
    absoluteLayout.Children.Add(boxView);
}
```

O manipulador de eventos `TouchAction` processa todos os eventos de toque para todos os elementos `BoxView`, mas precisa ter algum cuidado: ele não pode permitir dois dedos em uma única `BoxView` porque o programa implementa apenas a operação de arrastar e os dois dedos interferirão um com o outro. Por esse motivo, a página define uma classe inserida para cada dedo acompanhado no momento:

```csharp
class DragInfo
{
    public DragInfo(long id, Point pressPoint)
    {
        Id = id;
        PressPoint = pressPoint;
    }

    public long Id { private set; get; }

    public Point PressPoint { private set; get; }
}

Dictionary<BoxView, DragInfo> dragDictionary = new Dictionary<BoxView, DragInfo>();
```

O `dragDictionary` contém uma entrada para cada `BoxView` que está sendo arrastado no momento.

A ação de toque `Pressed` adiciona um item a esse dicionário e a ação `Released` remove-o. A lógica `Pressed` precisa verificar se já há um item no dicionário para esse `BoxView`. Nesse caso, a `BoxView` já está sendo arrastada e o novo evento é um segundo dedo sobre essa mesma `BoxView`. Para as ações `Moved` e `Released`, o manipulador de eventos precisa verificar se o dicionário tem uma entrada para essa `BoxView` e se a propriedade `Id` do toque para essa `BoxView` arrastada corresponde à entrada do dicionário:

```csharp
void OnTouchEffectAction(object sender, TouchActionEventArgs args)
{
    BoxView boxView = sender as BoxView;

    switch (args.Type)
    {
        case TouchActionType.Pressed:
            // Don't allow a second touch on an already touched BoxView
            if (!dragDictionary.ContainsKey(boxView))
            {
                dragDictionary.Add(boxView, new DragInfo(args.Id, args.Location));

                // Set Capture property to true
                TouchEffect touchEffect = (TouchEffect)boxView.Effects.FirstOrDefault(e => e is TouchEffect);
                touchEffect.Capture = true;
            }
            break;

        case TouchActionType.Moved:
            if (dragDictionary.ContainsKey(boxView) && dragDictionary[boxView].Id == args.Id)
            {
                Rectangle rect = AbsoluteLayout.GetLayoutBounds(boxView);
                Point initialLocation = dragDictionary[boxView].PressPoint;
                rect.X += args.Location.X - initialLocation.X;
                rect.Y += args.Location.Y - initialLocation.Y;
                AbsoluteLayout.SetLayoutBounds(boxView, rect);
            }
            break;

        case TouchActionType.Released:
            if (dragDictionary.ContainsKey(boxView) && dragDictionary[boxView].Id == args.Id)
            {
                dragDictionary.Remove(boxView);
            }
            break;
    }
}
```

A lógica `Pressed` define a propriedade `Capture` do objeto `TouchEffect` como `true`. Isso tem o efeito de fornecer todos os eventos seguintes desse dedo ao mesmo manipulador de eventos.

A lógica `Moved` move a `BoxView` alterando a propriedade anexada `LayoutBounds`. A propriedade `Location` dos argumentos do evento sempre é relativa à `BoxView` que está sendo arrastada e, se a `BoxView` estiver sendo arrastada a uma taxa constante, as propriedades `Location` dos eventos consecutivos serão aproximadamente as mesmas. Por exemplo, se um dedo pressiona a `BoxView` em seu centro, a ação `Pressed` armazena uma propriedade `PressPoint` de (50, 50), que permanece a mesma para eventos seguintes. Se a `BoxView` é arrastada diagonalmente a uma taxa constante, as propriedades `Location` seguintes durante a ação `Moved` podem ser valores de (55, 55); nesse caso, a lógica `Moved` adiciona 5 à posição horizontal e vertical da `BoxView`. Isso move a `BoxView` para que seu centro fique novamente diretamente sob o dedo.

Você pode mover vários elementos `BoxView` simultaneamente usando dedos diferentes.

[![](touch-tracking-images/boxviewdragging-small.png "Triple screenshot of the BoxView Dragging page")](touch-tracking-images/boxviewdragging-large.png#lightbox "Triple screenshot of the BoxView Dragging page")

### <a name="subclassing-the-view"></a>Criando subclasses da exibição

Geralmente, é mais fácil para um Xamarin.Forms elemento lidar com seus próprios eventos de toque. A página **Arrastando a BoxView arrastável** funciona da mesma maneira que a página **Arrastando BoxView**, mas os elementos que o usuário arrasta são instâncias de uma classe [`DraggableBoxView`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/DraggableBoxView.cs) que deriva de `BoxView`:

```csharp
class DraggableBoxView : BoxView
{
    bool isBeingDragged;
    long touchId;
    Point pressPoint;

    public DraggableBoxView()
    {
        TouchEffect touchEffect = new TouchEffect
        {
            Capture = true
        };
        touchEffect.TouchAction += OnTouchEffectAction;
        Effects.Add(touchEffect);
    }

    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!isBeingDragged)
                {
                    isBeingDragged = true;
                    touchId = args.Id;
                    pressPoint = args.Location;
                }
                break;

            case TouchActionType.Moved:
                if (isBeingDragged && touchId == args.Id)
                {
                    TranslationX += args.Location.X - pressPoint.X;
                    TranslationY += args.Location.Y - pressPoint.Y;
                }
                break;

            case TouchActionType.Released:
                if (isBeingDragged && touchId == args.Id)
                {
                    isBeingDragged = false;
                }
                break;
        }
    }
}
```

O construtor cria e anexa o `TouchEffect`e define a propriedade `Capture` quando é criada uma instância desse objeto pela primeira vez. Nenhum dicionário é necessário porque a própria classe armazena os valores `isBeingDragged`, `pressPoint` e `touchId` associados a cada dedo. A manipulação de `Moved` altera as propriedades `TranslationX` e `TranslationY` e, portanto, a lógica funcionará mesmo se o pai da `DraggableBoxView` não for um `AbsoluteLayout`.

### <a name="integrating-with-skiasharp"></a>Integração com o SkiaSharp

As duas próximas demonstrações exigem elementos gráficos e usam o SkiaSharp para essa finalidade. Talvez você queira aprender a [usar o SkiaSharp no Xamarin.Forms antes de](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) estudar esses exemplos. Os dois primeiros artigos ("Noções básicas de desenho do SkiaSharp" e "Linhas e caminhos do SkiaSharp") abrangem tudo o que você precisará aqui.

A página **Desenho de elipse** permite que você desenhe uma elipse passando o dedo na tela. Dependendo de como você mover o dedo, você poderá desenhar a elipse do canto superior esquerdo para o canto inferior direito ou de qualquer outro canto para o canto oposto. A elipse é desenhada com uma cor e uma opacidade aleatórias.

[![](touch-tracking-images/ellipsedrawing-small.png "Triple screenshot of the Ellipse Drawing page")](touch-tracking-images/ellipsedrawing-large.png#lightbox "Triple screenshot of the Ellipse Drawing page")

Se, em seguida, você tocar uma das elipses, poderá arrastá-la para outra localização. Isso exige uma técnica conhecida como "teste de clique," que envolve a pesquisa do objeto gráfico em um ponto específico. As reticências SkiaSharp não são Xamarin.Forms elementos, portanto, não podem executar seu próprio `TouchEffect` processamento. O `TouchEffect` precisa ser aplicada a todo objeto da `SKCanvasView`.

O arquivo [EllipseDrawPage.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/EllipseDrawingPage.xaml) cria uma instância da `SKCanvasView` em uma `Grid` de célula única. O objeto `TouchEffect` é anexado a esse `Grid`:

```xaml
<Grid x:Name="canvasViewGrid"
        Grid.Row="1"
        BackgroundColor="White">

    <skia:SKCanvasView x:Name="canvasView"
                        PaintSurface="OnCanvasViewPaintSurface" />
    <Grid.Effects>
        <tt:TouchEffect Capture="True"
                        TouchAction="OnTouchEffectAction" />
    </Grid.Effects>
</Grid>
```

No Android e na Plataforma Universal do Windows, o `TouchEffect` pode ser anexado diretamente à `SKCanvasView`, mas no iOS isso não funciona. Observe que a propriedade `Capture` está definida como `true`.

Cada elipse renderizada pelo SkiaSharp é representada por um objeto do tipo `EllipseDrawingFigure`:

```csharp
class EllipseDrawingFigure
{
    SKPoint pt1, pt2;

    public EllipseDrawingFigure()
    {
    }

    public SKColor Color { set; get; }

    public SKPoint StartPoint
    {
        set
        {
            pt1 = value;
            MakeRectangle();
        }
    }

    public SKPoint EndPoint
    {
        set
        {
            pt2 = value;
            MakeRectangle();
        }
    }

    void MakeRectangle()
    {
        Rectangle = new SKRect(pt1.X, pt1.Y, pt2.X, pt2.Y).Standardized;
    }

    public SKRect Rectangle { set; get; }

    // For dragging operations
    public Point LastFingerLocation { set; get; }

    // For the dragging hit-test
    public bool IsInEllipse(SKPoint pt)
    {
        SKRect rect = Rectangle;

        return (Math.Pow(pt.X - rect.MidX, 2) / Math.Pow(rect.Width / 2, 2) +
                Math.Pow(pt.Y - rect.MidY, 2) / Math.Pow(rect.Height / 2, 2)) < 1;
    }
}
```

As propriedades `StartPoint` e `EndPoint` são usadas quando o programa está processando a entrada por toque; a propriedade `Rectangle` é usada para desenhar a elipse. A propriedade `LastFingerLocation` entra em jogo quando a elipse está sendo arrastada e o método `IsInEllipse` ajuda no teste de clique. O método retorna `true` se o ponto está dentro da elipse.

O [arquivo code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/EllipseDrawingPage.xaml.cs) mantém três coleções:

```csharp
Dictionary<long, EllipseDrawingFigure> inProgressFigures = new Dictionary<long, EllipseDrawingFigure>();
List<EllipseDrawingFigure> completedFigures = new List<EllipseDrawingFigure>();
Dictionary<long, EllipseDrawingFigure> draggingFigures = new Dictionary<long, EllipseDrawingFigure>();
```

O dicionário `draggingFigure` contém um subconjunto da coleção `completedFigures`. O manipulador de eventos `PaintSurface` do SkiaSharp apenas renderiza os objetos nestas coleções `completedFigures` e `inProgressFigures`:

```csharp
SKPaint paint = new SKPaint
{
    Style = SKPaintStyle.Fill
};
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKCanvas canvas = args.Surface.Canvas;
    canvas.Clear();

    foreach (EllipseDrawingFigure figure in completedFigures)
    {
        paint.Color = figure.Color;
        canvas.DrawOval(figure.Rectangle, paint);
    }
    foreach (EllipseDrawingFigure figure in inProgressFigures.Values)
    {
        paint.Color = figure.Color;
        canvas.DrawOval(figure.Rectangle, paint);
    }
}
```

A parte mais complicada do processamento de toque é a manipulação de `Pressed`. É nesse momento que o teste de clique é executado, mas se o código detectar uma elipse sob o dedo do usuário, essa elipse só poderá ser arrastada se não estiver sendo arrastada por outro dedo. Se não houver nenhuma elipse sob o dedo do usuário, o código iniciará o processo de desenho de uma nova elipse:

```csharp
case TouchActionType.Pressed:
    bool isDragOperation = false;

    // Loop through the completed figures
    foreach (EllipseDrawingFigure fig in completedFigures.Reverse<EllipseDrawingFigure>())
    {
        // Check if the finger is touching one of the ellipses
        if (fig.IsInEllipse(ConvertToPixel(args.Location)))
        {
            // Tentatively assume this is a dragging operation
            isDragOperation = true;

            // Loop through all the figures currently being dragged
            foreach (EllipseDrawingFigure draggedFigure in draggingFigures.Values)
            {
                // If there's a match, we'll need to dig deeper
                if (fig == draggedFigure)
                {
                    isDragOperation = false;
                    break;
                }
            }

            if (isDragOperation)
            {
                fig.LastFingerLocation = args.Location;
                draggingFigures.Add(args.Id, fig);
                break;
            }
        }
    }

    if (isDragOperation)
    {
        // Move the dragged ellipse to the end of completedFigures so it's drawn on top
        EllipseDrawingFigure fig = draggingFigures[args.Id];
        completedFigures.Remove(fig);
        completedFigures.Add(fig);
    }
    else // start making a new ellipse
    {
        // Random bytes for random color
        byte[] buffer = new byte[4];
        random.NextBytes(buffer);

        EllipseDrawingFigure figure = new EllipseDrawingFigure
        {
            Color = new SKColor(buffer[0], buffer[1], buffer[2], buffer[3]),
            StartPoint = ConvertToPixel(args.Location),
            EndPoint = ConvertToPixel(args.Location)
        };
        inProgressFigures.Add(args.Id, figure);
    }
    canvasView.InvalidateSurface();
    break;
```

Outro exemplo do SkiaSharp é a página **Pintura a dedo**. Você pode selecionar uma cor e uma largura do traço em duas exibições `Picker` e, em seguida, desenhar com um ou mais dedos:

[![](touch-tracking-images/fingerpaint-small.png "Triple screenshot of the Finger Paint page")](touch-tracking-images/fingerpaint-large.png#lightbox "Triple screenshot of the Finger Paint page")

Este exemplo também exige uma classe separada para representar cada linha pintada na tela:

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new SKPath();
    }

    public SKPath Path { set; get; }

    public Color StrokeColor { set; get; }

    public float StrokeWidth { set; get; }
}
```

Um objeto `SKPath` é usado para renderizar cada linha. O arquivo [FingerPaint.xaml.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/FingerPaintPage.xaml.cs) mantém duas coleções desses objetos, uma para essas polilinhas desenhadas no momento e outra para as polilinhas concluídas:

```csharp
Dictionary<long, FingerPaintPolyline> inProgressPolylines = new Dictionary<long, FingerPaintPolyline>();
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

O processamento de `Pressed` cria uma nova `FingerPaintPolyline`, chama `MoveTo` no objeto de caminho para armazenar o ponto inicial e adiciona esse objeto ao dicionário `inProgressPolylines`. O processamento de `Moved` chama `LineTo` no objeto de caminho com a nova posição do dedo e o processamento de `Released` transfere a polilinha concluída de `inProgressPolylines` para `completedPolylines`. Mais uma vez, o código de desenho real do SkiaSharp é relativamente simples:

```csharp
SKPaint paint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    StrokeCap = SKStrokeCap.Round,
    StrokeJoin = SKStrokeJoin.Round
};
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKCanvas canvas = args.Surface.Canvas;
    canvas.Clear();

    foreach (FingerPaintPolyline polyline in completedPolylines)
    {
        paint.Color = polyline.StrokeColor.ToSKColor();
        paint.StrokeWidth = polyline.StrokeWidth;
        canvas.DrawPath(polyline.Path, paint);
    }

    foreach (FingerPaintPolyline polyline in inProgressPolylines.Values)
    {
        paint.Color = polyline.StrokeColor.ToSKColor();
        paint.StrokeWidth = polyline.StrokeWidth;
        canvas.DrawPath(polyline.Path, paint);
    }
}
```

### <a name="tracking-view-to-view-touch"></a>Acompanhando o toque exibição para exibição

Todos os exemplos anteriores definiram a propriedade `Capture` do `TouchEffect` como `true`, quando o `TouchEffect` foi criado ou quando o evento `Pressed` ocorreu. Isso garante que o mesmo elemento receba todos os eventos associados ao dedo que pressionou a exibição primeiro. A amostra final *não* define `Capture` como `true`. Isso causa um comportamento diferente quando um dedo em contato com a tela se move de um elemento para outro. O elemento movido pelo dedo recebe um evento com uma propriedade `Type` definida como `TouchActionType.Exited` e o segundo elemento recebe um evento com uma configuração `Type` igual a `TouchActionType.Entered`.

Esse tipo de processamento de toque é muito útil para um teclado musical. Uma tecla deve conseguir detectar quando ela é pressionada, mas também quando um dedo deslizar de uma tecla para outra.

A página **Teclado silencioso** define classes [`WhiteKey`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/WhiteKey.cs) e [`BlackKey`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/BlackKey.cs) pequenas que derivam de [`Key`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/Key.cs), que, por sua vez, deriva de `BoxView`.

A classe `Key` está pronta para ser usada em um programa real de música. Ela define as propriedades públicas chamadas `IsPressed` e `KeyNumber`, que se destinam a ser definidas como o código de teclas estabelecido pelo padrão MIDI. A classe `Key` também define um evento chamado `StatusChanged`, que é invocado quando a propriedade `IsPressed` é alterada.

Vários dedos são permitidos em cada tecla. Por esse motivo, a classe `Key` mantém uma `List` dos números de ID de toque dos dedos que estão tocando essa tecla:

```csharp
List<long> ids = new List<long>();
```

O manipulador de eventos `TouchAction` adiciona uma ID à lista `ids` para um tipo de evento `Pressed` e um tipo `Entered`, mas somente quando a propriedade `IsInContact` é `true` para o evento `Entered`. A ID é removida da `List` para um evento `Released` ou `Exited`:

```csharp
void OnTouchEffectAction(object sender, TouchActionEventArgs args)
{
    switch (args.Type)
    {
      case TouchActionType.Pressed:
          AddToList(args.Id);
          break;

        case TouchActionType.Entered:
            if (args.IsInContact)
            {
                AddToList(args.Id);
            }
            break;

        case TouchActionType.Moved:
            break;

        case TouchActionType.Released:
        case TouchActionType.Exited:
            RemoveFromList(args.Id);
            break;
    }
}

```

Os métodos `AddToList` e `RemoveFromList` verificam se a `List` foi alterada entre vazia e não vazia e, nesse caso, invoca o evento `StatusChanged`.

Os vários elementos `WhiteKey` e `BlackKey` são organizados no [arquivo XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/SilentKeyboardPage.xaml) da página, que tem uma melhor aparência quando o telefone é segurado em um modo paisagem:

[![](touch-tracking-images/silentkeyboard-small.png "Triple screenshot of the Silent Keyboard page")](touch-tracking-images/silentkeyboard-large.png#lightbox "Triple screenshot of the Silent Keyboard page")

Se você passar o dedo entre as teclas, pelas pequenas alterações nas cores, você verá que os eventos de toque são transferidos de uma tecla para outra.

## <a name="summary"></a>Resumo

Este artigo demonstrou como invocar eventos em um efeito e como gravar e usar um efeito que implementa o processamento multitoque de nível baixo.

## <a name="related-links"></a>Links relacionados

- [Acompanhamento de dedo multitoque no iOS](~/ios/app-fundamentals/touch/touch-tracking.md)
- [Acompanhamento de dedo multitoque no Android](~/android/app-fundamentals/touch/touch-tracking.md)
- [Efeito de acompanhamento de toque (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)
