---
title: Toque no Android
ms.prod: xamarin
ms.assetid: 405A1FA0-4EFA-4AEB-B672-F36307B9CF16
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 84767975eece4f8f0efae1fe53463cbc053bd836
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171464"
---
# <a name="touch-in-android"></a>Toque no Android

Muito como iOS, Android cria um objeto que contém dados sobre a interação do usuário físico com a tela &ndash; um `Android.View.MotionEvent` objeto. Esse objeto contém os dados, como qual ação é executada, em que levou a interatividade coloque, o nível de pressão foi aplicada, etc. Um `MotionEvent` objeto divide o movimento em com os seguintes valores:

-  Um código de ação que descreve o tipo de animação, como a interação inicial, a interatividade movendo pela tela ou o final de toque.

-  Um conjunto de valores do eixo que descrevem a posição do `MotionEvent` e outras propriedades de movimentação, como onde está ocorrendo o toque, quando ocorreu a interatividade e o nível de pressão foi usado.
   Os valores do eixo podem ser diferentes dependendo do dispositivo, portanto, a lista anterior não descreve todos os valores do eixo.


O `MotionEvent` objeto será passado para um método apropriado em um aplicativo. Há três maneiras para um aplicativo xamarin. Android responder a um evento de toque:

-  *Atribuir um manipulador de eventos `View.Touch`*  - o `Android.Views.View` classe tem um `EventHandler<View.TouchEventArgs>` quais aplicativos podem atribuir um manipulador para. Isso é o comportamento típico do .NET.

-  *Implementando `View.IOnTouchListener`*  -instâncias dessa interface podem ser atribuídos a um objeto de exibição usando o modo de exibição. `SetOnListener` método. Isso é funcionalmente equivalente à atribuição de um manipulador de eventos para o `View.Touch` eventos. Se houver alguma lógica comum ou compartilhada que vários modos de exibição podem precisar quando eles são tocados, será mais eficiente para criar uma classe e implementar esse método que to atribuir cada exibição seu próprio manipulador de eventos.

-  *Substituir `View.OnTouchEvent`*  -todas as exibições em Android subclasse `Android.Views.View`. Quando uma exibição é tocada, o Android chamará o `OnTouchEvent` e passe-o um `MotionEvent` objeto como um parâmetro.


> [!NOTE]
> Nem todos os dispositivos Android dão suporte a telas sensíveis ao toque. 

Adicionando a seguinte marca ao seu arquivo de manifesto faz com que o Google Play somente exibirá seu aplicativo para os dispositivos que são habilitado de toque:

```xml
<uses-configuration android:reqTouchScreen="finger" />
```

## <a name="gestures"></a>Gestos

Um gesto é uma forma desenhado na tela sensível ao toque. Um gesto pode ter um ou mais traços a ele, cada traço consiste em uma sequência de pontos criado por um ponto de contato com a tela de diferente. Android pode dar suporte a muitos tipos diferentes de gestos, de um simple despedida toda a tela para gestos complexos que envolvem multitoque.

O Android fornece o `Android.Gestures` namespace especificamente para gerenciar e responder a gestos. No coração de todos os gestos está uma classe especial chamada `Android.Gestures.GestureDetector`. Como o nome implica, essa classe escutará gestos e eventos com base em `MotionEvents` fornecida pelo sistema operacional.

Para implementar um detector de gesto, uma atividade deve criar uma instância de um `GestureDetector` de classe e fornecer uma instância de `IOnGestureListener`, conforme ilustrado pela seguinte trecho de código:

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

Quando uma instância do `GestureDetector` identifica um gesto de interesse, ele notificará o aplicativo ou uma atividade, gerando um evento ou por meio de um retorno de chamada fornecido pelo `GestureDetector.IOnGestureListener`.
Essa interface fornece seis métodos para os vários gestos:

-  *OnDown* -chamado quando um toque ocorre, mas não é liberado.

-  *OnFling* -chamado quando uma despedida ocorre e fornece dados sobre o toque de início e término que disparou o evento.

-  *OnLongPress* -chamado quando ocorre um pressionamento longo.

-  *OnScroll* -chamado quando ocorre um evento de rolagem.

