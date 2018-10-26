---
title: Adicionando um reconhecedor de gestos de passar o dedo
description: Este artigo explica como reconhecer um gesto de passar o dedo que ocorrem em um modo de exibição.
ms.prod: xamarin
ms.assetid: 164976C2-1429-49FB-9EB6-621E2681C19B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: 95e95d8849824cd2dc31c2019627cc5adbbefeec
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50130822"
---
# <a name="adding-a-swipe-gesture-recognizer"></a>Adicionando um reconhecedor de gestos de passar o dedo

_Um gesto de passar o dedo ocorre quando um dedo é movido pela tela na direção horizontal ou vertical e geralmente é usado para iniciar a navegação por meio do conteúdo. Os exemplos de código neste artigo são tirados de [gesto de passar o dedo](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/SwipeGesture/) exemplo._

Para fazer uma [ `View` ](xref:Xamarin.Forms.View) reconhecer um gesto de passar o dedo, crie um [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) da instância, defina o [ `Direction` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Direction) propriedade para um [ `SwipeDirection` ](xref:Xamarin.Forms.SwipeDirection) valor de enumeração (`Left`, `Right`, `Up`, ou `Down`), opcionalmente, defina a [ `Threshold` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) propriedade, o identificador de [ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) evento, e adicione o reconhecedor de gestos de novo para o [ `GestureRecognizers` ](xref:Xamarin.Forms.View.GestureRecognizers) coleta no modo de exibição. O seguinte exemplo de código mostra uma `SwipeGestureRecognizer` anexados a uma [ `BoxView` ](xref:Xamarin.Forms.BoxView):

```xaml
<BoxView Color="Teal" ...>
    <BoxView.GestureRecognizers>
        <SwipeGestureRecognizer Direction="Left" Swiped="OnSwiped"/>
    </BoxView.GestureRecognizers>
</BoxView>
```

Este é o equivalente C# código:

```csharp
var boxView = new BoxView { Color = Color.Teal, ... };
var leftSwipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Left };
leftSwipeGesture.Swiped += OnSwiped;

boxView.GestureRecognizers.Add(leftSwipeGesture);
```

O [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) classe também inclui um [ `Threshold` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) propriedade, que pode, opcionalmente, ser definida como um `uint` valor que representa a distância de passar o dedo mínima que deve ser obtida para um Passe o dedo para serem reconhecidos, em unidades independentes de dispositivo. O valor padrão dessa propriedade é 100, que significa que qualquer dedo que são que menos de 100 unidades de independentes de dispositivo serão ignoradas.

## <a name="recognizing-the-swipe-direction"></a>Reconhecendo a direção de passar o dedo

Nos exemplos acima, o [ `Direction` ](xref:Xamarin.Forms.SwipedEventArgs.Direction) estiver definida como um valor do único o [ `SwipeDirection` ](xref:Xamarin.Forms.SwipeDirection) enumeração. No entanto, também é possível definir essa propriedade para vários valores da `SwipeDirection` enumeração, para que o [ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) evento é acionado em resposta a um dedo em mais de uma única direção. No entanto, a restrição é que uma única [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) só conseguem reconhecer o dedo que ocorrem no mesmo eixo. Portanto, o dedo que ocorrem no eixo horizontal pode ser reconhecido, definindo o `Direction` propriedade para `Left` e `Right`:

```xaml
<SwipeGestureRecognizer Direction="Left,Right" Swiped="OnSwiped"/>
```

Da mesma forma, o dedo que ocorrem no eixo vertical pode ser reconhecido, definindo o [ `Direction` ](xref:Xamarin.Forms.SwipedEventArgs.Direction) propriedade `Up` e `Down`:

```csharp
var swipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up | SwipeDirection.Down };
```

Como alternativa, uma [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) para cada dedo direção pode ser criada para reconhecer o dedo em cada direção:

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

Este é o equivalente C# código:

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
> Nos exemplos acima, o manipulador de eventos responde para o [ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) acionamento do evento. No entanto, cada [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) instância pode usar um manipulador de eventos diferentes, se necessário.

