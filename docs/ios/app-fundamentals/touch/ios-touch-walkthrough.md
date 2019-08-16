---
title: 'Passo a passo: Usando o touch no Xamarin. iOS'
description: Este documento descreve como lidar com o toque em aplicativos Xamarin. iOS, discutindo interações de toque de exemplo, reconhecedores de gesto e reconhecedores de gestos personalizados.
ms.prod: xamarin
ms.assetid: 13F8289B-7A80-4959-AF3F-57874D866DCA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 352a312d795ba292b47621ddb84540c2259ea10a
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527061"
---
# <a name="walkthrough-using-touch-in-xamarinios"></a>Passo a passo: Usando o touch no Xamarin. iOS

Este tutorial demonstra como escrever código que responde a diferentes tipos de eventos de toque. Cada exemplo está contido em uma tela separada:

- [Exemplos de toque](#Touch_Samples) – como responder a eventos de toque.
- [Exemplos](#Gesture_Recognizer_Samples) de reconhecedor de gestos – como usar reconhecedores de gestos internos.
- [Exemplo](#Custom_Gesture_Recognizer) de reconhecedor de gestor personalizado – como criar um reconhecedor de gestos personalizado.

Cada seção contém instruções para escrever o código do zero.
O [código de exemplo inicial](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-start) já inclui uma tela completa de storyboard e de menu:

 [![](ios-touch-walkthrough-images/image3.png "O exemplo inclui a tela de menu")](ios-touch-walkthrough-images/image3.png#lightbox)

Siga as instruções abaixo para adicionar código ao storyboard e saiba mais sobre os diferentes tipos de eventos de toque disponíveis no iOS. Como alternativa, abra o [exemplo concluído](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-final) para ver tudo funcionando.

<a name="Touch_Samples"/>

## <a name="touch-samples"></a>Exemplos de toque

Neste exemplo, demonstraremos algumas das APIs de toque. Siga estas etapas para adicionar o código necessário para implementar eventos de toque:


1. Abra o projeto **Touch_Start**. Primeiro, execute o projeto para verificar se tudo está OK e toque no botão **amostras de toque** . Você verá uma tela semelhante à seguinte (embora nenhum dos botões funcione):
    
    [![](ios-touch-walkthrough-images/image4.png "Aplicativo de exemplo executado com botões que não são de trabalho")](ios-touch-walkthrough-images/image4.png#lightbox)


1. Edite o arquivo **TouchViewController.cs** e adicione as duas variáveis de instância a seguir `TouchViewController`à classe:

    ```csharp 
    #region Private Variables
    private bool imageHighlighted = false;
    private bool touchStartedInside;
    #endregion
    ```


1. Implemente `TouchesBegan` o método, conforme mostrado no código abaixo:

    ```csharp 
    public override void TouchesBegan(NSSet touches, UIEvent evt)
    {
        base.TouchesBegan(touches, evt);
    
        // If Multitouch is enabled, report the number of fingers down
        TouchStatus.Text = string.Format ("Number of fingers {0}", touches.Count);
    
        // Get the current touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            // Check to see if any of the images have been touched
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                // Fist image touched
                TouchImage.Image = UIImage.FromBundle("TouchMe_Touched.png");
                TouchStatus.Text = "Touches Began";
            } else if (touch.TapCount == 2 && DoubleTouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                // Second image double-tapped, toggle bitmap
                if (imageHighlighted)
                {
                    DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe.png");
                    TouchStatus.Text = "Double-Tapped Off";
                }
                else
                {
                    DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe_Highlighted.png");
                    TouchStatus.Text = "Double-Tapped On";
                }
                imageHighlighted = !imageHighlighted;
            } else if (DragImage.Frame.Contains(touch.LocationInView(View)))
            {
                // Third image touched, prepare to drag
                touchStartedInside = true;
            }
        }
    }
    ```
    
    Esse método funciona verificando um `UITouch` objeto e, se existir, executar alguma ação com base em onde o toque ocorreu:

    * _Inside TouchImage_ – exiba o texto `Touches Began` em um rótulo e altere a imagem.
    * _Dentro de DoubleTouchImage_ – altere a imagem exibida se o gesto fosse um toque duplo.
    * _Inside DragImage_ – defina um sinalizador que indica que o toque foi iniciado. O método `TouchesMoved` usará esse sinalizador para determinar se `DragImage` deve ser movido pela tela ou não, como veremos na próxima etapa.

    O código acima lida apenas com os toques individuais, mas ainda não há nenhum comportamento se o usuário estiver movendo o dedo na tela. Para responder ao movimento, implemente `TouchesMoved` conforme mostrado no código abaixo:

    ```csharp 
    public override void TouchesMoved(NSSet touches, UIEvent evt)
    {
        base.TouchesMoved(touches, evt);
        // get the touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            //==== IMAGE TOUCH
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                TouchStatus.Text = "Touches Moved";
            }

            //==== IMAGE DRAG
            // check to see if the touch started in the drag me image
            if (touchStartedInside)
            {
                // move the shape
                float offsetX = touch.PreviousLocationInView(View).X - touch.LocationInView(View).X;
                float offsetY = touch.PreviousLocationInView(View).Y - touch.LocationInView(View).Y;
                DragImage.Frame = new RectangleF(new PointF(DragImage.Frame.X - offsetX, DragImage.Frame.Y - offsetY), DragImage.Frame.Size);
            }
        }
    }
    ```

    Esse método obtém um `UITouch` objeto e, em seguida, verifica para ver onde o toque ocorreu. Se o toque ocorreu no `TouchImage`, então o texto tocará em movidos é exibido na tela. 

    Se `touchStartedInside` for true, sabemos que o usuário tem o dedo ligado `DragImage` e está movendo-o para o lugar. O código será movido `DragImage` conforme o usuário mover o dedo pela tela.

1. Precisamos lidar com o caso quando o usuário levanta seu dedo da tela ou o iOS cancela o evento de toque. Para isso, implementaremos `TouchesEnded` e `TouchesCancelled` conforme mostrado abaixo:

    ```csharp
    public override void TouchesCancelled(NSSet touches, UIEvent evt)
    {
        base.TouchesCancelled(touches, evt);
    
        // reset our tracking flags
        touchStartedInside = false;
        TouchImage.Image = UIImage.FromBundle("TouchMe.png");
        TouchStatus.Text = "";
    }
    
    public override void TouchesEnded(NSSet touches, UIEvent evt)
    {
        base.TouchesEnded(touches, evt);
        // get the touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            //==== IMAGE TOUCH
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                TouchImage.Image = UIImage.FromBundle("TouchMe.png");
                TouchStatus.Text = "Touches Ended";
            }
        }
        // reset our tracking flags
        touchStartedInside = false;
    }
    ```
    
    Ambos os métodos redefinirão o `touchStartedInside` sinalizador como false. `TouchesEnded`também será exibido `TouchesEnded` na tela.

1. Neste ponto, a tela de exemplos de toque é concluída. Observe como a tela muda à medida que você interage com cada uma das imagens, conforme mostrado na seguinte captura:
        
    [![](ios-touch-walkthrough-images/image4.png "A tela inicial do aplicativo")](ios-touch-walkthrough-images/image4.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image5.png "A tela depois que o usuário arrasta um botão")](ios-touch-walkthrough-images/image5.png#lightbox)
 

<a name="Gesture_Recognizer_Samples" />

## <a name="gesture-recognizer-samples"></a>Exemplos de reconhecedor de gesto

A [seção anterior](#Touch_Samples) demonstrou como arrastar um objeto pela tela usando eventos de toque.
Nesta seção, vamos livrar-se dos eventos de toque e mostrar como usar os seguintes reconhecedores de gesto:

- O `UIPanGestureRecognizer` para arrastar uma imagem pela tela.
- O `UITapGestureRecognizer` para responder a toques duplos na tela.

Se você executar o [código de exemplo inicial](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-start) e clicar no botão de exemplos do reconhecedor de **gesto** , você deverá ver a seguinte tela:

 [![](ios-touch-walkthrough-images/image6.png "Clicar no botão amostras do reconhecedor de gestos mostra esta tela")](ios-touch-walkthrough-images/image6.png#lightbox)

Siga estas etapas para implementar os reconhecedores de gesto:


1. Edite o arquivo **GestureViewController.cs** e adicione a seguinte variável de instância:

    ```csharp
    #region Private Variables
    private bool imageHighlighted = false;
    private RectangleF originalImageFrame = RectangleF.Empty;
    #endregion
    ```

    Precisamos dessa variável de instância para acompanhar o local anterior da imagem.
O reconhecedor de gesto de panorâmica `originalImageFrame` usará o valor para calcular o deslocamento necessário para redesenhar a imagem na tela.

1. Adicione o seguinte método ao controlador:

    ```csharp
    private void WireUpDragGestureRecognizer()
    {
        // Create a new tap gesture
        UIPanGestureRecognizer gesture = new UIPanGestureRecognizer();
    
        // Wire up the event handler (have to use a selector)
        gesture.AddTarget(() => HandleDrag(gesture));  // to be defined
    
        // Add the gesture recognizer to the view
        DragImage.AddGestureRecognizer(gesture);
    }
    ```

    Esse código instancia uma `UIPanGestureRecognizer` instância e a adiciona a uma exibição.
Observe que atribuímos um destino ao gesto na forma do método `HandleDrag` – esse método é fornecido na próxima etapa.

1. Para implementar o HandleDrag, adicione o seguinte código ao controlador:

    ```csharp
    private void HandleDrag(UIPanGestureRecognizer recognizer)
    {
        // If it's just began, cache the location of the image
        if (recognizer.State == UIGestureRecognizerState.Began)
        {
            originalImageFrame = DragImage.Frame;
        }
    
        // Move the image if the gesture is valid
        if (recognizer.State != (UIGestureRecognizerState.Cancelled | UIGestureRecognizerState.Failed
            | UIGestureRecognizerState.Possible))
        {
            // Move the image by adding the offset to the object's frame
            PointF offset = recognizer.TranslationInView(DragImage);
            RectangleF newFrame = originalImageFrame;
            newFrame.Offset(offset.X, offset.Y);
            DragImage.Frame = newFrame;
        }
    }
    ```

    O código acima verificará primeiro o estado do reconhecedor de gestos e, em seguida, moverá a imagem pela tela. Com esse código em vigor, o controlador agora pode dar suporte à arrastação de uma imagem em toda a tela.


1. Adicione um `UITapGestureRecognizer` que irá alterar a imagem que está sendo exibida em DoubleTouchImage. Adicione o seguinte método ao `GestureViewController` controlador:

    ```csharp
    private void WireUpTapGestureRecognizer()
    {
        // Create a new tap gesture
        UITapGestureRecognizer tapGesture = null;
    
        // Report touch
        Action action = () => {
            TouchStatus.Text = string.Format("Image touched at: {0}",tapGesture.LocationOfTouch(0, DoubleTouchImage));
    
            // Toggle the image
            if (imageHighlighted)
            {
                DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe.png");
            }
            else
            {
                DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe_Highlighted.png");
            }
            imageHighlighted = !imageHighlighted;
        };
    
        tapGesture = new UITapGestureRecognizer(action);
    
        // Configure it
        tapGesture.NumberOfTapsRequired = 2;
    
        // Add the gesture recognizer to the view
        DoubleTouchImage.AddGestureRecognizer(tapGesture);
    }
    ```

    Esse código é muito semelhante ao código para o `UIPanGestureRecognizer` , mas em vez de usar um delegado para um destino, estamos usando um. `Action` 

1. A última coisa que precisamos fazer é modificar `ViewDidLoad` para que chame os métodos que acabamos de adicionar. Altere ViewDidLoad para que seja semelhante ao seguinte código:

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
    
        Title = "Gesture Recognizers";
    
        // Save initial state
        originalImageFrame = DragImage.Frame;
    
        WireUpTapGestureRecognizer();
        WireUpDragGestureRecognizer();
    }
    ```

    Observe também que inicializamos o valor de `originalImageFrame`.


1. Execute o aplicativo e interaja com as duas imagens.
A captura de tela a seguir é um exemplo dessas interações:
    
    [![](ios-touch-walkthrough-images/image7.png "Esta captura de tela mostra uma interação de arrastar")](ios-touch-walkthrough-images/image7.png#lightbox)



<a name="Custom_Gesture_Recognizer"/>

## <a name="custom-gesture-recognizer"></a>Reconhecedor de gesto personalizado

Nesta seção, aplicaremos os conceitos de seções anteriores para criar um reconhecedor de gestos personalizado. O reconhecedor de gestor personalizado criará subclasses `UIGestureRecognizer`e reconhecerá quando o usuário desenha um "V" na tela e, em seguida, alterna um bitmap. A captura de tela a seguir é um exemplo disso:

 [![](ios-touch-walkthrough-images/image8.png "O aplicativo reconhecerá quando o usuário desenha um ' V ' na tela")](ios-touch-walkthrough-images/image8.png#lightbox)

Siga estas etapas para criar um reconhecedor de gestos personalizado:


1. Adicione uma nova classe ao projeto chamado `CheckmarkGestureRecognizer`e faça com que ele se pareça com o código a seguir:

    ```csharp
    using System;
    using CoreGraphics;
    using Foundation;
    using UIKit;
    
    namespace Touch
    {
        public class CheckmarkGestureRecognizer : UIGestureRecognizer
        {
            #region Private Variables
            private CGPoint midpoint = CGPoint.Empty;
            private bool strokeUp = false;
            #endregion
    
            #region Override Methods
            /// <summary>
            ///   Called when the touches end or the recognizer state fails
            /// </summary>
            public override void Reset()
            {
                base.Reset();
    
                strokeUp = false;
                midpoint = CGPoint.Empty;
            }
    
            /// <summary>
            ///   Is called when the fingers touch the screen.
            /// </summary>
            public override void TouchesBegan(NSSet touches, UIEvent evt)
            {
                base.TouchesBegan(touches, evt);
    
                // we want one and only one finger
                if (touches.Count != 1)
                {
                    base.State = UIGestureRecognizerState.Failed;
                }
    
                Console.WriteLine(base.State.ToString());
            }
    
            /// <summary>
            ///   Called when the touches are cancelled due to a phone call, etc.
            /// </summary>
            public override void TouchesCancelled(NSSet touches, UIEvent evt)
            {
                base.TouchesCancelled(touches, evt);
                // we fail the recognizer so that there isn't unexpected behavior
                // if the application comes back into view
                base.State = UIGestureRecognizerState.Failed;
            }
    
            /// <summary>
            ///   Called when the fingers lift off the screen
            /// </summary>
            public override void TouchesEnded(NSSet touches, UIEvent evt)
            {
                base.TouchesEnded(touches, evt);
                //
                if (base.State == UIGestureRecognizerState.Possible && strokeUp)
                {
                    base.State = UIGestureRecognizerState.Recognized;
                }
    
                Console.WriteLine(base.State.ToString());
            }
    
            /// <summary>
            ///   Called when the fingers move
            /// </summary>
            public override void TouchesMoved(NSSet touches, UIEvent evt)
            {
                base.TouchesMoved(touches, evt);
    
                // if we haven't already failed
                if (base.State != UIGestureRecognizerState.Failed)
                {
                    // get the current and previous touch point
                    CGPoint newPoint = (touches.AnyObject as UITouch).LocationInView(View);
                    CGPoint previousPoint = (touches.AnyObject as UITouch).PreviousLocationInView(View);
    
                    // if we're not already on the upstroke
                    if (!strokeUp)
                    {
                        // if we're moving down, just continue to set the midpoint at
                        // whatever point we're at. when we start to stroke up, it'll stick
                        // as the last point before we upticked
                        if (newPoint.X >= previousPoint.X && newPoint.Y >= previousPoint.Y)
                        {
                            midpoint = newPoint;
                        }
                        // if we're stroking up (moving right x and up y [y axis is flipped])
                        else if (newPoint.X >= previousPoint.X && newPoint.Y <= previousPoint.Y)
                        {
                            strokeUp = true;
                        }
                        // otherwise, we fail the recognizer
                        else
                        {
                            base.State = UIGestureRecognizerState.Failed;
                        }
                    }
                }
    
                Console.WriteLine(base.State.ToString());
            }
            #endregion
        }
    }
    ```

    O método Reset é chamado quando a `State` propriedade é alterada `Recognized` para ou `Ended`. Esse é o momento de redefinir qualquer estado interno definido no reconhecedor de gestos personalizado.
Agora, a classe pode começar na próxima vez que o usuário interage com o aplicativo e estar pronta para tentar reconhecê-lo.



1. Agora que definimos um reconhecedor de gestos`CheckmarkGestureRecognizer`personalizado (), edite o arquivo **CustomGestureViewController.cs** e adicione as duas variáveis de instância a seguir:

    ```csharp
    #region Private Variables
    private bool isChecked = false;
    private CheckmarkGestureRecognizer checkmarkGesture;
    #endregion
    ```

1. Para criar uma instância e configurar nosso reconhecedor de gestos, adicione o seguinte método ao controlador:

    ```csharp
    private void WireUpCheckmarkGestureRecognizer()
    {
        // Create the recognizer
        checkmarkGesture = new CheckmarkGestureRecognizer();
    
        // Wire up the event handler
        checkmarkGesture.AddTarget(() => {
            if (checkmarkGesture.State == (UIGestureRecognizerState.Recognized | UIGestureRecognizerState.Ended))
            {
                if (isChecked)
                {
                    CheckboxImage.Image = UIImage.FromBundle("CheckBox_Unchecked.png");
                }
                else
                {
                    CheckboxImage.Image = UIImage.FromBundle("CheckBox_Checked.png");
                }
                isChecked = !isChecked;
            }
        });
    
        // Add the gesture recognizer to the view
        View.AddGestureRecognizer(checkmarkGesture);
    }
    ```

1. Edite `ViewDidLoad` para que ele `WireUpCheckmarkGestureRecognizer`chame, conforme mostrado no seguinte trecho de código:

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
    
        // Wire up the gesture recognizer
        WireUpCheckmarkGestureRecognizer();
    }
    ```

1. Execute o aplicativo e tente desenhar um "V" na tela. Você deve ver a imagem sendo exibida, conforme mostrado nas seguintes capturas de tela:
    
    [![](ios-touch-walkthrough-images/image9.png "O botão marcado")](ios-touch-walkthrough-images/image9.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image10.png "O botão está desmarcado")](ios-touch-walkthrough-images/image10.png#lightbox)



As três seções acima demonstraram diferentes maneiras de responder a eventos de toque no iOS: usando eventos de toque, reconhecedores de gestos internos ou com um reconhecedor de gestos personalizado.



## <a name="related-links"></a>Links relacionados

- [Início do iOS Touch (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-start)
- [Final do iOS Touch (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-final)
