---
title: Firebase mensagens de nuvem
description: Mensagens de nuvem firebase (FCM) é um serviço que facilita a mensagens entre aplicativos móveis e aplicativos de servidor. Este artigo fornece uma visão geral de como funciona o FCM e explica como configurar os serviços do Google para que seu aplicativo possa usar FCM.
ms.prod: xamarin
ms.assetid: E5314D7F-2AAC-40DA-BEBA-27C834F078DD
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: ef2c23d16545d03dc267054a96f8b0f8883afcf1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30769813"
---
# <a name="firebase-cloud-messaging"></a>Firebase mensagens de nuvem

_Mensagens de nuvem firebase (FCM) é um serviço que facilita a mensagens entre aplicativos móveis e aplicativos de servidor. Este artigo fornece uma visão geral de como funciona o FCM e explica como configurar os serviços do Google para que seu aplicativo possa usar FCM._

[![Imagem do herói firebase Cloud Messaging](firebase-cloud-messaging-images/preview.png)](firebase-cloud-messaging-images/preview.png#lightbox)

Este tópico fornece uma visão geral de como Firebase Cloud Messaging roteia mensagens entre seu aplicativo xamarin e um servidor de aplicativo e fornece um procedimento passo a passo para adquirir credenciais para que seu aplicativo pode usar serviços FCM.



## <a name="overview"></a>Visão geral

Mensagens de nuvem firebase (FCM) é um serviço de plataforma cruzada que controla o envio, roteamento e enfileiramento de mensagens entre aplicativos de servidor e aplicativos cliente móveis. FCM é o sucessor para Google Cloud Messaging (GCM), e ele se baseia no Google executar serviços.

Conforme ilustrado no diagrama a seguir, FCM atua como um intermediário entre remetentes e clientes. Um *aplicativo cliente* é um aplicativo habilitado para FCM que é executado em um dispositivo. O *servidor de aplicativos* (fornecido por você ou sua empresa) é o servidor habilitado FCM que seu aplicativo cliente se comunica com a FCM. Ao contrário do GCM, FCM torna possível para enviar mensagens para aplicativos de cliente diretamente por meio da GUI Firebase Console notificações:

[![FCM fica entre o aplicativo cliente e um servidor de aplicativo](firebase-cloud-messaging-images/01-server-fcm-app-sml.png)](firebase-cloud-messaging-images/01-server-fcm-app.png#lightbox)

Usando FCM, servidores de aplicativo podem enviar mensagens em um único dispositivo, para um grupo de dispositivos ou para um número de dispositivos que estão inscritos em um tópico. Um aplicativo cliente pode usar FCM para assinar mensagens downstream de um servidor de aplicativo (por exemplo, para receber notificações remotas). Para obter mais informações sobre os diferentes tipos de mensagens Firebase, consulte [sobre FCM mensagens](https://firebase.google.com/docs/cloud-messaging/concept-options).



## <a name="firebase-cloud-messaging-in-action"></a>Nuvem firebase mensagens em ação

Quando uma mensagem de downstream é enviada para um aplicativo cliente de um servidor de aplicativo, o servidor de aplicativo envia a mensagem para um *servidor de conexão FCM* fornecido pelo Google; o servidor de conexão FCM, por sua vez, encaminha a mensagem para um dispositivo que esteja executando o aplicativo de cliente. As mensagens podem ser enviadas via HTTP ou [XMPP](https://developers.google.com/cloud-messaging/ccs) (mensagens extensível e protocolo de presença). Porque os aplicativos cliente não estão conectados sempre ou FCM conexão enfileira e armazena as mensagens do servidor, enviá-los para aplicativos cliente, como eles se reconectar e ficam disponíveis em execução. Da mesma forma, FCM será Enfileirar mensagens upstream no aplicativo cliente para o servidor de aplicativos se o servidor de aplicativo não está disponível. Para obter mais informações sobre servidores de conexão FCM, consulte [sobre Firebase mensagens servidor na nuvem](https://firebase.google.com/docs/cloud-messaging/server).

FCM usa as credenciais a seguir para identificar o servidor de aplicativo e o aplicativo cliente e usa essas credenciais para autorizar transações de mensagens por meio de FCM:

-   **ID do remetente** &ndash; o *ID do remetente* é um valor numérico exclusivo que é atribuído quando você criar o projeto Firebase. A ID do remetente é usada para identificar cada servidor de aplicativo que pode enviar mensagens para o aplicativo cliente. A ID do remetente é também o número de projeto; Você pode obter a ID do remetente do Console do Firebase ao registrar o seu projeto. Um exemplo de uma ID do remetente é `496915549731`.

-   **Chave de API** &ndash; o *chave API* fornece o acesso ao servidor de aplicativo nos serviços de Firebase; FCM usa essa chave para autenticar o servidor de aplicativo. Essa credencial é também conhecida como o *chave do servidor* ou *chave de API da Web*. Um exemplo de uma chave de API é `AJzbSyCTcpfRT1YRqbz-jIwp1h06YdauvewGDzk`.

-   **ID do aplicativo** &ndash; a identidade de seu aplicativo de cliente (independente de qualquer dispositivo determinado) que registra para receber mensagens de FCM. Um exemplo de uma ID de aplicativo é `1:415712510732:android:0e1eb7a661af2460`.

-   **Token de registro** &ndash; o *registro Token* (também conhecido como o *ID de instância*) é a identidade FCM do seu aplicativo cliente em um determinado dispositivo. O token de registro é gerado em tempo de execução &ndash; seu aplicativo recebe um token de registro quando ele registra pela primeira vez com FCM durante a execução em um dispositivo. O token de registro autoriza uma instância do aplicativo cliente (em execução no dispositivo específico) para receber mensagens de FCM.
    Um exemplo de um token de registro é `fkBQTHxKKhs:AP91bHuEedxM4xFAUn0z ... JKZS` (uma cadeia de caracteres muito longa).

[Configuração de backup Firebase Cloud Messaging](#setup_fcm) (posteriormente neste guia) fornece instruções detalhadas para criar um projeto e gerar essas credenciais. Quando você cria um novo projeto no [Firebase Console](https://console.firebase.google.com/), chamado de um arquivo de credenciais **google services.json** é criado &ndash; adicionar esse arquivo ao seu projeto de xamarin, conforme explicado em [ Notificações remotas com FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

As seções a seguir explicam como essas credenciais são usadas quando os aplicativos cliente se comunicam com servidores de aplicativos por meio de FCM.


<a name="registration" />

### <a name="registration-with-fcm"></a>Registro com FCM

Um aplicativo cliente deve primeiro registrar com FCM antes de mensagens podem ocorrer. O aplicativo cliente deve concluir as etapas de registro mostradas no diagrama a seguir:

[![Diagrama de etapas de registro de aplicativo](firebase-cloud-messaging-images/02-app-registration-sml.png)](firebase-cloud-messaging-images/02-app-registration.png#lightbox)

1.  O aplicativo cliente contata FCM para obter um token de registro, passando a ID do remetente, a chave de API e a ID do aplicativo para FCM.

2.  FCM retorna um token de registro para o aplicativo cliente.

3.  O aplicativo cliente (opcionalmente) encaminha o token de registro para o servidor de aplicativos.

O servidor de aplicativo armazena em cache o token de registro para comunicações subsequentes com o aplicativo cliente. O servidor de aplicativo pode enviar uma confirmação de volta para o aplicativo cliente para indicar que o token de registro foi recebido. Após esse handshake ocorre, o aplicativo cliente pode receber mensagens (ou enviar mensagens para) o servidor de aplicativos. O aplicativo cliente pode receber um novo token de registro se o token antigo estiver comprometido (consulte [remoto notificações com FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md) para obter um exemplo de como um aplicativo recebe atualizações de token de registro).

Quando o aplicativo cliente não deseja receber mensagens do servidor de aplicativo, ele pode enviar uma solicitação para o servidor de aplicativos para excluir o token de registro. Se o aplicativo cliente é desinstalado de um dispositivo, FCM detecta isso e notifica automaticamente o servidor de aplicativos para excluir o token de registro.



### <a name="downstream-messaging"></a>Mensagens de downstream

O diagrama a seguir ilustra como mensagens de nuvem Firebase armazena e encaminha mensagens downstream:

[![FCM usa armazenamento e avanço para mensagens downstream](firebase-cloud-messaging-images/03-downstream-sml.png)](firebase-cloud-messaging-images/03-downstream.png#lightbox)

Quando o servidor de aplicativo envia uma mensagem de downstream para o aplicativo cliente, ele usa as etapas a seguir como enumerados no diagrama acima:

1.  O servidor de aplicativo envia a mensagem para FCM.

2.  Se o dispositivo do cliente não estiver disponível, o servidor FCM armazena a mensagem na fila de transmissão posterior. As mensagens são retidas no armazenamento FCM para um máximo de 4 semanas (para obter mais informações, consulte [definindo o tempo de vida de uma mensagem](https://firebase.google.com/docs/cloud-messaging/concept-options#ttl)).

3.  Quando o dispositivo do cliente estiver disponível, FCM encaminha a mensagem para o aplicativo cliente no dispositivo.

4.  O aplicativo cliente recebe a mensagem de FCM, processa e exibe para o usuário. Por exemplo, se a mensagem é uma notificação remota, ele é apresentado ao usuário na área de notificação.

Neste cenário de mensagens (onde o servidor de aplicativo envia uma mensagem para um aplicativo cliente único), mensagens podem ser de até 4kB de comprimento.

Para obter informações detalhadas sobre o recebimento de mensagens FCM downstream no Android, consulte [remoto notificações com FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

### <a name="topic-messaging"></a>Mensagens do tópico

*Mensagens do tópico* torna possível para um servidor de aplicativo enviar uma mensagem para vários dispositivos que tem aceitado um tópico específico. Você também pode compor e enviar mensagens do tópico por meio da GUI de notificações do Console Firebase. FCM manipula o roteamento e entrega de mensagens do tópico aos clientes inscritos. Esse recurso pode ser usado para mensagens, como alertas de clima, cotações de ações e manchetes.

[![Diagrama de mensagens do tópico](firebase-cloud-messaging-images/04-topic-messaging-sml.png)](firebase-cloud-messaging-images/04-topic-messaging.png#lightbox)

As etapas a seguir são usadas no sistema de mensagens do tópico (depois que o aplicativo cliente obtém um token de registro, conforme explicado anteriormente):

1.  O aplicativo cliente assina um tópico, enviando uma mensagem de inscrever-se ao FCM.

2.  O servidor de aplicativo envia mensagens do tópico para FCM para distribuição.

3.  FCM encaminha mensagens do tópico para clientes que assinaram esse tópico.

Para obter mais informações sobre as mensagens de tópico Firebase, consulte do Google [tópico mensagens no Android](https://firebase.google.com/docs/cloud-messaging/android/topic-messaging).


<a name="setup_fcm" />

## <a name="setting-up-firebase-cloud-messaging"></a>Configuração de Firebase mensagens de nuvem

Antes de usar os serviços FCM em seu aplicativo, você deve criar um novo projeto (ou importar um projeto existente) por meio de [Firebase Console](https://console.firebase.google.com/). Use as seguintes etapas para criar um projeto Firebase mensagens de nuvem para seu aplicativo:

1.  Entrar a [Firebase Console](https://console.firebase.google.com/) com sua conta do Google (ou seja, o endereço do Gmail) e clique em **criar novo projeto**:

    [![Criar botão novo projeto](firebase-cloud-messaging-images/05-firebase-console-sml.png)](firebase-cloud-messaging-images/05-firebase-console.png#lightbox)

    Se você tiver um projeto existente, clique em **importar um projeto do Google**.

2.  No **criar um projeto** caixa de diálogo, digite o nome do seu projeto e clique em **criar projeto**. No exemplo a seguir, um novo projeto chamado **XamarinFCM** é criada:

    [![Criar uma caixa de diálogo do projeto](firebase-cloud-messaging-images/06-create-a-project-sml.png)](firebase-cloud-messaging-images/06-create-a-project.png#lightbox)

3.  No Console do Firebase **visão geral**, clique em **Firebase adicionar ao seu aplicativo Android**:

    [![Adicionar Firebase ao seu aplicativo Android](firebase-cloud-messaging-images/07-add-firebase-sml.png)](firebase-cloud-messaging-images/07-add-firebase.png#lightbox)

4.  Na próxima tela, insira o nome do pacote do aplicativo. Neste exemplo, o nome do pacote é **com.xamarin.fcmexample**. Esse valor deve corresponder ao nome do pacote do aplicativo Android. Um apelido do aplicativo também pode ser inserido no **apelido do aplicativo** campo:

    [![Inserindo exemplo FCM como o apelido do aplicativo](firebase-cloud-messaging-images/08-package-name-sml.png)](firebase-cloud-messaging-images/08-package-name.png#lightbox)

5.  Se seu aplicativo usa links dinâmicos, convites ou Google Auth, você também deve inserir seu certificado de assinatura de depuração. Para obter mais informações sobre como localizar seu certificado de autenticação, consulte [localizando o armazenamento de chaves MD5 ou SHA1 assinatura](~/android/deploy-test/signing/keystore-signature.md).
    Neste exemplo, o certificado de autenticação for deixado em branco.

6.  Clique em **Adicionar aplicativo**:

    [![Clique no botão Adicionar aplicativo](firebase-cloud-messaging-images/09-add-app-sml.png)](firebase-cloud-messaging-images/09-add-app.png#lightbox)

    Uma chave de API de servidor e uma ID de cliente são gerados automaticamente para o aplicativo. Essas informações são empacotadas em um **google services.json** arquivo é baixado automaticamente quando você clica em **Adicionar aplicativo**.
    Certifique-se de salvar este arquivo em um local seguro.

Para obter um exemplo detalhado de como adicionar **google services.json** para um projeto de aplicativo para receber mensagens de notificação por push FCM no Android, consulte [remoto notificações com FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).



## <a name="for-further-reading"></a>Para leitura adicional

-   Do Google [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/) fornece uma visão geral dos recursos principais do Firebase Cloud Messaging, uma explicação de como ele funciona e instruções de instalação.

-   Do Google [criar solicitações de envio de servidor de aplicativo](https://firebase.google.com/docs/cloud-messaging/send-message) explica como enviar mensagens com o servidor de aplicativo.

-   [6120 RFC](https://tools.ietf.org/html/rfc6120) e [6121 RFC](https://tools.ietf.org/html/rfc6121) explicam e definir as mensagens extensível e o protocolo de presença (XMPP).



## <a name="summary"></a>Resumo

Este artigo fornece uma visão geral do sistema de mensagens de nuvem com a Firebase (FCM). Ele explicado as várias credenciais que são usadas para identificar e autorizar mensagens entre servidores de aplicativos e aplicativos cliente. Ele ilustra o registro e cenários de mensagens downstream, e ele as etapas detalhadas para registrar seu aplicativo com FCM usar FCM serviços.


## <a name="related-links"></a>Links relacionados

- [Mensagens na nuvem do Firebase](https://firebase.google.com/docs/cloud-messaging/)
