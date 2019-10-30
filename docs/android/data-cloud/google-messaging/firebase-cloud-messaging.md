---
title: Mensagens na nuvem do firebase
description: O FCM (firebase Cloud Messaging) é um serviço que facilita o sistema de mensagens entre aplicativos móveis e aplicativos de servidor. Este artigo fornece uma visão geral de como o FCM funciona e explica como configurar os serviços do Google para que seu aplicativo possa usar o FCM.
ms.prod: xamarin
ms.assetid: E5314D7F-2AAC-40DA-BEBA-27C834F078DD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/31/2018
ms.openlocfilehash: c97c931445122cbaa613b87e3778f4dc9e92f4d0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73023663"
---
# <a name="firebase-cloud-messaging"></a>Mensagens na nuvem do firebase

_O FCM (firebase Cloud Messaging) é um serviço que facilita o sistema de mensagens entre aplicativos móveis e aplicativos de servidor. Este artigo fornece uma visão geral de como o FCM funciona e explica como configurar os serviços do Google para que seu aplicativo possa usar o FCM._

[imagem do firebase herói do sistema de mensagens de nuvem da ![](firebase-cloud-messaging-images/preview.png)](firebase-cloud-messaging-images/preview.png#lightbox)

Este tópico fornece uma visão geral de alto nível de como o firebase Cloud Messaging roteia mensagens entre seu aplicativo Xamarin. Android e um servidor de aplicativos e fornece um procedimento passo a passo para adquirir credenciais para que seu aplicativo possa usar os serviços do FCM.

## <a name="overview"></a>Visão Geral

O FCM (firebase Cloud Messaging) é um serviço de plataforma cruzada que manipula o envio, o roteamento e a fila de mensagens entre aplicativos de servidor e aplicativos cliente móveis. FCM é o sucessor de Google Cloud Messaging (GCM) e é criado em Google Play Services.

Conforme ilustrado no diagrama a seguir, o FCM atua como um intermediário entre os remetentes de mensagens e os clientes. Um *aplicativo cliente* é um aplicativo habilitado para FCM que é executado em um dispositivo. O *servidor de aplicativos* (fornecido por você ou sua empresa) é o servidor habilitado para FCM com o qual seu aplicativo cliente se comunica por meio do FCM. Diferentemente do GCM, o FCM possibilita que você envie mensagens para aplicativos cliente diretamente por meio da GUI de notificações do console do firebase:

[![FCM se situa entre o aplicativo cliente e um servidor de aplicativos](firebase-cloud-messaging-images/01-server-fcm-app-sml.png)](firebase-cloud-messaging-images/01-server-fcm-app.png#lightbox)

Usando o FCM, os servidores de aplicativos podem enviar mensagens para um único dispositivo, para um grupo de dispositivos ou para vários dispositivos que se inscreveram em um tópico. Um aplicativo cliente pode usar FCM para assinar mensagens downstream de um servidor de aplicativos (por exemplo, para receber notificações remotas). Para obter mais informações sobre os diferentes tipos de mensagens firebase, consulte [about FCM messages](https://firebase.google.com/docs/cloud-messaging/concept-options).

## <a name="fcm-in-action"></a>Firebase de mensagens na nuvem em ação

Quando uma mensagem downstream é enviada a um aplicativo cliente de um servidor de aplicativos, o servidor de aplicativos envia a mensagem para um *servidor de conexão FCM* fornecido pelo Google; o servidor de conexão FCM, por sua vez, encaminha a mensagem para um dispositivo que está executando o aplicativo cliente. As mensagens podem ser enviadas por HTTP ou [XMPP](https://developers.google.com/cloud-messaging/ccs) (protocolo de mensagens extensível e de presença). Como os aplicativos cliente nem sempre estão conectados ou em execução, o servidor de conexão FCM enfileira e armazena mensagens, enviando-as aos aplicativos cliente à medida que eles se reconectam e ficam disponíveis. Da mesma forma, o FCM enfileirará mensagens de upstream do aplicativo cliente para o servidor de aplicativos se o servidor de aplicativos não estiver disponível. Para obter mais informações sobre servidores de conexão do FCM, consulte [sobre o firebase Cloud Messaging Server](https://firebase.google.com/docs/cloud-messaging/server).

O FCM usa as seguintes credenciais para identificar o servidor de aplicativos e o aplicativo cliente e usa essas credenciais para autorizar transações de mensagens por meio do FCM:

- <a name="fcm-in-action-sender-id"></a>A **ID do remetente** &ndash; a ID do *remetente* é um valor numérico exclusivo que é atribuído quando você cria o projeto firebase. A ID do remetente é usada para identificar cada servidor de aplicativos que pode enviar mensagens para o aplicativo cliente. A ID do remetente também é o número do projeto; Você Obtém a ID do remetente no console do firebase ao registrar seu projeto. Um exemplo de uma ID de remetente é `496915549731`.

- <a name="fcm-in-action-api-key"></a>**Chave de api** &ndash; a *chave de API* dá ao servidor de aplicativo acesso aos serviços Firebases; O FCM usa essa chave para autenticar o servidor de aplicativos. Essa credencial também é conhecida como a *chave do servidor* ou a *chave da API Web*. Um exemplo de uma chave de API é `AJzbSyCTcpfRT1YRqbz-jIwp1h06YdauvewGDzk`.

- <a name="fcm-in-action-app-id"></a>A **ID do aplicativo** &ndash; a identidade do seu aplicativo cliente (independente de um determinado dispositivo) que se registra para receber mensagens de FCM. Um exemplo de uma ID de aplicativo é `1:415712510732:android:0e1eb7a661af2460`.

- <a name="fcm-in-action-registration-token"></a>O **token de registro** &ndash; o token de *registro* (também conhecido como a ID da *instância*) é a identidade FCM do seu aplicativo cliente em um determinado dispositivo. O token de registro é gerado em tempo de execução &ndash; seu aplicativo recebe um token de registro quando ele se registra pela primeira vez com FCM durante a execução em um dispositivo. O token de registro autoriza uma instância do seu aplicativo cliente (em execução nesse dispositivo específico) para receber mensagens do FCM.
    Um exemplo de token de registro é `fkBQTHxKKhs:AP91bHuEedxM4xFAUn0z ... JKZS` (uma cadeia de caracteres muito longa).

A [configuração do firebase Cloud Messaging](#setup_fcm) (mais adiante neste guia) fornece instruções detalhadas para a criação de um projeto e a geração dessas credenciais. Quando você cria um novo projeto no [console do firebase](https://console.firebase.google.com/), um arquivo de credenciais chamado **Google-Services. json** é criado &ndash; adicionar esse arquivo ao seu projeto Xamarin. Android, conforme explicado em [notificações remotas com FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

As seções a seguir explicam como essas credenciais são usadas quando os aplicativos cliente se comunicam com os servidores de aplicativos por meio do FCM.

<a name="registration" />

### <a name="registration-with-fcm"></a>Registro com FCM

Um aplicativo cliente deve primeiro se registrar no FCM antes que o sistema de mensagens possa ocorrer. O aplicativo cliente deve concluir as etapas de registro mostradas no diagrama a seguir:

[diagrama de etapas de registro do aplicativo![](firebase-cloud-messaging-images/02-app-registration-sml.png)](firebase-cloud-messaging-images/02-app-registration.png#lightbox)

1. O aplicativo cliente entra em contato com FCM para obter um token de registro, passando a ID do remetente, a chave de API e a ID do aplicativo para FCM.

2. FCM retorna um token de registro para o aplicativo cliente.

3. O aplicativo cliente (opcionalmente) encaminha o token de registro para o servidor de aplicativos.

O servidor de aplicativos armazena em cache o token de registro para comunicações subsequentes com o aplicativo cliente. O servidor de aplicativos pode enviar uma confirmação de volta ao aplicativo cliente para indicar que o token de registro foi recebido. Depois que esse handshake ocorre, o aplicativo cliente pode receber mensagens de (ou enviar mensagens para) o servidor de aplicativos. O aplicativo cliente pode receber um novo token de registro se o token antigo for comprometido (consulte [notificações remotas com FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md) para obter um exemplo de como um aplicativo recebe atualizações de token de registro).

Quando o aplicativo cliente não deseja mais receber mensagens do servidor de aplicativos, ele pode enviar uma solicitação ao servidor de aplicativos para excluir o token de registro. Se o aplicativo cliente for desinstalado de um dispositivo, o FCM detectará isso e notificará automaticamente o servidor de aplicativos para excluir o token de registro.

### <a name="downstream-messaging"></a>Mensagens downstream

O diagrama a seguir ilustra como o firebase Cloud Messaging armazena e encaminha mensagens downstream:

[![FCM usa armazenar e encaminhar para mensagens downstream](firebase-cloud-messaging-images/03-downstream-sml.png)](firebase-cloud-messaging-images/03-downstream.png#lightbox)

Quando o servidor de aplicativos envia uma mensagem downstream para o aplicativo cliente, ele usa as seguintes etapas, conforme enumerado no diagrama acima:

1. O servidor de aplicativos envia a mensagem para FCM.

2. Se o dispositivo cliente não estiver disponível, o servidor FCM armazenará a mensagem em uma fila para transmissão posterior. As mensagens são retidas no armazenamento FCM por um máximo de 4 semanas (para obter mais informações, consulte [definindo o ciclo de vida de uma mensagem](https://firebase.google.com/docs/cloud-messaging/concept-options#ttl)).

3. Quando o dispositivo cliente estiver disponível, o FCM encaminhará a mensagem para o aplicativo cliente nesse dispositivo.

4. O aplicativo cliente recebe a mensagem de FCM, a processa e a exibe para o usuário. Por exemplo, se a mensagem for uma notificação remota, ela será apresentada ao usuário na área de notificação.

Nesse cenário de mensagens (em que o servidor de aplicativos envia uma mensagem a um único aplicativo cliente), as mensagens podem ter até 4 KB de comprimento.

Para obter informações detalhadas sobre o recebimento de mensagens FCM de downstream no Android, consulte [notificações remotas com FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

### <a name="topic-messaging"></a>Mensagens de tópico

O *tópico mensagens* possibilita que um servidor de aplicativos envie uma mensagem para vários dispositivos que tenham optado por um tópico específico. Você também pode compor e enviar mensagens de tópico por meio da GUI de notificações do console do firebase. O FCM lida com o roteamento e a entrega de mensagens de tópico para clientes assinados. Esse recurso pode ser usado para mensagens como alertas meteorológicos, cotações de ações e notícias de manchetes.

[diagrama de mensagens do tópico ![](firebase-cloud-messaging-images/04-topic-messaging-sml.png)](firebase-cloud-messaging-images/04-topic-messaging.png#lightbox)

As etapas a seguir são usadas no tópico mensagens (depois que o aplicativo cliente obtém um token de registro, conforme explicado anteriormente):

1. O aplicativo cliente assina um tópico enviando uma mensagem de assinatura para FCM.

2. O servidor de aplicativos envia mensagens de tópico para o FCM para distribuição.

3. O FCM encaminha mensagens de tópico para clientes que assinaram esse tópico.

Para obter mais informações sobre mensagens de tópico do firebase, consulte mensagens de tópico do Google [no Android](https://firebase.google.com/docs/cloud-messaging/android/topic-messaging).

<a name="setup_fcm" />

## <a name="setting-up-firebase-cloud-messaging"></a>Configurando o firebase Cloud Messaging

Antes de poder usar os serviços do FCM em seu aplicativo, você deve criar um novo projeto (ou importar um projeto existente) por meio do [console do firebase](https://console.firebase.google.com/). Use as etapas a seguir para criar um projeto do firebase Cloud Messaging para seu aplicativo:

1. Entre no [console do firebase](https://console.firebase.google.com/) com sua conta do Google (ou seja, seu endereço do Gmail) e clique em **criar novo projeto**:

    [botão ![criar novo projeto](firebase-cloud-messaging-images/05-firebase-console-sml.png)](firebase-cloud-messaging-images/05-firebase-console.png#lightbox)

    Se você tiver um projeto existente, clique em **importar um projeto do Google**.

2. Na caixa de diálogo **criar um projeto** , insira o nome do seu projeto e clique em **criar projeto**. No exemplo a seguir, um novo projeto chamado **XamarinFCM** é criado:

    [caixa de diálogo ![criar um projeto](firebase-cloud-messaging-images/06-create-a-project-sml.png)](firebase-cloud-messaging-images/06-create-a-project.png#lightbox)

3. Na **visão geral**do console do firebase, clique em **Adicionar firebase ao seu aplicativo Android**:

    [![adicionar firebase ao seu aplicativo Android](firebase-cloud-messaging-images/07-add-firebase-sml.png)](firebase-cloud-messaging-images/07-add-firebase.png#lightbox)

4. Na próxima tela, insira o nome do pacote do seu aplicativo. Neste exemplo, o nome do pacote é **com. xamarin. fcmexample**. Esse valor deve corresponder ao nome do pacote do seu aplicativo Android. Um apelido de aplicativo também pode ser inserido no campo de **apelido do aplicativo** :

    [![inserindo o exemplo FCM como o apelido do aplicativo](firebase-cloud-messaging-images/08-package-name-sml.png)](firebase-cloud-messaging-images/08-package-name.png#lightbox)

5. Se seu aplicativo usar links dinâmicos, convites ou Google auth, você também deverá inserir seu certificado de autenticação de depuração. Para obter mais informações sobre como localizar o certificado de autenticação, consulte [localizando a assinatura MD5 ou SHA1 do keystore](~/android/deploy-test/signing/keystore-signature.md).
    Neste exemplo, o certificado de autenticação é deixado em branco.

6. Clique em **Adicionar aplicativo**:

    [![clicar no botão Adicionar aplicativo](firebase-cloud-messaging-images/09-add-app-sml.png)](firebase-cloud-messaging-images/09-add-app.png#lightbox)

    Uma chave de API de servidor e uma ID do cliente são geradas automaticamente para o aplicativo. Essas informações são empacotadas em um arquivo **. JSON do Google-Services** que é baixado automaticamente quando você clica em **Adicionar aplicativo**.
    Certifique-se de salvar esse arquivo em um local seguro.

Para obter um exemplo detalhado de como adicionar o **Google-Services. JSON** a um projeto de aplicativo para receber mensagens de notificação por push do FCM no Android, consulte [notificações remotas com FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

## <a name="for-further-reading"></a>Para leitura adicional

- O [firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/) do Google fornece uma visão geral dos principais recursos do firebase Cloud Messaging, uma explicação de como ele funciona e instruções de instalação.

- [As solicitações de envio do servidor de aplicativos de compilação](https://firebase.google.com/docs/cloud-messaging/send-message) do Google explicam como enviar mensagens com o servidor de aplicativos.

- [Rfc 6120](https://tools.ietf.org/html/rfc6120) e [RFC 6121](https://tools.ietf.org/html/rfc6121) explicam e definem o protocolo XMPP (Extensible Messaging and Presence Protocol).

- [Sobre mensagens FCM](https://firebase.google.com/docs/cloud-messaging/concept-options) descreve os diferentes tipos de mensagens que podem ser enviadas com o firebase Cloud Messaging.

## <a name="summary"></a>Resumo

Este artigo forneceu uma visão geral do FCM (firebase Cloud Messaging). Ele explicou as várias credenciais que são usadas para identificar e autorizar mensagens entre servidores de aplicativos e aplicativos cliente. Ele ilustrou os cenários de registro e de mensagens downstream e detalha as etapas para registrar seu aplicativo no FCM para usar os serviços do FCM.

## <a name="related-links"></a>Links relacionados

- [Mensagens na nuvem do Firebase](https://firebase.google.com/docs/cloud-messaging/)
