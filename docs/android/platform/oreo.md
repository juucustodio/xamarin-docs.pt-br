---
title: Recursos do Oreo
description: Como começar a usar o Xamarin. Android para desenvolver aplicativos para a versão mais recente do Android.
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: conceptdev
ms.author: crdun
ms.date: 07/06/2018
ms.openlocfilehash: 798819176c741d8afc07f9cd097a61d753debbc9
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523767"
---
# <a name="oreo-features"></a>Recursos do Oreo

_Como começar a usar o Xamarin. Android para desenvolver aplicativos para a versão mais recente do Android._

[Android 8,0 Oreo](https://developer.android.com/index.html) é a versão mais recente do Android disponível no Google. O Android Oreo oferece muitos recursos novos de interesse para desenvolvedores do Xamarin. Android. Esses recursos incluem canais de notificação, notificações de notificação, fontes personalizadas em XML, fontes para download, preenchimento automático e imagem em imagem (PIP). O Android Oreo inclui novas APIs para esses novos recursos, e essas APIs estão disponíveis para aplicativos Xamarin. Android quando você usa o Xamarin. Android 8,0 e posterior.

[![Imagem do herói Oreo do Android](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

Este artigo foi estruturado para ajudá-lo a começar a desenvolver aplicativos Xamarin. Android para Android 8,0 Oreo. Ele explica como instalar as atualizações necessárias, configurar o SDK e criar um emulador (ou dispositivo) para teste. Ele também fornece uma descrição dos novos recursos no Android 8,0 Oreo, com links para aplicativos de exemplo que ilustram como usar os recursos do Oreo do Android em aplicativos Xamarin. Android.


## <a name="requirements"></a>Requisitos

O seguinte é necessário para usar os recursos do Android Oreo em aplicativos baseados no Xamarin:

- **Visual Studio** &ndash; Se você estiver usando o Windows, a versão 15,5 ou posterior do Visual Studio será necessária.  Se você estiver usando um Mac, Visual Studio para Mac versão 7.2.0 será necessária.

- **Xamarin. Android** &ndash; xamarin. Android 8,0 ou posterior deve ser instalado e configurado com o Visual Studio.

- **SDK do Android** &ndash; SDK do Android 8,0 (API 26) ou posterior deve ser instalado por meio do Gerenciador de SDK do Android.



## <a name="getting-started"></a>Guia de Introdução

Para começar a usar o Android Oreo com o Xamarin. Android, você deve baixar e instalar as ferramentas e os pacotes de SDK mais recentes antes de criar um projeto Oreo do Android:

1. Atualize para a versão mais recente do Visual Studio.

2. Instale o **Android 8.0.0 (API 26)** ou pacotes e ferramentas posteriores por meio do Gerenciador do SDK.

3. Crie um novo projeto Xamarin. Android que tenha como alvo o Android Oreo (API 26).

4. Configure um emulador ou dispositivo para testar aplicativos Android Oreo.

Cada uma dessas etapas é explicada nas seguintes seções:

### <a name="update-visual-studio-and-xamarinandroid"></a>Atualizar o Visual Studio e o Xamarin. Android

Para adicionar o suporte do Android Oreo ao Visual Studio, faça o seguinte:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

- Para o Visual Studio 2019, use o [Gerenciador do SDK](~/android/get-started/installation/android-sdk.md) para instalar o nível de API 26,0 ou posterior.

- Se você estiver usando o Visual Studio 2017:

    1. Atualize para o Visual Studio 2017 versão 15,7 ou posterior (consulte [atualizar o visual studio 2017](https://docs.microsoft.com/visualstudio/install/update-visual-studio)).

    2. Use o [Gerenciador do SDK](~/android/get-started/installation/android-sdk.md) para instalar o nível de API 26,0 ou posterior.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

- Atualize para a versão estável mais recente do Visual Studio para Mac, conforme explicado em [atualizando Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/update).

-----

Para obter mais informações sobre o suporte do Xamarin para Android Oreo, consulte [notas de versão do xamarin. Android 8,0](https://docs.microsoft.com/xamarin/android/release-notes/8/8.0/).



### <a name="install-the-android-sdk"></a>Instalar o SDK do Android

Para criar um projeto com o Xamarin. Android 8,0, primeiro você deve usar o Xamarin SDK do Android Manager para instalar a plataforma SDK para **Android 8,0-Oreo** ou posterior. Você também deve instalar o Android SDK Tools 26,0 ou posterior.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Inicie o Gerenciador do SDK (no Visual Studio, clique em **ferramentas > Android > o Gerenciador de SDK do Android**).

2. Instale os pacotes do **Android 8,0-Oreo** . Se você estiver usando o emulador de SDK do Android, certifique-se de incluir as imagens do sistema **x86** que serão necessárias:

    [![Selecionando pacotes do Android 8,0 no Gerenciador de SDK do Android](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. Instale o **Android SDK Tools 26.0.2** ou posterior, **SDK do Android plataforma-ferramentas 26.0.0** ou posterior e **SDK do Android 26.0.0 de ferramentas de compilação** (ou posterior):

    [![Selecionando Android SDK Tools 26 no Gerenciador de SDK do Android](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Inicie o Gerenciador do SDK (em Visual Studio para Mac, clique em **ferramentas > Gerenciador do SDK**).

2. Instale os pacotes do SDK **8,0-Oreo do Android** . Se você estiver usando o emulador de SDK do Android, certifique-se de incluir as imagens do sistema **x86** que serão necessárias:

    [![Selecionando pacotes do Android 8,0 no Gerenciador do SDK](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. Instale o **Android SDK Tools 26.0.2** ou posterior, **SDK do Android plataforma-ferramentas 26.0.0** ou posterior e **SDK do Android 26.0.0 de ferramentas de compilação** (ou posterior):

    [![Selecionando o Android SDK Tools 26 no Gerenciador do SDK](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----



### <a name="start-a-xamarinandroid-project"></a>Iniciar um projeto Xamarin. Android

Crie um novo projeto Xamarin. Android. Se você for novo no desenvolvimento do Android com o Xamarin, consulte [Olá, Android](~/android/get-started/hello-android/index.md) para saber mais sobre como criar projetos do Xamarin. Android.

Ao criar um projeto do Android, você deve definir as configurações de versão para o Android 8,0 ou posterior de destino. Por exemplo, para direcionar seu projeto para Android 8,0, você deve configurar o nível de API do Android de destino do seu projeto para **android 8,0 (API 26)** . É recomendável que você também defina seu nível de estrutura de destino como API 26 ou posterior. Para saber mais sobre como configurar níveis de nível de API do Android, confira [noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md)


### <a name="configure-an-emulator-or-device"></a>Configurar um emulador ou dispositivo

Se você tentar iniciar o Gerenciador de AVD baseado em GUI do Google padrão depois de instalar o Android SDK Tools 26,0 ou posterior, você poderá obter a seguinte caixa de diálogo de erro, que instrui você a usar a ferramenta de linha de comando AVD Manager **avdmanager** em vez disso:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Caixa de diálogo de aviso do Android Emulator Manager](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Caixa de diálogo de aviso do Android Emulator Manager](oreo-images/mac/03-avd-warning.png)

-----

Essa mensagem é exibida porque o Google não fornece mais um Gerenciador de AVD de GUI autônomo que dá suporte à API 26,0 e posterior. Para o Android 8,0 Oreo, você deve usar o Xamarin Android Emulator Manager ou a ferramenta de linha `avdmanager` de comando para criar dispositivos virtuais para Oreo do Android.

Para usar o Android Device Manager para criar e gerenciar dispositivos virtuais, consulte [Gerenciando dispositivos virtuais com o Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md).
Para criar dispositivos virtuais sem o Android Device Manager, siga as etapas na próxima seção.


#### <a name="creating-virtual-devices-using-avdmanager"></a>Criando dispositivos virtuais usando o avdmanager

Para usar o **avdmanager** para criar um novo dispositivo virtual, siga estas etapas:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Abra uma janela de prompt de comando `JAVA_HOME` e defina como o local do SDK do Java em seu computador. Para uma instalação típica do Xamarin, você pode usar o seguinte comando:

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2. Adicione o local da pasta SDK do Android `bin` ao seu. `PATH`
    Para uma instalação típica do Xamarin, você pode usar o seguinte comando:

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3. Feche a janela do prompt de comando e abra uma nova janela do prompt de comando. Crie um novo dispositivo virtual usando o comando [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) . Por exemplo, para criar um AVD chamado **AVD-Oreo-8,0** usando a imagem do sistema x86 para o nível de API 26, use o seguinte comando:

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4. Quando for solicitado **que você deseja criar um perfil de hardware personalizado [não]** , você poderá digitar **não** e aceitar o perfil de hardware padrão. Se você disser **Sim**, o **avdmanager** emitirá uma lista de perguntas para personalizar o perfil de hardware.

Depois de **avdmanager** criar seu dispositivo virtual, ele será incluído no menu suspenso do dispositivo:

[![Novo AVD adicionado ao menu suspenso do dispositivo](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Abra uma janela de **terminal** e altere para o local do diretório de ferramentas de SDK do Android no seu Mac. Para uma instalação típica do Xamarin, você pode usar o seguinte comando:

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2. Crie um novo dispositivo virtual usando o comando [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html) . Por exemplo, para criar um AVD chamado **AVD-Oreo-8,0** usando a imagem do sistema x86 para o nível de API 26, use o seguinte comando:

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3. Quando for solicitado **que você deseja criar um perfil de hardware personalizado [não]** , você poderá digitar **não** e aceitar o perfil de hardware padrão. Se você disser **Sim**, o **avdmanager** emitirá uma lista de perguntas para personalizar o perfil de hardware.

Depois de usar o **avdmanager** para criar seu dispositivo virtual, ele será incluído no menu suspenso do dispositivo:

[![Novo AVD adicionado ao menu suspenso do dispositivo](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

Para obter mais informações sobre como configurar um emulador do Android para teste e depuração, consulte Depurando [no Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md).

Se você estiver usando um dispositivo físico, como um Nexus ou um pixel, poderá atualizar seu dispositivo por meio de atualizações automáticas por satélite (OTA) ou baixar uma imagem do sistema e atualizar seu dispositivo diretamente. Para obter mais informações sobre a atualização manual do dispositivo para o Android Oreo, consulte [imagens de fábrica para dispositivos Nexus e de pixel](https://developers.google.com/android/images).



## <a name="new-features"></a>Novos recursos

O Android Oreo apresenta uma variedade de novos recursos e funcionalidades, como canais de notificação, notificações de notificação, fontes personalizadas em XML, fontes para download, preenchimento automático e imagem em imagem. As seções a seguir destacam esses recursos e fornecem links para ajudá-lo a começar a usá-los em seu aplicativo.



### <a name="notification-channels"></a>Canais de notificação

Os *canais de notificação* são categorias definidas pelo aplicativo para notificações.
Você pode criar um canal de notificação para cada tipo de notificação que precisa enviar e pode criar canais de notificação para refletir as escolhas feitas pelos usuários do seu aplicativo. O novo recurso de canais de notificação possibilita que você forneça ao usuário um controle refinado sobre diferentes tipos de notificações. Por exemplo, se você estiver implementando um aplicativo de mensagens, poderá criar canais de notificação separados para cada grupo de conversa criado por um usuário.

Os [canais de notificação](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan) explicam como criar um canal de notificação e usá-lo para postar notificações locais. Para obter um exemplo de código do mundo real, consulte o exemplo [NotificationChannels](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-notificationchannels) ; Este aplicativo de exemplo gerencia dois canais e define opções de notificação adicionais.



### <a name="notification-badges"></a>Notificações de notificação

As notificações de notificação são pequenos pontos que aparecem sobre ícones de aplicativos, conforme mostrado nesta captura de tela:

[![Notificações de notificação de exemplo em ícones de aplicativo](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

Esses pontos indicam que há novas notificações para um ou mais canais de notificação no aplicativo associado a esse ícone &ndash; de aplicativo essas são notificações que o usuário ainda não ignorou ou agiu. Os usuários podem pressionar por longos um ícone para resumir as notificações associadas a um crachá de notificação, descartando ou agindo em notificações do menu de pressionamento longo que appeaars.

Para obter mais informações sobre notificações de notificação, consulte o tópico notificações de [notificação](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges) para desenvolvedores do Android.



### <a name="custom-fonts-in-xml"></a>Fontes personalizadas em XML

O Android Oreo introduz *fontes em XML*, o que torna possível incorporar fontes personalizadas como recursos. Há suporte para os formatos de fonte OpenType ( **. otf**) e TrueType ( **. ttf**). Para adicionar fontes como recursos, faça o seguinte:

1. Crie uma pasta de **recursos/fontes** .

2. Copie os arquivos de fonte (exemplo, **. ttf** e **. otf** ) para **recursos/fontes**. 

3. Se necessário, renomeie cada arquivo de fonte para que ele atenda às convenções de nomenclatura de arquivo do Android (ou seja, use apenas letras minúsculas *a-z*, *0-9*e sublinhados em nomes de arquivo). Por exemplo, o arquivo `Pacifico-Regular.ttf` de fonte pode ser renomeado para algo como. `pacifico.ttf`

4. Aplique a fonte personalizada usando o novo `android:fontFamily` atributo no XML de layout. Por exemplo, a Declaração `TextView` a seguir usa o recurso de fonte **Pacifico. ttf** adicionado:

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

Você também pode criar um arquivo XML da família de fontes que descreve várias fontes, bem como detalhes de estilo e peso. Para obter mais informações, consulte o tópico [fontes de](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html) desenvolvedor do Android no XML.


### <a name="downloadable-fonts"></a>Fontes para download

A partir do Android Oreo, os aplicativos podem solicitar fontes de um provedor em vez de agrupá-las no APK. As fontes são baixadas da rede somente conforme necessário. Esse recurso reduz o tamanho do APK, conservando a memória do telefone e o uso de dados da rede celular. Você também pode usar esse recurso nas versões 14 ou superiores da API do Android instalando o pacote da biblioteca de suporte do Android 26.

Quando seu aplicativo precisar de uma fonte, você cria `FontsRequest` um objeto (especificando a fonte a ser baixada) e, em `FontsContract` seguida, passa-o para um método para baixar a fonte. As etapas a seguir descrevem o processo de download de fonte em mais detalhes:

1. Crie uma instância de um objeto [FontRequest](https://developer.android.com/reference/android/provider/FontRequest.html) . 

2. Subclasse e instancie [FontsContract. FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html).

3. Implemente o método [FontRequestCallback. OnTypeFaceRetrieved](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29) , que é usado para lidar com a conclusão da solicitação de fonte.

4. Implemente o método [FontRequestCallback. OnTypeFaceRequestFailed](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29) , que é usado para informar seu aplicativo sobre quaisquer erros que ocorrem durante o processo de solicitação de fonte.

5. Chame o método [FontsContract. RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback)) para recuperar a fonte do provedor de fontes. 

Quando você chama o `RequestFonts` método, ele primeiro verifica se a fonte está armazenada em cache localmente (de uma chamada anterior para `RequestFont`). Se não estiver armazenado em cache, ele chamará o provedor de fontes, recuperará a fonte de forma assíncrona e, em seguida, passará os `OnTypeFaceRetrieved` resultados de volta para seu aplicativo invocando seu método.

O exemplo de [fontes baixáveis](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-downloadablefonts) demonstra como usar o recurso de fontes para download introduzido no Android Oreo. 

Para obter mais informações sobre como baixar fontes, consulte o tópico [fontes para download](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html) de desenvolvedores do Android.



### <a name="autofill"></a>Preenchimento automático

A nova estrutura de _preenchimento automático_ no Android Oreo torna mais fácil para os usuários manipular tarefas repetitivas, como logon, criação de conta e transações de cartão de crédito. Os usuários passam menos tempo redigitando as informações (o que pode levar a erros de entrada). Antes que seu aplicativo possa trabalhar com a estrutura de preenchimento automático, um serviço de preenchimento automático deve ser habilitado nas configurações do sistema (os usuários podem habilitar ou desabilitar o preenchimento automático).

O exemplo [AutofillFramework](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-autofillframework) demonstra o uso da estrutura de preenchimento automático. Ele inclui implementações de atividades do cliente com exibições que devem ser preenchidas de modo automático e um serviço que pode fornecer dados de Autopreenchimento para atividades do cliente.

Para obter mais informações sobre o novo recurso de preenchimento automático e como otimizar seu aplicativo para preenchimento automático, consulte o tópico [Framework de preenchimento automático](https://developer.android.com/guide/topics/text/autofill.html) para desenvolvedores do Android.



### <a name="picture-in-picture-pip"></a>Imagem em imagem (PIP)

O Android Oreo torna possível que uma atividade seja iniciada no modo PIP (Picture-in-Picture), sobrepondo a tela de outra atividade. Este recurso destina-se à reprodução de vídeo.

Para especificar que a atividade do aplicativo pode usar o modo PIP, defina o seguinte sinalizador como true no manifesto do Android:

```xml
android:supportsPictureInPicture
```

Para especificar como sua atividade deve se comportar quando estiver no modo PIP, use o novo objeto [PictureInPictureParams](https://developer.android.com/reference/android/app/PictureInPictureParams.html) . `PictureInPictureParams`representa um conjunto de parâmetros que você usa para inicializar e atualizar uma atividade no modo PIP (por exemplo, a taxa de proporção preferida da atividade). Os novos métodos Pip a seguir foram adicionados `Activity` ao no Android Oreo:

- [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29) &ndash; coloca a atividade no modo PIP. A atividade é colocada no canto da tela e o restante da tela é preenchido com a atividade anterior que estava na tela.

- [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29) &ndash; Atualiza as definições de configuração de Pip da atividade (por exemplo, uma alteração na taxa de proporção).

O exemplo [PictureInPicture](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-pictureinpicture) demonstra o uso básico do modo PIP (Picture-in-Picture) para dispositivos portáteis introduzidos no Oreo. O exemplo reproduz um vídeo que continua sem interrupção enquanto alterna entre os modos de exibição ou outras atividades.



### <a name="other-features"></a>Outros recursos

O Android Oreo contém muitos outros novos recursos, como a biblioteca de suporte de Emoji, a API de local, os limites de plano de fundo, a cor de ampla gama de aplicativos, novos codecs de áudio, aprimoramentos do WebView, suporte aprimorado à navegação de teclado e uma nova API de AAudio (Pro Audio) para áudio de baixa latência de alto desempenho, para obter mais informações sobre esses recursos, consulte o tópico [recursos e APIs de Oreo do Android](https://developer.android.com/about/versions/oreo/android-8.0.html) para desenvolvedores do Android.



## <a name="behavior-changes"></a>Alterações de comportamento

O Android Oreo inclui uma variedade de alterações de comportamento de API e sistema que podem afetar a funcionalidade dos aplicativos existentes. Essas alterações são descritas a seguir.


### <a name="background-execution-limits"></a>Limites de execução em segundo plano

Para melhorar a experiência do usuário, o Android Oreo impõe limitações sobre o que os aplicativos podem fazer durante a execução em segundo plano. Por exemplo, se o usuário estiver assistindo a um vídeo ou jogando um jogo, um aplicativo em execução em segundo plano poderá prejudicar o desempenho de um aplicativo com uso intensivo de vídeo em execução em primeiro plano. Como resultado, o Android Oreo coloca as seguintes restrições em aplicativos que não estão interagindo diretamente com o usuário:

1. **Limitações de serviço em segundo plano** &ndash; Quando um aplicativo está em execução em segundo plano, ele tem uma janela de vários minutos em que ainda é permitido criar e usar serviços. No final dessa janela, o Android interrompe o serviço em segundo plano do aplicativo e o trata como estando _ocioso_.

2. **Limitações de difusão** &ndash; O Android 7,0 (API 25) colocou as limitações nas difusões que um aplicativo registra para receber. O Android Oreo torna essas limitações mais rigorosas. Por exemplo, os aplicativos Android Oreo não podem mais registrar receptores de difusão para difusões implícitas em seus manifestos.

Para obter mais informações sobre os novos limites de execução em segundo plano, consulte o tópico [limites de execução em segundo plano](https://developer.android.com/about/versions/oreo/background.html) do desenvolvedor do Android.


### <a name="breaking-changes"></a>Alterações significativas

Os aplicativos destinados ao Android Oreo ou superior devem modificar seus aplicativos para dar suporte às seguintes alterações, quando aplicável:

- O Android Oreo substitui a capacidade de definir a prioridade de notificações individuais. Em vez disso, você define um nível de importância recomendado ao criar um canal de notificação. O nível de importância que você atribui a um canal de notificação se aplica a todas as mensagens de notificação postadas nele.

- Para aplicativos destinados a Oreo do `PendingIntent.GetService()` Android, o não funciona devido a novos limites colocados em serviços iniciados em segundo plano. Se você estiver destinando ao Android Oreo, deverá usar [PendingIntent. getbroadcast](xref:Android.App.PendingIntent.GetBroadcast*) .  


## <a name="sample-code"></a>Código de exemplo

Vários exemplos do Xamarin. Android estão disponíveis para mostrar como aproveitar os recursos do Oreo do Android:

- [NotificationsChannels](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-notificationchannels) demonstra como usar o novo sistema de canais de notificação introduzido no Android Oreo. Este exemplo gerencia dois canais de notificações: um com importância padrão e o outro com alta importância.

- O [PictureInPicture](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-pictureinpicture) demonstra o uso básico do modo PIP (Picture-in-Picture) para dispositivos portáteis introduzidos no Oreo. O exemplo reproduz um vídeo que continua sem interrupção enquanto alterna entre os modos de exibição ou outras atividades.

- [AutofillFramework](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-autofillframework) demonstra o uso da estrutura de preenchimento automático. Ele inclui implementações de atividades do cliente com exibições que devem ser preenchidas de modo automático e um serviço que pode fornecer dados de Autopreenchimento para atividades do cliente.

- As [fontes para download](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-downloadablefonts) fornecem um exemplo de como usar o recurso de fontes para download descrito anteriormente.

- [EmojiCompat](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-emojicompat) demonstra o uso da biblioteca de suporte do EmojiCompat. Você pode usar essa biblioteca para impedir que seu aplicativo mostre caracteres de Emoji ausentes como caracteres "tofu".

- A [tentativa pendente de atualizações de local](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-androidplaylocation-locupdpendintent) ilustra o uso da API de localização para obter atualizações sobre o local `PendingIntent`de um dispositivo usando um.

- O [serviço de primeiro plano de atualizações de local](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-androidplaylocation-locupdfgservice) demonstra como usar a API de localização para obter atualizações sobre o local de um dispositivo usando um serviço de primeiro plano associado e iniciado.


## <a name="video"></a>Vídeo

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**Desenvolvimento do Android 8,0 Oreo comC#**


## <a name="summary"></a>Resumo

Este artigo introduziu o Android Oreo e explicou como instalar e configurar as ferramentas e os pacotes mais recentes para o desenvolvimento do Xamarin. Android no Android Oreo. Ele forneceu uma visão geral dos principais recursos disponíveis no Android Oreo, com links para código-fonte de exemplo para vários novos recursos. Ele inclui links para documentação de API e tópicos para desenvolvedores do Android para ajudá-lo a começar a criar aplicativos para Android Oreo. Ele também realçou as alterações de comportamento Oreo do Android mais importantes que poderiam afetar os aplicativos existentes.


## <a name="related-links"></a>Links relacionados

- [Android 8,0 Oreo](https://developer.android.com/index.html)
