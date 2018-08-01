---
title: Notificações por push no iOS
description: Este documento descreve como trabalhar com notificações por push no iOS 9 e versões anteriores. Ele discute os certificados, registrando com o Apple Push notificações Gateway Service (APNS) e muito mais.
ms.prod: xamarin
ms.assetid: 64B3BE6A-A3E2-4B1B-95ED-02D27A8FDAAC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 7bb2a250b9d3cc0c8df02f432330f9fe1dc58f94
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788661"
---
# <a name="push-notifications-in-ios"></a>Notificações por push no iOS

> [!IMPORTANT]
> As informações nesta seção se refere ao iOS 9 e anterior, ele foi deixado aqui para dar suporte a versões anteriores do iOS. Para iOS 10 e posterior, consulte o [guia estrutura de notificação do usuário](~/ios/platform/user-notifications/index.md) para dar suporte a Local e remoto de notificação em um dispositivo iOS.

Notificações por push devem ser mantidas em breves e contenham apenas dados suficientes para notificar o aplicativo móvel que ele deve contatar o aplicativo de servidor para uma atualização. Por exemplo, quando o novo email chega, o aplicativo de servidor somente notifique o aplicativo móvel que o novo email chegou. A notificação não conterá o novo email em si. O aplicativo móvel seria recuperar novos emails do servidor quando é apropriado

No Centro de push de notificações no iOS é o *Apple Push notificação Gateway Service (APNS)*. Este é um serviço fornecido pela Apple que é responsável por notificações de roteamentos de um servidor de aplicativos para dispositivos iOS.
A imagem a seguir ilustra a topologia de notificação por push para iOS: ![ ] (remote-notifications-in-ios-images/image4.png "esta imagem ilustra a topologia de notificação por push para iOS")

