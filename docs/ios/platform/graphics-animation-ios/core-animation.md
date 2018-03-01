---
title: "Animação de núcleo"
description: "Este artigo examina a estrutura de animação principal, mostrando como ela permite alto desempenho, animações suaves na UIKit, e como usá-lo diretamente para o controle de animação de nível inferior."
ms.topic: article
ms.prod: xamarin
ms.assetid: D4744147-FACB-415B-8155-3A6B3C35E527
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 80fe298f3dd24aac7f84213aee96499dd369d16d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="core-animation"></a>Animação de núcleo

_Este artigo examina a estrutura de animação principal, mostrando como ela permite alto desempenho, animações suaves na UIKit, e como usá-lo diretamente para o controle de animação de nível inferior._

iOS inclui [ *Core animação* ](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html) para oferecer suporte a animação para modos de exibição em seu aplicativo.
Todas as animações suaves no iOS, como rolagem de tabelas e passar o dedo entre diferentes modos de exibição, bem como eles fazem porque elas contam com animação Core internamente executam.

As estruturas de animação principais e principais gráficos podem trabalhar juntos para criar lindos, animado gráficos 2D. Na verdade animação Core pode transformar gráficos 2D em espaço 3D, criando o incríveis experiências inicial. No entanto, para criar gráficos 3D true, você precisaria usar algo como OpenGL ES, ou para ativar jogos para uma API como MonoGame, embora 3D está além do escopo deste artigo.

## <a name="core-animation"></a>Animação de núcleo

iOS usa a estrutura de animação principal para criar efeitos de animação, como fazer a transição entre os modos de exibição, deslizante menus e rolagem efeitos para citar alguns. Há duas maneiras de trabalhar com animação:

