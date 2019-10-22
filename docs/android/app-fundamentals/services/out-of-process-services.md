---
title: Executando serviços Android em processos remotos
description: Em geral, todos os componentes em um aplicativo Android serão executados no mesmo processo. Os serviços Android são uma exceção notável para isso, pois podem ser configurados para serem executados em seus próprios processos e compartilhados com outros aplicativos, incluindo aqueles de outros desenvolvedores Android. Este guia explicará como criar e usar um serviço remoto Android usando o Xamarin.
ms.prod: xamarin
ms.assetid: 27A2E972-A690-480B-B31D-5EF1F74F673C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 5429f260399602b7ef15e8263bc74cb8ae940f4f
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70754884"
---
# <a name="running-android-services-in-remote-processes"></a>Executando serviços Android em processos remotos

_Em geral, todos os componentes em um aplicativo Android serão executados no mesmo processo. Os serviços Android são uma exceção notável para isso, pois podem ser configurados para serem executados em seus próprios processos e compartilhados com outros aplicativos, incluindo aqueles de outros desenvolvedores Android. Este guia explicará como criar e usar um serviço remoto Android usando o Xamarin._

## <a name="out-of-process-services-overview"></a>Visão geral dos serviços fora do processo

Quando um aplicativo é iniciado, o Android cria um processo no qual executar o aplicativo. Normalmente, todos os componentes que o aplicativo executará em um único processo. Os serviços Android são uma exceção notável para isso, pois podem ser configurados para serem executados em seus próprios processos e compartilhados com outros aplicativos, incluindo aqueles de outros desenvolvedores Android. Esses tipos de serviços são chamados de _serviços remotos_ ou _serviços fora de processo_. O código para esses serviços estará contido no mesmo APK que o aplicativo principal; no entanto, quando o serviço for iniciado, o Android criará um novo processo apenas para esse serviço. Por outro lado, um serviço executado no mesmo processo que o restante do aplicativo é, às vezes, chamado de _serviço local_.

Em geral, não é necessário que um aplicativo implemente um serviço remoto. Um serviço local é suficiente (e desejável) para os requisitos de um aplicativo em muitos casos. Um fora do processo tem seu próprio espaço de memória que deve ser gerenciado pelo Android. Embora isso apresente mais sobrecarga ao aplicativo geral, há alguns cenários em que pode ser vantajoso executar um serviço em seu próprio processo:

1. A **funcionalidade de compartilhamento** &ndash; alguns desenvolvedores de aplicativos podem ter vários aplicativos e funcionalidades compartilhados entre todos os aplicativos. O empacotamento dessa funcionalidade em um serviço Android que é executado em seu próprio processo pode simplificar a manutenção do aplicativo. Também é possível empacotar o serviço em seu próprio APK autônomo e implantá-lo separadamente do restante do aplicativo.
2. **Melhorando a experiência do usuário** &ndash; há duas maneiras possíveis de um serviço fora do processo poder melhorar a experiência do usuário do aplicativo. A primeira maneira de lidar com o gerenciamento de memória. Quando ocorre um ciclo de coleta de lixo (GC), o Android pausará todas as atividades no processo até que o GC seja concluído. O usuário pode perceber essa pausa como "stutter" ou "Jank". Quando um serviço é executado no próprio processo, é o processo de serviço em pausa, não o processo do aplicativo. Essa pausa será muito menos perceptível para o usuário, pois o processo do aplicativo (e, portanto, a interface do usuário) não está em pausa.

    Em segundo lugar, se os requisitos de memória de um processo ficarem muito grandes, o Android poderá interromper esse processo para liberar recursos para o dispositivo. Se um serviço tiver uma grande quantidade de memória e estiver em execução no mesmo processo que a interface do usuário, quando o Android recuperar forçosamente os recursos que a interface do usuário será desligada, forçando o usuário a iniciar o aplicativo. No entanto, se um serviço executado em seu próprio processo for desligado pelo Android, o processo de interface do usuário permanecerá inalterado. A interface do usuário pode ligar (e reiniciar) o serviço, transparente para o usuário e retomar o funcionamento normal.

3. **Melhorando o desempenho do aplicativo** &ndash; o processo da interface do usuário pode ser encerrado ou desligado independentemente do processo de serviço. Ao mover tarefas de inicialização demoradas para um serviço fora do processo, é possível que o tempo de inicialização da interface do usuário seja melhorado (supondo que o processo do serviço seja mantido ativo entre os horários em que a interface do usuário é iniciada).

De muitas maneiras, a associação a um serviço em execução em outro processo é a mesma que a [associação a um serviço local](~/android/app-fundamentals/services/creating-a-service/bound-services.md). O cliente invocará `BindService` para associar (e iniciar, se necessário) o serviço. Um objeto `Android.OS.IServiceConnection` será criado para gerenciar a conexão entre o cliente e o serviço. Se o cliente for associado com êxito ao serviço, o Android retornará um objeto por meio do `IServiceConnection` que pode ser usado para invocar métodos no serviço. O cliente interage com o serviço usando esse objeto. Para revisar, aqui estão as etapas para associar a um serviço:

