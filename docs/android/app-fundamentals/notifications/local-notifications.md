---
title: Notificações de locais
description: Esta seção mostra como implementar notificações de locais em xamarin. Ele explica os vários elementos de interface do usuário de uma notificação de Android e discute a API do envolvidos com a criação e exibindo uma notificação.
ms.prod: xamarin
ms.assetid: 03E19D14-7C81-4D5C-88FC-C3A3A927DB46
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 97c8372656f0cbfa5b8f7bb12d15b00feac4b5c3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="local-notifications"></a>Notificações de locais

_Esta seção mostra como implementar notificações de locais em xamarin. Ele explica os vários elementos de interface do usuário de uma notificação de Android e discute a API do envolvidos com a criação e exibindo uma notificação._

## <a name="local-notifications-overview"></a>Visão geral de notificações de local

Este tópico explica como implementar notificações locais em um aplicativo xamarin. Ele aborda as várias partes de uma notificação de Android, explica os estilos diferentes de notificação que estão disponíveis para os desenvolvedores de aplicativos e ele apresenta algumas das APIs que são usadas para criar e publicar as notificações.

Android fornece duas áreas controladas pelo sistema para exibir ícones de notificação e informações de notificação para o usuário. Quando uma notificação é publicada pela primeira vez, o ícone é exibido no *área de notificação*, conforme mostrado na captura de tela a seguir:

![Área de notificação de exemplo em um dispositivo](local-notifications-images/01-notification-shade.png)

Para obter detalhes sobre a notificação, o usuário pode abrir a gaveta de notificação (que se expande cada ícone de notificação para revelar o conteúdo da notificação) e executar as ações associadas com as notificações. Captura de tela a seguir mostra um *gaveta de notificação* que corresponde à área de notificação exibida acima:

