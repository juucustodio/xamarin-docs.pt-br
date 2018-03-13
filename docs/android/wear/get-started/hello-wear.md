---
title: "Olá, desgaste"
description: "Criar seu primeiro aplicativo do Android desgaste e executá-lo em um dispositivo ou emulador de desgaste. Este passo a passo fornece instruções passo a passo para criar um projeto Android desgaste pequeno que manipula cliques de botão e exibe um contador clique no dispositivo desgaste. Ele explica como depurar o aplicativo usando um emulador desgaste ou um dispositivo de desgaste que está conectado via Bluetooth para um telefone Android. Ele também fornece um conjunto de dicas de depuração para desgaste Android."
ms.topic: article
ms.prod: xamarin
ms.assetid: 86BCD0E7-E9DC-40F1-9B44-887BC51BB48D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 8eed2d6b825a6e6dd7e956bf901246b9a630081a
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="hello-wear"></a>Olá, desgaste

_Criar seu primeiro aplicativo do Android desgaste e executá-lo em um dispositivo ou emulador de desgaste. Este passo a passo fornece instruções passo a passo para criar um projeto Android desgaste pequeno que manipula cliques de botão e exibe um contador clique no dispositivo desgaste. Ele explica como depurar o aplicativo usando um emulador desgaste ou um dispositivo de desgaste que está conectado via Bluetooth para um telefone Android. Ele também fornece um conjunto de dicas de depuração para desgaste Android._

![Captura de tela do aplicativo desgaste para ser concluída neste tutorial](hello-wear-images/example.png)

## <a name="your-first-wear-app"></a>Seu primeiro aplicativo de desgaste

Siga estas etapas para criar seu primeiro aplicativo xamarin desgaste:

### <a name="1-create-a-new-android-project"></a>1. Criar um novo projeto Android

