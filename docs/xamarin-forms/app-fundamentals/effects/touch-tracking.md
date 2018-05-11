---
title: Invocação de eventos de efeitos
description: Um efeito pode definir e invocar um evento sinalizando alterações no modo de exibição nativo subjacente. Este artigo mostra como implementar o dedo de multitoque baixo nível de rastreamento e como gerar eventos que sinalizam a atividade de toque.
ms.prod: xamarin
ms.assetid: 6A724681-55EB-45B8-9EED-7E412AB19DD2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: e363cae4dd72a25e4768395410d4e56a8db30eba
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="invoking-events-from-effects"></a>Invocação de eventos de efeitos

_Um efeito pode definir e invocar um evento sinalizando alterações no modo de exibição nativo subjacente. Este artigo mostra como implementar o dedo de multitoque baixo nível de rastreamento e como gerar eventos que sinalizam a atividade de toque._

O efeito descrito neste artigo fornece acesso a eventos de nível baixo de toque. Esses eventos de nível inferior não estão disponíveis por meio de existente `GestureRecognizer` classes, mas eles são vitais para alguns tipos de aplicativos. Por exemplo, um finger-paint aplicativo precisa acompanhar dedos individuais conforme se move na tela. Um teclado de música precisa detectar toques e libera as chaves individuais, bem como um dedo gliding de uma chave para outra em uma glissando.

Um efeito é ideal para multitoque dedo controle porque ele pode ser anexado a qualquer elemento xamarin. Forms.

## <a name="platform-touch-events"></a>Eventos de toque de plataforma

O iOS, Android e plataforma Universal do Windows incluem uma API de nível inferior que permite que os aplicativos detectem toque atividade. Essas plataformas de que todos distinguem entre três tipos básicos toque eventos:

- *Pressionado*, quando um dedo toca a tela
- *Movido*, quando passa um dedo tocar na tela
- *Lançado*, quando o dedo seja liberado da tela

Em um ambiente de multitoque, vários dedos podem toque na tela ao mesmo tempo. Várias plataformas incluem um número de identificação (ID) que os aplicativos podem usar para distinguir entre vários dedos.

No iOS, o `UIView` classe define três métodos substituíveis, `TouchesBegan`, `TouchesMoved`, e `TouchesEnded` correspondente a esses três eventos básico. O artigo [multitoque dedo controle](~/ios/app-fundamentals/touch/touch-tracking.md) descreve como usar esses métodos. No entanto, um programa do iOS não precisa substituir uma classe que deriva de `UIView` usar esses métodos. O iOS `UIGestureRecognizer` também define esses mesmos três métodos e você pode anexar uma instância de uma classe que deriva de `UIGestureRecognizer` a qualquer `UIView` objeto.

No Android, o `View` classe define um método substituível chamado `OnTouchEvent` para processar todas as atividades de toque. O tipo da atividade de toque é definido por membros de enumeração `Down`, `PointerDown`, `Move`, `Up`, e `PointerUp` conforme descrito no artigo [multitoque dedo controle](~/android/app-fundamentals/touch/touch-tracking.md). O Android `View` também define um evento chamado `Touch` que permite que um manipulador de eventos a ser anexado a qualquer `View` objeto.

No Windows UWP (plataforma Universal), o `UIElement` classe define eventos denominados `PointerPressed`, `PointerMoved`, e `PointerReleased`. Elas são descritas no artigo [artigo lidar com entrada de ponteiro no MSDN](/windows/uwp/input-and-devices/handle-pointer-input/) e a documentação da API para o [ `UIElement` ](/uwp/api/windows.ui.xaml.uielement/) classe.

O `Pointer` API na plataforma Universal do Windows se destina a unificar o mouse, toque e entrada de caneta. Por esse motivo, o `PointerMoved` evento ser invocado quando o mouse se move em um elemento, mesmo quando um botão do mouse é pressionado não. O `PointerRoutedEventArgs` objeto que acompanha esses eventos tem uma propriedade denominada `Pointer` que tem uma propriedade denominada `IsInContact` que indica se um botão do mouse é pressionado ou com um clique em contato com a tela.

