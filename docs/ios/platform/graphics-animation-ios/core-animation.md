---
title: Animação de núcleo no Xamarin. iOS
description: Este artigo examina a estrutura de animação principal, mostrando como ela permite o alto desempenho, animações fluidas em UIKit, além de como usá-la diretamente para controle de animação de nível inferior.
ms.prod: xamarin
ms.assetid: D4744147-FACB-415B-8155-3A6B3C35E527
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 12bb8fe6f8ed77ea9091c165d4656da292266679
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656612"
---
# <a name="core-animation-in-xamarinios"></a>Animação de núcleo no Xamarin. iOS

_Este artigo examina a estrutura de animação principal, mostrando como ela permite o alto desempenho, animações fluidas em UIKit, além de como usá-la diretamente para controle de animação de nível inferior._

o iOS inclui [*animação de núcleo*](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html) para fornecer suporte de animação para exibições em seu aplicativo.
Todas as animações ultra suaves no iOS, como a rolagem de tabelas e o passar o dedo entre diferentes modos de exibição, também são executadas, pois dependem da animação principal internamente.

A animação principal e as estruturas gráficas principais podem trabalhar em conjunto para criar gráficos 2D animados. Na verdade, a animação principal pode até mesmo transformar gráficos 2D em espaço 3D, criando experiências incríveis e Cinematic. No entanto, para criar gráficos 3D verdadeiros, você precisaria usar algo como OpenGL ES, ou para que os jogos se transformem em uma API como monogame, embora o 3D esteja além do escopo deste artigo.

<a name="Using_Core_Animation" />

## <a name="core-animation"></a>Animação principal

o iOS usa a estrutura de animação principal para criar efeitos de animação, como transição entre exibições, menus deslizantes e efeitos de rolagem para citar alguns. Há duas maneiras de trabalhar com animação:

