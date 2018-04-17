---
title: Google Cloud Messaging
description: Google Cloud Messaging (GCM) é um serviço que facilita a mensagens entre aplicativos móveis e aplicativos de servidor. Este artigo fornece uma visão geral de como funciona o GCM e explica como configurar os serviços do Google para que seu aplicativo possa usar GCM.
ms.prod: xamarin
ms.assetid: DF8EF401-F63D-4BA0-B2C6-B22DF8FD60CB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/12/2018
ms.openlocfilehash: 29cccf414759a79a8ba74dfc35b7ba9f6a1cc5d6
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="google-cloud-messaging"></a>Google Cloud Messaging

_Google Cloud Messaging (GCM) é um serviço que facilita a mensagens entre aplicativos móveis e aplicativos de servidor. Este artigo fornece uma visão geral de como funciona o GCM e explica como configurar os serviços do Google para que seu aplicativo possa usar GCM._

[![Logotipo do Google Cloud Messaging](google-cloud-messaging-images/preview-sml.png)](google-cloud-messaging-images/preview.png#lightbox)

Este tópico fornece uma visão geral de como o Google Cloud Messaging roteia mensagens entre seu aplicativo e um servidor de aplicativo, e fornece um procedimento passo a passo para adquirir credenciais para que seu aplicativo pode usar os serviços do GCM.

> [!NOTE]
> GCM foi substituído por [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM).
> GCM APIs de cliente e servidor [foram preteridos](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html) e não estará disponível assim que 11 de abril de 2019.

## <a name="overview"></a>Visão geral

Google Cloud Messaging (GCM) é um serviço que trata de envio, roteamento e enfileiramento de mensagens entre aplicativos de servidor e aplicativos cliente móveis. Um *aplicativo cliente* é um aplicativo habilitado para GCM que é executado em um dispositivo. O *servidor de aplicativos* (fornecido por você ou sua empresa) é o servidor habilitado GCM que seu aplicativo cliente se comunica com por meio do GCM:

[![GCM reside entre o aplicativo cliente e o servidor de aplicativo](google-cloud-messaging-images/01-server-gcm-app-sml.png)](google-cloud-messaging-images/01-server-gcm-app.png#lightbox)

Usando GCM, servidores de aplicativo podem enviar mensagens para um único dispositivo, um grupo de dispositivos ou um número de dispositivos que estão inscritos em um tópico. O aplicativo cliente pode usar o GCM para assinar mensagens downstream de um servidor de aplicativo (por exemplo, para receber notificações remotas). Além disso, GCM torna possível para aplicativos de cliente enviar mensagens de upstream para o servidor de aplicativo.

Para obter informações sobre a implementação de um servidor de aplicativo para GCM, consulte [sobre o servidor de Conexão do GCM](https://developers.google.com/cloud-messaging/server).



## <a name="google-cloud-messaging-in-action"></a>Google Cloud Messaging em ação

Quando downstream são enviadas de um servidor de aplicativo para um aplicativo cliente, o servidor de aplicativo envia a mensagem para um *servidor de conexão do GCM*; o servidor de conexão do GCM, por sua vez, encaminha a mensagem para um dispositivo que esteja executando o aplicativo cliente. As mensagens podem ser enviadas via HTTP ou [XMPP](https://developers.google.com/cloud-messaging/ccs) (mensagens extensível e protocolo de presença). Como os aplicativos cliente não estão sempre conectados ou está em execução, GCM conexão enfileira e armazena as mensagens do servidor, enviá-los para aplicativos cliente, como eles se reconectar e ficam disponíveis. Da mesma forma, GCM será Enfileirar mensagens upstream no aplicativo cliente para o servidor de aplicativos se o servidor de aplicativo não está disponível.

GCM usa as credenciais a seguir para identificar o servidor de aplicativo e seu aplicativo cliente e usa essas credenciais para autorizar transações de mensagens por meio do GCM:

-   **Chave de API** &ndash; o *chave API* fornece o acesso ao servidor de aplicativo para serviços do Google; GCM usa essa chave para autenticar o servidor de aplicativo.
    Antes de usar o serviço do GCM, você deve primeiro obter uma chave de API do [Console de desenvolvedor do Google](https://console.developers.google.com/) criando um *projeto*. A chave de API deve ser mantida segura; Para obter mais informações sobre como proteger sua chave de API, consulte [práticas recomendadas para usar com segurança as chaves de API](https://support.google.com/cloud/answer/6310037?hl=en).

-   **ID do remetente** &ndash; o *ID do remetente* autoriza o servidor de aplicativos para seu aplicativo cliente &ndash; é um número exclusivo que identifica o servidor de aplicativo que tem permissão para enviar mensagens para o aplicativo cliente.
    A ID do remetente é também o número de projeto; Você pode obter a ID do remetente do Console de desenvolvedores do Google ao registrar o seu projeto.

-   **Token de registro** &ndash; o *registro Token* é a identidade do GCM do seu aplicativo cliente em um determinado dispositivo. O token de registro é gerado em tempo de execução &ndash; seu aplicativo recebe um token de registro quando ele registra pela primeira vez com GCM durante a execução em um dispositivo. O token de registro autoriza uma instância do aplicativo cliente (em execução no dispositivo específico) para receber mensagens do GCM.

-   **ID do aplicativo** &ndash; a identidade de seu aplicativo de cliente (independente de qualquer dispositivo determinado) que registra para receber mensagens do GCM. No Android, a ID do aplicativo é o nome do pacote registrado no **AndroidManifest.xml**, como `com.xamarin.gcmexample`.

[Configuração de backup Google Cloud Messaging](#settingup) (posteriormente neste guia) fornece instruções detalhadas para criar um projeto e gerar essas credenciais.

As seções a seguir explicam como essas credenciais são usadas quando os aplicativos cliente se comunicam com servidores de aplicativos por meio do GCM.



### <a name="registration-with-gcm"></a>Registro do GCM

Um aplicativo cliente instalado em um dispositivo deve primeiro registrar com GCM antes de mensagens podem ocorrer. O aplicativo cliente deve concluir as etapas de registro mostradas no diagrama a seguir:

[![Etapas de registro de aplicativo](google-cloud-messaging-images/02-app-registration-sml.png)](google-cloud-messaging-images/02-app-registration.png#lightbox)

1.  O aplicativo cliente contata o GCM para obter um token de registro, passando a ID do remetente para GCM.

2.  GCM retorna um token de registro para o aplicativo cliente.

3.  O aplicativo cliente encaminha o token de registro para o servidor de aplicativos.

O servidor de aplicativo armazena em cache o token de registro para comunicações subsequentes com o aplicativo cliente. Opcionalmente, o servidor de aplicativo pode enviar uma confirmação de volta para o aplicativo cliente para indicar que o token de registro foi recebido. Após esse handshake ocorre, o aplicativo cliente pode receber mensagens (ou enviar mensagens para) o servidor de aplicativos.

Quando o aplicativo cliente não deseja receber mensagens do servidor de aplicativo, ele pode enviar uma solicitação para o servidor de aplicativos para excluir o token de registro. Se o aplicativo cliente está recebendo mensagens do tópico (explicadas mais adiante neste artigo), ele pode cancelar sua assinatura do tópico.
Se o aplicativo cliente é desinstalado de um dispositivo, o GCM detecta isso e notifica automaticamente o servidor de aplicativos para excluir o token de registro.

Do Google [aplicativos de cliente registrando](https://developers.google.com/cloud-messaging/registration) explica o processo de registro mais detalhadamente; explica o cancelamento do registro e cancelamento de inscrição e descreve o processo de cancelamento de registro quando um aplicativo cliente é desinstalado.



### <a name="downstream-messaging"></a>Mensagens de downstream

Quando o servidor de aplicativo envia uma mensagem de downstream para o aplicativo cliente, ele segue as etapas ilustradas no diagrama a seguir:

[![Repositório de mensagens downstream e diagrama de encaminhamento](google-cloud-messaging-images/03-downstream-sml.png)](google-cloud-messaging-images/03-downstream.png#lightbox)

1.  O servidor de aplicativo envia a mensagem para o GCM.

2.  Se o dispositivo do cliente não estiver disponível, o servidor GCM armazena a mensagem na fila de transmissão posterior.

3.  Quando o dispositivo do cliente estiver disponível, o GCM envia a mensagem para o aplicativo cliente no dispositivo.

4.  O aplicativo cliente recebe a mensagem do GCM e manipula adequadamente. Por exemplo, se a mensagem é uma notificação remota, ele é apresentado ao usuário.

Neste cenário de mensagens (onde o servidor de aplicativo envia uma mensagem para um aplicativo cliente único), mensagens podem ser de até 4kB de comprimento.

Para obter informações detalhadas (incluindo exemplos de código) sobre o recebimento de mensagens de downstream GCM no Android, consulte [notificações remoto](~/android/data-cloud/google-messaging/remote-notifications-with-gcm.md).


#### <a name="topic-messaging"></a>Mensagens do tópico

*Mensagens do tópico* é um tipo de sistema de mensagens downstream onde o servidor de aplicativo envia uma única mensagem para vários dispositivos de aplicativos cliente que se inscrever para um tópico (por exemplo, uma previsão do tempo). Mensagens do tópico podem ser até 2KB de comprimento e mensagens do tópico oferece suporte a assinaturas de um milhão por aplicativo. Se o GCM estiver sendo usado apenas para mensagens do tópico, o aplicativo cliente não é necessário para enviar um token de registro para o servidor de aplicativos. Do Google [implementando tópico mensagens](https://developers.google.com/cloud-messaging/topic-messaging) explica como enviar mensagens de um servidor de aplicativo para vários dispositivos que se inscrever para um tópico específico.



#### <a name="group-messaging"></a>Grupo de mensagens

*Agrupar mensagens* é um tipo de sistema de mensagens downstream onde o servidor de aplicativo envia uma única mensagem para vários dispositivos de aplicativos cliente que pertencem a um grupo (por exemplo, um grupo de dispositivos que pertencem a um único usuário). Mensagens de grupo podem ser até 2KB de comprimento para dispositivos iOS e até 4KB de comprimento para dispositivos Android. Um grupo é limitado a um máximo de 20 membros. Do Google [mensagens do grupo de dispositivo](https://developers.google.com/cloud-messaging/notifications) explica como servidores de aplicativo podem enviar uma única mensagem para várias instâncias do aplicativo cliente executados em dispositivos que pertencem a um grupo.


### <a name="upstream-messaging"></a>Mensagens de upstream

Se seu aplicativo cliente se conecta a um servidor que oferece suporte a [XMPP](https://developers.google.com/cloud-messaging/ccs), que pode enviar mensagens para o servidor de aplicativo, conforme ilustrado no diagrama a seguir:

[![Diagrama de mensagens upstream](google-cloud-messaging-images/04-upstream-sml.png)](google-cloud-messaging-images/04-upstream.png#lightbox)

1.  O aplicativo cliente envia uma mensagem para o servidor de conexão do GCM XMPP.

2.  Se o servidor de aplicativo é desconectado, o servidor GCM armazena a mensagem em uma fila para encaminhamento mais tarde.

3.  Quando o servidor de aplicativos é reconectado, o GCM encaminha a mensagem para o servidor de aplicativos.

4.  O servidor de aplicativo analisa a mensagem para verificar a identidade do aplicativo cliente, em seguida, envia uma "confirmação" para o GCM para confirmar o recebimento da mensagem.

5.  O servidor de aplicativo processa a mensagem.

Do Google [mensagens Upstream](https://developers.google.com/cloud-messaging/ccs#upstream) explica como estruturar mensagens codificadas em JSON e enviá-los para servidores de aplicativos que executam o servidor na Conexão do Google XMPP com base em nuvem.


<a name="settingup" />

## <a name="setting-up-google-cloud-messaging"></a>Configurar o Google Cloud Messaging

Antes de usar os serviços GCM em seu aplicativo, você deve primeiro adquirir credenciais para acessar servidores do GCM do Google. As seções a seguir descrevem as etapas necessárias para concluir esse processo:



### <a name="enable-google-services-for-your-app"></a>Habilitar serviços do Google para seu aplicativo

1.  Entrar a [Console de desenvolvedores do Google](https://developers.google.com/mobile/add?platform=android) com o Google, conta (ou seja, o endereço do gmail) e crie um novo projeto. Se você tiver um projeto existente, escolha o projeto que você deseja transformar GCM habilitado. No exemplo a seguir, um novo projeto chamado **XamarinGCM** é criada:

    [![Criando projeto XamarinGCM](google-cloud-messaging-images/05-create-gcm-app-sml.png)](google-cloud-messaging-images/05-create-gcm-app.png#lightbox)

2.  Em seguida, digite o nome do pacote para seu aplicativo (neste exemplo, o nome do pacote é **com.xamarin.gcmexample**) e clique em **continuar para escolher e configurar serviços**:

    [![Inserir o nome do pacote](google-cloud-messaging-images/06-package-name-sml.png)](google-cloud-messaging-images/06-package-name.png#lightbox)

    Observe que esse nome de pacote também é a ID do aplicativo para seu aplicativo.

3.  O **escolher e configurar serviços** seção lista os serviços do Google que você pode adicionar ao seu aplicativo. Clique em **mensagens de nuvem**:

    [![Escolha a nuvem de mensagens](google-cloud-messaging-images/07-choose-gcm-service-sml.png)](google-cloud-messaging-images/07-choose-gcm-service.png#lightbox)

4.  Em seguida, clique em **habilitar GOOGLE CLOUD MESSAGING**:

    [![Habilitar o Google Cloud Messaging](google-cloud-messaging-images/08-enable-gcm-sml.png)](google-cloud-messaging-images/08-enable-gcm.png#lightbox)

5.  Um **chave de API de servidor** e um **ID do remetente** são gerados para seu aplicativo. Esses valores de registro e clique em **fechar**:

    [![Chave de API de servidor e a ID do remetente exibido](google-cloud-messaging-images/09-get-api-key-and-id-sml.png)](google-cloud-messaging-images/09-get-api-key-and-id.png#lightbox)

    Proteger a chave de API &ndash; não se destina para uso público. Se a chave de API estiver comprometida, servidores não autorizados podem publicar mensagens em aplicativos cliente.
    [Práticas recomendadas para usar com segurança as chaves de API](https://support.google.com/cloud/answer/6310037?hl=en) fornece diretrizes úteis para proteger sua chave de API.



### <a name="view-your-project-settings"></a>Exibir as configurações do projeto

Você pode exibir as configurações do projeto a qualquer momento entrando no [Google nuvem Console](https://console.cloud.google.com/) e selecionando seu projeto. Por exemplo, você pode exibir o **ID do remetente** selecionando seu projeto no menu suspenso na parte superior da página (neste exemplo, o projeto é chamado **XamarinGCM**). A ID do remetente é o número de projeto, conforme mostrado nesta captura de tela (a ID do remetente é **9349932736**):

[![Exibindo a identificação do remetente](google-cloud-messaging-images/10-view-server-id-sml.png)](google-cloud-messaging-images/10-view-server-id.png#lightbox)

Para exibir o **chave API**, clique em **Manager API** e, em seguida, clique em **credenciais**:

[![Exibindo a chave de API](google-cloud-messaging-images/11-view-credentials-sml.png)](google-cloud-messaging-images/11-view-credentials.png#lightbox)



## <a name="for-further-reading"></a>Para leitura adicional

-   Do Google [aplicativos de cliente registrando](https://developers.google.com/cloud-messaging/registration) descreve o processo de registro de cliente mais detalhadamente, e fornece informações sobre como configurar a repetição automática e manter o estado do registro em sincronia.

-   [6120 RFC](https://tools.ietf.org/html/rfc6120) e [6121 RFC](https://tools.ietf.org/html/rfc6121) explicam e definir as mensagens extensível e o protocolo de presença (XMPP).



## <a name="summary"></a>Resumo

Este artigo fornece uma visão geral do Google Cloud Messaging (GCM). Ele explicado as várias credenciais que são usadas para identificar e autorizar mensagens entre servidores de aplicativos e aplicativos cliente. Ele ilustra os cenários mais comuns de mensagens, e ele as etapas detalhadas para registrar seu aplicativo com GCM para usar os serviços do GCM.


## <a name="related-links"></a>Links relacionados

- [Mensagens de nuvem](https://developers.google.com/cloud-messaging/)
