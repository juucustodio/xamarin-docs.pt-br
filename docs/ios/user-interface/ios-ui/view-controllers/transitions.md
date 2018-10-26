---
title: Transições de controlador de exibição no xamarin. IOS
description: Este documento descreve como personalizar a transição animada entre controladores de exibição de aplicativos xamarin. IOS.
ms.prod: xamarin
ms.assetid: CB3AC8E2-8A47-4839-AFA5-AE33047BB26C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: c143d01a5e68bf8ce9b9b69fdaf79d445f372357
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118065"
---
# <a name="view-controller-transitions-in-xamarinios"></a>Transições de controlador de exibição no xamarin. IOS

UIKit adiciona suporte para personalizar a transição animada que ocorre quando a apresentação de controladores de exibição. Esse suporte está incluído com controladores internos, bem como os controladores personalizados que herdam diretamente de `UIViewController`. Além disso, `UICollectionViewController` tira proveito de personalização de transição de controlador para aproveitar a transição animada em layouts de exibição de coleção.

## <a name="custom-transitions"></a>Transições personalizadas

A transição animada entre controladores de exibição no iOS 7 é totalmente personalizável. `UIViewController` agora inclui um `TransitioningDelegate` propriedade que fornece uma classe personalizada animator ao sistema quando ocorre uma transição.

Para usar uma transição personalizada com `PresentViewController`:

1.  Defina as `ModalPresentationStyle` para `UIModalPresentationStyle.Custom` no controlador para ser apresentado.
2.  Implemente `UIViewControllerTransitioningDelegate` para criar uma classe animador, que é uma instância de `UIViewControllerAnimatedTransitioning` .
3.  Defina a `TransitioningDelegate` propriedade para uma instância de `UIViewControllerTransitioningDelegate` , também no controlador para ser apresentado.
4.  Apresente o controlador de exibição.


Por exemplo, o código a seguir apresenta um controlador de exibição do tipo `ControllerTwo` - uma `UIViewController` subclasse:

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo ();

    this.PresentViewController (controllerTwo, true, null);
};
```

Executando o aplicativo e tocar no botão faz com que a animação padrão do modo de exibição do segundo controlador ser usado para animar na parte inferior, conforme mostrado abaixo:

 ![](transitions-images/no-custom-transition.png "Executando o aplicativo e tocar no botão faz com que a animação padrão da segunda exibição controladores para animar na parte inferior")

No entanto, definir a `ModalPresentationStyle` e `TransitioningDelegate` resulta em uma animação personalizada para a transição:

```csharp
showTwo.TouchUpInside += (object sender, EventArgs e) => {

    controllerTwo = new ControllerTwo () {
        ModalPresentationStyle = UIModalPresentationStyle.Custom;
        };

    transitioningDelegate = new TransitioningDelegate ();
    controllerTwo.TransitioningDelegate = transitioningDelegate;

    this.PresentViewController (controllerTwo, true, null);
};
```

O `TransitioningDelegate` é responsável por criar uma instância das `UIViewControllerAnimatedTransitioning` subclasse - chamado `CustomAnimator` no exemplo a seguir:

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

Quando ocorre a transição, o sistema cria uma instância de `IUIViewControllerContextTransitioning`, que ele passada a métodos do animator. `IUIViewControllerContextTransitioning` contém o `ContainerView` onde ocorre a animação, bem como o controlador de exibição, iniciando a transição e o controlador de exibição que está sendo transferido para.

O `UIViewControllerAnimatedTransitioning` classe manipula a animação real. Dois métodos devem ser implementados:

1.  `TransitionDuration` – Retorna a duração da animação em segundos.
1.  `AnimateTransition` – executa a animação real.


Por exemplo, a classe a seguir implementa `UIViewControllerAnimatedTransitioning` para animar quadro do exibição controlador de:

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

Agora, quando o botão é tocado, a animação implementado de `UIViewControllerAnimatedTransitioning` classe é usada:

 ![](transitions-images/custom-transition.png "Um exemplo de como o zoom em vigor em execução")

## <a name="collection-view-transitions"></a>Transições de exibição de coleção

Exibições de coleção têm suporte interno para a criação de transição animada:

-  **Controladores de navegação** – a animado a transição entre dois `UICollectionViewController` instâncias podem, opcionalmente, ser tratadas automaticamente quando um `UINavigationController` gerencia-los.
-  **Fazer a transição de Layout** – um novo `UICollectionViewTransitionLayout` classe permite interativa em transição entre layouts.


### <a name="navigation-controller-transitions"></a>Transições de controlador de navegação

Quando usado dentro de um controlador de navegação, uma `UICollectionViewController` inclui suporte para transição animada entre os controladores. Esse suporte é interno e requer apenas algumas etapas simples para implementar:

1.  Definir `UseLayoutToLayoutNavigationTransitions` à `false` em um `UICollectionViewController` .
1.  Adicionar uma instância da `UICollectionViewController` para a raiz da pilha do controlador de navegação.
1.  Crie um segundo `UICollectionViewController` e defina sua `UseLayoutToLayoutNavigtionTransitions` propriedade `true` .
1.  Enviar por push o segundo `UICollectionViewController` na pilha do controlador de navegação.


O código a seguir adiciona uma `UICollectionViewController` subclasse denominada `ImagesCollectionViewController` para a raiz da pilha do controlador de navegação, com o `UseLayoutToLayoutNavigationTransitions` propriedade definida como `false`:

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
            UseLayoutToLayoutNavigationTransitions = false;
        }

    navController = new UINavigationController (viewController);

    window.RootViewController = navController;
    window.MakeKeyAndVisible ();
    
    return true;
}
```

