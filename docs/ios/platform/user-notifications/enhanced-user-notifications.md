---
title: Notificações de usuário aprimorada
description: Este artigo aborda todas as maneiras que a notificação de usuários foram aprimorada por iOS 10 e como usá-los em um aplicativo xamarin.
ms.prod: xamarin
ms.assetid: 4E1FF652-28F0-4566-B383-9D12664401A4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/02/2017
ms.openlocfilehash: b27d415240f3b8cd25c4bc54f6d176c50e42a250
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="enhanced-user-notifications"></a>Notificações de usuário aprimorada

_Este artigo aborda todas as maneiras que a notificação de usuários foram aprimorada por iOS 10 e como usá-los em um aplicativo xamarin._

Novo para o iOS 10, a notificação do usuário framework permite a entrega e a manipulação de notificações de locais e remotas. Usando essa estrutura, um aplicativo ou extensão do aplicativo pode agendar a entrega de notificações locais, especificando um conjunto de condições, como local ou a hora do dia.

## <a name="about-user-notifications"></a>Sobre notificações de usuário

Como mencionado acima, a nova estrutura de notificação do usuário permite a entrega e a manipulação de notificações de locais e remotas. Usando essa estrutura, um aplicativo ou extensão do aplicativo pode agendar a entrega de notificações locais, especificando um conjunto de condições, como local ou a hora do dia.

Além disso, o aplicativo ou extensão pode receber (e potencialmente modificar) locais e remotas notificações como elas são entregues para o dispositivo iOS do usuário.

A nova estrutura de interface do usuário de notificação de usuário permite que um aplicativo ou extensão do aplicativo para personalizar a aparência de notificações de locais e remotas quando eles são apresentados ao usuário.

Essa estrutura fornece as seguintes maneiras em que um aplicativo pode fornecer notificações a um usuário:

- **Alertas Visual** - onde a notificação rola para baixo da parte superior da tela como uma faixa.
- **Som e vibrações** -pode ser associado uma notificação.
- **Notificações de ícone do aplicativo** - onde o ícone do aplicativo exibe uma notificação mostrando que o novo conteúdo estiver disponível, como o número de mensagens de email não lido.

Além disso, dependendo do contexto do usuário atual, há maneiras diferentes de uma notificação será exibida:

- Se o dispositivo é bloqueado, a notificação será rolar para baixo da parte superior da tela como uma faixa.
- Se o dispositivo estiver bloqueado, a notificação será exibida na tela de bloqueio do usuário.
- Se o usuário foi perdido uma notificação, podem abrir o Centro de notificação e exibir qualquer disponível, notificações de espera.

Um aplicativo xamarin tem dois tipos de notificações de usuário que é capaz de enviar:

- **Notificações locais** -esses são enviados por aplicativos instalados localmente no dispositivo do usuário.
- **Notificações remotas** -são enviadas de um controle remoto server e o apresentadas ao usuário ou disparam uma atualização em segundo plano do conteúdo do aplicativo.

### <a name="about-local-notifications"></a>Sobre notificações de Local

As notificações de Local que pode enviar um aplicativo iOS tem os recursos e os atributos a seguir:

- Eles são enviados por aplicativos que são locais no dispositivo do usuário. 
- Eles são podem ser configurados usar o tempo ou local com base em gatilhos. 
- O aplicativo agendamentos de notificação com o dispositivo do usuário e ele é exibido quando a condição do gatilho é atendida.
- Quando o usuário interage com uma notificação, o aplicativo receberá um retorno de chamada.

Alguns exemplos de notificações Local:

- Alertas de calendário
- Alertas de lembrete
- Gatilhos com reconhecimento de local

Para obter mais informações, consulte da Apple [Local e remoto guia de programação de notificação](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) documentação.

### <a name="about-remote-notifications"></a>Sobre notificações remotas

As notificações de remoto pode enviar um aplicativo iOS tem os recursos e os atributos a seguir:

- O aplicativo tem um componente do lado do servidor que se comunica com.
- Apple Push Notification Service (APNs) é usado para transmitir uma entrega de melhor esforço de notificações remoto para o dispositivo do usuário dos servidores de baseado em nuvem do desenvolvedor.
- Quando o aplicativo recebe a notificação remoto, ele será exibido ao usuário.
- Quando o usuário interage com a notificação, o aplicativo receberá um retorno de chamada.