-  *OnShowPress* – chamado após um OnDown ocorreu e a movimentação ou evento não foi executado.

-  *OnSingleTapUp* -chamado quando ocorre um único toque.


Em muitos casos os aplicativos só podem ser interessantes em um subconjunto de gestos. Nesse caso, os aplicativos devem estender a classe GestureDetector.SimpleOnGestureListener e substituir os métodos que correspondem aos eventos que estão interessados.

## <a name="custom-gestures"></a>Gestos personalizados

Gestos são uma ótima maneira para que os usuários interagem com um aplicativo. As APIs que vimos até agora seria suficiente para gestos simples, mas provavelmente demorará um pouco onerosas para gestos mais complicados. Para ajudar com gestos mais complicados, o Android fornece outro conjunto de APIs no namespace Android.Gestures que facilitarão essa carga associada com gestos personalizados.

### <a name="creating-custom-gestures"></a>Criação de gestos personalizados

Desde o Android 1.6, o SDK do Android vem com um aplicativo já instalado no emulador chamado construtor de gestos. Este aplicativo permite que um desenvolvedor crie gestos predefinidos que podem ser inseridos em um aplicativo. A captura de tela a seguir mostra um exemplo do construtor de gestos de:

[![Captura de tela do construtor de gestos com gestos de exemplo](touch-in-android-images/image11.png)](touch-in-android-images/image11.png#lightbox)

Uma versão aprimorada desse aplicativo de chamada de gesto de ferramenta pode ser encontrada o Google Play. Ferramenta de gesto é muito parecido com o construtor de gestos, exceto que ele permite que você teste gestos após eles terem sido criados. Esta próxima captura de tela mostra o construtor de gestos:

[![Captura de tela da ferramenta de gesto com gestos de exemplo](touch-in-android-images/image12.png)](touch-in-android-images/image12.png#lightbox)

Ferramenta de gesto é um pouco mais útil para criar gestos personalizados, pois permite que os gestos a serem testadas conforme eles são criados e está facilmente disponível por meio do Google Play.

Ferramenta de gesto permite a que criação de um gesto de desenho na tela e atribuindo um nome. Depois que os gestos são criados, eles são salvos em um arquivo binário no cartão SD do dispositivo. Esse arquivo precisa ser recuperadas do dispositivo e, em seguida, empacotados com um aplicativo no /Resources/raw a pasta. Esse arquivo pode ser recuperado do emulador usando o Android Debug Bridge. O exemplo a seguir mostra como copiar o arquivo de um Galaxy Nexus para o diretório de recursos de um aplicativo:

```shell
$ adb pull /storage/sdcard0/gestures <projectdirectory>/Resources/raw
```

Depois de recuperar o arquivo deve ser empacotado com o aplicativo dentro do diretório /recursos/bruto. A maneira mais fácil de usar esse arquivo de gesto é carregar o arquivo em um GestureLibrary, conforme mostrado no trecho a seguir:

```csharp
GestureLibrary myGestures = GestureLibraries.FromRawResources(this, Resource.Raw.gestures);
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

O `GestureOverlayView` tem vários eventos que serão gerados durante o processo de um gesto de desenho. O evento mais interessante é `GesturePerformed`. Esse evento é gerado quando o usuário tiver concluído sua gesto de desenho.

Quando esse evento é gerado, a atividade solicita um `GestureLibrary` para tentar corresponder o gesto de que o usuário com um dos gestos criado pela ferramenta de gesto. `GestureLibrary` Retorna uma lista de objetos de previsão.

Cada objeto de previsão contém uma pontuação e o nome de um dos gestos no `GestureLibrary`. Quanto maior a pontuação, maior a probabilidade o gesto de chamada na previsão corresponde o gesto do usuário.
Em termos gerais, as pontuações de menores que 1.0 são consideradas correspondências ruins.

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

Com isso feito, você deve ter uma compreensão de como usar a toque e gestos em um aplicativo xamarin. Android. Vamos agora passar para um passo a passo e ver todos os conceitos em um aplicativo de exemplo de trabalho.



## <a name="related-links"></a>Links relacionados

- [Android Touch iniciar (amostra)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android toque Final (amostra)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
