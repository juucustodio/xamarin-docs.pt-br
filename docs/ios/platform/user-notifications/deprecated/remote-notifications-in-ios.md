---
title: Notificações por push no iOS
description: Este documento descreve como trabalhar com notificações por push no iOS 9 e versões anteriores. Ele aborda os certificados, registrando-se com as notificações de Gateway de serviço APNS (Apple Push) e muito mais.
ms.prod: xamarin
ms.assetid: 64B3BE6A-A3E2-4B1B-95ED-02D27A8FDAAC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 22ac6a3776e5fa5de2fc238efe90c435190e8005
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832077"
---
# <a name="push-notifications-in-ios"></a>Notificações por push no iOS

> [!IMPORTANT]
> As informações nesta seção referem-se ao iOS 9 e anterior, ele foi deixado aqui para dar suporte a versões anteriores do iOS. Para iOS 10 e posterior, consulte a [guia de estrutura de notificação do usuário](~/ios/platform/user-notifications/index.md) para dar suporte a locais e remotas de notificação em um dispositivo iOS.

Notificações por push devem ser mantidas em breves e contenham apenas dados suficientes para notificar o aplicativo móvel que ele deverá contatar o aplicativo de servidor para uma atualização. Por exemplo, quando novo email chega, o aplicativo de servidor só seria notificar o aplicativo móvel novo email recebido. A notificação não conteria o novo email em si. O aplicativo móvel, em seguida, seria recuperar novos emails do servidor quando ele estava apropriado

No Centro de push de notificações no iOS é o *notificação Gateway serviço APNS (Apple Push)* . Isso é um serviço fornecido pela Apple que é responsável por notificações de roteamentos de um servidor de aplicativos para dispositivos iOS.
A imagem a seguir ilustra a topologia de notificação por push para iOS: ![](remote-notifications-in-ios-images/image4.png "Esta imagem ilustra a topologia de notificação por push para iOS")

