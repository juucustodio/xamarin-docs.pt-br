---
title: Google Cloud Messaging
description: Google Cloud Messaging (GCM) é um serviço que facilita as mensagens entre aplicativos móveis e aplicativos de servidor. Este artigo fornece uma visão geral de como o GCM funciona e explica como configurar os serviços do Google para que seu aplicativo possa usar o GCM.
ms.prod: xamarin
ms.assetid: DF8EF401-F63D-4BA0-B2C6-B22DF8FD60CB
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/02/2019
ms.openlocfilehash: d5231d57e84d57788f318c8ae04e592da57a0f5d
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84566587"
---
# <a name="google-cloud-messaging"></a>Google Cloud Messaging

> [!WARNING]
> O Google preteriu o GCM a partir de 10 de abril de 2018. Os documentos e projetos de exemplo a seguir podem não ser mais mantidos. As APIs de cliente e servidor GCM do Google serão removidas assim que 29 de maio de 2019. O Google recomenda migrar aplicativos GCM para o firebase Cloud Messaging (FCM). Para obter mais informações sobre a substituição e a migração do GCM, consulte [Google Reterited Cloud Messaging](https://developers.google.com/cloud-messaging/).
>
> Para começar a usar o firebase Cloud Messaging com o Xamarin, consulte [firebase Cloud Messaging](firebase-cloud-messaging.md).

_Google Cloud Messaging (GCM) é um serviço que facilita as mensagens entre aplicativos móveis e aplicativos de servidor. Este artigo fornece uma visão geral de como o GCM funciona e explica como configurar os serviços do Google para que seu aplicativo possa usar o GCM._

[![Logotipo de Google Cloud Messaging](google-cloud-messaging-images/preview-sml.png)](google-cloud-messaging-images/preview.png#lightbox)

Este tópico fornece uma visão geral de alto nível de como Google Cloud Messaging roteia mensagens entre seu aplicativo e um servidor de aplicativos e fornece um procedimento passo a passo para adquirir credenciais para que seu aplicativo possa usar os serviços do GCM.

## <a name="overview"></a>Visão geral

O Google Cloud Messaging (GCM) é um serviço que manipula o envio, o roteamento e a fila de mensagens entre aplicativos de servidor e aplicativos cliente móveis. Um *aplicativo cliente* é um aplicativo habilitado para GCM que é executado em um dispositivo. O *servidor de aplicativos* (fornecido por você ou sua empresa) é o servidor habilitado para GCM ao qual seu aplicativo cliente se comunica por meio do gcm:

[![O GCM reside entre o aplicativo cliente e o servidor de aplicativos](google-cloud-messaging-images/01-server-gcm-app-sml.png)](google-cloud-messaging-images/01-server-gcm-app.png#lightbox)

Usando o GCM, os servidores de aplicativos podem enviar mensagens para um único dispositivo, um grupo de dispositivos ou vários dispositivos que se inscreveram em um tópico. Seu aplicativo cliente pode usar o GCM para assinar mensagens downstream de um servidor de aplicativos (por exemplo, para receber notificações remotas). Além disso, o GCM possibilita que os aplicativos cliente enviem mensagens upstream de volta ao servidor de aplicativos.

## <a name="google-cloud-messaging-in-action"></a>Google Cloud Messaging em ação

Quando as mensagens downstream são enviadas de um servidor de aplicativos para um aplicativo cliente, o servidor de aplicativos envia a mensagem para um *servidor de conexão GCM*; o servidor de conexão GCM, por sua vez, encaminha a mensagem para um dispositivo que está executando seu aplicativo cliente. As mensagens podem ser enviadas por HTTP ou [XMPP](https://firebase.google.com/docs/cloud-messaging/xmpp-server-ref) (protocolo de mensagens extensível e de presença). Como os aplicativos cliente nem sempre estão conectados ou em execução, o servidor de conexão do GCM enfileira e armazena mensagens, enviando-as aos aplicativos cliente à medida que eles se reconectam e ficam disponíveis. Da mesma forma, o GCM enfileirará mensagens de upstream do aplicativo cliente para o servidor de aplicativos, se o servidor de aplicativos não estiver disponível.

O GCM usa as seguintes credenciais para identificar o servidor de aplicativos e seu aplicativo cliente e usa essas credenciais para autorizar transações de mensagens por meio do GCM:

- **Chave** &ndash; de API A *chave de API* dá ao seu servidor de aplicativo acesso ao Google Services; O GCM usa essa chave para autenticar o servidor de aplicativos.
    Antes de poder usar o serviço GCM, você deve primeiro obter uma chave de API do [console do desenvolvedor do Google](https://console.developers.google.com/) criando um *projeto*. A chave de API deve ser mantida segura; para obter mais informações sobre como proteger sua chave de API, consulte [práticas recomendadas para usar com segurança as chaves de API](https://support.google.com/cloud/answer/6310037?hl=en).

- **ID** &ndash; do remetente A *ID do remetente* autoriza o servidor de aplicativos ao seu aplicativo cliente &ndash; é um número exclusivo que identifica o servidor de aplicativos que tem permissão para enviar mensagens ao seu aplicativo cliente.
    A ID do remetente também é o número do projeto; Você Obtém a ID do remetente do console de desenvolvedores do Google ao registrar seu projeto.

- **Token** &ndash; de registro O *token de registro* é a identidade do gcm do seu aplicativo cliente em um determinado dispositivo. O token de registro é gerado em tempo &ndash; de execução seu aplicativo recebe um token de registro quando ele é registrado pela primeira vez com GCM durante a execução em um dispositivo. O token de registro autoriza uma instância do seu aplicativo cliente (em execução nesse dispositivo específico) para receber mensagens do GCM.

- **ID** &ndash; do aplicativo A identidade do seu aplicativo cliente (independente de um determinado dispositivo) que se registra para receber mensagens do GCM. No Android, a ID do aplicativo é o nome do pacote registrado em **AndroidManifest. xml**, como `com.xamarin.gcmexample` .

A [configuração do Google Cloud Messaging](#settingup) (mais adiante neste guia) fornece instruções detalhadas para a criação de um projeto e a geração dessas credenciais.

As seções a seguir explicam como essas credenciais são usadas quando os aplicativos cliente se comunicam com os servidores de aplicativos por meio do GCM.

### <a name="registration-with-gcm"></a>Registro com GCM

Um aplicativo cliente instalado em um dispositivo deve primeiro se registrar no GCM antes que as mensagens possam ocorrer. O aplicativo cliente deve concluir as etapas de registro mostradas no diagrama a seguir:

[![Etapas de registro do aplicativo](google-cloud-messaging-images/02-app-registration-sml.png)](google-cloud-messaging-images/02-app-registration.png#lightbox)

1. O aplicativo cliente entra em contato com o GCM para obter um token de registro, passando a ID de remetente para GCM.

2. O GCM retorna um token de registro para o aplicativo cliente.

3. O aplicativo cliente encaminha o token de registro para o servidor de aplicativos.

O servidor de aplicativos armazena em cache o token de registro para comunicações subsequentes com o aplicativo cliente. Opcionalmente, o servidor de aplicativos pode enviar uma confirmação de volta ao aplicativo cliente para indicar que o token de registro foi recebido. Depois que esse handshake ocorre, o aplicativo cliente pode receber mensagens de (ou enviar mensagens para) o servidor de aplicativos.

Quando o aplicativo cliente não deseja mais receber mensagens do servidor de aplicativos, ele pode enviar uma solicitação ao servidor de aplicativos para excluir o token de registro. Se o aplicativo cliente estiver recebendo mensagens de tópico (explicadas mais adiante neste artigo), ele poderá cancelar a assinatura do tópico.
Se o aplicativo cliente for desinstalado de um dispositivo, o GCM detectará isso e notificará automaticamente o servidor de aplicativos para excluir o token de registro.

### <a name="downstream-messaging"></a>Mensagens downstream

Quando o servidor de aplicativos envia uma mensagem downstream para o aplicativo cliente, ele segue as etapas ilustradas no diagrama a seguir:

[![Armazenamento de mensagens downstream e diagrama de encaminhamento](google-cloud-messaging-images/03-downstream-sml.png)](google-cloud-messaging-images/03-downstream.png#lightbox)

1. O servidor de aplicativos envia a mensagem para o GCM.

2. Se o dispositivo cliente não estiver disponível, o servidor GCM armazenará a mensagem em uma fila para transmissão posterior.

3. Quando o dispositivo cliente está disponível, o GCM envia a mensagem para o aplicativo cliente nesse dispositivo.

4. O aplicativo cliente recebe a mensagem do GCM e a manipula de acordo. Por exemplo, se a mensagem for uma notificação remota, ela será apresentada ao usuário.

Nesse cenário de mensagens (em que o servidor de aplicativos envia uma mensagem a um único aplicativo cliente), as mensagens podem ter até 4 KB de comprimento.

Para obter informações detalhadas (incluindo exemplos de código) sobre o recebimento de mensagens do GCM downstream no Android, consulte [notificações remotas](~/android/data-cloud/google-messaging/remote-notifications-with-gcm.md).

#### <a name="topic-messaging"></a>Mensagens de tópico

O *tópico mensagens* é um tipo de mensagens downstream em que o servidor de aplicativos envia uma única mensagem para vários dispositivos de aplicativo cliente que assinam um tópico (como uma previsão do tempo). As mensagens de tópico podem ter até 2 KB de comprimento e o tópico mensagens dá suporte a até 1 milhão assinaturas por aplicativo. Se o GCM estiver sendo usado somente para mensagens de tópico, o aplicativo cliente não precisará enviar um token de registro para o servidor de aplicativos.

#### <a name="group-messaging"></a>Mensagens de grupo

*Mensagens de grupo* são um tipo de mensagens downstream em que o servidor de aplicativos envia uma única mensagem para vários dispositivos de aplicativo cliente que pertencem a um grupo (por exemplo, um grupo de dispositivos que pertencem a um único usuário). As mensagens de grupo podem ter até 2 KB de comprimento para dispositivos iOS e até 4 KB de comprimento para dispositivos Android. Um grupo é limitado a um máximo de 20 membros.

### <a name="upstream-messaging"></a>Mensagens upstream

Se seu aplicativo cliente se conectar a um servidor que dá suporte a [XMPP](https://firebase.google.com/docs/cloud-messaging/xmpp-server-ref), ele poderá enviar mensagens de volta ao servidor de aplicativos, conforme ilustrado no diagrama a seguir:

[![Diagrama de mensagens upstream](google-cloud-messaging-images/04-upstream-sml.png)](google-cloud-messaging-images/04-upstream.png#lightbox)

1. O aplicativo cliente envia uma mensagem para o servidor de conexão do GCM XMPP.

2. Se o servidor de aplicativos estiver desconectado, o servidor GCM armazenará a mensagem em uma fila para encaminhamento posterior.

3. Quando o servidor de aplicativos é reconectado, o GCM encaminha a mensagem para o servidor de aplicativos.

4. O servidor de aplicativos analisa a mensagem para verificar a identidade do aplicativo cliente e, em seguida, envia um "ACK" para o GCM para confirmar a confirmação da mensagem.

5. O servidor de aplicativos processa a mensagem.

[As mensagens de upstream](https://firebase.google.com/docs/cloud-messaging/xmpp-server-ref#upstream) do Google explicam como estruturar mensagens codificadas em JSON e enviá-las a servidores de aplicativos que executam o servidor de conexão de nuvem baseado em XMPP do Google.

<a name="settingup"></a>

## <a name="setting-up-google-cloud-messaging"></a>Configurando Google Cloud Messaging

Antes de poder usar os serviços do GCM em seu aplicativo, você deve primeiro adquirir credenciais para acesso aos servidores GCM do Google. As seções a seguir descrevem as etapas necessárias para concluir este processo:

### <a name="enable-google-services-for-your-app"></a>Habilitar serviços do Google para seu aplicativo

1. Entre no [console dos desenvolvedores do Google](https://developers.google.com/mobile/add?platform=android) com sua conta do Google (ou seja, seu endereço do Gmail) e crie um novo projeto. Se você tiver um projeto existente, escolha o projeto que você deseja que esteja habilitado para GCM. No exemplo a seguir, um novo projeto chamado **XamarinGCM** é criado:

    [![Criando projeto XamarinGCM](google-cloud-messaging-images/05-create-gcm-app-sml.png)](google-cloud-messaging-images/05-create-gcm-app.png#lightbox)

2. Em seguida, insira o nome do pacote para seu aplicativo (neste exemplo, o nome do pacote é **com. xamarin. gcmexample**) e clique em **continuar para escolher e configurar os serviços**:

    [![Inserindo o nome do pacote](google-cloud-messaging-images/06-package-name-sml.png)](google-cloud-messaging-images/06-package-name.png#lightbox)

    Observe que esse nome de pacote também é a ID do aplicativo para seu aplicativo.

3. A seção **escolher e configurar serviços** lista os serviços do Google que você pode adicionar ao seu aplicativo. Clique em **mensagens de nuvem**:

    [![Escolher mensagens na nuvem](google-cloud-messaging-images/07-choose-gcm-service-sml.png)](google-cloud-messaging-images/07-choose-gcm-service.png#lightbox)

4. Em seguida, clique em **habilitar Google Cloud Messaging**:

    [![Habilitar o sistema de mensagens em nuvem do Google](google-cloud-messaging-images/08-enable-gcm-sml.png)](google-cloud-messaging-images/08-enable-gcm.png#lightbox)

5. Uma **chave de API de servidor** e uma **ID de remetente** são geradas para seu aplicativo. Registre esses valores e clique em **fechar**:

    [![Chave de API do servidor e ID do remetente exibidas](google-cloud-messaging-images/09-get-api-key-and-id-sml.png)](google-cloud-messaging-images/09-get-api-key-and-id.png#lightbox)

    Proteger a chave de API &ndash; ela não é destinada ao uso público. Se a chave de API estiver comprometida, os servidores não autorizados poderão publicar mensagens em aplicativos cliente.
    [As práticas recomendadas para usar com segurança chaves de API](https://support.google.com/cloud/answer/6310037?hl=en) fornecem diretrizes úteis para proteger sua chave de API.

### <a name="view-your-project-settings"></a>Exibir as configurações do projeto

Você pode exibir as configurações do projeto a qualquer momento entrando no [console do Google Cloud](https://console.cloud.google.com/) e selecionando seu projeto. Por exemplo, você pode exibir a **ID do remetente** selecionando seu projeto no menu suspenso na parte superior da página (neste exemplo, o projeto é chamado de **XamarinGCM**). A ID do remetente é o número do projeto, conforme mostrado nesta captura de tela (a ID do remetente é **9349932736**):

[![Exibindo a ID do remetente](google-cloud-messaging-images/10-view-server-id-sml.png)](google-cloud-messaging-images/10-view-server-id.png#lightbox)

Para exibir a **chave de API**, clique em **Gerenciador de API** e, em seguida, clique em **credenciais**:

[![Exibindo a chave de API](google-cloud-messaging-images/11-view-credentials-sml.png)](google-cloud-messaging-images/11-view-credentials.png#lightbox)

## <a name="for-further-reading"></a>Para leitura adicional

- [Rfc 6120](https://tools.ietf.org/html/rfc6120) e [RFC 6121](https://tools.ietf.org/html/rfc6121) explicam e definem o protocolo XMPP (Extensible Messaging and Presence Protocol).

## <a name="summary"></a>Resumo

Este artigo forneceu uma visão geral do Google Cloud Messaging (GCM). Ele explicou as várias credenciais que são usadas para identificar e autorizar mensagens entre servidores de aplicativos e aplicativos cliente. Ele ilustrou os cenários de mensagens mais comuns e detalha as etapas para registrar seu aplicativo no GCM para usar os serviços do GCM.

## <a name="related-links"></a>Links relacionados

- [Mensagens na nuvem](https://developers.google.com/cloud-messaging/)
