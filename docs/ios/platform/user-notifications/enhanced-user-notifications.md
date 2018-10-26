---
title: Notificações de usuário aprimoradas no xamarin. IOS
description: Este artigo descreve a estrutura de notificações do usuário, introduzida no iOS 10. Ele aborda as notificações locais, notificações remotas, gerenciamento de notificação, ações de notificação e muito mais.
ms.prod: xamarin
ms.assetid: 4E1FF652-28F0-4566-B383-9D12664401A4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: d1b1a59b432315532844f8fca3b613ff3392a7b5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108232"
---
# <a name="enhanced-user-notifications-in-xamarinios"></a>Notificações de usuário aprimoradas no xamarin. IOS

Novo no iOS 10, a notificação do usuário framework permite a entrega e a manipulação de notificações locais e remotas. Usando essa estrutura, um aplicativo ou extensão do aplicativo pode agendar a entrega de notificações locais, especificando um conjunto de condições, como local ou a hora do dia.

## <a name="about-user-notifications"></a>Sobre notificações de usuário

Como mencionado acima, a nova estrutura de notificação do usuário permite a entrega e a manipulação de notificações locais e remotas. Usando essa estrutura, um aplicativo ou extensão do aplicativo pode agendar a entrega de notificações locais, especificando um conjunto de condições, como local ou a hora do dia.

Além disso, o aplicativo ou a extensão pode receber (e potencialmente modificar) notificações locais e remotas conforme elas são entregues ao dispositivo iOS do usuário.

A nova estrutura de interface do usuário de notificação de usuário permite que um aplicativo ou extensão do aplicativo para personalizar a aparência de notificações locais e remotas quando eles são apresentados ao usuário.

Essa estrutura fornece as seguintes maneiras que um aplicativo pode enviar notificações a um usuário:

- **Alertas Visual** – em que a notificação rola para baixo da parte superior da tela como uma faixa.
- **Som e vibrações** -pode ser associado uma notificação.
- **Notificações de ícone do aplicativo** - onde o ícone do aplicativo exibe uma notificação mostrando que o novo conteúdo está disponível, como o número de mensagens de email não lido.

Além disso, dependendo do contexto do usuário atual, há diferentes maneiras que uma notificação será exibida:

- Se o dispositivo seja desbloqueado, a notificação será rolar para baixo da parte superior da tela como uma faixa.
- Se o dispositivo estiver bloqueado, a notificação será exibida na tela de bloqueio do usuário.
- Se o usuário perdeu uma notificação, podem abrir o Centro de notificação e exibir qualquer disponível, notificações de espera.

Um aplicativo xamarin. IOS tem dois tipos de notificações de usuário que é capaz de enviar:

- **Notificações locais** -elas são enviadas por aplicativos instalados localmente no dispositivo do usuário.
- **Notificações remotas** -são enviadas de um remoto no servidor e o apresentado ao usuário ou, disparam uma atualização do plano de fundo do conteúdo do aplicativo.

### <a name="about-local-notifications"></a>Sobre notificações locais

O que um aplicativo iOS pode enviar notificações de Local tem os seguintes atributos:

- Elas são enviadas por aplicativos que são locais no dispositivo do usuário. 
- Eles são podem ser configurados usar o tempo ou local com base em gatilhos. 
- O aplicativo de agenda a notificação com o dispositivo do usuário e ele é exibido quando a condição do gatilho é atendida.
- Quando o usuário interage com uma notificação, o aplicativo receberá um retorno de chamada.

Alguns exemplos de notificações locais incluem:

- Alertas de calendário
- Alertas de lembrete
- Gatilhos de reconhecimento de local

Para obter mais informações, consulte da Apple [Local e o guia de programação de notificação remota](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) documentação.

### <a name="about-remote-notifications"></a>Sobre notificações remotas

As notificações remotas que um aplicativo iOS pode enviar têm os seguintes atributos:

