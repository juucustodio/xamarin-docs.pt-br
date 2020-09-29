---
title: Notificações de usuário aprimoradas no Xamarin. iOS
description: Este artigo descreve a estrutura de notificações do usuário introduzida no iOS 10. Ele aborda notificações locais, notificações remotas, gerenciamento de notificações, ações de notificação e muito mais.
ms.prod: xamarin
ms.assetid: 4E1FF652-28F0-4566-B383-9D12664401A4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2017
ms.openlocfilehash: 207aac33101615a0a103176cd2bf5dd061e0d264
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430419"
---
# <a name="enhanced-user-notifications-in-xamarinios"></a>Notificações de usuário aprimoradas no Xamarin. iOS

Novo no iOS 10, a estrutura de notificação de usuário permite a entrega e a manipulação de notificações locais e remotas. Usando essa estrutura, uma extensão de aplicativo ou aplicativo pode agendar a entrega de notificações locais, especificando um conjunto de condições, como local ou hora do dia.

## <a name="about-user-notifications"></a>Sobre as notificações do usuário

Conforme mencionado acima, a nova estrutura de notificação de usuário permite a entrega e a manipulação de notificações locais e remotas. Usando essa estrutura, uma extensão de aplicativo ou aplicativo pode agendar a entrega de notificações locais, especificando um conjunto de condições, como local ou hora do dia.

Além disso, o aplicativo ou a extensão pode receber (e potencialmente modificar) as notificações locais e remotas à medida que elas são entregues ao dispositivo iOS do usuário.

A nova estrutura de IU de notificação de usuário permite que uma extensão de aplicativo ou aplicativo Personalize a aparência de notificações locais e remotas quando elas são apresentadas ao usuário.

Essa estrutura fornece as seguintes maneiras em que um aplicativo pode entregar notificações a um usuário:

- **Alertas visuais** – onde a notificação é acumulada a partir da parte superior da tela como uma faixa.
- **Som e vibração** – podem ser associados a uma notificação.
- **Ícone do aplicativo notificações** -onde o ícone do aplicativo exibe uma notificação mostrando que o novo conteúdo está disponível, como o número de mensagens de email não lidas.

Além disso, dependendo do contexto atual do usuário, há diferentes maneiras pelas quais uma notificação será apresentada:

- Se o dispositivo estiver desbloqueado, a notificação será acumulada a partir da parte superior da tela como uma faixa.
- Se o dispositivo estiver bloqueado, a notificação será exibida na tela de bloqueio do usuário.
- Se o usuário tiver perdido uma notificação, ele poderá abrir o centro de notificações e exibir todas as notificações disponíveis e aguardando.

Um aplicativo Xamarin. iOS tem dois tipos de notificações de usuário que ele pode enviar:

- **Notificações locais** – elas são enviadas por aplicativos instalados localmente no dispositivo de usuários.
- **Notificações remotas** – são enviadas de um servidor remoto e apresentadas ao usuário ou disparam uma atualização em segundo plano do conteúdo do aplicativo.

### <a name="about-local-notifications"></a>Sobre notificações locais

As notificações locais que um aplicativo iOS pode enviar têm os seguintes recursos e atributos:

- Eles são enviados por aplicativos que são locais no dispositivo do usuário. 
- Eles podem ser configurados para usar gatilhos baseados em horário ou local. 
- O aplicativo agenda a notificação com o dispositivo do usuário e é exibido quando a condição do gatilho é atendida.
- Quando o usuário interage com uma notificação, o aplicativo receberá um retorno de chamada.

Alguns exemplos de notificações locais incluem:

- Alertas de calendário
- Alertas de lembrete
- Gatilhos com reconhecimento de local

