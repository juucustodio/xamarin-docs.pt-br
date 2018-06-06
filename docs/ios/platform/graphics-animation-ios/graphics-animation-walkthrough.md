---
title: Usando gráficos principais e principais de animação em xamarin
description: Este artigo passo a passo demonstra como criar um aplicativo que usa gráficos principais e principais de animação. Ele mostra como desenhar na tela em resposta a toque de usuário, bem como animar uma imagem em um caminho.
ms.prod: xamarin
ms.assetid: 4B96D5CD-1BF5-4520-AAA6-2B857C83815C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 7a4399a5d62e2000c2a15a65da8e0e427dc039e0
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787049"
---
# <a name="using-core-graphics-and-core-animation-in-xamarinios"></a>Usando gráficos principais e principais de animação em xamarin

Para este passo a passo, vamos desenhar um demarcador usando gráficos de núcleo em resposta a entrada por toque. Em seguida, adicionaremos um `CALayer` que contém uma imagem será animado ao longo do caminho.

Captura de tela a seguir mostra o aplicativo concluído:

![](graphics-animation-walkthrough-images/00-final-app.png "O aplicativo concluído")

Antes de começarmos a baixar o *GraphicsDemo* exemplo que acompanha este guia. Ele pode ser baixado [aqui](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/) e está localizado dentro de **GraphicsWalkthrough** directory iniciar o projeto chamado **GraphicsDemo_starter** clicando duas vezes nele, e Abra o `DemoView` classe.

## <a name="drawing-a-path"></a>Desenho de um caminho


1. Em `DemoView` adicionar um `CGPath` variável à classe e instanciá-la no construtor. Também declarar dois `CGPoint` variáveis, `initialPoint` e `latestPoint`, que usaremos para capturar o ponto de toque de que podemos construir o caminho:
    
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

3. Em seguida, substituir `TouchesBegan` e `TouchesMoved,` e adicione as implementações a seguir para capturar o ponto de toque inicial e cada ponto de toque subsequentes, respectivamente:

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

    `SetNeedsDisplay` será chamado sempre que mover ajustes para que `Draw` seja chamado na próxima passagem de execução do loop.

4. Vamos adicionar linhas para o caminho no `Draw` método e o uso de uma linha vermelha tracejada para desenhar com. [Implementar `Draw` ](~/ios/platform/graphics-animation-ios/core-graphics.md) com o código mostrado abaixo:

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

Se executarmos o aplicativo agora, vamos podem tocar para desenhar na tela, conforme mostrado na seguinte captura de tela:

![](graphics-animation-walkthrough-images/01-path.png "Na tela de desenho")

## <a name="animating-along-a-path"></a>Animação em um demarcador

Agora que implementamos o código para permitir que os usuários desenhar o caminho, vamos adicionar o código para animar uma camada ao longo do caminho desenhado.

1. Primeiro, adicione um [ `CALayer` ](~/ios/platform/graphics-animation-ios/core-animation.md) variável à classe e criá-lo no construtor:

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

2. Em seguida, adicionaremos a camada como uma subcamada de camada do modo de exibição quando o usuário levanta o dedo na tela. Em seguida, vamos criar uma animação de quadro-chave usando o caminho da camada de animação `Position`.

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

3. Executar o aplicativo agora e depois de desenho, uma camada com uma imagem é adicionada e passa ao longo do caminho desenhado:

![](graphics-animation-walkthrough-images/00-final-app.png "Uma camada com uma imagem é adicionada e passa ao longo do caminho desenhado")

## <a name="summary"></a>Resumo

Neste artigo, vamos percorreu um exemplo que vinculados conceitos de animação e elementos gráficos. Primeiro, mostramos como usar gráficos de núcleo para desenhar um demarcador um `UIView` em resposta a toque de usuário. Em seguida, mostramos como usar animação de núcleo para transformar uma imagem ao longo desse caminho de viagem.


## <a name="related-links"></a>Links relacionados

- [Animação principal](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Gráficos principais](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [Receitas de animação de núcleo](https://developer.xamarin.com/recipes/ios/animation/coreanimation)