Além disso, o UWP define dois eventos mais chamados `PointerEntered` e `PointerExited`. Elas indicam quando um mouse ou o dedo move de um elemento para outro. Por exemplo, considere dois elementos adjacentes, denominados A e b. Os dois elementos instalou manipuladores para os eventos de ponteiro. Quando um dedo pressiona em A, o `PointerPressed` evento ser invocado. Como o dedo move, invoca um `PointerMoved` eventos. Se o dedo mudar de A para B, invoca A um `PointerExited` B e evento invoca um `PointerEntered` eventos. Se o dedo é liberado, B invoca um `PointerReleased` eventos.

Plataformas Android e iOS são diferentes do UWP: O modo de exibição que primeiro obtém a chamada para `TouchesBegan` ou `OnTouchEvent` quando um dedo toca a exibição continua a obter todos os toque atividade mesmo que se move o dedo para diferentes modos de exibição. O UWP pode se comportar da mesma forma se o aplicativo captura o ponteiro: no `PointerEntered` manipulador de eventos, as chamadas do elemento `CapturePointer` e, em seguida, obtém todas as atividades de toque do que o dedo.

A abordagem UWP prova ser muito útil para alguns tipos de aplicativos, por exemplo, um teclado de música. Cada chave pode manipular os eventos de toque para essa chave e detectar quando um dedo tem deslize para de uma chave para outro usando o `PointerEntered` e `PointerExited` eventos.

Por esse motivo, o efeito de controle de toque descrito neste artigo implementa a abordagem UWP.

## <a name="the-touch-tracking-effect-api"></a>A API de efeito do controle de toque

O [ **Touch demonstrações do efeito de controle** ](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/) exemplo contém as classes (e uma enumeração) que implementa o controle de baixo nível toque. Esses tipos pertencem ao namespace `TouchTracking` e começar com a palavra `Touch`. O **TouchTrackingEffectDemos** projeto de biblioteca .NET padrão inclui o `TouchActionType` enumeração para o tipo de evento de toque:

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

O `TouchEffect` deriva de classe na biblioteca do .NET padrão `RoutingEffect` e define um evento chamado `TouchAction` e um método chamado `OnTouchAction` que invoca o `TouchAction` evento:

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

Observe também o `Capture` propriedade. Para capturar eventos de toque, um aplicativo deve definir essa propriedade como `true` antes de uma `Pressed` eventos. Caso contrário, os eventos de toque se comportam como as da plataforma Universal do Windows.

O `TouchActionEventArgs` classe na biblioteca do .NET padrão contém todas as informações que acompanha cada evento:

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

Um aplicativo pode usar o `Id` propriedade para controlar dedos individuais. Observe o `IsInContact` propriedade. Esta propriedade é sempre `true` para `Pressed` eventos e `false` para `Released` eventos. Ele também é sempre `true` para `Moved` eventos no iOS e Android. O `IsInContact` propriedade pode ser `false` para `Moved` eventos sobre a plataforma Universal do Windows quando a execução do programa na área de trabalho e o ponteiro do mouse se move sem um botão pressionado.

Você pode usar o `TouchEffect` classe em seus próprios aplicativos, incluindo o arquivo no projeto de biblioteca .NET padrão da solução e adicionando uma instância para o `Effects` coleção de qualquer elemento xamarin. Forms. Anexar um manipulador para o `TouchAction` evento para obter os eventos de toque.

Para usar `TouchEffect` em seu próprio aplicativo, você também precisará as implementações de plataforma incluídas no **TouchTrackingEffectDemos** solução.

## <a name="the-touch-tracking-effect-implementations"></a>As implementações de efeito do controle de toque

O iOS, Android e UWP implementações do `TouchEffect` são descritos abaixo começando com a implementação mais simples (UWP) e terminando com a implementação de iOS porque ela é estruturalmente mais complexa que os outros.

### <a name="the-uwp-implementation"></a>A implementação de UWP

A implementação de UWP do `TouchEffect` é a mais simples. Normalmente, a classe derivada de `PlatformEffect` e inclui dois atributos de assembly:

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