- **Crie uma intenção** &ndash; uma intenção explícita deve ser usada para associação ao serviço.
- **Implemente e crie uma instância de um objeto de `IServiceConnection`** &ndash; o objeto `IServiceConnection` atua como um intermediário entre o cliente e o serviço.  Ele é responsável por monitorar a conexão entre o cliente e o servidor.
- **Invocar o método `BindService`** &ndash; chamar `BindService` enviará a intenção e a conexão de serviço criada nas etapas anteriores para o Android, o que cuidará do início do serviço e do estabelecimento da comunicação entre o cliente e o serviço.

A necessidade de cruzar os limites do processo introduz uma complexidade extra: a comunicação é unidirecional (cliente para servidor) e o cliente não pode invocar diretamente os métodos na classe de serviço. Lembre-se de que quando um serviço está executando o mesmo processo que o cliente, o Android fornece um objeto `IBinder` que pode permitir a comunicação bidirecional. Esse não é o caso com o serviço em execução em seu próprio processo. Um cliente se comunica com um serviço remoto com a ajuda da classe `Android.OS.Messenger`.

Quando um cliente solicita a ligação com o serviço remoto, o Android invocará o método de ciclo de vida `Service.OnBind`, que retornará o objeto de `IBinder` interno que é encapsulado pelo `Messenger`. O `Messenger` é um wrapper fino em uma implementação especial de `IBinder` que é fornecida pelo SDK do Android. O `Messenger` cuida da comunicação entre os dois processos diferentes. O desenvolvedor não se preocupa com os detalhes de serialização de uma mensagem, Marshalling da mensagem pelo limite do processo e, em seguida, desserializando-a no cliente. Esse trabalho é tratado pelo objeto `Messenger`. Este diagrama mostra os componentes Android do lado do cliente envolvidos quando um cliente inicia a ligação a um serviço fora do processo:

![Diagrama que mostra as etapas e os componentes de uma associação de cliente a um serviço](out-of-process-services-images/ipc-01.png "Diagrama que mostra as etapas e os componentes de uma associação de cliente a um serviço.")

A classe `Service` no processo remoto passará pelos mesmos retornos de chamada de ciclo de vida que um serviço ligado em um processo local passará, e muitas das APIs envolvidas serão as mesmas. `Service.OnCreate` é usado para inicializar um `Handler` e injeta-lo em `Messenger` objeto. Da mesma forma, `OnBind` é substituído, mas em vez de retornar um objeto `IBinder`, o serviço retornará o `Messenger`.  Este diagrama ilustra o que acontece no serviço quando um cliente está ligando a ele:

![Diagrama que mostra as etapas e os componentes que um serviço passa ao ser ligado por um cliente remoto](out-of-process-services-images/ipc-02.png "Diagrama que mostra as etapas e os componentes que um serviço passa ao ser ligado por um cliente remoto.")

Quando um `Message` é recebido por um serviço, ele é processado por na instância do `Android.OS.Handler`. O serviço implementará sua própria subclasse `Handler`, que deve substituir o método `HandleMessage`. Esse método é invocado pelo `Messenger` e recebe o `Message` como um parâmetro. O `Handler` inspecionará os metadados de `Message` e usará essas informações para invocar métodos no serviço.

A comunicação unidirecional ocorre quando um cliente cria um objeto `Message` e o envia para o serviço usando o método `Messenger.Send`. `Messenger.Send` serializará a `Message` e a mão que serializam os dados para o Android, que rotearão a mensagem pelo limite do processo e para o serviço.  O `Messenger` hospedado pelo serviço criará um objeto de `Message` dos dados de entrada. Esse `Message` é colocado em uma fila, em que as mensagens são enviadas uma de cada vez para a `Handler`. O `Handler` inspecionará os metadados contidos no `Message` e invocará os métodos apropriados no `Service`. O diagrama a seguir ilustra esses vários conceitos em ação:

![Diagrama mostrando como as mensagens são passadas entre os processos](out-of-process-services-images/ipc-03.png "Diagrama mostrando como as mensagens são passadas entre os processos.")

Este guia abordará os detalhes da implementação de um serviço fora do processo. Ele discutirá como implementar um serviço que deve ser executado em seu próprio processo e como um cliente pode se comunicar com esse serviço usando o `Messenger` Framework. Ele também abordará brevemente a comunicação bidirecional: o cliente que envia uma mensagem para um serviço e o serviço que envia uma mensagem de volta para o cliente. Como os serviços podem ser compartilhados entre diferentes aplicativos, este guia também discutirá uma técnica para limitar o acesso do cliente ao serviço usando as permissões do Android.

