---
title: Walkthrough – usando o touch no Android
ms.prod: xamarin
ms.assetid: E281F89B-4142-4BD8-8882-FB65508BF69E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/09/2018
ms.openlocfilehash: b7db20c2d51e96de8fcadaf5d50627132946177c
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455281"
---
# <a name="walkthrough---using-touch-in-android"></a>Walkthrough – usando o touch no Android

Vamos ver como usar os conceitos da seção anterior em um aplicativo de trabalho. Criaremos um aplicativo com quatro atividades. A primeira atividade será um menu, ou um menu de controle, que iniciará as outras atividades para demonstrar as várias APIs. A captura de tela a seguir mostra a atividade principal:

[![Captura de tela de exemplo com o botão touch me](android-touch-walkthrough-images/image14.png)](android-touch-walkthrough-images/image14.png#lightbox)

A primeira atividade, exemplo de toque, mostrará como usar manipuladores de eventos para tocar nas exibições. A atividade de reconhecedor de gesto demonstrará como criar uma subclasse `Android.View.Views` e manipular eventos, além de mostrar como lidar com gestos de pinçagem. A terceira e última atividade, **gesto personalizado**, mostrará como usar gestos personalizados. Para tornar as coisas mais fáceis de seguir e absorver, dividiremos este passo a passos em seções, com cada seção concentrando-se em uma das atividades.

## <a name="touch-sample-activity"></a>Atividade de exemplo de toque

- Abra o projeto **TouchWalkthrough \_ Iniciar**. A **MainActivity** está definida como Go, cabe a &ndash; nós implementar o comportamento de toque na atividade. Se você executar o aplicativo e clicar em **exemplo de toque**, a seguinte atividade deverá ser iniciada:

  [![Captura de tela de atividade com toque começa a ser exibida](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

- Agora que confirmamos que a atividade é iniciada, abra o arquivo **TouchActivity.cs** e adicione um manipulador para o `Touch` evento do `ImageView` :

  ```csharp
  _touchMeImageView.Touch += TouchMeImageViewOnTouch;
  ```

- Em seguida, adicione o seguinte método a **TouchActivity.cs**:

  ```csharp
  private void TouchMeImageViewOnTouch(object sender, View.TouchEventArgs touchEventArgs)
  {
      string message;
      switch (touchEventArgs.Event.Action & MotionEventActions.Mask)
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

Observe no código acima que tratamos o `Move` e a `Down` ação como a mesma. Isso ocorre porque, embora o usuário possa não levantar o dedo para fora do `ImageView` , ele pode se movimentar ou a pressão exercida pelo usuário pode mudar. Esses tipos de alterações irão gerar uma `Move` ação.

Cada vez que o usuário toca no `ImageView` , o `Touch` evento será gerado e nosso manipulador exibirá o toque da mensagem **começa** na tela, conforme mostrado na seguinte captura:

[![Captura de tela de atividade com toque começa](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

Contanto que o usuário esteja tocando no `ImageView` , o **toque começar** será exibido no `TextView` . Quando o usuário não estiver mais tocando no `ImageView` , o **toque** de mensagem será exibido no `TextView` , conforme mostrado na seguinte captura de tela:

[![Captura de tela de atividade com finalidades de toque](android-touch-walkthrough-images/image16.png)](android-touch-walkthrough-images/image16.png#lightbox)

## <a name="gesture-recognizer-activity"></a>Atividade do reconhecedor de gesto

Agora, vamos implementar a atividade Recognizer gestor. Esta atividade demonstrará como arrastar uma exibição na tela e ilustrar uma maneira de implementar o pinçar em zoom.

- Adicione uma nova atividade ao aplicativo chamada `GestureRecognizer` .
  Edite o código para essa atividade para que seja semelhante ao seguinte código:

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

- Adicione uma nova exibição do Android ao projeto e nomeie-a `GestureRecognizerView` . Adicione as seguintes variáveis a esta classe:

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

- Adicione o seguinte construtor a `GestureRecognizerView` . Esse construtor adicionará uma `ImageView` à nossa atividade. Neste ponto, o código ainda não será compilado &ndash; , precisamos criar a classe `MyScaleListener` que ajudará a redimensionar o `ImageView` quando o usuário o pinçar:

  ```csharp
  public GestureRecognizerView(Context context): base(context, null, 0)
  {
      _icon = context.Resources.GetDrawable(Resource.Drawable.Icon);
      _icon.SetBounds(0, 0, _icon.IntrinsicWidth, _icon.IntrinsicHeight);
      _scaleDetector = new ScaleGestureDetector(context, new MyScaleListener(this));
  }
  ```

- Para desenhar a imagem em nossa atividade, precisamos substituir o `OnDraw` método da classe View, conforme mostrado no trecho a seguir. Esse código moverá o `ImageView` para a posição especificada por `_posX` e, `_posY` Além disso, redimensionará a imagem de acordo com o fator de dimensionamento:

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

- Em seguida, precisamos atualizar a variável de instância `_scaleFactor` à medida que o usuário pinça `ImageView` . Adicionaremos uma classe chamada `MyScaleListener` . Essa classe escutará os eventos de escala que serão gerados pelo Android quando o usuário pinçar o `ImageView` .
  Adicione a seguinte classe interna a `GestureRecognizerView` . Essa classe é um `ScaleGesture.SimpleOnScaleGestureListener` . Essa classe é uma classe de conveniência que os ouvintes podem subclasses quando você estiver interessado em um subconjunto de gestos:

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

          _view.Invalidate();
          return true;
      }
  }
  ```

- O próximo método que precisamos substituir no `GestureRecognizerView` é `OnTouchEvent` . O código a seguir lista a implementação completa desse método. Há muito código aqui, então vamos levar um minuto e examinar o que está acontecendo aqui. A primeira coisa que esse método faz é dimensionar o ícone, se necessário &ndash; , isso é manipulado chamando `_scaleDetector.OnTouchEvent` . Em seguida, tentaremos descobrir qual ação chamou esse método:

  - Se o usuário tiver tocado a tela com, registraremos as posições X e Y e a ID do primeiro ponteiro que se encontrava na tela.

  - Se o usuário moveu o toque na tela, descobrimos até onde o usuário moveu o ponteiro.

  - Se o usuário tiver levantado seu dedo na tela, vamos parar de acompanhar os gestos.

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

- Agora, execute o aplicativo e inicie a atividade reconhecedor de gestos.
  Quando ele iniciar, a tela deverá ser semelhante à captura abaixo:

  [![Tela inicial do reconhecedor de gesto com o ícone do Android](android-touch-walkthrough-images/image17.png)](android-touch-walkthrough-images/image17.png#lightbox)

- Agora toque no ícone e arraste-o pela tela. Experimente o gesto de pinçar para zoom. Em algum momento, sua tela pode ser semelhante à captura de tela a seguir:

  [![Ícone de movimentação de gestos ao contrário da tela](android-touch-walkthrough-images/image18.png)](android-touch-walkthrough-images/image18.png#lightbox)

Neste ponto, você deve me dar um Luigi no fundo: você acabou de implementar o pinçar para ampliar em um aplicativo Android! Faça uma pausa rápida e vamos passar para a terceira e última atividade neste passo a passos &ndash; usando gestos personalizados.

## <a name="custom-gesture-activity"></a>Atividade de gesto Personalizada

A tela final neste passo a passo usará gestos personalizados.

Para os fins deste passo a passos, a biblioteca de gestos já foi criada usando a ferramenta de gesto e adicionada ao projeto nos **recursos de arquivo/RAW/gestos**. Com esse pouco de manutenção fora do caminho, vamos começar com a atividade final na explicação.

- Adicione um arquivo de layout **chamado \_ layout de gestos personalizado \_ . axml** ao projeto com o conteúdo a seguir. O projeto já tem todas as imagens na pasta de **recursos** :

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

- Em seguida, adicione uma nova atividade ao projeto e nomeie-a `CustomGestureRecognizerActivity.cs` . Adicione duas variáveis de instância à classe, como mostrado nas duas linhas de código a seguir:

  ```csharp
  private GestureLibrary _gestureLibrary;
  private ImageView _imageView;
  ```

- Edite o `OnCreate` método da atividade this para que seja semelhante ao código a seguir. Vamos levar um minuto para explicar o que está acontecendo nesse código. A primeira coisa que fazemos é instanciar a `GestureOverlayView` e defini-la como a exibição raiz da atividade.
  Também atribuímos um manipulador de eventos ao `GesturePerformed` evento de `GestureOverlayView` . Em seguida, vamos inflar o arquivo de layout que foi criado anteriormente e adicioná-lo como uma exibição filho do `GestureOverlayView` . A etapa final é inicializar a variável `_gestureLibrary` e carregar o arquivo de gestos dos recursos do aplicativo. Se o arquivo de gestos não puder ser carregado por algum motivo, não há muito que essa atividade possa fazer, portanto, ela é desligada:

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

- A última coisa que precisamos fazer é implementar o método `GestureOverlayViewOnGesturePerformed` , conforme mostrado no trecho de código a seguir. Quando o `GestureOverlayView` detecta um gesto, ele chama de volta para esse método. A primeira coisa que tentamos obter um `IList<Prediction>` objeto que corresponde ao gesto chamando `_gestureLibrary.Recognize()` . Usamos um pouco de LINQ para obter o `Prediction` que tem a pontuação mais alta para o gesto.

  Se não houver nenhum gesto correspondente com uma pontuação alta o suficiente, o manipulador de eventos será encerrado sem fazer nada. Caso contrário, verificamos o nome da previsão e alteramos a imagem que está sendo exibida com base no nome do gesto:

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

- Execute o aplicativo e inicie a atividade reconhecedor de gestor personalizado. Ele deve ser semelhante à captura de tela a seguir:

  [![Captura de tela com imagem do check-me](android-touch-walkthrough-images/image19.png)](android-touch-walkthrough-images/image19.png#lightbox)

  Agora desenhe uma marca de seleção na tela e o bitmap que está sendo exibido deve ter uma aparência semelhante à mostrada nas próximas telas:

  [![Marca de seleção desenhada, marca de seleção é reconhecida](android-touch-walkthrough-images/image20.png)](android-touch-walkthrough-images/image20.png#lightbox)

  Por fim, desenhe um rabisco na tela. A caixa de seleção deve mudar de volta para a imagem original, conforme mostrado nestas capturas de tela:

  [![Rabisco na tela, a imagem original é exibida](android-touch-walkthrough-images/image21.png)](android-touch-walkthrough-images/image21.png#lightbox)

Agora você tem uma compreensão de como integrar toque e gestos em um aplicativo Android usando o Xamarin. Android.

## <a name="related-links"></a>Links Relacionados

- [Android Touch Start (exemplo)](/samples/xamarin/monodroid-samples/applicationfundamentals-touch-start)
- [Final do Android Touch (exemplo)](/samples/xamarin/monodroid-samples/applicationfundamentals-touch-final)