---
title: Android pizza 9
description: Como começar a desenvolver aplicativos para Android pizza 9 usando xamarin. Android.
ms.prod: xamarin
ms.assetid: 6575DD32-9DC8-44E6-85EF-1F8BD07D3780
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2018
ms.openlocfilehash: c353341af8899960b12437d55602415a02953cbc
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111467"
---
# <a name="android-pie-features"></a>Recursos de pizza do Android

_Como começar a desenvolver aplicativos para Android pizza 9 usando xamarin. Android._

[Pizza 9 Android](https://developer.android.com/about/versions/pie/) agora está disponível no Google. Um número de novos recursos e APIs está sendo disponibilizado nesta versão, e muitas delas são necessárias para tirar proveito dos novos recursos de hardware em dispositivos Android mais recentes.

![Imagem do herói pizza Android](pie-images/01-android-p-logo.png)

Este artigo é estruturado para ajudá-lo a começar a desenvolver aplicativos xamarin. Android para Android pizza. Ele explica como instalar as atualizações necessárias, configure o SDK e preparar um emulador ou dispositivo para teste. Ele também fornece uma descrição dos novos recursos no Android pizza e fornece código-fonte de exemplo que ilustra como usar alguns dos principais recursos do Android pizza.

![Visualizar](~/media/shared/preview.png)

Xamarin. Android 9.0 fornece suporte para Android pizza visualização. Para obter mais informações sobre o suporte de xamarin. Android para Android de pizza, consulte o [Android P Developer Preview 3](https://developer.xamarin.com/releases/android/xamarin.android_9/xamarin.android_9.0/#android-p-dp1) notas de versão.

## <a name="requirements"></a>Requisitos

A lista a seguir é necessário para usar os recursos do Android pizza em aplicativos baseados em Xamarin:

-   **Visual Studio** &ndash; se você estiver usando o Windows, atualize para o Visual Studio 2017 versão 15,8 ou posterior. Se você estiver usando um Mac, atualize para o Visual Studio 2017 para Mac versão 7,6 ou posterior.

-   **Xamarin. Android** &ndash; xamarin. Android ão 9.0.0.17 ou posterior deve ser instalado com o Visual Studio (xamarin. Android é instalado automaticamente como parte do **desenvolvimento móvel com .NET** carga de trabalho).

-   **Java Developer Kit** &ndash; 9.0 Android do Xamarin de desenvolvimento requer [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (ou você pode tentar a visualização da distribuição da Microsoft dos [OpenJDK](~/android/get-started/installation/openjdk.md)). JDK8 é instalado automaticamente como parte do **desenvolvimento móvel com .NET** carga de trabalho.

-   **SDK do Android** &ndash; API do Android SDK 28 ou posterior deve ser instalado por meio do Gerenciador de SDK do Android.

## <a name="getting-started"></a>Introdução

Para começar a desenvolver aplicativos de pizza Android com xamarin. Android, você deve baixar e instalar os pacotes do SDK e ferramentas mais recentes antes de criar seu primeiro projeto do Android pizza:

1. Atualizar para [Visual Studio 2017 versão 15,8](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) ou posterior. Se você estiver usando o Visual Studio para Mac, atualizar para [Visual Studio 2017 para Mac versão 7.6](https://docs.microsoft.com/visualstudio/releasenotes/vs2017-relnotes) ou posterior.

2. Instale **pizza Android (API 28)** pacotes e ferramentas por meio do Gerenciador de SDK.

3. Criar um novo projeto do xamarin. Android que direciona **9.0 Android**.

4. Configure um emulador ou dispositivo para testar aplicativos Android pizza.

Cada uma dessas etapas é explicada nas seções a seguir:


### <a name="update-visual-studio"></a>Atualizar o Visual Studio

Para adicionar suporte de pizza Android para o Visual Studio, atualize o Visual Studio 2017 versão 15,8 ou posterior (para obter instruções, consulte [atualizar o Visual Studio 2017 para a versão mais recente](https://docs.microsoft.com/visualstudio/install/update-visual-studio)).

Para adicionar suporte de pizza Android para o Visual Studio para Mac, atualize o Visual Studio 2017 para Mac 7.6 ou posterior (para obter instruções, consulte [programa de instalação e instalar o Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/installation)).


### <a name="install-the-android-sdk"></a>Instalar o SDK do Android

Para criar um projeto com o xamarin. Android 9.0, primeiro você deve usar o Gerenciador de SDK do Android para instalar a plataforma SDK para **pizza Android (API nível 28)** ou posterior.

1. Inicie o Gerenciador de SDK. No Visual Studio, clique em **Ferramentas > Android > Gerenciador de SDK do Android**. No Visual Studio para Mac, clique em **Ferramentas > Gerenciador de SDK**.

2. No canto inferior direito, clique no ícone de engrenagem e selecione **repositório > Google (sem suporte)**:

    [![Configurando o repositório do Google](pie-images/vs/set-repo-sml.png)](pie-images/vs/set-repo.png#lightbox)

3. Instalar o **Android pizza** pacotes SDK, que são listados como **28 de plataforma do SDK do Android** no **plataformas** guia (para obter mais informações sobre como usar o Gerenciador de SDK, consulte [ Instalação do SDK do Android](~/android/get-started/installation/android-sdk.md)):

    [![Instalar os pacotes de pizza Android](pie-images/vs/sdk-manager-sml.png)](pie-images/vs/sdk-manager.png#lightbox)

4. Se você estiver usando um emulador, criar um dispositivo virtual que dá suporte a **28 de nível de API**. Para obter mais informações sobre como criar dispositivos virtuais, consulte [gerenciamento de dispositivos virtuais com o Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).



### <a name="start-a-xamarinandroid-project"></a>Iniciar um projeto xamarin. Android

Crie um novo projeto do xamarin. Android. Se você for novo no desenvolvimento de Android com Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para saber mais sobre como criar projetos do xamarin. Android.

Quando você cria um projeto do Android, você deve configurar as configurações de versão destino Android 9.0 ou posterior. Por exemplo, para direcionar seu projeto para pizza Android, você deve configurar o nível de API do Android de destino do seu projeto para **9.0 Android** (28 de API). É recomendável que você também definir a estrutura de destino nível para 28 de API ou posterior. Para obter mais informações sobre como configurar níveis de API do Android, consulte [Noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).


### <a name="configure-a-device-or-emulator"></a>Configurar um dispositivo ou emulador

Se você estiver usando um dispositivo físico, como um Nexus ou um Pixel, você pode atualizar o dispositivo para Android pizza seguindo as instruções em [Factory imagens para dispositivos de Pixel e Nexus](https://developers.google.com/android/images).

Se você estiver usando um emulador, crie um dispositivo virtual para o nível de API 28 e selecione uma imagem com base em x86. Para obter informações sobre como usar o Gerenciador de dispositivos do Android para criar e gerenciar dispositivos virtuais, consulte [gerenciamento de dispositivos virtuais com o Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).
Para obter informações sobre como usar o emulador do Android para teste e depuração, consulte [depuração no emulador do Android](~/android/deploy-test/debugging/debug-on-emulator.md).



## <a name="new-features"></a>Novos recursos

Pizza Android apresenta uma variedade de novos recursos. Alguns desses novos recursos destinam-se para aproveitar os novos recursos de hardware oferecidos pelos dispositivos Android mais recente, enquanto outras são desenvolvidas para melhorar ainda mais a experiência do usuário do Android:

-   **Exibir o suporte de recorte** &ndash; fornece APIs para encontrar o local e a forma dos _recorte_ na parte superior da tela em dispositivos Android mais recentes.

-   **Aprimoramentos de notificação** &ndash; mensagens de notificação agora podem exibir imagens e um novo `Person` classe é usada para simplificar os participantes da conversa.

-   **Posicionamento interno** &ndash; suporte de plataforma para o protocolo de tempo de viagem ida Wi-Fi, que permite que aplicativos usem dispositivos Wi-Fi para navegação nas configurações interno.

-   **Suporte para a câmera** &ndash; oferece a capacidade para fluxos de acesso simultaneamente de vários câmeras físicas (por exemplo, câmeras dual-frente e verso de duplo).


As seções a seguir destacam esses recursos e fornecem exemplos de código breve para ajudar você a começar a usá-las em seu aplicativo.

### <a name="display-cutout-support"></a>Suporte à exibição de recorte

Muitos dispositivos Android mais recentes com a borda telas têm uma *recorte exibir* (ou "entalhe") na parte superior da tela para a câmera e alto-falante.
Captura de tela a seguir fornece um exemplo de emulador de corte:

[![Emulador do Android simulando um recorte](pie-images/02-example-cutout-sml.png)](pie-images/02-example-cutout.png#lightbox)

Para gerenciar como sua janela de aplicativo exibe seu conteúdo em dispositivos com um recorte de exibição, pizza Android adicionou uma nova [LayoutInDisplayCutoutMode](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#layoutInDisplayCutoutMode) atributo de layout de janela. Esse atributo pode ser definido para um dos seguintes valores:

-   [LayoutInDisplayCutoutModeNever](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_NEVER) &ndash; janela jamais é permitida para a área de recorte se sobrepor.

-   [LayoutInDisplayCutoutModeShortEdges](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES) &ndash; a janela tem permissão para estender para a área de recorte, mas somente nas bordas curtas da tela. 

-   [LayoutInDisplayCutoutModeDefault](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#LAYOUT_IN_DISPLAY_CUTOUT_MODE_DEFAULT) &ndash; a janela tem permissão para estender até a área de recorte, se o recorte estiver contido dentro de uma barra de sistema.

Por exemplo, para impedir que a janela do aplicativo sobrepostos com a área de recorte, defina o modo de recorte de layout como *nunca*: 

```csharp
Window.Attributes.LayoutInDisplayCutoutMode =
    Android.Views.LayoutInDisplayCutoutMode.Never;
```

Os exemplos a seguir fornecem exemplos desses modos de recorte. É a primeira captura de tela à esquerda do aplicativo no modo de não-tela inteira. A captura de tela do centro, o aplicativo fica em tela inteira com `LayoutInDisplayCutoutMode` definido como `LayoutInDisplayCutoutModeShortEdges`. Observe que o plano de fundo branco do aplicativo se estende para a área de recorte de exibição:

[![Modos de recorte de exibição de exemplo no emulador](pie-images/03-cutout-modes-sml.png)](pie-images/03-cutout-modes.png#lightbox)

Na captura de tela final (acima à direita), `LayoutInDisplayCutoutMode` é definido como `LayoutInDisplayCutoutModeShortNever` antes da transferência para tela inteira.
Observe que o plano de fundo branco do aplicativo não é permitido para estender para a área de recorte de exibição.

Se você precisar de informações mais detalhadas sobre a área de recorte no dispositivo, você pode usar o novo [DisplayCutout](https://developer.android.com/reference/android/view/DisplayCutout.html) classe. `DisplayCutout` representa a área de exibição que não pode ser usada para exibir o conteúdo. Você pode usar essas informações para recuperar o local e a forma de recorte para que seu aplicativo não tenta exibir o conteúdo nesta área não-funcionais.

Para obter mais informações sobre os novos recursos de recorte no Android P, consulte [recorte suporte a vídeo](https://developer.android.com/preview/features#cutout).



### <a name="notifications-enhancements"></a>Aprimoramentos de notificações

Pizza Android apresenta os seguintes aprimoramentos para melhorar a experiência de mensagens:

-   Canais de notificação (introduzido no [Android Oreo](~/android/platform/oreo.md)) agora dá suporte ao bloqueio de grupos de canais.

-   O sistema de notificação tem três novos faça-não incomodar categorias (priorizando alarmes, sons do sistema e origens de mídia). Além disso, há sete novos faça-não incomodar modos que podem ser usados para suprimir as interrupções visual (por exemplo, notificações, luzes de notificação, aparências de barra de status e a inicialização de atividades de tela inteira).

-   Uma nova [pessoa](https://developer.android.com/reference/android/app/Person.html) classe foi adicionada para representar o remetente de uma mensagem. Uso dessa classe ajuda a otimizar o processamento de cada notificação por meio da identificação de pessoas envolvidas em uma conversa (incluindo suas avatares e URIs).

-   As notificações agora podem exibir imagens. 

O exemplo a seguir ilustra como usar as novas APIs para gerar uma notificação que contém uma imagem. Nas capturas de tela seguir, uma notificação de texto é lançada e é seguida por uma notificação com uma imagem inserida. Quando as notificações são expandidas (como visto no lado direito), o texto da primeira notificação será exibido e a imagem incorporada na segunda notificação é ampliada:

[![Exemplo de notificação com imagem](pie-images/04-example-notifications-sml.png)](pie-images/04-example-notifications.png#lightbox)

O exemplo a seguir ilustra como incluir uma imagem em uma notificação de pizza Android, e ele demonstra o uso do novo `Person` classe:

1. Criar um `Person` objeto que representa o remetente. Por exemplo, o nome do remetente e o ícone estão incluídos no `fromPerson`:

    ```csharp
    Icon senderIcon = Icon.CreateWithResource(this, Resource.Drawable.sender_icon);
    Person fromPerson = new Person.Builder()
        .SetIcon(senderIcon)
        .SetName("Mark Sender")
        .Build();
    ```

2. Criar uma `Notification.MessagingStyle.Message` que contém a imagem para enviar, passando a imagem para o novo [Notification.MessagingStyle.Message.SetData](https://developer.android.com/reference/android/app/Notification.MessagingStyle.Message.html#setData%28java.lang.String,%20android.net.Uri) método.
   Por exemplo:

    ```csharp
    Uri imageUri = Uri.Parse("android.resource://com.xamarin.pminidemo/drawable/example_image");
    Notification.MessagingStyle.Message message = new Notification.MessagingStyle
            .Message("Here's a picture of where I'm currently standing", 0, fromPerson)
            .SetData("image/", imageUri);
    ```

3. Adicionar a mensagem para um `Notification.MessagingStyle` objeto. Por exemplo:

    ```csharp
    Notification.MessagingStyle style = new Notification.MessagingStyle(fromPerson)
            .AddMessage(message);
    ```

4. Conecte esse estilo o construtor de notificação. Por exemplo:

    ```csharp
    builder = new Notification.Builder(this, MY_CHANNEL)
        .SetContentTitle("Tour of the Colosseum")
        .SetContentText("I'm standing right here!")
        .SetSmallIcon(Resource.Mipmap.ic_notification)
        .SetStyle(style)
        .SetChannelId(MY_CHANNEL);
    ```

5. A notificação de publicação. Por exemplo:

    ```csharp
    const int notificationId = 1000;
    notificationManager.Notify(notificationId, builder.Build());
    ```

Para obter mais informações sobre a criação de notificações, consulte [notificações locais](~/android/app-fundamentals/notifications/local-notifications.md).


### <a name="indoor-positioning"></a>Posicionamento interno

Pizza Android fornece suporte para IEEE 802.11mc (também conhecido como _Wi-Fi ida-viagem tempo_ ou _WiFi RTT_), que torna possível para os aplicativos detectem a distância para um ou mais acesso de Wi-Fi aponta. Usando essas informações, é possível que seu aplicativo aproveitar *posicionamento interno* com uma precisão de um ou dois medidores. Em dispositivos Android que dão suporte de hardware para IEEE 801.11mc, seu aplicativo pode oferecer recursos de navegação, como controle baseados na localização de aplicativos inteligentes ou instruções de turno detalhados por meio de um repositório:

[![Exemplo de navegação interno usando o RTT WiFi](pie-images/05-wifi-rtt-sml.png)](pie-images/05-wifi-rtt.png#lightbox)

O novo [WifiRttManager](https://developer.android.com/reference/android/net/wifi/rtt/WifiRttManager) classe e várias classes de auxiliar fornece os meios para medir a distância em dispositivos de Wi-Fi. Para obter mais informações sobre as APIs de posicionamento interno introduzidas no Android P, consulte [Android.Net.Wifi.Rtt](https://developer.android.com/reference/android/net/wifi/rtt/package-summary).


### <a name="multi-camera-support"></a>Suporte para a câmera

Muitos dispositivos Android mais recentes têm câmeras dual-front e/ou dual-back que são úteis para recursos, como visão estéreo, efeitos visuais aprimorados e funcionalidade aprimorada de zoom. P Android apresenta um novo [câmera vários](https://developer.android.com/preview/features#camera) API que torna possível para seu aplicativo usar um *câmera lógica* (ou *lógica câmera várias*) que é respaldada por dois ou mais câmeras físicas.
Para determinar se o dispositivo dá suporte a uma câmera multi lógico, você pode examinar os recursos de cada câmera do dispositivo para ver se ele dá suporte a [RequestAvailableCapabilitiesLogicalMultiCamera](https://developer.android.com/reference/android/hardware/camera2/CameraMetadata#REQUEST_AVAILABLE_CAPABILITIES_LOGICAL_MULTI_CAMERA).

Pizza Android também inclui um novo [SessionConfiguration](https://developer.android.com/reference/android/hardware/camera2/params/SessionConfiguration.html) classe que pode ser usado para ajudar a reduzir os atrasos durante a captura inicial e eliminar a necessidade de iniciar e iniciar o fluxo da câmera.

Para obter mais informações sobre a câmera suporte no Android P, consulte [atualizações de suporte e a câmera de câmera vários](https://developer.android.com/preview/features#camera).


### <a name="other-features"></a>Outros recursos

Além disso, pizza Android dá suporte a vários outros novos recursos:

-   O novo [AnimatedImageDrawable](https://developer.android.com/reference/android/graphics/drawable/AnimatedImageDrawable.html) classe, que pode ser usado para desenhar e exibir imagens animadas.

-   Uma nova [ImageDecoder](https://developer.android.com/reference/android/graphics/ImageDecoder.html) classe que substitui `BitmapFactory`. `ImageDecoder` pode ser usado para decodificar um `AnimatedImageDrawable`.

-   Suporte para vídeo HDR (alto alcance de dinâmico) e HEIF (alta eficiência Image File Format) imagens.

-   O [JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html) foi aperfeiçoado para manipular mais inteligente de trabalhos relacionados à rede. O novo [GetNetwork](https://developer.android.com/reference/android/app/job/JobParameters#getNetwork%28%29) método o [JobParameters](https://developer.android.com/reference/android/app/job/JobParameters) classe retorna a melhor rede para a execução de quaisquer solicitações de rede para um determinado trabalho.

Para obter mais informações sobre os recursos mais recentes do Android pizza, consulte [APIs e recursos 9 Android](https://developer.android.com/about/versions/pie/android-9.0).


## <a name="behavior-changes"></a>Alterações de comportamento

Quando a versão do Android de destino é definida como 28 de nível de API, há várias alterações de plataforma que podem afetar o comportamento do seu aplicativo, mesmo se você não estiver implementando os novos recursos descritos acima. A lista a seguir está um breve resumo sobre essas alterações:

-  Aplicativos agora devem solicitar permissão de primeiro plano antes de usar os serviços em primeiro plano.

-  Se seu aplicativo tiver mais de um processo, ele não é possível compartilhar um único [WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) diretório de dados entre processos.

-  Não é permitida diretamente acessando o diretório de dados do aplicativo para outro caminho.

Para obter mais informações sobre as alterações de comportamento para aplicativos direcionados para Android P, consulte [alterações de comportamento](https://developer.android.com/preview/behavior-changes.html#p-apps).


## <a name="sample-code"></a>Código de exemplo

[AndroidPMiniDemo](https://github.com/xamarin/monodroid-samples/tree/master/android-p/AndroidPMiniDemo) é um aplicativo de exemplo do xamarin. Android para Android pizza que demonstra como definir modos de exibição de recorte, como usar o novo `Person` classe e como enviar uma notificação que inclui uma imagem.


## <a name="summary"></a>Resumo

Este artigo introduzido pizza Android e explicou como instalar e configurar as ferramentas e pacotes para o desenvolvimento do xamarin. Android mais recentes com pizza Android. Ele forneceu uma visão geral dos principais recursos disponíveis no Android pizza, com código-fonte de exemplo para vários desses recursos.
Ele incluía links para documentação de API e tópicos do Android Developer para ajudar você a começar a criação de aplicativos para Android pizza. Ele também realçado as alterações de comportamento de pizza Android mais importantes que podem afetar aplicativos existentes.


## <a name="related-links"></a>Links relacionados

- [Android pizza 9](https://developer.android.com/about/versions/pie/)
