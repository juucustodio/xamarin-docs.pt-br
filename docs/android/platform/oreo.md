---
title: Recursos de Oreos
description: Como começar a usar o xamarin para desenvolver aplicativos para a versão mais recente do Android.
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: mgmclemore
ms.author: mamcle
ms.date: 06/22/2018
ms.openlocfilehash: a23072427a74119bfa339fea8a695cd13b775685
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935097"
---
# <a name="oreo-features"></a>Recursos de Oreos

_Como começar a usar o xamarin para desenvolver aplicativos para a versão mais recente do Android._

[Android Oreos 8.0](https://developer.android.com/index.html) há a versão mais recente do Android da Google. Android Oreos oferece muitos recursos novos de interesse para os desenvolvedores do xamarin. Esses recursos incluem canais de notificação, notificações de notificação, fontes personalizadas em XML, as fontes para download, preenchimento automático e imagem na imagem (PIP). Android Oreos incluem novas APIs para esses novos capabilties, e essas APIs estão disponíveis para aplicativos xamarin quando você usar xamarin 8.0 e posterior.

[![Imagem de herói Oreos Android](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

Este artigo é estruturado para ajudá-lo a começar a desenvolver aplicativos xamarin para Oreos do Android 8.0. Ele explica como instalar as atualizações necessárias, configure o SDK e crie um emulador (ou dispositivo) para teste. Ele também fornece uma descrição dos novos recursos no Oreos 8.0 Android, com links para aplicativos de exemplo que ilustram como usar os recursos de Oreos Android em aplicativos xamarin.


## <a name="requirements"></a>Requisitos

O seguinte é necessário para usar os recursos de Oreos Android em aplicativos baseados em Xamarin:

-   **O Visual Studio** &ndash; se você estiver usando o Windows, versão 15.5 ou posterior do Visual Studio é necessário.  Se você estiver usando um Mac, o Visual Studio para Mac versão 7.2.0 é necessário.

-   **Xamarin** &ndash; xamarin 8.0 ou posterior deve ser instalado e configurado com o Visual Studio.

-   **SDK do Android** &ndash; Android SDK 8.0 (26 de API) ou posterior deve ser instalado por meio do Gerenciador de SDK do Android.



## <a name="getting-started"></a>Guia de Introdução

Para começar a usar o Android Oreos com xamarin, você deve baixar e instalar as ferramentas mais recentes e os pacotes SDK antes de criar um projeto Android Oreos:

1. Atualize para a versão mais recente do Visual Studio.

2. Instalar o **8.0.0 Android (API 26)** ou posterior pacotes e por meio do Gerenciador de SDK de ferramentas.

3. Crie um novo projeto de xamarin que tem como destino Oreos Android (API 26).

4. Configure um dispositivo para testar aplicativos do Android Oreos ou o emulador.

Cada uma dessas etapas é explicada nas seções a seguir:



### <a name="update-visual-studio-and-xamarinandroid"></a>Atualizar o xamarin e o Visual Studio

Para adicionar suporte de Oreos Android para o Visual Studio, faça o seguinte:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-   Se você estiver usando o Visual Studio de 2017: 

    1. Atualização para o Visual Studio 2017 versão 15.5 ou posterior (consulte [atualização Visual Studio 2017](https://docs.microsoft.com/en-us/visualstudio/install/update-visual-studio)).

    2. Use o Gerenciador de SDK [instruções de instalação](~/android/get-started/installation/android-sdk.md#installation) para instalar o Gerenciador de SDK do Xamarin.
       O Gerenciador de SDK do Xamarin deve ser instalado porque o Google não oferece um standlone manager GUI SDK que dá suporte à API 26,0 e posterior.

-   Se você estiver usando o Visual Studio 2015, nós recomendamos fazer o downgrade de ferramentas do SDK para 25 e usando o Gerenciador de emulador do Google antigo interface gráfica do usuário. Ferramentas do SDK 25 ainda podem ser usadas junto com a API 26, 27 e mais recente e não afetam o desenvolvimento para novas plataformas. Isso lhe dará uma interface para gerenciar o SDK do Android para versões mais antigas do VS.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

-   Atualizar para a versão estável mais recente do Visual Studio de 2017 para Mac, conforme explicado em [atualização Visual Studio para Mac](https://docs.microsoft.com/en-us/visualstudio/mac/update).

-----

Para obter mais informações sobre o suporte de Xamarin para Oreos Android, consulte o [notas de versão do xamarin 8.0](https://developer.xamarin.com/releases/android/xamarin.android_8/xamarin.android_8.0/).



### <a name="install-the-android-sdk"></a>Instalar o SDK do Android

Para criar um projeto com o xamarin 8.0, primeiro você deve usar o Gerenciador de SDK do Android Xamarin para instalar o platform SDK para **8.0 Android - Oreos** ou posterior. Você também deve instalar ferramentas do SDK do Android 26,0 ou posteriores.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Inicie o Gerenciador de SDK (no Visual Studio, clique em **Ferramentas > Android > Gerenciador de SDK do Android**).

2. Instalar o **8.0 Android - Oreos** pacotes. Se você estiver usando o emulador do SDK do Android, certifique-se de incluir o **x86** imagens do sistema que você precisará de:

    [![Selecionar pacotes 8.0 Android no Gerenciador de SDK do Android](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. Instalar **ferramentas do SDK do Android 26.0.2** ou posterior, **ferramentas de plataforma Android para o SDK 26.0.0** ou posterior, e **Android SDK-ferramentas de compilação 26.0.0** (ou posterior):

    [![Selecionando Ferramentas do SDK do Android 26 no Gerenciador de SDK do Android](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Inicie o Gerenciador de SDK (no Visual Studio para Mac, clique em **Ferramentas > Gerenciador de SDK**).

2. Instalar o **8.0 Android - Oreos** pacotes do SDK. Se você estiver usando o emulador do SDK do Android, certifique-se de incluir o **x86** imagens do sistema que você precisará de:

    [![Selecionar pacotes 8.0 Android no Gerenciador de SDK](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. Instalar **ferramentas do SDK do Android 26.0.2** ou posterior, **ferramentas de plataforma Android para o SDK 26.0.0** ou posterior, e **Android SDK-ferramentas de compilação 26.0.0** (ou posterior):

    [![Selecionando Ferramentas do SDK do Android 26 no Gerenciador de SDK](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----



### <a name="start-a-xamarinandroid-project"></a>Iniciar um projeto do xamarin

Crie um novo projeto de xamarin. Se você for novo no desenvolvimento do Android com o Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para saber mais sobre a criação de projetos do xamarin.

Quando você cria um projeto Android, você deve configurar as configurações de versão para o destino Android 8.0 ou posterior. Por exemplo, para direcionar seu projeto para 8.0 Android, você deve configurar o nível de API do Android do destino do seu projeto para **8.0 Android (API 26)**. É recomendável que você também pode definir o nível de estrutura de destino 26 de API ou posterior. Para obter mais informações sobre como configurar níveis de nível de API do Android, consulte [Noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).


### <a name="configure-an-emulator-or-device"></a>Configurar um dispositivo ou emulador

Se você tentar iniciar o Gerenciador de AVD baseado em GUI do Google padrão depois de instalar o Android 26.0 de ferramentas do SDK ou posterior, você pode receber o diálogo de erro a seguir, que instrui a usar a ferramenta de Gerenciador de linha de comando AVD **avdmanager** em vez disso :

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Caixa de diálogo de aviso de Gerenciador de emulador do Android](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

![Caixa de diálogo de aviso de Gerenciador de emulador do Android](oreo-images/mac/03-avd-warning.png)

-----

Esta mensagem é exibida porque o Google não fornece mais um standlone manager GUI AVD que dá suporte à API 26,0 e posterior. Para Oreos 8.0 Android, você deve usar o Gerenciador de emulador do Android Xamarin ou a linha de comando `avdmanager` ferramenta para criar dispositivos virtuais para Oreos Android.

Para usar o Gerenciador de dispositivos Android para criar e gerenciar dispositivos virtuais, consulte [gerenciando dispositivos virtuais com o Gerenciador de dispositivo do Android](~/android/get-started/installation/android-emulator/device-manager.md).
Para criar dispositivos virtuais sem o Gerenciador de dispositivos Android, siga as etapas na próxima seção.


#### <a name="creating-virtual-devices-using-avdmanager"></a>Criando dispositivos virtuais usando avdmanager

Para usar **avdmanager** para criar um novo dispositivo virtual, siga estas etapas:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  Abra uma janela de Prompt de comando e defina `JAVA_HOME` para o local do Java SDK em seu computador. Para uma instalação típica do Xamarin, você pode usar o seguinte comando:

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2.  Adicionar o local do SDK do Android `bin` pasta ao seu `PATH`.
    Para uma instalação típica do Xamarin, você pode usar o seguinte comando:

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3.  Feche a janela de Prompt de comando e abrir uma nova janela de Prompt de comando. Criar um novo dispositivo virtual usando o [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) comando. Por exemplo, para criar um AVD chamado **AVD-Oreos-8.0** usando x86 imagem do sistema para o nível de API 26, use o seguinte comando:

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4.  Quando for solicitado com **você deseja criar um perfil de hardware personalizado [não]** você pode inserir **sem** e aceitar o perfil de hardware padrão. Se você diz **Sim**, **avdmanager** solicitará uma lista de perguntas para personalizar o perfil de hardware.

Depois que você **avdmanager** para criar seu dispositivo virtual, ele será incluído no menu suspenso de dispositivo:

[![AVD novo é adicionado ao menu suspenso de dispositivo](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1.  Abra um **Terminal** janela e altere para o local do diretório de ferramentas do SDK do Android no seu Mac. Para uma instalação típica do Xamarin, você pode usar o seguinte comando:

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2.  Criar um novo dispositivo virtual usando o [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) comando. Por exemplo, para criar um AVD chamado **AVD-Oreos-8.0** usando x86 imagem do sistema para o nível de API 26, use o seguinte comando:

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3.  Quando for solicitado com **você deseja criar um perfil de hardware personalizado [não]** você pode inserir **sem** e aceitar o perfil de hardware padrão. Se você diz **Sim**, **avdmanager** solicitará uma lista de perguntas para personalizar o perfil de hardware.

Depois que você usar **avdmanager** para criar seu dispositivo virtual, ele será incluído no menu suspenso de dispositivo:

[![AVD novo é adicionado ao menu suspenso de dispositivo](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

Para obter mais informações sobre como configurar um emulador Android para teste e depuração, consulte [depuração no emulador do Android](~/android/deploy-test/debugging/debug-on-emulator.md).

Se você estiver usando um dispositivo físico, como uma ligação ou um Pixel, você pode atualizar seu dispositivo por meio de automático sobre as atualizações de ondas de rádio ou baixar uma imagem do sistema e flash seu dispositivo diretamente. Para obter mais informações sobre como atualizar manualmente seu dispositivo para Oreos Android, consulte [imagens de fábrica do Nexus e dispositivos de Pixel](https://developers.google.com/android/images).



## <a name="new-features"></a>Novos recursos

Oreos Android apresenta uma variedade de novos recursos e funcionalidades, como canais de notificação, notificações de notificação, fontes personalizadas em XML, fontes para download, preenchimento automático e imagem na imagem. As seções a seguir destacam esses recursos e fornecem links para ajudá-lo a começar a usá-las em seu aplicativo.



### <a name="notification-channels"></a>Canais de notificação

*Canais de notificação* são categorias definidas pelo aplicativo para notificações.
Você pode criar um canal de notificação para cada tipo de notificação que você precisa enviar, e você pode criar canais de notificação para refletir as opções feitas por usuários do seu aplicativo. O novo recurso de canais de notificação possibilita que você fornecer controle refinado de usuários diferentes tipos de notificações. Por exemplo, se você estiver implementando um aplicativo de mensagens, você pode criar canais de notificação separada para cada grupo de conversa é criado por um usuário.

[Canais de notificação](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan) explica como criar um canal de notificação e usá-lo para notificações de locais de lançamento. Para obter um exemplo de código do mundo real, consulte o [NotificationChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels) exemplo; esta amostra de aplicativo gerencia dois canais e define as opções de notificação adicional.



### <a name="notification-badges"></a>Notificações de notificação

Notificações de notificação são pequenos pontos aparecem em ícones de aplicativo, conforme mostrado nesta captura de tela:

[![Notificações de notificação de exemplo nos ícones de aplicativo](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

Esses pontos indicam se há novas notificações para um ou mais canais de notificação no aplicativo associado a esse ícone do aplicativo &ndash; são notificações que o usuário ainda não foi descartada ou tratados. Os usuários podem longa-pressione em um ícone para Observe as notificações associadas a uma notificação de notificação, ignorando ou atuando em notificações no menu pressione longa que appeaars.

Para obter mais informações sobre notificações de notificação, consulte o desenvolvedor Android [selos de notificação](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges) tópico.



### <a name="custom-fonts-in-xml"></a>Fontes personalizadas em XML

Apresenta o Android Oreos *fontes no XML*, que torna possível para incorporar fontes personalizadas como recursos. OpenType (**. OTF**) e TrueType (**. ttf**) têm suporte a formatos de fonte. Para adicionar fontes como recursos, faça o seguinte:

1. Criar um **recursos/fonte** pasta.

2. Copie os arquivos de fonte (exemplo, **. ttf** e **. OTF** arquivos) para **recursos/fonte**. 

3. Se necessário, renomeie cada arquivo de fonte para que ele segue o convenções de nomenclatura de arquivo Android (ou seja, use somente minúsculas *-z*, *0-9*e sublinhados em nomes de arquivo). Por exemplo, o arquivo de fonte `Pacifico-Regular.ttf` pode ser renomeado para algo como `pacifico.ttf`.

4. Aplicar a fonte personalizada usando o novo `android:fontFamily` atributo no layout do XML. Por exemplo, a seguinte `TextView` declaração usa adicionado **pacifico.ttf** recurso de fonte:

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

Você também pode criar um arquivo XML fonte família que descreve várias fontes, bem como os detalhes de estilo e peso. Para obter mais informações, consulte o desenvolvedor Android [fontes no XML](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html) tópico.


### <a name="downloadable-fonts"></a>Fontes para download

A partir do Android Oreos, aplicativos podem solicitar fontes de um provedor, em vez de empacotamento-los para o APK. As fontes são baixadas da rede, conforme necessário. Esse recurso reduz o tamanho APK, para conservar o uso de dados de celular e de memória do telefone. Você também pode usar esse recurso em versões de API do Android 14 e posterior ao instalar o pacote Android 26 de biblioteca de suporte.

Quando seu aplicativo precisa de uma fonte, você cria um `FontsRequest` objeto (especificando a fonte para fazer o download) e, em seguida, passá-lo para um `FontsContract` método para baixá-la. As etapas a seguir descrevem o processo de download de fonte mais detalhadamente:

1.  Criar uma instância de um [FontRequest](https://developer.android.com/reference/android/provider/FontRequest.html) objeto. 

2.  Subclasse e instanciar [FontsContract.FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html).

3.  Implementar o [FontRequestCallback.OnTypeFaceRetrieved](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29) método, que é usado para tratar a conclusão da solicitação de fonte.

4.  Implementar o [FontRequestCallback.OnTypeFaceRequestFailed](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29) método, que é usado para informar o seu aplicativo de quaisquer erros que ocorrem durante o processo de solicitação de fonte.

5.  Chamar o [FontsContract.RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback)) método para recuperar a fonte do provedor de fonte. 

Quando você chama o `RequestFonts` método, ele primeiro verifica se a fonte é armazenada em cache localmente (de uma chamada anterior para `RequestFont`). Se ele não está em cache, ele chama o provedor de fonte, recupera a fonte de forma assíncrona e, em seguida, passa os resultados para seu aplicativo, chamando o `OnTypeFaceRetrieved` método.

O [fontes carregáveis](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts) demonstra como usar o recurso de fontes carregáveis introduzido no Android Oreos. 

Para obter mais informações sobre como baixar fontes, consulte o desenvolvedor Android [fontes carregáveis](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html) tópico.



### <a name="autofill"></a>Preenchimento automático

O novo _preenchimento automático_ framework no Android Oreos torna mais fácil para os usuários lidar com tarefas repetitivas, como logon, a criação de conta e as transações com cartão de crédito. Os usuários perdem menos tempo digitar novamente as informações (o que podem levar a erros de entrada). Antes que seu aplicativo pode trabalhar com o Framework de preenchimento automático, um serviço de preenchimento automático deve ser habilitado nas configurações do sistema (os usuários podem habilitar ou desabilitar preenchimento automático).

O [AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework/) exemplo demonstra o uso do Framework de preenchimento automático. Ele inclui implementações de cliente atividades com modos de exibição que deve ser autofilled e um serviço que pode fornecer dados de preenchimento automático para as atividades do cliente.

Para obter mais informações sobre o novo recurso de preenchimento automático e como otimizar seu aplicativo para preenchimento automático, consulte o desenvolvedor Android [Framework de preenchimento automático](https://developer.android.com/guide/topics/text/autofill.html) tópico.



### <a name="picture-in-picture-pip"></a>Figura Figura (PIP)

Android Oreos possibilita uma atividade para iniciar em modo de (PIP) de imagem na imagem, sobrepondo a tela de outra atividade. Esse recurso destina-se a reprodução de vídeo.

Para especificar que a atividade do seu aplicativo pode usar modo PIP, defina o seguinte sinalizador como true no manifesto do Android:

```xml
android:supportsPictureInPicture
```

Para especificar como a atividade deve se comportar quando ele estiver no modo PIP, use o novo [PictureInPictureParams](https://developer.android.com/reference/android/app/PictureInPictureParams.html) objeto. `PictureInPictureParams` representa um conjunto de parâmetros que podem ser usados para inicializar e atualizar uma atividade no modo PIP (por exemplo, da atividade preferencial proporção). Os novos métodos PIP a seguir foram adicionados ao `Activity` no Android Oreos:

-   [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29) &ndash; coloca a atividade no modo PIP. A atividade é colocada no canto superior da tela e o restante da tela é preenchido com a atividade anterior que estava na tela.

-   [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29) &ndash; de atualizações de definições de configuração de PIP da atividade (por exemplo, uma alteração na taxa de proporção).

O [PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture) exemplo demonstra o uso básico do modo de imagem na imagem (PiP) para dispositivos portáteis introduzido no Oreos. O exemplo reproduz um vídeo que continua sem interrupção ao alternar entre modos de exibição ou outras atividades.



### <a name="other-features"></a>Outros recursos

Android Oreos contém muitos outros novos recursos, como os limites de plano de fundo Emoji suporte biblioteca, API de localização, cor de toda a gama de aplicativos, novos codecs de áudio, aprimoramentos de exibição da Web, suporte de navegação de teclado aprimorado e uma nova API AAudio (áudio pro) para áudio de baixa latência de alto desempenho, para obter mais informações sobre esses recursos, consulte o desenvolvedor Android [APIs e recursos do Android Oreos](https://developer.android.com/about/versions/oreo/android-8.0.html) tópico.



## <a name="behavior-changes"></a>Alterações de comportamento

Android Oreos incluem uma variedade de sistema e as alterações de comportamento de API que podem ter um impacto sobre a funcionalidade dos aplicativos existentes. Essas alterações são descritas a seguir.


### <a name="background-execution-limits"></a>Limites de execução do plano de fundo

Para melhorar a experiência do usuário, o Android Oreos impõe limitações sobre o que os aplicativos podem fazer durante a execução em segundo plano. Por exemplo, se o usuário é assistir a um vídeo ou um jogo, um aplicativo em execução em segundo plano pode prejudicar o desempenho de um aplicativo com uso intensivo de vídeo em execução em primeiro plano. Como resultado, o Android Oreos coloca as seguintes restrições em aplicativos que não estão interagindo diretamente com o usuário:

1.  **Limitações do serviço de plano de fundo** &ndash; quando um aplicativo é executado em segundo plano, ele tem uma janela de vários minutos em que ele ainda será permitido para criar e usar os serviços. No final dessa janela Android interrompe o serviço de plano de fundo do aplicativo e o tratará como sendo _ocioso_.

2.  **Limitações de difusão** &ndash; Android 7.0 (API 25) colocado limitações em transmissões registra um aplicativo para receber. Android Oreos torna essas limitações mais rigorosas. Por exemplo, aplicativos do Android Oreos não podem registrar receptores difusão para transmissões implícita em seus manifestos.

Para obter mais informações sobre os novos limites de execução do plano de fundo, consulte o desenvolvedor Android [limites de execução do plano de fundo](https://developer.android.com/about/versions/oreo/background.html) tópico.


### <a name="breaking-changes"></a>Alterações significativas

Aplicativos de destino Oreos Android ou superior, deverá modificar seus aplicativos para dar suporte as alterações a seguir, onde aplicável:

- Android Oreos substitui a capacidade de definir a prioridade de notificações individuais. Em vez disso, você definir um nível de importância recomendado ao criar um canal de notificação. O nível de prioridade que você atribui a um canal de notificação se aplica a todas as mensagens de notificação lançar a ele.

- Para aplicativos voltados para o Android Oreos, `PendingIntent.GetService()` não funciona devido a novos limites colocados em serviços iniciados em segundo plano. Se você estiver direcionando Oreos Android, você deve usar [PendingIntent.GetBroadcast](https://developer.xamarin.com/api/member/Android.App.PendingIntent.GetBroadcast/p/Android.Content.Context/System.Int32/Android.Content.Intent/Android.App.PendingIntentFlags/) em vez disso.  


## <a name="sample-code"></a>Código de exemplo

Vários exemplos de xamarin estão disponíveis para mostrar como tirar proveito dos recursos de Oreos Android:

-   [NotificationsChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels) demonstra como usar o novo sistema de canais de notificação introduzido no Android Oreos. Este exemplo gerencia dois canais de notificação: um com prioridade padrão e o outro com alta prioridade.

-   [PictureInPicture](https://developer.xamarin.com/samples/monodroid/android-o/PictureInPicture) demonstra o uso básico do modo de imagem na imagem (PiP) para dispositivos portáteis introduzido no Oreos. O exemplo reproduz um vídeo que continua sem interrupção ao alternar entre modos de exibição ou outras atividades.

-   [AutofillFramework](https://developer.xamarin.com/samples/monodroid/android-o/AutoFillFramework) demonstra o uso do Framework de preenchimento automático. Ele inclui implementações de cliente atividades com modos de exibição que deve ser autofilled e um serviço que pode fornecer dados de preenchimento automático para as atividades do cliente.

-   [Fontes carregáveis](https://developer.xamarin.com/samples/monodroid/android-o/DownloadableFonts) fornece um exemplo de como usar o recurso de fontes para download descrito anteriormente.

-   [EmojiCompat](https://developer.xamarin.com/samples/monodroid/android-o/EmojiCompat) demonstra o uso da biblioteca de suporte de EmojiCompat. Você pode usar esta biblioteca para impedir que seu aplicativo de caracteres de emoji ausente mostrando como os caracteres "tofu".

-   [Local atualizações pendentes intenção](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdPendIntent) ilustra o uso da API do local para obter atualizações sobre o local do dispositivo usando um `PendingIntent`.

-   [Serviço de primeiro plano de atualizações local](https://developer.xamarin.com/samples/monodroid/android-o/AndroidPlayLocation/LocUpdFgService) demonstra como usar a API de local para obter atualizações sobre o local do dispositivo usando um serviço de primeiro plano associado e iniciado.


## <a name="video"></a>Vídeo

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**Android development Oreos 8.0 com c#**


## <a name="summary"></a>Resumo

Este artigo introduzido Oreos Android e explicou como instalar e configurar as ferramentas mais recentes e os pacotes para o desenvolvimento do xamarin em Oreos Android. Ele fornecida uma visão geral dos principais recursos disponíveis no Android Oreos, com links para o código de exemplo para vários recursos novos. Ele incluído links para documentação da API e Android Developer tópicos para ajudá-lo a começar a criação de aplicativos para Android Oreos. Ele também realçado as alterações de comportamento Oreos Android mais importantes que podem afetar aplicativos existentes.


## <a name="related-links"></a>Links relacionados

- [Android Oreos 8.0](https://developer.android.com/index.html)