Notificações remotas em si são cadeias de caracteres que seguem o formato formatadas em JSON e protocolos especificado na [a carga de notificação](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CreatingtheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH10-SW1) seção o [Local e enviar por Push notificação Programming Guide](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)no [documentação do desenvolvedor do iOS](https://developer.apple.com/devcenter/ios/index.action).

Apple mantém dois ambientes de APNS: uma *área restrita* e uma *produção* ambiente. O ambiente de área restrita destina-se para teste, durante a fase de desenvolvimento e podem ser encontrado em `gateway.sandbox.push.apple.com` na porta TCP 2195. O ambiente de produção destina-se a ser usado em aplicativos que foram implantados e podem ser encontrados em `gateway.push.apple.com` na porta TCP 2195.

## <a name="requirements"></a>Requisitos

Notificação por push deve observar as seguintes regras são determinadas pela arquitetura do APNS:

-  **Limite de mensagem de 256 bytes** -o tamanho de toda a mensagem da notificação não deve exceder 256 bytes.
-  **Nenhuma confirmação de recebimento** -APNS não fornece o remetente com qualquer notificação de que uma mensagem chegou ao destinatário pretendido. Se o dispositivo estiver inacessível e várias sequenciais notificações forem enviadas, todas as notificações, exceto os mais recentes serão perdidas. Somente a notificação mais recente será entregue ao dispositivo.
-  **Cada aplicativo requer um certificado seguro** -comunicação com o APNS deve ser feita por SSL.


## <a name="creating-and-using-certificates"></a>Criando e usando certificados

Cada um dos ambientes mencionados na seção anterior requer seu próprio certificado. Esta seção abordará como criar um certificado, associá-lo a um perfil de provisionamento e, em seguida, obtenha um certificado de troca de informações pessoais para uso com PushSharp.

1.  Para criar um certificados, vá para o iOS Portal de provisionamento no site da Apple, conforme mostrado na seguinte captura de tela (Observe o item de menu de IDs do aplicativo à esquerda):

    [![](remote-notifications-in-ios-images/image5new.png "O Portal de provisionamento no site de maçãs do iOS")](remote-notifications-in-ios-images/image5new.png#lightbox)

2.  Em seguida, navegue até a seção de IDs de aplicativo e criar uma nova ID do aplicativo, conforme mostrado na seguinte captura de tela:

    [![](remote-notifications-in-ios-images/image6new.png "Navegue até a seção de IDs do aplicativo e criar uma nova ID de aplicativo")](remote-notifications-in-ios-images/image6new.png#lightbox)

3.  Quando você clica na **+** botão, você poderá inserir a descrição e um identificador de pacote para a ID do aplicativo, conforme mostrado na seguinte captura de tela:

    [![](remote-notifications-in-ios-images/image7new.png "Insira a descrição e um identificador de pacote para a ID do aplicativo")](remote-notifications-in-ios-images/image7new.png#lightbox)

4. Certifique-se de selecionar **ID do aplicativo explícita** e que o identificador de pacote não termina com um `*` . Isso criará um identificador que é bom para vários aplicativos e certificados de notificação por push devem ser de um único aplicativo.

1. Em serviços de aplicativos, selecione **notificações por Push**:

    [![](remote-notifications-in-ios-images/image8new.png "Selecione as notificações por Push")](remote-notifications-in-ios-images/image8new.png#lightbox)

2. E pressione **enviar** para confirmar o registro da nova ID do aplicativo:

    [![](remote-notifications-in-ios-images/image9new.png "Confirmar registro da nova ID do aplicativo")](remote-notifications-in-ios-images/image9new.png#lightbox)

3.  Em seguida, você deve criar o certificado para a ID do aplicativo. No painel de navegação à esquerda, navegue até **certificados > todos os** e selecione o `+` botão, conforme mostrado na seguinte captura de tela:

    [![](remote-notifications-in-ios-images/image10new.png "Criar o certificado para a ID do aplicativo")](remote-notifications-in-ios-images/image8.png#lightbox)

4. Selecione se você gostaria de usar um certificado de desenvolvimento ou produção:

    [![](remote-notifications-in-ios-images/image11new.png "Selecione um certificado de desenvolvimento ou produção")](remote-notifications-in-ios-images/image11new.png#lightbox)

5. E, em seguida, selecione a nova ID do aplicativo que acabamos de criar:

    [![](remote-notifications-in-ios-images/image12new.png "Selecione a ID do aplicativo acabou de criar")](remote-notifications-in-ios-images/image12new.png#lightbox)

6.  Isso exibirá instruções que o guiará pelo processo de criação de um *solicitação de assinatura de certificado* usando o **acesso ao conjunto de chaves** aplicativo em seu Mac.

7.  Agora que o certificado tiver sido criado, ele deve ser usado como parte do processo de compilação para assinar o aplicativo para que ele pode registrar com o APNs. Isso requer a criação e instalando um perfil de provisionamento que usa o certificado.

8.  Para criar um perfil de provisionamento de desenvolvimento, navegue até a **perfis de provisionamento** seção e siga as etapas para criá-lo, usando a Id do aplicativo que acabamos de criar.

9.  Depois de criar o perfil de provisionamento, abrirá **organizador do Xcode** e atualizá-lo. Se o perfil de provisionamento que você criou não aparecer, pode ser necessário baixar o perfil do Portal de provisionamento do iOS e importá-lo manualmente. A captura de tela a seguir mostra um exemplo do organizador com o perfil de provisionamento adicionado:  
    [![](remote-notifications-in-ios-images/image13new.png "Esta captura de tela mostra um exemplo do organizador com o perfil de provisionamento adicionado")](remote-notifications-in-ios-images/image13new.png#lightbox)

10.  Neste ponto, precisamos configurar o projeto xamarin. IOS para usar esse perfil de provisionamento de recém-criado. Isso é feito na **opções de projeto** caixa de diálogo, em **assinatura do pacote iOS** guia, como mostrado na seguinte captura de tela:  
    [![](remote-notifications-in-ios-images/image11.png "Configurar o projeto xamarin. IOS para usar esse perfil de provisionamento de recém-criado")](remote-notifications-in-ios-images/image11.png#lightbox)

Neste ponto, o aplicativo é configurado para funcionar com notificações por push. No entanto, ainda há mais algumas etapas necessárias com o certificado. Esse certificado está no formato DER que não é compatível com PushSharp, que requer um certificado de troca de informações pessoais (PKCS12). Para converter o certificado para que ele seja utilizável por PushSharp, execute estas etapas finais:

1.  **Baixe o arquivo de certificado** -faça logon no Portal de provisionamento iOS escolhido na guia certificados, selecione o certificado associado com o perfil de provisionamento correto e escolha **baixar** .
1.  **Abrir o conjunto de chaves de acesso** -isso é que o aplicativo é uma interface GUI para o sistema de gerenciamento de senha nos X.
1.  **Importar o certificado** – se o certificado já não estiver instalado, **arquivo... Importar itens** no menu de acesso do conjunto de chaves. Navegue até o certificado que exportou acima e selecioná-lo.
1.  **Exportar o certificado** - expandir o certificado para que a chave privada associada estiver visível, clique com botão direito na chave e escolheu a exportação. Você será solicitado um nome de arquivo e uma senha para o arquivo exportado.

Neste ponto, podemos terminar com certificados. Temos criado um certificado que será usado para assinar aplicativos do iOS e convertidos esse certificado em um formato que pode ser usado com PushSharp em um aplicativo de servidor. Próxima vamos dar uma olhada em como os aplicativos iOS interagem com o APNS.

## <a name="registering-with-apns"></a>Registrando com o APNS

Antes de um iOS o aplicativo pode receber notificação remota que deve ser registrado com o APNS. APNS irá gerar um token de dispositivo exclusivo e retorná-la ao aplicativo iOS. O aplicativo do iOS, em seguida, levar o token do dispositivo e, em seguida, se registrar com o servidor de aplicativos. Depois que tudo isso acontece, o registro é concluído e o servidor de aplicativos pode notificações por push para o dispositivo móvel.

Em teoria, o token do dispositivo pode mudar cada vez que um aplicativo do iOS se registra com o APNS, no entanto, na prática isso não acontece que muitas vezes. Como uma otimização, um aplicativo pode armazenar em cache o token do dispositivo mais recente e atualizar apenas o servidor de aplicativos quando ele é alterado. O diagrama a seguir ilustra o processo de registro e obter um token de dispositivo:

 ![](remote-notifications-in-ios-images/image12.png "Este diagrama ilustra o processo de registro e obter um token de dispositivo")

Registro com o APNS é tratado na `FinishedLaunching` método da classe de representante de aplicativo, chamando `RegisterForRemoteNotificationTypes` atuais `UIApplication` objeto. Quando um aplicativo do iOS se registra com o APNS, ele também deve especificar quais tipos de notificações remotas ele gostaria de receber. Esses tipos de notificação remota são declarados na enumeração `UIRemoteNotificationType`. O trecho de código a seguir está um exemplo de como um aplicativo iOS pode se registrar para receber notificações de alerta e notificação remotas:

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
    var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());

    UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications ();
} else {
    UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
    UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
}
```

A solicitação de registro APNS ocorre em segundo plano – quando a resposta é recebida, iOS chamará o método `RegisteredForRemoteNotifications` no `AppDelegate` de classe e passar o token de dispositivo registrado. O token estará contido em um `NSData` objeto. O trecho de código a seguir mostra como recuperar o token do dispositivo que APNS fornecidos:

```csharp
public override void RegisteredForRemoteNotifications (
UIApplication application, NSData deviceToken)
{
    // Get current device token
    var DeviceToken = deviceToken.Description;
    if (!string.IsNullOrWhiteSpace(DeviceToken)) {
        DeviceToken = DeviceToken.Trim('<').Trim('>');
    }

    // Get previous device token
    var oldDeviceToken = NSUserDefaults.StandardUserDefaults.StringForKey("PushDeviceToken");

    // Has the token changed?
    if (string.IsNullOrEmpty(oldDeviceToken) || !oldDeviceToken.Equals(DeviceToken))
    {
        //TODO: Put your own logic here to notify your server that the device token has changed/been created!
    }

    // Save new device token
    NSUserDefaults.StandardUserDefaults.SetString(DeviceToken, "PushDeviceToken");
}
```

Se o registro falhar por algum motivo (como o dispositivo não está conectado à Internet), iOS chamará `FailedToRegisterForRemoteNotifications` no aplicativo classe delegate. O trecho de código a seguir mostra como exibir um alerta para o usuário informando que o registro falhou:

```csharp
public override void FailedToRegisterForRemoteNotifications (UIApplication application , NSError error)
{
    new UIAlertView("Error registering push notifications", error.LocalizedDescription, null, "OK", null).Show();
}
```

### <a name="device-token-housekeeping"></a>Manutenção de Token do dispositivo

Tokens de dispositivo irá expirar ou ser alterada ao longo do tempo. Devido ao fato, aplicativos de servidor precisará fazer alguma limpeza de casa e limpar esses tokens expirados ou alterados. Quando um aplicativo envia como notificação por push para um dispositivo que tem um token expirado, o APNS registre e salve esse token expirado. Servidores, em seguida, podem consultar APNS para descobrir quais tokens expiraram.

APNS é usado para fornecer um *serviço de comentários* -um ponto de extremidade HTTPS que autentica por meio do certificado que foi criado para enviar por push notificações e envia os dados sobre quais tokens expiraram. Isso foi substituído pela Apple e removido.

Em vez disso, há um novo código de status HTTP para o caso que anteriormente foi relatado pelo serviço de comentários:

> 410 - o token do dispositivo não está mais ativo para o tópico.

Além disso, um novo `timestamp` será chave de dados JSON no corpo da resposta:

> Se o valor a: cabeçalho de status é 410, o valor dessa chave é a última vez em que o APNs confirmado que o token do dispositivo não era válido para o tópico.
>
> Pare de notificações por push até que o dispositivo registra um token com um carimbo de hora posterior com seu provedor.

## <a name="summary"></a>Resumo

Nesta seção apresentam os principais conceitos em torno de notificações por push no iOS. Ele explicou a função da Apple APNS Push Notification Gateway Service (). Então, ele abordou a criação e uso dos certificados de segurança que são essenciais para o APNS. Por fim, este documento concluído com uma discussão sobre como os servidores de aplicativos podem usar o *serviços de comentários* parar o controle de tokens de dispositivo expirados.


## <a name="related-links"></a>Links relacionados

- [Notificações - demonstrando notificações locais e remotas (amostra)](https://developer.xamarin.com/samples/monotouch/Notifications/)
- [Local e notificações por Push para desenvolvedores](https://developer.apple.com/notifications/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UIRemoteNotificationType](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIRemoteNotificationType)
