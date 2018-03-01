---
title: Toque no Android
ms.topic: article
ms.prod: xamarin
ms.assetid: 405A1FA0-4EFA-4AEB-B672-F36307B9CF16
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 83997018f4e08567a9150bf1e21374a98c8ddb4e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="touch-in-android"></a>Toque no Android

Muito como iOS, Android cria um objeto que contém dados sobre a interação do usuário físico com a tela &ndash; um `Android.View.MotionEvent` objeto. Esse objeto contém os dados como a ação é executada, onde o toque levou colocar, quanto pressão foi aplicado, etc. Um `MotionEvent` objeto divide o movimento nos seguintes valores:

-  Um código de ação que descreve o tipo de movimentação, como toque inicial, o toque atravessando a tela ou o final de toque.

-  Um conjunto de valores do eixo que descrevem a posição do `MotionEvent` e outras propriedades de movimentação, como onde o toque está ocorrendo, quando ocorreu o toque e quanto pressão foi usado.
   Os valores do eixo podem ser diferentes dependendo do dispositivo, para que a lista anterior não descreve todos os valores do eixo.


O `MotionEvent` objeto será passado para um método apropriado em um aplicativo. Há três maneiras de um aplicativo xamarin responder a um evento de toque:

-  *Atribuir um manipulador de eventos para `View.Touch`*  - o `Android.Views.View` classe tiver um `EventHandler<View.TouchEventArgs>` quais aplicativos podem atribuir um manipulador. Este é o comportamento típico do .NET.

-  *Implementando `View.IOnTouchListener`*  -instâncias desta interface podem ser atribuídos a um objeto de exibição usando o modo de exibição. `SetOnListener` método. Isso é funcionalmente equivalente à atribuição de um manipulador de eventos para o `View.Touch` evento. Se houver alguma lógica comum ou compartilhada que vários modos de exibição podem ser necessário quando eles são tocados, será mais eficiente para criar uma classe e implementar esse método de to atribuir cada exibição seu próprio manipulador de eventos.

-  *Substituir `View.OnTouchEvent`*  -todas as exibições no Android subclasse `Android.Views.View`. Quando uma exibição é tocada, Android chamará o `OnTouchEvent` e passá-lo um `MotionEvent` objeto como um parâmetro.


> [!NOTE]
> **Observação:** nem todos os dispositivos Android oferecem suporte a telas sensíveis ao toque. 

Adicionando a seguinte marca para o arquivo de manifesto faz com que o Google Play somente exibição seu aplicativo para os dispositivos que são toque habilitado:

```xml
<uses-configuration android:reqTouchScreen="finger" />
```

## <a name="gestures"></a>Gestos

Um gesto é uma forma desenhado na tela sensível ao toque. Um gesto pode ter um ou mais traços, cada traço consiste em uma sequência de pontos criado por outro ponto de contato com a tela. Android pode dar suporte a vários tipos diferentes de gestos, de uma simple fling na tela para gestos complexos que envolvem multitoque.

Android fornece o `Android.Gestures` namespace especificamente para gerenciar e responder aos gestos. No Centro de todos os gestos é uma classe especial chamada `Android.Gestures.GestureDetector`. Como o nome implica, essa classe escutará gestos e eventos com base em `MotionEvents` fornecido pelo sistema operacional.

Para implementar um detector de gesto, uma atividade deve instanciar uma `GestureDetector` classe e fornecer uma instância de `IOnGestureListener`, conforme ilustrado pelo seguinte trecho de código:

```csharp
GestureOverlayView.IOnGestureListener myListener = new MyGestureListener();
_gestureDetector = new GestureDetector(this, myListener);
```

Uma atividade também deve implementar o OnTouchEvent e passar o MotionEvent para o detector de gesto. O trecho de código a seguir mostra um exemplo disso:

```csharp
public override bool OnTouchEvent(MotionEvent e)
{
    // This method is in an Activity
    return _gestureDetector.OnTouchEvent(e);
}
```

Quando uma instância do `GestureDetector` identifica um gesto de interesse, ele notificará a atividade ou o aplicativo, gerando um evento ou por meio de um retorno de chamada fornecida pelo `GestureDetector.IOnGestureListener`.
Essa interface fornece seis métodos dos diversos gestos:

-  *OnDown* -chamado quando ocorre um toque, mas não está liberado.

-  *OnFling* -chamado quando um fling ocorre e fornece dados sobre o toque de início e término que disparou o evento.

-  *OnLongPress* -chamado quando ocorre um pressionamento de tempo.

-  *OnScroll* -chamados quando ocorre um evento de rolagem.

-  *OnShowPress* - chamado após um OnDown ocorreu e a movimentação ou evento não foi executada.

-  *OnSingleTapUp* -chamado quando ocorre um único toque.


Em muitos casos aplicativos só podem ser interessantes em um subconjunto de gestos. Nesse caso, os aplicativos devem estender a classe GestureDetector.SimpleOnGestureListener e substituir os métodos que correspondem aos eventos que estão interessados.

## <a name="custom-gestures"></a>Gestos personalizados

