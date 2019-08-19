---
title: Notificações locais no Android
description: Esta seção mostra como implementar notificações locais no Xamarin. Android. Ele explica os vários elementos da interface do usuário de uma notificação do Android e discute as APIs envolvidas na criação e exibição de uma notificação.
ms.prod: xamarin
ms.assetid: 03E19D14-7C81-4D5C-88FC-C3A3A927DB46
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/16/2018
ms.openlocfilehash: 3ff6bc9c603360232b22a36dadb42b6caf31cada
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522387"
---
# <a name="local-notifications-on-android"></a>Notificações locais no Android

_Esta seção mostra como implementar notificações locais no Xamarin. Android. Ele explica os vários elementos da interface do usuário de uma notificação do Android e discute as APIs envolvidas na criação e exibição de uma notificação._

## <a name="local-notifications-overview"></a>Visão geral das notificações locais

O Android fornece duas áreas controladas pelo sistema para exibir os ícones de notificação e informações de notificação para o usuário. Quando uma notificação é publicada pela primeira vez, seu ícone é exibido na *área de notificação*, conforme mostrado na seguinte captura de tela:

![Área de notificação de exemplo em um dispositivo](local-notifications-images/01-notification-shade.png)

Para obter detalhes sobre a notificação, o usuário pode abrir a gaveta de notificação (que expande cada ícone de notificação para revelar o conteúdo da notificação) e executar todas as ações associadas às notificações. A captura de tela a seguir mostra uma *gaveta de notificação* que corresponde à área de notificação exibida acima:

