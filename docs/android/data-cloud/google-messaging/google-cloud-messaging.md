---
title: Google Cloud Messaging
description: Google Cloud Messaging (GCM) é um serviço que facilita o sistema de mensagens entre aplicativos móveis e aplicativos de servidor. Este artigo fornece uma visão geral de como funciona o GCM e explica como configurar serviços do Google para que seu aplicativo pode usar o GCM.
ms.prod: xamarin
ms.assetid: DF8EF401-F63D-4BA0-B2C6-B22DF8FD60CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/12/2018
ms.openlocfilehash: c2ca567ffcb247622d1b3e8f3e0136c453723b96
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61017572"
---
# <a name="google-cloud-messaging"></a>Google Cloud Messaging

_Google Cloud Messaging (GCM) é um serviço que facilita o sistema de mensagens entre aplicativos móveis e aplicativos de servidor. Este artigo fornece uma visão geral de como funciona o GCM e explica como configurar serviços do Google para que seu aplicativo pode usar o GCM._

[![Logotipo do Google Cloud Messaging](google-cloud-messaging-images/preview-sml.png)](google-cloud-messaging-images/preview.png#lightbox)

Este tópico fornece uma visão geral de como o Google Cloud Messaging roteia mensagens entre seu aplicativo e um servidor de aplicativo e fornece um procedimento passo a passo para adquirir as credenciais para que seu aplicativo pode usar os serviços do GCM.

> [!NOTE]
> Foi substituído pelo GCM [Firebase Cloud Messaging](~/android/data-cloud/google-messaging/firebase-cloud-messaging.md) (FCM).
> GCM server e APIs de cliente [foram preteridos](https://firebase.googleblog.com/2018/04/time-to-upgrade-from-gcm-to-fcm.html) e não estará disponível assim que 11 de abril de 2019.

## <a name="overview"></a>Visão geral

Google Cloud Messaging (GCM) é um serviço que lida com o envio, roteamento e enfileiramento de mensagens entre aplicativos de servidor e aplicativos de cliente móvel. Um *aplicativo cliente* é um aplicativo habilitado para GCM que é executado em um dispositivo. O *do servidor de aplicativo* (fornecido por você ou sua empresa) é o servidor GCM habilitados que seu aplicativo cliente se comunica com por meio do GCM:

[![GCM reside entre o aplicativo cliente e o servidor de aplicativo](google-cloud-messaging-images/01-server-gcm-app-sml.png)](google-cloud-messaging-images/01-server-gcm-app.png#lightbox)

Usando o GCM, servidores de aplicativo podem enviar mensagens para um único dispositivo, um grupo de dispositivos ou um número de dispositivos que estão inscritos em um tópico. Seu aplicativo cliente pode usar o GCM para assinar mensagens downstream de um servidor de aplicativo (por exemplo, para receber notificações remotas). Além disso, o GCM possibilita para aplicativos de cliente enviar mensagens upstream para o servidor de aplicativo.

Para obter informações sobre como implementar um servidor de aplicativo para o GCM, consulte [sobre o servidor de Conexão do GCM](https://developers.google.com/cloud-messaging/server).



## <a name="google-cloud-messaging-in-action"></a>Google Cloud Messaging em ação

Quando mensagens downstream são enviadas de um servidor de aplicativo para um aplicativo cliente, o servidor de aplicativo envia a mensagem para um *o servidor de conexão do GCM*; o servidor de conexão do GCM, por sua vez, encaminha a mensagem para um dispositivo que está executando o aplicativo cliente. As mensagens podem ser enviadas por HTTP ou [XMPP](https://developers.google.com/cloud-messaging/ccs) (mensagens extensível e protocolo de presença). Porque os aplicativos de cliente nem sempre estão conectados ou em execução, os GCM conexão servidor enfileira e repositórios de mensagens, enviá-los para aplicativos cliente, como elas se reconectar e se tornam disponíveis. Da mesma forma, GCM irá Enfileirar mensagens upstream do aplicativo cliente para o servidor de aplicativo se o servidor de aplicativo não está disponível.

GCM usa as credenciais a seguir para identificar o servidor de aplicativo e seu aplicativo cliente, e usa essas credenciais para autorizar transações de mensagens por meio do GCM:

-   **Chave de API** &ndash; as *chave de API* dá acesso ao seu aplicativo servidor aos serviços do Google; GCM usa essa chave para autenticar o servidor de aplicativo.
    Antes de usar o serviço GCM, você deve primeiro obter uma chave de API do [Console do desenvolvedor do Google](https://console.developers.google.com/) criando um *projeto*. A chave de API devem ser mantida segura; Para obter mais informações sobre como proteger sua chave de API, consulte [práticas recomendadas para usar com segurança as chaves de API](https://support.google.com/cloud/answer/6310037?hl=en).

-   **ID do remetente** &ndash; as *ID de remetente* autoriza o servidor de aplicativo para seu aplicativo cliente &ndash; é um número exclusivo que identifica o servidor de aplicativo que tem permissão para enviar mensagens para seu aplicativo cliente.
    A ID de remetente também é o número do projeto; Você pode obter a ID de remetente do Console de desenvolvedores do Google ao registrar o seu projeto.

-   **Token de registro** &ndash; as *registro do Token* é a identidade do GCM do aplicativo cliente em um determinado dispositivo. O token de registro é gerado em tempo de execução &ndash; seu aplicativo recebe um token de registro quando ele registra pela primeira vez com o GCM durante a execução em um dispositivo. O token de registro autoriza uma instância do aplicativo cliente (em execução nesse dispositivo específico) para receber mensagens do GCM.

-   **ID do aplicativo** &ndash; a identidade do aplicativo cliente (independente de qualquer determinado dispositivo) que registra para receber mensagens do GCM. No Android, a ID do aplicativo é o nome do pacote registrado no **androidmanifest. XML**, como `com.xamarin.gcmexample`.

[Configuração de backup de Google Cloud Messaging](#settingup) (mais adiante neste guia) fornece instruções detalhadas para criar um projeto e gerar essas credenciais.

As seções a seguir explicam como essas credenciais são usadas quando os aplicativos cliente se comunicam com servidores de aplicativo por meio do GCM.



### <a name="registration-with-gcm"></a>Registro com o GCM

Um aplicativo de cliente instalado em um dispositivo deve primeiro registrar no GCM antes de mensagens podem ocorrer. O aplicativo cliente deve concluir as etapas de registro mostradas no diagrama a seguir:

[![Etapas de registro de aplicativo](google-cloud-messaging-images/02-app-registration-sml.png)](google-cloud-messaging-images/02-app-registration.png#lightbox)

1.  O aplicativo cliente entra em contato com o GCM para obter um token de registro, passando a ID de remetente para o GCM.

2.  GCM retorna um token de registro para o aplicativo cliente.

3.  O aplicativo cliente encaminha o token de registro para o servidor de aplicativo.

O servidor de aplicativos armazena em cache o token de registro para comunicações subsequentes com o aplicativo cliente. Opcionalmente, o servidor de aplicativo pode enviar uma confirmação ao aplicativo cliente para indicar que o token de registro foi recebido. Após esse handshake, o aplicativo cliente pode receber mensagens da (ou enviar mensagens para) do servidor de aplicativos.

Quando o aplicativo cliente não deseja receber mensagens do servidor de aplicativo, ele pode enviar uma solicitação para o servidor de aplicativo para excluir o token de registro. Se o aplicativo cliente está recebendo mensagens de tópico (explicadas neste artigo), ele pode cancelar a assinatura do tópico.
Se o aplicativo cliente seja desinstalado de um dispositivo, o GCM detecta isso e notifica automaticamente do servidor de aplicativos para excluir o token de registro.

Do Google [aplicativos de cliente registrando](https://developers.google.com/cloud-messaging/registration) explica o processo de registro em mais detalhes; ele explica o cancelamento do registro e cancelamento de inscrição e descreve o processo de cancelamento de registro quando um aplicativo cliente está desinstalado.



### <a name="downstream-messaging"></a>Mensagens de downstream

Quando o servidor de aplicativo envia uma mensagem de downstream para o aplicativo cliente, ele segue as etapas ilustradas no diagrama a seguir:

[![Diagrama de encaminhamento e repositório de mensagens downstream](google-cloud-messaging-images/03-downstream-sml.png)](google-cloud-messaging-images/03-downstream.png#lightbox)

1.  O servidor de aplicativo envia a mensagem para o GCM.

2.  Se o dispositivo do cliente não estiver disponível, o servidor GCM armazena a mensagem em uma fila para transmissão posterior.

3.  Quando o dispositivo do cliente estiver disponível, o GCM envia a mensagem para o aplicativo cliente nesse dispositivo.

4.  O aplicativo cliente recebe a mensagem do GCM e manipula adequadamente. Por exemplo, se a mensagem for uma notificação remota, ela é apresentada ao usuário.

Nesse cenário de sistema de mensagens (em que o servidor de aplicativo envia uma mensagem para um aplicativo cliente única), mensagens podem ser de até 4kB de comprimento.

Para obter informações detalhadas (incluindo exemplos de código) sobre o recebimento de mensagens GCM downstream no Android, consulte [notificações remotas](~/android/data-cloud/google-messaging/remote-notifications-with-gcm.md).


#### <a name="topic-messaging"></a>Mensagens de tópico

*Mensagens de tópico* é um tipo de sistema de mensagens downstream onde o servidor de aplicativo envia uma única mensagem para vários dispositivos de aplicativos cliente que assinem um tópico (por exemplo, uma previsão do tempo). Mensagens do tópico podem ser até 2KB de comprimento e mensagens de tópico oferece suporte a assinaturas de até um milhão por aplicativo. Se o GCM está sendo usado apenas para mensagens de tópico, o aplicativo cliente não é necessário para enviar um token de registro para o servidor de aplicativo. Do Google [implementar mensagens do tópico](https://developers.google.com/cloud-messaging/topic-messaging) explica como enviar mensagens de um servidor de aplicativo para vários dispositivos que assinem um tópico específico.



#### <a name="group-messaging"></a>Grupo de mensagens

*Agrupar mensagens* é um tipo de sistema de mensagens downstream onde o servidor de aplicativo envia uma única mensagem para vários dispositivos de aplicativos cliente que pertencem a um grupo (por exemplo, um grupo de dispositivos que pertencem a um único usuário). Mensagens de grupo podem ser de até 2KB de comprimento para dispositivos iOS e até 4KB de comprimento para dispositivos Android. Um grupo é limitado a um máximo de 20 membros. Do Google [mensagens do grupo de dispositivos](https://developers.google.com/cloud-messaging/notifications) explica como servidores de aplicativo podem enviar uma única mensagem para várias instâncias do aplicativo cliente em execução em dispositivos que pertencem a um grupo.


### <a name="upstream-messaging"></a>Mensagens upstream

Se seu aplicativo cliente se conecta a um servidor que oferece suporte a [XMPP](https://developers.google.com/cloud-messaging/ccs), ele pode enviar mensagens para o servidor de aplicativo, conforme ilustrado no diagrama a seguir:

[![Diagrama de sistema de mensagens upstream](google-cloud-messaging-images/04-upstream-sml.png)](google-cloud-messaging-images/04-upstream.png#lightbox)

1.  O aplicativo cliente envia uma mensagem para o servidor de conexão XMPP GCM.

2.  Se o servidor de aplicativo é desconectado, o servidor GCM armazena a mensagem em uma fila para encaminhamento mais tarde.

3.  Quando o servidor de aplicativo é reconectado, o GCM encaminha a mensagem para o servidor de aplicativo.

4.  O servidor de aplicativo analisa a mensagem para verificar a identidade do aplicativo cliente, em seguida, ele envia uma "confirmação" para o GCM para confirmar o recebimento de mensagem.

5.  O servidor de aplicativo processa a mensagem.

Do Google [mensagens Upstream](https://developers.google.com/cloud-messaging/ccs#upstream) explica como estruturar as mensagens codificadas em JSON e enviá-los para os servidores de aplicativos que executam o servidor de Conexão de nuvem com base em XMPP do Google.


<a name="settingup" />

## <a name="setting-up-google-cloud-messaging"></a>Como configurar o Google Cloud Messaging

Antes de usar serviços do GCM em seu aplicativo, você deve primeiramente adquirir credenciais para acessar servidores do GCM do Google. As seções a seguir descrevem as etapas necessárias para concluir esse processo:



### <a name="enable-google-services-for-your-app"></a>Habilitar os serviços do Google para seu aplicativo

1.  Entrar a [Console de desenvolvedores do Google](https://developers.google.com/mobile/add?platform=android) com o Google, conta (ou seja, seu email do Gmail) e crie um novo projeto. Se você tiver um projeto existente, escolha o projeto que você deseja se tornar habilitado pelo GCM. No exemplo a seguir, um novo projeto chamado **XamarinGCM** é criado:

    [![Criando projeto XamarinGCM](google-cloud-messaging-images/05-create-gcm-app-sml.png)](google-cloud-messaging-images/05-create-gcm-app.png#lightbox)

2.  Em seguida, insira o nome do pacote do aplicativo (neste exemplo, é o nome do pacote **com.xamarin.gcmexample**) e clique em **continuar para escolher e configurar serviços**:

    [![Inserir o nome do pacote](google-cloud-messaging-images/06-package-name-sml.png)](google-cloud-messaging-images/06-package-name.png#lightbox)

    Observe que esse nome de pacote também é a ID do aplicativo para seu aplicativo.

3.  O **escolher e configurar serviços** seção lista os serviços do Google que você pode adicionar ao seu aplicativo. Clique em **Cloud Messaging**:

    [![Escolha a nuvem de mensagens](google-cloud-messaging-images/07-choose-gcm-service-sml.png)](google-cloud-messaging-images/07-choose-gcm-service.png#lightbox)

4.  Em seguida, clique em **habilitar o GOOGLE CLOUD MESSAGING**:

    [![Habilitar o Google Cloud Messaging](google-cloud-messaging-images/08-enable-gcm-sml.png)](google-cloud-messaging-images/08-enable-gcm.png#lightbox)

5.  Um **chave de API do servidor** e uma **ID de remetente** são gerados para seu aplicativo. Registrar esses valores e clique em **fechar**:

    [![Chave de API do servidor e a ID de remetente exibido](google-cloud-messaging-images/09-get-api-key-and-id-sml.png)](google-cloud-messaging-images/09-get-api-key-and-id.png#lightbox)

    Proteger a chave de API &ndash; não se destina para uso público. Se a chave de API estiver comprometida, servidores não autorizados podem publicar mensagens em aplicativos cliente.
    [Práticas recomendadas para usar com segurança as chaves de API](https://support.google.com/cloud/answer/6310037?hl=en) fornece diretrizes úteis para proteger sua chave de API.



### <a name="view-your-project-settings"></a>Exibir as configurações do projeto

Você pode exibir as configurações do projeto a qualquer momento depois de entrar na [Google Cloud Console](https://console.cloud.google.com/) e selecionando seu projeto. Por exemplo, você pode exibir o **ID do remetente** selecionando o projeto no menu suspenso na parte superior da página (neste exemplo, o projeto é denominado **XamarinGCM**). A ID de remetente é o número do projeto, conforme mostrado nesta captura de tela (é o ID de remetente **9349932736**):

[![Exibindo o ID de remetente](google-cloud-messaging-images/10-view-server-id-sml.png)](google-cloud-messaging-images/10-view-server-id.png#lightbox)

Para exibir o **chave de API**, clique em **Gerenciador de API** e, em seguida, clique em **credenciais**:

[![Exibindo a chave de API](google-cloud-messaging-images/11-view-credentials-sml.png)](google-cloud-messaging-images/11-view-credentials.png#lightbox)



## <a name="for-further-reading"></a>Para leitura adicional

-   Do Google [aplicativos de cliente registrando](https://developers.google.com/cloud-messaging/registration) descreve o processo de registro de cliente mais detalhadamente e fornece informações sobre como configurar a repetição automática e manter o estado de registro em sincronia.

-   [RFC 6120](https://tools.ietf.org/html/rfc6120) e [6121 RFC](https://tools.ietf.org/html/rfc6121) explicar e definir as mensagens extensível e o protocolo de presença (XMPP).



## <a name="summary"></a>Resumo

Este artigo fornece uma visão geral do Google Cloud Messaging (GCM). Ele explicou as várias credenciais que são usadas para identificar e autorizar o sistema de mensagens entre aplicativos de cliente e servidores de aplicativo. Ele ilustrados os cenários de mensagens mais comuns, e ele as etapas detalhadas para registrar seu aplicativo com o GCM para usar os serviços do GCM.


## <a name="related-links"></a>Links relacionados

- [Mensagens de nuvem](https://developers.google.com/cloud-messaging/)
