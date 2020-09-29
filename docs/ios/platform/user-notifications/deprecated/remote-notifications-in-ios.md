---
title: Notificações por push no iOS
description: Este documento descreve como trabalhar com notificações por push no iOS 9 e versões anteriores. Ele aborda os certificados, o registro com o serviço de gateway de notificações por push da Apple (APNS) e muito mais.
ms.prod: xamarin
ms.assetid: 64B3BE6A-A3E2-4B1B-95ED-02D27A8FDAAC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: f9ef6e833fe48bb2b124f02d741a39e4141e56e6
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436355"
---
# <a name="push-notifications-in-ios"></a>Notificações por push no iOS

> [!IMPORTANT]
> As informações contidas nesta seção pertencem ao iOS 9 e anteriores, foram deixadas aqui para dar suporte a versões mais antigas do iOS. Para o iOS 10 e posterior, consulte o [guia da estrutura de notificação do usuário](~/ios/platform/user-notifications/index.md) para dar suporte a notificações locais e remotas em um dispositivo IOS.

As notificações por push devem ser resumidas e conter apenas dados suficientes para notificar o aplicativo móvel de que ele deve contatar o aplicativo de servidor para uma atualização. Por exemplo, quando um novo email chegar, o aplicativo de servidor somente notificaria o aplicativo móvel que o novo email chegou. A notificação não conteria o novo email. O aplicativo móvel então recuperaria os novos emails do servidor quando fosse apropriado

No centro das notificações por push no iOS está o *serviço de gateway de notificação por push da Apple (APNS)*. Esse é um serviço fornecido pela Apple que é responsável por rotear notificações de um servidor de aplicativos para dispositivos iOS.
A imagem a seguir ilustra a topologia de notificação por push para iOS: ![ essa imagem ilustra a topologia de notificação por push para IOS](remote-notifications-in-ios-images/image4.png)