O `OnAttached` substituição salva algumas informações como campos e anexa manipuladores para todos os eventos de ponteiro:

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

O `OnPointerPressed` manipulador invoca o evento de efeito chamando o `onTouchAction` campo o `CommonHandler` método:

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

`OnPointerPressed` também verifica o valor da `Capture` propriedade na classe efeito na biblioteca .NET padrão e chamadas `CapturePointer` se for `true`.

 Outros manipuladores de eventos UWP são ainda mais simples:

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

As implementações do Android e iOS são necessariamente mais complexas porque eles devem implementar a `Exited` e `Entered` eventos quando um dedo se move de um elemento para outro. Ambas as implementações são estruturadas da mesma forma.

O Android `TouchEffect` classe instala um manipulador para o `Touch` evento:

```csharp
view = Control == null ? Container : Control;
...
view.Touch += OnTouch;
```

A classe também define duas dicionários estáticos:

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

O `viewDictionary` obtém uma nova entrada toda vez que o `OnAttached` é chamada de substituição:

```csharp
viewDictionary.Add(view, this);
```

A entrada é removida do dicionário em `OnDetached`. Todas as instâncias de `TouchEffect` está associado um modo de exibição específico que o efeito é anexado a. O dicionário estático permite que qualquer `TouchEffect` instância para enumerar todos os outros modos de exibição e seus correspondente `TouchEffect` instâncias. Isso é necessário para permitir a transferência de eventos de um modo de exibição para outro.

Android atribui um código de ID para eventos de toque que permite que um aplicativo rastrear dedos individuais. O `idToEffectDictionary` associa esse código de ID com um `TouchEffect` instância. Um item é adicionado a esse dicionário quando o `Touch` manipulador é chamado para um pressionamento de dedo:

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

O item é removido do `idToEffectDictionary` quando o dedo é liberado na tela. O `FireEvent` método simplesmente acumula todas as informações necessárias para chamar o `OnTouchAction` método:

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

Todos os outros tipos de contato são processados de duas maneiras diferentes: se o `Capture` é de propriedade `true`, o evento de toque é uma conversão bem simple para o `TouchEffect` informações. Ele obtém mais complicado quando `Capture` é `false` porque os eventos de toque talvez precise ser movidos de um modo de exibição para outro. Isso é responsabilidade do `CheckForBoundaryHop` método, que é chamado durante a movimentação de eventos. Este método faz uso de ambos os dicionários estáticos. Enumera através o `viewDictionary` para determinar o modo de exibição que o dedo estiver tocando no momento, e ele usa `idToEffectDictionary` para armazenar atual `TouchEffect` instância (e, portanto, o modo de exibição) associado com uma determinada identificação:

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

Se houve uma alteração no `idToEffectionDictionary`, potencialmente chama o método `FireEvent` para `Exited` e `Entered` a transferência de uma exibição para outra. No entanto, o dedo pode ter sido movido para uma área ocupada por uma exibição sem um anexado `TouchEffect`, ou de área para uma exibição com o efeito anexado.

Observe o `try` e `catch` bloquear quando a exibição é acessada. Em uma página que navega para que, em seguida, navega de volta para a home page, o `OnDetached` método não é chamado e os itens permanecem no `viewDictionary` mas Android considera descartado.

### <a name="the-ios-implementation"></a>O implementação do iOS

A implementação do iOS é semelhante à implementação do Android, exceto que o iOS `TouchEffect` classe deve instanciar um derivativo de `UIGestureRecognizer`. Esta é uma classe no projeto iOS chamado `TouchRecognizer`. Essa classe mantém dois dicionários estáticos que armazenam `TouchRecognizer` instâncias:

```csharp
static Dictionary<UIView, TouchRecognizer> viewDictionary =
    new Dictionary<UIView, TouchRecognizer>();

static Dictionary<long, TouchRecognizer> idToTouchDictionary =
    new Dictionary<long, TouchRecognizer>();
```

Grande parte da estrutura desse `TouchRecognizer` classe é semelhante para o Android `TouchEffect` classe.