[![Gaveta de notificação exemplo exibir três notificações](local-notifications-images/02-notification-drawer-sml.png)](local-notifications-images/02-notification-drawer.png#lightbox)

Notificações do Android usam dois tipos de layouts:

-   ***Layout de base*** &ndash; um formato de apresentação compact, fixo.

-   ***Layout expandido*** &ndash; um formato de apresentação que pode ser expandido para um tamanho maior para exibir mais informações.

Cada um desses tipos de layout (e como criá-los) são explicados nas seções a seguir.


### <a name="base-layout"></a>Layout de base

Todas as notificações do Android são criadas no formato de layout de base, que, no mínimo, inclui os seguintes elementos:

1.  Um *ícone de notificação*, que representa o aplicativo de origem ou o tipo de notificação se o aplicativo dá suporte a diferentes tipos de notificações.

2.  A notificação *título*, ou o nome do remetente se a notificação é uma mensagem pessoal.

3.  A mensagem de notificação.

4.  Um *timestamp*.

Esses elementos são exibidos, conforme ilustrado no diagrama a seguir:

[![Local dos elementos de notificação](local-notifications-images/03-notification-callouts-sml.png)](local-notifications-images/03-notification-callouts.png#lightbox)

Layouts de base estão limitados a 64 independente de densidade de pixels (dp) de altura. Por padrão, o Android cria esse estilo de notificação basic.

Opcionalmente, as notificações podem exibir um ícone grande que representa o aplicativo ou uma foto do remetente. Quando um ícone grande é usado em uma notificação no Android 5.0 e versões posteriores, o ícone de notificação pequeno é exibido como uma notificação sobre o ícone grande:

![Foto de notificação simples](local-notifications-images/04-simple-notification-photo.png)

A partir do Android 5.0, as notificações também podem aparecer no bloqueio:

[![Notificação de bloqueio de exemplo](local-notifications-images/05-lockscreen-notification-sml.png)](local-notifications-images/05-lockscreen-notification.png#lightbox)

O usuário pode com um toque duplo na notificação de bloqueio para desbloquear o dispositivo e ir para o aplicativo que originou a notificação, ou passe o dedo para ignorar a notificação. Aplicativos podem definir o nível de visibilidade de uma notificação para controlar o que é mostrado no bloqueio e os usuários podem escolher se deseja permitir que conteúdo confidencial a ser mostrado em notificações de bloqueio.

Android 5.0 introduziu um formato de apresentação de notificação de alta prioridade chamado *Heads-Up*. Notificações de Heads-Up deslize para baixo da parte superior da tela por alguns segundos e, em seguida, retreat novamente até a área de notificação:

[![Notificação de heads-up de exemplo](local-notifications-images/06-heads-up-notification-sml.png)](local-notifications-images/06-heads-up-notification.png#lightbox)

Notificações de Heads-Up possibilitam que o sistema de interface do usuário para colocar informações importantes na frente do usuário sem interromper o estado da atividade em execução no momento.

Android inclui suporte para metadados de notificação para que as notificações podem ser classificadas e exibidas de forma inteligente. Metadados de notificação também controla como as notificações são apresentadas no bloqueio e no formato Heads-Up. Aplicativos podem definir os seguintes tipos de metadados de notificação:

-   **Prioridade** &ndash; o nível de prioridade determina como e quando as notificações são apresentadas. Por exemplo, no Android 5.0, notificações de alta prioridade são exibidas como notificações Heads-Up.

-   **Visibilidade** &ndash; Especifica a quantidade de conteúdo notificação é a ser exibida quando a notificação é exibida sobre o bloqueio.

-   **Categoria de** &ndash; informa o sistema como tratar a notificação em várias circunstâncias, como quando o dispositivo está em *não interfira forma* modo.

**Observação:** **visibilidade** e **categoria** foram introduzidas no Android 5.0 e não estão disponíveis em versões anteriores do Android. Começando com Android 8.0, [canais de notificação](#notif-chan) são usados para controlar como as notificações são apresentadas ao usuário.


### <a name="expanded-layouts"></a>Layouts expandidos

A partir do Android 4.1, notificações podem ser configuradas com estilos de layout expandido que permitem ao usuário expandir a altura da notificação para exibir mais conteúdo. Por exemplo, o exemplo a seguir ilustra uma notificação de layout expandido no modo contratado:

![Notificação contratada](local-notifications-images/07-contracted-notification.png)

Quando essa notificação é expandida, ela revela a mensagem inteira:

![Notificação expandida](local-notifications-images/08-expanded-notification.png)

Android oferece suporte a três estilos de layout expandido para notificações de evento único:

-   ***Texto grande*** &ndash; no modo contratado, exibe um trecho da primeira linha da mensagem seguida por dois pontos. No modo expandido, exibe a mensagem inteira (como visto no exemplo acima).

-   ***Caixa de entrada*** &ndash; no modo contratado, exibe o número de novas mensagens. No modo expandido, exibe a primeira mensagem de email ou uma lista de mensagens na caixa de entrada.

-   ***Imagem*** &ndash; no modo contratado, exibe apenas o texto da mensagem. No modo expandido, exibe o texto e imagem.

[Além de notificação Basic](#beyond-the-basic-notification) (posteriormente neste artigo) explica como criar *texto grande*, *caixa de entrada*, e *imagem* notificações.


## <a name="notification-creation"></a>Criação de notificação

Para criar uma notificação no Android, você deve usar o [Notification.Builder](https://developer.xamarin.com/api/type/Android.App.Notification+Builder/) classe. `Notification.Builder` foi introduzido no Android 3.0 para simplificar a criação de objetos de notificação. Para criar notificações que são compatíveis com versões anteriores do Android, você pode usar o [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html) classe `Notification.Builder` (consulte [compatibilidade](#compatibility) mais adiante neste tópico para Para obter mais informações sobre como usar `NotificationCompat.Builder`).
`Notification.Builder` fornece métodos para definir as várias opções em uma notificação, como:

-   O conteúdo, incluindo o título, o texto da mensagem e o ícone de notificação.

-   O estilo de notificação, como *texto grande*, *caixa de entrada*, ou *imagem* estilo.

-   A prioridade da notificação: mínimo, baixo, padrão, de alta ou máximo.

-   A visibilidade da notificação sobre o bloqueio: public, private ou segredo.

-   Metadados de categoria que ajuda a Android classificar e filtrar a notificação.

-   Uma tentativa opcional que indica uma atividade para iniciar quando a notificação é tocada.

Depois de definir essas opções no construtor de, você pode gerar um objeto de notificação que contém as configurações. Para publicar a notificação, você passa esse objeto de notificação para o *Gerenciador de notificação*. Android fornece o [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/) classe, que é responsável por notificações de publicação e exibi-los para o usuário. Uma referência a essa classe pode ser obtida em qualquer contexto, como uma atividade ou um serviço.


### <a name="how-to-generate-a-notification"></a>Como gerar uma notificação

Para gerar uma notificação no Android, siga estas etapas:

1.  Criar uma instância de um `Notification.Builder` objeto.

2.  Chamar vários métodos de `Notification.Builder` objeto para definir opções de notificação.

3.  Chamar o [criar](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.Build/) método o `Notification.Builder` objeto instanciar um objeto de notificação.

4.  Chamar o [notificar](https://developer.xamarin.com/api/member/Android.App.NotificationManager.Notify/(System.Int32%2cAndroid.App.Notification)) método do Gerenciador de notificações para a notificação de publicação.

Você deve fornecer pelo menos as seguintes informações para cada notificação:

-   Um pequeno ícone (24 x 24 dp em tamanho)

-   Um título curto

-   O texto da notificação

O exemplo de código a seguir ilustra como usar `Notification.Builder` para gerar uma notificação básico. Observe que `Notification.Builder` suportam a métodos [encadeamento de método](http://en.wikipedia.org/wiki/Method_chaining); ou seja, cada método retorna o objeto de construtor, você pode usar o resultado da última chamada de método para invocar a próxima chamada do método:

```csharp
// Instantiate the builder and set notification elements:
Notification.Builder builder = new Notification.Builder (this)
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

Neste exemplo, um novo `Notification.Builder` objeto chamado `builder` é instanciada, o título e o texto da notificação são definidas e o ícone de notificação é carregado do **Resources/drawable/ic_notification.png**. A chamada para o construtor de notificação `Build` método cria um objeto de notificação com essas configurações. A próxima etapa é chamar o `Notify` método do Gerenciador de notificação. Para localizar o Gerenciador de notificação, você deve chamar `GetSystemService`, conforme mostrado acima.

O `Notify` método aceita dois parâmetros: o identificador de notificação e o objeto de notificação. O identificador de notificação é um inteiro exclusivo que identifica a notificação para seu aplicativo. Neste exemplo, o identificador de notificação é definido como zero (0); No entanto, em um aplicativo de produção, você deve fornecer um identificador exclusivo de cada notificação. Reutilizando o valor do identificador anterior em uma chamada para `Notify` faz com que a última notificação seja substituído.

Quando esse código é executado em um dispositivo Android 5.0, ele gera uma notificação de que se parece com o exemplo a seguir:

![Resultados de notificação para o código de exemplo](local-notifications-images/09-hello-world.png)

O ícone de notificação é exibido no lado esquerdo da notificação &ndash; esta imagem de um dentro de um círculo &ldquo;&rdquo; tem um canal alfa para que Android pode desenhar um plano de fundo circular cinza por trás dele. Você também pode fornecer um ícone sem um canal alfa. Para exibir uma imagem photographic como um ícone, consulte [formato de ícone grande](#large-icon-format) mais adiante neste tópico.

O carimbo de hora é definido automaticamente, mas você pode substituir essa configuração chamando o [padrãoQuando](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetWhen/) método do construtor de notificação. Por exemplo, o exemplo de código a seguir define o carimbo de hora para a hora atual:

```csharp
builder.SetWhen (Java.Lang.JavaSystem.CurrentTimeMillis());
```

### <a name="enabling-sound-and-vibration"></a>Habilitando o som e vibração

Se você quiser que a notificação também executar um som, você pode chamar o construtor de notificação [SetDefaults](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetDefaults/) método e passar o `NotificationDefaults.Sound` sinalizador:

```csharp
// Instantiate the notification builder and enable sound:
Notification.Builder builder = new Notification.Builder (this)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetDefaults (NotificationDefaults.Sound)
    .SetSmallIcon (Resource.Drawable.ic_notification);
```

Essa chamada para `SetDefaults` fará com que o dispositivo tocar um som quando a notificação é publicada. Se você quiser que o dispositivo Vibrar em vez de tocar um som, você pode passar `NotificationDefaults.Vibrate` para `SetDefaults.` se desejar que o dispositivo tocar um som e Vibrar o dispositivo, você pode passar a ambos os sinalizadores para `SetDefaults`:

```csharp
builder.SetDefaults (NotificationDefaults.Sound | NotificationDefaults.Vibrate);
```

Se você habilitar o som sem especificar um som a ser executado, o Android usa o som de notificação do sistema padrão. No entanto, você pode alterar o som que será executado ao chamar o construtor de notificação [SetSound](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetSound/p/Android.Net.Uri/) método. Por exemplo, para executar o alarme som com a notificação (em vez de som de notificação padrão), você pode obter o URI para o alarme som do [RingtoneManager](https://developer.xamarin.com/api/type/Android.Media.RingtoneManager/) e passá-lo para `SetSound`:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Alarm));
```

Como alternativa, você pode usar o toque de padrão de sistema som para a notificação:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Ringtone));
```

Depois de criar um objeto de notificação, é possível definir propriedades de notificação no objeto de notificação (em vez de configurá-los por meio de antemão `Notification.Builder` métodos). Por exemplo, em vez de chamar o `SetDefaults` método para habilitar vibração em uma notificação, você pode modificar diretamente o sinalizador de bit da notificação de [padrões](https://developer.xamarin.com/api/property/Android.App.Notification.Defaults/) propriedade:

```csharp
// Build the notification:
Notification notification = builder.Build();

// Turn on vibrate:
notification.Defaults |= NotificationDefaults.Vibrate;
```

Este exemplo faz com que o dispositivo Vibrar quando a notificação é publicada.

<a name="updating-a-notification" />

### <a name="updating-a-notification"></a>Uma notificação de atualização

Se você quiser atualizar o conteúdo de uma notificação após ele ter sido publicado, você pode reutilizar existente `Notification.Builder` objeto para criar um novo objeto de notificação e esta notificação com o identificador da última notificação de publicação. Por exemplo:

```csharp
// Update the existing notification builder content:
builder.SetContentTitle ("Updated Notification");
builder.SetContentText ("Changed to this message.");

// Build a notification object with updated content:
notification = builder.Build();

// Publish the new notification with the existing ID:
notificationManager.Notify (notificationId, notification);
```

Neste exemplo, existente `Notification.Builder` objeto é usado para criar um novo objeto de notificação com uma mensagem e título diferente.
O novo objeto de notificação é publicado usando o identificador da notificação anterior, e isso atualiza o conteúdo da notificação publicada anteriormente.

![Notificação atualizada](local-notifications-images/12-updated-notification.png)

O corpo da notificação anterior é reutilizado &ndash; somente o título e o texto da notificação muda enquanto a notificação é exibida na gaveta de notificação. O texto do título muda de "Notificação de exemplo" a "Notificação de atualização" e o texto da mensagem muda de "Hello World! Esta é minha primeira notificação!" para "Alterado para esta mensagem."

Uma notificação permanece visível até que ocorra por um dos três coisas:

-   O usuário ignorar a notificação (ou toca *Limpar tudo*).

-   O aplicativo faz uma chamada para `NotificationManager.Cancel`, passando a ID exclusiva de notificação que foi atribuída quando a notificação foi publicada.

-   O aplicativo chama `NotificationManager.CancelAll`.

Para obter mais informações sobre a atualização de notificações do Android, consulte [modificar uma notificação](http://developer.android.com/training/notify-user/managing.html#Updating).


### <a name="starting-an-activity-from-a-notification"></a>Iniciando uma atividade de uma notificação

No Android, é comum uma notificação ser associado com um *ação* &ndash; uma atividade que é iniciada quando o usuário toca a notificação. Essa atividade pode residir em outro aplicativo ou até mesmo em outra tarefa. Para adicionar uma ação em uma notificação, você cria um [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/) de objeto e associar o `PendingIntent` com a notificação. Um `PendingIntent` é um tipo especial de intenção que permite que o aplicativo de destino executar uma parte predefinida do código com as permissões do aplicativo de envio. Quando o usuário toca a notificação, o Android começa a atividade especificada pelo `PendingIntent`.

O trecho de código a seguir ilustra como criar uma notificação com um `PendingIntent` que abrirá a atividade do aplicativo de origem, `MainActivity`:

```csharp
// Set up an intent so that tapping the notifications returns to this app:
Intent intent = new Intent (this, typeof(MainActivity));

// Create a PendingIntent; we're only using one PendingIntent (ID = 0):
const int pendingIntentId = 0;
PendingIntent pendingIntent =
    PendingIntent.GetActivity (this, pendingIntentId, intent, PendingIntentFlags.OneShot);

// Instantiate the builder and set notification elements, including pending intent:
Notification.Builder builder = new Notification.Builder(this)
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

Esse código é muito semelhante ao código de notificação na seção anterior, exceto que um `PendingIntent` é adicionado ao objeto de notificação. Neste exemplo, o `PendingIntent` está associada à atividade do aplicativo de origem antes de ser passado para o construtor de notificação [SetContentIntent](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetContentIntent/) método. O `PendingIntentFlags.OneShot` sinalizador é passado para o `PendingIntent.GetActivity` método para que o `PendingIntent` é usado apenas uma vez. Quando esse código é executado, a notificação a seguir é exibida:

![Primeira notificação de ação](local-notifications-images/10-first-action-notification.png)

Tocar esta notificação leva o usuário de volta para a atividade de origem.

Em um aplicativo de produção, seu aplicativo deve tratar o *pilha voltar* quando o usuário pressiona o **novamente** botão dentro da atividade de notificação (se você não estiver familiarizado com a pilha de volta e tarefas Android, consulte [ Tarefas e pilha voltar](http://developer.android.com/guide/components/tasks-and-back-stack.html)).
Na maioria dos casos, navegar para trás da atividade de notificação deve retornar o usuário para fora do aplicativo e de volta para a tela inicial. Para gerenciar a pilha de volta, seu aplicativo usa o [TaskStackBuilder](https://developer.xamarin.com/api/type/Android.App.TaskStackBuilder/) classe para criar um `PendingIntent` com uma pilha de volta.

Outra consideração do mundo real é que a atividade de origem pode ser necessário enviar dados para a atividade de notificação. Por exemplo, a notificação pode indicar que uma mensagem de texto foi recebido, e a atividade de notificação (uma mensagem exibindo tela), requer a identificação da mensagem para exibir a mensagem para o usuário. A atividade que cria o `PendingIntent` pode usar o [Intent.PutExtra](https://developer.xamarin.com/api/member/Android.Content.Intent.PutExtra/p/System.String/System.String/) método para adicionar dados (por exemplo, uma cadeia de caracteres) para o objetivo, para que esses dados são passados para a atividade de notificação.

O exemplo de código a seguir ilustra como usar `TaskStackBuilder` gerenciar a pilha de volta e inclui um exemplo de como enviar uma cadeia de caracteres única mensagem para uma atividade de notificação chamada `SecondActivity`:

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
Notification.Builder builder = new Notification.Builder (this)
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

Este exemplo de código, o aplicativo consiste em duas atividades: `MainActivity` (que contém o código de notificação acima), e `SecondActivity`, a tela em que o usuário vê depois de tocar a notificação. Quando esse código é executado, é apresentada uma notificação simple (semelhante ao exemplo anterior). Toque na notificação leva o usuário para o `SecondActivity` tela:

![Segunda tela de atividade](local-notifications-images/11-second-activity.png)

A mensagem de cadeia de caracteres (passado para a intenção `PutExtra` método) é recuperado `SecondActivity` por esta linha de código:

```csharp
// Get the message from the intent:
string message = Intent.Extras.GetString ("message", "");
```

Essa mensagem recuperada, "Saudações de MainActivity!," é exibida no `SecondActivity` tela, conforme mostrado na captura de tela acima. Quando o usuário pressiona o **novamente** botão ao `SecondActivity`, navegação leva para fora do aplicativo e de volta para a tela anterior a inicialização do aplicativo.

Para obter mais informações sobre como criar pendentes tentativas, consulte [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/).


<a name="notif-chan" />

## <a name="notification-channels"></a>Canais de notificação

A partir do Android 8.0 (Oreos), você pode usar o *canais de notificação* recurso para criar um canal de usuário personalizável para cada tipo de notificação que você deseja exibir. Canais de notificação tornam possível para você para notificações de grupo para que todas as notificações lançadas em um anexo de canal o mesmo comportamento. Por exemplo, você pode ter um canal de notificação que é destinado para notificações que exigem atenção imediata e um canal separado "mais silencioso" que é usado para mensagens informativas.

O **YouTube** duas categorias de notificação de lista do aplicativo que é instalado com o Android Oreos: **baixar notificações** e **gerais notificações**:

[![Telas de notificação para YouTube no Android Oreos](local-notifications-images/27-youtube-sml.png)](local-notifications-images/27-youtube.png#lightbox)

Cada uma dessas categorias corresponde a um canal de notificação. A YouTube aplicativo implementa uma **notificações de Download** channel e um **geral notificações** canal. O usuário pode tocar **baixar notificações**, que exibe a tela de configurações para o canal de notificações de baixar o aplicativo:

[![Baixar tela de notificações para o aplicativo YouTube](local-notifications-images/28-yt-download-sml.png)](local-notifications-images/28-yt-download.png#lightbox)

Nessa tela, o usuário pode modificar o comportamento do **baixar** notificações de canal, fazendo o seguinte:

-   Definir a importância a **urgente**, **alta**, **médio**, ou **baixa**, que configura o nível de interrupção visual e som.

-   Ative o ponto de notificação ou desativado.

-   Ative a luz pisca ou desativado.

-   Mostrar ou ocultar as notificações na tela de bloqueio.

-   Substituir o **não interfira forma** configuração.

O **geral notificações** canal tem configurações semelhantes:

[![Tela de notificações gerais do aplicativo YouTube](local-notifications-images/29-yt-general-sml.png)](local-notifications-images/29-yt-general.png#lightbox)

Observe que você não tem absoluto controle sobre como seus canais de notificação interagem com o usuário &ndash; o usuário pode modificar as configurações para qualquer canal de notificação no dispositivo, conforme visto nas capturas de tela acima. No entanto, você pode configurar valores padrão (que será descrito abaixo). Como estes exemplos ilustram, o novo recurso de canais de notificação possibilita que você forneça aos usuários um controle refinado sobre os diferentes tipos de notificações.

Você deve adicionar suporte para canais de notificação a seu aplicativo? Se você estiver direcionando Android 8.0, seu aplicativo *deve* implementar canais de notificação.
Um aplicativo direcionado para Oreos que tenta enviar uma notificação de local para o usuário sem o uso de um canal de notificação não exiba a notificação Oreos dispositivos. Se você não selecionar 8.0 Android, o aplicativo ainda será executado no Android 8.0, mas com o mesmo comportamento de notificação, ele seria exibem quando em execução no Android 7.1 ou anterior.


### <a name="creating-a-notification-channel"></a>Criar um canal de notificação

Para criar um canal de notificação, faça o seguinte:

1. Construir um [NotificationChannel](https://developer.android.com/reference/android/app/NotificationChannel.html) objeto com o seguinte:

    - Uma cadeia de caracteres de ID é exclusiva dentro de seu pacote. No exemplo a seguir, a cadeia de caracteres `com.xamarin.myapp.urgent` é usado.

    - O nome de usuário visível do canal (menos de 40 caracteres). No exemplo a seguir, o nome **urgente** é usado.

    - A importância do canal, que controla como semelhantes notificações são lançadas para o `NotificationChannel`. A importância pode ser `Default`, `High`, `Low`, `Max`, `Min`, `None`, ou `Unspecified`.

    Passar esses valores para o [construtor](https://developer.android.com/reference/android/app/NotificationChannel.html#NotificationChannel%28java.lang.String,%20java.lang.CharSequence,%20int%29) (neste exemplo, `Resource.String.noti_chan_urgent` é definido como **urgente**):

    ```csharp
    public const string URGENT_CHANNEL = "com.xamarin.myapp.urgent";
    . . .
    string chanName = GetString (Resource.String.noti_chan_urgent);
    var importance = NotificationImportance.High;
    NotificationChannel chan =
       new NotificationChannel (URGENT_CHANNEL, chanName, importance);
    ```

2.  Configurar o `NotificationChannel` objeto com as configurações iniciais.
    Por exemplo, o código a seguir configura o `NotificationChannel` do objeto para que notificações lançadas para esse canal serão Vibrar o dispositivo e aparecem na tela de bloqueio por padrão:

    ```csharp
    chan.EnableVibration (true);
    chan.LockscreenVisibility = NotificationVisibility.Public;
    ```

3.  Envie o objeto do canal de notificação para o Gerenciador de notificação:

    ```csharp
    NotificationManager notificationManager =
        (NotificationManager) GetSystemService (NotificationService);
    notificationManager.CreateNotificationChannel (chan);
    ```


### <a name="posting-to-a-notifications-channel"></a>Para um canal de notificações de lançamento

Para lançar uma notificação para um canal de notificação, faça o seguinte:

1.  Configure a notificação usando o `Notification.Builder`, passando a ID do canal para o `SetChannelId` método. Por exemplo:

    ```csharp
    Notification.Builder builder = new Notification.Builder (this)
        .SetContentTitle ("Attention!")
        .SetContentText ("This is an urgent notification message!")
        .SetChannelId (URGENT_CHANNEL);
    ```

2.  Criar e emitir a notificação usando o Gerenciador de notificação [notificar](https://developer.xamarin.com/api/member/Android.App.NotificationManager.Notify/p/System.Int32/Android.App.Notification/) método:

    ```csharp
    const int notificationId = 0;
    notificationManager.Notify (notificationId, builder.Build());
    ```

Você pode repetir as etapas acima para criar outro canal de notificação para mensagens informativas. Esse segundo canal pode, por padrão, desabilitar vibração, defina a visibilidade de tela de bloqueio padrão como `Private`e defina a importância de notificação para `Default`.

Para obter um exemplo de código completo Android Oreos de canais de notificação em ação, consulte o [NotificationChannels](https://developer.xamarin.com/samples/monodroid/android-o/NotificationChannels) exemplo; esta amostra de aplicativo gerencia dois canais e define as opções de notificação adicional.



<a name="beyond-the-basic-notification" />

## <a name="beyond-the-basic-notification"></a>Além de notificação Basic

Notificações padrão em um formato de layout de base sem ornamentos no Android, mas você pode aprimorar esse formato básico, tornando adicionais `Notification.Builder` chamadas de método. Nesta seção, você aprenderá a adicionar um ícone de foto grande para a notificação e você verá exemplos de como criar notificações de layout expandido.

<a name="large-icon-format" />

### <a name="large-icon-format"></a>Formato de ícone grande

Notificações do Android normalmente exibem o ícone do aplicativo original (no lado esquerdo da notificação). No entanto, as notificações podem exibir uma imagem ou uma foto (um *ícone grande*) em vez do ícone pequeno padrão. Por exemplo, um aplicativo de mensagens pode exibir uma foto do remetente, em vez de no ícone do aplicativo.

Aqui está um exemplo de uma notificação de Android 5.0 basic &ndash; exibe apenas o ícone pequeno do aplicativo:

![Notificação normal de exemplo](local-notifications-images/13-sample-notification.png)

E aqui está uma captura de tela da notificação depois de modificá-lo para exibir um ícone grande &ndash; usa um ícone criado a partir de uma imagem de um monkey de código de Xamarin:

![Notificação de ícone grande de exemplo](local-notifications-images/14-large-icon-sample.png)

Observe que, quando uma notificação é apresentada no formato de ícone grande, o ícone pequeno do aplicativo é exibido como uma notificação no canto inferior direito do ícone grande.

Para usar uma imagem como um ícone grande em uma notificação, você chama o construtor de notificação [SetLargeIcon](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetLargeIcon/) método e passar um bitmap da imagem. Ao contrário de `SetSmallIcon`, `SetLargeIcon` aceita apenas um bitmap. Para converter um arquivo de imagem em um bitmap, você deve usar o [BitmapFactory](https://developer.xamarin.com/api/type/Android.Graphics.BitmapFactory/) classe. Por exemplo:

```csharp
builder.SetLargeIcon (BitmapFactory.DecodeResource (Resources, Resource.Drawable.monkey_icon));
```

Este exemplo de código abre o arquivo de imagem em **Resources/drawable/monkey_icon.png**, converte-o em um bitmap e passa o bitmap resultante para `Notification.Builder`. Normalmente, a resolução de imagem de origem é maior do que o ícone pequeno &ndash; , mas não muito maior. Uma imagem que é muito grande pode causar o desnecessárias operações de redimensionamento que podem atrasar o lançamento da notificação.
Para obter mais informações sobre tamanhos de ícones de notificação no Android, consulte [ícones de notificação](http://developer.android.com/design/style/iconography.html#notification).


### <a name="big-text-style"></a>Estilo de texto grande

O *texto grande* estilo é um modelo de layout expandido que você usa para exibir mensagens de tempo de notificações. Como todas as notificações de layout expandido, a notificação de texto grande é exibida inicialmente em um formato de apresentação compact:

![Notificação de texto grande de exemplo](local-notifications-images/15-big-text-notification.png)

Nesse formato, somente um trecho da mensagem é mostrado, terminada por dois pontos. Quando o usuário arrasta para baixo na notificação, ele expande para mostrar a mensagem de notificação de inteiro:

![Notificação de texto grande expandida](local-notifications-images/16-big-text-expanded.png)

Esse formato de layout expandido também inclui o texto do resumo na parte inferior da notificação. A altura máxima do *texto grande* notificação é 256 dp.

Para criar um *texto grande* notificação, você cria uma instância uma `Notification.Builder` do objeto, como antes e, em seguida, criar uma instância e adicionar um [BigTextStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigTextStyle/) o objeto para o `Notification.Builder` objeto. Por exemplo:

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

Neste exemplo, o texto da mensagem e o texto do resumo são armazenados no `BigTextStyle` objeto (`textStyle`) antes de ser passado para `Notification.Builder.`


### <a name="image-style"></a>Estilo de imagem

O *imagem* estilo (também chamado de *panorama* estilo) é um formato de notificação expandido que você pode usar para exibir uma imagem no corpo de uma notificação. Por exemplo, um aplicativo de captura de tela ou um aplicativo de fotos pode usar o *imagem* notificação de estilo para fornecer ao usuário uma notificação da última imagem que foi capturada. Observe que a altura máxima do *imagem* notificação é 256 dp &ndash; Android redimensionará a imagem para ajustá-lo para essa restrição de altura máxima, dentro dos limites de memória disponível.

Como todas expandido notificações de layout, *imagem* pela primeira vez, as notificações são exibidas em um formato compacto que exibe um trecho do texto da mensagem a seguir:

![Notificação de imagem Compact não mostra nenhuma imagem](local-notifications-images/17-image-compact.png)

Quando o usuário arrasta para baixo *imagem* notificação, ele expande para mostrar uma imagem. Por exemplo, aqui está a versão expandida da notificação anterior:

![Imagem expandida notificação revela imagem](local-notifications-images/18-image-expanded.png)

Observe que, quando a notificação é exibida em formato compacto, ele exibirá o texto de notificação (o texto que é passado para o construtor de notificação `SetContentText` método, conforme mostrado anteriormente). No entanto, quando a notificação é expandida para revelar a imagem, ele exibe o texto de resumo sobre a imagem.

Para criar um *imagem* notificação, você cria uma instância uma `Notification.Builder` objeto como antes e, em seguida, criar e inserir um [BigPictureStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigPictureStyle/) objeto para o `Notification.Builder` objeto. Por exemplo:

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

Como o `SetLargeIcon` método `Notification.Builder`, o [BigPicture](https://developer.xamarin.com/api/member/Android.App.Notification+BigPictureStyle.BigPicture/) método `BigPictureStyle` requer um bitmap da imagem que você deseja exibir no corpo da notificação. Neste exemplo, o [DecodeResource](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeResource/(Android.Content.Res.Resources%2cSystem.Int32)) método `BitmapFactory` lê o arquivo de imagem localizado em **Resources/drawable/x_bldg.png** e o converte em um bitmap.

Você também pode exibir imagens que não são empacotadas como um recurso. Por exemplo, o código de exemplo a seguir carrega uma imagem de um cartão SD local e o exibe em uma *imagem* notificação:

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

Neste exemplo, o arquivo de imagem localizado em **/sdcard/Pictures/my-tshirt.jpg** é carregado, redimensionado para metade de seu tamanho original e, em seguida, convertida em um bitmap para uso na notificação:

![Imagem de camisa de exemplo na notificação](local-notifications-images/19-tshirt-notification.png)

Se você não souber o tamanho do arquivo de imagem previamente, é uma boa ideia encapsulam a chamada para [BitmapFactory.DecodeFile](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeFile/p/System.String/Android.Graphics.BitmapFactory+Options/) em um manipulador de exceção &ndash; um `OutOfMemoryError` exceção pode ser gerada se a imagem é muito grande para Para redimensionar o Android.

Para obter mais informações sobre como carregar e decodificação de imagens de bitmap grande, consulte [carga eficiente Bitmaps grandes](https://developer.xamarin.com/recipes/android/resources/general/load_large_bitmaps_efficiently).


### <a name="inbox-style"></a>Estilo de caixa de entrada

O *caixa de entrada* formato é um modelo de layout expandido destinado para exibir linhas separadas de texto (por exemplo, um resumo de entrada de email) no corpo da notificação. O *caixa de entrada* formato de notificação é exibida pela primeira vez em um formato compacto:

![Notificação do compact de caixa de entrada de exemplo](local-notifications-images/20-inbox-compact.png)

Quando o usuário arrasta para baixo na notificação, ele se expande para mostrar um resumo de email, como mostrado na captura de tela abaixo:

![Notificação de caixa de entrada de exemplo expandida](local-notifications-images/21-inbox-expanded.png)

Para criar um *caixa de entrada* notificação, você cria uma instância uma `Notification.Builder` do objeto, como antes e adicione um [InboxStyle](https://developer.xamarin.com/api/type/Android.App.Notification+InboxStyle/) o objeto para o `Notification.Builder`. Por exemplo:

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

Para adicionar novas linhas de texto ao corpo da notificação, chame o [Addline](https://developer.xamarin.com/api/member/Android.App.Notification+InboxStyle.AddLine/p/System.String/) método o `InboxStyle` objeto (a altura máxima do *caixa de entrada* notificação é 256 dp). Observe que, ao contrário de *texto grande* estilo, o *caixa de entrada* estilo dá suporte a linhas individuais do texto no corpo da notificação.

Você também pode usar o *caixa de entrada* estilo para qualquer notificação de que precisa para exibir linhas de texto individuais em um formato expandido. Por exemplo, o *caixa de entrada* estilo de notificação pode ser usado para combinar várias notificações pendentes em uma notificação de resumida &ndash; você pode atualizar um único *caixa de entrada* estilo notificação com novo linhas de conteúdo de notificação (consulte [uma notificação de atualização](#updating-a-notification) acima), em vez de gerar um fluxo contínuo de notificações de novo, principalmente semelhantes. Para obter mais informações sobre essa abordagem, consulte [resumir as notificações](http://developer.android.com/design/patterns/notifications.html#summarize_your_notifications).


## <a name="configuring-metadata"></a>Configuração de metadados

`Notification.Builder` inclui métodos que você pode chamar para definir metadados sobre a notificação, como prioridade, visibilidade e categoria. Android usa essas informações &mdash; juntamente com as configurações de preferências do usuário &mdash; para determinar como e quando deseja exibir as notificações.


### <a name="priority-settings"></a>Configurações de prioridade

A configuração de prioridade de uma notificação determina dois resultados quando a notificação é publicada:

-   Onde a notificação é exibida em relação a outras notificações.
    Por exemplo, as notificações de alta prioridade são apresentadas acima notificações de prioridade inferiores na gaveta notificações, independentemente de quando cada notificação foi publicada.

-   Se a notificação é exibida no formato de notificação Heads-up (Android 5.0 e versões posterior). Somente *alta* e *máximo* prioridade notificações são exibidas como notificações Heads-Up.

Xamarin define as enumerações a seguir para configurar a prioridade de notificação:

-   `NotificationPriority.Max` &ndash; Alerta o usuário a uma condição crítica ou urgente (por exemplo, uma chamada de entrada, direções de ativar, desativar ou um alerta de emergência). No Android 5.0 e dispositivos posteriores, as notificações de prioridade máxima são exibidas no formato Heads-Up.

-   `NotificationPriority.High` &ndash; Informa ao usuário de eventos importantes (como emails importantes ou a chegada de mensagens de chat em tempo real). No Android 5.0 e dispositivos posteriores, as notificações de alta prioridade são exibidas em formato de Heads-Up.

-   `NotificationPriority.Default` &ndash; Notifica o usuário das condições que têm um nível de importância médio.

-   `NotificationPriority.Low` &ndash; Para obter informações de não-urgentes que o usuário precisa ser informado (por exemplo, lembretes de atualização de software ou atualizações de rede social).

-   `NotificationPriority.Min` &ndash; Para obter informações que o usuário percebe apenas quando exibir notificações (por exemplo, informações de local ou de clima).

Para definir a prioridade de uma notificação, chame o [SetPriority](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetPriority/) método o `Notification.Builder` objeto, passando o nível de prioridade. Por exemplo:

```csharp
builder.SetPriority (NotificationPriority.High);
```

O exemplo a seguir, a notificação de alta prioridade, "Uma mensagem importante!" aparece na parte superior da gaveta de notificação:

![Notificação de alta prioridade de exemplo](local-notifications-images/22-hi-priority-drawer.png)

Como esta é uma notificação de alta prioridade, ele também será exibido como uma notificação Heads-Up acima da tela de atividade atual do usuário no Android 5.0:

![Notificação de heads-up de exemplo](local-notifications-images/23-heads-up-example.png)

O exemplo a seguir, a notificação de baixa prioridade "Considerado para o dia" é exibida em uma notificação de bateria de prioridade mais alto nível:

![Notificação de baixa prioridade de exemplo](local-notifications-images/24-lo-priority-drawer.png)

Como a notificação de "Pensamento do dia" é uma notificação de baixa prioridade, Android não exibirá-lo no formato Heads-up.


### <a name="visibility-settings"></a>Configurações de visibilidade

Começando com o Android 5.0, o *visibilidade* configuração está disponível para controlar a quantidade de conteúdo notificação aparece no bloqueio seguro.
Xamarin define as enumerações a seguir para definir a visibilidade de notificação:

-   `NotificationVisibility.Public` &ndash; Todo o conteúdo da notificação é exibido no bloqueio seguro.

-   `NotificationVisibility.Private` &ndash; São exibidas somente informações essenciais sobre o bloqueio seguro (como o ícone de notificação e o nome do aplicativo que colocou), mas o restante dos detalhes da notificação estão ocultos. Padrão de todas as notificações para `NotificationVisibility.Private`.

-   `NotificationVisibility.Secret` &ndash; Nada é exibido no bloqueio seguro, nem mesmo o ícone de notificação. O conteúdo de notificação está disponível somente depois que o usuário desbloqueia o dispositivo.

Para definir a visibilidade de uma notificação, chamada de aplicativos a `SetVisibility` método o `Notification.Builder` passagem na configuração de visibilidade do objeto. Por exemplo, essa chamada para `SetVisibility` faz com que a notificação `Private`:

```csharp
builder.SetVisibility (NotificationVisibility.Private);
```

Quando um `Private` notificação for lançada, somente o nome e o ícone do aplicativo é exibido no bloqueio seguro. Em vez da mensagem de notificação, o usuário vê "Desbloquear o dispositivo para ver essa notificação":

![Desbloquear a mensagem de notificação de dispositivo](local-notifications-images/25-lockscreen-private.png)

Neste exemplo, **NotificationsLab** é o nome do aplicativo de origem. Esta versão redigida da notificação aparece somente quando o bloqueio é seguro (ou seja, protegido por senha, padrão ou PIN) &ndash; se o bloqueio não é seguro, todo o conteúdo da notificação está disponível no bloqueio.


### <a name="category-settings"></a>Configurações de categoria

A partir do Android 5.0, categorias predefinidas estão disponíveis para classificação e filtragem de notificações. Xamarin fornece as enumerações a seguir para estas categorias:

-   `Notification.CategoryCall` &ndash; Chamada telefônica.

-   `Notification.CategoryMessage` &ndash; Mensagem de texto de entrada.

-   `Notification.CategoryAlarm` &ndash; Expiração de condição ou timer um alarme.

-   `Notification.CategoryEmail` &ndash; Mensagem de email de entrada.

-   `Notification.CategoryEvent` &ndash; Um evento de calendário.

-   `Notification.CategoryPromo` &ndash; Mensagem promocional ou anúncio.

-   `Notification.CategoryProgress` &ndash; O progresso de uma operação em segundo plano.

-   `Notification.CategorySocial` &ndash; Atualização de rede social.

-   `Notification.CategoryError` &ndash; Falha de um processo de autenticação ou operação do plano de fundo.

-   `Notification.CategoryTransport` &ndash; Atualização de reprodução de mídia.

-   `Notification.CategorySystem` &ndash; Reservado para uso do sistema (status do sistema ou dispositivo).

-   `Notification.CategoryService` &ndash; Indica se um serviço em segundo plano está em execução.

-   `Notification.CategoryRecommendation` &ndash; Uma mensagem de recomendação relacionada ao aplicativo em execução no momento.

-   `Notification.CategoryStatus` &ndash; Informações sobre o dispositivo.

Quando as notificações são classificadas, prioridade da notificação tem precedência sobre a configuração de categoria. Por exemplo, uma notificação de alta prioridade será exibida como Heads-Up mesmo que ele pertence a `Promo` categoria. Para definir a categoria de notificação, você deve chamar o `SetCategory` método o `Notification.Builder` passagem na configuração da categoria do objeto. Por exemplo:

```csharp
builder.SetCategory (Notification.CategoryCall);
```

O *não interfira forma* recurso (novo no Android 5.0) filtra notificações baseadas em categorias. Por exemplo, o *não interfira forma* tela em **configurações** permite que o usuário isentos notificações chamadas e mensagens:

![Não interfira forma switches de tela](local-notifications-images/26-do-not-disturb.png)

Quando o usuário configura *não interfira forma* para bloquear todas as interrupções, exceto as chamadas telefônicas (conforme ilustrado na captura de tela acima), o Android permite que as notificações com uma configuração de categoria de `Notification.CategoryCall` devem ser apresentados enquanto o dispositivo está em *não interfira forma* modo. Observe que `Notification.CategoryAlarm` notificações nunca são bloqueadas no *não interfira forma* modo.


<a name="compatibility" />

## <a name="compatibility"></a>Compatibilidade

Se você estiver criando um aplicativo que também é executado em versões anteriores do Android (mais cedo API nível 4), você usará o [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html) classe `Notification.Builder`. Quando você cria notificações com `NotificationCompat.Builder`, Android garante que o conteúdo de notificação basic é exibido corretamente em dispositivos mais antigos. No entanto, como alguns recursos avançados de notificação não estão disponíveis em versões anteriores do Android, seu código deve manipular explicitamente a problemas de compatibilidade para estilos de notificação expandido, categorias e níveis de visibilidade, conforme explicado a seguir.

Para usar `NotificationCompat.Builder` em seu aplicativo, você deve incluir o [biblioteca de suporte Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) NuGet em seu projeto.

O exemplo de código a seguir ilustra como criar uma notificação basic usando `NotificationCompat.Builder`:

```csharp
// Instantiate the builder and set notification elements:
NotificationCompat.Builder builder = new NotificationCompat.Builder (this)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetSmallIcon (Resource.Drawable.ic_notification);

// Build the notification:
Notification notification = builder.Build();
```

Como mostra este exemplo, chamadas de método para opções de notificação essenciais são idênticas do `Notification.Builder`. No entanto, seu código precisa lidar com problemas de compatibilidade para baixo para notificações mais complexas (descritos na próxima seção).

O [LocalNotifications](https://developer.xamarin.com/samples/monodroid/LocalNotifications) demonstra como usar `NotificationCompat.Builder` para iniciar uma segunda atividade de uma notificação. Esse código de exemplo é explicado no [usando notificações Local no xamarin](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md) passo a passo.


### <a name="notification-styles"></a>Estilos de notificação

Para criar *texto grande*, *imagem*, ou *caixa de entrada* estilo notificações com `NotificationCompat.Builder`, seu aplicativo deve usar as versões de compatibilidade desses estilos. Por exemplo, para usar o *texto grande* de estilo, instanciar `NotificationCompat.BigTextstyle`:

```csharp
NotificationCompat.BigTextStyle textStyle = new NotificationCompat.BigTextStyle();

// Plug this style into the builder:
builder.SetStyle (textStyle);
```

Da mesma forma, seu aplicativo pode usar `NotificationCompat.InboxStyle` e `NotificationCompat.BigPictureStyle` para *caixa de entrada* e *imagem* estilos, respectivamente.


### <a name="notification-priority-and-category"></a>Categoria e prioridade de notificação

`NotificationCompat.Builder` oferece suporte a `SetPriority` método (disponível começando com o Android 4.1). No entanto, o `SetCategory` método é *não* suporte `NotificationCompat.Builder` como categorias fazem parte do novo sistema de metadados de notificação que foi introduzido no Android 5.0.

Para dar suporte a versões mais antigas do Android, onde `SetCategory` é não está disponível, seu código pode verificar o nível de API em tempo de execução para chamar condicionalmente `SetCategory` quando o nível de API é igual ou maior que o Android 5.0 (API nível 21):

```csharp
if ((int) Android.OS.Build.Version.SdkInt >= 21) {
    builder.SetCategory (Notification.CategoryEmail);
}
```

Neste exemplo, o aplicativo **Framework de destino** está definido para o Android 5.0 e o **versão mínima do Android** é definido como **Android 4.1 (API nível 16)**. Porque `SetCategory` está disponível no nível de API 21 e posterior, chamará esse código de exemplo `SetCategory` somente quando está disponível &ndash; não chamará `SetCategory` quando o nível de API é menor que
21.


### <a name="lockscreen-visibility"></a>Visibilidade de bloqueio

Porque o Android não oferecia suporte a notificações de bloqueio antes Android 5.0 (API nível 21), `NotificationCompat.Builder` não oferece suporte a `SetVisibility` método. Conforme explicado acima para `SetCategory`, seu código pode verificar o nível de API em tempo de execução e chamada `SetVisiblity` somente quando está disponível:

```csharp
if ((int) Android.OS.Build.Version.SdkInt >= 21) {
    builder.SetVisibility (Notification.Public);
}
```


## <a name="summary"></a>Resumo

Este artigo explica como criar notificações locais no Android. Ele descrito Anatomia de uma notificação, ele explicou como usar `Notification.Builder` criar notificações, como notificações de estilo de ícone grande, *texto grande*, *imagem* e *caixa de entrada*  formatos, como definir configurações de metadados, como prioridade, visibilidade e categoria de notificação e como iniciar uma atividade de uma notificação. Este artigo também descritos como essas configurações de notificação funcionam com o novo Heads-Up, bloqueio, e *não interfira forma* recursos introduzidos no Android 5.0. Por fim, você aprendeu a usar `NotificationCompat.Builder` para manter a notificação de compatibilidade com versões anteriores do Android.

Para obter diretrizes sobre notificações de criação para o Android, consulte [notificações](http://developer.android.com/preview/notifications.html).


## <a name="related-links"></a>Links relacionados

- [NotificationsLab (exemplo)](https://developer.xamarin.com/samples/monodroid/android5.0/NotificationsLab/)
- [LocalNotifications (exemplo)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Notificações de locais no Android passo a passo](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)
- [Notificações](http://developer.android.com/design/patterns/notifications.html)
- [Notificar o usuário](http://developer.android.com/training/notify-user/index.html)
- [notificação](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