- O aplicativo tem um componente do lado do servidor que ele se comunica com.
- Apple Push Notification Service (APNs) é usado para transmitir uma entrega de melhor esforço de notificações remotas para o dispositivo do usuário dos servidores de baseado em nuvem do desenvolvedor.
- Quando o aplicativo recebe a notificação remota, ele será exibido ao usuário.
- Quando o usuário interage com a notificação, o aplicativo receberá um retorno de chamada.

Alguns exemplos de notificações remotas incluem:

- Alertas de notícias
- Atualizações de esportes
- Mensagens de sistema de mensagens instantâneas

Há dois tipos de notificações remotas disponíveis para um aplicativo do iOS:

- **Voltado para o usuário** – elas são exibidas para o usuário no dispositivo.
- **Atualizações silenciosas** -eles fornecem um mecanismo para atualizar o conteúdo de um aplicativo iOS em segundo plano. Quando uma atualização silenciosa é recebida, o aplicativo pode acessar remover servidores suspenso o conteúdo mais recente.

Para obter mais informações, consulte da Apple [Local e o guia de programação de notificação remota](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) documentação.

### <a name="about-the-existing-notifications-api"></a>Sobre as notificações do API existentes

Antes do iOS 10, um aplicativo iOS usaria `UIApplication` para registrar uma notificação com o sistema e agendar como essa notificação deve ser disparada (seja por hora ou local).

Há várias problema que um desenvolvedor pode encontrar ao trabalhar com a API de notificação existente:

- Havia diferentes retornos de chamada necessários para o Local ou remoto notificações que poderia levar à eliminação de duplicação de código.
- O aplicativo tinha controle da notificação limitado, depois que ele tinha foi agendado com o sistema.
- Havia diferentes níveis de suporte em todas as plataformas existentes da Apple.

### <a name="about-the-new-user-notification-framework"></a>Sobre a nova estrutura de notificação do usuário

Com o iOS 10, a Apple introduziu a nova estrutura de notificação do usuário, que substitui o existente `UIApplication` método mencionado acima.

A estrutura de notificação do usuário fornece o seguinte:

- Uma API familiar que inclui a paridade de recursos com os métodos anteriores, tornando mais fácil de portar código do framework existente.
- Inclui um conjunto expandido de opções de conteúdo que permite que mais ricas notificações sejam enviadas para o usuário.
- Local e notificações remotas podem ser manipuladas pelo mesmo código e os retornos de chamada.
- Simplifica o processo de lidar com retornos de chamada que são enviados a um aplicativo quando o usuário interage com uma notificação.
- Gerenciamento aprimorado de notificações pendentes e entregues, incluindo a capacidade de remover ou notificações de atualização.
- Adiciona a capacidade de fazer a apresentação no aplicativo de notificações.
- Adiciona a capacidade de agendar e lidar com notificações de dentro de extensões de aplicativo.
- Adiciona um novo ponto de extensão para as notificações em si. 

A nova estrutura de notificação do usuário fornece uma notificação unificada API entre os vários das plataformas Apple dá suporte a incluindo: 

- **iOS** -suporte para gerenciar e agendar notificações completo.
- **tvOS** -acrescenta a capacidade de ícones do aplicativo de notificação para notificações locais e remotas.
- **watchOS** – adiciona a capacidade de encaminhar as notificações do dispositivo do usuário iOS emparelhado para seu Apple Watch e oferece a capacidade de fazer as notificações locais diretamente no watch de aplicativos do watch.

