---
title: Recursos do nougat
description: Como começar a usar o Xamarin. Android para desenvolver aplicativos para Android nougat.
ms.prod: xamarin
ms.assetid: 5C74ABE2-C862-4ED0-8EA5-C7FEE5251D4B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/02/2018
ms.openlocfilehash: a28368e0fa4574fbb92a43dbd650a127008f5d06
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643458"
---
# <a name="nougat-features"></a>Recursos do nougat

_Como começar a usar o Xamarin. Android para desenvolver aplicativos para Android nougat._

Este artigo fornece uma descrição dos recursos introduzidos no Android nougat, explica como preparar o Xamarin. Android para desenvolvimento de nougat do Android e fornece links para aplicativos de exemplo que ilustram como usar os recursos nougat do Android no Aplicativos Xamarin. Android.


## <a name="overview"></a>Visão geral

O [Android nougat](https://developer.android.com/about/versions/nougat/android-7.0.html) é o acompanhamento do Google para Android 6,0 marshmallow. O xamarin. Android fornece suporte para **associações do Android 7. x** no Xamarin Android 7,0 e posterior. O Android nougat adiciona muitas APIs novas para os recursos do nougat descritos abaixo; essas APIs estão disponíveis para aplicativos Xamarin. Android quando você usa o Xamarin. Android 7,0.

[![Imagens do Hero de tablets Android e telefones que executam o Android nougat](nougat-images/android-n-hero-sml.png)](nougat-images/android-n-hero.png#lightbox)

Para obter mais informações sobre as APIs do Android 7. x, consulte [android 7,1 for Developers](https://developer.android.com/preview/api-overview.html).
Para obter uma lista de problemas conhecidos do Xamarin. Android 7,0, consulte as [notas de versão](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md).

O Android nougat fornece muitos recursos novos de interesse para desenvolvedores do Xamarin. Android. Esses recursos incluem:

-   **Suporte a várias janelas** &ndash; Esse aprimoramento possibilita que os usuários abram dois aplicativos na tela de uma vez.

-   **Aprimoramentos de notificação** O sistema de notificações reprojetado no Android nougat inclui um recurso de *resposta direta* que permite aos usuários responder rapidamente às mensagens de texto diretamente da interface do usuário de notificação. &ndash; Além disso, se seu aplicativo criar notificações para mensagens recebidas, o novo recurso de *notificações agrupadas* poderá agrupar notificações em conjunto como um único grupo quando mais de uma mensagem for recebida.

-   **Proteção de dados** &ndash; Esse recurso é um novo serviço do sistema que ajuda a reduzir o uso de dados da rede celular por aplicativos; ele dá aos usuários o controle sobre como os aplicativos usam dados da rede celular.

Além disso, o Android nougat traz muitos outros aprimoramentos de interesse para desenvolvedores de aplicativos, como um novo recurso de configuração de segurança de rede, doze em qualquer lugar, atestado de chave, novas APIs de configurações rápidas, suporte a várias localidades, APIs de ICU4J, aprimoramentos do WebView, acesso a recursos de linguagem Java 8, acesso ao diretório com escopo, uma API de ponteiro personalizada, suporte a plataforma VR, arquivos virtuais e otimizações de processamento em segundo plano.

Este artigo explica como começar a criar aplicativos com o Android nougat para experimentar os novos recursos e planejar a migração ou o trabalho de recursos para direcionar a nova plataforma nougat do Android.


## <a name="requirements"></a>Requisitos

O seguinte é necessário para usar os novos recursos do Android nougat em aplicativos baseados no Xamarin:

-   **Visual Studio ou Visual Studio para Mac** &ndash; Se você estiver usando o Visual Studio, a versão 4.2.0.628 ou posterior do ferramentas do Visual Studio para o Xamarin será necessária. Se você estiver usando Visual Studio para Mac, a versão 6.1.0 ou posterior de Visual Studio para Mac será necessária.

-   **Xamarin. Android** &ndash; xamarin. Android 7,0 ou posterior deve ser instalado e configurado com o Visual Studio ou Visual Studio para Mac.

-   O **SDK do Android** -SDK do Android 7,0 (API 24) ou posterior deve ser instalado por meio do gerenciador de SDK do Android.

-   **Java Developer Kit** O desenvolvimento do Xamarin Android 7,0 requer o [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior se você estiver desenvolvendo para o nível de API 24 ou superior (o JDK 8 também oferece suporte a níveis de API anteriores a 24). &ndash; A versão de 64 bits do JDK 8 será necessária se você estiver usando controles personalizados ou o Visualizador de formulários.

> [!IMPORTANT]
> O Xamarin.Android não é compatível com o JDK 9.

Observe que os aplicativos devem ser recriados com o Xamarin C6SR4 ou posterior para trabalhar de forma confiável com o Android nougat. Como o Android nougat pode vincular somente a [bibliotecas nativas fornecidas por NDK](https://developer.android.com/about/versions/nougat/android-7.0-changes.html), os aplicativos existentes usando bibliotecas como **mono. Data. sqlite. dll** podem falhar ao serem executados no Android nougat se não forem recriados corretamente.



## <a name="getting-started"></a>Guia de Introdução

Para começar a usar o Android nougat com o Xamarin. Android, você deve baixar e instalar as ferramentas e os pacotes de SDK mais recentes antes de criar um projeto nougat do Android:

1.  Instale as últimas atualizações do Xamarin. Android do Xamarin.

2.  Instale os pacotes e as ferramentas do **Android 7,0 (API 24)** ou posterior.

3.  Crie um novo projeto Xamarin. Android que tenha como alvo o Android nougat.

4.  Configurar um emulador ou dispositivo para nougat Android.

Cada uma dessas etapas é explicada nas seguintes seções:


### <a name="install-xamarin-updates"></a>Instalar atualizações do Xamarin

Para adicionar o suporte do Xamarin para Android nougat, altere o canal atualizações no Visual Studio ou Visual Studio para Mac para o canal estável e aplique as atualizações mais recentes. Se você também precisar de recursos atualmente disponíveis apenas no canal Alpha ou beta, poderá alternar para o canal Alpha ou beta (os canais alfa e beta também oferecem suporte para Android 7. x). Para obter informações sobre como alterar o canal de atualizações (releases), consulte [alterando o canal de atualizações](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel).



### <a name="install-the-android-sdk"></a>Instalar o SDK do Android

Para criar um projeto com o Xamarin Android 7,0, você deve primeiro usar o Gerenciador de SDK do Android para instalar a **plataforma SDK do Android N (API 24)** ou posterior. Você também deve instalar a **Android SDK Tools**mais recente:

1.  Inicie o Gerenciador de SDK do Android (em Visual Studio para Mac, use as **Ferramentas > abrir&hellip;SDK do Android Manager**; no Visual Studio, use **Ferramentas > Android > SDK do Android Manager**).

2.  Instale o **Android 7,0 (API 24)** ou posterior:

    [![Selecionando pacotes do Android 7,0 no Gerenciador de SDK do Android](nougat-images/preview-packages.png)](nougat-images/preview-packages.png#lightbox)

3.  Instale as ferramentas de SDK do Android mais recentes:

    [![Selecionando as ferramentas de SDK do Android mais recentes no Gerenciador de SDK do Android](nougat-images/preview-tools.png)](nougat-images/preview-tools.png#lightbox)

    Você deve instalar Android SDK Tools revisão 25.2.2 ou posterior, SDK do Android ferramentas de plataforma 24.0.3 ou posterior e SDK do Android ferramentas de Build 24.0.2 ou posterior.

4.  Verifique se o **local do Java Development Kit** está configurado para JDK 1,8:

    [![Configurando o caminho JDK 8 em ferramentas opções](nougat-images/use-jdk-1.8.png)](nougat-images/use-jdk-1.8.png#lightbox)

    Para exibir essa configuração no Visual Studio, clique em **ferramentas > opções > Xamarin > configurações do Android**. Em Visual Studio para Mac, clique em **preferências > projetos > locais do SDK > Android**.



### <a name="start-a-xamarinandroid-project"></a>Iniciar um projeto Xamarin. Android

Crie um novo projeto Xamarin. Android. Se você for novo no desenvolvimento do Android com o Xamarin, consulte [Olá, Android](~/android/get-started/hello-android/index.md) para saber mais sobre como criar projetos do Xamarin. Android.

Ao criar um projeto do Android, você deve definir as configurações de versão para o Android 7,0 ou posterior de destino. Por exemplo, para direcionar seu projeto para Android 7,0, você deve configurar o nível de API do Android de destino do seu projeto para **android 7,0 (API 24-nougat)** . É recomendável que você defina seu nível de estrutura de destino como API 24 ou posterior. Para saber mais sobre como configurar níveis de nível de API do Android, confira [noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md)


> [!NOTE]
> No momento, você deve definir a **versão mínima do Android** para **Android 7,0 (API 24-nougat)** para implantar seu aplicativo em dispositivos Android nougat ou emuladores.



### <a name="configure-an-emulator-or-device"></a>Configurar um emulador ou dispositivo

Se você estiver usando um emulador, inicie o Gerenciador de AVD do Android e crie um novo dispositivo usando as seguintes configurações:

-   Vice Nexus 5X, Nexus 6, Nexus 6P, Nexus Player, Nexus 9 ou pixel C.
-   Alvo Android 7,0 – nível de API 24
-   Abi: x86 ou x86\_64

Por exemplo, este dispositivo virtual está configurado para emular um Nexus 6:

[![Configurando um AVD usando o dispositivo Nexus 6, o destino do Android 7,0 e a CPU/ABI x86 do Intel Atom](nougat-images/android-n-avd.png)](nougat-images/android-n-avd.png#lightbox)

Se você estiver usando um dispositivo físico, como um Nexus 5X, 6 ou 9, poderá atualizar seu dispositivo por meio de atualizações automáticas por satélite (OTA) ou baixar uma imagem do sistema e atualizar seu dispositivo diretamente. Para obter mais informações sobre a atualização manual do dispositivo para o Android nougat, consulte [imagens OTA para dispositivos Nexus](https://developers.google.com/android/nexus/ota).

Observe que os dispositivos Nexus 5 não têm suporte do Android nougat.



## <a name="new-features"></a>Novos recursos

O Android nougat apresenta uma variedade de novos recursos e funcionalidades, como suporte a várias janelas, aprimoramentos de notificações e proteção de dados. As seções a seguir destacam esses recursos e fornecem links para ajudá-lo a começar a usá-los em seu aplicativo.



### <a name="multi-window-mode"></a>Modo de várias janelas

O modo de várias janelas possibilita que os usuários abram dois aplicativos de uma só vez com suporte completo a multitarefas. Esses aplicativos podem ser executados lado a lado (paisagem) ou um-acima-outro (retrato) no modo de tela de divisão.
Os usuários podem arrastar um divisor entre os aplicativos para redimensioná-los e podem recortar e colar o conteúdo entre os aplicativos. Quando dois aplicativos são apresentados no modo de várias janelas, a atividade selecionada continua a ser executada enquanto a atividade não selecionada está em pausa, mas ainda visível. O modo de várias janelas não modifica o ciclo de vida da atividade do Android.

[![Aplicativos de exemplo em execução no modo de várias janelas em retrato e paisagem](nougat-images/multi-window-mode.png)](nougat-images/multi-window-mode.png#lightbox)

Você pode configurar como as atividades do seu aplicativo Xamarin. Android dão suporte ao modo de várias janelas. Por exemplo, você pode configurar atributos que definem o tamanho mínimo e a altura e a largura padrão do seu aplicativo no modo de várias janelas. Você pode usar a nova `Activity.IsInMultiWindowMode` propriedade para determinar se sua atividade está no modo de várias janelas. Por exemplo:

```csharp
if (!IsInMultiWindowMode) {
    multiDisabledMessage.Visibility = ViewStates.Visible;
} else {
    multiDisabledMessage.Visibility = ViewStates.Gone;
}
```

O aplicativo de exemplo [MultiWindowPlayground](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-multiwindowplayground) inclui código C# que demonstra como tirar proveito de várias interfaces de usuário do Window com seu aplicativo.

Para obter mais informações sobre o modo de várias janelas, consulte [suporte a várias janelas](https://developer.android.com/guide/topics/ui/multi-window.html).



### <a name="enhanced-notifications"></a>Notificações aprimoradas

O Android nougat introduz um sistema de notificação reprojetado. Ele apresenta um novo recurso de resposta direta que possibilita aos usuários responder rapidamente às notificações de mensagens de texto de entrada diretamente na interface do usuário de notificação. A partir do Android 7,0, as mensagens de notificação podem ser agrupadas em conjunto como um único grupo quando mais de uma mensagem é recebida. Além disso, os desenvolvedores podem personalizar exibições de notificação, aproveitar as decorações do sistema em notificações e aproveitar os novos modelos de notificação ao gerar notificações.


#### <a name="direct-reply"></a>Resposta direta

Quando um usuário recebe uma notificação de mensagem de entrada, o Android nougat torna possível responder à mensagem dentro da notificação (em vez de abrir o aplicativo de mensagens para enviar uma resposta).
Esse recurso de resposta embutida possibilita que os usuários respondam rapidamente a uma mensagem SMS ou de texto diretamente na interface de notificação:

[![Captura de tela de uma notificação com um campo de resposta direta embutido](nougat-images/notifications-inline-reply-sml.png)](nougat-images/notifications-inline-reply.png#lightbox)

Para dar suporte a esse recurso em seu aplicativo, você deve adicionar *ações de resposta embutidas* ao seu aplicativo por meio de um objeto [RemoteInput](xref:Android.App.RemoteInput) para que os usuários possam responder via texto diretamente da interface do usuário de notificação.
Por exemplo, o código a seguir cria `RemoteInput` um para receber entrada de texto, cria uma tentativa pendente para a ação de resposta e cria uma ação habilitada para entrada remota:

```csharp
// Build a RemoteInput for receiving text input:
var remoteInput = new Android.Support.V4.App.RemoteInput.Builder (EXTRA_REMOTE_REPLY)
    .SetLabel (GetString (Resource.String.reply))
    .Build ();

// Build a Pending Intent for the reply action to trigger:
PendingIntent replyIntent = PendingIntent.GetBroadcast (ApplicationContext,
                                conversation.ConversationId,
                                GetMessageReplyIntent (conversation.ConversationId),
                                PendingIntentFlags.UpdateCurrent);

// Build an Android 7.0 compatible Remote Input enabled action:
NotificationCompat.Action actionReplyByRemoteInput = new NotificationCompat.Action.Builder (
    Resource.Drawable.notification_icon,
    GetString (Resource.String.reply),
    replyIntent).AddRemoteInput (remoteInput).Build ();
```

Esta ação é adicionada à notificação:

```csharp
// Create the notification:
NotificationCompat.Builder builder = new NotificationCompat.Builder (ApplicationContext)
   .SetSmallIcon (Resource.Drawable.notification_icon)
   ...
   .AddAction (actionReplyByRemoteInput);
```

O aplicativo de exemplo do [serviço de mensagens](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-messagingservice) inclui C# código que demonstra como estender notificações `RemoteInput` com um objeto. Para obter mais informações sobre como adicionar ações de resposta embutidas ao seu aplicativo para Android 7,0 ou posterior, consulte o tópico respondendo a [notificações](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#direct) do Android.


#### <a name="bundled-notifications"></a>Notificações agrupadas

O Android nougat pode agrupar mensagens de notificação em conjunto (por exemplo, por tópico de mensagem) e exibir o grupo em vez de cada mensagem separada.
Esse recurso de *notificações agrupadas* possibilita que os usuários descartam ou arquivem um grupo de notificações em uma ação. O usuário pode deslizar para expandir o pacote de notificações para exibir cada notificação em detalhes:

[![Exemplo de captura de tela de notificações agrupadas](nougat-images/bundled-notifications-sml.png)](nougat-images/bundled-notifications.png#lightbox)

Para dar suporte a notificações agrupadas, seu aplicativo pode usar o método [Builder. SetGroup](xref:Android.App.Notification.Builder.SetGroup*) para agrupar notificações semelhantes. Para obter mais informações sobre grupos de notificação agrupados no Android N, consulte o tópico [notificações de agrupamento](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#bundle) do Android.


#### <a name="custom-views"></a>Exibições personalizadas

O Android nougat possibilita que você crie exibições de notificação personalizadas com cabeçalhos de notificação do sistema, ações e layouts expansíveis. Para obter mais informações sobre exibições de notificação personalizadas no Android nougat, consulte o tópico aprimoramentos de [notificação](https://developer.android.com/about/versions/nougat/android-7.0.html#notification_enhancements) do Android.



### <a name="data-saver"></a>Proteção de dados

A partir do Android nougat, os usuários podem habilitar uma nova configuração de *economia de dados* que bloqueia o uso de dados em segundo plano. Essa configuração também sinaliza seu aplicativo para usar menos dados em primeiro plano sempre que possível. O [connectivitymanager](xref:Android.Net.ConnectivityManager) foi estendido no Android nougat para que seu aplicativo possa verificar se o usuário habilitou a proteção de dados para que seu aplicativo possa tomar um esforço para limitar seu uso de dados quando a proteção de dados estiver habilitada.

Para obter mais informações sobre o novo recurso de proteção de dados no Android nougat, consulte o tópico otimizando o [uso de dados de rede](https://developer.android.com/training/basics/network-ops/data-saver.html) do Android.



### <a name="app-shortcuts"></a>Atalhos de aplicativo

O Android 7,1 introduziu um recurso de *atalhos de aplicativo* que possibilita aos usuários iniciar rapidamente tarefas comuns ou recomendadas com seu aplicativo.
Para ativar o menu de atalhos, o usuário tem um pressionamento longo do ícone do aplicativo por um segundo &ndash; ou mais o menu é exibido com uma vibração rápida.
A liberação da prensa faz com que o menu permaneça:

[![Tela de exemplo de um menu de atalho do aplicativo para um aplicativo de mensagens](nougat-images/app-shortcuts-sml.png)](nougat-images/app-shortcuts.png#lightbox)

Este recurso está disponível somente para o nível de API 25 ou superior.
Para obter mais informações sobre o novo recurso de atalhos de aplicativo no Android 7,1, consulte o tópico de [atalhos do aplicativo](https://developer.android.com/guide/topics/ui/shortcuts.html) Android.


### <a name="sample-code"></a>Código de exemplo

Vários exemplos do Xamarin. Android estão disponíveis para mostrar como aproveitar os recursos do nougat do Android:

-   [MultiWindowPlayground](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-multiwindowplayground) demonstra o uso da API de várias janelas disponível no Android nougat. Você pode alternar o aplicativo de exemplo para o modo multiwindows para ver como ele afeta o ciclo de vida e o comportamento do aplicativo.

-   O [serviço de mensagens](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-messagingservice) é um serviço simples que envia notificações `NotificationCompatManager`usando o. Ele também estende a notificação com um `RemoteInput` objeto para permitir que dispositivos Android nougat respondam por texto diretamente da notificação sem precisar abrir um aplicativo.

-   [Notificações ativas](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-activenotifications) demonstra como usar a `NotificationManager` API para informar quantas notificações seu aplicativo está exibindo no momento.

-   [Acesso ao diretório com escopo](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-scopeddirectoryaccess) Demonstra como usar a API de acesso ao diretório com escopo para acessar facilmente diretórios específicos. Isso serve como uma alternativa para ter que definir `READ_EXTERNAL_STORAGE` ou `WRITE_EXTERNAL_STORAGE` permissões em seu manifesto.

-   [Inicialização direta](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-directboot) Ilustra como armazenar dados em um armazenamento criptografado por dispositivo que está sempre disponível enquanto o dispositivo é inicializado antes e depois que qualquer credencial do usuário (PIN/padrão/senha) é inserida.


## <a name="summary"></a>Resumo

Este artigo introduziu o Android nougat e explicou como instalar e configurar as ferramentas e os pacotes mais recentes para o desenvolvimento do Xamarin. Android no Android nougat. Ele também forneceu uma visão geral dos principais recursos disponíveis no Android nougat, com links para código-fonte de exemplo para ajudá-lo a começar a criar aplicativos para nougat do Android.


## <a name="related-links"></a>Links relacionados

- [Android 7,1 para desenvolvedores](https://developer.android.com/about/versions/nougat/android-7.1.html)
- [Notas de versão do Xamarin Android 7,0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md)