> [!IMPORTANT]
> [Bugzilla 51940/GitHub 1950-os serviços com processos isolados e a classe de aplicativo personalizada falham ao resolver sobrecargas corretamente](https://github.com/xamarin/xamarin-android/issues/1950) relata que um serviço Xamarin. Android não será iniciado corretamente quando o `IsolatedProcess` for definido como `true`. Este guia é fornecido para uma referência. Um aplicativo Xamarin. Android ainda deve ser capaz de se comunicar com um serviço fora do processo escrito em Java.

## <a name="requirements"></a>Requisitos

Este guia pressupõe familiaridade com a criação de serviços.

Embora seja possível usar intenções implícitas com aplicativos direcionados a APIs do Android mais antigas, este guia se concentrará exclusivamente no uso de intenções explícitas. Os aplicativos destinados ao Android 5,0 (API nível 21) ou superior devem usar uma intenção explícita para associar a um serviço; Esta é a técnica que será demonstrada neste guia.

## <a name="create-a-service-that-runs-in-a-separate-process"></a>Criar um serviço que é executado em um processo separado

Conforme descrito acima, o fato de um serviço estar em execução em seu próprio processo significa que algumas APIs diferentes estão envolvidas. Como uma visão geral rápida, estas são as etapas para associar e consumir um serviço remoto:  

- **Crie a subclasse de `Service`** &ndash; subclasse o tipo de `Service` e implemente os métodos de ciclo de vida para um serviço associado. Também é necessário definir metadados que informarão ao Android que o serviço deve ser executado em seu próprio processo.
- **Implemente um `Handler`** &ndash; o `Handler` é responsável por analisar as solicitações do cliente, extrair quaisquer parâmetros que foram passados do cliente e invocar os métodos apropriados no serviço.
- **Instancie um `Messenger`** &ndash; conforme descrito acima, cada `Service` deve manter uma instância da classe `Messenger` que roteará solicitações do cliente para o `Handler` que foi criado na etapa anterior.

Um serviço que deve ser executado em seu próprio processo é, fundamentalmente, ainda um serviço associado. A classe de serviço estenderá a classe de `Service` base e será decorada com o `ServiceAttribute` que contém os metadados que o Android precisa agrupar no manifesto do Android. Para começar, as seguintes propriedades do `ServiceAttribute` que são importantes para um serviço fora do processo:

1. `Exported` &ndash; essa propriedade deve ser definida como `true` para permitir que outros aplicativos interajam com o serviço. O valor padrão dessa propriedade é `false`.
2. `Process` &ndash; essa propriedade deve ser definida. Ele é usado para especificar o nome do processo no qual o serviço será executado.
3. `IsolatedProcess` &ndash; essa propriedade permitirá segurança extra, informando ao Android para executar o serviço em uma área restrita isolada com permissão mínima para interagir com o restante do sistema. Consulte [Bugzilla 51940-os serviços com processos isolados e a classe de aplicativo personalizada falham ao resolver sobrecargas corretamente](https://bugzilla.xamarin.com/show_bug.cgi?id=51940).
4. `Permission` &ndash; é possível controlar o acesso do cliente ao serviço especificando uma permissão que os clientes devem solicitar (e serem concedidos).

Para executar um serviço seu próprio processo, a propriedade `Process` na `ServiceAttribute` deve ser definida como o nome do serviço. Para interagir com aplicativos externos, a propriedade `Exported` deve ser definida como `true`. Se `Exported` for `false`, somente os clientes no mesmo APK (ou seja, o mesmo aplicativo) e em execução no mesmo processo poderão interagir com o serviço.

O tipo de processo no qual o serviço será executado depende do valor da propriedade `Process`. O Android identifica três tipos diferentes de processos:

- O **processo privado** &ndash; um processo privado é aquele que está disponível apenas para o aplicativo que o iniciou. Para identificar um processo como particular, seu nome deve começar com um **:** (ponto e vírgula). O serviço descrito no trecho de código anterior e captura de tela é um processo privado. O trecho de código a seguir é um exemplo da `ServiceAttribute`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process=":timestampservice_process",
             Exported=true)]
    ```

- O **processo global** &ndash; um serviço que é executado em um processo global é acessível a todos os aplicativos em execução no dispositivo. Um processo global deve ser um nome de classe totalmente qualificado que começa com um caractere minúsculo.
    (A menos que sejam executadas etapas para proteger o serviço, outros aplicativos podem associar e interagir com ele. A proteção do serviço contra uso não autorizado será discutida posteriormente neste guia.)

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

- O **processo isolado** &ndash; um processo isolado é um processo executado em sua própria área restrita, isolado do restante do sistema e sem nenhuma permissão especial. Para executar um serviço em um processo isolado, a propriedade `IsolatedProcess` da `ServiceAttribute` é definida como `true`, conforme mostrado neste trecho de código:
    
    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             IsolatedProcess= true,
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

> [!IMPORTANT]
> Consulte [Bugzilla 51940-serviços com processos isolados e classe de aplicativo personalizada falha ao resolver sobrecargas corretamente](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)

Um serviço isolado é uma maneira simples de proteger um aplicativo e o dispositivo contra código não confiável. Por exemplo, um aplicativo pode baixar e executar um script de um site. Nesse caso, fazer isso em um processo isolado fornece uma camada adicional de segurança contra código não confiável comprometendo o dispositivo Android.

> [!IMPORTANT]
> Depois que um serviço é exportado, o nome do serviço não deve ser alterado. Alterar o nome do serviço pode interromper outros aplicativos que estão usando o serviço.

Para ver o efeito que a propriedade `Process` tem, a captura de tela a seguir mostra um serviço em execução em seu próprio processo privado:

![Captura de tela que mostra um serviço em execução em um processo privado](out-of-process-services-images/ipc-04.png "Captura de tela que mostra um serviço em execução em um processo privado.")

Esta próxima captura de tela mostra `Process="com.xamarin.xample.messengerservice.timestampservice_process"` e o serviço em execução em um processo global:

![Captura de tela de um serviço em execução em um processo global](out-of-process-services-images/ipc-05.png "Captura de tela de um serviço em execução em um processo global.")

Depois que o `ServiceAttribute` tiver sido definido, o serviço precisará implementar um `Handler`.

### <a name="implementing-a-handler"></a>Implementando um manipulador

Para processar solicitações de cliente, o serviço deve implementar um `Handler` e substituir o `HandleMessage` methodThis é o método usa uma instância de `Message` que encapsula a chamada de método do cliente e converte essa chamada em alguma ação ou tarefa que o serviço irá executar. O objeto `Message` expõe uma propriedade chamada `What` que é um valor inteiro, o significado do qual é compartilhado entre o cliente e o serviço e está relacionado a alguma tarefa que o serviço deve executar para o cliente.

O trecho de código a seguir do aplicativo de exemplo mostra um exemplo de `HandleMessage`. Neste exemplo, há duas ações que um cliente pode solicitar do serviço:

- A primeira ação é uma mensagem _Hello, World_ , o cliente enviou uma mensagem simples para o serviço.
- A segunda ação invocará um método no serviço e recuperará uma cadeia de caracteres, nesse caso, a cadeia de caracteres é uma mensagem que retorna a hora em que o serviço foi iniciado e por quanto tempo ele está em execução:

```csharp
public class TimestampRequestHandler : Android.OS.Handler
{
    // other code omitted for clarity

