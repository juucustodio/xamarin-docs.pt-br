---
title: Exibir transições do controlador no Xamarin. iOS
description: Este documento descreve como personalizar transições animadas entre controladores de exibição em aplicativos Xamarin. iOS.
ms.prod: xamarin
ms.assetid: CB3AC8E2-8A47-4839-AFA5-AE33047BB26C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: f0886ac9d47e7ab08a6f74365bcc25163b803e11
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73002399"
---
# <a name="view-controller-transitions-in-xamarinios"></a>Exibir transições do controlador no Xamarin. iOS

O UIKit adiciona suporte para personalizar a transição animada que ocorre ao apresentar os controladores de exibição. Esse suporte está incluído em controladores internos, bem como em todos os controladores personalizados que herdam diretamente do `UIViewController`. Além disso, `UICollectionViewController` aproveita a personalização da transição do controlador para aproveitar as transições animadas nos layouts de exibição de coleção.

## <a name="custom-transitions"></a>Transições personalizadas

A transição animada entre os controladores de exibição no iOS 7 é totalmente personalizável. o `UIViewController` agora inclui uma propriedade `TransitioningDelegate` que fornece uma classe Animator personalizada para o sistema quando ocorre uma transição.

Para usar uma transição personalizada com o `PresentViewController`:

1. Defina o `ModalPresentationStyle` como `UIModalPresentationStyle.Custom` no controlador a ser apresentado.
2. Implemente `UIViewControllerTransitioningDelegate` para criar uma classe Animator, que é uma instância de `UIViewControllerAnimatedTransitioning`.
3. Defina a propriedade `TransitioningDelegate` como uma instância de `UIViewControllerTransitioningDelegate`, também no controlador a ser apresentado.
4. Apresente o controlador de exibição.

Por exemplo, o código a seguir apresenta um controlador de exibição do tipo `ControllerTwo`-uma subclasse de `UIViewController`:

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo ();

    this.PresentViewController (controllerTwo, true, null);
};
```

Executar o aplicativo e tocar no botão faz com que a animação padrão da exibição do segundo controlador seja animada na parte inferior, conforme mostrado abaixo:

 ![](transitions-images/no-custom-transition.png "Running the app and tapping the button causes the default animation of the second controllers view to animate in from the bottom")

No entanto, definir o `ModalPresentationStyle` e `TransitioningDelegate` resulta em uma animação personalizada para a transição:

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo () {
        ModalPresentationStyle = UIModalPresentationStyle.Custom
        };

    transitioningDelegate = new TransitioningDelegate ();
    controllerTwo.TransitioningDelegate = transitioningDelegate;

    this.PresentViewController (controllerTwo, true, null);
};
```

O `TransitioningDelegate` é responsável por criar uma instância da subclasse `UIViewControllerAnimatedTransitioning` chamada `CustomAnimator` no exemplo abaixo:

```csharp
public class TransitioningDelegate : UIViewControllerTransitioningDelegate
{
    CustomTransitionAnimator animator;

    public override IUIViewControllerAnimatedTransitioning GetAnimationControllerForPresentedController (UIViewController presented, UIViewController presenting, UIViewController source)
    {
        animator = new CustomTransitionAnimator ();
        return animator;
    }
}
```

Quando ocorre a transição, o sistema cria uma instância do `IUIViewControllerContextTransitioning`, que passou para os métodos do Animator. `IUIViewControllerContextTransitioning` contém a `ContainerView` em que a animação ocorre, bem como o controlador de exibição que inicia a transição e o controlador de exibição que está sendo transicionado.

A classe `UIViewControllerAnimatedTransitioning` manipula a animação real. Dois métodos devem ser implementados:

1. `TransitionDuration` – retorna a duração da animação em segundos.
1. `AnimateTransition` – executa a animação real.

Por exemplo, a classe a seguir implementa `UIViewControllerAnimatedTransitioning` para animar o quadro da exibição do controlador:

```csharp
public class CustomTransitionAnimator : UIViewControllerAnimatedTransitioning
{
    public CustomTransitionAnimator ()
    {
    }

    public override double TransitionDuration (IUIViewControllerContextTransitioning transitionContext)
    {
        return 1.0;
    }

    public override void AnimateTransition (IUIViewControllerContextTransitioning transitionContext)
    {
        var inView = transitionContext.ContainerView;
        var toVC = transitionContext.GetViewControllerForKey (UITransitionContext.ToViewControllerKey);
        var toView = toVC.View;

        inView.AddSubview (toView);

        var frame = toView.Frame;
        toView.Frame = CGRect.Empty;

        UIView.Animate (TransitionDuration (transitionContext), () => {
            toView.Frame = new CGRect (20, 20, frame.Width - 40, frame.Height - 40);
        }, () => {
            transitionContext.CompleteTransition (true);
        });
    }
}
```

Agora, quando o botão é tocado, a animação implementada na classe `UIViewControllerAnimatedTransitioning` é usada:

 ![](transitions-images/custom-transition.png "An example of the zoom in effect running")

## <a name="collection-view-transitions"></a>Transições de exibição de coleção

As exibições de coleção têm suporte interno para a criação de transições animadas:

- **Controladores de navegação** – a transição animada entre duas instâncias de `UICollectionViewController` pode, opcionalmente, ser manipulada automaticamente quando uma `UINavigationController` as gerencia.
- **Layout de transição** – uma nova classe `UICollectionViewTransitionLayout` permite A transição interativa entre layouts.

### <a name="navigation-controller-transitions"></a>Transições do controlador de navegação

Quando usado em um controlador de navegação, um `UICollectionViewController` inclui suporte para transições animadas entre controladores. Esse suporte é interno e requer apenas algumas etapas simples para implementar:

1. Defina `UseLayoutToLayoutNavigationTransitions` como `false` em um `UICollectionViewController`.
1. Adicione uma instância do `UICollectionViewController` à raiz da pilha do controlador de navegação.
1. Crie um segundo `UICollectionViewController` e defina sua propriedade `UseLayoutToLayoutNavigtionTransitions` como `true`.
1. Envie a segunda `UICollectionViewController` para a pilha do controlador de navegação.

O código a seguir adiciona uma subclasse `UICollectionViewController` chamada `ImagesCollectionViewController` à raiz de uma pilha do controlador de navegação, com a propriedade `UseLayoutToLayoutNavigationTransitions` definida como `false`:

```csharp
UIWindow window;
ImagesCollectionViewController viewController;
UICollectionViewFlowLayout layout;
UINavigationController navController;

public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
    window = new UIWindow (UIScreen.MainScreen.Bounds);

    // create and initialize a UICollectionViewFlowLayout
    layout = new UICollectionViewFlowLayout (){
        SectionInset = new UIEdgeInsets (10,5,10,5),
        MinimumInteritemSpacing = 5,
        MinimumLineSpacing = 5,
        ItemSize = new CGSize (100, 100)
    };

    viewController = new ImagesCollectionViewController (layout) {
            UseLayoutToLayoutNavigationTransitions = false
        };

    navController = new UINavigationController (viewController);

    window.RootViewController = navController;
    window.MakeKeyAndVisible ();

    return true;
}
```

Quando um item é selecionado, uma segunda instância do `ImagesController` é criada, somente desta vez usando uma classe de layout diferente. Para esse controlador, `UseLayoutToLayoutNavigtionTransitions` é definido como `true`, conforme mostrado abaixo:

```csharp
CircleLayout circleLayout;
ImagesCollectionViewController controller2;

...

public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    // UseLayoutToLayoutNavigationTransitions when item is selected
    circleLayout = new CircleLayout (Monkeys.Instance.Count){
        ItemSize = new CGSize (100, 100)
    };

    controller2 = new ImagesCollectionViewController (circleLayout) {
        UseLayoutToLayoutNavigationTransitions = true
        };

    NavigationController.PushViewController (controller2, true);
}
```