Gestos são uma ótima maneira para que os usuários interagem com um aplicativo. As APIs que vimos até agora seria suficiente para gestos simples, mas podem onerosas um pouco para gestos mais complicados. Para ajudar com gestos mais complicados, Android fornece outro conjunto de APIs no namespace Android.Gestures que facilitarão essa carga associada gestos personalizados.

### <a name="creating-custom-gestures"></a>Criando gestos personalizados

Desde 1.6 Android, o SDK do Android vem com um aplicativo instalado previamente no emulador chamado construtor de gestos. Esse aplicativo permite que um desenvolvedor crie gestos predefinidos que podem ser inseridos em um aplicativo. A captura de tela a seguir mostra um exemplo do construtor de gestos de:

[![Captura de tela de gestos de construtor com gestos de exemplo](touch-in-android-images/image11.png)](touch-in-android-images/image11.png)

Uma versão aprimorada desse aplicativo chamada ferramenta gesto pode ser encontrada Google Play. Gesto ferramenta é muito semelhante ao construtor de gestos exceto que ele permite que você teste gestos depois que elas foram criadas. Próxima captura de tela mostra gestos construtor:

[![Captura de tela de gesto de ferramenta com gestos de exemplo](touch-in-android-images/image12.png)](touch-in-android-images/image12.png)

Ferramenta de gesto é um pouco mais útil para criar gestos personalizados conforme ele permite que os gestos a ser testada conforme eles são criados e está facilmente disponível por meio do Google Play.

Ferramenta de gesto permite a que criação de um gesto na tela de desenho e atribuindo um nome. Depois que os gestos são criados, eles são salvos em um arquivo binário no cartão SD do dispositivo. Esse arquivo deve ser recuperado do dispositivo e, em seguida, empacotados com um aplicativo no /Resources/raw a pasta. Esse arquivo pode ser recuperado do emulador usando a ponte de depuração do Android. O exemplo a seguir mostra como copiar o arquivo de um Galaxy Nexus para o diretório de recursos de um aplicativo:

```shell
$ adb pull /storage/sdcard0/gestures <projectdirectory>/Resources/raw
```

Depois de recuperar o arquivo deve ser empacotado com o seu aplicativo dentro do diretório /recursos/bruto. A maneira mais fácil de usar esse arquivo de gesto é carregar o arquivo em um GestureLibrary, conforme mostrado no trecho a seguir:

```csharp
GestureLibary myGestures = GestureLibraries.FromRawResources(this, Resource.Raw.gestures);
if (!myGestures.Load())
{
    // The library didn't load, so close the activity.
    Finish();
}
```

### <a name="using-custom-gestures"></a>Usando gestos personalizados

Para reconhecer gestos personalizados em uma atividade, ele deve ter um objeto de Android.Gesture.GestureOverlay adicionado ao seu layout. O trecho de código a seguir mostra como adicionar programaticamente uma GestureOverlayView a uma atividade:

```csharp
GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
gestureOverlayView.AddOnGesturePerformedListener(this);
SetContentView(gestureOverlayView);
```

O trecho XML a seguir mostra como adicionar um GestureOverlayView declarativamente:

```xml
<android.gesture.GestureOverlayView
    android:id="@+id/gestures"
    android:layout_width="match_parent "
    android:layout_height="match_parent" />
```

O `GestureOverlayView` tem vários eventos que serão gerados durante o processo de desenho de um gesto. O evento mais interessante é `GesturePeformed`. Esse evento é gerado quando o usuário tiver concluído sua gesto de desenho.

Quando esse evento é gerado, a atividade solicita um `GestureLibrary` para tentar corresponder o gesto de que o usuário com um dos gestos criado pela ferramenta de gesto. `GestureLibrary` Retorna uma lista de objetos de previsão.

Cada objeto de previsão contém uma pontuação e nome de um dos gestos no `GestureLibrary`. Quanto maior a pontuação, mais provavelmente o gesto de chamada na previsão corresponde o gesto do usuário.
Em geral, pontuações menor que 1.0 são consideradas correspondências ruins.

O código a seguir mostra um exemplo de um gesto de correspondência:

```csharp
private void GestureOverlayViewOnGesturePerformed(object sender, GestureOverlayView.GesturePerformedEventArgs gesturePerformedEventArgs)
{
    // In this example _gestureLibrary was instantiated in OnCreate
    IEnumerable<Prediction> predictions = from p in _gestureLibrary.Recognize(gesturePerformedEventArgs.Gesture)
    orderby p.Score descending
    where p.Score > 1.0
    select p;
    Prediction prediction = predictions.FirstOrDefault();

    if (prediction == null)
    {
        Log.Debug(GetType().FullName, "Nothing matched the user's gesture.");
        return;
    }

    Toast.MakeText(this, prediction.Name, ToastLength.Short).Show();
}
```

Com isso, você deve ter uma compreensão de como usar o toque e gestos em um aplicativo xamarin. Vamos agora passar para um passo a passo e ver todos os conceitos em um aplicativo de exemplo de funcionamento.



## <a name="related-links"></a>Links relacionados

- [Iniciar Touch Android (exemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android toque Final (exemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