    public override void HandleMessage(Message msg)
    {
        int messageType = msg.What;
        Log.Debug(TAG, $"Message type: {messageType}.");

        switch (messageType)
        {
            case Constants.SAY_HELLO_TO_TIMESTAMP_SERVICE:
                // The client as sent a simple Hello, say in the Android Log.
                break;

            case Constants.GET_UTC_TIMESTAMP:
                // Call methods on the service to retrieve a timestamp message.
                break;
            default:
                Log.Warn(TAG, $"Unknown messageType, ignoring the value {messageType}.");
                base.HandleMessage(msg);
                break;
        }
    }
}
```

Também é possível empacotar parâmetros para o serviço no `Message`. Isso será discutido posteriormente neste guia. O próximo tópico a ser considerado é a criação do objeto de `Messenger` para processar o `Message`s de entrada.

### <a name="instantiating-the-messenger"></a>Instanciando o Messenger

Conforme discutido anteriormente, a desserialização do objeto de `Message` e a invocação de `Handler.HandleMessage` é responsabilidade do objeto de `Messenger`. A classe `Messenger` também fornece um objeto `IBinder` que o cliente usará para enviar mensagens ao serviço.  

Quando o serviço for iniciado, ele criará uma instância do `Messenger` e injetará o `Handler`. Um bom local para executar essa inicialização é no método `OnCreate` do serviço. Este trecho de código é um exemplo de um serviço que inicializa seu próprio `Handler` e `Messenger`:

```csharp
private Messenger messenger; // Instance variable for the Messenger

