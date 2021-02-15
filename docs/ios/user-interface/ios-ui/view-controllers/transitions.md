---
title: Exibir transições do controlador no Xamarin. iOS
description: Este documento descreve como personalizar transições animadas entre controladores de exibição em aplicativos Xamarin. iOS.
ms.prod: xamarin
ms.assetid: CB3AC8E2-8A47-4839-AFA5-AE33047BB26C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: e973221e013132c8172a4b1de0250a085f0ea1df
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436931"
---
# <a name="view-controller-transitions-in-xamarinios"></a>Exibir transições do controlador no Xamarin. iOS

O UIKit adiciona suporte para personalizar a transição animada que ocorre ao apresentar os controladores de exibição. Esse suporte está incluído em controladores internos, bem como em todos os controladores personalizados que herdam diretamente do `UIViewController` . Além disso, o aproveita `UICollectionViewController` a personalização da transição do controlador para aproveitar as transições animadas nos layouts de exibição de coleção.

## <a name="custom-transitions"></a>Transições personalizadas

A transição animada entre os controladores de exibição no iOS 7 é totalmente personalizável. `UIViewController` Agora inclui uma `TransitioningDelegate` propriedade que fornece uma classe Animator personalizada para o sistema quando ocorre uma transição.

Para usar uma transição personalizada com `PresentViewController` :

1. Defina  `ModalPresentationStyle` como  `UIModalPresentationStyle.Custom` no controlador a ser apresentado.
2. Implemente  `UIViewControllerTransitioningDelegate` para criar uma classe Animator, que é uma instância do  `UIViewControllerAnimatedTransitioning` .
3. Defina a  `TransitioningDelegate` propriedade como uma instância do  `UIViewControllerTransitioningDelegate` , também no controlador a ser apresentado.
4. Apresente o controlador de exibição.

Por exemplo, o código a seguir apresenta um controlador de exibição do tipo `ControllerTwo` -uma `UIViewController` subclasse:

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo ();

    this.PresentViewController (controllerTwo, true, null);
};
```

Executar o aplicativo e tocar no botão faz com que a animação padrão da exibição do segundo controlador seja animada na parte inferior, conforme mostrado abaixo:

 ![Executar o aplicativo e tocar no botão faz com que a animação padrão da segunda exibição controladores seja animada na parte inferior](transitions-images/no-custom-transition.png)

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

O `TransitioningDelegate` é responsável por criar uma instância da `UIViewControllerAnimatedTransitioning` subclasse, chamada `CustomAnimator` no exemplo abaixo:

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

Quando ocorre a transição, o sistema cria uma instância do `IUIViewControllerContextTransitioning` , que passou para os métodos do Animator. `IUIViewControllerContextTransitioning` contém o `ContainerView` local em que a animação ocorre, bem como o controlador de exibição que inicia a transição e o controlador de exibição ao qual está sendo feita a transição.

A `UIViewControllerAnimatedTransitioning` classe manipula a animação real. Dois métodos devem ser implementados:

1. `TransitionDuration` – Retorna a duração da animação em segundos.
1. `AnimateTransition` – executa a animação real.

Por exemplo, a seguinte classe implementa `UIViewControllerAnimatedTransitioning` para animar o quadro da exibição do controlador:

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

Agora, quando o botão é tocado, a animação implementada na `UIViewControllerAnimatedTransitioning` classe é usada:

 ![Um exemplo do efeito de ampliar em execução](transitions-images/custom-transition.png)

## <a name="collection-view-transitions"></a>Transições de exibição de coleção

As exibições de coleção têm suporte interno para a criação de transições animadas:

- **Controladores de navegação** – a transição animada entre duas  `UICollectionViewController` instâncias pode, opcionalmente, ser manipulada automaticamente quando uma  `UINavigationController` gerencia.
- **Layout de transição** – uma nova  `UICollectionViewTransitionLayout` classe permite A transição interativa entre layouts.

### <a name="navigation-controller-transitions"></a>Transições do controlador de navegação

Quando usado em um controlador de navegação, um `UICollectionViewController` inclui suporte para transições animadas entre controladores. Esse suporte é interno e requer apenas algumas etapas simples para implementar:

1. Defina  `UseLayoutToLayoutNavigationTransitions` como  `false` em um  `UICollectionViewController` .
1. Adicione uma instância do  `UICollectionViewController` à raiz da pilha do controlador de navegação.
1. Crie um segundo  `UICollectionViewController` e defina sua  `UseLayoutToLayoutNavigtionTransitions` propriedade como  `true` .
1. Envie o segundo  `UICollectionViewController` para a pilha do controlador de navegação.

O código a seguir adiciona uma `UICollectionViewController` subclasse chamada `ImagesCollectionViewController` à raiz de uma pilha do controlador de navegação, com a `UseLayoutToLayoutNavigationTransitions` propriedade definida como `false` :

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

Quando um item é selecionado, uma segunda instância do `ImagesController` é criada, somente desta vez usando uma classe de layout diferente. Para esse controlador, `UseLayoutToLayoutNavigtionTransitions` é definido como `true` , conforme mostrado abaixo:

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

A `UseLayoutToLayoutNavigationTransitions` propriedade deve ser definida antes de adicionar o controlador à pilha de navegação. Com esse conjunto de propriedades, a transição deslizante horizontal normal é substituída por uma transição animada entre os layouts dos dois controladores, conforme ilustrado abaixo:

![Uma transição animada entre os layouts dos dois controladores](transitions-images/nav2.png)

### <a name="transition-layout"></a>Layout de transição

Além do suporte de transição de layout nos controladores de navegação, um novo layout chamado `UICollectionViewTransitionLayout` agora está disponível. Essa classe de layout permite o controle interativo durante o processo de transição de layout, permitindo que o seja `TransitionProgress` definido a partir do código. `UICollectionViewTransitionLayout` é diferente de-e não uma substituição para-o `SetCollectionViewLayout` método do IOS 6 que causou a ocorrência de uma transição de layout animado. Esse método não forneceu suporte interno para controlar o progresso da transição animada.

 `UICollectionViewTransitionLayout` permite, por exemplo, que um reconhecedor de gestos seja configurado para controlar a transição entre layouts em resposta à interação do usuário, gerenciando o layout original, bem como o layout pretendido para a transição para o.

As etapas para implementar uma transição interativa dentro de um reconhecedor de gestos usando `UICollectionViewTransitionLayout` são as seguintes:

1. Crie um reconhecedor de gestos.
1. Chame o  `StartInteractiveTransition` método de  `UICollectionView` , passando o layout de destino e um manipulador de conclusão.
1. Defina a `TransitionProgress` propriedade da  `UICollectionViewTransitionLayout` instância retornada do  `StartInteractiveTransition` método.
1. Invalidar o layout.
1. Chame o `FinishInteractiveTransition` método de  `UICollectionView` para concluir a transição ou o  `CancelInteractiveTransition` método para cancelá-lo.  `FinishInteractiveTransition` faz com que a animação conclua sua transição para o layout de destino, enquanto `CancelInteractiveTransition` resulta na animação retornando ao layout original.
1. Manipule a conclusão da transição no manipulador de conclusão do  `StartInteractiveTransition` método.
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

## <a name="related-links"></a>Links Relacionados

- [Introdução ao iOS 7 (exemplo)](/samples/xamarin/ios-samples/introtoios7)
- [Visão geral da interface do usuário do iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Operação em segundo plano](~/ios/app-fundamentals/backgrounding/index.md)