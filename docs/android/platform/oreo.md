---
title: Características de Oreo
description: Como começar a usar o Xamarin.Android para desenvolver aplicativos para a versão mais recente do Android.
ms.prod: xamarin
ms.assetid: EAEF7341-7A00-4439-9FAF-43882637BEF8
ms.technology: xamarin-android
ms.custom: video
author: davidortinau
ms.author: daortin
ms.date: 07/06/2018
ms.openlocfilehash: 56430f8c4988c16a31f9806b0ffb8b6355d6340b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019994"
---
# <a name="oreo-features"></a>Características de Oreo

_Como começar a usar o Xamarin.Android para desenvolver aplicativos para a versão mais recente do Android._

[Android 8.0 Oreo](https://developer.android.com/index.html) é a versão mais recente do Android disponível no Google. O Android Oreo oferece muitos novos recursos de interesse para os desenvolvedores do Xamarin.Android. Esses recursos incluem canais de notificação, crachás de notificação, fontes personalizadas no XML, fontes para download, preenchimento automático e imagem em imagem (PIP). O Android Oreo inclui novas APIs para esses novos recursos, e essas APIs estão disponíveis para aplicativos Xamarin.Android quando você usa Xamarin.Android 8.0 e posterior.

[![Imagem de herói Android Oreo](oreo-images/01-android-o-logo-sml.png)](oreo-images/01-android-o-logo.png#lightbox)

Este artigo está estruturado para ajudá-lo a começar a desenvolver aplicativos Xamarin.Android para Android 8.0 Oreo. Ele explica como instalar as atualizações necessárias, configurar o SDK e criar um emulador (ou dispositivo) para testes. Ele também fornece um esboço dos novos recursos no Android 8.0 Oreo, com links para provar aplicativos que ilustram como usar os recursos do Android Oreo em aplicativos Xamarin.Android.

## <a name="requirements"></a>Requisitos

A seguir é necessário usar os recursos do Android Oreo em aplicativos baseados em Xamarin:

- **Visual Studio** &ndash; Se você estiver usando o Windows, a versão 15.5 ou posterior do Visual Studio é necessária.  Se você estiver usando um Mac, o Visual Studio para mac versão 7.2.0 é necessário.

- **Xamarin.Android** &ndash; Xamarin.Android 8.0 ou posterior deve ser instalado e configurado com o Visual Studio.

- **Android SDK** &ndash; Android SDK 8.0 (API 26) ou posterior deve ser instalado através do Android SDK Manager.

## <a name="getting-started"></a>Introdução

Para começar a usar o Android Oreo com Xamarin.Android, você deve baixar e instalar as ferramentas mais recentes e pacotes SDK antes de criar um projeto Android Oreo:

1. Atualize para a versão mais recente do Visual Studio.

2. Instale os pacotes e ferramentas do **Android 8.0.0 (API 26)** ou posteriores através do SDK Manager.

3. Crie um novo projeto Xamarin.Android que tem como alvo o Android Oreo (API 26).

4. Configure um emulador ou dispositivo para testar aplicativos Android Oreo.

Cada uma dessas etapas é explicada nas seguintes seções:

### <a name="update-visual-studio-and-xamarinandroid"></a>Atualizar Visual Studio e Xamarin.Android

Para adicionar suporte ao Android Oreo ao Visual Studio, faça o seguinte:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

- Para o Visual Studio 2019, use o [SDK Manager](~/android/get-started/installation/android-sdk.md) para instalar o Nível 26.0 da API ou posterior.

- Se você estiver usando o Visual Studio 2017:

    1. Atualização para visual studio 2017 versão 15.7 ou posterior (ver [Atualização Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/update-visual-studio)).

    2. Use o [SDK Manager](~/android/get-started/installation/android-sdk.md) para instalar o nível 26.0 da API ou posterior.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

- Atualize para a versão estável mais recente do Visual Studio para Mac, conforme explicado no [Update Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/update).

-----

Para obter mais informações sobre o suporte a Xamarin para Android Oreo, consulte as [notas de versão do Xamarin.Android 8.0](https://docs.microsoft.com/xamarin/android/release-notes/8/8.0/).

### <a name="install-the-android-sdk"></a>Instalar o SDK do Android

Para criar um projeto com Xamarin.Android 8.0, primeiro você deve usar o Xamarin Android SDK Manager para instalar a plataforma SDK para **Android 8.0 - Oreo** ou posterior. Você também deve instalar o Android SDK Tools 26.0 ou posterior.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Inicie o SDK Manager (no Visual Studio, clique em **Ferramentas > Android > Android SDK Manager**).

2. Instale os pacotes **Android 8.0 - Oreo.** Se você estiver usando o emulador Android SDK, certifique-se de incluir as imagens do sistema **x86** que você precisará:

    [![Selecionando pacotes Android 8.0 no Android SDK Manager](oreo-images/win/01-android-o-packages.png)](oreo-images/win/01-android-o-packages.png#lightbox)

3. Instale **as ferramentas Android SDK 26.0.2** ou posteriores, **Android SDK Platform-Tools 26.0.0 ou** posteriores, e Android **SDK Build-Tools 26.0.0** (ou posterior):

    [![Selecionando ferramentas Android SDK 26 no Android SDK Manager](oreo-images/win/02-sdk-tools.png)](oreo-images/win/02-sdk-tools.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Inicie o SDK Manager (no Visual Studio para Mac, clique em **Ferramentas > SDK Manager**).

2. Instale os pacotes **Android 8.0 - Oreo** SDK. Se você estiver usando o emulador Android SDK, certifique-se de incluir as imagens do sistema **x86** que você precisará:

    [![Selecionando pacotes Android 8.0 no SDK Manager](oreo-images/mac/01-android-o-packages.png)](oreo-images/mac/01-android-o-packages.png#lightbox)

3. Instale **as ferramentas Android SDK 26.0.2** ou posteriores, **Android SDK Platform-Tools 26.0.0 ou** posteriores, e Android **SDK Build-Tools 26.0.0** (ou posterior):

    [![Selecionando ferramentas Android SDK 26 no SDK Manager](oreo-images/mac/02-sdk-tools.png)](oreo-images/mac/02-sdk-tools.png#lightbox)

-----

### <a name="start-a-xamarinandroid-project"></a>Inicie um Projeto Xamarin.Android

Crie um novo projeto Xamarin.Android. Se você é novo no desenvolvimento do Android com xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para aprender sobre a criação de projetos Xamarin.Android.

Ao criar um projeto Android, você deve configurar as configurações da versão para atingir o Android 8.0 ou posterior. Por exemplo, para direcionar seu projeto para o Android 8.0, você deve configurar o nível de API android de destino do seu projeto para **o Android 8.0 (API 26)**. Recomenda-se que você também defina seu nível de estrutura-alvo para API 26 ou posterior. Para obter mais informações sobre a configuração dos níveis de nível de API do Android, consulte [Entendendo os níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).

### <a name="configure-an-emulator-or-device"></a>Configure um emulador ou dispositivo

Se você tentar iniciar o AVD Manager padrão baseado em GUI do Google depois de instalar o Android SDK Tools 26.0 ou posterior, você poderá obter a seguinte caixa de diálogo de erro, que instrui você a usar a ferramenta **avdmanager** da linha de comando AVD em vez disso:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Diálogo de aviso do Android Emmulator Manager](oreo-images/win/03-avd-warning.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![Diálogo de aviso do Android Emmulator Manager](oreo-images/mac/03-avd-warning.png)

-----

Essa mensagem é exibida porque o Google não fornece mais um gerenciador de GUI AVD autônomo que suporta a API 26.0 e posterior. Para o Android 8.0 Oreo, você deve usar o Xamarin `avdmanager` Android Emulator Manager ou a ferramenta de linha de comando para criar dispositivos virtuais para Android Oreo.

Para usar o Gerenciador de Dispositivos Android para criar e gerenciar dispositivos virtuais, consulte [Gerenciando dispositivos virtuais com o Gerenciador de Dispositivos Android](~/android/get-started/installation/android-emulator/device-manager.md).
Para criar dispositivos virtuais sem o Android Device Manager, siga os passos na próxima seção.

#### <a name="creating-virtual-devices-using-avdmanager"></a>Criando dispositivos virtuais usando avdmanager

Para usar **o avdmanager** para criar um novo dispositivo virtual, siga estas etapas:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Abra uma janela De `JAVA_HOME` comando prompt e defina a localização do Java SDK em seu computador. Para uma instalação típica de Xamarin, você pode usar o seguinte comando:

    ```cmd
    setx JAVA_HOME "C:\Program Files\Java\jdk1.8.0_131"
    ```

2. Adicione a localização da pasta `bin` Android `PATH`SDK ao seu .
    Para uma instalação típica de Xamarin, você pode usar o seguinte comando:

    ```cmd
    setx PATH "%PATH%;C:\Program Files (x86)\Android\android-sdk\tools\bin"
    ```

3. Feche a janela Prompt de comando e abra uma nova janela de prompt de comando. Crie um novo dispositivo virtual usando o comando [avdmanager.](https://developer.android.com/studio/command-line/avdmanager.html) Por exemplo, para criar um AVD chamado **AVD-Oreo-8.0** usando a imagem do sistema x86 para o nível 26 da API, use o seguinte comando:

    ```cmd
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

4. Quando você é solicitado com **Você deseja criar um perfil de hardware personalizado [não]** você pode inserir **não** e aceitar o perfil de hardware padrão. Se você **disser sim,** **o avdmanager** solicitará uma lista de perguntas para personalizar o perfil de hardware.

Depois **de avdmanager** para criar seu dispositivo virtual, ele será incluído no menu de retirada do dispositivo:

[![Novo AVD adicionado ao menu de retirada do dispositivo](oreo-images/win/04-android-o-avd-sml.png)](oreo-images/win/04-android-o-avd.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Abra uma janela **de Terminal** e mude para a localização do diretório de ferramentas Android SDK em seu Mac. Para uma instalação típica de Xamarin, você pode usar o seguinte comando:

    ```bash
    cd ~/Library/Developer/Xamarin/android-sdk-macosx/tools/bin
    ```

2. Crie um novo dispositivo virtual usando o comando [avdmanager.](https://developer.android.com/studio/command-line/avdmanager.html) Por exemplo, para criar um AVD chamado **AVD-Oreo-8.0** usando a imagem do sistema x86 para o nível 26 da API, use o seguinte comando:

    ```bash
    avdmanager create avd -n AVD-Oreo-8.0 -k "system-images;android-26;google_apis;x86"
    ```

3. Quando você é solicitado com **Você deseja criar um perfil de hardware personalizado [não]** você pode inserir **não** e aceitar o perfil de hardware padrão. Se você **disser sim,** **o avdmanager** solicitará uma lista de perguntas para personalizar o perfil de hardware.

Depois de usar **o avdmanager** para criar seu dispositivo virtual, ele será incluído no menu de retirada do dispositivo:

[![Novo AVD adicionado ao menu de retirada do dispositivo](oreo-images/mac/04-android-o-avd-sml.png)](oreo-images/mac/04-android-o-avd.png#lightbox)

-----

Para obter mais informações sobre como configurar um emulador android para testes e depuração, consulte [Depuração no Emulador do Android](~/android/deploy-test/debugging/debug-on-emulator.md).

Se você estiver usando um dispositivo físico como um Nexus ou um Pixel, você pode atualizar seu dispositivo através de atualizações automáticas sobre o ar (OTA) ou baixar uma imagem do sistema e piscar seu dispositivo diretamente. Para obter mais informações sobre como atualizar manualmente seu dispositivo para Android Oreo, consulte [Imagens de Fábrica para Dispositivos Nexus e Pixel](https://developers.google.com/android/images).

## <a name="new-features"></a>Novos recursos

O Android Oreo introduz uma variedade de novos recursos e recursos, como canais de notificação, crachás de notificação, fontes personalizadas no XML, fontes para download, preenchimento automático e imagem em imagem. As seções a seguir destacam esses recursos e fornecem links para ajudá-lo a começar a usá-los em seu aplicativo.

### <a name="notification-channels"></a>Canais de notificação

*Os canais de notificação* são categorias definidas pelo aplicativo para notificações.
Você pode criar um canal de notificação para cada tipo de notificação que você precisa enviar, e você pode criar canais de notificação para refletir as escolhas feitas pelos usuários do seu aplicativo. O novo recurso de canais de notificação permite que você dê aos usuários controle fino sobre diferentes tipos de notificações. Por exemplo, se você estiver implementando um aplicativo de mensagens, você pode criar canais de notificação separados para cada grupo de conversação criado por um usuário.

[Os canais de notificação](~/android/app-fundamentals/notifications/local-notifications.md#notif-chan) explicam como criar um canal de notificação e usá-lo para postar notificações locais. Para obter um exemplo de código do mundo real, consulte a amostra [NotificationChannels;](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-notificationchannels) este aplicativo de exemplo gerencia dois canais e define opções adicionais de notificação.

### <a name="notification-badges"></a>Crachás de notificação

Os crachás de notificação são pequenos pontos que aparecem sobre ícones de aplicativos, como mostrado nesta captura de tela:

[![Exemplo de crachás de notificação em ícones de aplicativos](oreo-images/02-badges-sml.png)](oreo-images/02-badges.png#lightbox)

Esses pontos indicam que há novas notificações para um ou mais canais &ndash; de notificação no aplicativo associado a esse ícone do aplicativo, essas são notificações que o usuário ainda não descartou ou agiu. Os usuários podem pressionar por muito tempo em um ícone para olhar as notificações associadas a um crachá de notificação, descartando ou agindo em notificações do menu de imprensa longa que apela.

Para obter mais informações sobre crachás de notificação, consulte o tópico [Crachás de Notificação](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#Badges) do Desenvolvedor Android.

### <a name="custom-fonts-in-xml"></a>Fontes personalizadas em XML

O Android Oreo introduz *fontes em XML,* o que torna possível que você incorpore fontes personalizadas como recursos. Os formatos de fonte OpenType **(.otf)** e TrueType **(.ttf)** são suportados. Para adicionar fontes como recursos, faça o seguinte:

1. Crie uma pasta **Recursos/fonte.**

2. Copie seus arquivos de fonte (exemplo, **arquivos .ttf** e **.otf)** para **Resources/font**. 

3. Se necessário, renomeie cada arquivo de fonte para que ele adere às convenções de nomeação de arquivos Android (ou seja, use apenas minúsculas *a-z*, *0-9,* e sublinha em nomes de arquivos). Por exemplo, o `Pacifico-Regular.ttf` arquivo de fonte `pacifico.ttf`pode ser renomeado para algo como .

4. Aplique a fonte personalizada `android:fontFamily` usando o novo atributo em seu layout XML. Por exemplo, `TextView` a seguinte declaração usa o recurso de fonte **adicionado pacifico.ttf:**

   ```xml
   <TextView
     android:text="Example Text in Pacifico Regular"
     android:layout_width="wrap_content"
     android:layout_height="wrap_content"
     android:fontFamily="@font/pacifico" />
   ```

Você também pode criar um arquivo XML da família de fontes que descreve várias fontes, bem como detalhes de estilo e peso. Para obter mais informações, consulte o tópico Android Developer [Fonts no XML.](https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml.html)

### <a name="downloadable-fonts"></a>Fontes para download

Começando com o Android Oreo, os aplicativos podem solicitar fontes de um provedor em vez de adelá-las ao APK. As fontes são baixadas da rede apenas conforme necessário. Esse recurso reduz o tamanho do APK, conservando a memória do telefone e o uso de dados celulares. Você também pode usar esse recurso nas versões 14 do Android API ou superior, instalando o pacote Android Support Library 26.

Quando seu aplicativo precisa de `FontsRequest` uma fonte, você cria um objeto (especificando a fonte para baixar) e, em seguida, passa-o para um `FontsContract` método para baixar a fonte. As etapas a seguir descrevem o processo de download de fontes com mais detalhes:

1. Instanciar um objeto [FontRequest.](https://developer.android.com/reference/android/provider/FontRequest.html) 

2. Fontes de subclasse e [instanteiateSContract.FontRequestCallback](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html).

3. Implemente o método [FontRequestCallback.OnTypeFaceRetrieved,](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRetrieved%28android.graphics.Typeface%29) que é usado para lidar com a conclusão da solicitação de fonte.

4. Implemente o método [FontRequestCallback.OnTypeFaceRequestFailed,](https://developer.android.com/reference/android/provider/FontsContract.FontRequestCallback.html#onTypefaceRequestFailed%28int%29) que é usado para informar seu aplicativo de quaisquer erros que ocorram durante o processo de solicitação de fonte.

5. Ligue para o método [FontsContract.RequestFonts](https://developer.android.com/reference/android/provider/FontsContract.html#requestFonts(android.content.Context,%20android.provider.FontRequest,%20android.os.Handler,%20android.os.CancellationSignal,%20android.provider.FontsContract.FontRequestCallback)) para recuperar a fonte do provedor de fontes. 

Quando você `RequestFonts` chama o método, ele primeiro verifica se a fonte está `RequestFont`em cache local (de uma chamada anterior para ). Se não estiver em cache, ele chama o provedor de fontes, recupera a fonte de forma assíncrona e, em seguida, repassa os resultados para o seu aplicativo invocando seu `OnTypeFaceRetrieved` método.

A amostra [De Fontes Para Download](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-downloadablefonts) demonstra como usar o recurso Fontes Para Download introduzido no Android Oreo. 

Para obter mais informações sobre como baixar fontes, consulte o tópico [Fontes para Download do](https://developer.android.com/guide/topics/ui/look-and-feel/downloadable-fonts.html) Desenvolvedor Android.

### <a name="autofill"></a>Preenchimento automático

A nova estrutura _de preenchimento automático_ no Android Oreo facilita o manuseio de tarefas repetitivas, como login, criação de conta e transações de cartão de crédito. Os usuários gastam menos tempo redigitando informações (o que pode levar a erros de entrada). Antes que seu aplicativo possa trabalhar com o Autofill Framework, um serviço de preenchimento automático deve ser ativado nas configurações do sistema (os usuários podem ativar ou desativar o preenchimento automático).

A amostra [AutofillFramework](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-autofillframework) demonstra o uso do Autofill Framework. Ele inclui implementações de atividades do cliente com visualizações que devem ser preenchidas automaticamente e um Serviço que pode fornecer dados de preenchimento automático às atividades do cliente.

Para obter mais informações sobre o novo recurso autopreenchimento e como otimizar seu aplicativo para preenchimento automático, consulte o tópico Android Developer [Autofill Framework.](https://developer.android.com/guide/topics/text/autofill.html)

### <a name="picture-in-picture-pip"></a>Imagem em Imagem (PIP)

O Android Oreo permite que uma Atividade seja lançada no modo PIP (picture-in-picture), sobrepondo a tela de outra Atividade. Este recurso destina-se à reprodução de vídeo.

Para especificar que a Atividade do seu aplicativo pode usar o modo PIP, defina o seguinte sinalizador como verdadeiro no manifesto do Android:

```xml
android:supportsPictureInPicture
```

Para especificar como sua atividade deve se comportar quando estiver no modo PIP, você usa o novo objeto [PictureInPictureParams.](https://developer.android.com/reference/android/app/PictureInPictureParams.html) `PictureInPictureParams`representa um conjunto de parâmetros que você usa para inicializar e atualizar uma atividade no modo PIP (por exemplo, a proporção preferida da Atividade). Os seguintes novos métodos `Activity` PIP foram adicionados no Android Oreo:

- [EnterPictureInPictureMode](https://developer.android.com/reference/android/app/Activity.html#enterPictureInPictureMode%28android.app.PictureInPictureParams%29) &ndash; coloca a atividade no modo PIP. A Atividade é colocada no canto da tela, e o resto da tela é preenchida com a atividade anterior que estava na tela.

- [SetPictureInPictureParams](https://developer.android.com/reference/android/app/Activity.html#setPictureInPictureParams%28android.app.PictureInPictureParams%29) &ndash; Atualiza as configurações de configuração PIP da Atividade (por exemplo, uma mudança na proporção).

A amostra [PictureInPicture](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-pictureinpicture) demonstra o uso básico do modo Picture-in-Picture (PiP) para dispositivos portáteis introduzidos no Oreo. A amostra reproduz um vídeo que continua ininterrupto enquanto alterna entre os modos de exibição ou outras atividades.

### <a name="other-features"></a>Outros recursos

O Android Oreo contém muitos outros recursos novos, como a biblioteca de suporte ao Emoji, API de localização, limites de fundo, cores amplas para aplicativos, novos codecs de áudio, aprimoramentos do WebView, suporte a navegação de teclado melhorado e uma nova API AAudio (pro audio) para áudio de baixa latência de alto desempenho, para obter mais informações sobre esses recursos, consulte o tópico Android [Android Oreo Features e APIs.](https://developer.android.com/about/versions/oreo/android-8.0.html)

## <a name="behavior-changes"></a>Alterações de comportamento

O Android Oreo inclui uma variedade de mudanças de comportamento de sistema e API que podem ter um impacto na funcionalidade dos aplicativos existentes. Essas mudanças são descritas da seguinte forma.

### <a name="background-execution-limits"></a>Limites de execução de fundo

Para melhorar a experiência do usuário, o Android Oreo impõe limitações sobre o que os aplicativos podem fazer durante a execução em segundo plano. Por exemplo, se o usuário estiver assistindo a um vídeo ou jogando um jogo, um aplicativo em execução em segundo plano pode prejudicar o desempenho de um aplicativo com uso intensivo de vídeo em primeiro plano. Como resultado, o Android Oreo coloca as seguintes restrições em aplicativos que não estão interagindo diretamente com o usuário:

1. **Limitações** &ndash; de serviço em segundo plano Quando um aplicativo está sendo executado em segundo plano, ele tem uma janela de vários minutos em que ainda é permitido criar e usar serviços. No final dessa janela, o Android interrompe o serviço de fundo do aplicativo e o trata como sendo _ocioso_.

2. **Limitações de transmissão** &ndash; O Android 7.0 (API 25) colocou limitações nas transmissões que um aplicativo registra para receber. O Android Oreo torna essas limitações mais rigorosas. Por exemplo, os aplicativos Android Oreo não podem mais registrar receptores de transmissão para transmissões implícitas em seus manifestos.

Para obter mais informações sobre os novos limites de execução em segundo plano, consulte o tópico [Limites de execução em segundo plano](https://developer.android.com/about/versions/oreo/background.html) do Desenvolvedor Android.

### <a name="breaking-changes"></a>Alterações de quebra

Aplicativos que visam o Android Oreo ou superior devem modificar seus aplicativos para suportar as seguintes alterações, quando aplicável:

- O Android Oreo deprecia a capacidade de definir a prioridade de notificações individuais. Em vez disso, você define um nível de importância recomendado ao criar um canal de notificação. O nível de importância atribuído a um canal de notificação se aplica a todas as mensagens de notificação que você postar para ele.

- Para aplicativos direcionados ao `PendingIntent.GetService()` Android Oreo, não funciona devido aos novos limites colocados nos serviços iniciados em segundo plano. Se você estiver mirando o Android Oreo, você deve usar [PendingIntent.GetBroadcast](xref:Android.App.PendingIntent.GetBroadcast*) em vez disso.  

## <a name="sample-code"></a>Exemplo de código

Várias amostras de Xamarin.Android estão disponíveis para mostrar como aproveitar os recursos do Android Oreo:

- [NotificaçõesCanais](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-notificationchannels) demonstram como usar o novo sistema de Canais de Notificação introduzido no Android Oreo. Esta amostra gerencia dois canais de notificações: um com importância padrão e outro com alta importância.

- [PictureInPicture](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-pictureinpicture) demonstra o uso básico do modo Picture-in-Picture (PiP) para dispositivos portáteis introduzidos no Oreo. A amostra reproduz um vídeo que continua ininterrupto enquanto alterna entre os modos de exibição ou outras atividades.

- [O AutofillFramework](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-autofillframework) demonstra o uso do Autofill Framework. Ele inclui implementações de atividades do cliente com visualizações que devem ser preenchidas automaticamente e um Serviço que pode fornecer dados de preenchimento automático às atividades do cliente.

- [Fontes para](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-downloadablefonts) download fornecem um exemplo de como usar o recurso Fontes para Download descrita anteriormente.

- [EmojiCompat](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-emojicompat) demonstra o uso da biblioteca de suporte emojiCompat. Você pode usar esta biblioteca para impedir que seu aplicativo mostre caracteres emoji ausentes como caracteres de "tofu".

- [Atualizações de localização A intenção pendente](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-androidplaylocation-locupdpendintent) ilustra o uso da API `PendingIntent`de localização para obter atualizações sobre a localização de um dispositivo usando um .

- [O Serviço de Primeiro Plano de Atualizações de Localização](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-o-androidplaylocation-locupdfgservice) demonstra como usar a API de localização para obter atualizações sobre a localização de um dispositivo usando um serviço de primeiro plano vinculado e iniciado.

## <a name="video"></a>Vídeo

> [!VIDEO https://youtube.com/embed/OuvEcaMO-Ho]

**Desenvolvimento do Android 8.0 Oreo com C #**

## <a name="summary"></a>Resumo

Este artigo introduziu o Android Oreo e explicou como instalar e configurar as ferramentas e pacotes mais recentes para o desenvolvimento do Xamarin.Android no Android Oreo. Ele forneceu uma visão geral dos principais recursos disponíveis no Android Oreo, com links para exemplo de código fonte para vários novos recursos. Ele incluiu links para documentação da API e tópicos do Android Developer para ajudá-lo a começar a criar aplicativos para Android Oreo. Ele também destacou as mudanças de comportamento mais importantes do Android Oreo que poderiam impactar os aplicativos existentes.

## <a name="related-links"></a>Links relacionados

- [Android 8.0 Oreo](https://developer.android.com/index.html)