-  [Por meio de UIKit](#Using_UIKit_Animation), que inclui animações com base na exibição, bem como animadas transições entre os controladores.
-   [Por meio de animação Core](#Using_Core_Animation), as camadas diretamente, permitindo um controle refinado.

## <a name="using-uikit-animation"></a>Usando animação UIKit

UIKit fornece vários recursos que tornam mais fácil adicionar animação a um aplicativo. Embora usa internamente principais de animação, ele abstrai-lo para trabalhar somente com controladores e exibições.

Esta seção discute os recursos de animação UIKit incluindo:

-  Faz a transição entre os controladores
-  Transições entre modos de exibição
-  Animação de propriedade do modo de exibição


### <a name="view-controller-transitions"></a>Transições de controlador de exibição

 `UIViewController` fornece suporte interno para fazer a transição entre os controladores de exibição por meio de `PresentViewController` método. Ao usar `PresentViewController`, a transição para o segundo controlador opcionalmente pode ser animada.

Por exemplo, considere um aplicativo com dois controladores, onde tocar um botão no primeiro controlador chama `PresentViewController` para exibir um segundo controlador. Para controlar quais animação de transição é usada para mostrar o segundo controlador, basta definir sua [ `ModalTransitionStyle` ](https://developer.xamarin.com/api/type/UIKit.UIModalTransitionStyle/) propriedade conforme mostrado abaixo:

```csharp
SecondViewController vc2 = new SecondViewController {
    ModalTransitionStyle = UIModalTransitionStyle.PartialCurl
};
```

Nesse caso um `PartialCurl` animação é usada, embora vários outros estão disponíveis, incluindo:

-  `CoverVertical` – Slides backup da parte inferior da tela
-  `CrossDissolve` – O modo de exibição antigo desaparece e a nova exibição fade in
-  `FlipHorizontal` -Inverter um horizontal direita para esquerda. Em demissão a transição inverte esquerda para a direita.


Para animar a transição, passar `true` como o segundo argumento para `PresentViewController`:

```csharp
PresentViewController (vc2, true, null);
```

Captura de tela a seguir mostra a aparência de transição o `PartialCurl` caso:

 ![](core-animation-images/06-view-transitions.png "Esta captura de tela mostra a transição de PartialCurl")

### <a name="view-transitions"></a>Transições de exibição

Além das transições entre os controladores, UIKit também oferece suporte a animação faz a transição entre os modos de troca de um modo de exibição para outra.

Por exemplo, digamos que você tenha um controlador com `UIImageView`, onde tocar na imagem deve exibir um segundo `UIImageView`. Para animar a imagem de visão ampla do modo de exibição para fazer a transição para o segundo modo de exibição de imagem é tão simples quanto chamar `UIView.Transition`, passando o `toView` e `fromView` conforme mostrado abaixo:

```csharp
UIView.Transition (
    fromView: view1,
    toView: view2,
    duration: 2,
    options: UIViewAnimationOptions.TransitionFlipFromTop |
        UIViewAnimationOptions.CurveEaseInOut,
    completion: () => { Console.WriteLine ("transition complete"); });
```

`UIView.Transition` também usa um `duration` parâmetro que controla quanto tempo a animação é executada, bem como [ `options` ](https://developer.xamarin.com/api/type/UIKit.UIViewAnimationOptions/) para especificar as coisas como a animação para uso e a função de easing. Além disso, você pode especificar um manipulador de conclusão que será chamado quando a animação é concluída.

Captura de tela a seguir mostram a transição animada entre a imagem exibições quando `TransitionFlipFromTop` é usado:

 ![](core-animation-images/07-animated-transition.png "Esta captura de tela mostra a transição animada entre os modos de exibição de imagem quando TransitionFlipFromTop é usado")

### <a name="view-property-animations"></a>Animações de propriedade do modo de exibição

UIKit oferece suporte a uma variedade de propriedades de animação de `UIView` classe gratuitamente, incluindo:

-  Quadro
-  Limites
-  Centralizado 
-  Alfa
-  Transformar
-  Cor


Essas animações implicitamente acontecem especificando as alterações de propriedade em uma `NSAction` delegado passado para estático `UIView.Animate` método. Por exemplo, o código a seguir anima o ponto central de um `UIImageView`:

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

Isso resulta em uma imagem de animação e para trás na parte superior da tela, conforme mostrado abaixo:

 ![](core-animation-images/08-animate-center.png "Uma imagem de animação e para trás na parte superior da tela como a saída")

Assim como acontece com o `Transition` método `Animate` permite que a duração deve ser definido, juntamente com a função de easing. Este exemplo também usado o `UIViewAnimationOptions.Autoreverse` opção, o que faz com que a animação animar do valor para aquele inicial. No entanto, o código também define o `Center` para o seu valor inicial em um manipulador de conclusão. Enquanto uma animação é interpolação de valores de propriedade ao longo do tempo, o valor de modelo real da propriedade é sempre o valor final que foi definido. Neste exemplo, o valor é um ponto próximo ao lado direito da visão ampla do. Sem configuração o `Center` para o ponto inicial, que é onde a animação é concluída devido ao `Autoreverse` que está sendo definida, a imagem seria snap volta à direita depois que a animação é concluída, conforme mostrado abaixo:

 ![](core-animation-images/09-animation-complete.png "Sem configurar o centro para o ponto inicial, a imagem seria snap volta à direita depois que a animação é concluída")

## <a name="using-core-animation"></a>Usando animação de núcleo

 `UIView` animações permitem muita capacidade e devem ser usadas, se possível, devido à facilidade de implementação. Como mencionado anteriormente, animações UIView usam a estrutura de animação de núcleo. No entanto, algumas coisas que não pode ser feito com `UIView` animações como animar propriedades adicionais que não podem ser animadas com uma exibição ou interpolação ao longo de um caminho não linear. Em tais casos em que é necessário um controle mais preciso, animação Core pode ser usada diretamente.

### <a name="layers"></a>Camadas

Ao trabalhar com principais de animação, a animação ocorre por meio *camadas*, que são do tipo `CALayer`. Uma camada é conceitualmente semelhante a um modo de exibição em que há uma hierarquia de camada, bem como uma hierarquia do modo de exibição. Na verdade, camadas faça modos de exibição, com o modo de exibição adicionando suporte a interação do usuário. Você pode acessar a camada de qualquer modo de exibição por meio do modo de exibição `Layer` propriedade. Na verdade, o contexto usado em `Draw` método `UIView` , na verdade, é criada a partir da camada. Internamente, a camada fazendo uma `UIView` tem seu delegado definido para o modo de exibição, que é o que chama `Draw`. Portanto, quando o desenho a um `UIView`, realmente desenhar sua camada.

Camada animações podem ser implícitas ou explícitas. Animações implícitas são declarativas. Você simplesmente declaram o que devem alterar propriedades da camada e a animação funciona. Por outro lado, animações explícitas são criadas por meio de uma classe de animação é adicionada a uma camada. Animações explícitas permitem o controle de adição sobre como uma animação é criada. As seções a seguir nos aprofundar animações explícitas e implícitas em mais detalhes.

### <a name="implicit-animations"></a>Animações implícita

Uma maneira para animar as propriedades de uma camada é por meio de uma animação implícita. `UIView` animações criar animações implícita. No entanto, você pode criar animações implícita diretamente em uma camada também.

Por exemplo, o código a seguir define uma camada `Contents` a partir de uma imagem, define a largura da borda e a cor e adiciona a camada como uma subcamada de camada do modo de exibição:

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

Para adicionar uma animação implícita para a camada, simplesmente encapsular alterações de propriedades em um `CATransaction`. Isso permite que a animação de propriedades que não seriam pode ser animadas com uma animação de exibição, como o `BorderWidth` e `BorderColor` conforme mostrado abaixo:

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

Esse código também anima a camada `Position`, que é o local do ponto de ancoragem da camada medido a partir do canto superior esquerdo de coordenadas do superlayer. O ponto de ancoragem de uma camada é um ponto normalizado no sistema de coordenadas da camada.

A figura a seguir mostra o ponto de ancoragem e de posição:

 ![](core-animation-images/10-postion-anchorpt.png "Esta figura mostra o ponto de ancoragem e de posição")

Quando o exemplo for executado, o `Position`, `BorderWidth` e `BorderColor` animar conforme mostrado nas capturas de tela seguir:

 ![](core-animation-images/11-implicit-animation.png "Quando o exemplo for executado, a posição, BorderWidth e BorderColor animar conforme mostrado")

### <a name="explicit-animations"></a>Animações explícitas

Além de animações implícita, animação Core inclui uma variedade de classes que herdam de `CAAnimation` que lhe permitem encapsular animações são explicitamente adicionadas a uma camada. Isso permite controle refinado sobre animações, como modificar o valor inicial de uma animação, animações de agrupamento e especificando os quadros-chave para permitir caminhos não linear.

O código a seguir mostra um exemplo de uma animação explícita usando um `CAKeyframeAnimation` para a camada mostrada anteriormente (na seção de animação implícita):

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

Esse código altera o `Position` da camada, criando um caminho que é usado para definir uma animação de quadro-chave. Observe que a camada `Position` é definido como o valor final do `Position` da animação. Sem isso, a camada abruptamente retornará ao seu `Position` antes da animação porque a animação altera somente o valor de apresentação e não o valor real do modelo. Definindo o valor do modelo para o valor final de animação, a camada permanecem em vigor no final da animação.

As capturas de tela a seguir mostram a camada que contém a imagem animada através do caminho especificado:

 ![](core-animation-images/12-explicit-animation.png "Esta captura de tela mostra a camada que contém a imagem animada através do caminho especificado")
 
## <a name="summary"></a>Resumo

Neste artigo examinamos os recursos de animação fornecidos por meio de *Core animação* estruturas. Examinamos animação principal, mostrando como ele é ativado animações na UIKit, e como ele pode ser usado diretamente do controle de animação de nível inferior.

## <a name="related-links"></a>Links relacionados

- [Exemplo de animação de núcleo](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)
- [Gráficos de núcleo](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [Elementos gráficos e instruções passo a passo de animação](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Animação de núcleo](https://developer.xamarin.com/recipes/ios/animation/coreanimation)