As próprias notificações remotas são cadeias de caracteres formatadas em JSON que aderem ao formato e aos protocolos especificados na seção [carga de notificação](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CreatingtheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH10-SW1) do guia de [programação de notificação por push e local](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) na [documentação do desenvolvedor do IOS](https://developer.apple.com/devcenter/ios/index.action).

A Apple mantém dois ambientes de APNS: uma *área restrita* e um ambiente de *produção* . O ambiente de área restrita destina-se a testes durante a fase de desenvolvimento e pode ser encontrado em `gateway.sandbox.push.apple.com` na porta TCP 2195. O ambiente de produção deve ser usado em aplicativos que foram implantados e podem ser encontrados em `gateway.push.apple.com` na porta TCP 2195.

## <a name="requirements"></a>Requisitos

A notificação por push deve observar as seguintes regras que são ditadas pela arquitetura do APNS:

- **limite de mensagens de 256 bytes** -o tamanho completo da mensagem da notificação não deve exceder 256 bytes.
- **Nenhuma confirmação** de confirmação-o APNS não fornece ao remetente nenhuma notificação de que uma mensagem o tenha feito para o destinatário pretendido. Se o dispositivo estiver inacessível e várias notificações sequenciais forem enviadas, todas as notificações, exceto as mais recentes, serão perdidas. Somente a notificação mais recente será entregue ao dispositivo.
- **Cada aplicativo requer um certificado seguro** -a comunicação com o APNS deve ser feita em SSL.

## <a name="creating-and-using-certificates"></a>Criando e usando certificados

Cada um dos ambientes mencionados na seção anterior requer seu próprio certificado. Esta seção explicará como criar um certificado, associá-lo a um perfil de provisionamento e obter um certificado de troca de informações pessoais para uso com o PushSharp.

1. Para criar certificados, vá para o portal de provisionamento do iOS no site da Apple, conforme mostrado na captura de tela a seguir (Observe o item de menu IDs do aplicativo à esquerda):

    [![O portal de provisionamento do iOS no site do maçãs](remote-notifications-in-ios-images/image5new.png)](remote-notifications-in-ios-images/image5new.png#lightbox)

2. Em seguida, navegue até a seção da ID do aplicativo e crie uma nova ID do aplicativo, conforme mostrado na seguinte captura de tela:

    [![Navegue até a seção IDs do aplicativo e crie uma nova ID do aplicativo](remote-notifications-in-ios-images/image6new.png)](remote-notifications-in-ios-images/image6new.png#lightbox)

3. Ao clicar no **+** botão, você poderá inserir a descrição e um identificador de pacote para a ID do aplicativo, conforme mostrado na próxima captura de tela:

    [![Insira a descrição e um identificador de pacote para a ID do aplicativo](remote-notifications-in-ios-images/image7new.png)](remote-notifications-in-ios-images/image7new.png#lightbox)

4. Certifique-se de selecionar **ID do aplicativo explícito** e que o identificador do pacote não termine com um  `*` . Isso criará um identificador que é bom para vários aplicativos e os certificados de notificação por push devem ser para um único aplicativo.

5. Em serviços de aplicativos, selecione **notificações por push**:

    [![Selecionar notificações por push](remote-notifications-in-ios-images/image8new.png)](remote-notifications-in-ios-images/image8new.png#lightbox)

6. E pressione **Enviar** para confirmar o registro da nova ID do aplicativo:

    [![Confirme o registro da nova ID do aplicativo](remote-notifications-in-ios-images/image9new.png)](remote-notifications-in-ios-images/image9new.png#lightbox)

7. Em seguida, você deve criar o certificado para a ID do aplicativo. No painel de navegação à esquerda, navegue até **certificados > todos** e selecione o `+` botão, conforme mostrado na seguinte captura de tela:

    [![Criar o certificado para a ID do aplicativo](remote-notifications-in-ios-images/image10new.png)](remote-notifications-in-ios-images/image8.png#lightbox)

8. Selecione se você deseja usar um certificado de desenvolvimento ou de produção:

    [![Selecionar um certificado de produção ou de desenvolvimento](remote-notifications-in-ios-images/image11new.png)](remote-notifications-in-ios-images/image11new.png#lightbox)

9. Em seguida, selecione a nova ID do aplicativo que acabamos de criar:

    [![Selecione a nova ID do aplicativo recém-criada](remote-notifications-in-ios-images/image12new.png)](remote-notifications-in-ios-images/image12new.png#lightbox)

10. Isso exibirá instruções que o guiarão pelo processo de criação de uma  *solicitação de assinatura de certificado* usando o aplicativo de acesso do conjunto de  **chaves** no seu Mac.

11. Agora que o certificado foi criado, ele deve ser usado como parte do processo de compilação para assinar o aplicativo para que ele possa se registrar no APNs. Isso requer a criação e a instalação de um perfil de provisionamento que usa o certificado.

12. Para criar um perfil de provisionamento de desenvolvimento, navegue até a seção **perfis de provisionamento** e siga as etapas para criá-lo, usando a ID do aplicativo que acabamos de criar.

13. Depois de criar o perfil de provisionamento, abra o  **organizador do Xcode** e atualize-o. Se o perfil de provisionamento criado não aparecer, talvez seja necessário baixar o perfil do portal de provisionamento do iOS e importá-lo manualmente. A captura de tela a seguir mostra um exemplo do organizador com o perfil de provisão adicionado:  
    [![Esta captura de tela mostra um exemplo do organizador com o perfil de provisionamento adicionado](remote-notifications-in-ios-images/image13new.png)](remote-notifications-in-ios-images/image13new.png#lightbox)

14. Neste ponto, precisamos configurar o projeto Xamarin. iOS para usar esse perfil de provisionamento criado recentemente. Isso é feito na caixa de diálogo **Opções do projeto** , na guia assinatura do  **pacote do IOS** , conforme mostrado na seguinte captura de tela:  
    [![Configurar o projeto Xamarin. iOS para usar esse perfil de provisionamento recém-criado](remote-notifications-in-ios-images/image11.png)](remote-notifications-in-ios-images/image11.png#lightbox)

Neste ponto, o aplicativo está configurado para trabalhar com notificações por push. No entanto, ainda há mais algumas etapas necessárias com o certificado. Esse certificado está em formato DER que não é compatível com PushSharp, que requer um certificado de troca de informações pessoais (PKCS12). Para converter o certificado para que ele possa ser usado pelo PushSharp, execute estas etapas finais:

1. **Baixe o arquivo de certificado** -logon no portal de provisionamento do IOS, escolha a guia certificados, selecione o certificado associado ao perfil de provisionamento correto e escolha  **baixar** .
1. **Abrir acesso** ao conjunto de chaves-este é um aplicativo da interface gráfica do sistema de gerenciamento de senhas no os X.
1. **Importar o certificado** -se o certificado ainda não estiver instalado,  **arquivo... Importar itens** do menu acesso do conjunto de chaves. Navegue até o certificado que foi exportado acima e selecione-o.
1. **Exportar o certificado** -expanda o certificado para que a chave privada associada esteja visível, clique com o botão direito do mouse na chave e escolha exportar. Será solicitado um nome de arquivo e uma senha para o arquivo exportado.

Neste ponto, fizemos certificados. Criamos um certificado que será usado para assinar aplicativos iOS e converter esse certificado em um formato que possa ser usado com PushSharp em um aplicativo de servidor. Em seguida, vamos ver como os aplicativos iOS interagem com o APNS.

## <a name="registering-with-apns"></a>Registrando com o APNS

Antes que um aplicativo iOS possa receber notificação remota, ele deve se registrar no APNS. O APNS irá gerar um token de dispositivo exclusivo e retorná-lo ao aplicativo iOS. O aplicativo iOS usará então o token do dispositivo e se registrará no servidor de aplicativos. Quando tudo isso acontecer, o registro será concluído e o servidor de aplicativos poderá enviar notificações por push para o dispositivo móvel.

Teoricamente, o token do dispositivo pode ser alterado cada vez que um aplicativo iOS se registra com o APNS, no entanto, na prática, isso não acontece com frequência. Como uma otimização, um aplicativo pode armazenar em cache o token de dispositivo mais recente e atualizar apenas o servidor de aplicativos quando ele mudar. O diagrama a seguir ilustra o processo de registro e obtenção de um token de dispositivo:

 ![Este diagrama ilustra o processo de registro e obtenção de um token de dispositivo](remote-notifications-in-ios-images/image12.png)

O registro com APNS é tratado no `FinishedLaunching` método da classe de representante do aplicativo chamando `RegisterForRemoteNotificationTypes` no objeto atual `UIApplication` . Quando um aplicativo iOS é registrado com o APNS, ele também deve especificar os tipos de notificações remotas que gostaria de receber. Esses tipos de notificação remota são declarados na enumeração `UIRemoteNotificationType` . O trecho de código a seguir é um exemplo de como um aplicativo iOS pode se registrar para receber notificações remotas de alerta e notificação:

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

A solicitação de registro do APNS ocorre em segundo plano – quando a resposta é recebida, o iOS chamará o método `RegisteredForRemoteNotifications` na `AppDelegate` classe e passará o token do dispositivo registrado. O token estará contido em um `NSData` objeto. O trecho de código a seguir mostra como recuperar o token de dispositivo fornecido pelo APNS:

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

Se o registro falhar por algum motivo (como o dispositivo não está conectado à Internet), o iOS será chamado `FailedToRegisterForRemoteNotifications` na classe de representante do aplicativo. O trecho de código a seguir mostra como exibir um alerta para o usuário informando que o registro falhou:

```csharp
public override void FailedToRegisterForRemoteNotifications (UIApplication application , NSError error)
{
    new UIAlertView("Error registering push notifications", error.LocalizedDescription, null, "OK", null).Show();
}
```

### <a name="device-token-housekeeping"></a>Manutenção do token do dispositivo

Os tokens de dispositivo expirarão ou serão alterados ao longo do tempo. Por causa desse fato, os aplicativos de servidor precisarão fazer uma limpeza doméstica e limpar esses tokens expirados ou alterados. Quando um aplicativo envia como notificação por push para um dispositivo que tem um token expirado, o APNS registrará e salvará esse token expirado. Em seguida, os servidores podem consultar o APNS para descobrir quais tokens expiraram.

APNS usado para fornecer um *serviço de comentários* – um ponto de extremidade HTTPS que autentica por meio do certificado que foi criado para enviar notificações por push e envia de volta dados sobre quais tokens expiraram. Isso foi preterido pela Apple e removido.

Em vez disso, há um novo código de status HTTP para o caso que foi relatado anteriormente pelo serviço de comentários:

> 410-o token do dispositivo não está mais ativo para o tópico.

Além disso, uma nova `timestamp` chave de dados JSON estará no corpo da resposta:

> Se o valor no cabeçalho: status for 410, o valor dessa chave será a última vez em que o APNs confirmou que o token do dispositivo não era mais válido para o tópico.
>
> Pare de enviar notificações até que o dispositivo registre um token com um carimbo de data/hora posterior com seu provedor.

## <a name="summary"></a>Resumo

Esta seção apresenta os principais conceitos que envolvem notificações por push no iOS. Ele explicou a função do serviço de gateway de notificação por push da Apple (APNS). Em seguida, ele abordou a criação e o uso dos certificados de segurança que são essenciais para o APNS. Por fim, este documento foi concluído com uma discussão sobre como os servidores de aplicativos podem usar os *serviços de comentários* para interromper o rastreamento de tokens de dispositivo expirados.

## <a name="related-links"></a>Links Relacionados

- [Notificações – demonstrando notificações locais e remotas (exemplo)](/samples/xamarin/ios-samples/notifications)
- [Notificações locais e por push para desenvolvedores](https://developer.apple.com/notifications/)
- [UIApplication](/dotnet/api/uikit.uiapplication)
- [UIRemoteNotificationType](/dotnet/api/uikit.UIRemoteNotificationType)