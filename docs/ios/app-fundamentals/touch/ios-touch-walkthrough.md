---
title: 'Passo a passo: Usar toque no xamarin. IOS'
description: Este documento descreve como lidar com o toque em aplicativos xamarin. IOS, discutindo as interações de toque de amostra, reconhecedores de gestos e reconhecedores de gestos personalizados.
ms.prod: xamarin
ms.assetid: 13F8289B-7A80-4959-AF3F-57874D866DCA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: bff4d46ac9d5fe893cbb0a2dfa032e1b9f6daa0e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121549"
---
# <a name="walkthrough-using-touch-in-xamarinios"></a>Passo a passo: Usar toque no xamarin. IOS

Este passo a passo demonstra como escrever código que responde a diferentes tipos de eventos de toque. Cada exemplo está contido em uma tela separada:

- [Exemplos de toque](#Touch_Samples) – como responder a eventos de toque.
- [Exemplos do reconhecedor de gestos](#Gesture_Recognizer_Samples) – como usar os reconhecedores de gestos internos.
- [Amostra do reconhecedor de gestos personalizado](#Custom_Gesture_Recognizer) – como compilar um reconhecedor de gestos personalizados.

Cada seção contém instruções para escrever o código do zero.
O [iniciando o código de exemplo](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start) já inclui uma tela de storyboard e menu concluída:

 [![](ios-touch-walkthrough-images/image3.png "O exemplo inclui a tela do menu")](ios-touch-walkthrough-images/image3.png#lightbox)

Siga as instruções abaixo para adicionar código ao storyboard e saiba mais sobre os diferentes tipos de eventos de toque disponíveis no iOS. Como alternativa, abra o [exemplo finalizado](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final) para ver tudo funcionar.

<a name="Touch_Samples"/>

## <a name="touch-samples"></a>Exemplos de toque

Neste exemplo, vamos demonstrar algumas das APIs de toque. Siga estas etapas para adicionar o código necessário para implementar eventos de toque:


1. Abra o projeto **Touch_Start**. Primeiro execute o projeto para garantir que tudo esteja okey e toque a **Touch exemplos** botão. (Embora nenhum dos botões funcionará), você verá uma tela semelhante à seguinte:
    
    [![](ios-touch-walkthrough-images/image4.png "Execução com botões de folga do aplicativo de exemplo")](ios-touch-walkthrough-images/image4.png#lightbox)


1. Edite o arquivo **TouchViewController.cs** e adicione as seguintes variáveis de instância de dois para a classe `TouchViewController`:

    ```csharp 
    #region Private Variables
    private bool imageHighlighted = false;
    private bool touchStartedInside;
    #endregion
    ```


1. Implementar o `TouchesBegan` método, conforme mostrado no código a seguir:

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
    
    Esse método funciona através da verificação de um `UITouch` de objeto e se ele existir realizar alguma ação com base em onde ocorreu o toque:

    * _Dentro de TouchImage_ – exibir o texto `Touches Began` em um rótulo e alterar a imagem.
    * _Dentro de DoubleTouchImage_ – alterar a imagem exibida se o gesto foi um toque duplo.
    * _Dentro de DragImage_ – definir um sinalizador que indica que o toque foi iniciada. O método `TouchesMoved` usará esse sinalizador para determinar se `DragImage` deve ser movido na tela ou não, como veremos na próxima etapa.

    O código acima lida apenas com toques individuais, há ainda um comportamento se o usuário está movendo o dedo na tela. Para responder a movimentação, implementar `TouchesMoved` conforme mostrado no código a seguir:

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

    Esse método obtém uma `UITouch` objeto e, em seguida, verifica para ver onde ocorreu a interatividade. Se o toque ocorreu no `TouchImage`, em seguida, o texto movido de toques é exibido na tela. 

    Se `touchStartedInside` for true, sabemos que o usuário tem seu dedo no `DragImage` e movê-lo ao redor. O código será movido `DragImage` conforme o usuário move o dedo pela tela.

1. Precisamos manipular o caso quando o usuário levanta seu dedo fora da tela ou iOS cancela o evento de toque. Para isso, vamos implementar `TouchesEnded` e `TouchesCancelled` conforme mostrado abaixo:

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
    
    Ambos os métodos redefinirá o `touchStartedInside` sinalizador como false. `TouchesEnded` também será exibição `TouchesEnded` na tela.

1. Neste momento, a tela Touch exemplos é concluída. Observe como a tela muda conforme você interage com cada uma das imagens, conforme mostrado na seguinte captura de tela:
        
    [![](ios-touch-walkthrough-images/image4.png "A tela inicial do aplicativo")](ios-touch-walkthrough-images/image4.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image5.png "A tela depois que o usuário arrasta um botão")](ios-touch-walkthrough-images/image5.png#lightbox)
 

<a name="Gesture_Recognizer_Samples" />

##  <a name="gesture-recognizer-samples"></a>Exemplos do reconhecedor de gestos

O [seção anterior](#Touch_Samples) demonstrou como arrastar um objeto na tela usando eventos de toque.
Nesta seção eliminará os eventos de toque e mostram como usar os reconhecedores de gestos a seguir:

-  O `UIPanGestureRecognizer` sobre o arraste de uma imagem pela tela.
-  O `UITapGestureRecognizer` para responder aos toques duplos na tela.

Se você executar o [iniciando o código de exemplo](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start) e clique no **amostras do reconhecedor de gestos** botão, você deverá ver a tela a seguir:

 [![](ios-touch-walkthrough-images/image6.png "Clicando no botão de exemplos do reconhecedor de gestos mostra esta tela")](ios-touch-walkthrough-images/image6.png#lightbox)

Siga estas etapas para implementar os reconhecedores de gestos:


1. Edite o arquivo **GestureViewController.cs** e adicione a seguinte variável de instância:

    ```csharp
    #region Private Variables
    private bool imageHighlighted = false;
    private RectangleF originalImageFrame = RectangleF.Empty;
    #endregion
    ```

    É necessário que essa variável de instância para controlar o local anterior da imagem.
O reconhecedor de gestos de panorâmica usará o `originalImageFrame` valor para calcular a compensação exigida para redesenhar a imagem na tela.

1. Adicione o seguinte método para o controlador:

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

    Esse código instancia uma `UIPanGestureRecognizer` da instância e adiciona-o para um modo de exibição.
Observe que podemos atribuir um destino para o gesto na forma do método `HandleDrag` – este método é fornecido na próxima etapa.

1. Para implementar HandleDrag, adicione o seguinte código para o controlador:

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

    O código acima será primeiro, verifique o estado do reconhecedor de gestos e, em seguida, mover a imagem pela tela. Com esse código em vigor, o controlador pode agora dar suporte a arrastar uma imagem pela tela.


1. Adicionar um `UITapGestureRecognizer` que irá alterar a imagem que está sendo exibida no DoubleTouchImage. Adicione o seguinte método para o `GestureViewController` controlador:

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

    Esse código é muito semelhante ao código para o `UIPanGestureRecognizer` , mas em vez de usar um delegado para um destino que estamos usando um `Action`. 

1. A última tarefa que precisamos fazer é modificar `ViewDidLoad` para que ele chama os métodos que acabamos de adicionar. Alterar ViewDidLoad para que ele fique parecido com o código a seguir:

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


1. Execute o aplicativo e interagir com as duas imagens.
Captura de tela a seguir é um exemplo dessas interações:
    
    [![](ios-touch-walkthrough-images/image7.png "Esta captura de tela mostra uma interação de arrastar")](ios-touch-walkthrough-images/image7.png#lightbox)



<a name="Custom_Gesture_Recognizer"/>

## <a name="custom-gesture-recognizer"></a>Reconhecedor de gestos personalizados

Nesta seção, aplicaremos os conceitos das seções anteriores para criar um reconhecedor de gestos personalizados. O reconhecedor de gestos personalizados serão subclasses `UIGestureRecognizer`e irá reconhecer quando o usuário desenha um "V" na tela e em seguida, alternar de um bitmap. Captura de tela a seguir está um exemplo dessa tela:

 [![](ios-touch-walkthrough-images/image8.png "O aplicativo reconheça quando o usuário desenha um \"V\" na tela")](ios-touch-walkthrough-images/image8.png#lightbox)

Siga estas etapas para criar um reconhecedor de gestos personalizados:


1. Adicione uma nova classe ao projeto nomeado `CheckmarkGestureRecognizer`e torná-lo a se parecer com o código a seguir:

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

    O método Reset é chamado quando o `State` propriedade é alterada para um `Recognized` ou `Ended`. Isso é o tempo para redefinir o estado interno definido no reconhecedor de gestos personalizados.
Agora a classe pode começar do zero próxima vez que o usuário interage com o aplicativo e estar pronto para tentar novamente a reconhecer o gesto.



1. Agora que definimos um reconhecedor de gestos personalizada (`CheckmarkGestureRecognizer`) edite o **CustomGestureViewController.cs** arquivo e adicione as seguintes variáveis de dois instância:

    ```csharp
    #region Private Variables
    private bool isChecked = false;
    private CheckmarkGestureRecognizer checkmarkGesture;
    #endregion
    ```

1. Para criar uma instância e configurar nosso reconhecedor de gestos, adicione o seguinte método para o controlador:

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

1. Edite `ViewDidLoad` para que ele chame `WireUpCheckmarkGestureRecognizer`, conforme mostrado no trecho de código a seguir:

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
    
        // Wire up the gesture recognizer
        WireUpCheckmarkGestureRecognizer();
    }
    ```

1. Execute o aplicativo e tente "V" de desenho na tela. Você deve ver que a imagem que está sendo exibido a alteração, conforme mostrado nas capturas de tela seguir:
    
    [![](ios-touch-walkthrough-images/image9.png "O botão check")](ios-touch-walkthrough-images/image9.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image10.png "O botão não verificado")](ios-touch-walkthrough-images/image10.png#lightbox)



As opções acima três seções demonstradas diferentes maneiras de responder ao toque eventos no iOS: usando eventos de toque, reconhecedores de gestos interno, ou com um reconhecedor de gestos personalizados.



## <a name="related-links"></a>Links relacionados

- [iOS Touch iniciar (amostra)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start)
- [iOS toque Final (amostra)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final)
