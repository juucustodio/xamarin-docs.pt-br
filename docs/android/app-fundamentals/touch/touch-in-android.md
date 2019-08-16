---
title: Toque no Android
ms.prod: xamarin
ms.assetid: 405A1FA0-4EFA-4AEB-B672-F36307B9CF16
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 274c441e0507f100697fc153a9f748de1bce4cf3
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526074"
---
# <a name="touch-in-android"></a>Toque no Android

Assim como o Ios, o Android cria um objeto que mantém os dados sobre a interação física do usuário &ndash; com `Android.View.MotionEvent` a tela de um objeto. Esse objeto contém dados como a ação executada, onde o toque ocorreu, quanto pressão foi aplicada, etc. Um `MotionEvent` objeto divide a movimentação em até os seguintes valores:

- Um código de ação que descreve o tipo de movimento, como o toque inicial, o toque percorrendo a tela ou a terminação de toque.

- Um conjunto de valores de eixo que descrevem a posição das `MotionEvent` outras propriedades de movimento, como o local em que o toque está ocorrendo, quando o toque ocorreu e quanto foi usada a quantidade de pressão.
   Os valores de eixo podem ser diferentes dependendo do dispositivo, portanto, a lista anterior não descreve todos os valores de eixo.


O `MotionEvent` objeto será passado para um método apropriado em um aplicativo. Há três maneiras de um aplicativo Xamarin. Android responder a um evento de toque:

- *Atribua um manipulador de eventos `View.Touch` a* - `Android.Views.View` `EventHandler<View.TouchEventArgs>` a classe com a qual os aplicativos podem atribuir um manipulador. Esse é um comportamento típico do .NET.

- *`View.IOnTouchListener` Implementando* -as instâncias dessa interface podem ser atribuídas a um objeto View usando a exibição. `SetOnListener`forma. Isso é funcionalmente equivalente a atribuir um manipulador de eventos ao `View.Touch` evento. Se houver alguma lógica comum ou compartilhada para a qual muitas exibições diferentes podem precisar quando elas forem tocadas, será mais eficiente criar uma classe e implementar esse método do que atribuir a cada exibição seu próprio manipulador de eventos.

- *Substituir`View.OnTouchEvent`* -todas as exibições na subclasse `Android.Views.View`do Android. Quando um modo de exibição for tocado, o `OnTouchEvent` Android chamará e passará um `MotionEvent` objeto como um parâmetro.


> [!NOTE]
> Nem todos os dispositivos Android dão suporte a telas de toque. 

Adicionar a seguinte marcação ao arquivo de manifesto faz com que Google Play exibir apenas seu aplicativo para os dispositivos que estão com toque habilitado:

```xml
<uses-configuration android:reqTouchScreen="finger" />
```

## <a name="gestures"></a>Gestos

Um gesto é uma forma de desenho à mão na tela sensível ao toque. Um gesto pode ter um ou mais traços para ele, cada traço que consiste em uma sequência de pontos criados por um ponto diferente de contato com a tela. O Android pode dar suporte a vários tipos diferentes de gestos, desde uma simples espalhará na tela até gestos complexos que envolvem multitoque.

O Android fornece `Android.Gestures` o namespace especificamente para gerenciar e responder a gestos. No coração de todos os gestos há uma classe especial chamada `Android.Gestures.GestureDetector`. Como o nome indica, essa classe escutará gestos e eventos com base no `MotionEvents` fornecido pelo sistema operacional.

Para implementar um detector de gestos, uma atividade deve `GestureDetector` instanciar uma classe e fornecer `IOnGestureListener`uma instância do, conforme ilustrado pelo seguinte trecho de código:

```csharp
GestureOverlayView.IOnGestureListener myListener = new MyGestureListener();
_gestureDetector = new GestureDetector(this, myListener);
```

Uma atividade também deve implementar o OnTouchEvent e passar o MotionEvent para o detector de gestos. O trecho de código a seguir mostra um exemplo disso:

```csharp
public override bool OnTouchEvent(MotionEvent e)
{
    // This method is in an Activity
    return _gestureDetector.OnTouchEvent(e);
}
```

Quando uma instância do `GestureDetector` identifica um gesto de interesse, ela notificará a atividade ou o aplicativo gerando um evento ou por meio de um retorno `GestureDetector.IOnGestureListener`de chamada fornecido pelo.
Essa interface fornece seis métodos para os vários gestos:

- *Ondown* – chamado quando um toque ocorre, mas não é liberado.

- *OnFling* – chamado quando um espalhará ocorre e fornece dados sobre o toque inicial e final que disparou o evento.

- *OnLongPress* – chamado quando ocorre uma longa prensa.

- *OnScroll* – chamado quando ocorre um evento de rolagem.

- *OnShowPress* -chamado depois que um ondown ocorreu e um evento move ou up não foi executado.

- *OnSingleTapUp* – chamado quando ocorre um único toque.