public override void OnCreate()
{
    base.OnCreate();
    messenger = new Messenger(new TimestampRequestHandler(this));
    Log.Info(TAG, $"TimestampService is running in process id {Android.OS.Process.MyPid()}.");
}
```

Neste ponto, a etapa final é para o `Service` substituir `OnBind`.

### <a name="implementing-serviceonbind"></a>Implementando Service. onBind

Todos os serviços associados, independentemente de serem executados em seu próprio processo ou não, devem implementar o método `OnBind`. O valor de retorno desse método é um objeto que o cliente pode usar para interagir com o serviço. Exatamente o que esse objeto depende se o serviço é um serviço local ou remoto. Embora um serviço local retorne uma implementação de `IBinder` personalizada, um serviço remoto retornará o `IBinder` encapsulado, mas o `Messenger` que foi criado na seção anterior:

```csharp
public override IBinder OnBind(Intent intent)
{
    Log.Debug(TAG, "OnBind");
    return messenger.Binder;
}
```

Depois que essas três etapas forem realizadas, o serviço remoto poderá ser considerado concluído.

## <a name="consuming-the-service"></a>Consumindo o serviço

Todos os clientes devem implementar algum código para poder ligar e consumir o serviço remoto. Conceitualmente, do ponto de vista do cliente, há poucas diferenças entre a associação ao serviço local ou um serviço remoto. O cliente invoca o método `BindService`, passando uma intenção explícita de identificar o serviço e um `IServiceConnection` que ajuda a gerenciar a conexão entre o cliente e o serviço.

Este trecho de código é um exemplo de como criar uma **intenção explícita** para a associação a um serviço remoto. A intenção deve identificar o pacote que contém o serviço e o nome do serviço. Uma maneira de definir essas informações é usar um objeto `Android.Content.ComponentName` e fornecer isso à intenção. Este trecho de código é um exemplo:  

```csharp
// This is the package name of the APK, set in the Android manifest
const string REMOTE_SERVICE_COMPONENT_NAME = "com.xamarin.TimestampService";
// This is the name of the service, according the value of ServiceAttribute.Name
const string REMOTE_SERVICE_PACKAGE_NAME   = "com.xamarin.xample.messengerservice";

// Provide the package name and the name of the service with a ComponentName object.
ComponentName cn = new ComponentName(REMOTE_SERVICE_PACKAGE_NAME, REMOTE_SERVICE_COMPONENT_NAME);
Intent serviceToStart = new Intent();
serviceToStart.SetComponent(cn);
```

Quando o serviço é associado, o método `IServiceConnection.OnServiceConnected` é invocado e fornece um `IBinder` a um cliente. No entanto, o cliente não usará diretamente o `IBinder`. Em vez disso, ele criará uma instância de um objeto `Messenger` a partir desse `IBinder`. Esse é o `Messenger` que o cliente usará para interagir com o serviço remoto.

Veja a seguir um exemplo de uma implementação de `IServiceConnection` muito básica que demonstra como um cliente trataria de se conectar e desconectar de um serviço. Observe que o método `OnServiceConnected` recebe e `IBinder` e o cliente cria uma `Messenger` do `IBinder`:

```csharp
public class TimestampServiceConnection : Java.Lang.Object, IServiceConnection
{
    static readonly string TAG = typeof(TimestampServiceConnection).FullName;

    MainActivity mainActivity;
    Messenger messenger;

    public TimestampServiceConnection(MainActivity activity)
    {
        IsConnected = false;
        mainActivity = activity;
    }

    public bool IsConnected { get; private set; }
    public Messenger Messenger { get; private set; }

    public void OnServiceConnected(ComponentName name, IBinder service)
    {
        Log.Debug(TAG, $"OnServiceConnected {name.ClassName}");

        IsConnected = service != null
        Messenger = new Messenger(service);

        if (IsConnected)
        {
            // things to do when the connection is successful. perhaps notify the client? enable UI features?
        }
        else
        {
            // things to do when the connection isn't successful.
        }
    }

    public void OnServiceDisconnected(ComponentName name)
    {
        Log.Debug(TAG, $"OnServiceDisconnected {name.ClassName}");
        IsConnected = false;
        Messenger = null;

        // Things to do when the service disconnects. perhaps notify the client? disable UI features?

    }
}
```

Depois que a conexão de serviço e a intenção são criadas, é possível que o cliente chame `BindService` e inicie o processo de associação:

```csharp
IServiceConnection serviceConnection = new TimestampServiceConnection(this);
BindActivity(serviceToStart, serviceConnection, Bind.AutoCreate);
```

Depois que o cliente é vinculado com êxito ao serviço e o `Messenger` está disponível, é possível que o cliente envie `Messages` ao serviço.

## <a name="sending-messages-to-the-service"></a>Enviando mensagens para o serviço

Depois que o cliente está conectado e tem um objeto `Messenger`, é possível se comunicar com o serviço expedindo `Message` objetos por meio do `Messenger`. Essa comunicação é unidirecional, o cliente envia a mensagem, mas não há nenhuma mensagem de retorno do serviço para o cliente. Nesse sentido, o `Message` é um mecanismo de incêndio e esquecer.

A maneira preferida de criar um objeto de `Message` é usar o método de fábrica de [`Message.Obtain`](xref:Android.OS.Message) . Esse método efetuará pull de um objeto `Message` de um pool global mantido pelo Android. `Message.Obtain` também tem alguns métodos sobrecarregados que permitem que o objeto de `Message` seja inicializado com os valores e parâmetros exigidos pelo serviço.  Depois que o `Message` é instanciado, ele é enviado para o serviço chamando `Messenger.Send`. Este trecho de código é um exemplo de criação e expedição de um `Message` para o processo de serviço:

```csharp
Message msg = Message.Obtain(null, Constants.SAY_HELLO_TO_TIMESTAMP_SERVICE);
try
{
    serviceConnection.Messenger.Send(msg);
}
catch (RemoteException ex)
{
    Log.Error(TAG, ex, "There was a error trying to send the message.");
}
```

Há várias formas diferentes do método `Message.Obtain`. O exemplo anterior usa o [`Message.Obtain(Handler h, Int32 what)`](xref:Android.OS.Message.Obtain). Porque essa é uma solicitação assíncrona a um serviço fora do processo; Não haverá resposta do serviço, portanto, o `Handler` está definido como `null`. O segundo parâmetro, `Int32 what`, será armazenado na propriedade `.What` do objeto `Message`. A propriedade `.What` é usada pelo código no processo de serviço para invocar métodos no serviço.

A classe `Message` também expõe duas propriedades adicionais que podem ser usadas para o destinatário: `Arg1` e `Arg2`. Essas duas propriedades são valores inteiros que podem ter alguns valores acordados especiais que têm significado entre o cliente e o serviço. Por exemplo, `Arg1` pode conter uma ID de cliente e `Arg2` pode conter um número de ordem de compra para esse cliente. O [`Method.Obtain(Handler h, Int32 what, Int32 arg1, Int32 arg2)`](xref:Android.OS.Message.Obtain) pode ser usado para definir as duas propriedades quando o `Message` é criado. Outra maneira de preencher esses dois valores é definir as propriedades `.Arg` e `.Arg2` diretamente no objeto `Message` depois que ele tiver sido criado.

### <a name="passing-additional-values-to-the-service"></a>Passando valores adicionais para o serviço

É possível passar dados mais complexos para o serviço usando um `Bundle`. Nesse caso, valores extras podem ser colocados em um `Bundle` e enviados junto com o `Message` definindo a propriedade de [propriedade `.Data`](xref:Android.OS.Message.Data) antes de enviar.

```csharp
Bundle serviceParameters = new Bundle();
serviceParameters.