Para obter mais informações, consulte a documentação do [Guia de programação de notificação local e remota](https://developer.apple.com/documentation/usernotifications) da Apple.

### <a name="about-remote-notifications"></a>Sobre notificações remotas

As notificações remotas que um aplicativo iOS pode enviar têm os seguintes recursos e atributos:

- O aplicativo tem um componente do lado do servidor com o qual ele se comunica.
- O Apple Push Notification Service (APNs) é usado para transmitir uma entrega de melhor esforço de notificações remotas para o dispositivo do usuário dos servidores baseados em nuvem do desenvolvedor.
- Quando o aplicativo receber a notificação remota, ele será exibido para o usuário.
- Quando o usuário interage com a notificação, o aplicativo receberá um retorno de chamada.

Alguns exemplos de notificações remotas incluem:

- Alertas de notícias
- Atualizações esportivas
- Mensagens de mensagens instantâneas

Há dois tipos de notificações remotas disponíveis para um aplicativo iOS:

- **Voltado** para o usuário-eles são exibidos para o usuário no dispositivo.
- **Atualizações silenciosas** -fornecem um mecanismo para atualizar o conteúdo de um aplicativo Ios em segundo plano. Quando uma atualização silenciosa é recebida, o aplicativo pode entrar em contato com os servidores remotos para extrair o conteúdo mais recente.

Para obter mais informações, consulte a documentação do [Guia de programação de notificação local e remota](https://developer.apple.com/documentation/usernotifications) da Apple.

### <a name="about-the-existing-notifications-api"></a>Sobre a API de notificações existente

Antes do iOS 10, um aplicativo iOS usaria `UIApplication` para registrar uma notificação com o sistema e agendar como essa notificação deve ser disparada (seja por hora ou local).

Há vários problemas que um desenvolvedor pode encontrar ao trabalhar com a API de notificação existente:

- Foram necessários retornos de chamada diferentes para notificações locais ou remotas, o que pode levar à duplicação de código.
- O aplicativo tinha controle limitado da notificação após ele ter sido agendado com o sistema.
- Havia diferentes níveis de suporte em todas as plataformas existentes da Apple.

### <a name="about-the-new-user-notification-framework"></a>Sobre a nova estrutura de notificação de usuário

Com o iOS 10, a Apple introduziu a nova estrutura de notificação de usuário, que substitui o `UIApplication` método existente anotado acima.

A estrutura de notificação do usuário fornece o seguinte:

- Uma API familiar que inclui a paridade de recursos com os métodos anteriores, facilitando o código de porta da estrutura existente.
- Inclui um conjunto expandido de opções de conteúdo que permite que notificações mais avançadas sejam enviadas ao usuário.
- As notificações locais e remotas podem ser manipuladas pelo mesmo código e retornos de chamada.
- Simplifica o processo de tratamento de retornos de chamada que são enviados a um aplicativo quando o usuário interage com uma notificação.
- Gerenciamento aprimorado de notificações pendentes e entregues, incluindo a capacidade de remover ou atualizar notificações.
- Adiciona a capacidade de fazer apresentações no aplicativo de notificações.
- Adiciona a capacidade de agendar e manipular notificações de dentro das extensões de aplicativo.
- Adiciona novo ponto de extensão para as notificações em si. 

A nova estrutura de notificação de usuário fornece uma API de notificação unificada entre várias das plataformas às quais a Apple oferece suporte, incluindo: 

- **Ios** -suporte completo para gerenciar e agendar notificações.
- **tvOS** -adiciona a capacidade de crachá de ícones de aplicativos para notificações locais e remotas.
- **watchOS** -adiciona a capacidade de encaminhar notificações do dispositivo IOS emparelhado do usuário para seus Apple Watch e dá aos aplicativos de inspeção a capacidade de fazer notificações locais diretamente no próprio relógio.

Para obter mais informações, consulte a [referência da estrutura Usernotifications](https://developer.apple.com/reference/usernotifications) da Apple e a documentação do [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui) .

## <a name="preparing-for-notification-delivery"></a>Preparando para entrega de notificação

Antes que um aplicativo iOS possa enviar notificações para o usuário, o aplicativo deve ser registrado no sistema e, como uma notificação é uma interrupção para o usuário, um aplicativo deve solicitar explicitamente a permissão antes de enviá-los.

Há três níveis diferentes de solicitações de notificação que o usuário pode aprovar para um aplicativo:

- Exibição de faixa.
- Alertas de som.
- Notificações o ícone do aplicativo.

Além disso, esses níveis de aprovação devem ser solicitados e definidos para notificações locais e remotas.

A permissão de notificação deve ser solicitada assim que o aplicativo é iniciado, adicionando o seguinte código ao `FinishedLaunching` método do `AppDelegate` e definindo o tipo de notificação desejado ( `UNAuthorizationOptions` ):

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

Além disso, um usuário sempre pode alterar os privilégios de notificação para um aplicativo a qualquer momento usando o aplicativo de **configurações** no dispositivo. O aplicativo deve verificar os privilégios de notificação solicitados do usuário antes de apresentar uma notificação usando o seguinte código:

```csharp
// Get current notification settings
UNUserNotificationCenter.Current.GetNotificationSettings ((settings) => {
    var alertsAllowed = (settings.AlertSetting == UNNotificationSetting.Enabled);
});    
``` 

### <a name="configuring-the-remote-notifications-environment"></a>Configurando o ambiente de notificações remotas

Novo no iOS 10, o desenvolvedor deve informar ao sistema operacional qual notificação por push do ambiente está sendo executada como desenvolvimento ou produção. A falha em fornecer essas informações pode fazer com que o aplicativo seja rejeitado quando enviado para a loja de aplicativos do iTune com uma notificação semelhante à seguinte:

> Direito de notificação por push ausente – seu aplicativo inclui uma API para o serviço de notificação por push da Apple, mas o `aps-environment` direito está faltando na assinatura do aplicativo.

Para fornecer o direito necessário, faça o seguinte:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Clique duas vezes no `Entitlements.plist` arquivo no **painel de soluções** para abri-lo para edição.
2. Alterne para a exibição de **origem** : 

    [![A exibição da fonte](enhanced-user-notifications-images/setup01.png)](enhanced-user-notifications-images/setup01.png#lightbox)
3. Clique no **+** botão para adicionar uma nova chave.
4. Insira `aps-environment` para a **Propriedade**, deixe o **tipo** como `String` e insira um `development` ou `production` para o **valor**: 

    [![A propriedade APS-Environment](enhanced-user-notifications-images/setup02.png)](enhanced-user-notifications-images/setup02.png#lightbox)
5. Salve as alterações no arquivo.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Clique duas vezes no `Entitlements.plist` arquivo no **Gerenciador de soluções** para abri-lo para edição.
2. Clique no **+** botão para adicionar uma nova chave.
3. Insira `aps-environment` para a **Propriedade**, deixe o **tipo** como `String` e insira um `development` ou `production` para o **valor**: 

    [![A propriedade APS-Environment](enhanced-user-notifications-images/setup02w.png)](enhanced-user-notifications-images/setup02.png#lightbox)
4. Salve as alterações no arquivo.

-----

### <a name="registering-for-remote-notifications"></a>Registrando para notificações remotas

Se o aplicativo estiver enviando e recebendo notificações remotas, ele ainda precisará fazer o _registro de token_ usando a `UIApplication` API existente. Esse registro exige que o dispositivo tenha uma conexão de rede ao vivo acessando o APNs, o que gerará o token necessário que será enviado ao aplicativo. O aplicativo precisa então encaminhar esse token para o aplicativo do lado do servidor do desenvolvedor para se registrar para notificações remotas:

[![Visão geral do registro de token](enhanced-user-notifications-images/token01.png)](enhanced-user-notifications-images/token01.png#lightbox)

Use o código a seguir para inicializar o registro necessário:

```csharp
UIApplication.SharedApplication.RegisterForRemoteNotifications ();
```

O token que é enviado ao aplicativo do lado do servidor do desenvolvedor precisará ser incluído como parte da carga de notificação enviada do servidor para o APNs ao enviar uma notificação remota:

[![O token incluído como parte da carga de notificação](enhanced-user-notifications-images/token02.png)](enhanced-user-notifications-images/token02.png#lightbox)

O token atua como a chave que agrupa a notificação e o aplicativo usado para abrir ou responder à notificação.

Para obter mais informações, consulte a documentação do [Guia de programação de notificação local e remota](https://developer.apple.com/documentation/usernotifications) da Apple.

## <a name="notification-delivery"></a>Entrega de notificação

Com o aplicativo totalmente registrado e as permissões necessárias solicitadas e concedidas pelo usuário, o aplicativo agora está pronto para enviar e receber notificações. 

### <a name="providing-notification-content"></a>Fornecendo conteúdo de notificação

Novo no iOS 10, todas as notificações contêm um **título** e um **subtítulo** que serão sempre exibidos com o **corpo** do conteúdo da notificação. Além disso, o é a capacidade de adicionar **anexos de mídia** ao conteúdo da notificação.

Para criar o conteúdo de uma notificação local, use o seguinte código:

```csharp
var content = new UNMutableNotificationContent();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
```

Para notificações remotas, o processo é semelhante:

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

### <a name="scheduling-when-a-notification-is-sent"></a>Agendando quando uma notificação é enviada

Com o conteúdo da notificação criado, o aplicativo precisa agendar quando a notificação será apresentada ao usuário configurando um *gatilho*. o iOS 10 fornece quatro tipos diferentes de gatilho:

- **Notificação por push** – é usada exclusivamente com notificações remotas e é disparada quando o APNs envia um pacote de notificação para o aplicativo em execução no dispositivo.
- **Intervalo de tempo** – permite que uma notificação local seja agendada a partir de um intervalo de tempo comece com agora e termine um certo ponto futuro. Por exemplo, `var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);`
- **Data do calendário** – permite que as notificações locais sejam agendadas para uma data e hora específicas.
- **Baseado na localização** – permite que as notificações locais sejam agendadas quando o dispositivo IOS está entrando ou saindo de uma localização geográfica específica ou está em uma determinada proximidade para qualquer Beacon Bluetooth.

Quando uma notificação local estiver pronta, o aplicativo precisará chamar o `Add` método do `UNUserNotificationCenter` objeto para agendar sua exibição para o usuário. Para notificações remotas, o aplicativo do lado do servidor envia uma carga de notificação para o APNs, que envia o pacote para o dispositivo do usuário.

Reunindo todas as partes, uma notificação local de exemplo pode ser semelhante a:

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

## <a name="handling-foreground-app-notifications"></a>Manipulando notificações de aplicativo em primeiro plano

Novo no iOS 10, um aplicativo pode manipular notificações de forma diferente quando está em primeiro plano e uma notificação é disparada. Ao fornecer um `UNUserNotificationCenterDelegate` e implementar o `WillPresentNotification` método, o aplicativo pode assumir a responsabilidade de exibir a notificação. Por exemplo:

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

Esse código está simplesmente gravando o conteúdo do na `UNNotification` saída do aplicativo e solicitando que o sistema exiba o alerta padrão para a notificação. 

Se o aplicativo quisesse exibir a notificação em si, quando estava em primeiro plano, e não usar os padrões do sistema, passe `None` para o manipulador de conclusão. Exemplo:

```csharp
completionHandler (UNNotificationPresentationOptions.None);
```

Com esse código em vigor, abra o `AppDelegate.cs` arquivo para edição e altere o `FinishedLaunching` método para que fique semelhante ao seguinte:

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

Esse código está anexando o personalizado `UNUserNotificationCenterDelegate` acima ao atual para `UNUserNotificationCenter` que o aplicativo possa manipular a notificação enquanto estiver ativo e em primeiro plano.

## <a name="notification-management"></a>Gerenciamento de notificação

Novo no iOS 10, o gerenciamento de notificação fornece acesso a notificações pendentes e entregues e adiciona a capacidade de remover, atualizar ou promover essas notificações.

Uma parte importante do gerenciamento de notificação é o _identificador de solicitação_ que foi atribuído à notificação quando foi criado e agendado com o sistema. Para notificações remotas, isso é atribuído por meio do novo `apps-collapse-id` campo no cabeçalho de solicitação HTTP.

O identificador de solicitação é usado para selecionar a notificação na qual o aplicativo deseja executar o gerenciamento de notificação.

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

### <a name="updating-an-existing-notification"></a>Atualizando uma notificação existente

Para atualizar uma notificação existente, basta criar uma nova notificação com os parâmetros desejados modificados (como um novo tempo de disparo) e adicioná-lo ao sistema com o mesmo identificador de solicitação que a notificação que precisa ser modificada. Exemplo:

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

Para notificações já entregues, a notificação existente será atualizada e promovida para a parte superior da lista nas telas início e bloqueio e no centro de notificações, se já tiver sido lida pelo usuário.

## <a name="working-with-notification-actions"></a>Trabalhando com ações de notificação

No iOS 10, as notificações que são entregues ao usuário não são estáticas e fornecem várias maneiras pelas quais o usuário pode interagir com eles (de ações internas a personalizadas).

Há três tipos de ações às quais um aplicativo iOS pode responder:

- **Ação padrão** – quando o usuário toca em uma notificação para abrir o aplicativo e exibir os detalhes da notificação determinada.
- **Ações personalizadas** -elas foram adicionadas no Ios 8 e fornecem uma maneira rápida para o usuário executar uma tarefa personalizada diretamente da notificação sem a necessidade de iniciar o aplicativo. Eles podem ser apresentados como uma lista de botões com títulos personalizáveis ou um campo de entrada de texto que pode ser executado em segundo plano (onde o aplicativo recebe uma pequena quantidade de tempo para atender à solicitação) ou o primeiro plano (onde o aplicativo é iniciado em primeiro plano para atender à solicitação). As ações personalizadas estão disponíveis no iOS e no watchOS.
- **Ignorar ação** – essa ação é enviada para o aplicativo quando o usuário ignora uma determinada notificação.

### <a name="creating-custom-actions"></a>Criando ações personalizadas

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

Ao criar um novo `UNNotificationAction` , ele recebe uma ID exclusiva e o título que será exibido no botão. Por padrão, a ação será criada como uma ação em segundo plano, no entanto, as opções podem ser fornecidas para ajustar o comportamento da ação (por exemplo, defini-la como uma ação de primeiro plano).

Cada uma das ações criadas precisa ser associada a uma categoria. Ao criar um novo `UNNotificationCategory` , ele recebe uma ID exclusiva, uma lista de ações que ele pode executar, uma lista de IDs de intenção para fornecer mais informações sobre a intenção das ações na categoria e algumas opções para controlar o comportamento da categoria.

Por fim, todas as categorias são registradas com o sistema usando o `SetNotificationCategories` método.

### <a name="presenting-custom-actions"></a>Apresentando ações personalizadas

Depois que um conjunto de ações e categorias personalizadas tiver sido criado e registrado no sistema, ele poderá ser apresentado por meio de notificações locais ou remotas.

Para notificação remota, defina um `category` na carga de notificação remota que corresponde a uma das categorias criadas acima. Por exemplo:

```csharp
{
    aps:{
        alert:"Hello world!",
        category:"message"
    }
}
```

Para notificações locais, defina a `CategoryIdentifier` Propriedade do `UNMutableNotificationContent` objeto. Por exemplo:

```csharp
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
content.CategoryIdentifier = "message";
...
```

Novamente, essa ID precisa corresponder a uma das categorias que foi criada acima.

### <a name="handling-dismiss-actions"></a>Tratamento de ações de descarte

Como mencionado acima, uma ação de ignorar pode ser enviada ao aplicativo quando o usuário ignora uma notificação. Como essa não é uma ação padrão, será necessário definir uma opção quando a categoria for criada. Por exemplo:

```csharp
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.CustomDismissAction);

```

### <a name="handling-action-responses"></a>Manipulando respostas de ação

Quando o usuário interage com as ações e as categorias personalizadas que foram criadas acima, o aplicativo precisa atender à tarefa solicitada. Isso é feito fornecendo um `UNUserNotificationCenterDelegate` e implementando o `UserNotificationCenter` método. Por exemplo:

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

A classe transmitida `UNNotificationResponse` tem uma `ActionIdentifier` propriedade que pode ser a ação padrão ou a ação ignorar. Use `response.Notification.Request.Identifier` para testar qualquer ação personalizada.

A `UserText` propriedade contém o valor de qualquer entrada de texto de usuário. A `Notification` propriedade contém a notificação de origem que inclui a solicitação com o conteúdo do gatilho e da notificação. O aplicativo pode decidir se foi uma notificação local ou remota com base no tipo de gatilho.

> [!NOTE]
> o iOS 12 torna possível que uma interface do usuário de notificação personalizada modifique seus botões de ação em tempo de execução. Para obter mais informações, veja a documentação dos [botões de ação de notificação dinâmica](~/ios/platform/introduction-to-ios12/notifications/dynamic-actions.md) .

## <a name="working-with-service-extensions"></a>Trabalhando com extensões de serviço

Ao trabalhar com notificações remotas, _as extensões de serviço_ fornecem uma maneira de habilitar a criptografia de ponta a ponta dentro da carga de notificação. Extensões de serviço são uma extensão de interface não do usuário (disponível no iOS 10) que é executada em segundo plano com a principal finalidade de aumentar ou substituir o conteúdo visível de uma notificação antes que ela seja apresentada ao usuário. 

[![Visão geral da extensão de serviço](enhanced-user-notifications-images/extension01.png)](enhanced-user-notifications-images/extension01.png#lightbox)

As extensões de serviço devem ser executadas rapidamente e só recebem um curto período de tempo para serem executadas pelo sistema. Caso a extensão de serviço não conclua sua tarefa no período de tempo alocado, um método de fallback será chamado. Se o fallback falhar, o conteúdo da notificação original será exibido para o usuário.

Alguns usos potenciais das extensões de serviço incluem:

- Fornecer criptografia de ponta a ponta do conteúdo de notificação remota.
- Adicionar anexos a notificações remotas para enriquecer.

### <a name="implementing-a-service-extension"></a>Implementando uma extensão de serviço

Para implementar uma extensão de serviço em um aplicativo Xamarin. iOS, faça o seguinte:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Abra a solução do aplicativo no Visual Studio para Mac.
2. Clique com o botão direito do mouse no nome da solução na **painel de soluções** e selecione **Adicionar**  >  **Adicionar novo projeto**.
3. Selecione **iOS**  >  **extensões do**  >  **serviço de notificação** extensões do IOS e clique no botão **Avançar** : 

    [![Selecionar extensões do serviço de notificação](enhanced-user-notifications-images/extension02.png)](enhanced-user-notifications-images/extension02.png#lightbox)
4. Insira um **nome** para a extensão e clique no botão **Avançar** : 

    [![Insira um nome para a extensão](enhanced-user-notifications-images/extension03.png)](enhanced-user-notifications-images/extension03.png#lightbox)
5. Ajuste o **nome do projeto** e/ou o **nome da solução** , se necessário, e clique no botão **criar** : 

    [![Ajustar o nome do projeto e/ou o nome da solução](enhanced-user-notifications-images/extension04.png)](enhanced-user-notifications-images/extension04.png#lightbox) 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Abra a solução do aplicativo no Visual Studio.
2. Clique com o botão direito do mouse no nome da solução na **Gerenciador de soluções** e selecione **Adicionar > novo projeto...**.
3. Selecione **Visual C# > extensões do iOS > extensão do serviço de notificação**:

    [![Selecionar extensões do serviço de notificação](enhanced-user-notifications-images/extension01.w157-sml.png)](enhanced-user-notifications-images/extension01.w157.png#lightbox)
4. Insira um **nome** para a extensão e clique no botão **OK** .

-----

> [!IMPORTANT]
> O identificador de pacote para a extensão de serviço deve corresponder ao identificador de pacote do aplicativo principal com `.appnameserviceextension` acrescentado ao final. Por exemplo, se o aplicativo principal tiver um identificador de pacote de  `com.xamarin.monkeynotify` , a extensão de serviço deverá ter um identificador de pacote de `com.xamarin.monkeynotify.monkeynotifyserviceextension` . Isso deve ser definido automaticamente quando a extensão é adicionada à solução. 

Há uma classe principal na extensão do Notification Service que precisará ser modificada para fornecer a funcionalidade necessária. Por exemplo:

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

O primeiro método, `DidReceiveNotificationRequest` , passará o identificador de notificação, bem como o conteúdo de notificação por meio do `request` objeto. O passado em `contentHandler` precisará ser chamado para apresentar a notificação ao usuário.

O segundo método, `TimeWillExpire` , será chamado logo antes que o tempo esteja prestes a ser executado para que a extensão de serviço processe a solicitação. Se a extensão de serviço não chamar o `contentHandler` no período de tempo alocado, o conteúdo original será exibido para o usuário.

### <a name="triggering-a-service-extension"></a>Disparando uma extensão de serviço

Com uma extensão de serviço criada e entregue com o aplicativo, ela pode ser disparada modificando a carga de notificação remota enviada ao dispositivo. Por exemplo:

```csharp
{
    aps : {
        alert : "New Message Available",
        mutable-content: 1
    },
    encrypted-content : "#theencryptedcontent"
}
```

A nova `mutable-content` chave especifica que a extensão de serviço precisará ser iniciada para atualizar o conteúdo de notificação remota. A `encrypted-content` chave contém os dados criptografados que a extensão de serviço pode descriptografar antes de apresentar ao usuário.

Dê uma olhada na seguinte extensão de serviço de exemplo:

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

Esse código descriptografa o conteúdo criptografado da `encrypted-content` chave, cria um novo `UNMutableNotificationContent` , define a `Body` propriedade para o conteúdo descriptografado e usa o `contentHandler` para apresentar a notificação ao usuário.

## <a name="summary"></a>Resumo

Este artigo abordou todas as maneiras como as notificações dos usuários foram aprimoradas pelo iOS 10. Ele apresentou a nova estrutura de notificação de usuário e como usá-la em um aplicativo Xamarin. iOS ou uma extensão de aplicativo.

## <a name="related-links"></a>Links Relacionados

- [Amostras do iOS 10](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)
- [Referência da estrutura do usernotifications](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Guia de programação de notificação local e remota](https://developer.apple.com/documentation/usernotifications)