Alguns exemplos de notificações remoto:

- Alertas de notícias
- Atualizações de esportes
- Mensagens instantâneas

Há dois tipos de notificações remoto disponíveis para um aplicativo iOS:

- **Voltado para o usuário** -esses são exibidos para o usuário no dispositivo.
- **Atualizações silenciosas** -elas fornecem um mecanismo para atualizar o conteúdo de um aplicativo iOS em segundo plano. Quando uma atualização silenciosa é recebida, o aplicativo pode chegar ao remover servidores suspenso o conteúdo mais recente.

Para obter mais informações, consulte da Apple [Local e remoto guia de programação de notificação](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) documentação.

### <a name="about-the-existing-notifications-api"></a>Sobre as notificações do API existentes

Antes de iOS 10, um aplicativo iOS usaria `UIApplication` para registrar uma notificação com o sistema e agendar como notificação deve ser disparada (por hora ou local).

Há vários problema que um desenvolvedor pode encontrar ao trabalhar com a API de notificação existente:

- Não havia diferentes retornos de chamada necessários para o Local ou remoto notificações que pode levar a eliminação de duplicação de código.
- O aplicativo limitou o controle da notificação depois que ele tinha foi agendado com o sistema.
- Não havia diferentes níveis de suporte em todas as plataformas existentes da Apple.

### <a name="about-the-new-user-notification-framework"></a>Sobre a nova estrutura de notificação do usuário

Com o iOS 10, a Apple introduziu a nova estrutura de notificação do usuário, que substitui o existente `UIApplication` método mencionado acima.

A estrutura de notificação do usuário fornece o seguinte:

- Uma API familiar que inclui a paridade de recursos com os métodos anteriores, tornando mais fácil de portar código do framework existente.
- Inclui um conjunto expandido de opções de conteúdo que permite que mais ricas notificações a serem enviadas para o usuário.
- Local e remoto notificações podem ser tratadas pelo mesmo código e retornos de chamada.
- Simplifica o processo de tratar retornos de chamada que são enviados para um aplicativo quando o usuário interage com uma notificação.
- Gerenciamento aprimorado de notificações pendentes e entregues, incluindo a capacidade de remover ou notificações de atualização.
- Adiciona a capacidade de fazer no aplicativo apresentação de notificações.
- Adiciona a capacidade para agendar e lidar com notificações de dentro de extensões de aplicativo.
- Adiciona um novo ponto de extensão para as notificações de si mesmos. 

A nova estrutura de notificação do usuário fornece uma notificação unificada API em várias das plataformas que Apple com suporte, incluindo: 

- **iOS** -suporte para gerenciar e agendar notificações completo.
- **tvOS** -acrescenta a capacidade de ícones de aplicativo de notificação para notificações de locais e remotos.
- **watchOS** - adiciona a capacidade de encaminhar as notificações de dispositivo do iOS emparelhada do usuário para seu Apple Watch e concede aos aplicativos de inspecionar a capacidade de fazer notificações locais diretamente no relógio.

