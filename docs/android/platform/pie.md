---
title: Android 9 Pie
description: Como começar a desenvolver aplicativos para o Android 9 Pie usando Xamarin.Android.
ms.prod: xamarin
ms.assetid: 6575DD32-9DC8-44E6-85EF-1F8BD07D3780
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2018
ms.openlocfilehash: 0105b43116df697bc6688becb77298c236dfa601
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019882"
---
# <a name="android-pie-features"></a>Recursos do Android Pie

_Como começar a desenvolver aplicativos para o Android 9 Pie usando Xamarin.Android._

[O Android 9 Pie](https://developer.android.com/about/versions/pie/) já está disponível no Google. Uma série de novos recursos e APIs estão sendo disponibilizados nesta versão, e muitos deles são necessários para aproveitar os novos recursos de hardware nos dispositivos Android mais recentes.

![Imagem de herói do Android Pie](pie-images/01-android-p-logo.png)

Este artigo está estruturado para ajudá-lo a começar a desenvolver aplicativos Xamarin.Android para Android Pie. Ele explica como instalar as atualizações necessárias, configurar o SDK e preparar um emulador ou dispositivo para testes. Ele também fornece um esboço dos novos recursos no Android Pie e fornece código fonte de exemplo que ilustra como usar alguns dos principais recursos do Android Pie.

Xamarin.Android 9.0 oferece suporte para Android Pie. Para obter mais informações sobre o suporte ao Xamarin.Android para Android Pie, consulte as notas de versão do [Android P Developer Preview 3.](https://docs.microsoft.com/xamarin/android/release-notes/9/9.0/#android-p-dp1)

## <a name="requirements"></a>Requisitos

A lista a seguir é necessária para usar os recursos do Android Pie em aplicativos baseados em Xamarin:

- **Visual Studio** &ndash; Visual Studio 2019 é recomendado.
    Se você estiver usando o Visual Studio 2017, na atualização do Windows para o Visual Studio 2017 versão 15.8 ou posterior. No macOS, atualize para o Visual Studio 2017 para mac versão 7.6 ou posterior.

- **Xamarin.Android** &ndash; Xamarin.Android 9.0.0.17 ou posterior deve ser instalado com o Visual Studio (Xamarin.Android é instalado automaticamente como parte do desenvolvimento mobile com carga de trabalho **.NET).**

- **Java Developer Kit** &ndash; Xamarin O desenvolvimento do Android 9.0 requer [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (ou você pode tentar a visualização da distribuição da Microsoft do [OpenJDK](~/android/get-started/installation/openjdk.md)). O JDK8 é instalado automaticamente como parte do desenvolvimento mobile com carga de trabalho **.NET.**

- **Android SDK** &ndash; Android SDK API 28 ou posterior deve ser instalado através do Android SDK Manager.

## <a name="getting-started"></a>Introdução

Para começar a desenvolver aplicativos Android Pie com Xamarin.Android, você deve baixar e instalar as ferramentas mais recentes e pacotes SDK antes de criar seu primeiro projeto Android Pie:

1. É recomendável o Visual Studio 2019. Se você estiver usando o Visual Studio 2017, atualize para o [Visual Studio 2017 versão 15.8](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) ou posterior. Se você estiver usando o Visual Studio para Mac, atualize para [o Visual Studio 2017 para a versão 7.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) do Mac ou posterior.

2. Instale pacotes e ferramentas do **Android Pie (API 28)** através do SDK Manager.

3. Crie um novo projeto Xamarin.Android que tem como alvo **o Android 9.0**.

4. Configure um emulador ou dispositivo para testar aplicativos Android Pie.

Cada uma dessas etapas é explicada nas seguintes seções:

### <a name="update-visual-studio"></a>Atualizar o Visual Studio

Visual Studio 2019 é recomendado para a construção de aplicativos Android Pie usando Xamarin.

Se você estiver usando o Visual Studio 2017, atualize para o Visual Studio 2017 versão 15.8 ou posterior (para instruções, consulte [Update Visual Studio 2017 para a versão mais recente).](https://docs.microsoft.com/visualstudio/install/update-visual-studio) No macOS, atualize para o Visual Studio 2017 para Mac 7.6 ou posterior (para instruções, consulte [Setup and Install Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation)).

### <a name="install-the-android-sdk"></a>Instalar o SDK do Android

Para criar um projeto com xamarin.Android 9.0, primeiro você deve usar o Android SDK Manager para instalar a plataforma SDK para **Android Pie (Nível 28 da API)** ou posterior.

1. Ligue o Gerente sdk. No Visual Studio, clique em **Ferramentas > Android > Android SDK Manager**. No Visual Studio para Mac, clique em **Ferramentas > SDK Manager**.

2. No canto inferior direito, clique no ícone de engrenagem e selecione **Repositório > Google (Sem suporte):**

    [![Configuração do repositório no Google](pie-images/vs/set-repo-sml.png)](pie-images/vs/set-repo.png#lightbox)

3. Instale os pacotes **Do Android Pie** SDK, que estão listados como Plataforma **SDK Android 28** na guia **Plataformas** (para obter mais informações sobre o uso do SDK Manager, consulte [a configuração do Android SDK](~/android/get-started/installation/android-sdk.md)):

    [![Instalando pacotes Android Pie](pie-images/vs/sdk-manager-sml.png)](pie-images/vs/sdk-manager.png#lightbox)

4. Se você estiver usando um emulador, crie um dispositivo virtual que suporte **a API Nível 28**. Para obter mais informações sobre a criação de dispositivos virtuais, consulte [Gerenciamento de dispositivos virtuais com o Gerenciador de Dispositivos Android](~/android/get-started/installation/android-emulator/device-manager.md).

### <a name="start-a-xamarinandroid-project"></a>Inicie um projeto Xamarin.Android

Crie um novo projeto Xamarin.Android. Se você é novo no desenvolvimento do Android com xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para aprender sobre a criação de projetos Xamarin.Android.

Ao criar um projeto Android, você deve configurar as configurações da versão para atingir o Android 9.0 ou posterior. Por exemplo, para direcionar seu projeto para o Android Pie, você deve configurar o nível de API android de destino do seu projeto para **o Android 9.0** (API 28). Recomenda-se que você também defina seu nível de Quadro de Destino para API 28 ou posterior. Para obter mais informações sobre a configuração dos níveis de API do Android, consulte [Entendendo os níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).

### <a name="configure-a-device-or-emulator"></a>Configure um dispositivo ou emulador

Se você estiver usando um dispositivo físico como um Nexus ou um Pixel, você pode atualizar seu dispositivo para o Android Pie seguindo as instruções em [Imagens de Fábrica para Dispositivos Nexus e Pixel](https://developers.google.com/android/images).

Se você estiver usando um emulador, crie um dispositivo virtual para o nível 28 da API e selecione uma imagem baseada em x86. Para obter informações sobre como usar o Gerenciador de Dispositivos Android para criar e gerenciar dispositivos virtuais, consulte [Gerenciando dispositivos virtuais com o Gerenciador de Dispositivos Android](~/android/get-started/installation/android-emulator/device-manager.md).
Para obter informações sobre como usar o emulador do Android para testes e depuração, consulte [Depuração no Emulador do Android](~/android/deploy-test/debugging/debug-on-emulator.md).

## <a name="new-features"></a>Novos recursos

O Android Pie apresenta uma variedade de novos recursos. Alguns desses novos recursos destinam-se a aproveitar os novos recursos de hardware oferecidos pelos dispositivos Android mais recentes, enquanto outros foram projetados para melhorar ainda mais a experiência do usuário do Android:

- **Suporte ao recorte de** &ndash; exibição Fornece APIs para encontrar a localização e a forma do _recorte_ na parte superior da tela em dispositivos Android mais novos.

- **Aprimoramentos** &ndash; de notificação As mensagens de `Person` notificação agora podem exibir imagens e uma nova classe é usada para simplificar os participantes da conversa.

- **Suporte à** &ndash; plataforma de posicionamento interno para o protocolo WiFi Round-Trip-Time, que permite que os aplicativos usem dispositivos WiFi para navegação em configurações internas.

- **Suporte** &ndash; multi-câmeras Oferece a capacidade de acessar fluxos simultaneamente a partir de múltiplas câmeras físicas (como câmeras de dupla frente e traseira dupla).

As seções a seguir destacam esses recursos e fornecem breves exemplos de código para ajudá-lo a começar a usá-los em seu aplicativo.

### <a name="display-cutout-support"></a>Suporte de recorte de exibição

Muitos dispositivos Android mais novos com telas edge-to-edge têm um *Recorte de Display* (ou "notch") na parte superior do display para câmera e alto-falante.
A captura de tela a seguir fornece um exemplo de emulador de um recorte:

[![Emulador de Android simulando um recorte](pie-images/02-example-cutout-sml.png)](pie-images/02-example-cutout.png#lightbox)

Para gerenciar como a janela do aplicativo exibe seu conteúdo em dispositivos com um recorte de tela, o Android Pie adicionou um novo atributo de layout de janela [LayoutInDisplayCutoutMode.](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#layoutInDisplayCutoutMode) Este atributo pode ser definido como um dos seguintes valores:

- [LayoutInDisplayCutoutModeNever](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_NEVER) &ndash; A janela nunca pode se sobrepor à área de recorte.

- [LayoutInDisplayCutoutModeShortEdges](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES) &ndash; A janela pode se estender até a área de recorte, mas apenas nas bordas curtas da tela. 

- [LayoutInDisplayCutoutModeDefault](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_DEFAULT) &ndash; A janela pode se estender para a área de recorte se o recorte estiver contido dentro de uma barra do sistema.

Por exemplo, para evitar que a janela do aplicativo se sobrepor com a área de recorte, defina o modo de recorte do layout para *nunca*: 

```csharp
Window.Attributes.LayoutInDisplayCutoutMode =
    Android.Views.LayoutInDisplayCutoutMode.Never;
```

Os exemplos a seguir fornecem exemplos desses modos de recorte. A primeira captura de tela à esquerda é do aplicativo no modo não fullscreen. Na captura de tela central, o `LayoutInDisplayCutoutMode` aplicativo `LayoutInDisplayCutoutModeShortEdges`vai em tela cheia com set para . Observe que o fundo branco do aplicativo se estende para a área de recorte do display:

[![Exemplo de modos de recorte de tela no emulador](pie-images/03-cutout-modes-sml.png)](pie-images/03-cutout-modes.png#lightbox)

Na captura final de tela `LayoutInDisplayCutoutMode` (acima `LayoutInDisplayCutoutModeShortNever` à direita), é definido antes de ir para a tela cheia.
Observe que o fundo branco do aplicativo não pode se estender para a área de recorte do display.

Se você precisar de informações mais detalhadas sobre a área de recorte no dispositivo, você pode usar a nova classe [DisplayCutout.](https://developer.android.com/reference/android/view/DisplayCutout.html) `DisplayCutout`representa a área do display que não pode ser usada para exibir conteúdo. Você pode usar essas informações para recuperar a localização e a forma do recorte para que seu aplicativo não tente exibir conteúdo nesta área não funcional.

Para obter mais informações sobre os novos recursos de recorte no Android P, consulte [o suporte ao recorte display](https://developer.android.com/about/versions/pie/android-9.0#cutout).

### <a name="notifications-enhancements"></a>Aprimoramentos de notificações

O Android Pie apresenta os seguintes aprimoramentos para melhorar a experiência de mensagens:

- Os canais de notificação (introduzidos no [Android Oreo)](~/android/platform/oreo.md)agora suportam o bloqueio de grupos de canais.

- O sistema de notificação tem três novas categorias do Não-Perturbação (priorizando alarmes, sons do sistema e fontes de mídia). Além disso, existem sete novos modos Do-Not-Disturb que podem ser usados para suprimir interrupções visuais (como crachás, luzes de notificação, aparências de barras de status e lançamento de atividades em tela cheia).

- Uma nova [classe Person](https://developer.android.com/reference/android/app/Person.html) foi adicionada para representar o remetente de uma mensagem. O uso desta classe ajuda a otimizar a renderização de cada notificação, identificando pessoas envolvidas em uma conversa (incluindo seus avatares e URIs).

- As notificações agora podem exibir imagens. 

O exemplo a seguir ilustra como usar as novas APIs para gerar uma notificação que contenha uma imagem. Nas capturas de tela a seguir, uma notificação de texto é postada e é seguida por uma notificação com uma imagem incorporada. Quando as notificações são expandidas (como visto à direita), o texto da primeira notificação é exibido e a imagem incorporada na segunda notificação é ampliada:

[![Notificação por exemplo com imagem](pie-images/04-example-notifications-sml.png)](pie-images/04-example-notifications.png#lightbox)

O exemplo a seguir ilustra como incluir uma imagem em uma notificação `Person` do Android Pie, e demonstra o uso da nova classe:

1. Crie `Person` um objeto que represente o remetente. Por exemplo, o nome e o ícone `fromPerson`do remetente estão incluídos em :

    ```csharp
    Icon senderIcon = Icon.CreateWithResource(this, Resource.Drawable.sender_icon);
    Person fromPerson = new Person.Builder()
        .SetIcon(senderIcon)
        .SetName("Mark Sender")
        .Build();
    ```

2. Crie `Notification.MessagingStyle.Message` um que contenha a imagem a ser enviada, passando a imagem para o novo método [Notification.MessageingStyle.Message.SetData.](https://developer.android.com/reference/android/app/Notification.MessagingStyle.Message.html#setData%28java.lang.String,%20android.net.Uri)
   Por exemplo:

    ```csharp
    Uri imageUri = Uri.Parse("android.resource://com.xamarin.pminidemo/drawable/example_image");
    Notification.MessagingStyle.Message message = new Notification.MessagingStyle
            .Message("Here's a picture of where I'm currently standing", 0, fromPerson)
            .SetData("image/", imageUri);
    ```

3. Adicione a mensagem a um `Notification.MessagingStyle` objeto. Por exemplo:

    ```csharp
    Notification.MessagingStyle style = new Notification.MessagingStyle(fromPerson)
            .AddMessage(message);
    ```

4. Conecte este estilo ao construtor de notificações. Por exemplo:

    ```csharp
    builder = new Notification.Builder(this, MY_CHANNEL)
        .SetContentTitle("Tour of the Colosseum")
        .SetContentText("I'm standing right here!")
        .SetSmallIcon(Resource.Mipmap.ic_notification)
        .SetStyle(style)
        .SetChannelId(MY_CHANNEL);
    ```

5. Publique a notificação. Por exemplo:

    ```csharp
    const int notificationId = 1000;
    notificationManager.Notify(notificationId, builder.Build());
    ```

Para obter mais informações sobre como criar notificações, consulte [Notificações Locais](~/android/app-fundamentals/notifications/local-notifications.md).

### <a name="indoor-positioning"></a>Posicionamento interno

O Android Pie oferece suporte para o IEEE 802.11mc (também conhecido como _WiFi Round-Trip-Time_ ou _WiFi RTT),_ o que torna possível que os aplicativos detectem a distância para um ou mais pontos de acesso Wi-Fi. Usando essas informações, é possível que seu aplicativo aproveite o *posicionamento interno* com uma precisão de um a dois metros. Em dispositivos Android que fornecem suporte a hardware para o IEEE 801.11mc, seu aplicativo pode oferecer recursos de navegação, como controle baseado em localização de dispositivos inteligentes ou instruções turn-by-turn através de uma loja:

[![Exemplo de navegação interior usando WiFi RTT](pie-images/05-wifi-rtt-sml.png)](pie-images/05-wifi-rtt.png#lightbox)

A nova classe [WifiRttManager](https://developer.android.com/reference/android/net/wifi/rtt/WifiRttManager) e várias classes auxiliares fornecem os meios para medir a distância dos dispositivos Wi-Fi. Para obter mais informações sobre as APIs de posicionamento interno introduzidas no Android P, consulte [Android.Net.Wifi.Rtt](https://developer.android.com/reference/android/net/wifi/rtt/package-summary).

### <a name="multi-camera-support"></a>Suporte a várias câmeras

Muitos dispositivos Android mais novos têm câmeras dual-front e/ou dual-back que são úteis para recursos como visão estéreo, efeitos visuais aprimorados e capacidade de zoom melhorada. O Android P apresenta uma nova API [multi-câmera](https://developer.android.com/about/versions/pie/android-9.0#camera) que torna possível para o seu aplicativo usar uma *câmera lógica* (ou *lógica multi-câmera*) que é apoiada por duas ou mais câmeras físicas.
Para determinar se o dispositivo suporta uma câmera multilógica, você pode olhar para os recursos de cada câmera no dispositivo para ver se ele suporta [RequestAvailableCapabilitiesLogicalMultiCamera](https://developer.android.com/reference/android/hardware/camera2/CameraMetadata#REQUEST_AVAILABLE_CAPABILITIES_LOGICAL_MULTI_CAMERA).

O Android Pie também inclui uma nova classe [SessionConfiguration](https://developer.android.com/reference/android/hardware/camera2/params/SessionConfiguration.html) que pode ser usada para ajudar a reduzir atrasos durante a captura inicial e eliminar a necessidade de iniciar e iniciar o fluxo da câmera.

Para obter mais informações sobre o suporte a várias câmeras no Android P, consulte [suporte a várias câmeras e atualizações de câmera](https://developer.android.com/about/versions/pie/android-9.0#camera).

### <a name="other-features"></a>Outros recursos

Além disso, o Android Pie suporta vários outros novos recursos:

- A nova classe [AnimatedImageDrawable,](https://developer.android.com/reference/android/graphics/drawable/AnimatedImageDrawable.html) que pode ser usada para desenhar e exibir imagens animadas.

- Uma nova classe [ImageDecoder](https://developer.android.com/reference/android/graphics/ImageDecoder.html) que substitui `BitmapFactory`. `ImageDecoder`pode ser usado para `AnimatedImageDrawable`decodificar um .

- Suporte para imagens de vídeo HDR (High Dynamic Range) e HEIF (High Efficiency Image File Format).

- O [JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html) foi aprimorado para lidar com trabalhos mais inteligentes relacionados à rede. O novo método [GetNetwork](https://developer.android.com/reference/android/app/job/JobParameters#getNetwork%28%29) da classe [JobParameters](https://developer.android.com/reference/android/app/job/JobParameters) retorna a melhor rede para realizar quaisquer solicitações de rede para um determinado trabalho.

Para obter mais informações sobre os recursos mais recentes do Android Pie, consulte [recursos e APIs do Android 9.](https://developer.android.com/about/versions/pie/android-9.0)

## <a name="behavior-changes"></a>Alterações de comportamento

Quando a versão target do Android é definida como Nível 28 da API, existem várias alterações na plataforma que podem afetar o comportamento do seu aplicativo, mesmo que você não esteja implementando os novos recursos descritos acima. A lista a seguir é um breve resumo dessas alterações:

- Os aplicativos agora devem solicitar permissão em primeiro plano antes de usar serviços em primeiro plano.

- Se o seu aplicativo tiver mais de um processo, ele não poderá compartilhar um único diretório de dados [do WebView](xref:Android.Webkit.WebView) entre os processos.

- Acessar diretamente o diretório de dados de outro aplicativo por caminho não é mais permitido.

Para obter mais informações sobre mudanças de comportamento para aplicativos direcionados ao Android P, consulte [Mudanças de comportamento](https://developer.android.com/about/versions/pie/android-9.0-changes-all#p-apps).

## <a name="sample-code"></a>Código de exemplo

[AndroidPMiniDemo](https://github.com/xamarin/monodroid-samples/tree/master/android-p/AndroidPMiniDemo) é um aplicativo de amostra xamarin.Android para Android Pie que demonstra como `Person` definir modos de recorte de exibição, como usar a nova classe e como enviar uma notificação que inclui uma imagem.

## <a name="summary"></a>Resumo

Este artigo introduziu o Android Pie e explicou como instalar e configurar as ferramentas e pacotes mais recentes para o desenvolvimento do Xamarin.Android com Android Pie. Ele forneceu uma visão geral dos principais recursos disponíveis no Android Pie, com exemplo de código fonte para vários desses recursos.
Ele incluiu links para documentação da API e tópicos do Android Developer para ajudá-lo a começar a criar aplicativos para Android Pie. Ele também destacou as mudanças de comportamento mais importantes do Android Pie que poderiam impactar os aplicativos existentes.

## <a name="related-links"></a>Links relacionados

- [Android 9 Pie](https://developer.android.com/about/versions/pie/)
