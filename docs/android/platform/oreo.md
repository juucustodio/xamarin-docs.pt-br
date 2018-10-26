---
title: Recursos do Oreo
description: Como começar a usar xamarin. Android para desenvolver aplicativos para a versão mais recente do Android.
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: conceptdev
ms.author: crdun
ms.date: 07/06/2018
ms.openlocfilehash: e4f0138c0177c046c1e967a3d3625604f73327d6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103186"
---
# <a name="oreo-features"></a>Recursos do Oreo

_Como começar a usar xamarin. Android para desenvolver aplicativos para a versão mais recente do Android._

[O Android 8.0 Oreo](https://developer.android.com/index.html) é a versão mais recente do Android disponível no Google. O Android Oreo oferece muitos recursos novos de interesse para desenvolvedores do xamarin. Android. Esses recursos incluem os canais de notificação, notificações de notificação, fontes personalizadas em XML, que pode ser baixadas fontes, preenchimento automático e picture in picture (PIP). O Android Oreo inclui novas APIs para esses novos avançados, e essas APIs estão disponíveis para aplicativos xamarin. Android quando você usa o xamarin. Android 8.0 e posterior.

[![Imagem do herói do Android Oreo](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

Este artigo é estruturado para ajudá-lo a começar a desenvolver aplicativos xamarin. Android para o Android 8.0 Oreo. Ele explica como instalar as atualizações necessárias, configure o SDK e criar um (dispositivo ou emulador) para teste. Ele também fornece uma descrição dos novos recursos do Android 8.0 Oreo, com links para aplicativos de exemplo que ilustram como usar os recursos do Android Oreo em aplicativos xamarin. Android.


## <a name="requirements"></a>Requisitos

A seguir é necessário para usar os recursos do Android Oreo em aplicativos baseados em Xamarin:

-   **Visual Studio** &ndash; se você estiver usando o Windows, versão 15.5 ou posterior do Visual Studio é necessária.  Se você estiver usando um Mac, o Visual Studio para Mac versão 7.2.0 é necessário.

-   **Xamarin. Android** &ndash; xamarin. Android 8.0 ou posterior deve ser instalado e configurado com o Visual Studio.

-   **SDK do Android** &ndash; SDK do Android 8.0 (API 26) ou posterior deve ser instalado por meio do Gerenciador de SDK do Android.



## <a name="getting-started"></a>Guia de Introdução

Para começar a usar o Android Oreo com xamarin. Android, você deve baixar e instalar os pacotes do SDK e ferramentas mais recentes antes de criar um projeto do Android Oreo:

1. Atualize para a versão mais recente do Visual Studio.

2. Instalar o **8.0.0 Android (API 26)** ou posterior pacotes e ferramentas por meio do Gerenciador de SDK.

3. Crie um novo projeto do xamarin. Android que tem como alvo o Android Oreo (API 26).

4. Configure um emulador ou dispositivo para testar aplicativos Android Oreo.

Cada uma dessas etapas é explicada nas seções a seguir:



### <a name="update-visual-studio-and-xamarinandroid"></a>Xamarin. Android e atualizar o Visual Studio

Para adicionar suporte do Android Oreo para o Visual Studio, faça o seguinte:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

-   Se você estiver usando o Visual Studio 2017: 

    1. Atualização para o Visual Studio 2017 versão 15.7 ou posterior (consulte [atualizar o Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/update-visual-studio)).

    2. Use o [Gerenciador de SDK](~/android/get-started/installation/android-sdk.md) para instalar o nível de API 26.0 ou posterior.

-   Se você estiver usando o Visual Studio 2015, nós recomendamos fazer o downgrade de SDK Tools para 25 e usando a GUI do Gerenciador de emulador do Google antigo. Ferramentas do SDK 25 ainda podem ser usadas junto com a API 26, 27 e mais recente e não afetarão o desenvolvimento para novas plataformas. Isso lhe dará uma interface para o gerenciamento do SDK do Android para versões mais antigas do VS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

-   Atualizar para a versão estável mais recente do Visual Studio 2017 para Mac, conforme explicado em [atualização do Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/update).

-----

Para obter mais informações sobre o suporte de Xamarin para Android Oreo, consulte o [notas de versão do xamarin. Android 8.0](https://developer.xamarin.com/releases/android/xamarin.android_8/xamarin.android_8.0/).



### <a name="install-the-android-sdk"></a>Instalar o SDK do Android

Para criar um projeto com o xamarin. Android 8.0, primeiro você deve usar o Gerenciador de SDK do Xamarin Android para instalar a plataforma SDK para **Android 8.0 - Oreo** ou posterior. Você também deve instalar as Android SDK Tools 26.0 ou posterior.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Inicie o Gerenciador de SDK (no Visual Studio, clique em **Ferramentas > Android > Gerenciador de SDK do Android**).

2. Instalar o **Android 8.0 - Oreo** pacotes. Se você estiver usando o emulador do SDK do Android, certifique-se de incluir a **x86** imagens do sistema que você precisará de:

    [![Selecionando pacotes do Android 8.0 no Gerenciador de SDK do Android](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. Instale **Android SDK Tools 26.0.2** ou posterior **Android SDK Platform-Tools 26.0.0** ou posterior, e **Android SDK Build-Tools 26.0.0** (ou posterior):

    [![Selecionando o Android SDK Tools 26 no Gerenciador de SDK do Android](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Inicie o Gerenciador de SDK (no Visual Studio para Mac, clique em **Ferramentas > Gerenciador de SDK**).

2. Instalar o **Android 8.0 - Oreo** pacotes do SDK. Se você estiver usando o emulador do SDK do Android, certifique-se de incluir a **x86** imagens do sistema que você precisará de:

    [![Selecionando pacotes do Android 8.0 no Gerenciador de SDK](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. Instale **Android SDK Tools 26.0.2** ou posterior **Android SDK Platform-Tools 26.0.0** ou posterior, e **Android SDK Build-Tools 26.0.0** (ou posterior):

    [![Selecionando o Android SDK Tools 26 no Gerenciador de SDK](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----



### <a name="start-a-xamarinandroid-project"></a>Iniciar um projeto xamarin. Android

Crie um novo projeto do xamarin. Android. Se você for novo no desenvolvimento de Android com Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para saber mais sobre como criar projetos do xamarin. Android.

Quando você cria um projeto do Android, você deve configurar as configurações de versão destino Android 8.0 ou posterior. Por exemplo, para direcionar seu projeto para o Android 8.0, você deve configurar o nível de API do Android de destino do seu projeto para **Android 8.0 (API 26)**. É recomendável que você também pode definir seu nível de estrutura de destino para a API 26 ou posterior. Para obter mais informações sobre como configurar níveis de nível de API do Android, consulte [Noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).


### <a name="configure-an-emulator-or-device"></a>Configurar um dispositivo ou emulador

Se você tentar iniciar o Gerenciador de AVD baseado em GUI do Google padrão depois de instalar o Android SDK Tools 26.0 de ou posterior, você pode receber o diálogo de erro a seguir, que pede que você use a ferramenta de Gerenciador de AVD de linha de comando **avdmanager** em vez disso :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Caixa de diálogo de aviso de Gerenciador de emulador do Android](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Caixa de diálogo de aviso de Gerenciador de emulador do Android](oreo-images/mac/03-avd-warning.png)

-----

Esta mensagem é exibida porque o Google não fornece mais um Gerenciador de AVD de GUI que dá suporte à API 26.0 e posterior independentes. Para o Android 8.0 Oreo, você deve usar o Gerenciador de emulador de Android do Xamarin ou da linha de comando `avdmanager` ferramenta para criar dispositivos virtuais para Android Oreo.

Para usar o Gerenciador de dispositivos do Android para criar e gerenciar dispositivos virtuais, consulte [gerenciamento de dispositivos virtuais com o Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).
Para criar dispositivos virtuais sem o Android Device Manager, siga as etapas na próxima seção.


#### <a name="creating-virtual-devices-using-avdmanager"></a>Criar dispositivos virtuais usando avdmanager

Para usar **avdmanager** para criar um novo dispositivo virtual, siga estas etapas:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Abra uma janela de Prompt de comando e defina `JAVA_HOME` até o local do SDK do Java em seu computador. Para uma instalação típica do Xamarin, você pode usar o comando a seguir:

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2.  Adicione o local do SDK do Android `bin` pasta para sua `PATH`.
    Para uma instalação típica do Xamarin, você pode usar o comando a seguir:

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3.  Feche a janela de Prompt de comando e abrir uma nova janela de Prompt de comando. Criar um novo dispositivo virtual usando o [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) comando. Por exemplo, para criar um AVD chamado **AVD-Oreo-8.0** usando x86 a imagem do sistema para o nível de API 26, use o seguinte comando:

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4.  Quando for solicitado com **você deseja criar um perfil de hardware personalizado [não]** você pode inserir **nenhuma** e aceitar o perfil de hardware padrão. Se você disser **yes**, **avdmanager** solicitará que você com uma lista de perguntas para personalizar o perfil de hardware.

Depois que você **avdmanager** para criar o seu dispositivo virtual, ele será incluído no menu suspenso do dispositivo:

[![Novo AVD adicionado ao menu suspenso do dispositivo](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1.  Abra uma **Terminal** janela e mudar para o local do diretório de ferramentas do SDK do Android no Mac. Para uma instalação típica do Xamarin, você pode usar o comando a seguir:

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2.  Criar um novo dispositivo virtual usando o [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) comando. Por exemplo, para criar um AVD chamado **AVD-Oreo-8.0** usando x86 a imagem do sistema para o nível de API 26, use o seguinte comando:

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3.  Quando for solicitado com **você deseja criar um perfil de hardware personalizado [não]** você pode inserir **nenhuma** e aceitar o perfil de hardware padrão. Se você disser **yes**, **avdmanager** solicitará que você com uma lista de perguntas para personalizar o perfil de hardware.

Depois de usar **avdmanager** para criar o seu dispositivo virtual, ele será incluído no menu suspenso do dispositivo:

[![Novo AVD adicionado ao menu suspenso do dispositivo](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

Para obter mais informações sobre como configurar um emulador do Android para teste e depuração, consulte [depuração no emulador do Android](~/android/deploy-test/debugging/debug-on-emulator.md).

Se você estiver usando um dispositivo físico, como um Nexus ou um Pixel, você pode atualizar seu dispositivo por meio de automático sobre as atualizações de ar (OTA) ou baixar uma imagem do sistema e seu dispositivo flash diretamente. Para obter mais informações sobre como atualizar manualmente seu dispositivo para Android Oreo, consulte [Factory imagens para dispositivos de Pixel e Nexus](https://developers.google.com/android/images).



## <a name="new-features"></a>Novos recursos

O Android Oreo apresenta uma variedade de novos recursos e funcionalidades, como canais de notificação, selos de notificação, fontes personalizadas em XML, que pode ser baixadas fontes, preenchimento automático e picture in picture. As seções a seguir destacam esses recursos e fornecem links para ajudá-lo a começar a usá-las em seu aplicativo.



### <a name="notification-channels"></a>Canais de notificação

*Canais de notificação* são categorias definidas pelo aplicativo para notificações.
Você pode criar um canal de notificação para cada tipo de notificação de que você precisa enviar, e você pode criar canais de notificação para refletir as opções feitas por usuários do seu aplicativo. O novo recurso de canais de notificação torna possível para você fornecer controle refinado que os usuários de diferentes tipos de notificações. Por exemplo, se você estiver implementando um aplicativo de mensagens, você pode criar canais de notificação separada para cada grupo de conversa é criado por um usuário.

[Canais de notificação](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan) explica como criar um canal de notificação e usá-lo para lançamento de notificações locais. Para obter um exemplo de código do mundo real, consulte o [NotificationChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels) exemplo; Este aplicativo de exemplo gerencia dois canais e define as opções de notificação adicionais.



### <a name="notification-badges"></a>Selos de notificação

Selos de notificação são pequenos pontos que aparecem ao longo de ícones do aplicativo, conforme mostrado nesta captura de tela:

[![Selos de notificação de exemplo nos ícones de aplicativo](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

Esses pontos indicam que há novas notificações para um ou mais canais de notificação no aplicativo associado com esse ícone do aplicativo &ndash; esses são notificações que o usuário ainda não foi descartada ou tratados. Os usuários podem longa-pressione em um ícone olhei as notificações associadas a uma notificação de notificação, ignorando ou atuando em notificações no menu pressione longa que appeaars.

Para obter mais informações sobre notificações de notificação, consulte o desenvolvedor do Android [selos de notificação](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges) tópico.



### <a name="custom-fonts-in-xml"></a>Fontes personalizadas em XML

Apresenta o Android Oreo *fontes no XML*, que possibilita a incorporar fontes personalizadas como recursos. OpenType (**. OTF**) e TrueType (**. ttf**) há suporte para formatos de fonte. Para adicionar fontes como recursos, faça o seguinte:

1. Criar uma **recursos/fonte** pasta.

2. Copie os arquivos de fonte (exemplo, **. ttf** e **. OTF** arquivos) para **recursos/fonte**. 

3. Se necessário, renomeie cada arquivo de fonte para que ela adere às convenções de nomenclatura de arquivo Android (ou seja, use somente minúscula *-z*, *0-9*e sublinhados em nomes de arquivo). Por exemplo, o arquivo de fonte `Pacifico-Regular.ttf` pode ser renomeado para algo como `pacifico.ttf`.

4. Aplicar a fonte personalizada usando o novo `android:fontFamily` atributo em seu layout de XML. Por exemplo, a seguinte `TextView` declaração usa adicionado **pacifico.ttf** recurso de fonte:

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

Você também pode criar um arquivo XML fonte família que descreve várias fontes, bem como detalhes de estilo e peso. Para obter mais informações, consulte o desenvolvedor do Android [fontes no XML](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html) tópico.


### <a name="downloadable-fonts"></a>Fontes que pode ser baixadas

Começando com o Android Oreo, aplicativos podem solicitar fontes de um provedor em vez de agrupá-las para o APK. As fontes são baixadas da rede, conforme necessário. Esse recurso reduz o tamanho do APK, economizando o uso de dados de celular e de memória do telefone. Você também pode usar esse recurso em versões de API do Android 14 e superior, instalando o pacote Android 26 da biblioteca de suporte.

Quando seu aplicativo precisa de uma fonte, você cria um `FontsRequest` (especificando a fonte para baixar) do objeto e, em seguida, passá-lo para um `FontsContract` método para baixar a fonte. As etapas a seguir descrevem o processo de download de fonte mais detalhadamente:

1.  Criar uma instância de um [FontRequest](https://developer.android.com/reference/android/provider/FontRequest.html) objeto. 

2.  Subclasse e instanciar [FontsContract.FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html).

3.  Implemente a [FontRequestCallback.OnTypeFaceRetrieved](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29) método, que é usado para manipular a conclusão da solicitação da fonte.

4.  Implemente a [FontRequestCallback.OnTypeFaceRequestFailed](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29) método, que é usado para informar o seu aplicativo de quaisquer erros que ocorrem durante o processo de solicitação de fonte.

5.  Chame o [FontsContract.RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback)) método para recuperar a fonte do provedor de fonte. 

Quando você chama o `RequestFonts` método, ele primeiro verifica para ver se a fonte é armazenada em cache localmente (de uma chamada anterior a `RequestFont`). Se não estiver em cache, ele chama o provedor de fonte, recupera a fonte de forma assíncrona e, em seguida, passa os resultados novamente até seu aplicativo invocando o `OnTypeFaceRetrieved` método.

O [baixável fontes](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts) exemplo demonstra como usar o recurso de fontes que pode ser baixado, introduzido no Android Oreo. 

Para obter mais informações sobre como baixar fontes, consulte o desenvolvedor do Android [baixável fontes](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html) tópico.



### <a name="autofill"></a>Preenchimento automático

O novo _Autopreenchimento_ framework no Android Oreo torna mais fácil para os usuários lidar com tarefas repetitivas, como logon, a criação da conta e transações de cartão de crédito. Os usuários gastam menos tempo que digitar novamente as informações (que podem levar a erros de entrada). Antes de seu aplicativo pode trabalhar com a estrutura de preenchimento automático, um serviço de preenchimento automático deve ser habilitado nas configurações do sistema (os usuários podem habilitar ou desabilitar o preenchimento automático).

O [AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework/) exemplo demonstra o uso da estrutura de preenchimento automático. Ele inclui implementações do cliente de atividades com modos de exibição que deve ser autofilled e um serviço que pode fornecer dados de preenchimento automático para atividades do cliente.

Para obter mais informações sobre o novo recurso de preenchimento automático e como otimizar seu aplicativo para preenchimento automático, consulte o desenvolvedor do Android [estrutura de preenchimento automático](https://developer.android.com/guide/topics/text/autofill.html) tópico.



### <a name="picture-in-picture-pip"></a>Picture in Picture (PIP)

O Android Oreo torna possível para uma atividade Iniciar em modo de (PIP) picture in picture, sobreposição de tela de outra atividade. Esse recurso destina-se a reprodução de vídeo.

Para especificar que a atividade do seu aplicativo pode usar modo PIP, defina o seguinte sinalizador como true no manifesto do Android:

```xml
android:supportsPictureInPicture
```

Para especificar como sua atividade deve se comportar quando ele estiver no modo PIP, use a nova [PictureInPictureParams](https://developer.android.com/reference/android/app/PictureInPictureParams.html) objeto. `PictureInPictureParams` representa um conjunto de parâmetros que podem ser usados para inicializar e atualizar uma atividade no modo PIP (por exemplo, da atividade preferencial proporção). Os novos métodos PIP a seguir foram adicionados ao `Activity` no Android Oreo:

-   [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29) &ndash; coloca a atividade no modo PIP. A atividade é colocada no canto da tela, e o restante da tela é preenchido com a atividade anterior que estava na tela.

-   [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29) &ndash; de atualizações de definições de configuração de PIP da atividade (por exemplo, uma alteração na taxa de proporção).

O [PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture) exemplo demonstra o uso básico do modo Picture in Picture (PiP) para dispositivos portáteis, introduzido no Oreo. O exemplo reproduz um vídeo que continua sem interrupções ao alternar entre modos de exibição ou outras atividades.



### <a name="other-features"></a>Outros recursos

O Android Oreo contém muitos outros novos recursos, como os limites do Emoji suporte biblioteca, a API de localização, plano de fundo, cor de toda a gama de aplicativos, novos codecs de áudio, aprimoramentos de exibição da Web, suporte à navegação de teclado aprimorada e uma nova API AAudio (áudio pro) para áudio de baixa latência de alto desempenho, para obter mais informações sobre esses recursos, consulte o desenvolvedor do Android [APIs e recursos do Android Oreo](https://developer.android.com/about/versions/oreo/android-8.0.html) tópico.



## <a name="behavior-changes"></a>Alterações de comportamento

O Android Oreo inclui uma variedade de sistema e as alterações de comportamento de API que podem ter um impacto sobre a funcionalidade dos aplicativos existentes. Essas alterações são descritas da seguinte maneira.


### <a name="background-execution-limits"></a>Limites de execução do plano de fundo

Para melhorar a experiência do usuário, o Android Oreo impõe limitações a quais aplicativos podem fazer durante a execução em segundo plano. Por exemplo, se o usuário está assistindo a um vídeo ou um jogo, um aplicativo em execução em segundo plano pode prejudicar o desempenho de um aplicativo de vídeo intensiva em execução em primeiro plano. Como resultado, o Android Oreo coloca as seguintes restrições em aplicativos que não estão interagindo diretamente com o usuário:

1.  **Limitações do serviço de plano de fundo** &ndash; quando um aplicativo é executado em segundo plano, ele tem uma janela de vários minutos em que ele ainda é permitido para criar e usar os serviços. No final dessa janela, Android interrompe o serviço de plano de fundo do aplicativo e os tratará como sendo _ocioso_.

2.  **Limitações de difusão** &ndash; Android 7.0 (API 25) colocado limitações em que um aplicativo registra para receber transmissões. O Android Oreo torna essas limitações mais estritos. Por exemplo, aplicativos do Android Oreo não podem registrar receptores de difusão para transmissões implícitas em seus manifestos.

Para obter mais informações sobre os novos limites de execução em segundo plano, consulte o desenvolvedor do Android [limites de execução do plano de fundo](https://developer.android.com/about/versions/oreo/background.html) tópico.


### <a name="breaking-changes"></a>Alterações significativas

Aplicativos destinados a Android Oreo ou superior, deverá modificar seus aplicativos para dar suporte as alterações a seguir, quando aplicável:

- O Android Oreo pretere a capacidade de definir a prioridade de notificações individuais. Em vez disso, você definir um nível de importância recomendada durante a criação de um canal de notificação. O nível de importância que você atribuir a um canal de notificação se aplica a todas as mensagens de notificação que você postar a ele.

- Para aplicativos direcionados para Android Oreo, `PendingIntent.GetService()` não funciona devido a novos limites colocados em serviços iniciados em segundo plano. Se você tiver como alvo o Android Oreo, você deve usar [PendingIntent.GetBroadcast](https://developer.xamarin.com/api/member/Android.App.PendingIntent.GetBroadcast/p/Android.Content.Context/System.Int32/Android.Content.Intent/Android.App.PendingIntentFlags/) em vez disso.  


## <a name="sample-code"></a>Código de exemplo

Vários exemplos de xamarin. Android estão disponíveis para mostrar como tirar proveito dos recursos do Android Oreo:

-   [NotificationsChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels) demonstra como usar o novo sistema de canais de notificação introduzido no Android Oreo. Este exemplo gerencia dois canais de notificações: uma com importância padrão e o outro com alta importância.

-   [PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture) demonstra o uso básico do modo Picture in Picture (PiP) para dispositivos portáteis, introduzido no Oreo. O exemplo reproduz um vídeo que continua sem interrupções ao alternar entre modos de exibição ou outras atividades.

-   [AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework) demonstra o uso da estrutura de preenchimento automático. Ele inclui implementações do cliente de atividades com modos de exibição que deve ser autofilled e um serviço que pode fornecer dados de preenchimento automático para atividades do cliente.

-   [Fontes que pode ser baixadas](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts) fornece um exemplo de como usar o recurso de fontes para download descrito anteriormente.

-   [EmojiCompat](https://developer.xamarin.com/samples/monodroid/android-o/EmojiCompat) demonstra o uso da biblioteca de suporte do EmojiCompat. Você pode usar esta biblioteca para impedir que seu aplicativo do mostrando ausente emoji caracteres como caracteres de "Bananas".

-   [Local atualizações pendentes intenção](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdPendIntent) ilustra o uso da API de localização para obter atualizações sobre o local de um dispositivo usando um `PendingIntent`.

-   [Serviço de primeiro plano de atualizações local](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdFgService) demonstra como usar a API de localização para obter atualizações sobre o local de um dispositivo usando um serviço de primeiro plano associado e ser iniciado.


## <a name="video"></a>Vídeo

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**Desenvolvimento do Oreo 8.0 Android com c#**


## <a name="summary"></a>Resumo

Este artigo introduziu o Android Oreo e explicou como instalar e configurar as ferramentas mais recentes e os pacotes para o desenvolvimento do xamarin. Android no Android Oreo. Ele forneceu uma visão geral dos principais recursos disponíveis no Android Oreo, com links para código-fonte de exemplo para vários recursos novos. Ele incluía links para documentação de API e tópicos do Android Developer para ajudar você a começar a criação de aplicativos para Android Oreo. Ele também realçado as alterações de comportamento Android Oreo mais importantes que podem afetar aplicativos existentes.


## <a name="related-links"></a>Links relacionados

- [O Android Oreo 8.0](https://developer.android.com/index.html)
