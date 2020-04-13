---
title: Características de Nougat
description: Como começar a usar xamarin.android para desenvolver aplicativos para Android Nougat.
ms.prod: xamarin
ms.assetid: 5C74ABE2-C862-4ED0-8EA5-C7FEE5251D4B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/02/2018
ms.openlocfilehash: 6274c75abf229268070d495ced662724f5c16627
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027084"
---
# <a name="nougat-features"></a>Características de Nougat

_Como começar a usar xamarin.android para desenvolver aplicativos para Android Nougat._

Este artigo fornece um esboço dos recursos introduzidos no Android Nougat, explica como preparar o Xamarin.Android para o desenvolvimento do Android Nougat e fornece links para aplicativos de amostra que ilustram como usar os recursos do Android Nougat em aplicativos Xamarin.Android.

## <a name="overview"></a>Visão geral

[Android Nougat](https://developer.android.com/about/versions/nougat/android-7.0.html) é o acompanhamento do Google para o Android 6.0 Marshmallow. Xamarin.Android oferece suporte para **Android 7.x Bindings** no Xamarin Android 7.0 e posterior. Android Nougat adiciona muitas novas APIs para os recursos nougat descritos abaixo; essas APIs estão disponíveis para aplicativos Xamarin.Android quando você usa Xamarin.Android 7.0.

[![Imagens de herói de tablets e telefones Android rodando Android Nougat](nougat-images/android-n-hero-sml.png)](nougat-images/android-n-hero.png#lightbox)

Para obter mais informações sobre o Android 7.x APIs, consulte [o Android 7.1 para Desenvolvedores](https://developer.android.com/preview/api-overview.html).
Para obter uma lista de problemas conhecidos do Xamarin.Android 7.0, consulte as [notas de lançamento](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md).

O Android Nougat oferece muitos novos recursos de interesse para os desenvolvedores do Xamarin.Android. Esses recursos incluem:

- **Suporte a várias** &ndash; janelas Este aprimoramento permite que os usuários abram dois aplicativos na tela ao mesmo tempo.

- **Aprimoramentos** &ndash; de notificação O sistema de notificações redesenhado no Android Nougat inclui um recurso *Direct Reply* que permite que os usuários respondam rapidamente às mensagens de texto diretamente da interface do usuário de notificação. Além disso, se o aplicativo criar notificações para mensagens recebidas, o novo recurso *de notificações empacotadas* pode empacotar notificações em conjunto como um único grupo quando mais de uma mensagem for recebida.

- **Armazenamento de dados** &ndash; Este recurso é um novo serviço de sistema que ajuda a reduzir o uso de dados celulares por aplicativos; ele dá aos usuários controle sobre como os aplicativos usam dados celulares.

Além disso, o Android Nougat traz muitos outros aprimoramentos de interesse para os desenvolvedores de aplicativos, como um novo recurso de configuração de segurança de rede, Doze on the Go, attestation de chaves, novas APIs de Configurações Rápidas, suporte a multilocais, APIs icu4J, aprimoramentos do WebView, acesso a recursos de linguagem Java 8, acesso a diretório suscitado, uma API de ponteiro personalizado, suporte a plataforma VR, arquivos virtuais e otimizações de processamento em segundo plano.

Este artigo explica como começar a criar aplicativos com o Android Nougat para experimentar os novos recursos e planejar a migração ou o trabalho de recursos para atingir a nova plataforma Android Nougat.

## <a name="requirements"></a>Requisitos

A seguir, é necessário usar os novos recursos do Android Nougat em aplicativos baseados em Xamarin:

- **Visual Studio ou Visual Studio para Mac** &ndash; Se você estiver usando o Visual Studio, a versão 4.2.0.628 ou posterior do Visual Studio Tools para Xamarin é necessária. Se você estiver usando o Visual Studio para Mac, a versão 6.1.0 ou posterior do Visual Studio para Mac é necessária.

- **Xamarin.Android** &ndash; Xamarin.Android 7.0 ou posterior deve ser instalado e configurado com visual studio ou visual studio para Mac.

- **Android SDK** - Android SDK 7.0 (API 24) ou posterior deve ser instalado através do Android SDK Manager.

- **Java Developer Kit** &ndash; Xamarin O desenvolvimento do Android 7.0 requer [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou posterior se você estiver desenvolvendo para a API nível 24 ou superior (JDK 8 também suporta níveis de API antes de 24). A versão de 64 bits do JDK 8 é necessária se você estiver usando controles personalizados ou o Visualizador de Formulários.

> [!IMPORTANT]
> O Xamarin.Android não é compatível com o JDK 9.

Observe que os aplicativos devem ser reconstruídos com Xamarin C6SR4 ou posteriorpara funcionar de forma confiável com o Android Nougat. Como o Android Nougat pode se vincular apenas a [bibliotecas nativas fornecidas pelo NDK,](https://developer.android.com/about/versions/nougat/android-7.0-changes.html)aplicativos existentes usando bibliotecas como **Mono.Data.Sqlite.dll** podem falhar ao ser executados no Android Nougat se não forem reconstruídos corretamente.

## <a name="getting-started"></a>Introdução

Para começar a usar o Android Nougat com Xamarin.Android, você deve baixar e instalar as ferramentas mais recentes e pacotes SDK antes de criar um projeto Android Nougat:

1. Instale as últimas atualizações do Xamarin.Android do Xamarin.

2. Instale os pacotes e ferramentas do **Android 7.0 (API 24)** ou posterior.

3. Crie um novo projeto Xamarin.Android que tem como alvo o Android Nougat.

4. Configure um emulador ou dispositivo para Android Nougat.

Cada uma dessas etapas é explicada nas seguintes seções:

### <a name="install-xamarin-updates"></a>Instale atualizações de Xamarin

Para adicionar suporte ao Xamarin para Android Nougat, altere o canal de atualizações no Visual Studio ou Visual Studio para Mac para o canal Stable e aplique as últimas atualizações. Se você também precisar de recursos que estão disponíveis atualmente apenas no canal Alpha ou Beta, você pode mudar para o canal Alfa ou Beta (os canais Alfa e Beta também fornecem suporte para o Android 7.x). Para obter informações sobre como alterar o canal de atualizações (versões), consulte [Alterando o Canal de Atualizações](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/change_updates_channel).

### <a name="install-the-android-sdk"></a>Instalar o SDK do Android

Para criar um projeto com o Xamarin Android 7.0, primeiro você deve usar o Android SDK Manager para instalar a **plataforma SDK Android N (API 24)** ou posterior. Você também deve instalar as ferramentas mais recentes **do Android SDK**:

1. Inicie o Android SDK Manager (no Visual Studio para Mac, use **ferramentas > Abra o Android SDK Manager&hellip;**; no Visual Studio, use ferramentas > Android > Android **SDK Manager**).

2. Instale **o Android 7.0 (API 24)** ou posterior:

    [![Selecionando pacotes android 7.0 no Android SDK Manager](nougat-images/preview-packages.png)](nougat-images/preview-packages.png#lightbox)

3. Instale as ferramentas mais recentes do Android SDK:

    [![Selecionando as ferramentas mais recentes do Android SDK no Android SDK Manager](nougat-images/preview-tools.png)](nougat-images/preview-tools.png#lightbox)

    Você deve instalar a revisão 25.2.2.2 ou posterior do Android SDK Tools 24.0.3 ou posterior, e as ferramentas Android SDK Build 24.0.2 ou posterior.

4. Verifique se a localização do **kit de desenvolvimento Java** está configurada para JDK 1.8:

    [![Configurando o caminho JDK 8 em opções de ferramentas](nougat-images/use-jdk-1.8.png)](nougat-images/use-jdk-1.8.png#lightbox)

    Para ver esta configuração no Visual Studio, clique em **Ferramentas > Opções > Xamarin > Configurações do Android**. No Visual Studio para Mac, clique em **Preferências > Projetos > Locais SDK > Android**.

### <a name="start-a-xamarinandroid-project"></a>Inicie um Projeto Xamarin.Android

Crie um novo projeto Xamarin.Android. Se você é novo no desenvolvimento do Android com xamarin, consulte [Hello, Android](~/android/get-started/hello-android/index.md) para aprender sobre a criação de projetos Xamarin.Android.

Ao criar um projeto Android, você deve configurar as configurações da versão para atingir o Android 7.0 ou posterior. Por exemplo, para direcionar seu projeto para o Android 7.0, você deve configurar o nível de API android alvo do seu projeto para **o Android 7.0 (API 24 - Nougat)**. Recomenda-se que você defina o nível de estrutura de destino para API 24 ou posterior. Para obter mais informações sobre a configuração dos níveis de nível de API do Android, consulte [Entendendo os níveis de API do Android](~/android/app-fundamentals/android-api-levels.md).

> [!NOTE]
> Atualmente você deve definir a **versão mínima** do Android para o **Android 7.0 (API 24 - Nougat)** para implantar seu aplicativo em dispositivos ou emuladores Android Nougat.

### <a name="configure-an-emulator-or-device"></a>Configure um emulador ou dispositivo

Se você estiver usando um emulador, inicie o Android AVD Manager e crie um novo dispositivo usando as seguintes configurações:

- Dispositivo: Nexus 5X, Nexus 6, Nexus 6P, Nexus Player, Nexus 9 ou Pixel C.
- Alvo: Android 7.0 - API Nível 24
- ABI: x86 ou\_x86 64

Por exemplo, este dispositivo virtual está configurado para emular um Nexus 6:

[![Configurando um AVD usando dispositivo Nexus 6, alvo do Android 7.0 e CPU/ABI Intel Atom x86](nougat-images/android-n-avd.png)](nougat-images/android-n-avd.png#lightbox)

Se você estiver usando um dispositivo físico como um Nexus 5X, 6 ou 9, você pode atualizar seu dispositivo através de atualizações automáticas sobre o ar (OTA) ou baixar uma imagem do sistema e piscar seu dispositivo diretamente. Para obter mais informações sobre como atualizar manualmente seu dispositivo para Android Nougat, consulte [OTA Images for Nexus Devices](https://developers.google.com/android/nexus/ota).

Observe que os dispositivos Nexus 5 não são suportados pelo Android Nougat.

## <a name="new-features"></a>Novos recursos

O Android Nougat introduz uma variedade de novos recursos e recursos, como suporte a várias janelas, aprimoramentos de notificações e armazenamento de dados. As seções a seguir destacam esses recursos e fornecem links para ajudá-lo a começar a usá-los em seu aplicativo.

### <a name="multi-window-mode"></a>Modo multi-janelas

O modo multi-janelas permite que os usuários abram dois aplicativos ao mesmo tempo com suporte multitarefa completo. Esses aplicativos podem ser executados lado a lado (paisagem) ou um acima do outro (retrato) no modo de tela dividida.
Os usuários podem arrastar um divisor entre os aplicativos para redimensioná-los, e eles podem cortar e colar conteúdo entre aplicativos. Quando dois aplicativos são apresentados no modo multi-janela, a atividade selecionada continua a ser executada enquanto a atividade não selecionada é pausada, mas ainda visível. O modo de várias janelas não modifica o ciclo de vida da atividade do Android.

[![Exemplo de aplicativos em execução no modo multi-janelas em retrato e paisagem](nougat-images/multi-window-mode.png)](nougat-images/multi-window-mode.png#lightbox)

Você pode configurar como as atividades do seu aplicativo Xamarin.Android suportam o modo multi-janelas. Por exemplo, você pode configurar atributos que definem o tamanho mínimo e a altura e largura padrão do aplicativo no modo multi-janela. Você pode usar `Activity.IsInMultiWindowMode` a nova propriedade para determinar se sua atividade está no modo multi-janelas. Por exemplo:

```csharp
if (!IsInMultiWindowMode) {
    multiDisabledMessage.Visibility = ViewStates.Visible;
} else {
    multiDisabledMessage.Visibility = ViewStates.Gone;
}
```

O aplicativo de amostra [MultiWindowPlayground](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-multiwindowplayground) inclui o código C# que demonstra como tirar proveito de várias interfaces de usuário de janela com o seu aplicativo.

Para obter mais informações sobre o modo multi-janelas, consulte o [suporte de várias janelas](https://developer.android.com/guide/topics/ui/multi-window.html).

### <a name="enhanced-notifications"></a>Notificações aprimoradas

O Android Nougat introduz um sistema de notificação redesenhado. Ele possui um novo recurso Direct Reply que permite que os usuários respondam rapidamente às notificações de mensagens de texto recebidas diretamente na interface do usuário de notificação. A partir do Android 7.0, as mensagens de notificação podem ser empacotadas em conjunto como um único grupo quando mais de uma mensagem é recebida. Além disso, os desenvolvedores podem personalizar visualizações de notificações, aproveitar a decoração do sistema em notificações e aproveitar novos modelos de notificação ao gerar notificações.

#### <a name="direct-reply"></a>Resposta Direta

Quando um usuário recebe uma notificação para mensagem recebida, o Android Nougat torna possível responder à mensagem dentro da notificação (em vez de abrir o aplicativo de mensagens para enviar uma resposta).
Esse recurso de resposta inline permite que os usuários respondam rapidamente a um SMS ou mensagem de texto diretamente dentro da interface de notificação:

[![Captura de tela de uma notificação com um campo de resposta direta inline](nougat-images/notifications-inline-reply-sml.png)](nougat-images/notifications-inline-reply.png#lightbox)

Para suportar esse recurso em seu aplicativo, você deve adicionar *ações de resposta em linha* ao seu aplicativo através de um objeto [RemoteInput](xref:Android.App.RemoteInput) para que os usuários possam responder via texto diretamente da interface do usuário de notificação.
Por exemplo, o código `RemoteInput` a seguir constrói a para receber entrada de texto, cria uma intenção pendente para a ação de resposta e cria uma ação ativada de entrada remota:

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

O aplicativo de exemplo [do Serviço de Mensagens](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-messagingservice) inclui `RemoteInput` o código C# que demonstra como estender notificações com um objeto. Para obter mais informações sobre como adicionar ações de resposta em linha ao seu aplicativo para Android 7.0 ou posterior, consulte o tópico [Android Respondendo a Notificações.](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#direct)

#### <a name="bundled-notifications"></a>Notificações empacotadas

O Android Nougat pode agrupar mensagens de notificação (por exemplo, por tópico de mensagem) e exibir o grupo em vez de cada mensagem separada.
Esse recurso *de notificações empacotado* permite que os usuários desconsiderem ou arquivem um grupo de notificações em uma única ação. O usuário pode deslizar para baixo para expandir o pacote de notificações para visualizar cada notificação em detalhes:

[![Exemplo de captura de tela de notificações empacotadas](nougat-images/bundled-notifications-sml.png)](nougat-images/bundled-notifications.png#lightbox)

Para suportar notificações empacotadas, seu aplicativo pode usar o método [Builder.SetGroup](xref:Android.App.Notification.Builder.SetGroup*) para empacotar notificações semelhantes. Para obter mais informações sobre grupos de notificação empacotados no Android N, consulte o tópico Notificações de [Agrupamento](https://developer.android.com/guide/topics/ui/notifiers/notifications.html#bundle) do Android.

#### <a name="custom-views"></a>Modos de exibição personalizados

O Android Nougat permite que você crie visualizações de notificação personalizadas com cabeçalhos de notificação do sistema, ações e layouts expansíveis. Para obter mais informações sobre visualizações de notificação personalizadas no Android Nougat, consulte o tópico [Aprimoramentos de Notificação do](https://developer.android.com/about/versions/nougat/android-7.0.html#notification_enhancements) Android.

### <a name="data-saver"></a>Poupador de dados

Começando pelo Android Nougat, os usuários podem habilitar uma nova configuração *de Data Saver* que bloqueia o uso de dados em segundo plano. Esta configuração também sinaliza que seu aplicativo deve usar menos dados em primeiro plano sempre que possível. O [Gerenciador de Conectividade](xref:Android.Net.ConnectivityManager) foi estendido no Android Nougat para que seu aplicativo possa verificar se o usuário habilitou o Data Saver para que seu aplicativo possa fazer um esforço para limitar seu uso de dados quando o Data Saver estiver ativado.

Para obter mais informações sobre o novo recurso Data Saver no Android Nougat, consulte o tópico Android [Optimizing Network Data Use Usage.](https://developer.android.com/training/basics/network-ops/data-saver.html)

### <a name="app-shortcuts"></a>Atalhos de aplicativo

O Android 7.1 introduziu um recurso *App Shortcuts* que permite que os usuários iniciem rapidamente tarefas comuns ou recomendadas com seu aplicativo.
Para ativar o menu de atalhos, o usuário pressiona o &ndash; ícone do aplicativo por um segundo ou mais o menu aparece com uma vibração rápida.
A liberação da prensa faz com que o menu permaneça:

[![Exemplo de tela de um menu de atalho de aplicativo para um aplicativo de mensagens](nougat-images/app-shortcuts-sml.png)](nougat-images/app-shortcuts.png#lightbox)

Este recurso está disponível apenas nível API 25 ou superior.
Para obter mais informações sobre o novo recurso App Shortcuts no Android 7.1, consulte o tópico [Atalhos do Aplicativo](https://developer.android.com/guide/topics/ui/shortcuts.html) para Android.

### <a name="sample-code"></a>Exemplo de código

Várias amostras de Xamarin.Android estão disponíveis para mostrar como aproveitar os recursos do Android Nougat:

- [MultiWindowPlayground](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-multiwindowplayground) demonstra o uso da API de várias janelas disponível no Android Nougat. Você pode mudar o aplicativo de exemplo para o modo multi-janelas para ver como ele afeta o ciclo de vida e o comportamento do aplicativo.

- [O Serviço de Mensagens](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-messagingservice) é um `NotificationCompatManager`serviço simples que envia notificações usando o . Ele também estende a `RemoteInput` notificação com um objeto para permitir que os dispositivos Android Nougat respondam via texto diretamente da notificação sem ter que abrir um aplicativo.

- [Notificações ativas](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-activenotifications) demonstram `NotificationManager` como usar a API para informar quantas notificações seu aplicativo está exibindo no momento.

- [Acesso ao diretório escopo](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-scopeddirectoryaccess) Demonstra como usar a API de acesso ao diretório escopo para acessar facilmente diretórios específicos. Isso serve como uma alternativa `READ_EXTERNAL_STORAGE` `WRITE_EXTERNAL_STORAGE` para ter que definir ou permissões em seu manifesto.

- [Inicialização direta](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-n-directboot) Ilustra como armazenar dados em um armazenamento criptografado por dispositivo que está sempre disponível enquanto o dispositivo é inicializado antes e depois de qualquer credencial de usuário (PIN/Padrão/Senha) ser inserida.

## <a name="summary"></a>Resumo

Este artigo introduziu o Android Nougat e explicou como instalar e configurar as ferramentas e pacotes mais recentes para o desenvolvimento do Xamarin.Android no Android Nougat. Ele também forneceu uma visão geral dos principais recursos disponíveis no Android Nougat, com links para exemplo de código fonte para ajudá-lo a começar a criar aplicativos para Android Nougat.

## <a name="related-links"></a>Links relacionados

- [Android 7.1 Para Desenvolvedores](https://developer.android.com/about/versions/nougat/android-7.1.html)
- [Notas de lançamento do Android 7.0 da Xamarin](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_7/xamarin.android_7.0/index.md)