## <a name="responding-to-the-swipe"></a>Respondendo ao dedo

Um manipulador de eventos para o [ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) evento é mostrado no exemplo a seguir:

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

O [ `SwipedEventArgs` ](xref:Xamarin.Forms.SwipedEventArgs) pode ser examinado para determinar a direção do dedo, com lógica personalizada, respondendo ao dedo conforme necessário. A direção de passar o dedo pode ser obtida de [ `Direction` ](xref:Xamarin.Forms.SwipedEventArgs.Direction) propriedade dos argumentos do evento, que será definido como um dos valores da [ `SwipeDirection` ](xref:Xamarin.Forms.SwipeDirection) enumeração. Além disso, os argumentos de evento também têm uma [ `Parameter` ](xref:Xamarin.Forms.SwipedEventArgs.Parameter) que será definida como o valor da propriedade a [ `CommandParameter` ](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) propriedade, se definido.

## <a name="using-commands"></a>Usando comandos

O [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) classe também inclui [ `Command` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Command) e [ `CommandParameter` ](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) propriedades. Essas propriedades são geralmente usadas em aplicativos que usam o padrão Model-View-ViewModel (MVVM). O `Command` propriedade define o `ICommand` a ser invocado quando um gesto de passar o dedo é reconhecido, com o `CommandParameter` propriedade definindo um objeto a ser passado para o `ICommand.` o exemplo de código a seguir mostra como associar o `Command` propriedade para um `ICommand` definidos no modelo de exibição cuja instância é definida como a página [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext):

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

`SwipeCommand` é uma propriedade do tipo `ICommand` definido na instância do modelo de exibição que é definida como a página [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext). Quando um gesto de passar o dedo for reconhecido, o `Execute` método da `SwipeCommand` objeto será executado. O argumento para o `Execute` método é o valor da [ `CommandParameter` ](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) propriedade. Para obter mais informações sobre comandos, consulte [a Interface de comando](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="creating-a-swipe-container"></a>Criar um contêiner de passar o dedo

O `SwipeContainer` classe, que é mostrado no exemplo de código a seguir, é uma classe de reconhecimento de passar o dedo generalizado ser encapsulado em torno de um [ `View` ](xref:Xamarin.Forms.View) para executar o reconhecimento de gesto de passar o dedo:

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

O `SwipeContainer` classe cria [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) objetos para todas as direções de quatro passar o dedo e anexa `Swipe` manipuladores de eventos. Esses manipuladores de eventos é invocar o `Swipe` evento definido pelo `SwipeContainer`.

Mostra o exemplo de código XAML abaixo de `SwipeContainer` classe quebra automática de um [ `BoxView` ](xref:Xamarin.Forms.BoxView):

```xaml
<ContentPage ...>
    <StackLayout>
        <local:SwipeContainer Swipe="OnSwiped" ...>
            <BoxView Color="Teal" ... />
        </local:SwipeContainer>
    </StackLayout>
</ContentPage>
```

O seguinte exemplo de código mostra como o `SwipeContainer` encapsula uma [ `BoxView` ](xref:Xamarin.Forms.BoxView) em um C# página:

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

Quando o [ `BoxView` ](xref:Xamarin.Forms.BoxView) recebe um gesto de passar o dedo, o [ `Swiped` ](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) evento no [ `SwipeGestureRecognizer` ](xref:Xamarin.Forms.SwipeGestureRecognizer) é disparado. Isso é tratado pelos `SwipeContainer` classe, que dispara seu próprio `Swipe` eventos. Isso `Swipe` evento é manipulado na página. O [ `SwipedEventArgs` ](xref:Xamarin.Forms.SwipedEventArgs) , em seguida, pode ser examinado para determinar a direção do dedo, com lógica personalizada, respondendo ao dedo conforme necessário.

## <a name="related-links"></a>Links relacionados

- [Gesto de passar o dedo (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithGestures/SwipeGesture/)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [SwipeGestureRecognizer](xref:Xamarin.Forms.SwipeGestureRecognizer)