Criar um novo **aplicativo Android usam**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Criando um novo aplicativo Android desempenham na caixa de diálogo Novo projeto](hello-wear-images/vs/new-solution-sml.png)](hello-wear-images/vs/new-solution.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Criando um novo aplicativo Android desempenham na caixa de diálogo nova solução](hello-wear-images/xs/new-solution-sml.png)](hello-wear-images/xs/new-solution.png#lightbox)

-----


Esse modelo inclui automaticamente o **Xamarin biblioteca de vestir Android** NuGet (e as dependências) para que você terá acesso aos widgets desgaste específicos. Se você não vir o modelo de desgaste, examine o [instalação e configuração](~/android/wear/get-started/installation.md) guia para verificar se você instalou um SDK do Android com suporte. 

### <a name="2-choose-the-correct-target-framework"></a>2. Escolher o correto **Framework de destino**

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Certifique-se de que **mínimo Android para o destino** é definido como **Android 5.0 (pirulito)** ou posterior: 

[![Definir a estrutura de destino para o Android 5.0 no Visual Studio](hello-wear-images/vs/target-framework-sml.png)](hello-wear-images/vs/target-framework.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

Certifique-se de que a estrutura de destino é definida como **Android 5.0 (pirulito)** ou posterior:

[![Definir a estrutura de destino para o Android 5.0 no Visual Studio para Mac](hello-wear-images/xs/target-framework-sml.png)](hello-wear-images/xs/target-framework.png#lightbox)

-----

Para obter mais informações sobre como definir a estrutura de destino, consulte [Noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).


### <a name="3-edit-the-mainaxml-layout"></a>3. Editar o **Main.axml** layout

Configure o layout para conter um `TextView` e um `Button` para o exemplo: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:layout_width="match_parent"
android:layout_height="match_parent">
  <ScrollView
    android:id="@+id/scroll"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:background="#000000"
    android:fillViewport="true">
    <LinearLayout
      android:layout_width="match_parent"
      android:layout_height="wrap_content"
      android:orientation="vertical">
      <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="2dp"
        android:text="Main Activity"
        android:textSize="36sp"
        android:textColor="#006600" />
      <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="2dp"
        android:textColor="#cccccc"
        android:id="@+id/result" />
      <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="showNotification"
        android:text="Click Me!"
        android:id="@+id/click_button" />
    </LinearLayout>
  </ScrollView>
</FrameLayout>
```

### <a name="4-edit-the-mainactivitycs-source"></a>4. Editar o **MainActivity.cs** fonte

Adicione o código para incrementar um contador e exibi-lo sempre que o botão é clicado: 

```csharp
[Activity (Label = "WearTest", MainLauncher = true, Icon = "@drawable/icon")]
public class MainActivity : Activity
{
  int count = 1;

  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);

    Button button = FindViewById<Button> (Resource.Id.click_button);
    TextView text = FindViewById<TextView> (Resource.Id.result);

    button.Click += delegate {
      text.Text = string.Format ("{0} clicks!", count++);
    };
  }
}
```

### <a name="5-setup-an-emulator-or-device"></a>5. Configurar um dispositivo ou emulador

A próxima etapa é configurar um dispositivo ou emulador para implantar e executar o aplicativo. Se você não estiver familiarizado com o processo de implantação e execução de aplicativos xamarin em geral, consulte o [Hello, Android Quickstart](~/android/get-started/hello-android/hello-android-quickstart.md).

Se você não tiver um dispositivo Android desgaste como um Smartwatch de desgaste Android, você pode executar o aplicativo no emulador. Para obter informações sobre como depurar aplicativos de desgaste em um emulador, consulte [depurar Android desempenham em um emulador](~/android/wear/deploy-test/debug-on-emulator.md).

Se você tiver um dispositivo Android desgaste como um Smartwatch de desgaste Android, você pode executar o aplicativo no dispositivo em vez de usar um emulador. Para obter mais informações sobre como depurar em um dispositivo de desgaste, consulte [depurar em um dispositivo de desgaste](~/android/wear/deploy-test/debug-on-device.md).


### <a name="6-run-the-android-wear-app"></a>6. Executar o aplicativo do Android desgaste

O dispositivo Android desgaste deve aparecer no menu suspenso do dispositivo. Certifique-se de escolher o dispositivo Android desgaste correto ou AVD antes de iniciar a depuração. Depois de selecionar o dispositivo, clique no botão Play para implantar o aplicativo no emulador ou dispositivo.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Escolhendo um AVD desempenham no menu de dispositivo do Visual Studio](hello-wear-images/vs/choose-wear-sim.png)](hello-wear-images/vs/choose-wear-sim.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

[![Escolhendo um AVD desempenham no Visual Studio para o menu de dispositivo do Mac](hello-wear-images/xs/choose-wear-sim.png)](hello-wear-images/xs/choose-wear-sim.png#lightbox)

-----

Você pode ver um **apenas um minuto...**  mensagem (ou alguma outra tela intersticial) primeiro: 

![Assista o emulador exibe apenas um minuto...](hello-wear-images/please-wait.png)

Se você estiver usando um emulador watch, pode levar um tempo para iniciar o aplicativo. Quando você estiver usando o Bluetooth, levará mais tempo para implantar o aplicativo do que seria necessário por USB. (Por exemplo, levará cerca de 5 minutos para implantar esse aplicativo para o Watch G LG que esteja conectada Bluetooth em um telefone Nexus 5.)

Depois que o aplicativo implanta com êxito, a tela do dispositivo desgaste deve exibir uma tela semelhante ao seguinte:

[![Tela inicial do aplicativo de desgaste](hello-wear-images/mainactivity-screen.png)](hello-wear-images/mainactivity-screen.png#lightbox)

Toque na **clique ME!** botão na frente do dispositivo de desgaste e consulte o incremento de contagem com cada toque:

[![Captura de tela de desgaste aplicativo após 3 cliques](hello-wear-images/mainactivity-counts.png)](hello-wear-images/mainactivity-counts.png#lightbox)


## <a name="next-steps"></a>Próximas etapas

Check-out de [desgaste exemplos](https://developer.xamarin.com/samples/android/Android%20Wear/) incluindo aplicativos Android desgaste com aplicativos de telefone complementar.

Quando você estiver pronto para distribuir seu aplicativo, consulte [trabalhar com empacotamento](~/android/wear/deploy-test/packaging.md).


## <a name="related-links"></a>Links relacionados

- [Clique Me aplicativo (exemplo)](https://developer.xamarin.com/samples/monodroid/wear/WearTest/)
