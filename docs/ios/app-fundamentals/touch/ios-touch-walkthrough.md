---
title: 'Passo a passo: Usando toque em xamarin'
description: Este documento descreve como lidar com o toque em aplicativos xamarin, discutindo as interações de toque de exemplo e reconhecedores de gestos reconhecedores de gestos personalizados.
ms.prod: xamarin
ms.assetid: 13F8289B-7A80-4959-AF3F-57874D866DCA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: fff49599d3843bb09d407316d6964ca54b6a1004
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784784"
---
# <a name="walkthrough-using-touch-in-xamarinios"></a>Passo a passo: Usando toque em xamarin

Este passo a passo demonstra como escrever código que responde a diferentes tipos de eventos de toque. Cada exemplo está contido em uma tela separada:

- [Exemplos de toque](#Touch_Samples) – como responder aos eventos de toque.
- [Exemplos do reconhecedor de gestos](#Gesture_Recognizer_Samples) – como usar reconhecedores de gestos internos.
- [Exemplo do reconhecedor de gestos personalizado](#Custom_Gesture_Recognizer) – como compilar um reconhecedor de gestos personalizados.

Cada seção contém instruções para escrever o código do zero.
O [iniciando o código de exemplo](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start) já inclui uma tela de storyboard e menu concluída:

 [![](ios-touch-walkthrough-images/image3.png "O exemplo inclui a tela de menu")](ios-touch-walkthrough-images/image3.png#lightbox)

Siga as instruções abaixo para adicionar código para o storyboard e saiba mais sobre os diferentes tipos de eventos de toque disponíveis no iOS. Como alternativa, abra o [exemplo finalizado](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final) para ver tudo funcionar.

<a name="Touch_Samples"/>

## <a name="touch-samples"></a>Exemplos de toque

Neste exemplo, demonstraremos algumas das APIs de toque. Siga estas etapas para adicionar o código necessário para implementar eventos de toque:


1. Abra o projeto **Touch_Start**. Primeiro execute o projeto para verificar se tudo está okey e toque o **exemplos Touch** botão. (Embora nenhum dos botões funcionará), você verá uma tela semelhante à seguinte:
    
    [![](ios-touch-walkthrough-images/image4.png "Aplicativo de exemplo executar com os botões de folga")](ios-touch-walkthrough-images/image4.png#lightbox)


1. Edite o arquivo **TouchViewController.cs** e adicione as seguintes variáveis de duas instância à classe `TouchViewController`:

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
    
    Esse método funciona através da verificação de um `UITouch` de objeto e se ele existe executam alguma ação com base no qual ocorreu o toque:

    * _Dentro de TouchImage_ – exibir o texto `Touches Began` em um rótulo e mudar a imagem.
    * _Dentro de DoubleTouchImage_ – alterar a imagem exibida se o gesto foi um toque duplo.
    * _Dentro de DragImage_ – definir um sinalizador que indica que o toque foi iniciado. O método `TouchesMoved` usará esse sinalizador para determinar se `DragImage` devem ser movidos pela tela ou não, como podemos ver na próxima etapa.

    O código acima lida apenas com ajustes individuais, ainda há um comportamento se o usuário está movendo o dedo na tela. Para responder a movimentação, implementar `TouchesMoved` conforme mostrado no código a seguir:

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

    Esse método obtém um `UITouch` objeto e, em seguida, verifica onde ocorreu o toque. Se o toque ocorreu no `TouchImage`, em seguida, o texto movido de toques é exibido na tela. 

    Se `touchStartedInside` for true, sabemos que o usuário tem o dedo no `DragImage` e movê-lo ao redor. O código moverá `DragImage` como o usuário passa o dedo na tela.

1. Precisamos tratar o caso quando o usuário levante seu dedo na tela ou iOS cancela o evento de toque. Para isso, vamos implementar `TouchesEnded` e `TouchesCancelled` conforme mostrado abaixo:

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

##  <a name="gesture-recognizer-samples"></a>Exemplos do reconhecedor de gesto

O [seção anterior](#Touch_Samples) demonstrou como arrastar um objeto na tela usando os eventos de toque.
Nesta seção eliminará os eventos de toque e mostram como usar os seguintes identificadores de gesto:

-  O `UIPanGestureRecognizer` para arrastar uma imagem de tela.
-  O `UITapGestureRecognizer` para responder a toques duplas na tela.

Se você executar o [iniciando o código de exemplo](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start) e clique no **exemplos do reconhecedor de gesto** botão, você verá a tela a seguir:

 [![](ios-touch-walkthrough-images/image6.png "Clicando no botão de exemplos do reconhecedor de gesto mostra esta tela")](ios-touch-walkthrough-images/image6.png#lightbox)

Siga estas etapas para implementar reconhecedores de gestos:


1. Edite o arquivo **GestureViewController.cs** e adicione a seguinte variável da instância:

    ```csharp
    #region Private Variables
    private bool imageHighlighted = false;
    private RectangleF originalImageFrame = RectangleF.Empty;
    #endregion
    ```

    Precisamos essa variável de instância para controlar o local anterior da imagem.
O reconhecedor de gestos panorâmica usará o `originalImageFrame` para calcular o deslocamento deve redesenhar a imagem na tela.

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

    Esse código cria um `UIPanGestureRecognizer` de instância e adiciona-o a um modo de exibição.
Observe que nós atribuímos um destino para o gesto na forma do método `HandleDrag` – esse método é fornecido na próxima etapa.

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

    O código acima primeiro verificar o estado do reconhecedor de gestos e, em seguida, mover a imagem pela tela. Com esse código, o controlador pode suportar agora arrastando uma imagem de tela.


1. Adicionar um `UITapGestureRecognizer` que alterará a imagem que está sendo exibida no DoubleTouchImage. Adicione o seguinte método para o `GestureViewController` controlador:

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

1. A final coisa que precisamos fazer é modificar `ViewDidLoad` para que ele chama os métodos que acabamos de adicionar. Alterar ViewDidLoad para que ele se parece com o código a seguir:

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

    Observe também que podemos inicializar o valor de `originalImageFrame`.


1. Execute o aplicativo e interagir com as duas imagens.
Captura de tela a seguir é um exemplo dessas interações:
    
    [![](ios-touch-walkthrough-images/image7.png "Esta captura de tela mostra uma interação de arrastar")](ios-touch-walkthrough-images/image7.png#lightbox)



<a name="Custom_Gesture_Recognizer"/>

## <a name="custom-gesture-recognizer"></a>Reconhecedor de gestos personalizados

Esta seção aplicaremos os conceitos das seções anteriores para criar um reconhecedor de gestos personalizados. O reconhecedor de gestos personalizados serão subclasses `UIGestureRecognizer`e reconhecer quando o usuário desenha um "V" na tela e alternar um bitmap. Captura de tela a seguir está um exemplo dessa tela:

 [![](ios-touch-walkthrough-images/image8.png "O aplicativo reconhecerá quando o usuário desenha um V na tela")](ios-touch-walkthrough-images/image8.png#lightbox)

Siga estas etapas para criar um reconhecedor de gestos personalizados:


1. Adicionar uma nova classe ao projeto nomeado `CheckmarkGestureRecognizer`e torná-lo a se parecer com o código a seguir:

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

    O método de reinicialização é chamado quando o `State` propriedade muda para um `Recognized` ou `Ended`. Este é o tempo para redefinir a qualquer estado interno definido no reconhecedor de gestos personalizados.
Agora a classe pode começar do zero próxima vez que o usuário interage com o aplicativo e estará pronto para tentar novamente a reconhecer o gesto.



1. Agora que definimos um reconhecedor de gestos personalizados (`CheckmarkGestureRecognizer`) edite o **CustomGestureViewController.cs** e adicione as seguintes variáveis de dois instância:

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

1. Editar `ViewDidLoad` para que ele chama `WireUpCheckmarkGestureRecognizer`, conforme mostrado no trecho de código a seguir:

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
    
        // Wire up the gesture recognizer
        WireUpCheckmarkGestureRecognizer();
    }
    ```

1. Execute o aplicativo e tente desenhar um "V" na tela. Você deve ver que a imagem que está sendo exibido alteração, conforme mostrado nas capturas de tela seguir:
    
    [![](ios-touch-walkthrough-images/image9.png "O botão marcado")](ios-touch-walkthrough-images/image9.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image10.png "O botão não verificado")](ios-touch-walkthrough-images/image10.png#lightbox)



Acima três seções demonstradas maneiras diferentes para responder a eventos no iOS de toque: usar eventos de toque, reconhecedores de gestos internas, ou com um reconhecedor de gestos personalizados.



## <a name="related-links"></a>Links relacionados

- [iOS Touch iniciar (exemplo)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start)
- [iOS toque Final (exemplo)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final)