Quando um item é selecionado, uma segunda instância da `ImagesController` é criado, só que desta vez usando uma classe de layout diferente. Para este controlador `UseLayoutToLayoutNavigtionTransitions` é definido como `true`, conforme mostrado abaixo:

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
        UseLayoutToLayoutNavigationTransitions = true;
        }

    NavigationController.PushViewController (controller2, true);
}
```

O `UseLayoutToLayoutNavigationTransitions` propriedade deve ser definida antes de adicionar o controlador para a pilha de navegação. Com essa propriedade definida, a transição de deslizante horizontal normal é substituída por uma transição animada entre os layouts de dois controladores, conforme ilustrado abaixo:

![](transitions-images/nav2.png "Uma transição animada entre os layouts dos dois controladores")

### <a name="transition-layout"></a>Layout de transição

Além do suporte de transição de layout em controladores de navegação, um novo layout chamado `UICollectionViewTransitionLayout` agora está disponível. Essa classe de layout permite controle interativo durante o processo de transição de layout, permitindo que o `TransitionProgress` a ser definido no código. `UICollectionViewTransitionLayout` é diferente de - e não um substituto - o `SetCollectionViewLayout` método do iOS 6, o que causou uma transição animada layout ocorra. Esse método não forneceu o suporte interno para controlar o progresso de transição animada.

 `UICollectionViewTransitionLayout` permite, por exemplo ser configurado para controlar a transição entre layouts em resposta à interação do usuário, ao gerenciar o layout original, bem como o layout desejado para fazer a transição para um reconhecedor de gestos.

As etapas para implementar uma transição interativa dentro de um reconhecedor de gestos usando `UICollectionViewTransitionLayout` são da seguinte maneira:

1.  Crie um reconhecedor de gestos.
1.  Chame o `StartInteractiveTransition` método da `UICollectionView` , passando-o layout de destino e um manipulador de conclusão.
1.  Defina a `TransitionProgress` propriedade do `UICollectionViewTransitionLayout` instância retornada do `StartInteractiveTransition` método.
1.  Invalida o layout.
1.  Chame o `FinishInteractiveTransition` método da `UICollectionView` para concluir a transição ou o `CancelInteractiveTransition` método para cancelá-la.  `FinishInteractiveTransition` faz com que a animação concluir sua transição para o layout de destino, enquanto `CancelInteractiveTransition` resulta na animação retornar ao layout original.
1.  Manipular a conclusão da transição no manipulador de conclusão do `StartInteractiveTransition` método.
1.  Adicione o reconhecedor de gestos à exibição de coleção.


O código a seguir implementa uma transição de layout interativo dentro de um reconhecedor de gestos de aperto:

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

Como o usuário pinches a exibição de coleção, o `TransitionProgress` é definida em relação à escala da situação de emergência. Nessa implementação, se o usuário encerra a situação de emergência antes que a transição é 50% concluída, a transição é cancelada. Caso contrário, a transição é concluída.




## <a name="related-links"></a>Links relacionados

- [Introdução ao iOS 7 (amostra)](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [Visão geral da interface do usuário do iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Backgrounding](~/ios/app-fundamentals/backgrounding/index.md)
