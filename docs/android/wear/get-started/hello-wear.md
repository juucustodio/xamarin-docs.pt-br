---
title: Olá, Wear
description: Crie seu primeiro aplicativo de desgaste do Android e execute-o em um emulador ou dispositivo de desgaste. Este passo a passo fornece instruções passo a passo para a criação de um pequeno projeto de desgaste de Android que manipula cliques de botão e exibe um contador de cliques no dispositivo de desgaste. Ele explica como depurar o aplicativo usando um emulador de desgaste ou um dispositivo de desgaste conectado via Bluetooth a um telefone Android. Ele também fornece um conjunto de dicas de depuração para o desgaste do Android.
ms.prod: xamarin
ms.assetid: 86BCD0E7-E9DC-40F1-9B44-887BC51BB48D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/10/2018
ms.openlocfilehash: 8b640e867e3f7adb6960ecda557ac1e410ec5799
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456923"
---
# <a name="hello-wear"></a>Olá, Wear

_Crie seu primeiro aplicativo de desgaste do Android e execute-o em um emulador ou dispositivo de desgaste. Este passo a passo fornece instruções passo a passo para a criação de um pequeno projeto de desgaste de Android que manipula cliques de botão e exibe um contador de cliques no dispositivo de desgaste. Ele explica como depurar o aplicativo usando um emulador de desgaste ou um dispositivo de desgaste conectado via Bluetooth a um telefone Android. Ele também fornece um conjunto de dicas de depuração para o desgaste do Android._

![Captura de tela do aplicativo de desgaste a ser concluído neste tutorial](hello-wear-images/example.png)

## <a name="your-first-wear-app"></a>Seu primeiro aplicativo de desgaste

Siga estas etapas para criar seu primeiro aplicativo de desgaste do Xamarin. Android:

### <a name="1-create-a-new-android-project"></a>1. criar um novo projeto Android

Criar um novo **aplicativo de desgaste do Android**:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Criando um novo aplicativo Android desgaste na caixa de diálogo novo projeto](hello-wear-images/vs/new-solution-sml.w157.png)](hello-wear-images/vs/new-solution.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Criando um novo aplicativo Android desgaste na caixa de diálogo nova solução](hello-wear-images/xs/new-solution-sml.png)](hello-wear-images/xs/new-solution.png#lightbox)

-----

Esse modelo inclui automaticamente o NuGet (e as dependências) da **biblioteca portátil do Xamarin Android** para que você tenha acesso a widgets específicos de desgaste. Se você não vir o modelo de desgaste, examine o guia de [instalação e instalação](~/android/wear/get-started/installation.md) para verificar se você instalou um SDK do Android com suporte. 

### <a name="2-choose-the-correct-target-framework"></a>2. escolha a **estrutura de destino** correta

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Verifique se o **Android para destino mínimo** está definido como **Android 5,0 (pirulito)** ou posterior: 

[![Definindo a estrutura de destino como Android 5,0 no Visual Studio](hello-wear-images/vs/target-framework-sml.png)](hello-wear-images/vs/target-framework.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Verifique se a estrutura de destino está definida como **Android 5,0 (pirulito)** ou posterior:

[![Definindo a estrutura de destino como Android 5,0 no Visual Studio para Mac](hello-wear-images/xs/target-framework-sml.png)](hello-wear-images/xs/target-framework.png#lightbox)

-----

Para obter mais informações sobre como definir a estrutura de destino, consulte [noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).

### <a name="3-edit-the-mainaxml-layout"></a>3. editar o layout **Main. axml**

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

### <a name="4-edit-the-mainactivitycs-source"></a>4. editar a origem do **MainActivity.cs**

Adicione o código para incrementar um contador e exibi-lo sempre que o botão for clicado: 

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

### <a name="5-setup-an-emulator-or-device"></a>5. configurar um emulador ou dispositivo

A próxima etapa é configurar um emulador ou dispositivo para implantar e executar o aplicativo. Se você ainda não estiver familiarizado com o processo de implantação e execução de aplicativos Xamarin. Android em geral, consulte o [início rápido Olá, Android](~/android/get-started/hello-android/hello-android-quickstart.md).

Se você não tiver um dispositivo de desgaste do Android, como um SmartWatch de desgaste do Android, poderá executar o aplicativo em um emulador. Para obter informações sobre como depurar aplicativos de desgaste em um emulador, consulte [depurar o desgaste do Android em um emulador](~/android/wear/deploy-test/debug-on-emulator.md).

Se você tiver um dispositivo de desgaste do Android, como um SmartWatch de desgaste do Android, poderá executar o aplicativo no dispositivo em vez de usar um emulador. Para obter mais informações sobre a depuração em um dispositivo de desgaste, consulte [Depurar em um dispositivo de desgaste](~/android/wear/deploy-test/debug-on-device.md).

### <a name="6-run-the-android-wear-app"></a>6. executar o aplicativo Android desgaste

O dispositivo Android desgaste deve aparecer no menu suspenso dispositivo. Certifique-se de escolher o dispositivo de desgaste do Android ou o AVD correto antes de iniciar a depuração. Depois de selecionar o dispositivo, clique no botão reproduzir para implantar o aplicativo no emulador ou dispositivo.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Escolhendo um AVD de desgaste no menu de dispositivo do Visual Studio](hello-wear-images/vs/choose-wear-sim.png)](hello-wear-images/vs/choose-wear-sim.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![Escolhendo um AVD de desgaste no menu Visual Studio para Mac dispositivo](hello-wear-images/xs/choose-wear-sim.png)](hello-wear-images/xs/choose-wear-sim.png#lightbox)

-----

Você pode ver uma mensagem de **apenas um minuto...** (ou alguma outra tela de intersticial) a princípio: 

![O emulador de inspeção exibe apenas um minuto...](hello-wear-images/please-wait.png)

Se você estiver usando um emulador Watch, pode levar algum tempo para iniciar o aplicativo. Quando você estiver usando o Bluetooth, levará mais tempo para implantar o aplicativo do que seria sobre USB. (Por exemplo, leva cerca de 5 minutos para implantar esse aplicativo em um LG G, que é conectado por Bluetooth a um telefone do Nexus 5.)

Depois que o aplicativo for implantado com êxito, a tela do dispositivo de desgaste deverá exibir uma tela semelhante à seguinte:

[![Tela inicial do aplicativo de desgaste](hello-wear-images/mainactivity-screen.png)](hello-wear-images/mainactivity-screen.png#lightbox)

Toque **em ME comigo!** na face do dispositivo de desgaste e veja o incremento de contagem com cada toque:

[![Captura de tela do aplicativo de desgaste após três cliques](hello-wear-images/mainactivity-counts.png)](hello-wear-images/mainactivity-counts.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

Confira os [exemplos de desgaste](/samples/browse/?products=xamarin&term=Xamarin.Android%2bwear) , incluindo aplicativos de desgaste do Android com aplicativos de telefone complementares.

Quando você estiver pronto para distribuir seu aplicativo, consulte [trabalhando com empacotamento](~/android/wear/deploy-test/packaging.md).

## <a name="related-links"></a>Links Relacionados

- [Aplicativo Click me (exemplo)](/samples/xamarin/monodroid-samples/wear-weartest)