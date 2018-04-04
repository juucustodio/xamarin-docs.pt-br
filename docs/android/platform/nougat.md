---
title: Recursos de nougat
description: Como começar a usar o xamarin para desenvolver aplicativos para Android Nougat.
ms.prod: xamarin
ms.assetid: 5C74ABE2-C862-4ED0-8EA5-C7FEE5251D4B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: fe544f8ac677987f8921ccb1c11b8930811b9553
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="nougat-features"></a>Recursos de nougat

_Como começar a usar o xamarin para desenvolver aplicativos para Android Nougat._

Este artigo fornece uma estrutura de tópicos dos recursos introduzidos no Android Nougat, explica como preparar o xamarin para desenvolvimento Android Nougat e fornece links para aplicativos de exemplo que ilustram como usar os recursos de Nougat Android em Aplicativos xamarin.


## <a name="overview"></a>Visão geral

[Android Nougat](https://developer.android.com/about/versions/nougat/android-7.0.html) é o acompanhamento do Google para Android Marshmallow 6.0. Xamarin dá suporte para **Android 7. x associações** no Xamarin Android 7.0 e posterior. Android Nougat adiciona muitas novas APIs para os recursos de Nougat descritos abaixo; Essas APIs estão disponíveis para aplicativos xamarin quando você usar xamarin 7.0.

[![Imagens herói de tablets Android e telefones Android Nougat de execução](nougat-images/android-n-hero-sml.png)](nougat-images/android-n-hero.png#lightbox)

Para obter mais informações sobre Android 7. x APIs, consulte [7.1 Android para desenvolvedores](http://developer.android.com/preview/api-overview.html).
Para obter uma lista dos problemas conhecidos do xamarin 7.0, consulte o [notas de versão](https://developer.xamarin.com/releases/android/xamarin.android_7/xamarin.android_7.0/).

Android Nougat fornece muitos recursos novos de interesse para os desenvolvedores do xamarin. Esses recursos incluem:

-   **Suporte a várias janela** &ndash; esse aprimoramento possibilita que os usuários abram dois aplicativos na tela de uma só vez.

-   **Aprimoramentos de notificação** &ndash; o sistema notificações reprojetado no Android Nougat inclui um *resposta direta* recursos que permitem aos usuários responder rapidamente às mensagens de texto diretamente da notificação de INTERFACE DO USUÁRIO. Além disso, se seu aplicativo cria as notificações recebidas mensagens, o novo *agrupados notificações* recurso pode agrupar notificações juntos como um único grupo quando mais de uma mensagem é recebida.

-   **Proteção de dados** &ndash; esse recurso é um novo serviço de sistema que ajuda a reduzir o uso de dados de celular, aplicativos; ele fornece aos usuários controle sobre como os aplicativos usam dados de celular.

Além disso, o Android Nougat traz muitos outros aprimoramentos de interesse para os desenvolvedores de aplicativos como um novo recurso de configuração de segurança de rede, Doze viajando, principais Atestado, APIs de configurações novas rápida, suporte a vários locais, APIs de ICU4J WebView aperfeiçoamentos, acesso aos recursos da linguagem Java 8, acesso ao diretório no escopo, uma API personalizada do ponteiro, suporte a plataformas VR, arquivos virtuais e otimizações de processamento em segundo plano.

Este artigo explica como começar a criar aplicativos com Android Nougat para experimentar os novos recursos e planejar o trabalho de migração ou recurso para direcionar a plataforma Android Nougat novo.


## <a name="requirements"></a>Requisitos

O seguinte é necessário para usar os novos recursos do Android Nougat em aplicativos baseados em Xamarin:

-   **O Visual Studio ou o Visual Studio para Mac** &ndash; se você estiver usando o Visual Studio, versão 4.2.0.628 ou posterior do Xamarin para Visual Studio é necessário. Se você estiver usando o Visual Studio para Mac, versão 6.1.0 ou posterior do Visual Studio para Mac é necessário.

-   **Xamarin** &ndash; xamarin 7.0 ou posterior deve ser instalado e configurado com o Visual Studio ou Visual Studio para Mac.

-   **SDK do Android** -Android SDK 7.0 (API 24) ou posterior deve ser instalado por meio do Gerenciador de SDK do Android.

-   **Java Developer Kit** &ndash; requer o desenvolvimento do Xamarin Android 7.0 [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior se você estiver desenvolvendo para o nível de API 24 ou superior (JDK 8 também dá suporte a API níveis anteriores a 24). A versão de 64 bits do JDK 8 é necessária se você estiver usando controles personalizados ou o Visualizador de formulários.

> [!IMPORTANT]
> O Xamarin.Android não é compatível com o JDK 9.

Observe que aplicativos devem ser recriados com Xamarin C6SR4 ou posterior para funcionam de forma confiável com Nougat Android. Porque Nougat Android pode vincular somente a [bibliotecas nativas fornecido NDK](https://developer.android.com/about/versions/nougat/android-7.0-changes.html), os aplicativos existentes usando bibliotecas como **Mono.Data.Sqlite.dll** pode falhar quando executados em Android Nougat se eles não estão corretamente recriado.



## <a name="getting-started"></a>Guia de Introdução

Para começar a usar o Android Nougat com xamarin, você deve baixar e instalar as ferramentas mais recentes e os pacotes SDK antes de criar um projeto Android Nougat:

1.  Instale as atualizações mais recentes do xamarin a partir do Xamarin.

2.  Instalar o **Android 7.0 (API 24)** pacotes e as ferramentas ou posterior.

3.  Crie um novo projeto de xamarin que tem como destino Nougat Android.

4.  Configure um dispositivo ou emulador para Nougat Android.

Cada uma dessas etapas é explicada nas seções a seguir:


### <a name="install-xamarin-updates"></a>Instalar atualizações do Xamarin

Para adicionar suporte de Xamarin para Nougat Android, altere o canal de atualizações no Visual Studio ou Visual Studio para Mac para o canal estável e aplicar as atualizações mais recentes. Se você precisar de recursos que estão atualmente disponíveis apenas no canal alfa ou Beta também, você pode alternar para o canal alfa ou Beta (os canais alfa e Beta também oferecem suporte para Android 7. x). Para obter informações sobre como alterar o canal de atualizações (versões), consulte [mudar o canal atualizações](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/).



### <a name="install-the-android-sdk"></a>Instalar o SDK do Android

Para criar um projeto com Xamarin Android 7.0, primeiro você deve usar o Gerenciador de SDK do Android para instalar **SDK de plataforma Android N (API 24)** ou posterior. Você também deve instalar a versão mais recente **ferramentas do SDK do Android**:

1.  Inicie o Gerenciador de SDK do Android (no Visual Studio para Mac, use **Ferramentas > Abrir o Gerenciador de SDK do Android&hellip;**; no Visual Studio, use **Ferramentas > Android > Gerenciador de SDK do Android**).

2.  Instalar **Android 7.0 (API 24)** ou posterior:

    [![Selecionar pacotes do Android 7.0 no Gerenciador de SDK do Android](nougat-images/preview-packages.png)](nougat-images/preview-packages.png#lightbox)

3.  Instale as ferramentas mais recentes do SDK do Android:

    [![Selecionando as ferramentas mais recentes do SDK do Android no Gerenciador de SDK do Android](nougat-images/preview-tools.png)](nougat-images/preview-tools.png#lightbox)

    Você deve instalar a revisão de ferramentas do Android SDK 25.2.2 ou posteriores, Android ferramentas de plataforma SDK 24.0.3 ou ferramentas de compilação do SDK do Android e posteriores 24.0.2 ou posterior.

4.  Verifique o **Java Development Kit local** está configurado para o JDK 1.8:

    [![Configurando o caminho do JDK 8 em Opções de ferramentas](nougat-images/use-jdk-1.8.png)](nougat-images/use-jdk-1.8.png#lightbox)

    Para exibir essa configuração no Visual Studio, clique em **Ferramentas > Opções > Xamarin > configurações do Android**. No Visual Studio para Mac, clique em **Preferências > projetos > locais SDK > Android**.



### <a name="start-a-xamarinandroid-project"></a>Iniciar um projeto do xamarin

Crie um novo projeto de xamarin. Se você for novo no desenvolvimento do Android com o Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para saber mais sobre a criação de projetos do xamarin.

Quando você cria um projeto Android, você deve configurar as configurações de versão para o destino Android 7.0 ou posterior. Por exemplo, para direcionar seu projeto para Android 7.0, você deve configurar o nível de API do Android do destino do seu projeto para **Android 7.0 (API de 24 - Nougat)**. É recomendável que você defina o nível de estrutura de destino para a API 24 ou posterior. Para obter mais informações sobre como configurar níveis de nível de API do Android, consulte [Noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).


> [!NOTE]
> No momento, você deve definir o **versão do Android mínimo** para **Android 7.0 (API de 24 - Nougat)** para implantar seu aplicativo para dispositivos Android Nougat ou emuladores.



### <a name="configure-an-emulator-or-device"></a>Configurar um dispositivo ou emulador

Se você estiver usando um emulador, inicie o Gerenciador de AVD do Android e criar um novo dispositivo com as seguintes configurações:

-   Dispositivo: Nexus 5 X Nexus 6, Nexus 6P, Nexus Player, Nexus 9 ou Pixel C.
-   Destino: Android 7.0 - 24 de nível de API
-   ABI: x86 ou x86\_64

Por exemplo, este dispositivo virtual está configurado para emular uma Nexus 6:

[![Configurando um AVD usando dispositivo Nexus 6, destino do Android 7.0 e CPU/ABI Intel Atom x86](nougat-images/android-n-avd.png)](nougat-images/android-n-avd.png#lightbox)

Se você estiver usando um dispositivo físico, como uma ligação de X de 5, 6 ou 9, você pode atualizar seu dispositivo por meio de automático sobre as atualizações de ondas de rádio ou baixar uma imagem do sistema e flash seu dispositivo diretamente. Para obter mais informações sobre como atualizar manualmente seu dispositivo para Nougat Android, consulte [OTA imagens para dispositivos do Nexus](https://developers.google.com/android/nexus/ota).

Observe que não há suporte para dispositivos Nexus 5 por Nougat Android.



## <a name="new-features"></a>Novos recursos

Nougat Android apresenta uma variedade de novos recursos e funcionalidades, como suporte a várias janelas, aprimoramentos de notificações e proteção de dados. As seções a seguir destacam esses recursos e fornecem links para ajudá-lo a começar a usá-las em seu aplicativo.



### <a name="multi-window-mode"></a>Modo de várias janela

Modo de várias janela possibilita aos usuários abrir dois aplicativos de uma vez com suporte de multitarefa completo. Esses aplicativos podem executar lado a lado (paisagem) ou um-acima-a-outra (retrato) no modo de tela de divisão.
Os usuários podem arrastar um divisor entre os aplicativos para redimensioná-las, e eles podem recortar e colar o conteúdo de entre aplicativos. Quando dois aplicativos são apresentados no modo de várias janela, a atividade selecionada continua em execução enquanto a atividade não selecionada está em pausa, mas ainda estarão visíveis. Modo de várias janela não modifica o ciclo de vida da atividade do Android.

[![Aplicativos de exemplo em execução no modo de várias janela em Retrato e paisagem](nougat-images/multi-window-mode.png)](nougat-images/multi-window-mode.png#lightbox)

Você pode configurar como as atividades do seu aplicativo xamarin dão suporte ao modo de várias janela. Por exemplo, você pode configurar atributos que defina o tamanho mínimo e a altura padrão e a largura do seu aplicativo no modo de várias janela. Você pode usar o novo `Activity.IsInMultiWindowMode` propriedade para determinar se a atividade está no modo de várias janela. Por exemplo:

```csharp
if (!IsInMultiWindowMode) {
    multiDisabledMessage.Visibility = ViewStates.Visible;
} else {
    multiDisabledMessage.Visibility = ViewStates.Gone;
}
```

O [MultiWindowPlayground](https://developer.xamarin.com/samples/monodroid/android-n/MultiWindowPlayground/) aplicativo de exemplo inclui código em c# que demonstra como tirar proveito da janela várias interfaces de usuário com seu aplicativo.

Para obter mais informações sobre o modo de várias janela, consulte o [suporte a várias janelas](https://developer.android.com/guide/topics/ui/multi-window.html).



### <a name="enhanced-notifications"></a>Notificações aprimoradas

Nougat Android apresenta um sistema de notificação redesenhada. Ele apresenta um novo recurso de resposta direta que possibilita aos usuários rapidamente resposta notificações para mensagens de texto de entrada diretamente na notificação da interface do usuário. Iniciando com o Android 7.0, as mensagens podem ser agrupadas como um único grupo quando mais de uma mensagem é recebida de notificação. Além disso, os desenvolvedores podem personalizar a notificação de modos de exibição, aproveitar decorações de sistema em notificações e tirar proveito dos novos modelos de notificação ao gerar notificações.


#### <a name="direct-reply"></a>Resposta direta

Quando um usuário recebe uma notificação de mensagem de entrada, Nougat Android torna possível responder à mensagem de dentro da notificação (em vez de abrir o aplicativo de mensagens para enviar uma resposta).
Esse recurso de resposta embutida possibilita que os usuários de responder rapidamente a uma mensagem SMS ou texto diretamente dentro da interface de notificação:

[![Captura de tela de uma notificação com um campo de resposta direta embutido](nougat-images/notifications-inline-reply-sml.png)](nougat-images/notifications-inline-reply.png#lightbox)

Para dar suporte a esse recurso em seu aplicativo, você deve adicionar *ações de resposta embutida* para seu aplicativo por meio de um [RemoteInput](https://developer.xamarin.com/api/type/Android.App.RemoteInput/) para que os usuários podem responder por meio de texto diretamente da notificação de interface do usuário do objeto.
Por exemplo, o código a seguir compilações um `RemoteInput` para receber entrada de texto, cria uma intenção pendente para a ação de resposta e cria uma ação de habilitado entrada remota:

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

O [serviço de mensagens](https://developer.xamarin.com/samples/monodroid/android-n/MessagingService/) aplicativo de exemplo inclui código em c# que demonstra como estender as notificações com um `RemoteInput` objeto. Para obter mais informações sobre como adicionar embutido resposta ações ao seu aplicativo para Android 7.0 ou posterior, consulte o Android [resposta a notificações](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#direct) tópico.


#### <a name="bundled-notifications"></a>Pacote de notificações

Android Nougat pode agrupar mensagens de notificação (por exemplo, ao tópico de mensagem) e exibir o grupo em vez de cada mensagem separada.
Isso *agrupados notificações* recurso torna possível para os usuários ignorar ou arquivar um grupo de notificações em uma ação. O usuário pode deslizar para baixo para expandir o pacote de notificações para exibir cada notificação em detalhes:

[![Captura de tela exemplo de notificações agrupadas](nougat-images/bundled-notifications-sml.png)](nougat-images/bundled-notifications.png#lightbox)

Para dar suporte a notificações agrupadas, seu aplicativo pode usar o [Builder.SetGroup](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetGroup/p/System.String/) método agrupar notificações semelhantes. Para obter mais informações sobre grupos de notificação agrupadas no Android N, consulte o Android [agrupamento notificações](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#bundle) tópico.


#### <a name="custom-views"></a>Modos de exibição personalizados

Android Nougat possibilita criar modos de exibição de notificação personalizada com layouts expansíveis, ações e os cabeçalhos de notificação do sistema. Para obter mais informações sobre modos de exibição de notificação personalizada no Android Nougat, consulte o Android [aprimoramentos de notificação](https://developer.android.com/about/versions/nougat/android-7.0.html#notification_enhancements) tópico.



### <a name="data-saver"></a>Proteção de dados

A partir do Android Nougat, os usuários podem habilitar uma nova *proteção de dados* blocos em segundo plano uso de dados de configuração. Essa configuração também sinaliza o aplicativo para usar menos dados no primeiro plano, sempre que possível. O [ConnectivityManager](https://developer.xamarin.com/api/type/Android.Net.ConnectivityManager/) foi estendido no Android Nougat para que seu aplicativo possa verificar se o usuário tiver habilitado a proteção de dados para que seu aplicativo possa fazer um esforço para limitar o uso de dados quando a proteção de dados está habilitada.

Para obter mais informações sobre o novo recurso de proteção de dados no Android Nougat, consulte o Android [otimizar o uso de dados de rede](https://developer.android.com/training/basics/network-ops/data-saver.html) tópico.



### <a name="app-shortcuts"></a>Atalhos de aplicativos

7.1 Android introduzida um *aplicativo atalhos* recurso que possibilita aos usuários rapidamente iniciar tarefas comuns ou recomendado ao seu aplicativo.
Para ativar o menu de atalhos, o usuário longa-pressiona o ícone do aplicativo para um segundo ou mais &ndash; menu é exibido com uma rápida vibração.
Liberar o pressionamento faz com que o menu:

[![Tela de exemplo de um menu de atalho do aplicativo para um aplicativo de mensagens](nougat-images/app-shortcuts-sml.png)](nougat-images/app-shortcuts.png#lightbox)

Este recurso está disponível somente nível de API 25 ou maior.
Para obter mais informações sobre o novo recurso de atalhos do aplicativo do Android 7.1, consulte o Android [aplicativo atalhos](https://developer.android.com/guide/topics/ui/shortcuts.html) tópico.


### <a name="sample-code"></a>Código de exemplo

Vários exemplos de xamarin estão disponíveis para mostrar como tirar proveito dos recursos de Nougat Android:

-   [MultiWindowPlayground](https://developer.xamarin.com/samples/monodroid/android-n/MultiWindowPlayground/) demonstra o uso da API disponível no Android Nougat várias janela. Você pode alternar o aplicativo de exemplo no modo multi-windows para ver como ele afeta o comportamento e o ciclo de vida do aplicativo.

-   [Serviço de mensagens](https://developer.xamarin.com/samples/monodroid/android-n/MessagingService/) é um serviço simples que envia notificações usando o `NotificationCompatManager`. Ele também estende a notificação com um `RemoteInput` objeto para permitir que dispositivos Android Nougat resposta por meio de texto diretamente de notificação sem a necessidade de abrir um aplicativo.

-   [Notificações ativas](https://developer.xamarin.com/samples/monodroid/android-n/ActiveNotifications/) demonstra como usar o `NotificationManager` API para informar quantas notificações que seu aplicativo está exibindo no momento.

-   [No escopo de acesso ao diretório](https://developer.xamarin.com/samples/monodroid/android-n/ScopedDirectoryAccess/) demonstra como usar a API de acesso de diretório com escopo para acessar facilmente os diretórios específicos. Isso serve como uma alternativa a necessidade de definir `READ_EXTERNAL_STORAGE` ou `WRITE_EXTERNAL_STORAGE` permissões em seu manifesto.

-   [Direcionar inicialização](https://developer.xamarin.com/samples/monodroid/android-n/DirectBoot/) ilustra como armazenar dados em um armazenamento criptografado de dispositivo que está sempre disponível enquanto o dispositivo é inicializado ambos antes e depois que qualquer usuário credentials(PIN/Pattern/Password) são inseridos.


## <a name="summary"></a>Resumo

Este artigo introduzido Nougat Android e explicou como instalar e configurar as ferramentas mais recentes e os pacotes para o desenvolvimento do xamarin em Nougat Android. Também forneceu uma visão geral dos principais recursos disponíveis no Android Nougat, com links para o código de exemplo para ajudá-lo a começar a criação de aplicativos para Android Nougat.


## <a name="related-links"></a>Links relacionados

- [7.1 Android para desenvolvedores](https://developer.android.com/about/versions/nougat/android-7.1.html)
- [Notas de versão do Xamarin Android 7.0](https://developer.xamarin.com/releases/android/xamarin.android_7/xamarin.android_7.0/)