Para obter mais informações, consulte da Apple [referência de estrutura UserNotifications](https://developer.apple.com/reference/usernotifications) e [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui) documentação.

## <a name="preparing-for-notification-delivery"></a>Preparando para a entrega da notificação

Antes de um iOS aplicativo pode enviar notificações para o usuário que o aplicativo deve ser registrado com o sistema e, como uma notificação é uma interrupção para o usuário, um aplicativo deve solicitar explicitamente a permissão antes de enviá-los.

Há três níveis diferentes de solicitações de notificação que o usuário pode aprovar para um aplicativo:

- Exibe a faixa.
- Alertas de som.
- Notificações no ícone do aplicativo.

Além disso, esses níveis de aprovação devem ser solicitados e definir para notificações locais e remotas.

Permissão de notificação deve ser solicitado, assim que o aplicativo for iniciado, adicionando o seguinte código para o `FinishedLaunching` método da `AppDelegate` e definindo o tipo de notificação desejado (`UNAuthorizationOptions`):

```csharp
using UserNotifications;
...

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    // Request notification permissions from the user
    UNUserNotificationCenter.Current.RequestAuthorization (UNAuthorizationOptions.Alert, (approved, err) => {
        // Handle approval
    });

    return true;
}
```

Além disso, um usuário sempre pode alterar os privilégios de notificação para um aplicativo a qualquer momento usando o **configurações** aplicativo no dispositivo. O aplicativo deve verificar os privilégios do usuário solicitado de notificação antes de apresentar uma notificação usando o seguinte código:

```csharp
// Get current notification settings
UNUserNotificationCenter.Current.GetNotificationSettings ((settings) => {
    var alertsAllowed = (settings.AlertSetting == UNNotificationSetting.Enabled);
}); 
``` 

### <a name="configuring-the-remote-notifications-environment"></a>Configurando o ambiente de notificações remotas

Novo para o iOS 10, o desenvolvedor deve informar o sistema operacional qual ambiente notificação por Push estão em execução no como desenvolvimento ou produção. Falha ao fornecer essas informações pode resultar no aplicativo que está sendo rejeitado quando enviados à App Store o iTune com uma notificação semelhante à seguinte:

> Qualificação de notificação por Push ausente - seu aplicativo inclui uma API para o serviço de notificação por Push da Apple, mas o `aps-environment` está ausente da assinatura do aplicativo.

Para fornecer os direitos necessários, faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Clique duas vezes o `Entitlements.plist` arquivo o **painel de soluções** para abri-lo para edição.
2. Alterne para o **origem** exibição: 

    [![](enhanced-user-notifications-images/setup01.png "A exibição da fonte")](enhanced-user-notifications-images/setup01.png#lightbox)
3. Clique o **+** botão para adicionar uma nova chave.
4. Insira `aps-environment` para o **propriedade**, deixe o **tipo** como `String` e insira `development` ou `production` para o **valor**: 

    [![](enhanced-user-notifications-images/setup02.png "A propriedade de ambiente de aps")](enhanced-user-notifications-images/setup02.png#lightbox)
5. Salve as alterações no arquivo.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Clique duas vezes o `Entitlements.plist` do arquivo na **Gerenciador de soluções** para abri-lo para edição.
3. Clique o **+** botão para adicionar uma nova chave.
4. Insira `aps-environment` para o **propriedade**, deixe o **tipo** como `String` e insira `development` ou `production` para o **valor**: 

    [![](enhanced-user-notifications-images/setup02w.png "A propriedade de ambiente de aps")](enhanced-user-notifications-images/setup02.png#lightbox)
5. Salve as alterações no arquivo.

-----

### <a name="registering-for-remote-notifications"></a>Se registrar para notificações remotas

Se o aplicativo irá enviar e receber notificações remotas, ainda será necessário fazer _Token de registro_ usar existente `UIApplication` API. Esse registro requer que o dispositivo para ter um acesso de conexão de rede ao vivo APNs, que irá gerar o token necessário que será enviado para o aplicativo. O aplicativo precisa, em seguida, encaminhar esse token para o aplicativo do lado do servidor do desenvolvedor para se registrar para notificações remotas:

[![](enhanced-user-notifications-images/token01.png "Visão geral do registro de token")](enhanced-user-notifications-images/token01.png#lightbox)

Use o seguinte código para inicializar a necessárias para o registro:

```csharp
UIApplication.SharedApplication.RegisterForRemoteNotifications ();
```

O token que é enviado para o aplicativo do lado do servidor do desenvolvedor precisa ser incluído como parte da notificação que get do conteúdo enviado do servidor de APNs ao enviar uma notificação remota:

[![](enhanced-user-notifications-images/token02.png "O token incluído como parte da carga de notificação")](enhanced-user-notifications-images/token02.png#lightbox)

O token atua como a chave que vincula a notificação e o aplicativo usado para abrir ou responder à notificação.

Para obter mais informações, consulte da Apple [Local e o guia de programação de notificação remota](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) documentação.

## <a name="notification-delivery"></a>Entrega da notificação

Com o aplicativo totalmente registrada e as permissões necessárias solicitado do e concedida pelo usuário, o aplicativo agora está pronto para enviar e receber notificações. 

### <a name="providing-notification-content"></a>Fornecimento de conteúdo de notificação

Novo para o iOS 10, a todas as notificações contêm um **Title** e **subtítulo** que sempre será exibido com o **corpo** o conteúdo de notificação. Outra novidade, é a capacidade de adicionar **anexos de mídia** para o conteúdo da notificação.

Para criar o conteúdo de uma notificação Local, use o seguinte código:

```csharp
var content = new UNMutableNotificationContent();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
```

Para obter notificações remotas, o processo é semelhante:

```csharp
{
    "aps":{
        "alert":{
            "title":"Notification Title",
            "subtitle":"Notification Subtitle",
            "body":"This is the message body of the notification."
        },
        "badge":1
    }
}
```

### <a name="scheduling-when-a-notification-is-sent"></a>Agendamento de notificação de quando é enviado

Com o conteúdo da notificação criado, o aplicativo precisa agendar quando a notificação será apresentada ao usuário, definindo uma *disparador*. iOS 10 fornece quatro tipos diferentes de gatilho:

- **Notificação por push** - é usado exclusivamente com notificações remotas e é disparado quando o pacote de APNs envia uma notificação para o aplicativo em execução no dispositivo.
- **Intervalo de tempo** -permite que uma notificação de Local ser agendada de um tempo de intervalo comece agora e encerrando um algum momento futuro. Por exemplo, `var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);`
- **Data do calendário** -permite que notificações locais seja agendada para uma data e hora específicas.
- **Baseado local** -permite que notificações locais sejam agendadas quando o dispositivo iOS está entrando ou saindo de uma localização geográfica específica ou está em uma determinado proximidade Beacons qualquer Bluetooth.

Quando uma notificação Local estiver pronta, o aplicativo precisa chamar o `Add` método da `UNUserNotificationCenter` objeto agendar sua exibição ao usuário. Para obter notificações remotas, o aplicativo do lado do servidor envia uma carga de notificação para APNs, que envia o pacote de logon no dispositivo do usuário.

Reunir todas as partes, um exemplo de notificação Local pode parecer com:

```csharp
using UserNotifications;
...

var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;

var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);

var requestID = "sampleRequest";
var request = UNNotificationRequest.FromIdentifier (requestID, content, trigger);

UNUserNotificationCenter.Current.AddNotificationRequest (request, (err) => {
    if (err != null) {
        // Do something with error...
    }
});
```

## <a name="handling-foreground-app-notifications"></a>Manipulando notificações de aplicativo de primeiro plano

Novo para o iOS 10, um aplicativo pode tratar notificações de forma diferente quando ele estiver em primeiro plano e uma notificação é disparada. Fornecendo uma `UNUserNotificationCenterDelegate` e implementar o `UserNotificationCenter` método, o aplicativo pode assumir a responsabilidade para exibir a notificação. Por exemplo:

```csharp
using System;
using UserNotifications;

namespace MonkeyNotification
{
    public class UserNotificationCenterDelegate : UNUserNotificationCenterDelegate
    {
        #region Constructors
        public UserNotificationCenterDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void WillPresentNotification (UNUserNotificationCenter center, UNNotification notification, Action<UNNotificationPresentationOptions> completionHandler)
        {
            // Do something with the notification
            Console.WriteLine ("Active Notification: {0}", notification);

            // Tell system to display the notification anyway or use
            // `None` to say we have handled the display locally.
            completionHandler (UNNotificationPresentationOptions.Alert);
        }
        #endregion
    }
}
```

Esse código é simplesmente escrever o conteúdo do `UNNotification` para a saída do aplicativo e pede que o sistema para exibir o alerta padrão para a notificação. 

Se quisesse que o aplicativo exibir a notificação em si, quando ele estava em primeiro plano e não usar os padrões do sistema, passe `None` para o manipulador de conclusão. Exemplo:

```csharp
completionHandler (UNNotificationPresentationOptions.None);
```

Com esse código funcionando, abra o `AppDelegate.cs` do arquivo para edição e altere o `FinishedLaunching` método procurar semelhante ao seguinte:

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    // Request notification permissions from the user
    UNUserNotificationCenter.Current.RequestAuthorization (UNAuthorizationOptions.Alert, (approved, err) => {
        // Handle approval
    });

    // Watch for notifications while the app is active
    UNUserNotificationCenter.Current.Delegate = new UserNotificationCenterDelegate ();

    return true;
}
```

Esse código está relacionada a anexar personalizado `UNUserNotificationCenterDelegate` acima para a atual `UNUserNotificationCenter` para que o aplicativo possa manipular a notificação enquanto ele está ativo e em primeiro plano.

## <a name="notification-management"></a>Gerenciamento de notificação

Novo para o iOS 10, gerenciamento de notificação fornece acesso às notificações pendentes e entregues e adiciona a capacidade de remover, atualizar ou promover essas notificações.

Uma parte importante do gerenciamento de notificação é o _identificador de solicitação_ que foi atribuído à notificação quando ele foi criado e programado com o sistema. Notificações remotas, isso é atribuído através da nova `apps-collapse-id` campo no cabeçalho da solicitação HTTP.

O identificador de solicitação é usado para selecionar a notificação de que o aplicativo deseja executar o gerenciamento de notificação no.

### <a name="removing-notifications"></a>Removendo notificações

Para remover uma notificação pendente do sistema, use o seguinte código:

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemovePendingNotificationRequests (requests);
```

Para remover uma notificação já entregue, use o seguinte código:

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemoveDeliveredNotifications (requests);
```

### <a name="updating-an-existing-notification"></a>Atualizando uma notificação de existente

Para atualizar um existente de notificação, basta criar uma nova notificação com os parâmetros desejados modificados (por exemplo, um novo tempo de gatilho) e adicioná-lo para o sistema com o mesmo identificador de solicitação que a notificação de que precisa ser modificado. Exemplo:


```csharp
using UserNotifications;
...

// Rebuild notification
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;

// New trigger time
var trigger = UNTimeIntervalNotificationTrigger.CreateTrigger (10, false);

// ID of Notification to be updated
var requestID = "sampleRequest";
var request = UNNotificationRequest.FromIdentifier (requestID, content, trigger);

// Add to system to modify existing Notification
UNUserNotificationCenter.Current.AddNotificationRequest (request, (err) => {
    if (err != null) {
        // Do something with error...
    }
});
```

Para notificações já entregues, a notificação existente será obter atualizada e promovida à parte superior da lista nas telas iniciais e de bloqueio e no Centro de notificação, se ele já foi lida pelo usuário.

## <a name="working-with-notification-actions"></a>Trabalhando com ações de notificação

No iOS 10, notificações são entregues para o usuário não são estáticas em fornecem várias maneiras que o usuário pode interagir com eles (de interno para ações personalizadas).

Há três tipos de ações que um aplicativo iOS pode responder a:

- **Ação padrão** -isso é quando o usuário toca uma notificação para abrir o aplicativo e exibir os detalhes da notificação determinado.
- **Ações personalizadas** -eles foram adicionados no iOS 8 e fornecem uma maneira rápida para o usuário execute uma tarefa personalizada diretamente na notificação sem a necessidade de iniciar o aplicativo. Eles podem ser apresentados como uma lista de botões com títulos personalizáveis ou um texto campo de entrada que pode ser executados em segundo plano (em que o aplicativo é fornecido uma pequena quantidade de tempo para atender à solicitação) ou o primeiro plano (em que o aplicativo é iniciado em primeiro plano para fu lfill a solicitação). Ações personalizadas estão disponíveis no iOS e watchOS.
- **Ação de ignorar** -esta ação é enviada para o aplicativo quando o usuário o descarte de uma determinada notificação.

### <a name="creating-custom-actions"></a>Criação de ações personalizadas

Para criar e registrar uma ação personalizada com o sistema, use o seguinte código:

```csharp
// Create action
var actionID = "reply";
var title = "Reply";
var action = UNNotificationAction.FromIdentifier (actionID, title, UNNotificationActionOptions.None);

// Create category
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.None);
    
// Register category
var categories = new UNNotificationCategory [] { category };
UNUserNotificationCenter.Current.SetNotificationCategories (new NSSet<UNNotificationCategory>(categories)); 
```

Ao criar um novo `UNNotificationAction`, ele é atribuído a uma ID exclusiva e o título que aparecerá no botão. Por padrão, a ação será criada como uma ação do plano de fundo, no entanto, as opções podem ser fornecidas para ajustar o comportamento da ação (por exemplo defini-lo como uma ação de primeiro plano).

Cada uma das ações criadas precisa ser associado uma categoria. Ao criar um novo `UNNotificationCategory`, ele é atribuído a uma ID exclusiva, uma lista de ações que ele possa executar, uma lista de IDs de intenção de fornecer mais informações sobre a intenção das ações na categoria e algumas opções para controlar o comportamento da categoria.

Por fim, todas as categorias são registradas com o sistema usando o `SetNotificationCategories` método.

### <a name="presenting-custom-actions"></a>Apresentando as ações personalizadas

Depois que um conjunto de ações personalizadas e as categorias foram criados e registrados com o sistema, podem ser apresentadas de Local ou notificações remotas.

Para notificação remoto, defina um `category` no conteúdo da notificação remota que corresponde a uma das categorias criadas acima. Por exemplo:

```csharp
{
    aps:{
        alert:"Hello world!",
        category:"message"
    }
}
```

Para notificações locais, defina as `CategoryIdentifier` propriedade do `UNMutableNotificationContent` objeto. Por exemplo:

```csharp
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
content.CategoryIdentifier = "message";
...
```

Novamente, essa ID deve corresponder a uma das categorias que foi criado acima.

### <a name="handling-dismiss-actions"></a>Tratamento de ignorar as ações

Como mencionado acima, uma ação de ignorar podem ser enviada para o aplicativo quando o usuário o descarte de uma notificação. Como essa não é uma ação padrão, uma opção precisará ser definido quando a categoria é criada. Por exemplo:

```csharp
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.CustomDismissAction);

```

### <a name="handling-action-responses"></a>Manipulando respostas de ação

Quando o usuário interage com as categorias que foram criadas acima e ações personalizadas, o aplicativo precisa atender a tarefa solicitada. Isso é feito fornecendo uma `UNUserNotificationCenterDelegate` e implementar o `UserNotificationCenter` método. Por exemplo:

```csharp
using System;
using UserNotifications;

namespace MonkeyNotification
{
    public class UserNotificationCenterDelegate : UNUserNotificationCenterDelegate
    {
        ...

        #region Override Methods
        public override void DidReceiveNotificationResponse (UNUserNotificationCenter center, UNNotificationResponse response, Action completionHandler)
        {
            // Take action based on Action ID
            switch (response.ActionIdentifier) {
            case "reply":
                // Do something
                break;
            default:
                // Take action based on identifier
                if (response.IsDefaultAction) {
                    // Handle default action...
                } else if (response.IsDismissAction) {
                    // Handle dismiss action
                }
                break;
            }

            // Inform caller it has been handled
            completionHandler();
        }
        #endregion
    }
}
```

O que for passado em `UNNotificationResponse` classe tem um `ActionIdentifier` propriedade que pode ser a ação padrão ou a ação de ignorar. Use `response.Notification.Request.Identifier` para testar as ações personalizadas.

O `UserText` propriedade contém o valor de qualquer entrada de texto do usuário. O `Notification` propriedade contém a notificação de origem que inclui a solicitação com o gatilho e conteúdo de notificação. O aplicativo pode decidir se fosse um Local ou remoto de notificação com base no tipo de gatilho.

> [!NOTE]
> iOS 12 possibilita uma notificação personalizada da interface do usuário modificar seus botões de ação em tempo de execução. Para obter mais informações, examine os [botões de ação de notificação dinâmico](~/ios/platform/introduction-to-ios12/notifications/dynamic-actions.md) documentação.

## <a name="working-with-service-extensions"></a>Trabalhando com extensões de serviço

Ao trabalhar com notificações remotas, _extensões de serviços_ fornecem uma maneira de habilitar a criptografia de ponta a ponta dentro a carga de notificação. Extensões de serviços são uma extensão de Interface de usuário (disponível no iOS 10) que são executados em segundo plano com o objetivo principal de aumentando ou substituindo o conteúdo visível de uma notificação antes de serem apresentado ao usuário. 

[![](enhanced-user-notifications-images/extension01.png "Visão geral da extensão de serviço")](enhanced-user-notifications-images/extension01.png#lightbox)

Extensões de serviços devem ser executadas rapidamente e recebem apenas um curto período de tempo para serem executadas pelo sistema. No caso em que a extensão de serviço falha ao concluir a tarefa em determinado período de tempo, um método de fallback será chamado. Se o fallback falhar, o conteúdo de notificação original será exibido ao usuário.

Alguns usos possíveis de extensões de serviço incluem:

- Fornecendo criptografia de ponta a ponta do conteúdo da notificação remota.
- Adição de anexos para notificações remotas para enriquecê-los.

### <a name="implementing-a-service-extension"></a>Implementando uma extensão de serviço

Para implementar uma extensão de serviço em um aplicativo xamarin. IOS, faça o seguinte:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Abra a solução do aplicativo no Visual Studio para Mac.
2. Clique com botão direito no nome da solução nos **painel de soluções** e selecione **Add** > **adicionar novo projeto**.
3. Selecione **iOS** > **extensões** > **extensões de serviços de notificação** e clique no **próxima** botão: 

    [![](enhanced-user-notifications-images/extension02.png "Selecione as extensões de serviços de notificação")](enhanced-user-notifications-images/extension02.png#lightbox)
4. Insira um **nome** para a extensão e clique no **próxima** botão: 

    [![](enhanced-user-notifications-images/extension03.png "Insira um nome para a extensão")](enhanced-user-notifications-images/extension03.png#lightbox)
5. Ajustar a **nome do projeto** e/ou **nome da solução** se necessário e clique no **criar** botão: 

    [![](enhanced-user-notifications-images/extension04.png "Ajustar o nome do projeto e/ou o nome da solução")](enhanced-user-notifications-images/extension04.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Abra a solução do aplicativo no Visual Studio.
2. Clique com botão direito no nome da solução nos **Gerenciador de soluções** e selecione **Adicionar > Novo projeto...** .
3. Selecione **Visual C# > extensões do iOS > extensão de serviço de notificação**:

    [![](enhanced-user-notifications-images/extension01.w157-sml.png "Selecione as extensões de serviços de notificação")](enhanced-user-notifications-images/extension01.w157.png#lightbox)
4. Insira um **nome** para a extensão e clique no **Okey** botão.

-----

> [!IMPORTANT]
> O identificador de pacote para a extensão de serviço deve corresponder ao identificador de pacote do aplicativo principal com `.appnameserviceextension` acrescentado ao final. Por exemplo, se o aplicativo principal tinha um identificador de pacote do `com.xamarin.monkeynotify`, a extensão de serviço deve ter um identificador de pacote de `com.xamarin.monkeynotify.monkeynotifyserviceextension`. Isso deve ser definido automaticamente quando a extensão é adicionada à solução. 

Há uma classe principal na extensão de serviço de notificação de que precisam ser modificados para fornecer a funcionalidade necessária. Por exemplo:

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;

namespace MonkeyChatServiceExtension
{
    [Register ("NotificationService")]
    public class NotificationService : UNNotificationServiceExtension
    {
        #region Computed Properties
        public Action<UNNotificationContent> ContentHandler { get; set; }
        public UNMutableNotificationContent BestAttemptContent { get; set; }
        #endregion

        #region Constructors
        protected NotificationService (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void DidReceiveNotificationRequest (UNNotificationRequest request, Action<UNNotificationContent> contentHandler)
        {
            ContentHandler = contentHandler;
            BestAttemptContent = (UNMutableNotificationContent)request.Content.MutableCopy ();

            // Modify the notification content here...
            BestAttemptContent.Title = $"{BestAttemptContent.Title}[modified]";

            ContentHandler (BestAttemptContent);
        }

        public override void TimeWillExpire ()
        {
            // Called just before the extension will be terminated by the system.
            // Use this as an opportunity to deliver your "best attempt" at modified content, otherwise the original push payload will be used.

            ContentHandler (BestAttemptContent);
        }
        #endregion
    }
}
```

O primeiro método, `DidReceiveNotificationRequest`, passará o identificador de notificação, bem como o conteúdo de notificação por meio de `request` objeto. O que for passado no `contentHandler` precisará ser chamado para apresentar a notificação ao usuário.

O segundo método, `TimeWillExpire`, será chamado antes de tempo está prestes a expirar para a extensão de serviço processar a solicitação. Se a extensão de serviço falha ao chamar o `contentHandler` no período de tempo designado, o conteúdo original será exibido ao usuário.

### <a name="triggering-a-service-extension"></a>Disparar uma extensão de serviço

Com uma extensão de serviço criada e entregue com o aplicativo, ele pode ser acionado, modificando a carga de notificação remota enviados para o dispositivo. Por exemplo:

```csharp
{
    aps : {
        alert : "New Message Available",
        mutable-content: 1
    },
    encrypted-content : "#theencryptedcontent"
}
```

O novo `mutable-content` chave especifica que a extensão de serviço precisará ser iniciado para atualizar o conteúdo da notificação remota. O `encrypted-content` chave mantém os dados criptografados que a extensão de serviço pode descriptografar antes de apresentar ao usuário.

Dê uma olhada no exemplo a seguir, a extensão de serviço:

```csharp
using UserNotification;

namespace myApp {
    public class NotificationService : UNNotificationServiceExtension {
    
        public override void DidReceiveNotificationRequest(UNNotificationRequest request, contentHandler) {
            // Decrypt payload
            var decryptedBody = Decrypt(Request.Content.UserInfo["encrypted-content"]);
            
            // Modify Notification body
            var newContent = new UNMutableNotificationContent();
            newContent.Body = decryptedBody;
            
            // Present to user
            contentHandler(newContent);
        }
        
        public override void TimeWillExpire() {
            // Handle out-of-time fallback event
            ...
        }
        
    }
}
```

Esse código descriptografa o conteúdo criptografado do `encrypted-content` da chave, cria um novo `UNMutableNotificationContent`, define o `Body` propriedade para o conteúdo descriptografado e usa o `contentHandler` para apresentar a notificação ao usuário.

## <a name="summary"></a>Resumo

Este artigo cobriu todas as maneiras que os usuários de notificação foram aprimorada pelo iOS 10. Ela apresentada a nova estrutura de notificação do usuário e como usá-lo em um aplicativo xamarin. IOS ou extensão do aplicativo.



## <a name="related-links"></a>Links relacionados

- [Amostras do iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Referência de estrutura Usernotifications](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Guia de programação de notificação local e remota](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