var msg = Message.Obtain(null, Constants.SERVICE_TASK_TO_PERFORM);
msg.Data = serviceParameters;

messenger.Send(msg);
```

> [!NOTE]
> Em geral, um `Message` não deve ter uma carga maior que 1 MB. O limite de tamanho pode variar de acordo com a versão do Android e em qualquer alteração proprietária que o fornecedor possa ter feito em sua implementação do AOSP (Open Source Project) do Android que é fornecido com o dispositivo.

## <a name="returning-values-from-the-service"></a>Retornando valores do serviço

A arquitetura de mensagens que foi discutida para esse ponto é unidirecional, o cliente envia uma mensagem para o serviço. Se for necessário que o serviço retorne um valor para um cliente, tudo o que foi discutido para esse ponto será invertido. O serviço deve criar um `Message`, empacotado quaisquer valores de retorno e enviar o `Message` por meio de um `Messenger` ao cliente. No entanto, o serviço não cria seu próprio `Messenger`; em vez disso, ele se baseia no cliente instanciando e empacotando um `Messenger` como parte da solicitação inicial. O serviço `Send`á a mensagem usando este `Messenger` fornecido pelo cliente.  

A sequência de eventos para comunicação bidirecional é esta:

1. O cliente é associado ao serviço. Quando o serviço e o cliente se conectam, o `IServiceConnection` que é mantido pelo cliente terá uma referência a um objeto `Messenger` que é usado para transmitir `Message`s para o serviço. Para evitar confusão, isso será chamado de _Messenger do serviço_.
2. O cliente cria uma instância de um `Handler` (chamado de _manipulador de cliente_) e o usa para inicializar seu próprio `Messenger` (o _cliente Messenger_). Observe que o serviço Mensageiro e o cliente Messenger são dois objetos diferentes que manipulam o tráfego em duas direções diferentes. O serviço Messenger lida com mensagens do cliente para o serviço, enquanto o Messenger do cliente tratará as mensagens do serviço para o cliente.
3. O cliente cria um objeto `Message` e define a propriedade `ReplyTo` com o cliente Messenger. A mensagem é enviada para o serviço usando o serviço mensageiro.
4. O serviço recebe a mensagem do cliente e executa o trabalho solicitado.
5. Quando for o momento em que o serviço enviar a resposta para o cliente, ele usará `Message.Obtain` para criar um novo objeto de `Message`.
6. Para enviar essa mensagem ao cliente, o serviço extrairá o cliente mensageiro da propriedade `.ReplyTo` da mensagem do cliente e usará isso para `.Send` o `Message` de volta ao cliente.
7. Quando a resposta é recebida pelo cliente, ela tem seu próprio `Handler` que processará o `Message` inspecionando a propriedade `.What` (e, se necessário, extraindo quaisquer parâmetros contidos na `Message`).

Este código de exemplo demonstra como o cliente criará uma instância do `Message` e empacotará um `Messenger` que o serviço deve usar para sua resposta:

```csharp
Handler clientHandler = new ActivityHandler();
Messenger clientMessenger = new Messenger(activityHandler);

Message msg = Message.Obtain(null, Constants.GET_UTC_TIMESTAMP);
msg.ReplyTo = clientMessenger;

