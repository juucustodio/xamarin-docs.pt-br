---
title: Adicionar um reconhecedor de gestos de passar
description: Este artigo explica como reconhecer a ocorrência de um gesto de passar o dedo em uma exibição.
ms.prod: xamarin
ms.assetid: 164976C2-1429-49FB-9EB6-621E2681C19B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 85492866c5d613b9688d215f5979192c32bb7263
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375194"
---
# <a name="add-a-swipe-gesture-recognizer"></a>Adicionar um reconhecedor de gestos de passar

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)

_Um gesto de passar o dedo ocorre quando um dedo é movido pela tela em uma direção horizontal ou vertical e geralmente é usado para iniciar a navegação por meio de conteúdo. Os exemplos de código neste artigo são tirados do exemplo de [gesto de passar o dedo](/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture) ._

Para fazer um [`View`](xref:Xamarin.Forms.View) gesto reconhecer um dedo, crie uma [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) instância, defina a [`Direction`](xref:Xamarin.Forms.SwipeGestureRecognizer.Direction) propriedade como um [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) valor de enumeração ( `Left` , `Right` , `Up` ou `Down` ), opcionalmente, defina a [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) propriedade, manipule o [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) evento e adicione o novo reconhecedor de gesto à [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) coleção na exibição. O exemplo de código a seguir mostra um `SwipeGestureRecognizer` anexado a um [`BoxView`](xref:Xamarin.Forms.BoxView) :

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Swiped="OnSwiped"/>
    </BoxView.GestureRecognizers>
</BoxView>
```

Este é o código C# equivalente:

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left };
leftSwipeGesture.Swiped += OnSwiped;

boxView.GestureRecognizers.Add(leftSwipeGesture);
```

A [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) classe também inclui uma [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) propriedade, que pode ser definida opcionalmente como um `uint` valor que representa a distância mínima do toque que deve ser obtida para que um toque seja reconhecido, em unidades independentes do dispositivo. O valor padrão dessa propriedade é 100, o que significa que qualquer gesto de passar o dedo com menos de 100 unidades independentes do dispositivo será ignorado.

## <a name="recognizing-the-swipe-direction"></a>Reconhecendo a direção do gesto de passar o dedo

Nos exemplos acima, a [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) propriedade é definida como um valor único da [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) enumeração. No entanto, também é possível definir essa propriedade com vários valores da `SwipeDirection` enumeração, para que o evento seja disparado [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) em resposta a um dedo em mais de uma direção. No entanto, a restrição é que um único [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) só pode reconhecer os passes que ocorrem no mesmo eixo. Portanto, gestos que ocorrem no eixo horizontal podem ser reconhecidos definindo a propriedade `Direction` como `Left` e `Right`:

```xaml
<SwipeGestureRecognizer Direction="Left,Right" Swiped="OnSwiped"/>
```

Da mesma forma, os passes que ocorrem no eixo vertical podem ser reconhecidos definindo a [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) propriedade como `Up` e `Down` :

```csharp
var swipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up | SwipeDirection.Down };
```

Como alternativa, um [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) para cada direção do dedo pode ser criado para reconhecer o toque em cada direção:

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Right" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Up" Swiped="OnSwiped"/>
        <SwipeGestureRecognizer Direction="Down" Swiped="OnSwiped"/>
    </BoxView.GestureRecognizers>
</BoxView>
```

Este é o código C# equivalente:

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left };
leftSwipeGesture.Swiped += OnSwiped;
var rightSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Right };
rightSwipeGesture.Swiped += OnSwiped;
var upSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up };
upSwipeGesture.Swiped += OnSwiped;
var downSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Down };
downSwipeGesture.Swiped += OnSwiped;

boxView.GestureRecognizers.Add(leftSwipeGesture);
boxView.GestureRecognizers.Add(rightSwipeGesture);
boxView.GestureRecognizers.Add(upSwipeGesture);
boxView.GestureRecognizers.Add(downSwipeGesture);
```

> [!NOTE]
> Nos exemplos acima, o mesmo manipulador de eventos responde ao [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) acionamento do evento. No entanto, cada [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) instância pode usar um manipulador de eventos diferente, se necessário.

## <a name="responding-to-the-swipe"></a>Respondendo ao gesto de passar o dedo

Um manipulador de eventos para o [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) evento é mostrado no exemplo a seguir:

```csharp
void OnSwiped(object sender, SwipedEventArgs e)
{
    switch (e.Direction)
    {
        case SwipeDirection.Left:
            // Handle the swipe
            break;
        case SwipeDirection.Right:
            // Handle the swipe
            break;
        case SwipeDirection.Up:
            // Handle the swipe
            break;
        case SwipeDirection.Down:
            // Handle the swipe
            break;
    }
}
```