## <a name="putting-the-touch-effect-to-work"></a>Colocando o efeito de toque em funcionamento

O [ **TouchTrackingEffectDemos** ](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/) programa contém cinco páginas que testar o efeito de controle de toque para tarefas comuns.

O **BoxView arrastando** página permite que você adicione `BoxView` elementos para um `AbsoluteLayout` e, em seguida, arraste-os na tela. O [arquivo XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BoxViewDraggingPage.xaml) cria dois `Button` exibições para adicionar `BoxView` elementos para o `AbsoluteLayout` e limpar o `AbsoluteLayout`.

O método a [arquivo code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BoxViewDraggingPage.xaml.cs) que adiciona um novo `BoxView` para o `AbsoluteLayout` também adiciona uma `TouchEffect` do objeto para o `BoxView` e anexa um manipulador de eventos para o efeito:

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

O `TouchAction` manipulador processa todos os eventos de toque para todos o `BoxView` elementos, mas ela precisa ter algum cuidado: não é possível permitir que dois dedos em um único `BoxView` porque o programa implementa apenas arrastando e dois dedos seriam interferir com o outro. Por esse motivo, a página define uma classe inserida para cada dedo sendo controlado no momento:

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

O `dragDictionary` contém uma entrada para cada `BoxView` sendo arrastado.

O `Pressed` toque ação adiciona um item para este dicionário e o `Released` ação remove-lo. O `Pressed` lógica deve verificar se já existe um item no dicionário para que `BoxView`. Nesse caso, o `BoxView` é já está sendo arrastado e o novo evento é um segundo dedo naquele mesmo `BoxView`. Para o `Moved` e `Released` ações, o manipulador de eventos deve verificar se o dicionário tem uma entrada para que `BoxView` e que o toque `Id` propriedade para que arrastadas `BoxView` corresponde à entrada de dicionário:

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

O `Pressed` conjuntos de lógica de `Capture` propriedade do `TouchEffect` do objeto para `true`. Isso tem o efeito de fornecimento de todos os eventos subsequentes para que o dedo para o mesmo manipulador de eventos.

O `Moved` lógica move o `BoxView` alterando o `LayoutBounds` propriedade anexada. O `Location` propriedade dos argumentos do evento sempre é relativo a `BoxView` sendo arrastado e se o `BoxView` está sendo arrastado a uma taxa constante, o `Location` propriedades dos eventos consecutivos será aproximadamente o mesmo. Por exemplo, se um dedo pressiona o `BoxView` no centro, o `Pressed` ação repositórios um `PressPoint` propriedade de (50, 50), que permanece o mesmo para eventos subsequentes. Se o `BoxView` é arrastado diagonalmente a uma taxa constante próximos `Location` propriedades durante o `Moved` ação pode ser valores de (55, 55), caso em que o `Moved` lógica adiciona 5 para a posição horizontal e vertical do `BoxView`. Isso move a `BoxView` para que seu centro novamente está diretamente sob o dedo.

Você pode mover várias `BoxView` elementos simultaneamente usando os dedos diferentes.