Em muitos casos, os aplicativos podem estar interessados apenas em um subconjunto de gestos. Nesse caso, os aplicativos devem estender a classe GestureDetector. SimpleOnGestureListener e substituir os métodos que correspondem aos eventos em que estão interessados.

## <a name="custom-gestures"></a>Gestos personalizados

Os gestos são uma ótima maneira de os usuários interagirem com um aplicativo. As APIs que vimos até agora seriam suficientes para gestos simples, mas podem provar um pouco de oneroso para gestos mais complicados. Para ajudar com gestos mais complicados, o Android fornece outro conjunto de APIs no namespace do Android. gestos que facilitará parte da carga associada aos gestos personalizados.

### <a name="creating-custom-gestures"></a>Criando gestos personalizados

Desde o Android 1,6, o SDK do Android vem com um aplicativo pré-instalado no emulador chamado do construtor de gestos. Esse aplicativo permite que um desenvolvedor crie gestos predefinidos que podem ser inseridos em um aplicativo. A captura de tela a seguir mostra um exemplo do construtor de gestos:

[![Captura de tela do construtor de gestos com gestos de exemplo](touch-in-android-images/image11.png)](touch-in-android-images/image11.png#lightbox)

Uma versão aprimorada desse aplicativo chamada ferramenta de gestos pode ser encontrada Google Play. A ferramenta de gestos é muito semelhante ao construtor de gestos, exceto pelo fato de que ele permite que você teste os gestos depois que eles foram criados. Esta próxima captura de tela mostra o construtor de gestos:

[![Captura de tela da ferramenta de gesto com gestos de exemplo](touch-in-android-images/image12.png)](touch-in-android-images/image12.png#lightbox)

A ferramenta de gestos é um pouco mais útil para a criação de gestos personalizados, pois permite que os gestos sejam testados à medida que estão sendo criados e estão facilmente disponíveis por meio de Google Play.

A ferramenta de gestos permite criar um gesto desenhando na tela e atribuindo um nome. Depois que os gestos são criados, eles são salvos em um arquivo binário no cartão SD do seu dispositivo. Esse arquivo precisa ser recuperado do dispositivo e, em seguida, empacotado com um aplicativo na pasta/resources/RAW. Esse arquivo pode ser recuperado do emulador usando o Android Debug Bridge. O exemplo a seguir mostra a cópia do arquivo de um Galaxy Nexus para o diretório de recursos de um aplicativo:

```shell
$ adb pull /storage/sdcard0/gestures <projectdirectory>/Resources/raw
```

Depois de recuperar o arquivo, ele deve ser empacotado com seu aplicativo dentro do diretório/Resources/RAW. A maneira mais fácil de usar esse arquivo de gesto é carregar o arquivo em um GestureLibrary, conforme mostrado no trecho a seguir:

```csharp
GestureLibrary myGestures = GestureLibraries.FromRawResources(this, Resource.Raw.gestures);
if (!myGestures.Load())
{
    // The library didn't load, so close the activity.
    Finish();
}
```

### <a name="using-custom-gestures"></a>Usando gestos personalizados

Para reconhecer gestos personalizados em uma atividade, ele deve ter um objeto Android. Gesture. GestureOverlay adicionado ao seu layout. O trecho de código a seguir mostra como adicionar programaticamente um GestureOverlayView a uma atividade:

```csharp
GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
gestureOverlayView.AddOnGesturePerformedListener(this);
SetContentView(gestureOverlayView);
```

O trecho de código XML a seguir mostra como adicionar um GestureOverlayView declarativamente:

```xml
<android.gesture.GestureOverlayView
    android:id="@+id/gestures"
    android:layout_width="match_parent "
    android:layout_height="match_parent" />
```

O `GestureOverlayView` tem vários eventos que serão gerados durante o processo de desenho de um gesto. O evento mais interessante é `GesturePerformed`. Esse evento é gerado quando o usuário conclui o desenho de seu gesto.

Quando esse evento é gerado, a atividade solicita um `GestureLibrary` para tentar e corresponder ao gesto que o usuário com um dos gestos criados pela ferramenta de gesto. `GestureLibrary`retornará uma lista de objetos de previsão.

Cada objeto de previsão contém uma pontuação e um nome de um dos gestos no `GestureLibrary`. Quanto maior a pontuação, maior é a probabilidade de o gesto nomeado na previsão corresponder ao gesto desenhado pelo usuário.
Em termos gerais, as pontuações inferiores a 1,0 são consideradas correspondências ruins.

O código a seguir mostra um exemplo de correspondência de um gesto:

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

Com isso feito, você deve ter uma compreensão de como usar toque e gestos em um aplicativo Xamarin. Android. Agora vamos passar para uma explicação e ver todos os conceitos em um aplicativo de exemplo funcional.



## <a name="related-links"></a>Links relacionados

- [Android Touch Start (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-start)
- [Final do Android Touch (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-final)