try
{
    serviceConnection.Messenger.Send(msg);
}
catch (RemoteException ex)
{
    Log.Error(TAG, ex, "There was a problem sending the message.");
}
```

O serviço deve fazer algumas alterações em seu próprio `Handler` para extrair o `Messenger` e usá-lo para enviar respostas ao cliente. Esse trecho de código é um exemplo de como o `Handler` do serviço criaria um `Message` e o enviaria de volta ao cliente:  

```csharp
// This is the message that the service will send to the client.
Message responseMessage = Message.Obtain(null, Constants.RESPONSE_TO_SERVICE);
Bundle dataToReturn = new Bundle();
dataToReturn.PutString(Constants.RESPONSE_MESSAGE_KEY, "This is the result from the service.");
responseMessage.Data = dataToReturn;

// The msg object here is the message that was received by the service. The service will not instantiate a client,
// It will use the client that is encapsulated by the message from the client.
Messenger clientMessenger = msg.ReplyTo;
if (clientMessenger!= null)
{
    try
    {
        clientMessenger.Send(responseMessage);
    }
    catch (Exception ex)
    {
        Log.Error(TAG, ex, "There was a problem sending the message.");
    }
}
```

Observe que nos exemplos de código acima, a instância de `Messenger` que é criada pelo cliente *não* é o mesmo objeto recebido pelo serviço. Esses são dois objetos de `Messenger` diferentes em execução em dois processos separados que representam o canal de comunicação.

## <a name="securing-the-service-with-android-permissions"></a>Protegendo o serviço com permissões do Android

Um serviço executado em um processo global pode ser acessado por todos os aplicativos em execução nesse dispositivo Android. Em algumas situações, essa abertura e disponibilidade são indesejáveis e é necessário proteger o serviço contra o acesso de clientes não autorizados. Uma maneira de limitar o acesso ao serviço remoto é usar as permissões do Android.

As permissões podem ser identificadas pela propriedade `Permission` do `ServiceAttribute` que decora a subclasse `Service`. Isso nomeará uma permissão que o cliente deve receber ao fazer a ligação com o serviço. Se o cliente não tiver as permissões apropriadas, o Android gerará um `Java.Lang.SecurityException` quando o cliente tentar se associar ao serviço.

Há quatro níveis de permissão diferentes que o Android fornece:

- **normal** &ndash; esse é o nível de permissão padrão. Ele é usado para identificar permissões de baixo risco que podem ser concedidas automaticamente pelo Android aos clientes que o solicitam. O usuário não precisa conceder essas permissões explicitamente, mas as permissões podem ser exibidas nas configurações do aplicativo.
- **assinatura** &ndash; essa é uma categoria especial de permissão que será concedida automaticamente pelo Android a aplicativos que são todos assinados com o mesmo certificado. Essa permissão foi projetada para tornar fácil para um desenvolvedor de aplicativos compartilhar componentes ou dados entre seus aplicativos sem incomodarr o usuário para aprovações constantes.
- **signatureOrSystem** &ndash; isso é muito semelhante às permissões de **assinatura** descritas acima. Além de ser concedido automaticamente a aplicativos que são assinados pelo mesmo certificado, essa permissão também será concedida aos aplicativos que assinaram o mesmo certificado que foi usado para assinar os aplicativos instalados com a imagem do sistema Android. Essa permissão normalmente é usada apenas por desenvolvedores de ROM Android para permitir que seus aplicativos funcionem com aplicativos de terceiros. Normalmente, ele não é usado por aplicativos que destinam a distribuição geral para o público de grande porte.
- permissões perigosas **perigosas** &ndash; são aquelas que podem causar problemas para o usuário. Por esse motivo, permissões **perigosas** devem ser explicitamente aprovadas pelo usuário.

Como as permissões `signature` e `normal` são concedidas automaticamente no momento da instalação pelo Android, é crucial que o APK que hospeda o serviço seja instalado **antes** do APK que contém o cliente. Se o cliente for instalado primeiro, o Android não concederá as permissões. Nesse caso, será necessário desinstalar o APK do cliente, instalar o serviço APK e, em seguida, reinstalar o APK do cliente.

Há duas maneiras comuns de proteger um serviço com permissões do Android:

1. **Implementar segurança em nível de assinatura** &ndash; segurança de nível de assinatura significa que a permissão é concedida automaticamente a esses aplicativos assinados com a mesma chave que foi usada para assinar o apk que contém o serviço. Essa é uma maneira simples para os desenvolvedores protegerem seus serviços, mantendo-os acessíveis de seus próprios aplicativos. As permissões de nível de assinatura são declaradas definindo a propriedade `Permission` da `ServiceAttribute` como `signature`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.TimestampService.timestampservice_process",
             Permission="signature")]
    public class TimestampService : Service
    {
    }
    ```

