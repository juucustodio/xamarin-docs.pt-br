---
title: Adicionando um reconhecedor de gesto de passar o dedo
description: Este artigo explica como reconhecer a ocorrência de um gesto de passar o dedo em uma exibição.
ms.prod: xamarin
ms.assetid: 164976C2-1429-49FB-9EB6-621E2681C19B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: 95e95d8849824cd2dc31c2019627cc5adbbefeec
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50130822"
---
# <a name="adding-a-swipe-gesture-recognizer"></a>Adicionando um reconhecedor de gesto de passar o dedo

_Um gesto de passar o dedo ocorre quando um dedo é movido pela tela na direção horizontal ou vertical e geralmente é usado para iniciar a navegação pelo conteúdo. Os exemplos de código neste artigo são provenientes do exemplo [Gesto de passar o dedo](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/SwipeGesture/)._

Para fazer com que um [`View`](xref:Xamarin.Forms.View) reconheça um gesto de passar o dedo, crie uma instância de [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer), defina a propriedade [`Direction`](xref:Xamarin.Forms.SwipeGestureRecognizer.Direction) como um valor de enumeração [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) (`Left`, `Right`, `Up` ou `Down`), opcionalmente, defina a propriedade [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold), manipule o evento [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) e adicione o novo reconhecedor de gestos à coleção [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) na exibição. O exemplo de código a seguir mostra um `SwipeGestureRecognizer` anexado a um [`BoxView`](xref:Xamarin.Forms.BoxView):

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

A classe [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) também inclui uma propriedade [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) que pode, opcionalmente, ser definida como um valor de `uint` que representa a distância mínima que gesto deve percorrer para ser reconhecido, em unidades independentes do dispositivo. O valor padrão dessa propriedade é 100, o que significa que qualquer gesto de passar o dedo com menos de 100 unidades independentes do dispositivo será ignorado.

## <a name="recognizing-the-swipe-direction"></a>Reconhecendo a direção do gesto de passar o dedo

Nos exemplos acima, a propriedade [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) é definida como um valor único da enumeração [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection). No entanto, também é possível definir essa propriedade como vários valores da enumeração `SwipeDirection`, para que o evento [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) seja acionado em resposta a um gesto de passar o dedo em mais de uma direção. No entanto, a restrição é que um [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) único só é capaz de reconhecer gestos que ocorrem no mesmo eixo. Portanto, gestos que ocorrem no eixo horizontal podem ser reconhecidos definindo a propriedade `Direction` como `Left` e `Right`:

```xaml
<SwipeGestureRecognizer Direction="Left,Right" Swiped="OnSwiped"/>
```

De forma semelhante, gestos que ocorrem no eixo vertical podem ser reconhecidos definindo a propriedade [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) como `Up` e `Down`:

```csharp
var swipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up | SwipeDirection.Down };
```

Como alternativa, é possível criar um [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) para o gesto de passar o dedo em cada direção a fim de reconhecer o gesto em todas as direções:

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
> Nos exemplos acima, o mesmo manipulador de eventos responde ao acionamento do evento [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped). No entanto, cada instância de [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) pode usar um manipulador de eventos diferente se necessário.

## <a name="responding-to-the-swipe"></a>Respondendo ao gesto de passar o dedo

Um manipulador de eventos para o evento [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) é mostrado no código a seguir:

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

O [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) pode ser examinado para determinar a direção do gesto de passar o dedo, com a lógica personalizada respondendo ao gesto conforme necessário. A direção do gesto de passar o dedo pode ser obtida da propriedade [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) dos argumentos do evento, que será definida como um dos valores da enumeração [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection). Além disso, os argumentos do evento também têm uma propriedade [`Parameter`](xref:Xamarin.Forms.SwipedEventArgs.Parameter) que será definida com o valor da propriedade [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter), se definida.

## <a name="using-commands"></a>Usando comandos

A classe [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) também inclui propriedades [`Command`](xref:Xamarin.Forms.SwipeGestureRecognizer.Command) e [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter). Essas propriedades geralmente são usadas em aplicativos que usam o padrão MVVM (Model-View-ViewModel). A propriedade `Command` define o `ICommand` a ser invocado quando um gesto de passar o dedo for reconhecido, com a propriedade `CommandParameter` definindo um objeto a ser passado para o `ICommand.`. O exemplo de código a seguir mostra como associar a propriedade `Command` a um `ICommand` definido no modelo de exibição cuja instância é definida como a página [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext):

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

`SwipeCommand` é uma propriedade do tipo `ICommand` definida na instância do modelo de exibição que é definida como o [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) da página. Quando um gesto de passar o dedo é reconhecido, o método `Execute` do objeto `SwipeCommand` é executado. O argumento para o método `Execute` assume o valor da propriedade [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter). Para obter mais informações sobre comandos, confira [A Interface de Comando](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="creating-a-swipe-container"></a>Criando um contêiner para o gesto de passar o dedo

A classe `SwipeContainer`, que é mostrada no exemplo de código a seguir, é uma classe de reconhecimento generalizado do gesto de passar o dedo, que é encapsulada em torno de um [`View`](xref:Xamarin.Forms.View) para reconhecer o gesto:

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

A classe `SwipeContainer` cria objetos [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) para os quatro sentidos do gesto de passar o dedo e anexa manipuladores de eventos `Swipe`. Esses manipuladores de eventos invocam o evento `Swipe` definido pelo `SwipeContainer`.

O exemplo de código XAML a seguir mostra a classe `SwipeContainer` encapsulando um [`BoxView`](xref:Xamarin.Forms.BoxView):

```xaml
<ContentPage ...>
    <StackLayout>
        <local:SwipeContainer Swipe="OnSwiped" ...>
            <BoxView Color="Teal" ... />
        </local:SwipeContainer>
    </StackLayout>
</ContentPage>
```

O exemplo de código a seguir mostra como o `SwipeContainer` encapsula um [`BoxView`](xref:Xamarin.Forms.BoxView) em uma página de C#:

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

Quando o [`BoxView`](xref:Xamarin.Forms.BoxView) recebe um gesto de passar o dedo, o evento [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) no [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) é acionado. Isso é manipulado pela classe `SwipeContainer`, que aciona seu próprio evento `Swipe`. Esse evento `Swipe` é manipulado na página. O [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) pode, então, ser examinado para determinar a direção do gesto de passar o dedo, com a lógica personalizada respondendo ao gesto conforme necessário.

## <a name="related-links"></a>Links relacionados

- [Gesto de passar o dedo (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/SwipeGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [SwipeGestureRecognizer](xref:Xamarin.Forms.SwipeGestureRecognizer)