[![Gaveta de notificação de exemplo exibindo três notificações](local-notifications-images/02-notification-drawer-sml.png)](local-notifications-images/02-notification-drawer.png#lightbox)

As notificações do Android usam dois tipos de layouts:

- ***Layout de base*** &ndash; um formato de apresentação compacto e fixo.

- ***Layout expandido*** &ndash; um formato de apresentação que pode expandir para um tamanho maior para revelar mais informações.

Cada um desses tipos de layout (e como criá-los) é explicado nas seções a seguir.

> [!NOTE]
> Este guia concentra-se nas [APIs do NotificationCompat](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html) da [biblioteca de suporte do Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Essas APIs garantirão a compatibilidade máxima com versões anteriores do Android 4,0 (nível de API 14).

### <a name="base-layout"></a>Layout de base

Todas as notificações do Android são criadas no formato de layout base, que, no mínimo, inclui os seguintes elementos:

1. Um *ícone de notificação*, que representa o aplicativo de origem ou o tipo de notificação se o aplicativo der suporte a diferentes tipos de notificações.

2. O *título*da notificação ou o nome do remetente se a notificação for uma mensagem pessoal.

3. A mensagem de notificação.

4. Um *carimbo de data/hora*.

Esses elementos são exibidos conforme ilustrado no diagrama a seguir:

[![Local dos elementos de notificação](local-notifications-images/03-notification-callouts-sml.png)](local-notifications-images/03-notification-callouts.png#lightbox)

Layouts de base são limitados a 64 pixels independentes de densidade (DP) em altura. O Android cria esse estilo básico de notificação por padrão.

Opcionalmente, as notificações podem exibir um ícone grande que representa o aplicativo ou a foto do remetente. Quando um ícone grande é usado em uma notificação no Android 5,0 e posterior, o ícone de notificação pequena é exibido como um selo sobre o ícone grande:

![Foto de notificação simples](local-notifications-images/04-simple-notification-photo.png)

A partir do Android 5,0, as notificações também podem aparecer na tela de bloqueio:

[![Exemplo de notificação de tela de bloqueio](local-notifications-images/05-lockscreen-notification-sml.png)](local-notifications-images/05-lockscreen-notification.png#lightbox)

O usuário pode tocar duas vezes na notificação da tela de bloqueio para desbloquear o dispositivo e ir até o aplicativo que originou essa notificação ou passar o dedo para ignorar a notificação. Os aplicativos podem definir o nível de visibilidade de uma notificação para controlar o que é mostrado na tela de bloqueio, e os usuários podem escolher se deseja permitir que o conteúdo confidencial seja mostrado nas notificações da tela de bloqueio.

O Android 5,0 introduziu um formato de apresentação de notificação de alta prioridade chamado *heads-up*. As notificações de cabeça para cima deslizam para baixo na parte superior da tela por alguns segundos e revertem-se para a área de notificação:

[![Notificações de exemplo-notificação](local-notifications-images/06-heads-up-notification-sml.png)](local-notifications-images/06-heads-up-notification.png#lightbox)

As notificações de pesquisa possibilitam que a interface do usuário do sistema Coloque informações importantes na frente dos usuários sem interromper o estado da atividade em execução no momento.

O Android inclui suporte para metadados de notificação para que as notificações possam ser classificadas e exibidas de forma inteligente. Os metadados de notificação também controlam como as notificações são apresentadas na tela de bloqueio e no formato de cabeçotes. Os aplicativos podem definir os seguintes tipos de metadados de notificação:

- **Prioridade** do &ndash; O nível de prioridade determina como e quando as notificações são apresentadas. Por exemplo, no Android 5,0, as notificações de alta prioridade são exibidas como notificações de cabeça.

- **Visibilidade** do &ndash; Especifica a quantidade de conteúdo de notificação a ser exibida quando a notificação aparece na tela de bloqueio.

- **Categoria** de Informa ao sistema como lidar com a notificação em várias circunstâncias, como quando o dispositivo está no modo *não incomodar.* &ndash;

> [!NOTE]
> A **visibilidade** e a **categoria** foram introduzidas no Android 5,0 e não estão disponíveis em versões anteriores do Android. A partir do Android 8,0, os [canais de notificação](#notif-chan) são usados para controlar como as notificações são apresentadas ao usuário.


### <a name="expanded-layouts"></a>Layouts expandidos

A partir do Android 4,1, as notificações podem ser configuradas com estilos de layout expandidos que permitem que o usuário expanda a altura da notificação para exibir mais conteúdo. Por exemplo, o exemplo a seguir ilustra uma notificação de layout expandida no modo contratado:

![Notificação contratada](local-notifications-images/07-contracted-notification.png)

Quando essa notificação é expandida, ela revela a mensagem inteira:

![Notificação expandida](local-notifications-images/08-expanded-notification.png)

O Android dá suporte a três estilos de layout expandidos para notificações de evento único:

- ***Texto grande*** &ndash; No modo contratado, exibe um trecho da primeira linha da mensagem seguida de dois pontos. No modo expandido, exibe a mensagem inteira (como visto no exemplo acima).

- ***Caixa de entrada*** &ndash; Em modo contratado, exibe o número de novas mensagens. No modo expandido, exibe a primeira mensagem de email ou uma lista das mensagens na caixa de entrada.

- ***Imagem*** do &ndash; No modo contratado, exibe apenas o texto da mensagem. No modo expandido, exibe o texto e uma imagem.

[Além da notificação básica](#beyond-the-basic-notification) (posteriormente neste artigo) explica como criar notificações de *Big Text*, *caixa de entrada*e *imagem* .

<a name="notif-chan"></a>
<a name="notification-channels"></a>
## <a name="notification-channels"></a>Canais de notificação

A partir do Android 8,0 (Oreo), você pode usar o recurso de *canais de notificação* para criar um canal personalizável pelo usuário para cada tipo de notificação que você deseja exibir. Os canais de notificação possibilitam que você agrupe notificações para que todas as notificações postadas em um canal exibam o mesmo comportamento. Por exemplo, você pode ter um canal de notificação destinado a notificações que exigem atenção imediata e um canal "mais silencioso" separado que é usado para mensagens informativas.

O aplicativo **YouTube** que é instalado com o Android Oreo lista duas categorias de notificação: **Baixar notificações** e **notificações gerais**:

[![Telas de notificação para o YouTube no Android Oreo](local-notifications-images/27-youtube-sml.png)](local-notifications-images/27-youtube.png#lightbox)

Cada uma dessas categorias corresponde a um canal de notificação. O aplicativo YouTube implementa um canal de **notificações de download** e um canal de **notificações gerais** . O usuário pode tocar em **baixar notificações**, que exibe a tela de configurações para o canal de notificações de download do aplicativo:

[![Baixar a tela de notificações para o aplicativo YouTube](local-notifications-images/28-yt-download-sml.png)](local-notifications-images/28-yt-download.png#lightbox)

Nessa tela, o usuário pode modificar o comportamento do canal de notificações de **Download** fazendo o seguinte:

- Defina o nível de importância como **urgente**, **alto**, **médio**ou **baixo**, que configura o nível de som e a interrupção Visual.

- Ativar ou desativar o ponto de notificação.

- Ativar ou desativar a luz piscando.

- Mostrar ou ocultar notificações na tela de bloqueio.

- Substitua a configuração não **incomodar** .

O canal **notificações gerais** tem configurações semelhantes:

[![Tela de notificações gerais para o aplicativo YouTube](local-notifications-images/29-yt-general-sml.png)](local-notifications-images/29-yt-general.png#lightbox)

Observe que você não tem controle absoluto sobre como seus canais de notificação interagem com o &ndash; usuário o usuário pode modificar as configurações de qualquer canal de notificação no dispositivo, como visto nas capturas de tela acima. No entanto, você pode configurar valores padrão (como será descrito abaixo). Como esses exemplos ilustram, o novo recurso de canais de notificação possibilita que você forneça ao usuário um controle refinado sobre diferentes tipos de notificações.

## <a name="notification-creation"></a>Criação de notificação

Para criar uma notificação no Android, use a classe [NotificationCompat. Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder) do pacote NuGet [Xamarin. Android. support. v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) . Essa classe possibilita criar e publicar notificações em versões mais antigas do Android.
`NotificationCompat.Builder`também é discutido.

`NotificationCompat.Builder`fornece métodos para definir as várias opções em uma notificação, como:

- O conteúdo, incluindo o título, o texto da mensagem e o ícone de notificação.

- O estilo da notificação, como *texto grande*, caixa de *entrada*ou estilo de *imagem* .

- A prioridade da notificação: mínima, baixa, padrão, alta ou máxima. No Android 8,0 e superior, a prioridade é definida por meio de um [_canal de notificação_](#notification-channels).

- A visibilidade da notificação na tela de bloqueio: pública, privada ou secreta.

- Metadados de categoria que ajudam o Android a classificar e filtrar a notificação.

- Uma intenção opcional que indica uma atividade a ser iniciada quando a notificação é tocada.

- A ID do canal de notificação em que a notificação será publicada (Android 8,0 e superior).

Depois de definir essas opções no construtor, você gera um objeto de notificação que contém as configurações. Para publicar a notificação, você passa esse objeto de notificação para o *Gerenciador de notificações*. O Android fornece a classe [notificationmanager](xref:Android.App.NotificationManager), que é responsável por publicar notificações e exibi-las para o usuário. Uma referência a essa classe pode ser Obtida de qualquer contexto, como uma atividade ou um serviço.

### <a name="creating-a-notification-channel"></a>Criando um canal de notificação

Os aplicativos que estão em execução no Android 8,0 devem criar um canal de notificação para suas notificações. Um canal de notificação requer as três seguintes informações:

- Uma cadeia de caracteres de ID que é exclusiva para o pacote que identificará o canal.
- O nome do canal que será exibido para o usuário.  O nome deve ter entre um e 40 caracteres.
- A importância do canal.

Os aplicativos precisarão verificar a versão do Android que estão em execução.
Dispositivos que executam versões anteriores ao Android 8,0 não devem criar um canal de notificação. O método a seguir é um exemplo de como criar um canal de notificação em uma atividade:

```csharp
void CreateNotificationChannel()
{
    if (Build.VERSION.SdkInt < BuildVersionCodes.O)
    {
        // Notification channels are new in API 26 (and not a part of the
        // support library). There is no need to create a notification
        // channel on older versions of Android.
        return;
    }

    var channelName = Resources.GetString(Resource.String.channel_name);
    var channelDescription = GetString(Resource.String.channel_description);
    var channel = new NotificationChannel(CHANNEL_ID, channelName, NotificationImportance.Default)
                  {
                      Description = channelDescription
                  };

    var notificationManager = (NotificationManager) GetSystemService(NotificationService);
    notificationManager.CreateNotificationChannel(channel);
}
```

O canal de notificação deve ser criado toda vez que a atividade é criada. Para o `CreateNotificationChannel` método, ele deve ser chamado `OnCreate` no método de uma atividade.

### <a name="creating-and-publishing-a-notification"></a>Criando e publicando uma notificação

Para gerar uma notificação no Android, siga estas etapas:

1. Crie uma `NotificationCompat.Builder` instância de um objeto.

2. Chame vários métodos no `NotificationCompat.Builder` objeto para definir as opções de notificação.

3. Chame o método [Build](xref:Android.App.Notification.Builder.Build) do `NotificationCompat.Builder` objeto para instanciar um objeto de notificação.

4. Chame o método [Notify](xref:Android.App.NotificationManager.Notify*) do Gerenciador de notificações para publicar a notificação.

Você deve fornecer pelo menos as seguintes informações para cada notificação:

- Um pequeno ícone (24x24 DP tamanho)

- Um título curto

- O texto da notificação

O exemplo de código a seguir ilustra como `NotificationCompat.Builder` usar o para gerar uma notificação básica. Observe que `NotificationCompat.Builder` os métodos oferecem suporte ao [encadeamento de método](https://en.wikipedia.org/wiki/Method_chaining); ou seja, cada método retorna o objeto Builder para que você possa usar o resultado da última chamada de método para invocar a próxima chamada de método:

```csharp
// Instantiate the builder and set notification elements:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

Neste exemplo, um novo `NotificationCompat.Builder` objeto chamado `builder` é instanciado, juntamente com a ID do canal de notificação a ser usado. O título e o texto da notificação são definidos, e o ícone de notificação é carregado de **recursos/empates/ic_notification. png**. A chamada para o método do `Build` Construtor de notificações cria um objeto de notificação com essas configurações. A próxima etapa é chamar o `Notify` método do Gerenciador de notificações. Para localizar o Gerenciador de notificações, você `GetSystemService`chama, conforme mostrado acima.

O `Notify` método aceita dois parâmetros: o identificador de notificação e o objeto de notificação. O identificador de notificação é um inteiro exclusivo que identifica a notificação para seu aplicativo. Neste exemplo, o identificador de notificação é definido como zero (0); no entanto, em um aplicativo de produção, você desejará dar a cada notificação um identificador exclusivo. Reutilizar o valor do identificador anterior em uma chamada `Notify` para faz com que a última notificação seja substituída.

Quando esse código é executado em um dispositivo Android 5,0, ele gera uma notificação semelhante ao exemplo a seguir:

![Resultado da notificação para o código de exemplo](local-notifications-images/09-hello-world.png)

O ícone de notificação é exibido no lado esquerdo da notificação &ndash; essa imagem de um &ldquo;círculo&rdquo; com um canal alfa para que o Android possa desenhar um plano de fundo circular cinza por trás dele. Você também pode fornecer um ícone sem um canal alfa. Para exibir uma imagem fotográfica como um ícone, consulte [formato de ícone grande](#large-icon-format) mais adiante neste tópico.

O carimbo de data/hora é definido automaticamente, mas você pode substituir essa configuração chamando o método [setwhen](xref:Android.App.Notification.Builder.SetWhen*) do construtor de notificações. Por exemplo, o exemplo de código a seguir define o carimbo de data/hora como o tempo atual:

```csharp
builder.SetWhen (Java.Lang.JavaSystem.CurrentTimeMillis());
```

### <a name="enabling-sound-and-vibration"></a>Habilitando som e vibração

Se você quiser que sua notificação também reproduza um som, poderá chamar o método [setpadrões](xref:Android.App.Notification.Builder.SetDefaults*) do construtor de notificações e passar o sinalizador `NotificationDefaults.Sound`:

```csharp
// Instantiate the notification builder and enable sound:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetDefaults (NotificationDefaults.Sound)
    .SetSmallIcon (Resource.Drawable.ic_notification);
```

Essa chamada para `SetDefaults` fará com que o dispositivo reproduza um som quando a notificação for publicada. Se você quiser que o dispositivo vibrar em vez de reproduzir um som, poderá passar `NotificationDefaults.Vibrate` para `SetDefaults.` se quiser que o dispositivo reproduza um som e vibrar o dispositivo, você pode passar ambos os `SetDefaults`sinalizadores para:

```csharp
builder.SetDefaults (NotificationDefaults.Sound | NotificationDefaults.Vibrate);
```

Se você habilitar o som sem especificar um som a ser reproduzido, o Android usará o som de notificação do sistema padrão. No entanto, você pode alterar o som que será reproduzido chamando o método [setsound](xref:Android.App.Notification.Builder.SetSound*) do construtor de notificações. Por exemplo, para reproduzir o som do alarme com sua notificação (em vez do som de notificação padrão), você pode obter o URI para o som do alarme do [toquemanager](xref:Android.Media.RingtoneManager) e passá-lo para `SetSound`:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Alarm));
```

Como alternativa, você pode usar o som de toque padrão do sistema para sua notificação:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Ringtone));
```

Depois de criar um objeto de notificação, é possível definir propriedades de notificação no objeto de notificação (em vez de configurá-los com `NotificationCompat.Builder` os métodos de adiantamento). Por exemplo, em vez de chamar `SetDefaults` o método para habilitar a vibração em uma notificação, você pode modificar diretamente o sinalizador de bits da propriedade de [padrões](xref:Android.App.Notification.Defaults) da notificação:

```csharp
// Build the notification:
Notification notification = builder.Build();

// Turn on vibrate:
notification.Defaults |= NotificationDefaults.Vibrate;
```

Este exemplo faz com que o dispositivo vibrar quando a notificação é publicada.

### <a name="updating-a-notification"></a>Atualizando uma notificação

Se você quiser atualizar o conteúdo de uma notificação após sua publicação, poderá reutilizar o objeto existente `NotificationCompat.Builder` para criar um novo objeto de notificação e publicar essa notificação com o identificador da última notificação. Por exemplo:

```csharp
// Update the existing notification builder content:
builder.SetContentTitle ("Updated Notification");
builder.SetContentText ("Changed to this message.");

// Build a notification object with updated content:
notification = builder.Build();

// Publish the new notification with the existing ID:
notificationManager.Notify (notificationId, notification);
```

Neste exemplo, o objeto existente `NotificationCompat.Builder` é usado para criar um novo objeto de notificação com um título e uma mensagem diferentes.
O novo objeto de notificação é publicado usando o identificador da notificação anterior, e isso atualiza o conteúdo da notificação publicada anteriormente:

![Notificação atualizada](local-notifications-images/12-updated-notification.png)

O corpo da notificação anterior é reutilizado &ndash; apenas o título e o texto da notificação é alterado enquanto a notificação é exibida na gaveta de notificação. O texto do título muda de "notificação de exemplo" para "notificação atualizada" e o texto da mensagem muda de "Olá, Mundo! Esta é minha primeira notificação! " para "alterado para esta mensagem".

Uma notificação permanece visível até que uma das três coisas aconteça:

- O usuário ignora a notificação (ou toca em *limpar tudo*).

- O aplicativo faz uma chamada para `NotificationManager.Cancel`, passando a ID de notificação exclusiva que foi atribuída quando a notificação foi publicada.

- O aplicativo chama `NotificationManager.CancelAll`.

Para obter mais informações sobre a atualização de notificações do Android, consulte [modificar uma notificação](https://developer.android.com/training/notify-user/managing.html#Updating).


### <a name="starting-an-activity-from-a-notification"></a>Iniciando uma atividade de uma notificação

No Android, é comum que uma notificação seja associada a uma *ação* &ndash; uma atividade que é iniciada quando o usuário toca na notificação. Essa atividade pode residir em outro aplicativo ou mesmo em outra tarefa. Para adicionar uma ação a uma notificação, você cria um objeto [PendingIntent](xref:Android.App.PendingIntent) e associa `PendingIntent` o com a notificação. Um `PendingIntent` é um tipo especial de intenção que permite que o aplicativo destinatário execute uma parte de código predefinida com as permissões do aplicativo de envio. Quando o usuário toca na notificação, o Android inicia a atividade especificada pelo `PendingIntent`.

O trecho de código a seguir ilustra como criar uma notificação com `PendingIntent` um que iniciará a atividade do aplicativo de origem, `MainActivity`:

```csharp
// Set up an intent so that tapping the notifications returns to this app:
Intent intent = new Intent (this, typeof(MainActivity));

// Create a PendingIntent; we're only using one PendingIntent (ID = 0):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    PendingIntent.GetActivity (this, pendingIntentId, intent, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including pending intent:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentIntent (pendingIntent)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first action notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

Esse código é muito semelhante ao código de notificação na seção anterior, exceto que um `PendingIntent` é adicionado ao objeto de notificação. Neste exemplo, o `PendingIntent` é associado à atividade do aplicativo de origem antes de ser passado para o método [SetContentIntent](xref:Android.App.Notification.Builder.SetContentIntent*) do construtor de notificações. O `PendingIntentFlags.OneShot` sinalizador é passado para o `PendingIntent.GetActivity` método para que o `PendingIntent` seja usado apenas uma vez. Quando esse código é executado, a seguinte notificação é exibida:

![Primeira notificação de ação](local-notifications-images/10-first-action-notification.png)

Tocar essa notificação leva o usuário de volta para a atividade de origem.

Em um aplicativo de produção, seu aplicativo deve lidar com a *pilha voltar* quando o usuário pressiona o botão **voltar** dentro da atividade de notificação (se você não estiver familiarizado com as tarefas do Android e a pilha voltar, consulte [tarefas e pilha voltar](https://developer.android.com/guide/components/tasks-and-back-stack.html)).
Na maioria dos casos, navegar para fora da atividade de notificação deve retornar o usuário do aplicativo e voltar para a tela inicial. Para gerenciar a pilha voltar, seu aplicativo usa a classe [TaskStackBuilder](xref:Android.App.TaskStackBuilder) para criar uma `PendingIntent` com uma pilha voltar.

Outra consideração real é que a atividade de origem pode precisar enviar dados para a atividade de notificação. Por exemplo, a notificação pode indicar que uma mensagem de texto chegou e a atividade de notificação (uma tela de exibição de mensagem) requer que a ID da mensagem exiba a mensagem para o usuário. A atividade que cria o `PendingIntent` pode usar o método de [tentativa. PutExtra](xref:Android.Content.Intent.PutExtra*) para adicionar dados (por exemplo, uma cadeia de caracteres) à intenção para que esses dados sejam passados para a atividade de notificação.

O exemplo de código a seguir ilustra como `TaskStackBuilder` usar o para gerenciar a pilha voltar e inclui um exemplo de como enviar uma única cadeia de caracteres de mensagem para uma atividade `SecondActivity`de notificação chamada:

```csharp
// Setup an intent for SecondActivity:
Intent secondIntent = new Intent (this, typeof(SecondActivity));

// Pass some information to SecondActivity:
secondIntent.PutExtra ("message", "Greetings from MainActivity!");

// Create a task stack builder to manage the back stack:
TaskStackBuilder stackBuilder = TaskStackBuilder.Create(this);

// Add all parents of SecondActivity to the stack:
stackBuilder.AddParentStack (Java.Lang.Class.FromType (typeof (SecondActivity)));

// Push the intent that starts SecondActivity onto the stack:
stackBuilder.AddNextIntent (secondIntent);

// Obtain the PendingIntent for launching the task constructed by
// stackbuilder. The pending intent can be used only once (one shot):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    stackBuilder.GetPendingIntent (pendingIntentId, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including
// the pending intent:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentIntent (pendingIntent)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my second action notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();

// Get the notification manager:
NotificationManager notificationManager =
    GetSystemService (Context.NotificationService) as NotificationManager;

// Publish the notification:
const int notificationId = 0;
notificationManager.Notify (notificationId, notification);
```

Neste exemplo de código, o aplicativo consiste em duas atividades: `MainActivity` (que contém o código de notificação acima) e `SecondActivity`a tela que o usuário vê depois de tocar na notificação. Quando esse código é executado, uma notificação simples (semelhante ao exemplo anterior) é apresentada. Tocar na notificação leva o usuário para a `SecondActivity` tela:

![Captura de tela da segunda atividade](local-notifications-images/11-second-activity.png)

A mensagem de cadeia de caracteres (passada para `PutExtra` o método da intenção) `SecondActivity` é recuperada por meio desta linha de código:

```csharp
// Get the message from the intent:
string message = Intent.Extras.GetString ("message", "");
```

Essa mensagem recuperada, "Greetings from MainActivity!", é exibida `SecondActivity` na tela, conforme mostrado na captura de telas acima. Quando o usuário pressiona o botão **voltar** enquanto estiver no `SecondActivity`, a navegação leva do aplicativo e de volta à tela que antecede o lançamento do aplicativo.

Para obter mais informações sobre como criar tentativas pendentes, consulte [PendingIntent](xref:Android.App.PendingIntent).

<a name="beyond-the-basic-notification" />

## <a name="beyond-the-basic-notification"></a>Além da notificação básica

As notificações assumem como padrão um formato de layout base simples no Android, mas você pode aprimorar esse `NotificationCompat.Builder` formato básico fazendo chamadas de método adicionais. Nesta seção, você aprenderá a adicionar um ícone de foto grande à sua notificação e verá exemplos de como criar notificações de layout expandidas.

<a name="large-icon-format" />

### <a name="large-icon-format"></a>Formato de ícone grande

As notificações do Android normalmente exibem o ícone do aplicativo de origem (no lado esquerdo da notificação). No entanto, as notificações podem exibir uma imagem ou uma foto (um *ícone grande*) em vez do ícone pequeno padrão. Por exemplo, um aplicativo de mensagens pode exibir uma foto do remetente em vez do ícone do aplicativo.

Aqui está um exemplo de uma notificação &ndash; básica do Android 5,0, que exibe apenas o ícone pequeno do aplicativo:

![Exemplo de notificação normal](local-notifications-images/13-sample-notification.png)

E aqui está uma captura de tela da notificação depois de modificá-la para exibir &ndash; um ícone grande, ela usa um ícone criado com base em uma imagem de um macaco Code do Xamarin:

![Exemplo de notificação de ícone grande](local-notifications-images/14-large-icon-sample.png)

Observe que, quando uma notificação é apresentada no formato de ícone grande, o ícone de aplicativo pequeno é exibido como um emblema no canto inferior direito do ícone grande.

Para usar uma imagem como um ícone grande em uma notificação, você chama o método [setlargeicon](xref:Android.App.Notification.Builder.SetLargeIcon*) do construtor de notificações e passa um bitmap da imagem. Ao `SetSmallIcon`contrário `SetLargeIcon` de, aceita apenas um bitmap. Para converter um arquivo de imagem em um bitmap, use a classe [BitmapFactory](xref:Android.Graphics.BitmapFactory) . Por exemplo:

```csharp
builder.SetLargeIcon (BitmapFactory.DecodeResource (Resources, Resource.Drawable.monkey_icon));
```

Este código de exemplo abre o arquivo de imagem em Resources/ **monkey_icon. png**, converte-o em um bitmap e passa o bitmap resultante `NotificationCompat.Builder`para. Normalmente, a resolução da imagem de origem é maior do que &ndash; o ícone pequeno, mas não muito maior. Uma imagem muito grande pode causar operações de redimensionamento desnecessárias que podem atrasar o lançamento da notificação.

### <a name="big-text-style"></a>Estilo de texto grande

O estilo de *texto grande* é um modelo de layout expandido que você usa para exibir mensagens longas em notificações. Como todas as notificações de layout expandidas, a notificação de Big Text é exibida inicialmente em um formato de apresentação compacta:

![Notificação de Big Text de exemplo](local-notifications-images/15-big-text-notification.png)

Nesse formato, apenas um trecho da mensagem é mostrado, encerrado por dois pontos. Quando o usuário arrasta para baixo na notificação, ele se expande para revelar a mensagem de notificação inteira:

![Notificação de Big Text expandida](local-notifications-images/16-big-text-expanded.png)

Esse formato de layout expandido também inclui o texto de resumo na parte inferior da notificação. A altura máxima da notificação de *Big Text* é 256 DP.

Para criar uma notificação de *Big Text* , você cria `NotificationCompat.Builder` uma instância de um objeto, como antes, e depois instancia e adiciona um `NotificationCompat.Builder` objeto [BigTextStyle](xref:Android.App.Notification.BigTextStyle) ao objeto. Veja um exemplo:

```csharp
// Instantiate the Big Text style:
Notification.BigTextStyle textStyle = new Notification.BigTextStyle();

// Fill it with text:
string longTextMessage = "I went up one pair of stairs.";
longTextMessage += " / Just like me. ";
//...
textStyle.BigText (longTextMessage);

// Set the summary text:
textStyle.SetSummaryText ("The summary text goes here.");

// Plug this style into the builder:
builder.SetStyle (textStyle);

// Create the notification and publish it ...
```

Neste exemplo, o texto da mensagem e o texto de resumo são armazenados `BigTextStyle` no objeto`textStyle`() antes de ser passado para`NotificationCompat.Builder.`

### <a name="image-style"></a>Estilo da imagem

O estilo de *imagem* (também chamado de estilo de *visão geral* ) é um formato de notificação expandido que você pode usar para exibir uma imagem no corpo de uma notificação. Por exemplo, um aplicativo de captura de tela ou um aplicativo de foto pode usar o estilo de notificação de *imagem* para fornecer ao usuário uma notificação da última imagem capturada. Observe que a altura máxima da notificação de *imagem* é 256 DP &ndash; Android redimensionará a imagem para se ajustar a essa restrição de altura máxima, dentro dos limites de memória disponível.

Assim como todas as notificações de layout expandidas, as notificações de *imagem* são exibidas primeiro em um formato compacto que exibe um trecho do texto da mensagem que o acompanha:

![A notificação de imagem compacta não mostra nenhuma imagem](local-notifications-images/17-image-compact.png)

Quando o usuário arrasta para baixo na notificação de *imagem* , ele se expande para revelar uma imagem. Por exemplo, aqui está a versão expandida da notificação anterior:

![A notificação de imagem expandida revela a imagem](local-notifications-images/18-image-expanded.png)

Observe que, quando a notificação é exibida no formato compacto, ela exibe o texto de notificação (o texto que é passado para o `SetContentText` método do construtor de notificações, como mostrado anteriormente). No entanto, quando a notificação é expandida para revelar a imagem, ela exibe o texto de resumo acima da imagem.

Para criar uma notificação de *imagem* , você cria `NotificationCompat.Builder` uma instância de um objeto como antes e, em seguida, cria e `NotificationCompat.Builder` insere um objeto [BigPictureStyle](xref:Android.App.Notification.BigPictureStyle) no objeto. Por exemplo:

```csharp
// Instantiate the Image (Big Picture) style:
Notification.BigPictureStyle picStyle = new Notification.BigPictureStyle();

// Convert the image to a bitmap before passing it into the style:
picStyle.BigPicture (BitmapFactory.DecodeResource (Resources, Resource.Drawable.x_bldg));

// Set the summary text that will appear with the image:
picStyle.SetSummaryText ("The summary text goes here.");

// Plug this style into the builder:
builder.SetStyle (picStyle);

// Create the notification and publish it ...
```

Como o `SetLargeIcon` método de `NotificationCompat.Builder`, o método [BigPicture](xref:Android.App.Notification.BigPictureStyle.BigPicture*) de `BigPictureStyle` requer um bitmap da imagem que você deseja exibir no corpo da notificação. Neste exemplo, o método [DecodeResource](xref:Android.Graphics.BitmapFactory.DecodeResource*) de `BitmapFactory` lê o arquivo de imagem localizado em Resources/ **desenháable/x_bldg. png** e o converte em um bitmap.

Você também pode exibir imagens que não são empacotadas como um recurso. Por exemplo, o código de exemplo a seguir carrega uma imagem do cartão SD local e a exibe em uma notificação de *imagem* :

```csharp
// Using the Image (Big Picture) style:
Notification.BigPictureStyle picStyle = new Notification.BigPictureStyle();

// Read an image from the SD card, subsample to half size:
BitmapFactory.Options options = new BitmapFactory.Options();
options.InSampleSize = 2;
string imagePath = "/sdcard/Pictures/my-tshirt.jpg";
picStyle.BigPicture (BitmapFactory.DecodeFile (imagePath, options));

// Set the summary text that will appear with the image:
picStyle.SetSummaryText ("Check out my new T-Shirt!");

// Plug this style into the builder:
builder.SetStyle (picStyle);

// Create notification and publish it ...
```

Neste exemplo, o arquivo de imagem localizado em **/sdcard/Pictures/My-tshirt.jpg** é carregado, redimensionado para metade de seu tamanho original e, em seguida, convertido em um bitmap para uso na notificação:

![Exemplo de imagem de camiseta em notificação](local-notifications-images/19-tshirt-notification.png)

Se você não souber o tamanho do arquivo de imagem com antecedência, é uma boa ideia encapsular a chamada para [BitmapFactory.](xref:Android.Graphics.BitmapFactory.DecodeFile*) decodificafile em um manipulador &ndash; de exceção. `OutOfMemoryError` uma exceção poderá ser gerada se a imagem for muito grande para que o Android redimensione.

Para saber mais sobre como carregar e decodificar imagens de bitmap grandes, confira [carregar bitmaps grandes com eficiência](https://github.com/xamarin/recipes/tree/master/Recipes/android/resources/general/load_large_bitmaps_efficiently).

### <a name="inbox-style"></a>Estilo da caixa de entrada

O formato da *caixa de entrada* é um modelo de layout expandido destinado à exibição de linhas de texto separadas (como um resumo de caixa de entrada de email) no corpo da notificação. A notificação de formato da *caixa de entrada* é exibida primeiro em um formato compacto:

![Exemplo de notificação de caixa de entrada Compact](local-notifications-images/20-inbox-compact.png)

Quando o usuário é arrastado para baixo na notificação, ele se expande para revelar um resumo de email, como mostrado na captura de tela abaixo:

![Exemplo de notificação de caixa de entrada expandida](local-notifications-images/21-inbox-expanded.png)

Para criar uma notificação de *caixa de entrada* , `NotificationCompat.Builder` você cria uma instância de um objeto, como antes, e adiciona `NotificationCompat.Builder`um objeto [inboxstyle](xref:Android.App.Notification.InboxStyle) ao. Veja um exemplo:

```csharp
// Instantiate the Inbox style:
Notification.InboxStyle inboxStyle = new Notification.InboxStyle();

// Set the title and text of the notification:
builder.SetContentTitle ("5 new messages");
builder.SetContentText ("chimchim@xamarin.com");

// Generate a message summary for the body of the notification:
inboxStyle.AddLine ("Cheeta: Bananas on sale");
inboxStyle.AddLine ("George: Curious about your blog post");
inboxStyle.AddLine ("Nikko: Need a ride to Evolve?");
inboxStyle.SetSummaryText ("+2 more");

// Plug this style into the builder:
builder.SetStyle (inboxStyle);
```

Para adicionar novas linhas de texto ao corpo da notificação, chame o método [AddLine](xref:Android.App.Notification.InboxStyle.AddLine*) do objeto `InboxStyle` (a altura máxima da notificação da *caixa de entrada* é 256 DP). Observe que, diferentemente do estilo de *texto grande* , o estilo da *caixa de entrada* dá suporte a linhas individuais de texto no corpo da notificação.

Você também pode usar o estilo da *caixa de entrada* para qualquer notificação que precise exibir linhas individuais de texto em um formato expandido. Por exemplo, o estilo de notificação de *caixa de entrada* pode ser usado para combinar várias notificações pendentes em uma notificação &ndash; de resumo, você pode atualizar uma única notificação de estilo de caixa de *entrada* com novas linhas de conteúdo de notificação (consulte [ Atualizar uma notificação](#updating-a-notification) acima), em vez de gerar um fluxo contínuo de notificações novas, principalmente semelhantes.

## <a name="configuring-metadata"></a>Configurando metadados

`NotificationCompat.Builder`inclui métodos que você pode chamar para definir metadados sobre sua notificação, como prioridade, visibilidade e categoria. O Android usa essas &mdash; informações junto com as configurações &mdash; de preferência do usuário para determinar como e quando exibir notificações.

### <a name="priority-settings"></a>Configurações de prioridade

Aplicativos em execução no Android 7,1 e uma necessidade mais baixa de definir a prioridade diretamente na própria notificação. A configuração de prioridade de uma notificação determina dois resultados quando a notificação é publicada:

- Em que a notificação aparece em relação a outras notificações.
    Por exemplo, as notificações de alta prioridade são apresentadas acima das notificações de prioridade mais baixa na gaveta de notificações, independentemente de quando cada notificação foi publicada.

- Se a notificação é exibida no formato de notificação de cabeçotes (Android 5,0 e posterior). Somente as notificações de prioridade *alta* e *máxima* são exibidas como notificações de cabeçotes.

O Xamarin. Android define as seguintes enumerações para definir a prioridade de notificação:

- `NotificationPriority.Max`&ndash; Alerta o usuário para uma condição urgente ou crítica (por exemplo, uma chamada de entrada, orientações de ativação ou um alerta de emergência). Em dispositivos Android 5,0 e posteriores, as notificações de prioridade máxima são exibidas no formato de cabeçotes.

- `NotificationPriority.High`&ndash; Informa o usuário sobre eventos importantes (como emails importantes ou a chegada de mensagens de chat em tempo real). Em dispositivos Android 5,0 e posteriores, as notificações de alta prioridade são exibidas no formato de cabeçotes.

- `NotificationPriority.Default`&ndash; Notifica o usuário sobre as condições que têm um nível médio de importância.

- `NotificationPriority.Low`&ndash; Para informações não urgentes que o usuário precisa ser informado (por exemplo, lembretes de atualização de software ou atualizações de rede social).

- `NotificationPriority.Min`&ndash; Para obter informações básicas que o usuário observa apenas ao exibir notificações (por exemplo, informações de localização ou clima).

Para definir a prioridade de uma notificação, chame o método [setanteriority](xref:Android.App.Notification.Builder.SetPriority*) do objeto `NotificationCompat.Builder`, passando o nível de prioridade. Por exemplo:

```csharp
builder.SetPriority (NotificationPriority.High);
```

No exemplo a seguir, a notificação de alta prioridade, "uma mensagem importante!" aparece na parte superior da gaveta de notificação:

![Exemplo de notificação de alta prioridade](local-notifications-images/22-hi-priority-drawer.png)

Como essa é uma notificação de alta prioridade, ela também é exibida como uma notificação de cabeça para cima na tela de atividade atual do usuário no Android 5,0:

![Notificações de exemplo-notificação](local-notifications-images/23-heads-up-example.png)

No exemplo a seguir, a notificação "pensamento para o dia" de baixa prioridade é exibida sob uma notificação de nível de bateria de prioridade mais alta:

![Notificação de baixa prioridade de exemplo](local-notifications-images/24-lo-priority-drawer.png)

Como a notificação "pensado para o dia" é uma notificação de baixa prioridade, o Android não a exibirá no formato de cabeçotes.

> [!NOTE]
> No Android 8,0 e superior, a prioridade das configurações do canal de notificação e do usuário determinará a prioridade da notificação.

### <a name="visibility-settings"></a>Configurações de visibilidade

A partir do Android 5,0, a configuração de *visibilidade* está disponível para controlar a quantidade de conteúdo de notificação exibida na tela de bloqueio seguro.
O Xamarin. Android define as seguintes enumerações para definir a visibilidade da notificação:

- `NotificationVisibility.Public`&ndash; O conteúdo completo da notificação é exibido na tela de bloqueio seguro.

- `NotificationVisibility.Private`&ndash; Somente informações essenciais são exibidas na tela de bloqueio seguro (como o ícone de notificação e o nome do aplicativo que o postou), mas o restante dos detalhes da notificação está oculto. Todas as notificações são `NotificationVisibility.Private`padrão para.

- `NotificationVisibility.Secret`&ndash; Nada é exibido na tela de bloqueio seguro, nem mesmo no ícone de notificação. O conteúdo da notificação só estará disponível depois que o usuário desbloquear o dispositivo.

Para definir a visibilidade de uma notificação, os aplicativos chamam `SetVisibility` o método `NotificationCompat.Builder` do objeto, passando a configuração de visibilidade. Por exemplo, esta chamada para `SetVisibility` faz a notificação `Private`:

```csharp
builder.SetVisibility (NotificationVisibility.Private);
```

Quando uma `Private` notificação é postada, somente o nome e o ícone do aplicativo são exibidos na tela de bloqueio seguro. Em vez da mensagem de notificação, o usuário vê "desbloquear seu dispositivo para ver esta notificação":

![Desbloquear a mensagem de notificação do dispositivo](local-notifications-images/25-lockscreen-private.png)

Neste exemplo, **NotificationsLab** é o nome do aplicativo de origem. Essa versão redação da notificação aparece somente quando a tela de bloqueio é segura (ou seja, protegida por meio de PIN, padrão ou senha &ndash; ) se a tela de bloqueio não for segura, o conteúdo completo da notificação estará disponível na tela de bloqueio.

### <a name="category-settings"></a>Configurações de categoria

A partir do Android 5,0, as categorias predefinidas estão disponíveis para classificação e filtragem de notificações. O Xamarin. Android fornece as seguintes enumerações para essas categorias:

- `Notification.CategoryCall`&ndash; Chamada telefônica de entrada.

- `Notification.CategoryMessage`&ndash; Mensagem de texto de entrada.

- `Notification.CategoryAlarm`&ndash; Uma condição de alarme ou expiração do temporizador.

- `Notification.CategoryEmail`&ndash; Mensagem de email de entrada.

- `Notification.CategoryEvent`&ndash; Um evento de calendário.

- `Notification.CategoryPromo`&ndash; Uma mensagem promocional ou anúncio.

- `Notification.CategoryProgress`&ndash; O progresso de uma operação em segundo plano.

- `Notification.CategorySocial`&ndash; Atualização de rede social.

- `Notification.CategoryError`&ndash; Falha de uma operação em segundo plano ou processo de autenticação.

- `Notification.CategoryTransport`&ndash; Atualização de reprodução de mídia.

- `Notification.CategorySystem`&ndash; Reservado para uso do sistema (status do sistema ou do dispositivo).

- `Notification.CategoryService`&ndash; Indica que um serviço em segundo plano está em execução.

- `Notification.CategoryRecommendation`&ndash; Uma mensagem de recomendação relacionada ao aplicativo em execução no momento.

- `Notification.CategoryStatus`&ndash; Informações sobre o dispositivo.

Quando as notificações são classificadas, a prioridade da notificação tem precedência sobre sua configuração de categoria. Por exemplo, uma notificação de alta prioridade será exibida como cara, mesmo que ela pertença à `Promo` categoria. Para definir a categoria de uma notificação, você chama o `SetCategory` método `NotificationCompat.Builder` do objeto, passando a configuração de categoria. Por exemplo:

```csharp
builder.SetCategory (Notification.CategoryCall);
```

O recurso *não incomodar* (novo no Android 5,0) filtra as notificações com base em categorias. Por exemplo, a tela não *incomodar* em **configurações** permite que o usuário isentar notificações para chamadas telefônicas e mensagens:

![Opções de tela não incomodar](local-notifications-images/26-do-not-disturb.png)

Quando o usuário configura *não incomodar* para bloquear todas as interrupções, exceto para chamadas telefônicas (conforme ilustrado na captura de tela acima), o Android permite que as notificações `Notification.CategoryCall` com uma configuração de categoria seja apresentada enquanto o dispositivo está em nãomodo de perturbador. Observe que `Notification.CategoryAlarm` as notificações nunca são bloqueadas no modo não *incomodar* .

O exemplo [LocalNotifications](https://docs.microsoft.com/samples/xamarin/monodroid-samples/localnotifications) demonstra como usar `NotificationCompat.Builder` o para iniciar uma segunda atividade de uma notificação. Este código de exemplo é explicado nas instruções [using local Notifications no Xamarin. Android](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md) .

### <a name="notification-styles"></a>Estilos de notificação

Para criar notificações de estilo *Big Text*, *Image*ou *inbox* com `NotificationCompat.Builder`o, seu aplicativo deve usar as versões de compatibilidade desses estilos. Por exemplo, para usar o estilo de *texto grande* , `NotificationCompat.BigTextstyle`crie uma instância:

```csharp
NotificationCompat.BigTextStyle textStyle = new NotificationCompat.BigTextStyle();

// Plug this style into the builder:
builder.SetStyle (textStyle);
```

Da mesma forma, seu aplicativo `NotificationCompat.InboxStyle` pode `NotificationCompat.BigPictureStyle` usar e para estilos de *caixa de entrada* e *imagem* , respectivamente.

### <a name="notification-priority-and-category"></a>Prioridade e categoria da notificação

`NotificationCompat.Builder`dá suporte `SetPriority` ao método (disponível a partir do Android 4,1). No entanto `SetCategory` , o método *não* tem `NotificationCompat.Builder` suporte pelo porque as categorias fazem parte do novo sistema de metadados de notificação que foi introduzido no Android 5,0.

Para dar suporte a versões mais antigas do `SetCategory` Android, em que o não está disponível, seu código pode verificar o nível da API `SetCategory` em tempo de execução para chamar condicionalmente quando o nível da API for igual ou maior que o Android 5,0 (API nível 21):

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetCategory (Notification.CategoryEmail);
}
```

Neste exemplo, a **estrutura de destino** do aplicativo é definida como Android 5,0 e a **versão mínima do Android** é definida como **Android 4,1 (API nível 16)** . Como `SetCategory` o está disponível no nível da API 21 e posteriores, este código `SetCategory` de exemplo chamará somente &ndash; quando estiver disponível, `SetCategory` ele não será chamado quando o nível da API for inferior a 21.

### <a name="lock-screen-visibility"></a>Visibilidade da tela de bloqueio

Como o Android não oferecia suporte a notificações de tela de bloqueio antes do Android 5,0 `NotificationCompat.Builder` (nível de API `SetVisibility` 21), o não oferece suporte ao método. Conforme explicado acima `SetCategory`para, seu código pode verificar o nível da API no tempo `SetVisiblity` de execução e chamar somente quando ele estiver disponível:

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetVisibility (Notification.Public);
}
```

## <a name="summary"></a>Resumo

Este artigo explicou como criar notificações locais no Android. Ele descreveu a anatomia de uma notificação, explicando como usar `NotificationCompat.Builder` o para criar notificações, como estilizar notificações em ícones grandes, *texto grande*, *imagem* e formatos de *caixa de entrada* , como definir configurações de metadados de notificação, como prioridade, visibilidade e categoria e como iniciar uma atividade de uma notificação. Este artigo também descreveu como essas configurações de notificação funcionam com os novos recursos de cabeçotes, tela de bloqueio e *não incomodar* introduzidos no Android 5,0. Por fim, você aprendeu a usar `NotificationCompat.Builder` o para manter a compatibilidade de notificação com versões anteriores do Android.

Para obter diretrizes sobre como criar notificações para Android, consulte [notificações](https://developer.android.com/guide/topics/ui/notifiers/notifications.html).

## <a name="related-links"></a>Links relacionados

- [NotificationsLab (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-notificationslab)
- [LocalNotifications (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/localnotifications)
- [Instruções locais sobre notificações do Android](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)
- [Notificando o usuário](https://developer.android.com/training/notify-user/index.html)
- [Notification](xref:Android.App.Notification)
- [NotificationManager](xref:Android.App.NotificationManager)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](xref:Android.App.PendingIntent)