2. **Crie uma permissão personalizada** &ndash; é possível que o desenvolvedor do serviço crie uma permissão personalizada para o serviço. Isso é melhor para quando um desenvolvedor quiser compartilhar seus serviços com aplicativos de outros desenvolvedores. Uma permissão personalizada requer um pouco mais de esforço para implementar e será abordada abaixo.

Um exemplo simplificado de criação de uma permissão de `normal` personalizada será descrito na próxima seção. Para obter mais informações sobre as permissões do Android, consulte a documentação do Google para obter [as práticas recomendadas & segurança](https://developer.android.com/training/articles/security-tips.html). Para obter mais informações sobre permissões do Android, consulte a [seção permissões](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms) da documentação do Android para o manifesto do aplicativo para obter mais informações sobre as permissões do Android.

> [!NOTE]
> Em geral, [o Google não incentiva o uso de permissões personalizadas](https://developer.android.com/training/articles/security-tips.html#RequestingPermissions) , pois elas podem provar confusas para os usuários.

### <a name="creating-a-custom-permission"></a>Criando uma permissão personalizada

Para usar uma permissão personalizada, ela é declarada pelo serviço enquanto o cliente solicita essa permissão explicitamente.

Para criar uma permissão no serviço APK, um elemento `permission` é adicionado ao elemento `manifest` em **AndroidManifest. xml**. Essa permissão deve ter os atributos `name`, `protectionLevel` e `label` definidos. O atributo `name` deve ser definido como uma cadeia de caracteres que identifica exclusivamente a permissão. O nome será exibido na exibição **informações do aplicativo** das configurações do **Android** (conforme mostrado na próxima seção).

O atributo `protectionLevel` deve ser definido como um dos quatro valores de cadeia de caracteres descritos acima.  O `label` e `description` devem se referir a recursos de cadeia de caracteres e são usados para fornecer um nome amigável e uma descrição para o usuário.

Esse trecho de código é um exemplo de declaração de um atributo de `permission` personalizado em **AndroidManifest. xml** do APK que contém o serviço:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="com.xamarin.xample.messengerservice">

    <uses-sdk android:minSdkVersion="21" />

    <permission android:name="com.xamarin.xample.messengerservice.REQUEST_TIMESTAMP"
                android:protectionLevel="signature"
                android:label="@string/permission_label"
                android:description="@string/permission_description"
                />

    <application android:allowBackup="true"
            android:icon="@mipmap/icon"
            android:label="@string/app_name"
            android:theme="@style/AppTheme">

    </application>
</manifest>
```

Em seguida, o **AndroidManifest. xml** do APK do cliente deve solicitar explicitamente essa nova permissão. Isso é feito adicionando o atributo `users-permission` ao **AndroidManifest. xml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="com.xamarin.xample.messengerclient">

    <uses-sdk android:minSdkVersion="21" />

    <uses-permission android:name="com.xamarin.xample.messengerservice.REQUEST_TIMESTAMP" />

    <application
            android:allowBackup="true"
            android:icon="@mipmap/icon"
            android:label="@string/app_name"
            android:theme="@style/AppTheme">
    </application>
    </manifest>
```

### <a name="view-the-permissions-granted-to-an-app"></a>Exibir as permissões concedidas a um aplicativo

Para exibir as permissões que um aplicativo recebeu, abra o aplicativo Configurações do Android e selecione **aplicativos**. Localize e selecione o aplicativo na lista. Na tela de **informações do aplicativo** , toque em **permissões** que abrirão uma exibição que mostra todas as permissões concedidas ao aplicativo:

[![Screenshots de um dispositivo Android mostrando como localizar as permissões concedidas a um aplicativo](out-of-process-services-images/ipc-06-sml.png)](out-of-process-services-images/ipc-06.png#lightbox)

## <a name="summary"></a>Resumo

Este guia foi uma discussão avançada sobre como executar um serviço Android em um processo remoto. As diferenças entre um serviço local e um remoto foram explicadas, juntamente com alguns motivos pelos quais um serviço remoto pode ser útil para a estabilidade e o desempenho de um aplicativo Android. Depois de explicar como implementar um serviço remoto e como um cliente pode se comunicar com o serviço, o guia passou a fornecer uma maneira de limitar o acesso ao serviço somente de clientes autorizados.

## <a name="related-links"></a>Links relacionados

- [Cliques](xref:Android.OS.Handler)
- [Message](xref:Android.OS.Message)
- [Mensageiro](xref:Android.OS.Messenger)
- [ServiceAttribute](xref:Android.App.ServiceAttribute)
- [O atributo exportado](https://developer.android.com/guide/topics/manifest/service-element.html#exported)
- [Serviços com processos isolados e classe de aplicativo personalizada falham ao resolver sobrecargas corretamente](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)
- [Processos e threads](https://developer.android.com/guide/components/processes-and-threads.html)
- [Manifesto do Android-permissões](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)
- [Dicas de segurança](https://developer.android.com/training/articles/security-tips.html)
- [MessengerServiceDemo (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-messengerservicedemo)
