---
title: Usando os principais gráficos e animação principal no xamarin. IOS
description: Este artigo passo a passo demonstra como criar um aplicativo que usa os principais gráficos e animação principal. Ele mostra como desenhar na tela em resposta ao toque do usuário, bem como animar uma imagem percorrer ao longo de um caminho.
ms.prod: xamarin
ms.assetid: 4B96D5CD-1BF5-4520-AAA6-2B857C83815C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 2690b60abe963cf7b02ca282b32091098a224ccf
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104239"
---
# <a name="using-core-graphics-and-core-animation-in-xamarinios"></a>Usando os principais gráficos e animação principal no xamarin. IOS

Para este passo a passo vamos desenhar um caminho usando elementos gráficos principais em resposta à entrada por toque. Em seguida, adicionaremos um `CALayer` que contém uma imagem que podemos será animada ao longo do caminho.

Captura de tela a seguir mostra o aplicativo concluído:

![](graphics-animation-walkthrough-images/00-final-app.png "O aplicativo concluído")

Antes de começarmos o download do *GraphicsDemo* exemplo que acompanha este guia. Ele pode ser baixado [aqui](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/) e está localizado dentro de **GraphicsWalkthrough** directory inicie o projeto chamado **GraphicsDemo_starter** clicando duas vezes nele, e Abra o `DemoView` classe.

## <a name="drawing-a-path"></a>Desenho de um caminho


1. Na `DemoView` adicionar um `CGPath` variável à classe e instanciá-lo no construtor. Também declarar dois `CGPoint` variáveis `initialPoint` e `latestPoint`, que usaremos para capturar o ponto de toque do qual podemos construir o caminho:
    
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

2. Adicione o seguinte usando diretivas:

    ```csharp
    using CoreGraphics;
    using CoreAnimation;
    using Foundation;
    ```

3. Em seguida, substitua `TouchesBegan` e `TouchesMoved,` e adicione as implementações a seguir para capturar o ponto de toque inicial e cada ponto de toque subsequentes, respectivamente:

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

    `SetNeedsDisplay` será chamado sempre que toques mover na ordem para `Draw` a ser chamado na próxima passagem de execução do loop.

4. Vamos adicionar linhas para o caminho no `Draw` método e o uso de uma linha vermelha tracejada para desenhar com. [Implemente `Draw` ](~/ios/platform/graphics-animation-ios/core-graphics.md) com o código mostrado abaixo:

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

Se executarmos o aplicativo agora, podemos pode tocar para desenhar na tela, conforme mostrado na seguinte captura de tela:

![](graphics-animation-walkthrough-images/01-path.png "Na tela de desenho")

## <a name="animating-along-a-path"></a>Animando ao longo de um caminho

Agora que implementamos o código para permitir que os usuários desenhar o caminho, vamos adicionar o código para animar uma camada ao longo do caminho desenhado.

1. Primeiro, adicione uma [ `CALayer` ](~/ios/platform/graphics-animation-ios/core-animation.md) variável à classe e criá-lo no construtor:

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

2. Em seguida, vamos adicionar a camada como uma subcamada da camada da exibição quando o usuário levanta o dedo na tela. Em seguida, vamos criar uma animação de quadro-chave usando o caminho, animando a camada `Position`.

    Para fazer isso, precisamos substituir o `TouchesEnded` e adicione o seguinte código:

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

3. Executar o aplicativo agora e depois de desenho, uma camada com uma imagem é adicionada e se movimenta ao longo do caminho desenhado:

![](graphics-animation-walkthrough-images/00-final-app.png "Uma camada com uma imagem é adicionada e se movimenta ao longo do caminho desenhado")

## <a name="summary"></a>Resumo

Neste artigo, passamos por meio de um exemplo que interligados conceitos de gráficos e animação. Primeiro, mostramos como usar gráficos principais para desenhar um caminho em um `UIView` em resposta ao toque do usuário. Em seguida, mostramos como usar animação principal para criar uma imagem de viagem ao longo desse caminho.


## <a name="related-links"></a>Links relacionados

- [Animação principal](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Gráficos principais](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [Receitas de animação de núcleo](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
