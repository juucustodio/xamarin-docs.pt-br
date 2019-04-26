---
title: Mensagens na nuvem do firebase
description: O firebase Cloud Messaging (FCM) é um serviço que facilita o sistema de mensagens entre aplicativos móveis e aplicativos de servidor. Este artigo fornece uma visão geral de como funciona o FCM e explica como configurar serviços do Google para que seu aplicativo possa usar o FCM.
ms.prod: xamarin
ms.assetid: E5314D7F-2AAC-40DA-BEBA-27C834F078DD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/31/2018
ms.openlocfilehash: bf0523a98c8e68691228b6e305265277e2925fa3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61017377"
---
# <a name="firebase-cloud-messaging"></a>Mensagens na nuvem do firebase

_O firebase Cloud Messaging (FCM) é um serviço que facilita o sistema de mensagens entre aplicativos móveis e aplicativos de servidor. Este artigo fornece uma visão geral de como funciona o FCM e explica como configurar serviços do Google para que seu aplicativo possa usar o FCM._

[![Imagem do herói firebase Cloud Messaging](firebase-cloud-messaging-images/preview.png)](firebase-cloud-messaging-images/preview.png#lightbox)

Este tópico fornece uma visão geral de como o Firebase Cloud Messaging roteia mensagens entre o aplicativo xamarin. Android e um servidor de aplicativo e fornece um procedimento passo a passo para adquirir as credenciais para que seu aplicativo pode usar os serviços do FCM.

## <a name="overview"></a>Visão geral

O firebase Cloud Messaging (FCM) é um serviço de plataforma cruzada que manipula o envio, roteamento e enfileiramento de mensagens entre aplicativos de servidor e aplicativos de cliente móvel. FCM é o sucessor para Google Cloud Messaging (GCM), e ele se baseia na Google Play Services.

Conforme ilustrado no diagrama a seguir, FCM atua como um intermediário entre clientes e remetentes de mensagens. Um *aplicativo cliente* é um aplicativo habilitado para FCM que é executado em um dispositivo. O *do servidor de aplicativo* (fornecido por você ou sua empresa) é o servidor FCM habilitados que seu aplicativo cliente se comunica com por meio do FCM. Ao contrário do GCM, FCM torna possível para que você possa enviar mensagens para aplicativos de cliente diretamente por meio da GUI de notificações do Console Firebase:

[![FCM fica entre o aplicativo cliente e um servidor de aplicativo](firebase-cloud-messaging-images/01-server-fcm-app-sml.png)](firebase-cloud-messaging-images/01-server-fcm-app.png#lightbox)

Usando o FCM, servidores de aplicativo podem enviar mensagens para um único dispositivo, a um grupo de dispositivos ou para um número de dispositivos que estão inscritos em um tópico. Um aplicativo cliente pode usar o FCM para assinar mensagens downstream de um servidor de aplicativo (por exemplo, para receber notificações remotas). Para obter mais informações sobre os diferentes tipos de mensagens do Firebase, consulte [sobre mensagens do FCM](https://firebase.google.com/docs/cloud-messaging/concept-options).

## <a name="fcm-in-action"></a>O firebase Cloud Messaging em ação

Quando uma mensagem de downstream é enviada para um aplicativo cliente de um servidor de aplicativo, o servidor de aplicativo envia a mensagem para um *servidor de conexão do FCM* fornecido pelo Google; o servidor de conexão do FCM, por sua vez, encaminha a mensagem para um dispositivo que está executando o aplicativo cliente. As mensagens podem ser enviadas por HTTP ou [XMPP](https://developers.google.com/cloud-messaging/ccs) (mensagens extensível e protocolo de presença). Porque os aplicativos de cliente nem sempre estão conectados ou em execução, as conexão servidor enfileira e repositórios de mensagens do FCM, enviá-los para aplicativos cliente, como elas se reconectar e se tornam disponíveis. Da mesma forma, FCM irá Enfileirar mensagens upstream do aplicativo cliente para o servidor de aplicativo se o servidor de aplicativo não está disponível. Para obter mais informações sobre servidores de conexão do FCM, consulte [sobre o Firebase Cloud Messaging Server](https://firebase.google.com/docs/cloud-messaging/server).

FCM usa as credenciais a seguir para identificar o servidor de aplicativo e o aplicativo cliente, e usa essas credenciais para autorizar transações de mensagens por meio do FCM:

-   <a name="fcm-in-action-sender-id"></a>**ID do remetente** &ndash; as *ID de remetente* é um valor numérico exclusivo atribuído quando você cria seu projeto do Firebase. A ID de remetente é usada para identificar cada servidor de aplicativo que pode enviar mensagens para o aplicativo cliente. A ID de remetente também é o número do projeto; Você pode obter a ID de remetente do Console Firebase quando você registra seu projeto. Um exemplo de uma ID de remetente é `496915549731`.

-   <a name="fcm-in-action-api-key"></a>**Chave de API** &ndash; as *chave de API* concede o acesso ao servidor de aplicativo aos serviços do Firebase; FCM usa essa chave para autenticar o servidor de aplicativo. Essa credencial é também conhecida como o *chave do servidor* ou o *chave de API da Web*. Um exemplo de uma chave de API é `AJzbSyCTcpfRT1YRqbz-jIwp1h06YdauvewGDzk`.

-   <a name="fcm-in-action-app-id"></a>**ID do aplicativo** &ndash; a identidade do aplicativo cliente (independente de qualquer determinado dispositivo) que registra para receber mensagens do FCM. Um exemplo de uma ID do aplicativo é `1:415712510732:android:0e1eb7a661af2460`.

-   <a name="fcm-in-action-registration-token"></a>**Token de registro** &ndash; as *Token de registro* (também conhecido como o *ID da instância*) é a identidade do FCM do seu aplicativo cliente em um determinado dispositivo. O token de registro é gerado em tempo de execução &ndash; seu aplicativo recebe um token de registro quando ele registra pela primeira vez com o FCM durante a execução em um dispositivo. O token de registro autoriza uma instância do aplicativo cliente (em execução nesse dispositivo específico) para receber mensagens do FCM.
    Um exemplo de um token de registro é `fkBQTHxKKhs:AP91bHuEedxM4xFAUn0z ... JKZS` (uma cadeia de caracteres muito longa).

[Configuração de backup Firebase Cloud Messaging](#setup_fcm) (mais adiante neste guia) fornece instruções detalhadas para criar um projeto e gerar essas credenciais. Quando você cria um novo projeto na [Console Firebase](https://console.firebase.google.com/), chamado de um arquivo de credenciais **google-Services. JSON** é criado &ndash; adicionar esse arquivo ao seu projeto xamarin. Android, conforme explicado na [ Notificações remotas com o FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

As seções a seguir explicam como essas credenciais são usadas quando os aplicativos cliente se comunicam com servidores de aplicativo por meio do FCM.


<a name="registration" />

### <a name="registration-with-fcm"></a>Registro com FCM

Um aplicativo cliente deve primeiro registrar com o FCM antes de mensagens podem ocorrer. O aplicativo cliente deve concluir as etapas de registro mostradas no diagrama a seguir:

[![Diagrama de etapas de registro de aplicativo](firebase-cloud-messaging-images/02-app-registration-sml.png)](firebase-cloud-messaging-images/02-app-registration.png#lightbox)

1.  O aplicativo cliente contata o FCM para obter um token de registro, passando a ID de remetente, a chave de API e a ID do aplicativo para o FCM.

2.  FCM retorna um token de registro para o aplicativo cliente.

3.  (Opcionalmente), o aplicativo cliente encaminha o token de registro para o servidor de aplicativo.

O servidor de aplicativos armazena em cache o token de registro para comunicações subsequentes com o aplicativo cliente. O servidor de aplicativo pode enviar uma confirmação ao aplicativo cliente para indicar que o token de registro foi recebido. Após esse handshake, o aplicativo cliente pode receber mensagens da (ou enviar mensagens para) do servidor de aplicativos. O aplicativo cliente pode receber um novo token de registro se o token antigo for comprometido (consulte [notificações remotas com o FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md) para obter um exemplo de como um aplicativo recebe as atualizações de token de registro).

Quando o aplicativo cliente não deseja receber mensagens do servidor de aplicativo, ele pode enviar uma solicitação para o servidor de aplicativo para excluir o token de registro. Se o aplicativo cliente seja desinstalado de um dispositivo, o FCM detecta isso e notifica automaticamente do servidor de aplicativos para excluir o token de registro.



### <a name="downstream-messaging"></a>Mensagens de downstream

O diagrama a seguir ilustra como o Firebase Cloud Messaging armazena e encaminha mensagens downstream:

[![FCM usa armazenar e encaminhar para mensagens de downstream](firebase-cloud-messaging-images/03-downstream-sml.png)](firebase-cloud-messaging-images/03-downstream.png#lightbox)

Quando o servidor de aplicativo envia uma mensagem de downstream para o aplicativo cliente, ele usa as etapas a seguir como enumerados no diagrama acima:

1.  O servidor de aplicativo envia a mensagem ao FCM.

2.  Se o dispositivo do cliente não estiver disponível, o servidor FCM armazena a mensagem em uma fila para transmissão posterior. As mensagens são mantidas no armazenamento do FCM para um máximo de 4 semanas (para obter mais informações, consulte [definindo o tempo de vida de uma mensagem](https://firebase.google.com/docs/cloud-messaging/concept-options#ttl)).

3.  Quando o dispositivo do cliente estiver disponível, FCM encaminha a mensagem para o aplicativo cliente nesse dispositivo.

4.  O aplicativo cliente recebe a mensagem do FCM, processa-os e exibe para o usuário. Por exemplo, se a mensagem for uma notificação remota, ela é apresentada ao usuário na área de notificação.

Nesse cenário de sistema de mensagens (em que o servidor de aplicativo envia uma mensagem para um aplicativo cliente única), mensagens podem ser de até 4kB de comprimento.

Para obter informações detalhadas sobre o recebimento de mensagens do FCM downstream no Android, consulte [notificações remotas com o FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

### <a name="topic-messaging"></a>Mensagens de tópico

*Mensagens de tópico* torna possível para um servidor de aplicativo enviar uma mensagem para vários dispositivos que tem aceitado um tópico específico. Você também pode compor e enviar mensagens do tópico por meio da GUI de notificações do Console Firebase. FCM manipula o roteamento e entrega de mensagens de tópico para clientes inscritos. Esse recurso pode ser usado para mensagens, como alertas de clima, cotações de ações e manchetes.

[![Diagrama de sistema de mensagens do tópico](firebase-cloud-messaging-images/04-topic-messaging-sml.png)](firebase-cloud-messaging-images/04-topic-messaging.png#lightbox)

As etapas a seguir são usadas no sistema de mensagens do tópico (depois que o aplicativo cliente obtém um token de registro, conforme explicado anteriormente):

1.  O aplicativo cliente assina um tópico enviando uma mensagem de inscrever-se ao FCM.

2.  O servidor de aplicativo envia mensagens do tópico para FCM para distribuição.

3.  FCM encaminha mensagens do tópico para clientes que se inscreveu para esse tópico.

Para obter mais informações sobre as mensagens de tópico do Firebase, consulte do Google [mensagens do tópico no Android](https://firebase.google.com/docs/cloud-messaging/android/topic-messaging).


<a name="setup_fcm" />

## <a name="setting-up-firebase-cloud-messaging"></a>Configurando o Firebase Cloud Messaging

Antes de usar os serviços FCM em seu aplicativo, você deve criar um novo projeto (ou importar um projeto existente) por meio de [Console Firebase](https://console.firebase.google.com/). Use as seguintes etapas para criar um projeto do Firebase Cloud Messaging para seu aplicativo:

1.  Entrar a [Console Firebase](https://console.firebase.google.com/) com sua conta do Google (ou seja, seu email do Gmail) e clique em **criar novo projeto**:

    [![Criar botão novo projeto](firebase-cloud-messaging-images/05-firebase-console-sml.png)](firebase-cloud-messaging-images/05-firebase-console.png#lightbox)

    Se você tiver um projeto existente, clique em **importar um projeto do Google**.

2.  No **criar um projeto** caixa de diálogo, insira o nome do seu projeto e clique em **criar projeto**. No exemplo a seguir, um novo projeto chamado **XamarinFCM** é criado:

    [![Criar uma caixa de diálogo do projeto](firebase-cloud-messaging-images/06-create-a-project-sml.png)](firebase-cloud-messaging-images/06-create-a-project.png#lightbox)

3.  No Console do Firebase **visão geral**, clique em **adicionar Firebase ao seu aplicativo Android**:

    [![Adicionar o Firebase ao seu aplicativo Android](firebase-cloud-messaging-images/07-add-firebase-sml.png)](firebase-cloud-messaging-images/07-add-firebase.png#lightbox)

4.  Na próxima tela, insira o nome do pacote do aplicativo. Neste exemplo, é o nome do pacote **com.xamarin.fcmexample**. Esse valor deve corresponder ao nome do pacote de aplicativo Android. Um apelido de aplicativo também pode ser inserido na **apelido aplicativo** campo:

    [![Inserindo exemplo FCM como o apelido do aplicativo](firebase-cloud-messaging-images/08-package-name-sml.png)](firebase-cloud-messaging-images/08-package-name.png#lightbox)

5.  Se seu aplicativo usa o Google Auth, convites ou links dinâmicos, você também deve inserir seu certificado de assinatura de depuração. Para obter mais informações sobre como localizar seu certificado de autenticação, consulte [Localizando seu repositório de chaves assinatura MD5 ou SHA1](~/android/deploy-test/signing/keystore-signature.md).
    Neste exemplo, o certificado de autenticação for deixado em branco.

6.  Clique em **Adicionar aplicativo**:

    [![Clicar no botão Adicionar aplicativo](firebase-cloud-messaging-images/09-add-app-sml.png)](firebase-cloud-messaging-images/09-add-app.png#lightbox)

    Uma chave de API de servidor e uma ID de cliente são gerados automaticamente para o aplicativo. Essa informação é empacotada em uma **google-Services. JSON** arquivo é baixado automaticamente quando você clica em **Adicionar aplicativo**.
    Certifique-se de salvar este arquivo em um local seguro.

Para obter um exemplo detalhado de como adicionar **google-Services. JSON** a um projeto de aplicativo para receber mensagens de notificação do FCM por push no Android, consulte [notificações remotas com o FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).



## <a name="for-further-reading"></a>Para leitura adicional

-   Do Google [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/) fornece uma visão geral dos recursos principais do Firebase Cloud Messaging, obter uma explicação de como ele funciona e instruções de instalação.

-   Do Google [criar aplicativo enviar solicitações do servidor](https://firebase.google.com/docs/cloud-messaging/send-message) explica como enviar mensagens com o servidor de aplicativos.

-   [RFC 6120](https://tools.ietf.org/html/rfc6120) e [6121 RFC](https://tools.ietf.org/html/rfc6121) explicar e definir as mensagens extensível e o protocolo de presença (XMPP).

-   [Sobre mensagens de FCM](https://firebase.google.com/docs/cloud-messaging/concept-options) descreve os diferentes tipos de mensagens que podem ser enviadas com o Firebase Cloud Messaging.

## <a name="summary"></a>Resumo

Este artigo fornece uma visão geral do Firebase Cloud Messaging (FCM). Ele explicou as várias credenciais que são usadas para identificar e autorizar o sistema de mensagens entre aplicativos de cliente e servidores de aplicativo. Ele ilustrados o registro e cenários de mensagens de downstream, e ele as etapas detalhadas para registrar seu aplicativo com o FCM usar os serviços do FCM.


## <a name="related-links"></a>Links relacionados

- [Mensagens na nuvem do Firebase](https://firebase.google.com/docs/cloud-messaging/)
