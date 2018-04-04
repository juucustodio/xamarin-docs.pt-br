---
title: Passo a passo - usando toque no Android
ms.prod: xamarin
ms.assetid: E281F89B-4142-4BD8-8882-FB65508BF69E
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/02/2018
ms.openlocfilehash: ae4d5b0b8cd384a11d130bc9258894e1cf4cfa36
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="walkthrough---using-touch-in-android"></a>Passo a passo - usando toque no Android

Vamos ver como usar os conceitos da seção anterior em um aplicativo de trabalho. Criaremos um aplicativo com quatro atividades. A primeira atividade será um menu ou um menu de controle que iniciará as outras atividades para demonstrar as várias APIs. Captura de tela a seguir mostra a atividade principal:

[![Captura de tela de exemplo com toque Me botão](android-touch-walkthrough-images/image14.png)](android-touch-walkthrough-images/image14.png#lightbox)

A primeira atividade, o exemplo de toque, mostrará como usar manipuladores de eventos de tocar os modos de exibição. A atividade do reconhecedor de gestos demonstrará como subclasse `Android.View.Views` e manipular eventos, bem como mostram como lidar com gestos de aperto. A terceira e última atividade, **gesto personalizado**, irá mostrar como usar gestos personalizados. Para facilitar as coisas a seguir e absorver, podemos irá dividir este passo a passo em seções, com cada seção concentrando-se em uma das atividades.

## <a name="touch-sample-activity"></a>Exemplo de atividade de toque

-   Abra o projeto **TouchWalkthrough\_iniciar**. O **MainActivity** está configurado para ir &ndash; depende para implementar o comportamento de toque na atividade. Se você executar o aplicativo e clique em **exemplo Touch**, a seguinte atividade deve ser iniciado:

    [![Captura de tela de atividade com toque começa exibido](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

-   Agora que Confirmamos que a atividade é iniciado, abra o arquivo **TouchActivity.cs** e adicione um manipulador para o `Touch` evento o `ImageView`:

    ```csharp
    _touchMeImageView.Touch += TouchMeImageViewOnTouch;
    ```

-   Em seguida, adicione o seguinte método à **TouchActivity.cs**:

    ```csharp
    private void TouchMeImageViewOnTouch(object sender, View.TouchEventArgs touchEventArgs)
    {
        string message;
        switch (touchEventArgs.Event.Action & MotionEventArgs.Mask)
        {
            case MotionEventActions.Down:
            case MotionEventActions.Move:
            message = "Touch Begins";
            break;

            case MotionEventActions.Up:
            message = "Touch Ends";
            break;

            default:
            message = string.Empty;
            break;
        }

        _touchInfoTextView.Text = message;
    }
    ```

Observe, no código acima, que será tratado o `Move` e `Down` ação como a mesma. Isso ocorre porque, embora o usuário pode não Levante o dedo o `ImageView`, ele pode mover-se ou a pressão exercida pelo usuário pode ser alteradas. Esses tipos de alterações irá gerar um `Move` ação.

Cada vez que o usuário toques a `ImageView`, o `Touch` evento será gerado e nosso manipulador exibirá a mensagem **Touch começa** na tela, conforme mostrado na seguinte captura de tela:

[![Captura de tela de atividade com toque começa](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

Desde que o usuário estiver tocando os `ImageView`, **Touch começa** será exibido no `TextView`. Quando o usuário não estiver tocando os `ImageView`, a mensagem **Touch termina** será exibido no `TextView`, conforme mostrado na seguinte captura de tela:

[![Captura de tela de atividade com toque termina](android-touch-walkthrough-images/image16.png)](android-touch-walkthrough-images/image16.png#lightbox)


## <a name="gesture-recognizer-activity"></a>Atividade do reconhecedor de gesto

Agora, vamos implementar a atividade do reconhecedor de gestos. Essa atividade demonstrará como arrastar uma exibição na tela e ilustra uma maneira de implementar Pince para aplicar zoom.

-   Adicionar uma nova atividade para o aplicativo chamado `GestureRecognizer`.
    Edite o código para essa atividade para que ele se parece com o código a seguir:

    ```csharp
    public class GestureRecognizerActivity : Activity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            View v = new GestureRecognizerView(this);
            SetContentView(v);
        }
    }
    ```

-   Adicionar um novo Android exibir ao projeto e o nome de `GestureRecognizerView`. Adicione as seguintes variáveis para esta classe:

    ```csharp
    private static readonly int InvalidPointerId = -1;

    private readonly Drawable _icon;
    private readonly ScaleGestureDetector _scaleDetector;

    private int _activePointerId = InvalidPointerId;
    private float _lastTouchX;
    private float _lastTouchY;
    private float _posX;
    private float _posY;
    private float _scaleFactor = 1.0f;
    ```

-   Adicione o seguinte construtor para `GestureRecognizerView`. Este construtor adicionará um `ImageView` a nossa atividade. Neste momento da compilação do código ainda não &ndash; é necessário criar a classe `MyScaleListener` que ajudará com redimensionando o `ImageView` quando o usuário pinches:

    ```csharp
    public GestureRecognizerView(Context context): base(context, null, 0)
    {
        _icon = context.Resources.GetDrawable(Resource.Drawable.Icon);
        _icon.SetBounds(0, 0, _icon.IntrinsicWidth, _icon.IntrinsicHeight);
        _scaleDetector = new ScaleGestureDetector(context, new MyScaleListener(this));
    }
    ```

-   Para desenhar a imagem em nossa atividade, é preciso substituir o `OnDraw` método da classe de exibição, conforme mostrado no trecho a seguir. Esse código moverá o `ImageView` para a posição especificada pelo `_posX` e `_posY` , bem como redimensionar a imagem de acordo com o fator de escala:

    ```csharp
    protected override void OnDraw(Canvas canvas)
    {
        base.OnDraw(canvas);
        canvas.Save();
        canvas.Translate(_posX, _posY);
        canvas.Scale(_scaleFactor, _scaleFactor);
        _icon.Draw(canvas);
        canvas.Restore();
    }
    ```

-   Em seguida, precisamos atualizar a variável de instância `_scaleFactor` como o usuário pinches a `ImageView`. Vamos adicionar uma classe chamada `MyScaleListener`. Essa classe escutará eventos de escala que será gerado pelo Android quando o usuário pinches a `ImageView`.
    Adicione a seguinte classe interna para `GestureRecognizerView`. Essa classe é um `ScaleGesture.SimpleOnScaleGestureListener`. Essa classe é uma classe de conveniência que ouvintes podem subclasse quando você estiver interessado em um subconjunto de gestos:

    ```csharp
    private class MyScaleListener : ScaleGestureDetector.SimpleOnScaleGestureListener
    {
        private readonly GestureRecognizerView _view;

        public MyScaleListener(GestureRecognizerView view)
        {
            _view = view;
        }

        public override bool OnScale(ScaleGestureDetector detector)
        {
            _view._scaleFactor *= detector.ScaleFactor;

            // put a limit on how small or big the image can get.
            if (_view._scaleFactor > 5.0f)
            {
                _view._scaleFactor = 5.0f;
            }
            if (_view._scaleFactor < 0.1f)
            {
                _view._scaleFactor = 0.1f;
            }

            _iconview.Invalidate();
            return true;
        }
    }
    ```

-   O próximo método precisamos substituir em `GestureRecognizerView` é `OnTouchEvent`. O código a seguir lista a implementação completa desse método. Há muitos códigos aqui, assim, reserve um minuto e veja o que está acontecendo aqui. A primeira coisa que esse método não é dimensionar o ícone se necessário &ndash; isso é tratado chamando `_scaleDetector.OnTouchEvent`. Em seguida, tentar descobrir ação chamado esse método:

    - Se o usuário tocadas à tela, podemos gravar as posições de X e Y e a ID do primeiro ponteiro que tocadas a tela.

    - Se o usuário moveu o toque na tela, podemos descobrir quanto o usuário moveu o ponteiro.

    - Se o usuário foi eliminada seu dedo na tela, em seguida, vamos parar os gestos de controle.

    ```csharp
    public override bool OnTouchEvent(MotionEvent ev)
    {
        _scaleDetector.OnTouchEvent(ev);

        MotionEventActions action = ev.Action & MotionEventActions.Mask;
        int pointerIndex;

        switch (action)
        {
            case MotionEventActions.Down:
            _lastTouchX = ev.GetX();
            _lastTouchY = ev.GetY();
            _activePointerId = ev.GetPointerId(0);
            break;

            case MotionEventActions.Move:
            pointerIndex = ev.FindPointerIndex(_activePointerId);
            float x = ev.GetX(pointerIndex);
            float y = ev.GetY(pointerIndex);
            if (!_scaleDetector.IsInProgress)
            {
                // Only move the ScaleGestureDetector isn't already processing a gesture.
                float deltaX = x - _lastTouchX;
                float deltaY = y - _lastTouchY;
                _posX += deltaX;
                _posY += deltaY;
                Invalidate();
            }

            _lastTouchX = x;
            _lastTouchY = y;
            break;

            case MotionEventActions.Up:
            case MotionEventActions.Cancel:
            // We no longer need to keep track of the active pointer.
            _activePointerId = InvalidPointerId;
            break;

            case MotionEventActions.PointerUp:
            // check to make sure that the pointer that went up is for the gesture we're tracking.
            pointerIndex = (int) (ev.Action & MotionEventActions.PointerIndexMask) >> (int) MotionEventActions.PointerIndexShift;
            int pointerId = ev.GetPointerId(pointerIndex);
            if (pointerId == _activePointerId)
            {
                // This was our active pointer going up. Choose a new
                // action pointer and adjust accordingly
                int newPointerIndex = pointerIndex == 0 ? 1 : 0;
                _lastTouchX = ev.GetX(newPointerIndex);
                _lastTouchY = ev.GetY(newPointerIndex);
                _activePointerId = ev.GetPointerId(newPointerIndex);
            }
            break;

        }
        return true;
    }
    ```

-   Agora execute o aplicativo e iniciar a atividade do reconhecedor de gestos.
    Ao iniciar a tela deve ser semelhante a captura de tela abaixo:

    [![Tela de início de reconhecedor de gestos com ícone de Android](android-touch-walkthrough-images/image17.png)](android-touch-walkthrough-images/image17.png#lightbox)

-   Agora o ícone de toque e arraste-o pela tela. Tente o gesto de pinçagem para aplicar zoom. Em algum momento sua tela deve ser semelhante a captura de tela a seguir:

    [![Ícone de movimentação de gestos na tela](android-touch-walkthrough-images/image18.png)](android-touch-walkthrough-images/image18.png#lightbox)

Neste ponto, abra um pat na parte traseira: você implementou apenas Pince para aplicar zoom em um aplicativo do Android! Faça uma pausa rápida e permite passar para a terceira e última atividade neste passo a passo &ndash; usando gestos personalizados.

## <a name="custom-gesture-activity"></a>Atividade de gesto personalizado

Tela final neste passo a passo irá usar gestos personalizados.

Para os fins deste passo a passo, a biblioteca de gestos já foi criada usando a ferramenta de gesto e adicionada ao projeto no arquivo **recursos/bruto/gestos**. Com este trecho de limpeza do caminho, permite obter em com a atividade final no passo a passo.

-   Adicione um arquivo de layout denominado **personalizado\_gesto\_layout.axml** para o projeto com o seguinte conteúdo. O projeto já tem todas as imagens no **recursos** pasta:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:orientation="vertical"
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1" />
        <ImageView
            android:src="@drawable/check_me"
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="3"
            android:id="@+id/imageView1"
            android:layout_gravity="center_vertical" />
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1" />
    </LinearLayout>
    ```

-   Em seguida, adicionar uma nova atividade para o projeto e nomeie-o `CustomGestureRecognizerActivity.cs`. Adicione duas variáveis de instância para a classe, como mostrando nas duas linhas de código a seguir:

    ```csharp
    private GestureLibrary _gestureLibrary;
    private ImageView _imageView;
    ```

-   Editar o `OnCreate` método dessa atividade para que ela se parece com o código a seguir. Permite levar um minuto para explicar o que está acontecendo nesse código. A primeira coisa que podemos fazer é criar uma instância de um `GestureOverlayView` e defina-o como o modo de exibição de raiz da atividade.
    Também atribuímos um manipulador de eventos para o `GesturePerformed` evento `GestureOverlayView`. Em seguida, aumentar o arquivo de layout que foi criado anteriormente e adicioná-la como uma exibição de filho do `GestureOverlayView`. A etapa final é inicializar a variável `_gestureLibrary` e carregar o arquivo de gestos de recursos do aplicativo. Se o arquivo de gestos não pode ser carregado por algum motivo, não há muito essa atividade pode fazer, portanto, é o desligamento:

    ```csharp
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);

        GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
        SetContentView(gestureOverlayView);
        gestureOverlayView.GesturePerformed += GestureOverlayViewOnGesturePerformed;

        View view = LayoutInflater.Inflate(Resource.Layout.custom_gesture_layout, null);
        _imageView = view.FindViewById<ImageView>(Resource.Id.imageView1);
        gestureOverlayView.AddView(view);

        _gestureLibrary = GestureLibraries.FromRawResource(this, Resource.Raw.gestures);
        if (!_gestureLibrary.Load())
        {
            Log.Wtf(GetType().FullName, "There was a problem loading the gesture library.");
            Finish();
        }
    }
    ```

-   O último item é necessário para implementar o método `GestureOverlayViewOnGesturePerformed` conforme mostrado no seguinte trecho de código. Quando o `GestureOverlayView` detecta um gesto, ele chama esse método. A primeira coisa que podemos tentar obter um `IList<Prediction>` objetos que correspondem o gesto chamando `_gestureLibrary.Recognize()`. Usamos um pouco de LINQ para obter o `Prediction` que tem a pontuação mais alta para o movimento.

    Se não houver nenhuma correspondência suficiente pontuação de gestos com um alto, em seguida, o manipulador de eventos será encerrado sem fazer nada. Caso contrário, verifique o nome da previsão e alterar a imagem que está sendo exibida com base no nome do gesto de:

    ```csharp
    private void GestureOverlayViewOnGesturePerformed(object sender, GestureOverlayView.GesturePerformedEventArgs gesturePerformedEventArgs)
    {
        IEnumerable<Prediction> predictions = from p in _gestureLibrary.Recognize(gesturePerformedEventArgs.Gesture)
        orderby p.Score descending
        where p.Score > 1.0
        select p;
        Prediction prediction = predictions.FirstOrDefault();

        if (prediction == null)
        {
            Log.Debug(GetType().FullName, "Nothing seemed to match the user's gesture, so don't do anything.");
            return;
        }

        Log.Debug(GetType().FullName, "Using the prediction named {0} with a score of {1}.", prediction.Name, prediction.Score);

        if (prediction.Name.StartsWith("checkmark"))
        {
            _imageView.SetImageResource(Resource.Drawable.checked_me);
        }
        else if (prediction.Name.StartsWith("erase", StringComparison.OrdinalIgnoreCase))
        {
            // Match one of our "erase" gestures
            _imageView.SetImageResource(Resource.Drawable.check_me);
        }
    }
    ```

-   Execute o aplicativo e começar a atividade do reconhecedor de gestos personalizados. Ele deve ser semelhante a captura de tela a seguir:

    [![Captura de tela com Check-Me a imagem](android-touch-walkthrough-images/image19.png)](android-touch-walkthrough-images/image19.png#lightbox)

    Agora desenhe uma marca de seleção na tela e o bitmap que está sendo exibido deverá ser semelhante ao mostrado nas capturas de tela seguinte:

    [![Desenhar a marca de seleção](android-touch-walkthrough-images/image20.png)](android-touch-walkthrough-images/image20.png#lightbox)
    [![reconhecido marca de seleção](android-touch-walkthrough-images/image21.png)](android-touch-walkthrough-images/image21.png#lightbox)

    Por fim, desenhe um rabisco na tela. A caixa de seleção deve alterar novamente para a imagem original, conforme mostrado nessas capturas de tela:

    [![Rabisco na tela](android-touch-walkthrough-images/image22.png)](android-touch-walkthrough-images/image22.png#lightbox)
    [![imagem Original é exibida](android-touch-walkthrough-images/image23.png)](android-touch-walkthrough-images/image23.png#lightbox)

Agora você tem uma compreensão de como integrar o toque e gestos em um aplicativo do Android usando o xamarin.


## <a name="related-links"></a>Links relacionados

- [Iniciar Touch Android (exemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android toque Final (exemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
