---
title: Recursos de nougat
description: Como começar a usar xamarin. Android para desenvolver aplicativos para Android Nougat.
ms.prod: xamarin
ms.assetid: 5C74ABE2-C862-4ED0-8EA5-C7FEE5251D4B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/02/2018
ms.openlocfilehash: 2e15de944f05fede802dbf52987d80a46fb890ef
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242147"
---
# <a name="nougat-features"></a>Recursos de nougat

_Como começar a usar xamarin. Android para desenvolver aplicativos para Android Nougat._

Este artigo fornece uma estrutura de tópicos dos recursos introduzidos no Android Nougat, explica como preparar o xamarin. Android para desenvolvimento Android Nougat e fornece links para aplicativos de exemplo que ilustram como usar recursos do Android Nougat no Aplicativos xamarin. Android.


## <a name="overview"></a>Visão geral

[Android Nougat](https://developer.android.com/about/versions/nougat/android-7.0.html) é o acompanhamento do Google para o Android Marshmallow 6.0. Xamarin. Android dá suporte para **Android 7.x associações** no Xamarin Android 7.0 e versões posteriores. Android Nougat adiciona várias APIs novas para os recursos de Nougat descritos abaixo; Essas APIs estão disponíveis para aplicativos xamarin. Android quando você usar o xamarin. Android 7.0.

[![Imagens de Hero de tablets Android e telefones executando o Android Nougat](nougat-images/android-n-hero-sml.png)](nougat-images/android-n-hero.png#lightbox)

Para obter mais informações sobre o Android 7.x APIs, consulte [Android 7.1 para desenvolvedores](http://developer.android.com/preview/api-overview.html).
Para obter uma lista dos problemas conhecidos do xamarin. Android 7.0, consulte o [notas de versão](https://developer.xamarin.com/releases/android/xamarin.android_7/xamarin.android_7.0/).

Android Nougat oferece muitos novos recursos de interesse para desenvolvedores do xamarin. Android. Esses recursos incluem:

-   **Suporte a várias janela** &ndash; essa melhoria possibilita aos usuários abrir dois aplicativos na tela ao mesmo tempo.

-   **Aprimoramentos de notificação** &ndash; o sistema de notificações reprojetado no Android Nougat inclui um *resposta direta* recursos que permitem aos usuários responder rapidamente às mensagens de texto diretamente na notificação INTERFACE DO USUÁRIO. Além disso, se seu aplicativo cria notificações para recebeu mensagens, a nova *agrupado notificações* recurso pode agrupar as notificações como um único grupo quando mais de uma mensagem é recebida.

-   **Proteção de dados** &ndash; esse recurso é um novo serviço de sistema que ajuda a reduzir o uso de dados de celular, aplicativos; ele fornece aos usuários controle sobre como os aplicativos usam dados de celular.

Além disso, o Android Nougat traz muitos outros aprimoramentos de interesse para desenvolvedores de aplicativos, como um novo recurso de configuração de segurança de rede, Doze em qualquer lugar, os principais aprimoramentos de WebView do Atestado, APIs de configurações do novo rápido, suporte a vários locais, APIs de ICU4J, acesso aos recursos da linguagem Java 8, acesso ao diretório no escopo, uma API personalizada de ponteiro, o suporte à plataforma de VR, arquivos virtuais e otimizações de processamento em segundo plano.

Este artigo explica como começar a criar aplicativos com o Android Nougat para experimentar os novos recursos e planejar o trabalho de migração ou recurso para a nova plataforma Android Nougat de destino.


## <a name="requirements"></a>Requisitos

A seguir é necessário para usar os novos recursos do Android Nougat em aplicativos baseados em Xamarin:

-   **Visual Studio ou Visual Studio para Mac** &ndash; se você estiver usando o Visual Studio, versão 4.2.0.628 ou posterior do Visual Studio Tools para o Xamarin é necessária. Se você estiver usando o Visual Studio para Mac, versão 6.1.0 ou posterior do Visual Studio para Mac é necessária.

-   **Xamarin. Android** &ndash; xamarin. Android 7.0 ou posterior deve ser instalado e configurado com o Visual Studio ou Visual Studio para Mac.

-   **SDK do Android** -7.0 do SDK do Android (API 24) ou posterior deve ser instalado por meio do Gerenciador de SDK do Android.

-   **Java Developer Kit** &ndash; desenvolvimento do Xamarin Android 7.0 requer [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior, se você estiver desenvolvendo para o nível de API 24 ou superior (JDK 8 também dá suporte a níveis de API anteriores ao 24). A versão de 64 bits do JDK 8 é necessária se você estiver usando controles personalizados ou o pré-visualizador de formulários.

> [!IMPORTANT]
> O Xamarin.Android não é compatível com o JDK 9.

Observe que aplicativos devem ser recriados com Xamarin C6SR4 ou posterior para trabalhar de forma confiável com o Android Nougat. Porque o Android Nougat pode vincular somente ao [fornecido pelo NDK bibliotecas nativas](https://developer.android.com/about/versions/nougat/android-7.0-changes.html), os aplicativos existentes usando bibliotecas como **Mono.Data.Sqlite.dll** pode falhar quando executados em Android Nougat se eles não forem corretamente reconstruído.



## <a name="getting-started"></a>Guia de Introdução

Para começar a usar o Android Nougat com xamarin. Android, você deve baixar e instalar os pacotes do SDK e ferramentas mais recentes antes de criar um projeto do Android Nougat:

1.  Instale as atualizações mais recentes do xamarin. Android do Xamarin.

2.  Instalar o **Android 7.0 (API 24)** pacotes e ferramentas ou posterior.

3.  Crie um novo projeto do xamarin. Android que tem como alvo o Android Nougat.

4.  Configure um dispositivo ou emulador para Android Nougat.

Cada uma dessas etapas é explicada nas seções a seguir:


### <a name="install-xamarin-updates"></a>Instalar atualizações do Xamarin

Para adicionar suporte ao Xamarin para Android Nougat, alterar o canal de atualizações no Visual Studio ou Visual Studio para Mac para o canal estável e aplique as atualizações mais recentes. Se você também precisa de recursos que estão atualmente disponíveis apenas no canal alfa ou Beta, você pode alternar para o canal alfa ou Beta (os canais alfa e Beta também fornecem suporte para Android 7. x). Para obter informações sobre como alterar o canal de atualizações (versões), consulte [alterar o canal de atualizações](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel).



### <a name="install-the-android-sdk"></a>Instalar o SDK do Android

Para criar um projeto com o Xamarin Android 7.0, primeiro você deve usar o Gerenciador de SDK do Android para instalar **plataforma SDK do Android N (API 24)** ou posterior. Você também deve instalar a versão mais recente **do Android SDK Tools**:

1.  Inicie o Gerenciador de SDK do Android (no Visual Studio para Mac, use **Ferramentas > Abrir Gerenciador de SDK do Android&hellip;**; no Visual Studio, use **Ferramentas > Android > Gerenciador de SDK do Android**).

2.  Instale **Android 7.0 (API 24)** ou posterior:

    [![Selecionando pacotes Android 7.0 no Gerenciador de SDK do Android](nougat-images/preview-packages.png)](nougat-images/preview-packages.png#lightbox)

3.  Instale as ferramentas mais recentes do SDK do Android:

    [![Selecionando as ferramentas mais recentes do SDK do Android no Gerenciador de SDK do Android](nougat-images/preview-tools.png)](nougat-images/preview-tools.png#lightbox)

    Você deve instalar a revisão do Android SDK Tools 25.2.2 ou posteriores, o Android SDK Platform tools 24.0.3 ou posteriores e Android SDK Build tools 24.0.2 ou posterior.

4.  Verifique se que o **local do Java Development Kit** está configurado para o JDK 1.8:

    [![Configurando o caminho do JDK 8 em Opções de ferramentas](nougat-images/use-jdk-1.8.png)](nougat-images/use-jdk-1.8.png#lightbox)

    Para exibir essa configuração no Visual Studio, clique em **Ferramentas > Opções > Xamarin > configurações do Android**. No Visual Studio para Mac, clique em **Preferências > projetos > locais do SDK > Android**.



### <a name="start-a-xamarinandroid-project"></a>Iniciar um projeto xamarin. Android

Crie um novo projeto do xamarin. Android. Se você for novo no desenvolvimento de Android com Xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para saber mais sobre como criar projetos do xamarin. Android.

Quando você cria um projeto do Android, você deve configurar as configurações de versão destino Android 7.0 ou posterior. Por exemplo, para direcionar seu projeto para o Android 7.0, você deve configurar o nível de API do Android de destino do seu projeto para **Android 7.0 (API 24 – Nougat)**. É recomendável que você defina o nível de framework de destino para a API 24 ou posterior. Para obter mais informações sobre como configurar níveis de nível de API do Android, consulte [Noções básicas sobre níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).


> [!NOTE]
> No momento, você deve definir a **versão mínima do Android** à **Android 7.0 (API 24 – Nougat)** para implantar seu aplicativo no Android Nougat dispositivos ou emuladores.



### <a name="configure-an-emulator-or-device"></a>Configurar um dispositivo ou emulador

Se você estiver usando um emulador, inicie o Gerenciador de AVD do Android e criar um novo dispositivo usando as seguintes configurações:

-   Dispositivo: Nexus 5 X, Nexus 6, Nexus 6P, Nexus Player, Nexus 9 ou Pixel C.
-   Destino: Android 7.0 – nível de API 24
-   ABI: x86 ou x86\_64

Por exemplo, este dispositivo virtual é configurado para emular um Nexus 6:

[![Configurando um AVD usando dispositivos Nexus 6, o destino do Android 7.0 e o Intel Atom x86 CPU/ABI](nougat-images/android-n-avd.png)](nougat-images/android-n-avd.png#lightbox)

Se você estiver usando um dispositivo físico, como um Nexus 5x, 6 ou 9, você pode atualizar seu dispositivo por meio de automático sobre as atualizações de ar (OTA) ou baixar uma imagem do sistema e seu dispositivo flash diretamente. Para obter mais informações sobre como atualizar manualmente seu dispositivo para o Android Nougat, consulte [OTA imagens para dispositivos Nexus](https://developers.google.com/android/nexus/ota).

Observe que não há suporte para dispositivos Nexus 5 pelo Android Nougat.



## <a name="new-features"></a>Novos recursos

Android Nougat apresenta uma variedade de novos recursos e funcionalidades, como suporte a várias janelas, aprimoramentos de notificações e proteção de dados. As seções a seguir destacam esses recursos e fornecem links para ajudá-lo a começar a usá-las em seu aplicativo.



### <a name="multi-window-mode"></a>Modo de várias janela

Modo de várias janela possibilita aos usuários abrir dois aplicativos ao mesmo tempo com suporte de multitarefa completo. Esses aplicativos podem ser executados lado a lado (paisagem) ou um-acima-the-other (retrato) no modo de tela de divisão.
Os usuários poderão arrastar um divisor entre os aplicativos para redimensioná-los e eles podem recortar e colar o conteúdo a entre aplicativos. Quando dois aplicativos são apresentados no modo de várias janela, a atividade selecionada continua a ser executado enquanto a atividade não selecionada está em pausa, mas ainda estarão visíveis. Modo de várias janela não modifica o ciclo de vida de atividade do Android.

[![Aplicativos de exemplo em execução no modo de várias janela em Retrato e paisagem](nougat-images/multi-window-mode.png)](nougat-images/multi-window-mode.png#lightbox)

Você pode configurar como as atividades do seu aplicativo xamarin. Android dão suporte ao modo de várias janela. Por exemplo, você pode configurar atributos que definir o tamanho mínimo e a altura padrão e a largura de seu aplicativo no modo de várias janela. Você pode usar o novo `Activity.IsInMultiWindowMode` propriedade para determinar se sua atividade está no modo de várias janela. Por exemplo:

```csharp
if (!IsInMultiWindowMode) {
    multiDisabledMessage.Visibility = ViewStates.Visible;
} else {
    multiDisabledMessage.Visibility = ViewStates.Gone;
}
```

O [MultiWindowPlayground](https://developer.xamarin.com/samples/monodroid/android-n/MultiWindowPlayground/) aplicativo de exemplo inclui um código c# que demonstra como tirar proveito da janela de várias interfaces de usuário com seu aplicativo.

Para obter mais informações sobre o modo de várias janela, consulte a [suporte a várias janelas](https://developer.android.com/guide/topics/ui/multi-window.html).



### <a name="enhanced-notifications"></a>Notificações aprimoradas

Android Nougat apresenta um sistema de notificação reprojetada. Ele apresenta um novo recurso de resposta direta que torna possível para que os usuários rapidamente a resposta para enviar notificações para mensagens de texto de entrada diretamente na notificação de interface do usuário. Começando com o Android 7.0, notificação de mensagens podem ser agrupadas como um único grupo quando mais de uma mensagem é recebida. Além disso, os desenvolvedores podem personalizar os modos de exibição, aproveitar as decorações de sistema em notificações e tirar proveito dos novos modelos de notificação ao gerar notificações de notificação.


#### <a name="direct-reply"></a>Resposta direta

Quando um usuário recebe uma notificação de mensagem de entrada, o Android Nougat possibilita responder à mensagem de dentro da notificação (em vez de abrir o aplicativo de mensagens para enviar uma resposta).
Esse recurso de resposta embutido torna possível para os usuários a responder rapidamente a uma mensagem SMS ou texto diretamente dentro da interface de notificação:

[![Captura de tela de uma notificação com um campo de resposta direta embutido](nougat-images/notifications-inline-reply-sml.png)](nougat-images/notifications-inline-reply.png#lightbox)

Para dar suporte a esse recurso em seu aplicativo, você deve adicionar *ações de resposta embutido* ao seu aplicativo por meio de uma [RemoteInput](https://developer.xamarin.com/api/type/Android.App.RemoteInput/) , de modo que os usuários podem responder por meio de texto diretamente na notificação da interface do usuário do objeto.
Por exemplo, o código a seguir compilações um `RemoteInput` para receber entrada de texto, compila uma intenção pendente para a ação de resposta e cria uma ação remota de entrada habilitada:

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

Essa ação é adicionada à notificação:

```csharp
// Create the notification:
NotificationCompat.Builder builder = new NotificationCompat.Builder (ApplicationContext)
   .SetSmallIcon (Resource.Drawable.notification_icon)
   ...
   .AddAction (actionReplyByRemoteInput);
```

O [serviço de mensagens](https://developer.xamarin.com/samples/monodroid/android-n/MessagingService/) aplicativo de exemplo inclui um código c# que demonstra como estender as notificações com um `RemoteInput` objeto. Para obter mais informações sobre como adicionar resposta embutido ações ao seu aplicativo para Android 7.0 ou posterior, consulte o Android [responder a notificações](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#direct) tópico.


#### <a name="bundled-notifications"></a>Notificações agrupadas

Android Nougat pode agrupar as mensagens de notificação (por exemplo, ao tópico de mensagem) e exibir o grupo em vez de cada mensagem separada.
Isso *agrupado notificações* recurso torna possível para os usuários ignorar ou de um grupo de notificações em uma ação de arquivo. O usuário pode deslizar para baixo para expandir o pacote de notificações para exibir cada notificação em detalhes:

[![Captura de tela exemplo de notificações agrupados](nougat-images/bundled-notifications-sml.png)](nougat-images/bundled-notifications.png#lightbox)

Para dar suporte a notificações agrupadas, o aplicativo pode usar o [Builder.SetGroup](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetGroup/p/System.String/) método agrupar notificações semelhantes. Para obter mais informações sobre grupos de notificação embutida no Android N, consulte o Android [agrupamento notificações](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#bundle) tópico.


#### <a name="custom-views"></a>Modos de exibição personalizados

Android Nougat possibilita que você crie modos de exibição de notificação personalizada com layouts expansíveis, ações e os cabeçalhos de notificação do sistema. Para obter mais informações sobre modos de exibição de notificação personalizada no Android Nougat, consulte o Android [aprimoramentos de notificação](https://developer.android.com/about/versions/nougat/android-7.0.html#notification_enhancements) tópico.



### <a name="data-saver"></a>Proteção de dados

Começando com o Android Nougat, os usuários podem habilitar uma nova *proteção de dados* blocos em segundo plano uso de dados de configuração. Essa configuração também sinaliza o aplicativo para usar menos dados em primeiro plano sempre que possível. O [ConnectivityManager](https://developer.xamarin.com/api/type/Android.Net.ConnectivityManager/) foi estendido no Android Nougat para que seu aplicativo possa verificar se o usuário tiver habilitado a proteção de dados para que seu aplicativo possa fazer um esforço para limitar seu uso de dados quando a proteção de dados está habilitada.

Para obter mais informações sobre o novo recurso de proteção de dados no Android Nougat, consulte o Android [otimizando o uso de dados de rede](https://developer.android.com/training/basics/network-ops/data-saver.html) tópico.



### <a name="app-shortcuts"></a>Atalhos de aplicativos

Android 7.1 introduzidas uma *atalhos de aplicativo* recurso que torna possível para que os usuários rapidamente iniciar tarefas comuns ou recomendada com seu aplicativo.
Para ativar o menu de atalhos, o usuário longa pressionamentos de ícone do aplicativo para um segundo ou mais &ndash; o menu é exibido com uma vibração rápida.
Liberar o pressionamento faz com que o menu de permanecer:

[![Tela de exemplo de um menu de atalho do aplicativo para um aplicativo de mensagens](nougat-images/app-shortcuts-sml.png)](nougat-images/app-shortcuts.png#lightbox)

Esse recurso está disponível somente API nível 25 ou superior.
Para obter mais informações sobre o novo recurso de atalhos de aplicativos no Android 7.1, consulte o Android [atalhos de aplicativo](https://developer.android.com/guide/topics/ui/shortcuts.html) tópico.


### <a name="sample-code"></a>Código de exemplo

Vários exemplos de xamarin. Android estão disponíveis para mostrar como tirar proveito dos recursos do Android Nougat:

-   [MultiWindowPlayground](https://developer.xamarin.com/samples/monodroid/android-n/MultiWindowPlayground/) demonstra o uso da API disponível no Android Nougat várias janela. Você pode alternar o aplicativo de exemplo no modo do windows de vários para ver como ele afeta o ciclo de vida e o comportamento do aplicativo.

-   [Serviço de mensagens](https://developer.xamarin.com/samples/monodroid/android-n/MessagingService/) é um serviço simples que envia notificações usando o `NotificationCompatManager`. Ele também se estende a notificação com um `RemoteInput` objeto para permitir que dispositivos Android Nougat responder por meio de texto diretamente na notificação sem precisar abrir um aplicativo.

-   [Notificações do Active Directory](https://developer.xamarin.com/samples/monodroid/android-n/ActiveNotifications/) demonstra como usar o `NotificationManager` API lhe dizer quantas notificações de seu aplicativo está exibindo no momento.

-   [No escopo do acesso ao diretório](https://developer.xamarin.com/samples/monodroid/android-n/ScopedDirectoryAccess/) demonstra como usar a API de acesso de diretório no escopo para acessar facilmente os diretórios específicos. Isso serve como uma alternativa à necessidade de definir `READ_EXTERNAL_STORAGE` ou `WRITE_EXTERNAL_STORAGE` permissões em seu manifesto.

-   [Direcionar inicialização](https://developer.xamarin.com/samples/monodroid/android-n/DirectBoot/) ilustra como armazenar dados em um armazenamento criptografado de dispositivo que está sempre disponível enquanto o dispositivo for inicializado ambos antes e depois que qualquer usuário credentials(PIN/Pattern/Password) são inseridos.


## <a name="summary"></a>Resumo

Este artigo introduziu o Android Nougat e explicou como instalar e configurar as ferramentas mais recentes e os pacotes para o desenvolvimento do xamarin. Android no Android Nougat. Também forneceu uma visão geral dos principais recursos disponíveis no Android Nougat, com links para código-fonte de exemplo para ajudá-lo a começar a criar aplicativos para Android Nougat.


## <a name="related-links"></a>Links relacionados

- [Android 7.1 para desenvolvedores](https://developer.android.com/about/versions/nougat/android-7.1.html)
- [Notas de versão do Xamarin Android 7.0](https://developer.xamarin.com/releases/android/xamarin.android_7/xamarin.android_7.0/)