Para obter mais informações, consulte da Apple [UserNotifications Framework referência](https://developer.apple.com/reference/usernotifications) e [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui) documentação.

## <a name="preparing-for-notification-delivery"></a>Preparando para a entrega da notificação

Antes de um iOS aplicativo pode enviar notificações para o usuário que o aplicativo deve ser registrado com o sistema e, como uma notificação é uma interrupção para o usuário, um aplicativo deve solicitar explicitamente a permissão antes de enviá-los.

Há três níveis diferentes de solicitações de notificação que o usuário pode aprovar para um aplicativo:

- Exibe a faixa.
- Alertas de som.
- Marca o ícone do aplicativo.

Além disso, esses níveis de aprovação devem ser solicitadas e definir para notificações de locais e remotas.

Permissão de notificação deve ser solicitado assim que o aplicativo é iniciado, adicionando o seguinte código para o `FinishedLaunching` método o `AppDelegate` e definindo o tipo de notificação desejado (`UNAuthorizationOptions`):

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

Além disso, um usuário pode alterar sempre os privilégios de notificação para um aplicativo a qualquer momento usando o **configurações** aplicativo no dispositivo. O aplicativo deve verificar os privilégios do usuário solicitado de notificação antes de apresentar uma notificação usando o seguinte código:

```csharp
// Get current notification settings
UNUserNotificationCenter.Current.GetNotificationSettings ((settings) => {
    var alertsAllowed = (settings.AlertSetting == UNNotificationSetting.Enabled);
}); 
``` 

### <a name="configuring-the-remote-notifications-environment"></a>Configurando o ambiente remoto notificações

Novo para iOS 10, o desenvolvedor deve informar o sistema operacional o ambiente de notificação por Push estão executando desenvolvimento ou de produção. Falha ao fornecer essas informações pode resultar no aplicativo que está sendo rejeitado quando enviado para a loja de aplicativos iTune com uma notificação semelhante à seguinte:

> Qualificação de notificação por Push ausente - seu aplicativo inclui uma API para o serviço de notificação por Push da Apple, mas o `aps-environment` direito está ausente da assinatura do aplicativo.

Para fornecer a qualificação necessária, faça o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Clique duas vezes o `Entitlements.plist` do arquivo no **solução preenchimento** para abri-lo para edição.
2. Alterne para o **fonte** exibição: 

    [![](enhanced-user-notifications-images/setup01.png "A exibição da fonte")](enhanced-user-notifications-images/setup01.png#lightbox)
3. Clique o **+** botão para adicionar uma nova chave.
4. Digite `aps-environment` para o **propriedade**, deixe o **tipo** como `String` e digite um `development` ou `production` para o **valor**: 

    [![](enhanced-user-notifications-images/setup02.png "A propriedade de ambiente de aps")](enhanced-user-notifications-images/setup02.png#lightbox)
5. Salve as alterações no arquivo.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Clique duas vezes o `Entitlements.plist` arquivo o **Gerenciador de soluções** para abri-lo para edição.
3. Clique o **+** botão para adicionar uma nova chave.
4. Digite `aps-environment` para o **propriedade**, deixe o **tipo** como `String` e digite um `development` ou `production` para o **valor**: 

    [![](enhanced-user-notifications-images/setup02w.png "A propriedade de ambiente de aps")](enhanced-user-notifications-images/setup02.png#lightbox)
5. Salve as alterações no arquivo.

-----

### <a name="registering-for-remote-notifications"></a>Se registrar para notificações remotas

Se o aplicativo será enviando e recebendo notificações remoto, ele ainda será necessário fazer _registro Token_ com o uso da `UIApplication` API. Esse registro exige que o dispositivo tenha um acesso de conexão de rede ativa APNs, o que irá gerar o token necessário que será enviado para o aplicativo. O aplicativo precisa encaminhar, em seguida, esse token para o aplicativo do lado do servidor do desenvolvedor se registrar para notificações remotas:

[![](enhanced-user-notifications-images/token01.png "Visão geral do registro de token")](enhanced-user-notifications-images/token01.png#lightbox)

Use o código a seguir para inicializar o registro solicitado:

```csharp
UIApplication.SharedApplication.RegisterForRemoteNotifications ();
```

O token que é enviado para o aplicativo do lado do servidor do desenvolvedor precisa ser incluído como parte do notificação de carga que get enviados do servidor APNs ao enviar uma notificação remoto:

[![](enhanced-user-notifications-images/token02.png "O token incluído como parte da carga de notificação")](enhanced-user-notifications-images/token02.png#lightbox)

O token atua como a chave que une a notificação e o aplicativo usado para abrir ou responder à notificação.

Para obter mais informações, consulte da Apple [Local e remoto guia de programação de notificação](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) documentação.

## <a name="notification-delivery"></a>Entrega da notificação

Com o aplicativo totalmente registrado e as permissões necessárias solicitada e concedida pelo usuário, o aplicativo agora está pronto para enviar e receber notificações. 

### <a name="providing-notification-content"></a>Fornecimento de conteúdo de notificação

Novo para iOS 10, a todas as notificações contêm um **título** e **subtítulo** que será sempre exibido com o **corpo** o conteúdo de notificação. Também nova é a capacidade de adicionar **mídia anexos** para o conteúdo de notificação.

Para criar o conteúdo de uma notificação de Local, use o seguinte código:

```csharp
var content = new UNMutableNotificationContent();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
```

Para notificações remoto, o processo é semelhante:

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

Com o conteúdo da notificação de criado, o aplicativo precisa agendar quando a notificação será apresentada ao usuário, definindo um *gatilho*. iOS 10 fornece quatro tipos diferentes de gatilho:

- **Notificação por push** - é usado exclusivamente com notificações remoto e é disparada quando o pacote de APNs envia uma notificação para o aplicativo em execução no dispositivo.
- **Intervalo de tempo** -permite que uma notificação Local sejam agendadas de um tempo de intervalo começam com agora e encerrando um algum ponto futuro. Por exemplo, `var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);`
- **Data do calendário** -notificações de Local permite que seja agendada para uma data e hora específicas.
- **Baseado local** -permite que notificações de Local para ser agendadas quando o dispositivo iOS está inserindo deixando uma localização geográfica específica ou em uma determinado proximidade com qualquer Beacons Bluetooth.

Quando uma notificação Local estiver pronta, o aplicativo precisa chamar o `Add` método o `UNUserNotificationCenter` objeto agendar sua exibição para o usuário. Para notificações remoto, o aplicativo do lado do servidor envia uma carga de notificação para os APNs, que envia o pacote de logon no dispositivo do usuário.

Reunir todas as partes, um exemplo de notificação de Local pode parecer com:

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

Novo para iOS 10, um aplicativo pode manipular notificações de forma diferente quando ele estiver em primeiro plano e uma notificação será disparada. Fornecendo um `UNUserNotificationCenterDelegate` e implementando o `UserNotificationCenter` método, o aplicativo pode assumir a responsabilidade para exibir a notificação. Por exemplo:

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

Esse código é simplesmente gravando o conteúdo do `UNNotification` para a saída do aplicativo e solicitando que o sistema para exibir o alerta padrão para a notificação. 

Se o aplicativo quiser exibir a própria notificação quando ele estava em primeiro plano e não usa os padrões do sistema, transmitir `None` ao manipulador de conclusão. Exemplo:

```csharp
completionHandler (UNNotificationPresentationOptions.None);
```

Com esse código no local, abra o `AppDelegate.cs` de arquivo para edição e altere o `FinishedLaunching` método para pesquisar, como o seguinte:

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

Esse código é anexar personalizado `UNUserNotificationCenterDelegate` acima para atual `UNUserNotificationCenter` para o aplicativo possa lidar com notificação enquanto ele está ativo e em primeiro plano.

## <a name="notification-management"></a>Gerenciamento de notificação

Novo para iOS 10, gerenciamento de notificação fornece acesso às notificações entregues e pendentes e adiciona a capacidade de remover, atualizar ou promover essas notificações.

Uma parte importante do gerenciamento de notificação é o _identificador de solicitação_ que foi atribuído à notificação quando ele foi criado e agendado com o sistema. Para notificações remoto, isso é atribuído por meio do novo `apps-collapse-id` campo no cabeçalho da solicitação HTTP.

O identificador de solicitação é usado para selecionar a notificação de que o aplicativo que deseja executar o gerenciamento de notificação no.

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

Para atualizar uma notificação existente, crie uma nova notificação com os parâmetros desejados modificados (como uma nova hora de gatilho) e adicioná-lo para o sistema com o mesmo identificador de solicitação que a notificação de que precisa ser modificada. Exemplo:


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

Já entregues notificações, a notificação existente será obter atualizada e promovida para a parte superior da lista nas telas de casa e bloqueio e Centro de notificações se ele já foi lido pelo usuário.

## <a name="working-with-notification-actions"></a>Trabalhando com ações de notificação

No iOS 10, notificações são entregues para o usuário não são estáticas em fornecem várias maneiras que o usuário pode interagir com eles (de interno para ações personalizadas).

Há três tipos de ações que um aplicativo iOS pode responder a:

- **Ação padrão** -isso é quando o usuário toca uma notificação para abrir o aplicativo e exibir os detalhes da notificação de determinado.
- **Ações personalizadas** -eles foram adicionados no iOS 8 e fornecem uma maneira rápida para o usuário executar uma tarefa personalizada diretamente a notificação sem a necessidade de iniciar o aplicativo. Eles podem ser apresentados como uma lista de botões com títulos personalizáveis ou um texto campo de entrada que pode ser executado em segundo plano (onde o aplicativo recebe uma pequena quantidade de tempo para atender à solicitação) ou o primeiro plano (onde o aplicativo é iniciado em primeiro plano fu lfill a solicitação). Ações personalizadas estão disponíveis em watchOS e iOS.
- **Ação de ignorar** -esta ação é enviada ao aplicativo quando o usuário ignorar uma notificação determinada.

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

Ao criar um novo `UNNotificationAction`, ela é atribuída uma identificação exclusiva e o título que será exibido no botão. Por padrão, a ação será criada como uma ação de plano de fundo, mas as opções podem ser fornecidas para ajustar o comportamento da ação (por exemplo defini-la como uma ação de primeiro plano).

Cada uma das ações criadas precisa ser associado uma categoria. Ao criar um novo `UNNotificationCategory`, ele recebe uma ID exclusiva, uma lista de ações que ele possa executar uma lista de IDs de intenção de fornecer mais informações sobre a intenção de ações na categoria e algumas opções para controlar o comportamento da categoria.

Por fim, todas as categorias são registradas com o sistema usando o `SetNotificationCategories` método.

### <a name="presenting-custom-actions"></a>Apresentando as ações personalizadas

Depois que um conjunto de ações personalizadas e as categorias foram criados e registrados com o sistema, podem ser apresentados do Local ou remoto notificações.

Para notificação remoto, defina um `category` na carga de notificação remoto que corresponde a uma das categorias criadas acima. Por exemplo:

```csharp
{
    aps:{
        alert:"Hello world!",
        category:"message"
    }
}
```

Para notificações de Local, defina o `CategoryIdentifier` propriedade o `UNMutableNotificationContent` objeto. Por exemplo:

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

### <a name="handling-dismiss-actions"></a>Tratamento de ignorar ações

Como mencionado acima, uma ação de ignorar podem ser enviada para o aplicativo quando o usuário ignorar uma notificação. Como essa não é uma ação padrão, uma opção precisará ser definida quando a categoria é criada. Por exemplo:

```csharp
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.CustomDismissAction);

```

### <a name="handling-action-responses"></a>Tratamento de respostas de ação

Quando o usuário interage com as categorias que foram criadas acima e ações personalizadas, o aplicativo precisa atender a tarefa solicitada. Isso é feito fornecendo uma `UNUserNotificationCenterDelegate` e implementando o `UserNotificationCenter` método. Por exemplo:

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

Passado na `UNNotificationResponse` classe tiver um `ActionIdentifier` propriedade que pode ser a ação padrão ou a ação de ignorar. Use `response.Notification.Request.Identifier` para testar as ações personalizadas.

O `UserText` propriedade mantém o valor de qualquer entrada de texto do usuário. O `Notification` propriedade contém a notificação de origem que inclui a solicitação com o gatilho e notificação de conteúdo. O aplicativo pode decidir se é um Local ou remota de notificação com base no tipo de gatilho.

## <a name="working-with-service-extensions"></a>Trabalhando com extensões de serviço

Ao trabalhar com notificações remoto, _extensões de serviço_ fornecem uma maneira de habilitar a criptografia de ponta a ponta dentro a carga de notificação. Extensões de serviços são uma extensão de Interface de usuário (disponível no iOS 10) que são executados em segundo plano com o objetivo principal de aumentar ou substituir o conteúdo visível de notificação antes de ela é apresentada ao usuário. 

[![](enhanced-user-notifications-images/extension01.png "Visão geral da extensão de serviço")](enhanced-user-notifications-images/extension01.png#lightbox)

Extensões de serviço devem ser executado rapidamente e recebem somente um curto período de tempo para ser executada pelo sistema. Se a extensão do serviço falhar ao concluir a tarefa em determinado período de tempo, um método de fallback será chamado. Se o fallback falhar, o conteúdo de notificação original será exibida ao usuário.

Alguns possíveis usos de extensões de serviço incluem:

- Fornece criptografia de ponta a ponta do conteúdo remoto notificação.
- Adicionar anexos remoto notificações para enriquecê-los.

### <a name="implementing-a-service-extension"></a>Implementando uma extensão de serviço

Para implementar uma extensão de serviço em um aplicativo xamarin, faça o seguinte:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Abra a solução do aplicativo no Visual Studio para Mac.
2. Com o botão direito no nome da solução no **solução preenchimento** e selecione **adicionar** > **adicionar novo projeto**.
3. Selecione **iOS** > **extensões** > **extensões de serviços de notificação** e clique no **próximo** botão: 

    [![](enhanced-user-notifications-images/extension02.png "Selecione as extensões de serviços de notificação")](enhanced-user-notifications-images/extension02.png#lightbox)
4. Insira um **nome** para a extensão e clique no **próximo** botão: 

    [![](enhanced-user-notifications-images/extension03.png "Insira um nome para a extensão")](enhanced-user-notifications-images/extension03.png#lightbox)
5. Ajustar o **nome do projeto** e/ou **nome da solução** se necessário e clique no **criar** botão: 

    [![](enhanced-user-notifications-images/extension04.png "Ajustar o nome do projeto e/ou o nome da solução")](enhanced-user-notifications-images/extension04.png#lightbox) 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Abra a solução do aplicativo no Visual Studio.
2. Clique com botão direito no nome da solução no **Solution Explorer** e selecione **Adicionar > Novo projeto...** .
3. Selecione **Visual C# > extensões do iOS > extensão do serviço de notificação**:

    [![](enhanced-user-notifications-images/extension01.w157-sml.png "Selecione as extensões de serviços de notificação")](enhanced-user-notifications-images/extension01.w157.png#lightbox)
4. Insira um **nome** para a extensão e clique no **Okey** botão.

-----

> [!IMPORTANT]
> O identificador de pacote para a extensão do serviço deve corresponder o identificador de pacote do aplicativo principal com `.appnameserviceextension` acrescentado ao final. Por exemplo, se o aplicativo principal tiver um identificador de pacote de `com.xamarin.monkeynotify`, a extensão do serviço deve ter um identificador de pacote de `com.xamarin.monkeynotify.monkeynotifyserviceextension`. Isso deve ser definido automaticamente quando a extensão é adicionada à solução. 

Há uma classe principal na extensão de serviço de notificação de que precisará ser modificada para fornecer a funcionalidade necessária. Por exemplo:

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

O primeiro método, `DidReceiveNotificationRequest`, passará o identificador de notificação, bem como o conteúdo de notificação por meio de `request` objeto. Passado na `contentHandler` precisa ser chamado para apresentar a notificação ao usuário.

O segundo método, `TimeWillExpire`, será chamado antes de tempo está prestes a esgotar o tempo limite para a extensão de serviço processar a solicitação. Se a extensão do serviço falhar ao chamar o `contentHandler` no período alocado de tempo, o conteúdo será exibido ao usuário.

### <a name="triggering-a-service-extension"></a>Disparar uma extensão de serviço

Com uma extensão de serviço criada e entregue com o aplicativo, ele pode ser disparado modificando a carga de notificação remoto enviado para o dispositivo. Por exemplo:

```csharp
{
    aps : {
        alert : "New Message Available",
        mutable-content: 1
    },
    encrypted-content : "#theencryptedcontent"
}
```

O novo `mutable-content` chave especifica que a extensão de serviço precisará ser iniciado para atualizar o conteúdo de notificação remoto. O `encrypted-content` chave contém os dados criptografados que pode descriptografar a extensão do serviço antes de apresentar ao usuário.

Veja o exemplo a seguir extensão de serviço:

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

Esse código descriptografa o conteúdo criptografado do `encrypted-content` chave, cria um novo `UNMutableNotificationContent`, define o `Body` propriedade para o conteúdo descriptografado e usa o `contentHandler` para apresentar a notificação ao usuário.

## <a name="summary"></a>Resumo

Este artigo abordou todas as maneiras que os usuários notificação foram aprimorada pelo iOS 10. A nova estrutura de notificação do usuário e como usá-lo em uma extensão de aplicativo ou um aplicativo xamarin apresentá-lo.



## <a name="related-links"></a>Links relacionados

- [Exemplos de iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Referência UserNotifications Framework](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Guia de programação de notificação de locais e remotos](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
