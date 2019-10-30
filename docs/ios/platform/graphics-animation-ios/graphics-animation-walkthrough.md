---
title: Usando gráficos principais e animações de núcleo no Xamarin. iOS
description: Este artigo demonstra passo a passo como criar um aplicativo que usa gráficos de núcleo e animação de núcleo. Ele mostra como desenhar na tela em resposta ao toque do usuário, bem como animar uma imagem para viajar ao longo de um caminho.
ms.prod: xamarin
ms.assetid: 4B96D5CD-1BF5-4520-AAA6-2B857C83815C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: b35e88cfdc0bce321068951f1617885c90331c83
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032444"
---
# <a name="using-core-graphics-and-core-animation-in-xamarinios"></a>Usando gráficos principais e animações de núcleo no Xamarin. iOS

Neste tutorial, vamos desenhar um caminho usando gráficos de núcleo em resposta à entrada por toque. Em seguida, adicionaremos um `CALayer` contendo uma imagem que vamos animar ao longo do caminho.

A captura de tela a seguir mostra o aplicativo concluído:

![](graphics-animation-walkthrough-images/00-final-app.png "The completed application")

Antes de começarmos a baixar o exemplo de *GraphicsDemo* que acompanha este guia. Ele pode ser baixado [aqui](https://docs.microsoft.com/samples/xamarin/ios-samples/graphicsandanimation) e está localizado dentro do diretório **GraphicsWalkthrough** inicie o projeto chamado **GraphicsDemo_starter** clicando duas vezes nele e abre a classe `DemoView`.

## <a name="drawing-a-path"></a>Desenhando um caminho

1. No `DemoView` adicione uma variável `CGPath` à classe e instancie-a no construtor. Além disso, declare duas `CGPoint` variáveis, `initialPoint` e `latestPoint`, que usaremos para capturar o ponto de toque do qual construímos o caminho:

    ```csharp
    public class DemoView : UIView
    {
        CGPath path;
        CGPoint initialPoint;
        CGPoint latestPoint;

        public DemoView ()
        {
            BackgroundColor = UIColor.White;

            path = new CGPath ();
        }
    }
    ```

2. Adicione as seguintes diretivas using:

    ```csharp
    using CoreGraphics;
    using CoreAnimation;
    using Foundation;
    ```

3. Em seguida, substitua `TouchesBegan` e `TouchesMoved,` e adicione as seguintes implementações para capturar o ponto de toque inicial e cada ponto de toque subsequente, respectivamente:

    ```csharp
    public override void TouchesBegan (NSSet touches, UIEvent evt){

        base.TouchesBegan (touches, evt);

        UITouch touch = touches.AnyObject as UITouch;

        if (touch != null) {
            initialPoint = touch.LocationInView (this);
        }
    }

    public override void TouchesMoved (NSSet touches, UIEvent evt){

        base.TouchesMoved (touches, evt);

        UITouch touch = touches.AnyObject as UITouch;

        if (touch != null) {
            latestPoint = touch.LocationInView (this);
            SetNeedsDisplay ();
        }
    }
    ```

    `SetNeedsDisplay` será chamado cada vez que os toques forem movidos para que `Draw` seja chamado na próxima etapa de loop de execução.

4. Adicionaremos linhas ao caminho no método `Draw` e usarei uma linha tracejada vermelha para desenhar. [Implemente `Draw`](~/ios/platform/graphics-animation-ios/core-graphics.md) com o código mostrado abaixo:

    ```csharp
    public override void Draw (CGRect rect){

        base.Draw (rect);

        if (!initialPoint.IsEmpty) {

            //get graphics context
            using(CGContext g = UIGraphics.GetCurrentContext ()){

                //set up drawing attributes
                g.SetLineWidth (2);
                UIColor.Red.SetStroke ();

                //add lines to the touch points
                if (path.IsEmpty) {
                    path.AddLines (new CGPoint[]{initialPoint, latestPoint});
                } else {
                    path.AddLineToPoint (latestPoint);
                }

                //use a dashed line
                g.SetLineDash (0, new nfloat[] { 5, 2 * (nfloat)Math.PI });

                //add geometry to graphics context and draw it
                g.AddPath (path);
                g.DrawPath (CGPathDrawingMode.Stroke);
            }
        }
    }
    ```

Se executarmos o aplicativo agora, podemos tocar para desenhar na tela, conforme mostrado na seguinte captura:

![](graphics-animation-walkthrough-images/01-path.png "Drawing on the screen")

## <a name="animating-along-a-path"></a>Animação ao longo de um caminho

Agora que implementamos o código para permitir que os usuários desenhem o caminho, vamos adicionar o código para animar uma camada ao longo do caminho desenhado.

1. Primeiro, adicione uma variável [`CALayer`](~/ios/platform/graphics-animation-ios/core-animation.md) à classe e crie-a no construtor:

    ```csharp
    public class DemoView : UIView
        {
            …

            CALayer layer;

            public DemoView (){
                …

                //create layer
                layer = new CALayer ();
                layer.Bounds = new CGRect (0, 0, 50, 50);
                layer.Position = new CGPoint (50, 50);
                layer.Contents = UIImage.FromFile ("monkey.png").CGImage;
                layer.ContentsGravity = CALayer.GravityResizeAspect;
                layer.BorderWidth = 1.5f;
                layer.CornerRadius = 5;
                layer.BorderColor = UIColor.Blue.CGColor;
                layer.BackgroundColor = UIColor.Purple.CGColor;
            }
    ```

2. Em seguida, adicionaremos a camada como uma subcamada da camada da exibição quando o usuário levantar o dedo da tela. Em seguida, criaremos uma animação de quadro-chave usando o caminho, animando o `Position`da camada.

    Para fazer isso, precisamos substituir o `TouchesEnded` e adicionar o seguinte código:

    ```csharp
    public override void TouchesEnded (NSSet touches, UIEvent evt)
        {
            base.TouchesEnded (touches, evt);

            //add layer with image and animate along path

            if (layer.SuperLayer == null)
                Layer.AddSublayer (layer);

            // create a keyframe animation for the position using the path
            layer.Position = latestPoint;
            CAKeyFrameAnimation animPosition = (CAKeyFrameAnimation)CAKeyFrameAnimation.FromKeyPath ("position");
            animPosition.Path = path;
            animPosition.Duration = 3;
            layer.AddAnimation (animPosition, "position");
        }
    ```

3. Executar o aplicativo agora e depois de desenhar, uma camada com uma imagem é adicionada e viaja ao longo do caminho desenhado:

![](graphics-animation-walkthrough-images/00-final-app.png "A layer with an image is added and travels along the drawn path")

## <a name="summary"></a>Resumo

Neste artigo, apresentamos um exemplo de conceitos gráficos e animações vinculados juntos. Primeiro, mostramos como usar gráficos de núcleo para desenhar um caminho em uma `UIView` em resposta ao toque do usuário. Em seguida, mostramos como usar a animação de núcleo para fazer uma imagem viajar ao longo desse caminho.

## <a name="related-links"></a>Links relacionados

- [Animação principal](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Gráficos principais](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [Receitas de animação de núcleo](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