O [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) pode ser examinado para determinar a direção do dedo, com lógica personalizada respondendo ao dedo, conforme necessário. A direção do dedo pode ser obtida na [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) propriedade dos argumentos do evento, que será definida como um dos valores da [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) enumeração. Além disso, os argumentos de evento também têm uma [`Parameter`](xref:Xamarin.Forms.SwipedEventArgs.Parameter) propriedade que será definida como o valor da [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) propriedade, se definido.

## <a name="using-commands"></a>Usando comandos

A [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) classe também inclui [`Command`](xref:Xamarin.Forms.SwipeGestureRecognizer.Command) [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) Propriedades e. Essas propriedades geralmente são usadas em aplicativos que usam o padrão MVVM (Model-View-ViewModel). A `Command` propriedade define o `ICommand` a ser invocado quando um gesto de passar o dedo é reconhecido, com a `CommandParameter` propriedade que define um objeto a ser passado para o `ICommand.` exemplo de código a seguir mostra como associar a `Command` Propriedade a um `ICommand` definido no modelo de exibição cuja instância está definida como a página [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) :

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left, CommandParameter = "Left" };
leftSwipeGesture.SetBinding(SwipeGestureRecognizer.CommandProperty, "SwipeCommand");
boxView.GestureRecognizers.Add(leftSwipeGesture);
```

O código XAML equivalente é:

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Command="{Binding SwipeCommand}" CommandParameter="Left" />
    </BoxView.GestureRecognizers>
</BoxView>
```

`SwipeCommand` é uma propriedade do tipo `ICommand` definido na instância do modelo de exibição que é definida como a página [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) . Quando um gesto de passar o dedo é reconhecido, o método `Execute` do objeto `SwipeCommand` é executado. O argumento para o `Execute` método é o valor da [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) propriedade. Para obter mais informações sobre comandos, confira [A Interface de Comando](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="creating-a-swipe-container"></a>Criando um contêiner para o gesto de passar o dedo

A `SwipeContainer` classe, que é mostrada no exemplo de código a seguir, é uma classe de reconhecimento de toque generalizada que é encapsulada em torno de um [`View`](xref:Xamarin.Forms.View) para executar o reconhecimento do gesto de passar o dedo:

```csharp
public class SwipeContainer : ContentView
{
    public event EventHandler<SwipedEventArgs> Swipe;

    public SwipeContainer()
    {
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Left));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Right));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Up));
        GestureRecognizers.Add(GetSwipeGestureRecognizer(SwipeDirection.Down));
    }

    SwipeGestureRecognizer GetSwipeGestureRecognizer(SwipeDirection direction)
    {
        var swipe = new SwipeGestureRecognizer { Direction = direction };
        swipe.Swiped += (sender, e) => Swipe?.Invoke(this, e);
        return swipe;
    }
}
```

A `SwipeContainer` classe cria [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) objetos para todas as quatro direções do dedo e anexa `Swipe` manipuladores de eventos. Esses manipuladores de eventos invocam o evento `Swipe` definido pelo `SwipeContainer`.

O exemplo de código XAML a seguir mostra o `SwipeContainer` encapsulamento de classe a [`BoxView`](xref:Xamarin.Forms.BoxView) :

```xaml
<ContentPage ...>
    <StackLayout>
        <local:SwipeContainer Swipe="OnSwiped" ...>
            <BoxView Color="Teal" ... />
        </local:SwipeContainer>
    </StackLayout>
</ContentPage>
```

O exemplo de código a seguir mostra como o `SwipeContainer` encapsula um [`BoxView`](xref:Xamarin.Forms.BoxView) em uma página C#:

```csharp
public class SwipeContainerPageCS : ContentPage
{
    public SwipeContainerPageCS()
    {
        var boxView = new BoxView { Color = Color.Teal, ... };
        var swipeContainer = new SwipeContainer { Content = boxView, ... };
        swipeContainer.Swipe += (sender, e) =>
        {
          // Handle the swipe
        };

        Content = new StackLayout
        {
            Children = { swipeContainer }
        };
    }
}
```

Quando o [`BoxView`](xref:Xamarin.Forms.BoxView) recebe um gesto de passar o dedo, o [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) evento no [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) é acionado. Isso é manipulado pela classe `SwipeContainer`, que aciona seu próprio evento `Swipe`. Esse evento `Swipe` é manipulado na página. O [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) pode então ser examinado para determinar a direção do dedo, com lógica personalizada respondendo ao dedo conforme necessário.

## <a name="related-links"></a>Links relacionados

- [Gesto de passar o dedo (amostra)](/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [SwipeGestureRecognizer](xref:Xamarin.Forms.SwipeGestureRecognizer)