[![](touch-tracking-images/boxviewdragging-small.png "Captura de tela da página BoxView arrastando tripla")](touch-tracking-images/boxviewdragging-large.png#lightbox "tripla captura de tela da página BoxView arrastando")

### <a name="subclassing-the-view"></a>Subclasses de modo de exibição

Geralmente, é mais fácil para um elemento xamarin. Forms lidar com seus próprios eventos de toque. O **arrastável arrastando de BoxView** página funciona da mesma forma a **BoxView arrastando** página, mas os elementos que o usuário arrasta é instâncias de um [ `DraggableBoxView` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/DraggableBoxView.cs) classe que deriva de `BoxView`:

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

O construtor cria e anexa o `TouchEffect`e define o `Capture` propriedade quando esse objeto é instanciado pela primeira vez. Não há dicionário é necessário porque a própria classe armazena `isBeingDragged`, `pressPoint`, e `touchId` valores associados a cada dedo. O `Moved` tratamento altera o `TranslationX` e `TranslationY` propriedades para a lógica funcione mesmo se o pai do `DraggableBoxView` não é um `AbsoluteLayout`.

### <a name="integrating-with-skiasharp"></a>Integrando SkiaSharp

As próximas duas demonstrações exigem gráficos, e eles usam SkiaSharp para essa finalidade. Talvez você queira saber mais sobre [usando SkiaSharp em xamarin. Forms](~/xamarin-forms/user-interface/graphics/skiasharp/index.md) antes que você estude esses exemplos. Os dois primeiros artigos ("Fundamentos de desenho SkiaSharp" e "SkiaSharp linhas e caminhos") abrangem tudo o que você precisará aqui.

O **desenho elipse** página permite que você desenhar uma elipse passando o dedo na tela. Dependendo de como você pode mover o dedo, você pode desenhar a elipse de canto superior esquerdo para o canto inferior direito, ou de qualquer outro canto para o canto oposto. Elipse é desenhada com uma cor aleatória e opacidade.

[![](touch-tracking-images/ellipsedrawing-small.png "Captura de tela da página de desenho de elipse tripla")](touch-tracking-images/ellipsedrawing-large.png#lightbox "tripla captura de tela da página de desenho de elipse")

Se você tocar em seguida, uma das elipses, você pode arrastá-lo para outro local. Isso requer uma técnica conhecida como "teste de clique," que envolve a pesquisa para o objeto gráfico em um momento específico. As reticências SkiaSharp não são elementos de xamarin. Forms, para que eles não podem executar suas próprias `TouchEffect` de processamento. O `TouchEffect` deve ser aplicada a todo o `SKCanvasView` objeto.

O [EllipseDrawPage.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/EllipseDrawingPage.xaml) arquivo instancia o `SKCanvasView` em uma única célula `Grid`. O `TouchEffect` objeto está anexado ao `Grid`:

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

No Android e a plataforma Universal do Windows, o `TouchEffect` pode ser anexado diretamente para o `SKCanvasView`, mas no iOS que não funciona. Observe que o `Capture` está definida como `true`.

Cada elipse que renderiza SkiaSharp é representado por um objeto do tipo `EllipseDrawingFigure`:

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

O `StartPoint` e `EndPoint` propriedades são usadas quando o programa está processando o toque de entrada; o `Rectangle` propriedade é usada para desenhar a elipse. O `LastFingerLocation` propriedade entra em jogo quando estiver sendo arrastada elipse e o `IsInEllipse` auxílios de método de teste de clique. O método retorna `true` se o ponto está dentro da elipse.

O [arquivo code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/EllipseDrawingPage.xaml.cs) mantém três coleções:

```csharp
Dictionary<long, EllipseDrawingFigure> inProgressFigures = new Dictionary<long, EllipseDrawingFigure>();
List<EllipseDrawingFigure> completedFigures = new List<EllipseDrawingFigure>();
Dictionary<long, EllipseDrawingFigure> draggingFigures = new Dictionary<long, EllipseDrawingFigure>();
```

O `draggingFigure` dicionário contém um subconjunto do `completedFigures` coleção. O SkiaSharp `PaintSurface` manipulador de eventos simplesmente processa os objetos do `completedFigures` e `inProgressFigures` coleções:

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

A parte mais complicada do processamento de toque é o `Pressed` tratamento. Isso é onde o teste de hit é executado, mas se o código de detectar uma elipse em dedo do usuário, que elipse só pode ser arrastada se atualmente não está sendo arrastado pelo outro dedo. Se não houver nenhum elipse em dedo do usuário, o código começa o processo de desenhar uma elipse novo:

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

Outro exemplo de SkiaSharp é o **dedo pintura** página. Você pode selecionar uma cor de traço e a largura do traço de dois `Picker` modos de exibição e, em seguida, desenhar com um ou mais indicadores:

[![](touch-tracking-images/fingerpaint-small.png "Tripla captura de tela da página de pintura dedo")](touch-tracking-images/fingerpaint-large.png#lightbox "tripla captura de tela da página de pintura dedo")

Este exemplo também requer uma classe separada para representar cada linha pintada na tela:

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

Um `SKPath` objeto é usado para processar cada linha. O [FingerPaint.xaml.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/FingerPaintPage.xaml.cs) arquivo mantém duas coleções desses objetos, uma para essas polilinhas desenhado no momento e outra para as polilinhas concluídas:

```csharp
Dictionary<long, FingerPaintPolyline> inProgressPolylines = new Dictionary<long, FingerPaintPolyline>();
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

O `Pressed` processamento cria um novo `FingerPaintPolyline`, chamadas `MoveTo` no objeto de caminho para armazenar o ponto inicial e adiciona esse objeto para o `inProgressPolylines` dicionário. O `Moved` processamento chamadas `LineTo` no objeto de caminho com a nova posição de dedo e o `Released` transfere o processamento de polilinha concluída de `inProgressPolylines` para `completedPolylines`. Novamente, o código de desenho de SkiaSharp real é relativamente simple:

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

### <a name="tracking-view-to-view-touch"></a>Controle de exibição para exibição toque

Todos os exemplos anteriores tem definido o `Capture` propriedade do `TouchEffect` para `true`, quando o `TouchEffect` foi criado ou quando o `Pressed` evento ocorreu. Isso garante que o mesmo elemento recebe todos os eventos associados com o dedo pressionada pela primeira vez o modo de exibição. O exemplo final faz *não* definir `Capture` para `true`. Isso faz com que um comportamento diferente quando um dedo em contato com a tela se move de um elemento para outro. O elemento que move o dedo da recebe um evento com um `Type` propriedade definida como `TouchActionType.Exited` e o segundo elemento recebe um evento com um `Type` de `TouchActionType.Entered`.

Esse tipo de processamento de toque é muito útil para um teclado de música. Uma chave deve ser capaz de detectar quando ele for pressionado, mas também quando um dedo slides de uma chave para outra.

O **teclado silenciosa** página define pequena [ `WhiteKey` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/WhiteKey.cs) e [ `BlackKey` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BlackKey.cs) classes que derivam de [ `Key` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/Key.cs), que é derivado de `BoxView`.

O `Key` classe está pronta para ser usado em um programa de música real. Define as propriedades públicas denominadas `IsPressed` e `KeyNumber`, que se destina a ser definido como o código de tecla estabelecido pelo padrão MIDI. O `Key` classe também define um evento chamado `StatusChanged`, que é invocado quando o `IsPressed` alterações de propriedade.

Vários dedos são permitidos em cada chave. Por esse motivo, o `Key` classe mantém um `List` dos números de ID de toque dos dedos tocar no momento essa chave:

```csharp
List<long> ids = new List<long>();
```

O `TouchAction` manipulador de eventos adiciona uma ID para o `ids` lista tanto para um `Pressed` tipo de evento e um `Entered` tipo, mas somente quando o `IsInContact` é de propriedade `true` para o `Entered` evento. A ID é removida do `List` para um `Released` ou `Exited` evento:

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

O `AddToList` e `RemoveFromList` métodos Verifique se o `List` foi alterado entre vazio e não-vazio e nesse caso, invoca o `StatusChanged` evento.

As várias `WhiteKey` e `BlackKey` elementos são organizados na página de [arquivo XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/SilentKeyboardPage.xaml), que fica melhor quando o telefone é mantido em um modo de paisagem:

[![](touch-tracking-images/silentkeyboard-small.png "Captura de tela da página teclado silenciosa tripla")](touch-tracking-images/silentkeyboard-large.png#lightbox "tripla captura de tela da página teclado silenciosa")

Se você passa o dedo através de chaves, você verá por pequenas alterações na cor que os eventos de toque são transferidos de uma chave para outra.

## <a name="summary"></a>Resumo

Este artigo demonstrou como invocar um efeito de eventos e como gravar e usar um efeito que implementa o processamento de baixo nível multitoque.


## <a name="related-links"></a>Links relacionados

- [Rastreamento de dedo multitoque iOS](~/ios/app-fundamentals/touch/touch-tracking.md)
- [Dedo multitoque controle no Android](~/android/app-fundamentals/touch/touch-tracking.md)
- [Efeito de controle de toque (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/)