- [Via UIKit](#Using_UIKit_Animation), que inclui animações baseadas em exibição, bem como transições animadas entre controladores.
- [Por meio de animação de núcleo](#Using_Core_Animation), quais camadas são diretamente, permitindo um controle mais refinado.

<a name="Using_UIKit_Animation" />

## <a name="using-uikit-animation"></a>Usando a animação UIKit

O UIKit fornece vários recursos que facilitam a adição de animação a um aplicativo. Embora ele use a animação de núcleo internamente, ele a abstrai para que você trabalhe apenas com exibições e controladores.

Esta seção aborda os recursos de animação do UIKit, incluindo:

-  Transições entre controladores
-  Transições entre exibições
-  Exibir animação de propriedade


### <a name="view-controller-transitions"></a>Transições de controlador de exibição

 `UIViewController`fornece suporte interno para fazer a transição entre os controladores de exibição por `PresentViewController` meio do método. Ao usar `PresentViewController`o, a transição para o segundo controlador pode, opcionalmente, ser animada.

Por exemplo, considere um aplicativo com dois controladores, em que tocar um botão no primeiro controlador chama `PresentViewController` para exibir um segundo controlador. Para controlar qual animação de transição é usada para mostrar o segundo controlador, basta definir [`ModalTransitionStyle`](xref:UIKit.UIModalTransitionStyle) sua propriedade, conforme mostrado abaixo:

```csharp
SecondViewController vc2 = new SecondViewController {
    ModalTransitionStyle = UIModalTransitionStyle.PartialCurl
};
```

Nesse caso, uma `PartialCurl` animação é usada, embora várias outras estejam disponíveis, incluindo:

-  `CoverVertical`– Desliza para cima na parte inferior da tela
-  `CrossDissolve`– A exibição antiga esmaece & a nova exibição esmaece
-  `FlipHorizontal`-Uma inversão horizontal da direita para a esquerda. No descarte, a transição inverte da esquerda para a direita.


Para animar a transição, `true` passe como o segundo argumento `PresentViewController`para:

```csharp
PresentViewController (vc2, true, null);
```

A captura de tela a seguir mostra a aparência da transição `PartialCurl` para o caso:

 ![](core-animation-images/06-view-transitions.png "Esta captura de tela mostra a transição PartialCurl")

### <a name="view-transitions"></a>Exibir transições

Além das transições entre controladores, o UIKit também dá suporte a transições de animação entre exibições para alternar uma exibição para outra.

Por exemplo, digamos que você tenha um controlador `UIImageView`com, onde tocar na imagem deve exibir um segundo `UIImageView`. Para animar a superexibição da exibição de imagem para fazer a transição para a segunda exibição de imagem `UIView.Transition`é tão simples quanto `toView` chamar `fromView` , passando-a e conforme mostrado abaixo:

```csharp
UIView.Transition (
    fromView: view1,
    toView: view2,
    duration: 2,
    options: UIViewAnimationOptions.TransitionFlipFromTop |
        UIViewAnimationOptions.CurveEaseInOut,
    completion: () => { Console.WriteLine ("transition complete"); });
```

`UIView.Transition`também usa um `duration` parâmetro que controla por quanto tempo a animação é executada, bem [`options`](xref:UIKit.UIViewAnimationOptions) como para especificar coisas como a animação a ser usada e a função de atenuação. Além disso, você pode especificar um manipulador de conclusão que será chamado quando a animação for concluída.

A captura de tela abaixo mostra a transição animada entre as exibições de imagem quando `TransitionFlipFromTop` é usado:

 ![](core-animation-images/07-animated-transition.png "Esta captura de tela mostra a transição animada entre as exibições de imagem quando TransitionFlipFromTop é usado")

### <a name="view-property-animations"></a>Exibir animações de propriedade

O `UIView` UIKit dá suporte à animação de uma variedade de propriedades na classe gratuitamente, incluindo:

-  Quadro
-  Limites
-  Centralizado
-  Alfa
-  Transformar
-  Cor


Essas animações acontecem implicitamente especificando alterações de propriedade em um `NSAction` delegado passado para o método `UIView.Animate` estático. Por exemplo, o código a seguir anima o ponto central de um `UIImageView`:

```csharp
pt = imgView.Center;

UIView.Animate (
    duration: 2, 
    delay: 0, 
    options: UIViewAnimationOptions.CurveEaseInOut | 
        UIViewAnimationOptions.Autoreverse,
    animation: () => {
        imgView.Center = new CGPoint (View.Bounds.GetMaxX () 
            - imgView.Frame.Width / 2, pt.Y);},
    completion: () => {
        imgView.Center = pt; }
);
```

Isso resulta em uma animação de imagem para frente e para trás na parte superior da tela, conforme mostrado abaixo:

 ![](core-animation-images/08-animate-center.png "Uma animação de imagem para frente e para trás na parte superior da tela como a saída")

Assim como com `Transition` o método `Animate` , permite que a duração seja definida, juntamente com a função de atenuação. Este exemplo também usou a `UIViewAnimationOptions.Autoreverse` opção, que faz com que a animação Anime o valor de volta para o inicial. No entanto, o código também `Center` define de volta para seu valor inicial em um manipulador de conclusão. Embora uma animação esteja interpolando valores de propriedade ao longo do tempo, o valor do modelo real da propriedade é sempre o valor final que foi definido. Neste exemplo, o valor é um ponto próximo do lado direito da superexibição. Sem definir o `Center` para o ponto inicial, que é onde a animação é concluída devido `Autoreverse` ao que está sendo definido, a imagem seria ajustada para o lado direito após a conclusão da animação, como mostrado abaixo:

 ![](core-animation-images/09-animation-complete.png "Sem definir o centro para o ponto inicial, a imagem seria ajustada para o lado direito após a conclusão da animação")

## <a name="using-core-animation"></a>Usando a animação de núcleo

 `UIView`as animações permitem muita capacidade e devem ser usadas se possível devido à facilidade de implementação. Como mencionado anteriormente, as animações UIView usam a estrutura de animação principal. No entanto, algumas coisas não podem `UIView` ser feitas com animações, como animação de propriedades adicionais que não podem ser animadas com uma exibição ou interpolação ao longo de um caminho não linear. Nesses casos em que você precisa de controle mais preciso, a animação de núcleo também pode ser usada diretamente.

### <a name="layers"></a>Layer

Ao trabalhar com a animação principal, a animação ocorre por meio de *camadas*, `CALayer`que são do tipo. Uma camada é conceitualmente semelhante a uma exibição em que há uma hierarquia de camadas, assim como há uma hierarquia de exibição. Na verdade, as exibições de fundo de camadas, com a exibição adicionando suporte para interação do usuário. Você pode acessar a camada de qualquer modo de exibição por meio `Layer` da propriedade da exibição. Na verdade, o contexto usado no `Draw` método de `UIView` é realmente criado a partir da camada. Internamente, a camada que faz o `UIView` backup de um tem seu delegado definido como a própria exibição, que `Draw`é o que chama. Portanto, ao desenhar em `UIView`um, você está realmente desenhando em sua camada.

Animações de camada podem ser implícitas ou explícitas. Animações implícitas são declarativas. Você simplesmente declara quais propriedades de camada devem ser alteradas e a animação funciona apenas. Por outro lado, animações explícitas são criadas por meio de uma classe de animação que é adicionada a uma camada. As animações explícitas permitem o controle de adição sobre como uma animação é criada. As seções a seguir investigam animações implícitas e explícitas em maior profundidade.

### <a name="implicit-animations"></a>Animações implícitas

Uma maneira de animar as propriedades de uma camada é por meio de uma animação implícita. `UIView`animações criam animações implícitas. No entanto, você também pode criar animações implícitas diretamente em uma camada.

Por exemplo, o código a seguir define uma camada `Contents` de uma imagem, define uma largura e uma cor de borda e adiciona a camada como uma subcamada da camada da exibição:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    layer = new CALayer ();
    layer.Bounds = new CGRect (0, 0, 50, 50);
    layer.Position = new CGPoint (50, 50);
    layer.Contents = UIImage.FromFile ("monkey2.png").CGImage;
    layer.ContentsGravity = CALayer.GravityResize;
    layer.BorderWidth = 1.5f;
    layer.BorderColor = UIColor.Green.CGColor;

    View.Layer.AddSublayer (layer);
}
```

Para adicionar uma animação implícita para a camada, basta encapsular as alterações de `CATransaction`Propriedade em um. Isso permite animar propriedades que não seriam animáveis com uma animação de exibição, `BorderWidth` como e `BorderColor` conforme mostrado abaixo:

```csharp
public override void ViewDidAppear (bool animated)
{
    base.ViewDidAppear (animated);

    CATransaction.Begin ();
    CATransaction.AnimationDuration = 10;
    layer.Position = new CGPoint (50, 400);
    layer.BorderWidth = 5.0f;
    layer.BorderColor = UIColor.Red.CGColor;
    CATransaction.Commit ();
}
```

Esse código também anima a camada `Position`, que é o local do ponto de ancoragem da camada medido a partir do canto superior esquerdo das coordenadas da SUPERCAMADA. O ponto de ancoragem de uma camada é um ponto normalizado dentro do sistema de coordenadas da camada.

A figura a seguir mostra a posição e o ponto de ancoragem:

 ![](core-animation-images/10-postion-anchorpt.png "Esta figura mostra a posição e o ponto de ancoragem")

Quando o exemplo é executado, o `Position` `BorderWidth` e `BorderColor` o animam conforme mostrado nas seguintes capturas de tela:

 ![](core-animation-images/11-implicit-animation.png "Quando o exemplo é executado, a posição, BorderWidth e BorderColor são animadas conforme mostrado")

### <a name="explicit-animations"></a>Animações explícitas

Além das animações implícitas, a animação principal inclui uma variedade de classes que herdam do `CAAnimation` que permitem encapsular animações que são adicionadas explicitamente a uma camada. Eles permitem um controle mais refinado sobre animações, como modificar o valor inicial de uma animação, agrupar animações e especificar quadros-chave para permitir caminhos não lineares.

O código a seguir mostra um exemplo de uma animação explícita usando `CAKeyframeAnimation` um para a camada mostrada anteriormente (na seção animação implícita):

```csharp
public override void ViewDidAppear (bool animated)
{
    base.ViewDidAppear (animated);
    
    // get the initial value to start the animation from
    CGPoint fromPt = layer.Position;
    
    /* set the position to coincide with the final animation value
    to prevent it from snapping back to the starting position
    after the animation completes*/
    layer.Position = new CGPoint (200, 300);
    
    // create a path for the animation to follow
    CGPath path = new CGPath ();
    path.AddLines (new CGPoint[] { fromPt, new CGPoint (50, 300), new CGPoint (200, 50), new CGPoint (200, 300) });
    
    // create a keyframe animation for the position using the path
    CAKeyFrameAnimation animPosition = (CAKeyFrameAnimation)CAKeyFrameAnimation.FromKeyPath ("position");
    animPosition.Path = path;
    animPosition.Duration = 2;
    
    // add the animation to the layer.
    /* the "position" key is used to overwrite the implicit animation created
    when the layer positino is set above*/
    layer.AddAnimation (animPosition, "position");
}
```

Esse código altera o `Position` da camada criando um caminho que é usado para definir uma animação de quadro-chave. Observe que a camada `Position` é definida como o valor final `Position` do da animação. Sem isso, a camada retornaria abruptamente para seu `Position` antes da animação porque a animação só altera o valor da apresentação e não o valor real do modelo. Ao definir o valor do modelo como o valor final da animação, a camada permanece em vigor no final da animação.

As capturas de tela a seguir mostram a camada que contém a animação da imagem por meio do caminho especificado:

 ![](core-animation-images/12-explicit-animation.png "Esta captura de tela mostra a camada que contém a animação da imagem por meio do caminho especificado")
 
## <a name="summary"></a>Resumo

Neste artigo, examinamos os recursos de animação fornecidos por meio das estruturas de *animação de núcleo* . Examinamos a animação principal, mostrando como ele alimenta animações em UIKit e como ela pode ser usada diretamente para controle de animação de nível inferior.

## <a name="related-links"></a>Links relacionados

- [Exemplo de animação de núcleo](https://docs.microsoft.com/samples/xamarin/ios-samples/graphicsandanimation)
- [Gráficos principais](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [Instruções de gráficos e animação](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Animação principal](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