A propriedade `UseLayoutToLayoutNavigationTransitions` deve ser definida antes da adição do controlador à pilha de navegação. Com esse conjunto de propriedades, a transição deslizante horizontal normal é substituída por uma transição animada entre os layouts dos dois controladores, conforme ilustrado abaixo:

![](transitions-images/nav2.png "An animated transition between the layouts of the two controllers")

### <a name="transition-layout"></a>Layout de transição

Além do suporte de transição de layout nos controladores de navegação, um novo layout chamado `UICollectionViewTransitionLayout` agora está disponível. Essa classe de layout permite o controle interativo durante o processo de transição de layout, permitindo que o `TransitionProgress` seja definido a partir do código. `UICollectionViewTransitionLayout` é diferente de-e não uma substituição para-o método `SetCollectionViewLayout` do iOS 6 que causou a ocorrência de uma transição de layout animado. Esse método não forneceu suporte interno para controlar o progresso da transição animada.

 `UICollectionViewTransitionLayout` permite que, por exemplo, um reconhecedor de gesto seja configurado para controlar a transição entre os layouts em resposta à interação do usuário, gerenciando o layout original, bem como o layout pretendido para a transição para o.

As etapas para implementar uma transição interativa dentro de um reconhecedor de gestos usando `UICollectionViewTransitionLayout` são as seguintes:

1. Crie um reconhecedor de gestos.
1. Chame o método `StartInteractiveTransition` do `UICollectionView`, passando-o para o layout de destino e um manipulador de conclusão.
1. Defina a propriedade `TransitionProgress` da instância de `UICollectionViewTransitionLayout` retornada do método `StartInteractiveTransition`.
1. Invalidar o layout.
1. Chame o método `FinishInteractiveTransition` da `UICollectionView` para concluir a transição ou o método `CancelInteractiveTransition` para cancelá-lo.  `FinishInteractiveTransition` faz com que a animação conclua sua transição para o layout de destino, enquanto `CancelInteractiveTransition` resulta na animação retornando ao layout original.
1. Manipule a conclusão da transição no manipulador de conclusão do método `StartInteractiveTransition`.
1. Adicione o reconhecedor de gestos ao modo de exibição de coleção.

O código a seguir implementa uma transição de layout interativo dentro de um reconhecedor de gestos de pinçagem:

```csharp
imagesController = new ImagesCollectionViewController (flowLayout);

nfloat sf = 0.4f;
UICollectionViewTransitionLayout trLayout = null;
UICollectionViewLayout nextLayout;

pinch = new UIPinchGestureRecognizer (g => {

    var progress = Math.Abs(1.0f -  g.Scale)/sf;

    if(trLayout == null){
        if(imagesController.CollectionView.CollectionViewLayout is CircleLayout)
            nextLayout = flowLayout;
        else
            nextLayout = circleLayout;

        trLayout = imagesController.CollectionView.StartInteractiveTransition (nextLayout, (completed, finished) => {
            Console.WriteLine ("transition completed");
            trLayout = null;
        });
    }

    trLayout.TransitionProgress = (nfloat)progress;

    imagesController.CollectionView.CollectionViewLayout.InvalidateLayout ();

    if(g.State == UIGestureRecognizerState.Ended){
        if (trLayout.TransitionProgress > 0.5f)
            imagesController.CollectionView.FinishInteractiveTransition ();
        else
            imagesController.CollectionView.CancelInteractiveTransition ();
    }

});

imagesController.CollectionView.AddGestureRecognizer (pinch);

```

À medida que o usuário pinça o modo de exibição de coleção, o `TransitionProgress` é definido em relação à escala de pinçagem. Nessa implementação, se o usuário encerrar a pinçagem antes que a transição seja de 50% concluída, a transição será cancelada. Caso contrário, a transição será concluída.

## <a name="related-links"></a>Links relacionados

- [Introdução ao iOS 7 (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/introtoios7)
- [Visão geral da interface do usuário do iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Backgrounding](~/ios/app-fundamentals/backgrounding/index.md)
