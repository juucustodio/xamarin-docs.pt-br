---
title: Adicionando um reconhecedor de gesto de passar o dedo
description: Este artigo explica como reconhecer a ocorrência de um gesto de passar o dedo em uma exibição.
ms.prod: xamarin
ms.assetid: 164976C2-1429-49FB-9EB6-621E2681C19B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/14/2018
ms.openlocfilehash: ae9b5eb5b768b50ddcbc199040074de855f220de
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "68649450"
---
# <a name="adding-a-swipe-gesture-recognizer"></a>Adicionando um reconhecedor de gesto de passar o dedo

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)

_Um gesto de deslizamento ocorre quando um dedo é movido através da tela em uma direção horizontal ou vertical, e é frequentemente usado para iniciar a navegação através do conteúdo. Os exemplos de código neste artigo são retirados da amostra [Gesto de deslizamento.](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)_

Para reconhecer [`View`](xref:Xamarin.Forms.View) um gesto de deslize, crie uma [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) instância, defina a [`Direction`](xref:Xamarin.Forms.SwipeGestureRecognizer.Direction) propriedade como um [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) valor de enumeração (`Left`, `Right` `Up`ou `Down`), defina opcionalmente a [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) propriedade, manuseie o [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) evento e adicione o novo reconhecimento de gestos à [`GestureRecognizers`](xref:Xamarin.Forms.View.GestureRecognizers) coleção na exibição. O exemplo de `SwipeGestureRecognizer` código a [`BoxView`](xref:Xamarin.Forms.BoxView)seguir mostra um anexo a:

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

A [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) classe também [`Threshold`](xref:Xamarin.Forms.SwipeGestureRecognizer.Threshold) inclui uma propriedade, que pode `uint` ser opcionalmente definida como um valor que representa a distância mínima de deslizamento que deve ser alcançada para que um deslize seja reconhecido, em unidades independentes de dispositivos. O valor padrão dessa propriedade é 100, o que significa que qualquer gesto de passar o dedo com menos de 100 unidades independentes do dispositivo será ignorado.

## <a name="recognizing-the-swipe-direction"></a>Reconhecendo a direção do gesto de passar o dedo

Nos exemplos acima, [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) a propriedade é definida como [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) um único valor da enumeração. No entanto, também é possível definir essa `SwipeDirection` propriedade para múltiplos valores a partir da enumeração, de modo que o [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) evento seja acionado em resposta a um deslize em mais de uma direção. No entanto, a [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) restrição é que um único só pode reconhecer furtos que ocorrem no mesmo eixo. Portanto, gestos que ocorrem no eixo horizontal podem ser reconhecidos definindo a propriedade `Direction` como `Left` e `Right`:

```xaml
<SwipeGestureRecognizer Direction="Left,Right" Swiped="OnSwiped"/>
```

Da mesma forma, os deslizes que ocorrem no [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) eixo `Up` vertical `Down`podem ser reconhecidos definindo a propriedade para e:

```csharp
var swipeGesture = new SwipeGestureRecognizer { Direction = SwipeDirection.Up | SwipeDirection.Down };
```

Alternativamente, [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) um para cada direção de deslizamento pode ser criado para reconhecer deslizes em todas as direções:

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
> Nos exemplos acima, o mesmo manipulador de [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) eventos responde ao demissão do evento. No entanto, cada [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) instância pode usar um manipulador de eventos diferente, se necessário.

## <a name="responding-to-the-swipe"></a>Respondendo ao gesto de passar o dedo

Um manipulador de [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) eventos para o evento é mostrado no seguinte exemplo:

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

O [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) pode ser examinado para determinar a direção do golpe, com a lógica personalizada respondendo ao golpe conforme necessário. A direção do golpe pode ser [`Direction`](xref:Xamarin.Forms.SwipedEventArgs.Direction) obtida a partir da propriedade dos argumentos do [`SwipeDirection`](xref:Xamarin.Forms.SwipeDirection) evento, que será definida como um dos valores da enumeração. Além disso, os argumentos [`Parameter`](xref:Xamarin.Forms.SwipedEventArgs.Parameter) do evento também possuem um [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) imóvel que será definido para o valor do imóvel, se definido.

## <a name="using-commands"></a>Usando comandos

A [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) classe também [`Command`](xref:Xamarin.Forms.SwipeGestureRecognizer.Command) [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) inclui e propriedades. Essas propriedades geralmente são usadas em aplicativos que usam o padrão MVVM (Model-View-ViewModel). A `Command` propriedade define `ICommand` o a ser invocado quando um `CommandParameter` gesto de deslizamento é reconhecido, com a propriedade definindo um objeto a ser passado `ICommand.` para o O exemplo de código a seguir mostra como vincular `Command` a propriedade a um modelo de exibição `ICommand` definido cuja instância é definida como a página [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext):

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

`SwipeCommand`é uma propriedade `ICommand` de tipo definida na instância do [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)modelo de exibição que é definida como a página . Quando um gesto de passar o dedo é reconhecido, o método `Execute` do objeto `SwipeCommand` é executado. O argumento `Execute` para o método [`CommandParameter`](xref:Xamarin.Forms.SwipeGestureRecognizer.CommandParameter) é o valor da propriedade. Para obter mais informações sobre comandos, confira [A Interface de Comando](~/xamarin-forms/app-fundamentals/data-binding/commanding.md).

## <a name="creating-a-swipe-container"></a>Criando um contêiner para o gesto de passar o dedo

A `SwipeContainer` classe, que é mostrada no exemplo de código a seguir, [`View`](xref:Xamarin.Forms.View) é uma classe de reconhecimento de swipe generalizada que é enrolada em torno de um para executar o reconhecimento de gestos de swipe:

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

A `SwipeContainer` classe [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) cria objetos para todas as `Swipe` quatro direções de deslizamento e anexa manipuladores de eventos. Esses manipuladores de eventos invocam o evento `Swipe` definido pelo `SwipeContainer`.

O exemplo de código XAML a seguir mostra a `SwipeContainer` classe embrulhando um: [`BoxView`](xref:Xamarin.Forms.BoxView)

```xaml
<ContentPage ...>
    <StackLayout>
        <local:SwipeContainer Swipe="OnSwiped" ...>
            <BoxView Color="Teal" ... />
        </local:SwipeContainer>
    </StackLayout>
</ContentPage>
```

O exemplo de código `SwipeContainer` a [`BoxView`](xref:Xamarin.Forms.BoxView) seguir mostra como o envoltório a em uma página C#:

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

Quando [`BoxView`](xref:Xamarin.Forms.BoxView) o recebe um gesto [`Swiped`](xref:Xamarin.Forms.SwipeGestureRecognizer.Swiped) de [`SwipeGestureRecognizer`](xref:Xamarin.Forms.SwipeGestureRecognizer) furto, o evento no é disparado. Isso é manipulado pela classe `SwipeContainer`, que aciona seu próprio evento `Swipe`. Esse evento `Swipe` é manipulado na página. Em [`SwipedEventArgs`](xref:Xamarin.Forms.SwipedEventArgs) seguida, pode ser examinado para determinar a direção do golpe, com a lógica personalizada respondendo ao golpe conforme necessário.

## <a name="related-links"></a>Links relacionados

- [Gesto de passar o dedo (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithgestures-swipegesture)
- [GestureRecognizer](xref:Xamarin.Forms.GestureRecognizer)
- [SwipeGestureRecognizer](xref:Xamarin.Forms.SwipeGestureRecognizer)
