---
title: Notificações locais
description: Esta seção mostra como implementar notificações locais no xamarin. Android. Ele explica os vários elementos de interface do usuário de uma notificação de Android e discute a API do envolvido com a criação e exibindo uma notificação.
ms.prod: xamarin
ms.assetid: 03E19D14-7C81-4D5C-88FC-C3A3A927DB46
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/16/2018
ms.openlocfilehash: 362041efc5a19dfb70430054f3e4636d4fdfbd7e
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672736"
---
<a name="compatibility"></a>

# <a name="local-notifications"></a>Notificações locais

_Esta seção mostra como implementar notificações locais no xamarin. Android. Ele explica os vários elementos de interface do usuário de uma notificação de Android e discute a API do envolvido com a criação e exibindo uma notificação._

## <a name="local-notifications-overview"></a>Visão geral de notificações locais

O Android fornece duas áreas controladas pelo sistema para exibir ícones de notificação e informações de notificação ao usuário. Quando uma notificação é publicada pela primeira vez, seu ícone é exibido na *área de notificação*, conforme mostrado na seguinte captura de tela:

![Área de notificação de exemplo em um dispositivo](local-notifications-images/01-notification-shade.png)

Para obter detalhes sobre a notificação, o usuário pode abrir a gaveta de notificação (que se expande cada ícone de notificação para revelar o conteúdo da notificação) e executar todas as ações associadas com as notificações. A captura de tela a seguir mostra uma *gaveta de notificação* que corresponde à área de notificação exibida acima:

[![Gaveta de notificação de exemplo exibindo três notificações](local-notifications-images/02-notification-drawer-sml.png)](local-notifications-images/02-notification-drawer.png#lightbox)

Notificações do Android usam dois tipos de layouts:

-   ***Layout de base*** &ndash; um formato de apresentação compacto e fixo.

-   ***Layout expandida*** &ndash; um formato de apresentação que pode ser expandido para um tamanho maior para exibir mais informações.

Cada um desses tipos de layout (e como criá-los) são explicadas nas seções a seguir.

> [!NOTE]
> Este guia enfoca as [NotificationCompat APIs](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.html) da [biblioteca de suporte do Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Essas APIs garantirá máximo com versões anteriores a compatibilidade para o Android 4.0 (API nível 14).


### <a name="base-layout"></a>Layout de base

Todas as notificações do Android são criadas em formato de layout de base, que, no mínimo, inclui os seguintes elementos:

1.  Um *ícone de notificação*, que representa o aplicativo de origem ou o tipo de notificação se o aplicativo dá suporte a diferentes tipos de notificações.

2.  A notificação *título*, ou o nome do remetente se a notificação for uma mensagem pessoal.

3.  A mensagem de notificação.

4.  Um *carimbo de hora*.

Esses elementos são exibidos, conforme ilustrado no diagrama a seguir:

[![Local dos elementos de notificação](local-notifications-images/03-notification-callouts-sml.png)](local-notifications-images/03-notification-callouts.png#lightbox)

Layouts de base são limitados a 64 pixels independentes de densidade (dp) de altura. Por padrão, o Android cria esse estilo de notificação básica.

Opcionalmente, as notificações podem exibir um ícone grande que representa o aplicativo ou uma foto do remetente. Quando um ícone grande é usado em uma notificação no Android 5.0 e versões posteriores, o ícone de notificação pequeno é exibido uma notificação sobre o ícone grande:

![Foto de notificação simples](local-notifications-images/04-simple-notification-photo.png)

Começando com o Android 5.0, as notificações também podem aparecer na tela de bloqueio:

[![Exemplo de notificação de tela de bloqueio](local-notifications-images/05-lockscreen-notification-sml.png)](local-notifications-images/05-lockscreen-notification.png#lightbox)

O usuário pode com um toque duplo na notificação de tela de bloqueio para desbloquear o dispositivo e vá para o aplicativo que originou essa notificação, ou passe o dedo para ignorar a notificação. Os aplicativos podem definir o nível de visibilidade de uma notificação para controlar o que é mostrado na tela de bloqueio e os usuários podem escolher se deseja permitir que conteúdos confidenciais sejam mostrados nas notificações da tela de bloqueio.

Android 5.0 introduziu um formato de apresentação da notificação de alta prioridade chamado *Heads-Up*. Notificações de alerta Deslizar para baixo da parte superior da tela por alguns segundos e, em seguida, retreat volta até a área de notificação:

[![Exemplo de notificação heads-up](local-notifications-images/06-heads-up-notification-sml.png)](local-notifications-images/06-heads-up-notification.png#lightbox)

Notificações de alerta possibilitam que o sistema de interface do usuário para colocar informações importantes na frente do usuário sem interromper o estado da atividade em execução no momento.

Android inclui suporte para metadados de notificação para que as notificações podem ser classificadas e exibidas de forma inteligente. Metadados de notificação também controla como as notificações são apresentadas na tela de bloqueio e no formato Heads-Up. Aplicativos podem definir os seguintes tipos de metadados de notificação:

-   **Prioridade** &ndash; o nível de prioridade que determina como e quando as notificações são apresentadas. Por exemplo, no Android 5.0, notificações de alta prioridade são exibidas como notificações de alerta.

-   **Visibilidade** &ndash; Especifica quanto conteúdo de notificação deve ser exibida quando a notificação será exibida na tela de bloqueio.

-   **Categoria** &ndash; informa o sistema como manipular a notificação em várias circunstâncias, como quando o dispositivo está em *não incomodar* modo.

**Observação:** **Visibilidade** e **categoria** foram introduzidas no Android 5.0 e não estão disponíveis em versões anteriores do Android. Começando com o Android 8.0 [canais de notificação](#notif-chan) são usados para controlar como as notificações são apresentadas ao usuário.


### <a name="expanded-layouts"></a>Layouts expandidos

Começando com o Android 4.1, notificações podem ser configuradas com os estilos de layout expandido que permitem ao usuário expandir a altura da notificação para exibir mais conteúdo. Por exemplo, o exemplo a seguir ilustra uma notificação de layout expandido no modo contratada:

![Notificação contratada](local-notifications-images/07-contracted-notification.png)

Quando essa notificação é expandida, ele revela toda a mensagem:

![Notificação expandida](local-notifications-images/08-expanded-notification.png)

Android dá suporte a três estilos de layout expandido para notificações de evento único:

-   ***Texto grande*** &ndash; no modo contratado, exibe um excerto da primeira linha da mensagem seguida por dois pontos finais. No modo expandido, exibe a mensagem inteira (como visto no exemplo acima).

-   ***Caixa de entrada*** &ndash; no modo contratado, exibe o número de novas mensagens. No modo expandido, exibe a primeira mensagem de email ou uma lista das mensagens na caixa de entrada.

-   ***Imagem*** &ndash; no modo contratado, exibe apenas o texto da mensagem. No modo expandido, exibe o texto e imagem.

[Além de notificação Basic](#beyond-the-basic-notification) (posteriormente neste artigo) explica como criar *texto grande*, *caixa de entrada*, e *imagem* notificações.

<a name="notif-chan"></a>
<a name="notification-channels"></a>
## <a name="notification-channels"></a>Canais de notificação

Começando com o Android 8.0 (Oreo), você pode usar o *canais de notificação* recurso para criar um canal personalizáveis pelo usuário para cada tipo de notificação que você deseja exibir. Canais de notificação tornam possível para você as notificações de grupo para que todas as notificações lançados para um anexo de canal, o mesmo comportamento. Por exemplo, você pode ter um canal de notificação que destina-se para receber notificações que exigem atenção imediata e um canal separado "mais silencioso" que é usado para mensagens informativas.

O **YouTube** aplicativo é instalado com o Android Oreo lista duas categorias de notificação: **Notificações de download** e **notificações gerais**:

[![Telas de notificação para o YouTube no Android Oreo](local-notifications-images/27-youtube-sml.png)](local-notifications-images/27-youtube.png#lightbox)

Cada uma dessas categorias corresponde a um canal de notificação. A YouTube aplicativo implementa um **notificações de Download** canal e uma **notificações gerais** canal. O usuário pode tocar **baixar notificações**, que exibe a tela de configurações para o aplicativo de download do canal de notificações:

[![Baixe a tela de notificações para o aplicativo do YouTube](local-notifications-images/28-yt-download-sml.png)](local-notifications-images/28-yt-download.png#lightbox)

Nessa tela, o usuário pode modificar o comportamento do **baixar** notificações de canal, fazendo o seguinte:

-   Defina a importância nível como **urgente**, **alta**, **médio**, ou **baixa**, que configura o nível de interrupção de som e visual.

-   Ative o ponto de notificação ou desativar.

-   Ative a luz de intermitente ou desativar.

-   Mostrar ou ocultar notificações na tela de bloqueio.

-   Substituir a **não incomodar** configuração.

O **notificações gerais** canal tem configurações semelhantes:

[![Tela de notificações gerais para o aplicativo do YouTube](local-notifications-images/29-yt-general-sml.png)](local-notifications-images/29-yt-general.png#lightbox)

Observe que você não tem absoluto controle sobre como seus canais de notificação interagem com o usuário &ndash; o usuário pode modificar as configurações para qualquer canal de notificação no dispositivo, conforme visto nas capturas de tela acima. No entanto, você pode configurar valores padrão (conforme será descrito abaixo). Como estes exemplos ilustram, o novo recurso de canais de notificação torna possível para fornecer aos usuários controle refinado sobre os diferentes tipos de notificações.


## <a name="notification-creation"></a>Criação de notificação

Para criar uma notificação no Android, você deve usar o [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder) classe a [Xamarin.Android.Support.v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) pacote do NuGet. Essa classe torna possível criar e publicar as notificações em versões mais antigas do Android. Para obter mais informações sobre como usar `NotificationCompat.Builder`, consulte [compatibilidade](#compatibility) mais adiante neste tópico.

`NotificationCompat.Builder` fornece métodos para definir as várias opções em uma notificação, como:

-   O conteúdo, incluindo o título, o texto da mensagem e o ícone de notificação.

-   O estilo de notificação, como *texto grande*, *caixa de entrada*, ou *imagem* estilo.

-   A prioridade da notificação: mínimo, baixo, padrão, de alta ou máximo. No Android 8.0 e posterior, a prioridade é definida por meio de um [ _canal de notificação_](#notification-channels).

-   A visibilidade da notificação na tela de bloqueio: pública, privada ou segredo.

-   Metadados de categoria que ajuda a Android classificar e filtrar a notificação.

-   Uma intenção opcional que indica uma atividade para iniciar quando a notificação for tocada.

-   A ID do canal de notificação que a notificação será publicada no (Android 8.0 e posterior).

Depois de definir essas opções no construtor, você pode gerar um objeto de notificação que contém as configurações. Para publicar a notificação, você passa esse objeto de notificação para o *Gerenciador de notificação*. O Android fornece as [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/) classe, que é responsável por notificações de publicação e exibi-los para o usuário. Uma referência a essa classe pode ser obtida em qualquer contexto, como uma atividade ou um serviço.


### <a name="creating-a-notification-channel"></a>Criação de um canal de notificação

Aplicativos que estão em execução no Android 8.0 devem criar um canal de notificação para suas notificações. Um canal de notificação requer as seguintes três partes de informações:

* Uma cadeia de caracteres de ID exclusivo para o pacote que identificará o canal.
* O nome do canal que será exibido ao usuário.  O nome deve ter entre um e 40 caracteres.
* A importância do canal.

Serão necessário verificar a versão do Android que estão executando aplicativos.
Dispositivos que executam versões anteriores ao Android 8.0 não devem criar um canal de notificação. O método a seguir é um exemplo de como criar um canal de notificação em uma atividade:

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

O canal de notificação deve ser criado a cada vez que a atividade é criada. Para o `CreateNotificationChannel` método, ele deve ser chamado `OnCreate` método de uma atividade.

### <a name="creating-and-publishing-a-notification"></a>Criar e publicar uma notificação

Para gerar uma notificação no Android, siga estas etapas:

1.  Criar uma instância de um `NotificationCompat.Builder` objeto.

2.  Chamar vários métodos no `NotificationCompat.Builder` objeto para definir opções de notificação.

3.  Chamar o [compilar](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.Build/) método o `NotificationCompat.Builder` objeto instanciar um objeto de notificação.

4.  Chame o [notificar](https://developer.xamarin.com/api/member/Android.App.NotificationManager.Notify/(System.Int32%2cAndroid.App.Notification)) método do Gerenciador de notificações para a notificação de publicação.

Você deve fornecer pelo menos as seguintes informações para cada notificação:

-   Um pequeno ícone (24 x 24 dp de tamanho)

-   Um título curto

-   O texto da notificação

O exemplo de código a seguir ilustra como usar `NotificationCompat.Builder` para gerar uma notificação básica. Observe que `NotificationCompat.Builder` dar suporte a métodos [encadeamento de método](https://en.wikipedia.org/wiki/Method_chaining); ou seja, cada método retorna o objeto de construtor para que você pode usar o resultado da última chamada de método para invocar a próxima chamada de método:

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

Neste exemplo, um novo `NotificationCompat.Builder` objeto chamado `builder` é instanciado, junto com a ID do canal de notificação a ser usado. O título e o texto da notificação são definidos, e o ícone de notificação é carregado a partir **Resources/drawable/ic_notification.png**. A chamada para o construtor de notificação `Build` método cria um objeto de notificação com essas configurações. A próxima etapa é chamar o `Notify` método do Gerenciador de notificação. Para localizar o Gerenciador de notificação, você deve chamar `GetSystemService`, conforme mostrado acima.

O `Notify` método aceita dois parâmetros: o identificador de notificação e o objeto de notificação. O identificador de notificação é um inteiro exclusivo que identifica a notificação para seu aplicativo. Neste exemplo, o identificador de notificação é definido como zero (0); No entanto, em um aplicativo de produção, convém fornecer um identificador exclusivo de cada notificação. Reutilizando o valor do identificador anterior em uma chamada para `Notify` faz com que a última notificação a serem substituídos.

Quando esse código é executado em um dispositivo Android 5.0, ele gera uma notificação de que se parece com o exemplo a seguir:

![Resultado de notificação para o código de exemplo](local-notifications-images/09-hello-world.png)

O ícone de notificação é exibido no lado esquerdo da notificação &ndash; essa imagem de um circulada &ldquo;eu&rdquo; tem um canal alfa para que o Android pode desenhar um plano de fundo cinza circular por trás dele. Você também pode fornecer um ícone sem um canal alfa. Para exibir uma imagem fotográfica como um ícone, consulte [formato de ícone grande](#large-icon-format) mais adiante neste tópico.

O carimbo de hora é definido automaticamente, mas você pode substituir essa configuração por meio da chamada a [padrãoQuando](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetWhen/) método do construtor de notificação. Por exemplo, o exemplo de código a seguir define o carimbo de hora para a hora atual:

```csharp
builder.SetWhen (Java.Lang.JavaSystem.CurrentTimeMillis());
```

### <a name="enabling-sound-and-vibration"></a>Habilitando a vibração e som

Se você quiser que a notificação também reproduzir um som, você pode chamar o construtor de notificação [SetDefaults](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetDefaults/) método e passar o `NotificationDefaults.Sound` sinalizador:

```csharp
// Instantiate the notification builder and enable sound:
NotificationCompat.Builder builder = new NotificationCompat.Builder(this, CHANNEL_ID)
    .SetContentTitle ("Sample Notification")
    .SetContentText ("Hello World! This is my first notification!")
    .SetDefaults (NotificationDefaults.Sound)
    .SetSmallIcon (Resource.Drawable.ic_notification);
```

Essa chamada para `SetDefaults` fará com que o dispositivo reproduzir um som quando a notificação é publicada. Se você quiser que o dispositivo Vibrar em vez de reproduzir um som, você pode passar `NotificationDefaults.Vibrate` à `SetDefaults.` se desejar que o dispositivo para reproduzir um som e vibre o dispositivo, você pode passar a ambos os sinalizadores a serem `SetDefaults`:

```csharp
builder.SetDefaults (NotificationDefaults.Sound | NotificationDefaults.Vibrate);
```

Se você habilitar o som sem especificar um som a ser executado, o Android usa o som de notificação do sistema padrão. No entanto, você pode alterar o som que será executado ao chamar o construtor de notificação [SetSound](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetSound/p/Android.Net.Uri/) método. Por exemplo, para reproduzir o alarme som com sua notificação (em vez do som de notificação padrão), você pode obter o URI para o alarme de som da [RingtoneManager](https://developer.xamarin.com/api/type/Android.Media.RingtoneManager/) e passá-lo para `SetSound`:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Alarm));
```

Como alternativa, você pode usar o toque de padrão de sistema som para a notificação:

```csharp
builder.SetSound (RingtoneManager.GetDefaultUri(RingtoneType.Ringtone));
```

Depois de criar um objeto de notificação, é possível definir propriedades de notificação no objeto de notificação (em vez de configurá-los por meio de antemão `NotificationCompat.Builder` métodos). Por exemplo, em vez de chamar o `SetDefaults` método para ativar a vibração em uma notificação, você pode modificar diretamente o sinalizador de bit da notificação [padrões](https://developer.xamarin.com/api/property/Android.App.Notification.Defaults/) propriedade:

```csharp
// Build the notification:
Notification notification = builder.Build();

// Turn on vibrate:
notification.Defaults |= NotificationDefaults.Vibrate;
```

Este exemplo faz com que o dispositivo vibre quando a notificação é publicada.

<a name="updating-a-notification" />

### <a name="updating-a-notification"></a>Atualizando uma notificação

Se você quiser atualizar o conteúdo de uma notificação após ele ter sido publicado, você pode reutilizar existente `NotificationCompat.Builder` objeto para criar um novo objeto de notificação e esta notificação com o identificador da última notificação de publicação. Por exemplo:

```csharp
// Update the existing notification builder content:
builder.SetContentTitle ("Updated Notification");
builder.SetContentText ("Changed to this message.");

// Build a notification object with updated content:
notification = builder.Build();

// Publish the new notification with the existing ID:
notificationManager.Notify (notificationId, notification);
```

Neste exemplo, existente `NotificationCompat.Builder` objeto é usado para criar um novo objeto de notificação com um título diferente e uma mensagem.
O novo objeto de notificação é publicado usando o identificador da notificação anterior, e isso atualizará o conteúdo da notificação publicada anteriormente:

![Notificação atualizada](local-notifications-images/12-updated-notification.png)

O corpo da notificação anterior é reutilizado &ndash; somente o título e o texto da notificação muda enquanto a notificação é exibida na gaveta de notificação. O texto do título muda de "Notificação de exemplo" a "Notificação de atualização" e o texto da mensagem muda de "Hello World! Esta é minha primeira notificação!" para "Alterado para esta mensagem."

Uma notificação permanece visível até que ocorra um destes três coisas:

-   O usuário a ignore a notificação (ou toca *Limpar tudo*).

-   O aplicativo faz uma chamada para `NotificationManager.Cancel`, passando a ID exclusiva de notificação que foi atribuída quando a notificação foi publicada.

-   O aplicativo chama `NotificationManager.CancelAll`.

Para obter mais informações sobre a atualização de notificações do Android, consulte [modificar uma notificação](https://developer.android.com/training/notify-user/managing.html#Updating).


### <a name="starting-an-activity-from-a-notification"></a>Iniciando uma atividade de uma notificação

No Android, é comum que uma notificação a ser associada com um *ação* &ndash; uma atividade que é iniciada quando o usuário toca na notificação. Essa atividade pode residir em outro aplicativo ou até mesmo em outra tarefa. Para adicionar uma ação em uma notificação, você cria um [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/) do objeto e associe o `PendingIntent` com a notificação. Um `PendingIntent` é um tipo especial de intenção que permite que o aplicativo de destino executar uma parte predefinida do código com as permissões do aplicativo de envio. Quando o usuário toca na notificação, o Android começa a atividade especificada pelo `PendingIntent`.

O trecho de código a seguir ilustra como criar uma notificação com um `PendingIntent` que iniciará a atividade do aplicativo de origem, `MainActivity`:

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

Esse código é muito semelhante ao código de notificação na seção anterior, exceto que um `PendingIntent` é adicionado ao objeto de notificação. Neste exemplo, o `PendingIntent` está associado com a atividade do aplicativo de origem antes de ser passado para o construtor de notificação [SetContentIntent](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetContentIntent/) método. O `PendingIntentFlags.OneShot` sinalizador é passado para o `PendingIntent.GetActivity` método para que o `PendingIntent` é usado apenas uma vez. Quando esse código é executado, a seguinte notificação será exibida:

![Primeira notificação de ação](local-notifications-images/10-first-action-notification.png)

Tocar essa notificação leva o usuário de volta para a atividade de origem.

Em um aplicativo de produção, seu aplicativo deve lidar com o *pilha voltar* quando o usuário pressiona o **volta** botão dentro da atividade de notificação (se você não estiver familiarizado com a pilha voltar e tarefas Android, consulte [ Tarefas e a pilha voltar](https://developer.android.com/guide/components/tasks-and-back-stack.html)).
Na maioria dos casos, navegar para trás fora da atividade de notificação deve retornar o usuário para fora do aplicativo e de volta para a tela inicial. Para gerenciar a pilha voltar, seu aplicativo usa o [TaskStackBuilder](https://developer.xamarin.com/api/type/Android.App.TaskStackBuilder/) classe para criar um `PendingIntent` com uma pilha voltar.

Outra consideração do mundo real é que a atividade de origem precisa enviar dados para a atividade de notificação. Por exemplo, a notificação pode indicar que chegou uma mensagem de texto, e a atividade de notificação (uma mensagem exibindo tela), requer a ID da mensagem para exibir a mensagem para o usuário. A atividade que cria o `PendingIntent` pode usar o [Intent.PutExtra](https://developer.xamarin.com/api/member/Android.Content.Intent.PutExtra/p/System.String/System.String/) método para adicionar dados (por exemplo, uma cadeia de caracteres) à intenção para que esses dados são passados para a atividade de notificação.

O exemplo de código a seguir ilustra como usar `TaskStackBuilder` gerenciar a pilha voltar e ele inclui um exemplo de como enviar uma cadeia de caracteres de mensagem único para uma atividade de notificação chamada `SecondActivity`:

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

Neste exemplo de código, o aplicativo consiste em duas atividades: `MainActivity` (que contém o código de notificação acima), e `SecondActivity`, a tela que o usuário vê depois de tocar a notificação. Quando esse código é executado, é apresentada uma simple notificação (semelhante ao exemplo anterior). Tocar na notificação leva o usuário para o `SecondActivity` tela:

![Segunda captura de tela de atividade](local-notifications-images/11-second-activity.png)

A mensagem de cadeia de caracteres (passado para a intenção `PutExtra` método) é recuperado no `SecondActivity` via esta linha de código:

```csharp
// Get the message from the intent:
string message = Intent.Extras.GetString ("message", "");
```

Essa mensagem recuperada, "Saudações do MainActivity!," é exibida no `SecondActivity` de tela, conforme mostrado na captura de tela acima. Quando o usuário pressiona o **volta** botão enquanto no `SecondActivity`, navegação leva para fora do aplicativo e de volta para a tela anterior a inicialização do aplicativo.

Para obter mais informações sobre como criar pendentes intenções, consulte [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/).


<a name="beyond-the-basic-notification" />

## <a name="beyond-the-basic-notification"></a>Além de notificação basic

Notificações padrão para um formato de layout de base simples no Android, mas você pode aprimorar esse formato básico, tornando adicionais `NotificationCompat.Builder` chamadas de método. Nesta seção, você aprenderá a adicionar um ícone de foto grande para a notificação e você verá exemplos de como criar notificações de layout expandido.

<a name="large-icon-format" />

### <a name="large-icon-format"></a>Formato de ícone grande

Notificações do Android normalmente exibem o ícone do aplicativo de origem (no lado esquerdo da notificação). No entanto, as notificações podem exibir uma imagem ou uma foto (um *ícone grande*) em vez do ícone pequeno do padrão. Por exemplo, um aplicativo de mensagens poderia exibir uma foto do remetente em vez de no ícone do aplicativo.

Aqui está um exemplo de uma notificação no Android 5.0 básico &ndash; ele exibe apenas o ícone pequeno do aplicativo:

![Exemplo de notificação normal](local-notifications-images/13-sample-notification.png)

E aqui está uma captura de tela da notificação depois de modificá-lo para exibir um ícone grande &ndash; ele usa um ícone criado de uma imagem de um monkey de código do Xamarin:

![Exemplo de notificação de ícone grande](local-notifications-images/14-large-icon-sample.png)

Observe que, quando uma notificação é apresentada no formato de ícone grande, o ícone pequeno do aplicativo é exibido como uma notificação no canto inferior direito do ícone grande.

Para usar uma imagem como um ícone grande em uma notificação, você chama o construtor de notificação [SetLargeIcon](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetLargeIcon/) método e passar em um bitmap da imagem. Diferentemente `SetSmallIcon`, `SetLargeIcon` aceita apenas um bitmap. Para converter um arquivo de imagem em um bitmap, você deve usar o [BitmapFactory](https://developer.xamarin.com/api/type/Android.Graphics.BitmapFactory/) classe. Por exemplo:

```csharp
builder.SetLargeIcon (BitmapFactory.DecodeResource (Resources, Resource.Drawable.monkey_icon));
```

Esse código de exemplo abre o arquivo de imagem na **Resources/drawable/monkey_icon.png**, converte-o em um bitmap e passa o bitmap resultante para `NotificationCompat.Builder`. Normalmente, a resolução de imagem de origem é maior do que o ícone pequeno &ndash; , mas não muito maior. Uma imagem que é muito grande pode fazer com que as operações de redimensionamento desnecessárias que poderiam atrasar o lançamento da notificação.


### <a name="big-text-style"></a>Estilo de texto grande

O *texto grande* estilo é um modelo de layout expandido que você pode usar para exibir mensagens longas em notificações. Como todas as notificações de layout expandido, a notificação de texto grande é exibida inicialmente em um formato compacto de apresentação:

![Exemplo de notificação de texto grande](local-notifications-images/15-big-text-notification.png)

Nesse formato, somente um trecho da mensagem é mostrado, terminada por dois pontos finais. Quando o usuário arrasta para baixo na notificação, ele é expandido para revelar a mensagem de notificação toda:

![Notificação de texto grande expandida](local-notifications-images/16-big-text-expanded.png)

Esse formato expandido de layout também inclui o texto de resumo na parte inferior da notificação. A altura máxima dos *texto grande* notificação é 256 dp.

Para criar uma *texto grande* notificação, você cria uma instância uma `NotificationCompat.Builder` do objeto, como antes e, em seguida, crie uma instância e adicionar um [BigTextStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigTextStyle/) do objeto para o `NotificationCompat.Builder` objeto. Veja um exemplo:

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

Neste exemplo, o texto da mensagem e o texto de resumo são armazenadas do `BigTextStyle` objeto (`textStyle`) antes de ser passado para `NotificationCompat.Builder.`


### <a name="image-style"></a>Estilo de imagem

O *imagem* estilo (também chamada a *panorama* estilo) é um formato de notificação expandido que você pode usar para exibir uma imagem no corpo de uma notificação. Por exemplo, um aplicativo de captura de tela ou um aplicativo de fotos pode usar o *imagem* notificação estilo para fornecer ao usuário uma notificação sobre a última imagem que foi capturada. Observe que a altura máxima dos *imagem* notificação é 256 dp &ndash; Android redimensionará a imagem para caber nessa restrição de altura máxima, dentro dos limites de memória disponível.

Como todas as notificações de layout, de expandido *imagem* pela primeira vez, as notificações são exibidas em um formato compacto que exibe um excerto do texto da mensagem que acompanha este artigo:

![Notificação de imagem Compact não mostra nenhuma imagem](local-notifications-images/17-image-compact.png)

Quando o usuário arrasta para baixo a *imagem* notificação, ele é expandido para revelar uma imagem. Por exemplo, aqui está a versão expandida da notificação anterior:

![Imagem expandida notificação revela imagem](local-notifications-images/18-image-expanded.png)

Observe que, quando a notificação é exibida em um formato compacto, ele exibe o texto de notificação (o texto que é passado para o construtor de notificação `SetContentText` método, conforme mostrado anteriormente). No entanto, quando a notificação é expandida para revelar a imagem, ele exibe o texto de resumo acima da imagem.

Para criar uma *imagem* notificação, você instancia um `NotificationCompat.Builder` como antes, do objeto e, em seguida, criar e inserir um [BigPictureStyle](https://developer.xamarin.com/api/type/Android.App.Notification+BigPictureStyle/) do objeto no `NotificationCompat.Builder` objeto. Por exemplo:

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

Como o `SetLargeIcon` método de `NotificationCompat.Builder`, o [BigPicture](https://developer.xamarin.com/api/member/Android.App.Notification+BigPictureStyle.BigPicture/) método de `BigPictureStyle` requer um bitmap da imagem que você deseja exibir no corpo da notificação. Neste exemplo, o [DecodeResource](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeResource/(Android.Content.Res.Resources%2cSystem.Int32)) método `BitmapFactory` lê o arquivo de imagem localizado em **Resources/drawable/x_bldg.png** e o converte em um bitmap.

Você também pode exibir imagens que não são empacotadas como um recurso. Por exemplo, o código de exemplo a seguir carrega uma imagem do cartão SD local e exibe-o em um *imagem* notificação:

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

Neste exemplo, o arquivo de imagem localizado em **/sdcard/Pictures/my-tshirt.jpg** é carregado, redimensionado a metade do tamanho original e, em seguida, convertido em um bitmap para uso na notificação:

![Imagem de camiseta de exemplo na notificação](local-notifications-images/19-tshirt-notification.png)

Se você não souber o tamanho do arquivo de imagem com antecedência, ele é uma boa ideia para encapsular a chamada para [BitmapFactory.DecodeFile](https://developer.xamarin.com/api/member/Android.Graphics.BitmapFactory.DecodeFile/p/System.String/Android.Graphics.BitmapFactory+Options/) em um manipulador de exceção &ndash; um `OutOfMemoryError` exceção seria lançada se a imagem for muito grande para Android para redimensionar.

Para obter mais informações sobre como carregar e decodificação de imagens de bitmap grande, consulte [carga grande Bitmaps com eficiência](https://github.com/xamarin/recipes/tree/master/Recipes/android/resources/general/load_large_bitmaps_efficiently).


### <a name="inbox-style"></a>Estilo de caixa de entrada

O *caixa de entrada* formato é um modelo de layout expandido se destina para exibir linhas separadas de texto (por exemplo, um email na caixa de entrada resumo) no corpo da notificação. O *caixa de entrada* formato de notificação é exibida pela primeira vez em um formato compacto:

![Exemplo de notificação de caixa de entrada compact](local-notifications-images/20-inbox-compact.png)

Quando o usuário arrasta para baixo na notificação, ele é expandido para revelar um resumo de email, como visto na captura de tela abaixo:

![Notificação de caixa de entrada de exemplo expandida](local-notifications-images/21-inbox-expanded.png)

Para criar uma *caixa de entrada* notificação, você cria uma instância uma `NotificationCompat.Builder` do objeto, como antes e adicione um [InboxStyle](https://developer.xamarin.com/api/type/Android.App.Notification+InboxStyle/) do objeto para o `NotificationCompat.Builder`. Veja um exemplo:

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

Para adicionar novas linhas de texto ao corpo da notificação, chame o [Addline](https://developer.xamarin.com/api/member/Android.App.Notification+InboxStyle.AddLine/p/System.String/) método da `InboxStyle` objeto (a altura máxima dos *caixa de entrada* notificação é 256 dp). Observe que, diferentemente *texto grande* estilo, a *caixa de entrada* estilo dá suporte a linhas individuais de texto no corpo da notificação.

Você também pode usar o *caixa de entrada* estilo para qualquer notificação de que precisa para exibir linhas de texto individuais em um formato expandido. Por exemplo, o *caixa de entrada* estilo de notificação pode ser usado para combinar várias notificações pendentes em uma notificação de resumida &ndash; você pode atualizar um único *caixa de entrada* notificação com novos de estilo linhas de conteúdo de notificação (consulte [atualizando uma notificação](#updating-a-notification) acima), em vez de gerar um fluxo contínuo de notificações de novo, principalmente semelhantes.


## <a name="configuring-metadata"></a>Configuração de metadados

`NotificationCompat.Builder` inclui métodos que você pode chamar para definir metadados sobre a notificação, como prioridade, visibilidade e categoria. Android usa essas informações &mdash; junto com as configurações de preferência do usuário &mdash; para determinar como e quando deseja exibir as notificações.


### <a name="priority-settings"></a>Configurações de prioridade

Aplicativos em execução no Android 7.1 e mais baixo é necessário definir a prioridade diretamente na própria notificação. A configuração de prioridade de uma notificação determina os dois resultados quando a notificação é publicada:

-   Em que a notificação será exibida em relação a outras notificações.
    Por exemplo, notificações de alta prioridade são apresentadas acima notificações de prioridade mais baixos na gaveta de notificações, independentemente de quando cada notificação foi publicada.

-   Se a notificação é exibida no formato Heads-up notificação (Android 5.0 e posterior). Somente *alta* e *máximo* notificações de prioridade são exibidas como notificações de alerta.

Xamarin. Android define as enumerações a seguir para configurar a prioridade de notificação:

-   `NotificationPriority.Max` &ndash; Alerta o usuário a uma condição urgente ou essencial (por exemplo, uma chamada de entrada, instruções de folheio de curva ou um alerta de emergência). No Android 5.0 e dispositivos posteriores, as notificações de prioridade máxima são exibidas no formato Heads-Up.

-   `NotificationPriority.High` &ndash; Informa o usuário de eventos importantes (como emails importantes ou a chegada de mensagens de bate-papo em tempo real). No Android 5.0 e dispositivos posteriores, as notificações de prioridade alta são exibidas em formato de Heads-Up.

-   `NotificationPriority.Default` &ndash; Notifica o usuário das condições que têm um nível de importância médio.

-   `NotificationPriority.Low` &ndash; Para obter informações não-urgentes que o usuário precisa ser informado de (por exemplo, lembretes de atualização de software ou atualizações de rede social).

-   `NotificationPriority.Min` &ndash; Para obter informações que o usuário percebe apenas quando exibir notificações (por exemplo, informações de local ou o clima).

Para definir a prioridade de uma notificação, chame o [SetPriority](https://developer.xamarin.com/api/member/Android.App.Notification+Builder.SetPriority/) método o `NotificationCompat.Builder` objeto, passando o nível de prioridade. Por exemplo:

```csharp
builder.SetPriority (NotificationPriority.High);
```

No exemplo a seguir, a notificação de alta prioridade, "Uma mensagem importante!" será exibida na parte superior da gaveta de notificação:

![Exemplo de notificação de alta prioridade](local-notifications-images/22-hi-priority-drawer.png)

Como esta é uma notificação de alta prioridade, ele também será exibido como uma notificação de alerta acima da tela de atividade atual do usuário no Android 5.0:

![Exemplo de notificação de alerta](local-notifications-images/23-heads-up-example.png)

No exemplo a seguir, a notificação de "Considerada para o dia" de baixa prioridade é exibida em uma notificação de nível de bateria de prioridade mais alta:

![Exemplo de notificação de baixa prioridade](local-notifications-images/24-lo-priority-drawer.png)

Como a notificação de "Para o dia de pensamento" é uma notificação de baixa prioridade, Android não exibirá-lo no formato Heads-up.

> [!NOTE]
> No Android 8.0 e posterior, a prioridade das configurações de usuário e o canal de notificação determina a prioridade da notificação.

### <a name="visibility-settings"></a>Configurações de visibilidade

Começando com o Android 5.0, o *visibilidade* configuração está disponível para controlar quanto conteúdo de notificação é exibida na tela de bloqueio seguro.
Xamarin. Android define as enumerações a seguir para definir a visibilidade de notificação:

-   `NotificationVisibility.Public` &ndash; Todo o conteúdo da notificação é exibido na tela de bloqueio seguro.

-   `NotificationVisibility.Private` &ndash; Somente informações essenciais são exibidas na tela de bloqueio de seguro (como o ícone de notificação e o nome do aplicativo que postou), mas o restante dos detalhes da notificação estão ocultos. Todas as notificações padrão `NotificationVisibility.Private`.

-   `NotificationVisibility.Secret` &ndash; Nada é exibido na tela de bloqueio seguro, nem mesmo o ícone de notificação. O conteúdo da notificação está disponível somente depois que o usuário desbloqueie o dispositivo.

Para definir a visibilidade de uma notificação, aplicativos de chamada a `SetVisibility` método da `NotificationCompat.Builder` objeto, passando a configuração de visibilidade. Por exemplo, essa chamada para `SetVisibility` faz com que a notificação `Private`:

```csharp
builder.SetVisibility (NotificationVisibility.Private);
```

Quando um `Private` notificação for lançada, somente o nome e o ícone do aplicativo é exibido na tela de bloqueio seguro. Em vez da mensagem de notificação, o usuário vê "Desbloquear seu dispositivo para ver essa notificação":

![Desbloqueie a mensagem de notificação do dispositivo](local-notifications-images/25-lockscreen-private.png)

Neste exemplo, **NotificationsLab** é o nome do aplicativo de origem. Esta versão redigida da notificação é exibida somente quando a tela de bloqueio é segura (ou seja, protegido por senha, padrão ou PIN) &ndash; se a tela de bloqueio não for segura, todo o conteúdo da notificação está disponível na tela de bloqueio.


### <a name="category-settings"></a>Configurações de categoria

Começando com o Android 5.0, categorias predefinidas estão disponíveis para classificação e filtragem de notificações. Xamarin. Android fornece as seguintes enumerações para estas categorias:

-   `Notification.CategoryCall` &ndash; Chamada telefônica.

-   `Notification.CategoryMessage` &ndash; Mensagem de texto de entrada.

-   `Notification.CategoryAlarm` &ndash; Uma expiração de timer ou condição de alarme.

-   `Notification.CategoryEmail` &ndash; Mensagem de email de entrada.

-   `Notification.CategoryEvent` &ndash; Um evento de calendário.

-   `Notification.CategoryPromo` &ndash; Mensagem promocional ou anúncio.

-   `Notification.CategoryProgress` &ndash; O progresso de uma operação em segundo plano.

-   `Notification.CategorySocial` &ndash; Atualização do sistema de rede social.

-   `Notification.CategoryError` &ndash; Falha de um processo de autenticação ou operação em segundo plano.

-   `Notification.CategoryTransport` &ndash; Atualização de reprodução de mídia.

-   `Notification.CategorySystem` &ndash; Reservado para uso do sistema (status do sistema ou dispositivo).

-   `Notification.CategoryService` &ndash; Indica que um serviço em segundo plano está em execução.

-   `Notification.CategoryRecommendation` &ndash; Uma mensagem de recomendação relacionada ao aplicativo em execução no momento.

-   `Notification.CategoryStatus` &ndash; Informações sobre o dispositivo.

Quando as notificações são classificadas, a prioridade da notificação tem precedência sobre sua configuração de categoria. Por exemplo, uma notificação de alta prioridade será exibida como Heads-Up, mesmo que ele pertence a `Promo` categoria. Para definir a categoria de uma notificação, você chama o `SetCategory` método da `NotificationCompat.Builder` objeto, passando a configuração de categoria. Por exemplo:

```csharp
builder.SetCategory (Notification.CategoryCall);
```

O *não incomodar* notificações com base nas categorias de filtros de recurso (novo no Android 5.0). Por exemplo, o *não incomodar* tela no **configurações** permite que o usuário isentos notificações para chamadas telefônicas e mensagens:

![Não incomodar switches de tela](local-notifications-images/26-do-not-disturb.png)

Quando o usuário configura *não incomodar* para bloquear todas as interrupções, exceto para chamadas telefônicas (conforme ilustrado na captura de tela acima), o Android permite que as notificações com uma configuração de categoria de `Notification.CategoryCall` sejam apresentados enquanto o dispositivo está na *não incomodar* modo. Observe que `Notification.CategoryAlarm` notificações nunca são bloqueadas no *não incomodar* modo.

O [LocalNotifications](https://developer.xamarin.com/samples/monodroid/LocalNotifications) exemplo demonstra como usar `NotificationCompat.Builder` para iniciar uma segunda atividade de uma notificação. Esse código de exemplo é explicado com o [usando notificações locais no xamarin. Android](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md) passo a passo.


### <a name="notification-styles"></a>Estilos de notificação

Para criar *texto grande*, *imagem*, ou *caixa de entrada* estilo notificações com `NotificationCompat.Builder`, seu aplicativo deve usar as versões de compatibilidade desses estilos. Por exemplo, para usar o *texto grande* de estilo, instancie `NotificationCompat.BigTextstyle`:

```csharp
NotificationCompat.BigTextStyle textStyle = new NotificationCompat.BigTextStyle();

// Plug this style into the builder:
builder.SetStyle (textStyle);
```

Da mesma forma, seu aplicativo pode usar `NotificationCompat.InboxStyle` e `NotificationCompat.BigPictureStyle` para *caixa de entrada* e *imagem* estilos, respectivamente.


### <a name="notification-priority-and-category"></a>Categoria e prioridade de notificação

`NotificationCompat.Builder` oferece suporte a `SetPriority` método (disponível iniciando com o Android 4.1). No entanto, o `SetCategory` método for *não* com suporte pelo `NotificationCompat.Builder` como categorias fazem parte do novo sistema de metadados de notificação que foi introduzido no Android 5.0.

Para dar suporte a versões mais antigas do Android, onde `SetCategory` é não está disponível, seu código pode verificar o nível de API em tempo de execução para chamar condicionalmente `SetCategory` quando o nível de API é igual ou maior que o Android 5.0 (API nível 21):

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetCategory (Notification.CategoryEmail);
}
```

Neste exemplo, o aplicativo **estrutura de destino** é definido como Android 5.0 e o **versão mínima do Android** está definido como **Android 4.1 (API nível 16)**. Porque `SetCategory` está disponível no nível da API 21 e versões posterior, esse código de exemplo chamará `SetCategory` apenas quando ele está disponível &ndash; chamará não `SetCategory` quando o nível de API é menor que 21.


### <a name="lock-screen-visibility"></a>Visibilidade da tela de bloqueio

Porque o Android não dava suporte a notificações de tela de bloqueio antes de Android 5.0 (API nível 21), `NotificationCompat.Builder` não oferece suporte a `SetVisibility` método. Conforme explicado acima para `SetCategory`, seu código pode verificar o nível de API em tempo de execução e chame `SetVisiblity` apenas quando ele está disponível:

```csharp
if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.Lollipop) {
    builder.SetVisibility (Notification.Public);
}
```


## <a name="summary"></a>Resumo

Este artigo explicou como criar notificações locais no Android. Ele descreveu a anatomia de uma notificação, ele explicou como usar `NotificationCompat.Builder` para criar notificações, como notificações de estilo de ícone grande, *texto grande*, *imagem* e *caixa de entrada*  formatos, como definir configurações de metadados, como prioridade, a visibilidade e a categoria de notificação e como inicializar uma atividade de uma notificação. Este artigo também descreveu como essas configurações de notificação funcionam com a novo alerta, tela de bloqueio, e *não incomodar* recursos introduzidos no Android 5.0. Por fim, você aprendeu a usar `NotificationCompat.Builder` para manter a notificação de compatibilidade com versões anteriores do Android.

Para obter diretrizes sobre as notificações de criação para o Android, consulte [notificações](https://developer.android.com/guide/topics/ui/notifiers/notifications.html).


## <a name="related-links"></a>Links relacionados

- [NotificationsLab (amostra)](https://developer.xamarin.com/samples/monodroid/android5.0/NotificationsLab/)
- [LocalNotifications (amostra)](https://developer.xamarin.com/samples/monodroid/LocalNotifications/)
- [Notificações locais Android passo a passo](~/android/app-fundamentals/notifications/local-notifications-walkthrough.md)
- [Notificar o usuário](https://developer.android.com/training/notify-user/index.html)
- [Notificação](https://developer.xamarin.com/api/type/Android.App.Notification/)
- [NotificationManager](https://developer.xamarin.com/api/type/Android.App.NotificationManager/)
- [NotificationCompat.Builder](https://developer.android.com/reference/android/support/v4/app/NotificationCompat.Builder.html)
- [PendingIntent](https://developer.xamarin.com/api/type/Android.App.PendingIntent/)
