---
title: Android 9 Pie
description: Como começar a desenvolver aplicativos para a pizza do Android 9 usando o Xamarin. Android.
ms.prod: xamarin
ms.assetid: 6575DD32-9DC8-44E6-85EF-1F8BD07D3780
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2018
ms.openlocfilehash: 52141141ab525c7407fa2f3ff2dca749473b39c1
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511448"
---
# <a name="android-pie-features"></a>Recursos de pizza do Android

_Como começar a desenvolver aplicativos para a pizza do Android 9 usando o Xamarin. Android._

A [pizza do Android 9](https://developer.android.com/about/versions/pie/) agora está disponível no Google. Vários novos recursos e APIs estão sendo disponibilizados nesta versão, e muitos deles são necessários para aproveitar os novos recursos de hardware nos dispositivos Android mais recentes.

![Imagem Hero de pizza do Android](pie-images/01-android-p-logo.png)

Este artigo foi estruturado para ajudá-lo a começar a desenvolver aplicativos Xamarin. Android para a pizza do Android. Ele explica como instalar as atualizações necessárias, configurar o SDK e preparar um emulador ou dispositivo para teste. Ele também fornece uma descrição dos novos recursos na pizza do Android e fornece código-fonte de exemplo que ilustra como usar alguns dos principais recursos de pizza do Android.

O Xamarin. Android 9,0 fornece suporte para a pizza do Android. Para obter mais informações sobre o suporte do Xamarin. Android para a pizza do Android, consulte as notas de versão do [Android P Developer Preview 3](https://docs.microsoft.com/xamarin/android/release-notes/9/9.0/#android-p-dp1) .

## <a name="requirements"></a>Requisitos

A lista a seguir é necessária para usar os recursos de pizza do Android em aplicativos baseados no Xamarin:

- **Visual Studio** &ndash; O Visual Studio 2019 é recomendado.
    Se você estiver usando o Visual Studio 2017, no Windows Update para o Visual Studio 2017 versão 15,8 ou posterior. No macOS, atualize para o Visual Studio 2017 para Mac versão 7,6 ou posterior.

- **Xamarin. Android** &ndash; xamarin. Android 9.0.0.17 ou posterior deve ser instalado com o Visual Studio (o xamarin. Android é instalado automaticamente como parte do **desenvolvimento móvel com** carga de trabalho do .net).

- **Java Developer Kit** &ndash;O desenvolvimento do Xamarin Android 9,0 requer o [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (ou você pode experimentar a versão prévia da distribuição da Microsoft do [OpenJDK](~/android/get-started/installation/openjdk.md)). O JDK8 é instalado automaticamente como parte do **desenvolvimento móvel com** carga de trabalho do .net.

- **SDK do Android** &ndash; SDK do Android API 28 ou posterior deve ser instalada por meio do Gerenciador de SDK do Android.

## <a name="getting-started"></a>Introdução

Para começar a desenvolver aplicativos de pizza do Android com o Xamarin. Android, você deve baixar e instalar as ferramentas e os pacotes de SDK mais recentes antes de criar seu primeiro projeto de pizza do Android:

1. É recomendável o Visual Studio 2019. Se você estiver usando o Visual Studio 2017, atualize para o [visual studio 2017 versão 15,8](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) ou posterior. Se você estiver usando Visual Studio para Mac, atualize para o [Visual Studio 2017 para Mac versão 7,6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) ou posterior.

2. Instale pacotes e ferramentas de **pizza do Android (API 28)** por meio do Gerenciador do SDK.

3. Crie um novo projeto Xamarin. Android que tenha como destino o **Android 9,0**.

4. Configure um emulador ou dispositivo para testar aplicativos de pizza do Android.

Cada uma dessas etapas é explicada nas seguintes seções:

### <a name="update-visual-studio"></a>Atualizar o Visual Studio

O Visual Studio 2019 é recomendado para a criação de aplicativos de pizza do Android usando o Xamarin.

Se você estiver usando o Visual Studio 2017, atualize para o Visual Studio 2017 versão 15,8 ou posterior (para obter instruções, consulte [atualizar o visual studio 2017 para a versão mais recente](https://docs.microsoft.com/visualstudio/install/update-visual-studio)). No macOS, atualize para o Visual Studio 2017 para Mac 7,6 ou posterior (para obter instruções, consulte [configurar e instalar Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/installation)).

### <a name="install-the-android-sdk"></a>Instalar o SDK do Android

Para criar um projeto com o Xamarin. Android 9,0, primeiro você deve usar o SDK do Android Manager para instalar a plataforma SDK para a **pizza do Android (API nível 28)** ou posterior.

1. Inicie o Gerenciador do SDK. No Visual Studio, clique em **ferramentas > Android > o Gerenciador de SDK do Android**. Em Visual Studio para Mac, clique em **ferramentas > Gerenciador do SDK**.

2. No canto inferior direito, clique no ícone de engrenagem e selecione **repositório > Google (sem suporte)** :

    [![Configurando o repositório para o Google](pie-images/vs/set-repo-sml.png)](pie-images/vs/set-repo.png#lightbox)

3. Instale os pacotes do SDK de **pizza do Android** , listados como **SDK do Android plataforma 28** na guia **plataformas** (para obter mais informações sobre como usar o Gerenciador do SDK, consulte [SDK do Android setup](~/android/get-started/installation/android-sdk.md)):

    [![Instalando pacotes de pizza do Android](pie-images/vs/sdk-manager-sml.png)](pie-images/vs/sdk-manager.png#lightbox)

4. Se você estiver usando um emulador, crie um dispositivo virtual que dê suporte ao **nível de API 28**. Para obter mais informações sobre como criar dispositivos virtuais, consulte [Managing Virtual Devices With the Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).

### <a name="start-a-xamarinandroid-project"></a>Iniciar um projeto Xamarin. Android

Crie um novo projeto Xamarin. Android. Se você for novo no desenvolvimento do Android com o Xamarin, consulte [Olá, Android](~/android/get-started/hello-android/index.md) para saber mais sobre como criar projetos do Xamarin. Android.

Ao criar um projeto do Android, você deve definir as configurações de versão para o Android 9,0 ou posterior de destino. Por exemplo, para direcionar seu projeto para a pizza do Android, você deve configurar o nível de API do Android de destino do seu projeto para **Android 9,0** (API 28). É recomendável que você também defina seu nível de estrutura de destino como API 28 ou posterior. Para saber mais sobre como configurar os níveis de API do Android, confira [noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md)


### <a name="configure-a-device-or-emulator"></a>Configurar um dispositivo ou emulador

Se você estiver usando um dispositivo físico, como um Nexus ou um pixel, poderá atualizar seu dispositivo para a pizza do Android seguindo as instruções em [imagens de fábrica para dispositivos Nexus e de pixel](https://developers.google.com/android/images).

Se você estiver usando um emulador, crie um dispositivo virtual para o nível de API 28 e selecione uma imagem baseada em x86. Para obter informações sobre como usar o Android Device Manager para criar e gerenciar dispositivos virtuais, consulte [Gerenciando dispositivos virtuais com o Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).
Para obter informações sobre como usar o emulador do Android para teste e depuração, consulte Depurando [no Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md).



## <a name="new-features"></a>Novos recursos

A pizza do Android apresenta uma variedade de novos recursos. Alguns desses novos recursos destinam-se a aproveitar os novos recursos de hardware oferecidos pelos dispositivos Android mais recentes, enquanto outros foram projetados para aprimorar ainda mais a experiência do usuário do Android:

-   **Exibir suporte** a recorte Fornece APIs para localizar o local e a forma do recorte na parte superior da tela em dispositivos Android mais recentes. &ndash;

-   **Aprimoramentos de notificação** As mensagens de notificação agora podem exibir imagens e uma `Person` nova classe é usada para simplificar os participantes da conversa. &ndash;

-   **Posicionamento interno** &ndash; Suporte de plataforma para o protocolo de tempo de ida e volta de WiFi, o que possibilita que os aplicativos usem dispositivos WiFi para navegação em configurações de interno.

-   **Suporte a várias câmeras** &ndash; Oferece a capacidade de acessar fluxos simultaneamente de várias câmeras físicas (como câmeras duplas e duplas).


As seções a seguir destacam esses recursos e fornecem exemplos de código breve para ajudá-lo a começar a usá-los em seu aplicativo.

### <a name="display-cutout-support"></a>Exibir suporte a recorte

Muitos dispositivos Android mais recentes com telas de borda a borda têm um *recorte de exibição* (ou "entalhe") na parte superior da exibição para câmera e palestrante.
A captura de tela a seguir fornece um exemplo de emulador de um recorte:

[![Emulador do Android simulando um recorte](pie-images/02-example-cutout-sml.png)](pie-images/02-example-cutout.png#lightbox)

Para gerenciar como a janela do aplicativo exibe seu conteúdo em dispositivos com um recorte de exibição, a pizza do Android adicionou um novo atributo de layout de janela [LayoutInDisplayCutoutMode](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#layoutInDisplayCutoutMode) . Esse atributo pode ser definido como um dos seguintes valores:

-   [LayoutInDisplayCutoutModeNever](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_NEVER) &ndash; A janela nunca pode se sobrepor à área de recorte.

-   [LayoutInDisplayCutoutModeShortEdges](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES) &ndash; A janela tem permissão para se estender para a área de recorte, mas apenas nas bordas curtas da tela. 

-   [LayoutInDisplayCutoutModeDefault](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_DEFAULT) &ndash; A janela poderá ser estendida para a área de recorte se o recorte estiver contido em uma barra do sistema.

Por exemplo, para impedir que a janela do aplicativo se sobreponha à área de recorte, defina o modo de recorte de layout como *nunca*: 

```csharp
Window.Attributes.LayoutInDisplayCutoutMode =
    Android.Views.LayoutInDisplayCutoutMode.Never;
```

Os exemplos a seguir fornecem exemplos desses modos de recorte. A primeira captura de tela à esquerda é do aplicativo em modo não-fullscreen. Na captura de tela do centro, o aplicativo é preenchido com `LayoutInDisplayCutoutMode` o botão `LayoutInDisplayCutoutModeShortEdges`completo com definido como. Observe que o plano de fundo branco do aplicativo se estende à área de recorte de exibição:

[![Exemplo exibe modos de recorte no emulador](pie-images/03-cutout-modes-sml.png)](pie-images/03-cutout-modes.png#lightbox)

Na captura de tela final (acima à direita), `LayoutInDisplayCutoutMode` é definido como `LayoutInDisplayCutoutModeShortNever` antes de ir para o ecrã inteiro.
Observe que o plano de fundo branco do aplicativo não tem permissão para estender para a área de recorte de exibição.

Se precisar de informações mais detalhadas sobre a área de recorte no dispositivo, você poderá usar a nova classe [DisplayCutout](https://developer.android.com/reference/android/view/DisplayCutout.html) . `DisplayCutout`representa a área da exibição que não pode ser usada para exibir o conteúdo. Você pode usar essas informações para recuperar o local e a forma do recorte para que seu aplicativo não tente exibir o conteúdo nessa área não funcional.

Para obter mais informações sobre os novos recursos de recorte no Android P, consulte [Exibir suporte ao recorte](https://developer.android.com/about/versions/pie/android-9.0#cutout).



### <a name="notifications-enhancements"></a>Aprimoramentos de notificações

A pizza do Android apresenta os seguintes aprimoramentos para melhorar a experiência de mensagens:

-   Os canais de notificação (introduzidos no [Android Oreo](~/android/platform/oreo.md)) agora dão suporte ao bloqueio de grupos de canais.

-   O sistema de notificação tem três novas categorias do-not-incomodar (priorização de alarmes, sons do sistema e fontes de mídia). Além disso, há sete novos modos não incomodar que podem ser usados para suprimir interrupções visuais (como notificações, luzes de notificação, aparências da barra de status e inicialização de atividades em tela inteira).

-   Uma nova classe [Person](https://developer.android.com/reference/android/app/Person.html) foi adicionada para representar o remetente de uma mensagem. O uso dessa classe ajuda a otimizar a renderização de cada notificação identificando pessoas envolvidas em uma conversa (incluindo seus avatars e URIs).

-   As notificações agora podem exibir imagens. 

O exemplo a seguir ilustra como usar as novas APIs para gerar uma notificação que contém uma imagem. Nas capturas de tela a seguir, uma notificação de texto é postada e seguida por uma notificação com uma imagem inserida. Quando as notificações são expandidas (como visto à direita), o texto da primeira notificação é exibido e a imagem inserida na segunda notificação é ampliada:

[![Exemplo de notificação com imagem](pie-images/04-example-notifications-sml.png)](pie-images/04-example-notifications.png#lightbox)

O exemplo a seguir ilustra como incluir uma imagem em uma notificação de pizza do Android e demonstra o uso da nova `Person` classe:

1. Crie um `Person` objeto que represente o remetente. Por exemplo, o nome e o ícone do remetente estão incluídos `fromPerson`em:

    ```csharp
    Icon senderIcon = Icon.CreateWithResource(this, Resource.Drawable.sender_icon);
    Person fromPerson = new Person.Builder()
        .SetIcon(senderIcon)
        .SetName("Mark Sender")
        .Build();
    ```

2. Crie um `Notification.MessagingStyle.Message` que contenha a imagem a ser enviada, passando a imagem para o novo método [Notification. messagingcollection. Message. SetData](https://developer.android.com/reference/android/app/Notification.MessagingStyle.Message.html#setData%28java.lang.String,%20android.net.Uri) .
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

Para obter mais informações sobre como criar notificações, consulte [notificações locais](~/android/app-fundamentals/notifications/local-notifications.md).


### <a name="indoor-positioning"></a>Posicionamento interno

A pizza do Android fornece suporte para o IEEE 802.11 MC (também conhecido como _tempo de ida_ e volta do WiFi ou _RTT de WiFi_), o que possibilita que os aplicativos detectem a distância para um ou mais pontos de acesso Wi-Fi. Usando essas informações, é possível que seu aplicativo aproveite o *posicionamento interno* com uma precisão de um a dois metros. Em dispositivos Android que fornecem suporte de hardware para o IEEE 801.11 MC, seu aplicativo pode oferecer recursos de navegação, como o controle baseado em local de dispositivos inteligentes ou instruções ativadas por meio de uma loja:

[![Exemplo de navegação em interno usando RTT de WiFi](pie-images/05-wifi-rtt-sml.png)](pie-images/05-wifi-rtt.png#lightbox)

A nova classe [WifiRttManager](https://developer.android.com/reference/android/net/wifi/rtt/WifiRttManager) e várias classes auxiliares fornecem os meios para medir a distância para dispositivos Wi-Fi. Para obter mais informações sobre as APIs de posicionamento interno introduzidas no Android P, consulte [Android .net. WiFi. RTT](https://developer.android.com/reference/android/net/wifi/rtt/package-summary).


### <a name="multi-camera-support"></a>Suporte a várias câmeras

Muitos dispositivos Android mais recentes têm câmeras duplas e/ou duplas que são úteis para recursos como visão de estéreo, efeitos visuais aprimorados e capacidade de zoom aprimorada. O Android P apresenta uma nova API de [várias câmeras](https://developer.android.com/about/versions/pie/android-9.0#camera) que possibilita ao seu aplicativo usar uma *câmera lógica* (ou uma multicâmera *lógica*) com suporte de duas ou mais câmeras físicas.
Para determinar se o dispositivo oferece suporte a várias câmeras lógicas, você pode examinar os recursos de cada câmera no dispositivo para ver se ele dá suporte a [RequestAvailableCapabilitiesLogicalMultiCamera](https://developer.android.com/reference/android/hardware/camera2/CameraMetadata#REQUEST_AVAILABLE_CAPABILITIES_LOGICAL_MULTI_CAMERA).

A pizza do Android também inclui uma nova classe [SessionConfiguration](https://developer.android.com/reference/android/hardware/camera2/params/SessionConfiguration.html) que pode ser usada para ajudar a reduzir atrasos durante a captura inicial e eliminar a necessidade de iniciar e iniciar o fluxo da câmera.

Para obter mais informações sobre o suporte a várias câmeras no Android P, consulte [suporte a várias câmeras e atualizações da câmera](https://developer.android.com/about/versions/pie/android-9.0#camera).


### <a name="other-features"></a>Outros recursos

Além disso, a pizza do Android dá suporte a vários outros recursos novos:

-   A nova classe [AnimatedImageDrawable](https://developer.android.com/reference/android/graphics/drawable/AnimatedImageDrawable.html) , que pode ser usada para desenhar e exibir imagens animadas.

-   Uma nova classe [ImageDecoder](https://developer.android.com/reference/android/graphics/ImageDecoder.html) que substitui `BitmapFactory`. `ImageDecoder`pode ser usado para decodificar `AnimatedImageDrawable`um.

-   Suporte para imagens de vídeo HDR (alta faixa dinâmica) e HEIF (formato de arquivo de imagem de alta eficiência).

-   O [JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html) foi aprimorado para lidar com mais inteligência com os trabalhos relacionados à rede. O novo método [getnetwork](https://developer.android.com/reference/android/app/job/JobParameters#getNetwork%28%29) da classe [JobParameters](https://developer.android.com/reference/android/app/job/JobParameters) retorna a melhor rede para executar qualquer solicitação de rede para um determinado trabalho.

Para obter mais informações sobre os recursos mais recentes de pizza do Android, consulte [APIs e recursos do Android 9](https://developer.android.com/about/versions/pie/android-9.0).


## <a name="behavior-changes"></a>Alterações de comportamento

Quando a versão de destino do Android é definida como API nível 28, há várias alterações de plataforma que podem afetar o comportamento do seu aplicativo mesmo se você não estiver implementando os novos recursos descritos acima. A lista a seguir é um breve resumo dessas alterações:

-  Os aplicativos agora devem solicitar permissão de primeiro plano antes de usar os serviços em primeiro plano.

-  Se seu aplicativo tiver mais de um processo, ele não poderá compartilhar um único diretório de dados [WebView](xref:Android.Webkit.WebView) entre processos.

-  O acesso direto ao diretório de dados de outro aplicativo por caminho não é mais permitido.

Para obter mais informações sobre alterações de comportamento para aplicativos direcionados para Android P, consulte [alterações de comportamento](https://developer.android.com/about/versions/pie/android-9.0-changes-all#p-apps).


## <a name="sample-code"></a>Código de exemplo

[AndroidPMiniDemo](https://github.com/xamarin/monodroid-samples/tree/master/android-p/AndroidPMiniDemo) é um aplicativo de exemplo do Xamarin. Android para pizza do Android que demonstra como definir modos de recorte de exibição, `Person` como usar a nova classe e como enviar uma notificação que inclui uma imagem.


## <a name="summary"></a>Resumo

Este artigo apresentou a pizza do Android e explicou como instalar e configurar as ferramentas e os pacotes mais recentes para desenvolvimento do Xamarin. Android com a pizza do Android. Ele forneceu uma visão geral dos principais recursos disponíveis na pizza do Android, com código-fonte de exemplo para vários desses recursos.
Ele inclui links para documentação da API e tópicos para desenvolvedores do Android para ajudá-lo a começar a criar aplicativos para a pizza do Android. Ele também realçou as alterações de comportamento de pizza do Android mais importantes que poderiam afetar os aplicativos existentes.


## <a name="related-links"></a>Links relacionados

- [Pizza do Android 9](https://developer.android.com/about/versions/pie/)