Remoto as próprias notificações são cadeias de caracteres que seguem o formato formatadas em JSON e protocolos especificados na [a carga de notificação](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CreatingtheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH10-SW1) seção o [Local e enviar notificação de guia de programação](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)no [documentação do desenvolvedor iOS](https://developer.apple.com/devcenter/ios/index.action).

Apple mantém dois ambientes de APNS: um *Sandbox* e um *produção* ambiente. O ambiente de área restrita destina-se de teste durante a fase de desenvolvimento e pode ser encontrado em `gateway.sandbox.push.apple.com` 2195 da porta TCP. O ambiente de produção destina-se a ser usado em aplicativos que foram implantados e podem ser encontrados em `gateway.push.apple.com` 2195 da porta TCP.

## <a name="requirements"></a>Requisitos

Notificação por push deve observar as seguintes regras são determinadas pela arquitetura do APNS:

-  **Limite de mensagem de 256 bytes** -o tamanho de toda a mensagem da notificação não deve exceder 256 bytes.
-  **Nenhuma confirmação de recebimento** -APNS não fornece o remetente com qualquer notificação de que uma mensagem chegou ao destinatário pretendido. Se o dispositivo estiver inacessível e várias notificações sequenciais forem enviadas, todas as notificações, exceto o mais recente serão perdidas. Somente a notificação mais recente será entregue ao dispositivo.
-  **Cada aplicativo requer um certificado de seguro** -comunicação com APNS deve ser feita por SSL.


## <a name="creating-and-using-certificates"></a>Criando e usando certificados

Cada um dos ambientes mencionados na seção anterior requer seu próprio certificado. Esta seção aborda como criar um certificado, associe-o um perfil de provisionamento e, em seguida, obter um certificado de troca de informações pessoais para uso com PushSharp.

1.  Para criar um certificados vá para o iOS Portal de provisionamento no site da Apple, conforme mostrado na seguinte captura de tela (Observe o item de menu de IDs de aplicativo no lado esquerdo):

    [![](remote-notifications-in-ios-images/image5new.png "O Portal de provisionamento no site de maçãs do iOS")](remote-notifications-in-ios-images/image5new.png#lightbox)

2.  Em seguida, navegue até a seção de IDs de aplicativo e criar uma nova ID de aplicativo, conforme mostrado na seguinte captura de tela:

    [![](remote-notifications-in-ios-images/image6new.png "Navegue até a seção de IDs de aplicativo e criar uma nova ID de aplicativo")](remote-notifications-in-ios-images/image6new.png#lightbox)

3.  Quando você clicar no **+** botão, você poderá inserir a descrição e um identificador de pacote para a ID do aplicativo, conforme mostrado na seguinte captura de tela:

    [![](remote-notifications-in-ios-images/image7new.png "Insira a descrição e um identificador de pacote para a ID de aplicativo")](remote-notifications-in-ios-images/image7new.png#lightbox)

4. Certifique-se de selecionar **explícita ID do aplicativo** e que o identificador de pacote não termina com um `*` . Isso criará um identificador que é bom para vários aplicativos e certificados de notificação por push devem ser de um único aplicativo.

1. Em serviços de aplicativos, selecione **notificações por Push**:

    [![](remote-notifications-in-ios-images/image8new.png "Selecione as notificações por Push")](remote-notifications-in-ios-images/image8new.png#lightbox)

2. E pressione **enviar** para confirmar o registro da nova ID de aplicativo:

    [![](remote-notifications-in-ios-images/image9new.png "Confirmar registro da nova ID de aplicativo")](remote-notifications-in-ios-images/image9new.png#lightbox)

3.  Em seguida, você deve criar o certificado para a ID do aplicativo. No painel de navegação à esquerda, navegue até **certificados > todos os** e selecione o `+` botão, conforme mostrado na seguinte captura de tela:

    [![](remote-notifications-in-ios-images/image10new.png "Criar o certificado para a ID de aplicativo")](remote-notifications-in-ios-images/image8.png#lightbox)

4.  Selecione se você gostaria de usar um certificado de produção ou desenvolvimento:

    [![](remote-notifications-in-ios-images/image11new.png "Selecione um certificado de produção ou desenvolvimento")](remote-notifications-in-ios-images/image11new.png#lightbox)

5. E, em seguida, selecione a nova ID de aplicativo que você criou:

    [![](remote-notifications-in-ios-images/image12new.png "Selecione a nova ID de aplicativo que acabou de criar")](remote-notifications-in-ios-images/image12new.png#lightbox)

6.  Isso exibirá as instruções que o guiará pelo processo de criação de um *solicitação de assinatura de certificado* usando o **ao conjunto de chaves** aplicativo no seu Mac.

7.  Agora que o certificado tiver sido criado, ele deve ser usado como parte do processo de compilação para assinar o aplicativo para que ele pode registrar com APNs. Isso exige criar e instalar um perfil de provisionamento que usa o certificado.

8.  Para criar um perfil de provisionamento de desenvolvimento, navegue até o **perfis de provisionamento** seção e siga as etapas para criar, usando a Id de aplicativo que acabamos de criar.

9.  Depois de criar o perfil de provisionamento, abra **Xcode organizador** e atualizá-lo. Se o perfil de provisionamento que você criou não aparece, pode ser necessário baixar o perfil de Portal de provisionamento iOS e importá-lo manualmente. A captura de tela a seguir mostra um exemplo do organizador com o perfil de provisionamento adicionado:

    [![](remote-notifications-in-ios-images/image13new.png "Esta captura de tela mostra um exemplo do organizador com o perfil de provisionamento adicionado")](remote-notifications-in-ios-images/image13new.png#lightbox)

10.  Neste ponto, é preciso configurar o projeto xamarin para usar esse perfil de provisionamento de recém-criado. Isso é feito de **opções de projeto** caixa de diálogo, em **iOS de assinatura de pacote** guia, como mostra a captura de tela a seguir:

    [![](remote-notifications-in-ios-images/image11.png "Configurar o projeto xamarin para usar esse perfil de provisionamento de recém-criado")](remote-notifications-in-ios-images/image11.png#lightbox)



Neste ponto, o aplicativo é configurado para funcionar com as notificações por push. No entanto, ainda há algumas etapas mais necessárias com o certificado. Este certificado está no formato DER que não é compatível com PushSharp, que requer um certificado de troca de informações pessoais (PKCS12). Para converter o certificado para que seja utilizável por PushSharp, execute estas etapas finais:

1.  **Baixe o arquivo de certificado** -logon para o Portal de provisionamento do iOS escolhido na guia certificados, selecione o certificado associado com o perfil de provisionamento correto e escolha **baixar** .
1.  **Abrir o conjunto de chaves de acesso** -este é o aplicativo é uma interface gráfica para o sistema de gerenciamento de senha nos X.
1.  **Importar o certificado** - se o certificado já não estiver instalado, **arquivo... Importar itens de** no menu de conjunto de chaves de acesso. Navegue até o certificado exportado acima e selecioná-lo.
1.  **Exportar o certificado** - expanda o certificado para a chave privada associada fique visível, com o botão direito na chave e escolher a exportação. Você será solicitado um nome de arquivo e uma senha para o arquivo exportado.

Neste ponto, são realizadas com certificados. Temos criado um certificado que será usado para assinar aplicativos do iOS e convertidos esse certificado em um formato que pode ser usado com PushSharp em um aplicativo de servidor. Avançar vamos dar uma olhada em como os aplicativos iOS interagem com APNS.

## <a name="registering-with-apns"></a>Registrando com APNS

Antes de um iOS aplicativo pode receber notificação remoto que deve ser registrado com APNS. APNS vai gerar um token de dispositivo exclusivo e retorná-la para o aplicativo iOS. O aplicativo iOS, em seguida, executar o token do dispositivo e, em seguida, se registrar com o servidor de aplicativos. Quando isso acontece, registro for concluído e o servidor de aplicativo pode enviar notificações por push para o dispositivo móvel.

Em teoria, o token do dispositivo pode alterar sempre que um aplicativo iOS se registra com APNS, no entanto, na prática, isso não acontece que muitas vezes. Como uma otimização, um aplicativo pode armazenar em cache o token de dispositivo mais recente e atualizar apenas o servidor de aplicativos quando é alterado. O diagrama a seguir ilustra o processo de registro e obter um token de dispositivo:

 ![](remote-notifications-in-ios-images/image12.png "Este diagrama ilustra o processo de registro e obter um token de dispositivo")

Registro de APNS é tratado no `FinishedLaunching` método da classe delegate aplicativo chamando `RegisterForRemoteNotificationTypes` no atual `UIApplication` objeto. Quando um aplicativo do iOS registra com APNS, também deve especificar quais tipos de notificações remotos que deseja receber. Esses tipos de notificação remoto são declarados na enumeração `UIRemoteNotificationType`. O trecho de código a seguir é um exemplo de como um aplicativo iOS pode registrar para receber notificações de alerta e notificação remotas:

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

A solicitação de registro APNS ocorre em segundo plano - quando a resposta é recebida, iOS chamará o método `RegisteredForRemoteNotifications` no `AppDelegate` classe e passar o token de dispositivo registrado. O token estarão contido em um `NSData` objeto. O trecho de código a seguir mostra como recuperar o token do dispositivo que APNS fornecidos:

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

Tokens de dispositivo irá expirar ou alterar ao longo do tempo. Por causa disso, aplicativos de servidor precisará fazer algumas limpeza de casa e limpar esses tokens expirados ou alterados. Quando um aplicativo envia como notificação por push para um dispositivo que tem um token expirado, APNS irá registrar e salvar esse token expirado. Servidores, em seguida, podem consultar APNS para descobrir quais tokens expiraram.

APNS usado para fornecer uma *comentários serviço* -um ponto de extremidade HTTPS que autentica por meio do certificado que foi criado para enviar por push notificações e envia dados sobre quais tokens expiraram. Isso foi substituído pela Apple e removido.

Em vez disso, há um novo código de status HTTP para o caso em que foi relatado anteriormente pelo serviço de comentários:

> 410 - o token do dispositivo não está mais ativo para o tópico.

Além disso, um novo `timestamp` será chave de dados JSON no corpo da resposta:

> Se o valor de: cabeçalho de status é 410, o valor dessa chave é a última vez em que o APNs confirmado que o token do dispositivo não era válido para o tópico.
>
> Pare de enviar notificações até que o dispositivo se registra um token com um carimbo de hora posterior com seu provedor.

## <a name="summary"></a>Resumo

Esta seção introduz os principais conceitos em torno de notificações por push no iOS. Ele explicado a função do Apple Push notificação Gateway Service (APNS). Ele coberto, em seguida, a criação e o uso de certificados de segurança que são essenciais para APNS. Por fim deste documento terminar com uma discussão sobre como os servidores de aplicativos podem usar o *serviços de comentários* parar o controle expirado tokens de dispositivo.


## <a name="related-links"></a>Links relacionados

- [Notificações - demonstrando notificações locais e remotas (exemplo)](https://developer.xamarin.com/samples/monotouch/Notifications/)
- [Local e enviar notificações para desenvolvedores](https://developer.apple.com/notifications/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UIRemoteNotificationType](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIRemoteNotificationType)
