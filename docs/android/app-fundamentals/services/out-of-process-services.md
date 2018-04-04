---
title: Serviços Android em execução em processos remotos
description: Em geral, todos os componentes em um aplicativo do Android serão executados no mesmo processo. Android serviços são uma exceção notável em que pode ser configurados para ser executado em seus próprios processos e compartilhados com outros aplicativos, incluindo aqueles de outros desenvolvedores do Android. Este guia sobre como criar e usar um serviço remoto Android usando o Xamarin.
ms.prod: xamarin
ms.assetid: 27A2E972-A690-480B-B31D-5EF1F74F673C
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 02/16/2018
ms.openlocfilehash: 57be8187509ad7607c38ea17233e9ab5d25b41f1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="running-android-services-in-remote-processes"></a>Serviços Android em execução em processos remotos

_Em geral, todos os componentes em um aplicativo do Android serão executados no mesmo processo. Android serviços são uma exceção notável em que pode ser configurados para ser executado em seus próprios processos e compartilhados com outros aplicativos, incluindo aqueles de outros desenvolvedores do Android. Este guia sobre como criar e usar um serviço remoto Android usando o Xamarin._

## <a name="out-of-process-services-overview"></a>Fora da visão geral dos serviços de processo

Quando um aplicativo é iniciado, o Android cria um processo no qual executar o aplicativo. Normalmente, todos os componentes do aplicativo será executado neste processo de um. Android serviços são uma exceção notável em que pode ser configurados para ser executado em seus próprios processos e compartilhados com outros aplicativos, incluindo aqueles de outros desenvolvedores do Android. Esses tipos de serviços são chamados de _serviços remotos_ ou _serviços fora do processo_. O código para esses serviços estarão contido no APK mesmo que o aplicativo principal; No entanto, quando o serviço é iniciado Android criará um novo processo apenas desse serviço. Por outro lado, um serviço que é executado no mesmo processo que o restante do aplicativo é às vezes conhecido como um _serviço local_.

Em geral, não é necessário para um aplicativo implementar um serviço remoto. Um serviço local é suficiente (e desejável) para obter os requisitos do aplicativo em muitos casos. Um limite de processo tem seu próprio espaço de memória que deve ser gerenciado pelo Android. Embora isso apresentam maior sobrecarga para o aplicativo geral, há alguns cenários onde pode ser vantajoso para executar um serviço em seu próprio processo:

1. **Funcionalidade de compartilhamento** &ndash; alguns desenvolvedores de aplicativo podem ter vários aplicativos e a funcionalidade que é compartilhada entre todos os aplicativos. Empacotando essa funcionalidade em um serviço Android que é executado em seu próprio processo pode simplificar a manutenção do aplicativo. Também é possível empacotar o serviço em seu próprio APK autônomo e implantá-lo separadamente do restante do aplicativo.
2. **Melhorar a experiência de usuário** &ndash; há duas maneiras possíveis que um serviço de fora do processo pode melhorar a experiência do usuário do aplicativo. O primeiro modo lida com gerenciamento de memória. Quando uma coleta de lixo (GC) ocorre ciclo Android fará uma pausa todas as atividades do processo até que o GC seja concluído. O usuário pode perceber essa pausa como "repetitivo" ou "jank". Quando um serviço está em execução próprio processo é, é o processo de serviço que está em pausa, não o processo do aplicativo. Essa pausa será muito menos significativo para o usuário como o processo do aplicativo (e, portanto, a interface do usuário) não está em pausa.

    Em segundo lugar, se os requisitos de memória de um processo ficar muito grande, o Android pode eliminar esse processo para liberar recursos para o dispositivo. Se um serviço tiver um volume grande de memória e ele está em execução no mesmo processo que a interface do usuário, em seguida, quando Android forçadamente recupera os recursos a interface do usuário será desligado, forçar o usuário a iniciar o aplicativo. No entanto, se um serviço em execução em seu próprio processo é desligado pelo Android, o processo de interface do usuário permanece afetado. A interface do usuário pode associar (e reinicie) o serviço, transparente para o usuário e retomar o funcionamento normal.

3. **Melhorando o desempenho do aplicativo** &ndash; processo a interface do usuário pode ser encerrado ou desligar independentemente do processo do serviço. Movendo as tarefas de inicialização longo para um serviço de fora do processo, é possível que o tempo de inicialização da interface do usuário pode ser melhorado (supondo que o processo do serviço é mantido ativo entre os horários em que a interface do usuário é iniciado).

Em muitos aspectos, associação a um serviço em execução em outro processo é o mesmo que [de associação para um serviço local](~/android/app-fundamentals/services/creating-a-service/bound-services.md). O cliente chamará `BindService` associar (e iniciar, se necessário) o serviço. Um `Android.OS.IServiceConnection` objeto será criado para gerenciar a conexão entre o cliente e o serviço. Se o cliente associa com êxito para o serviço, o Android retornará um objeto por meio de `IServiceConnection` que pode ser usado para invocar os métodos do serviço. O cliente, em seguida, interage com o serviço de uso do objeto. Para examinar, aqui estão as etapas para vincular a um serviço:

* **Criar uma intenção** &ndash; uma tentativa explícita deve ser usada para associação com o serviço.
* **Implementar e instanciar uma `IServiceConnection` objeto** &ndash; o `IServiceConnection` objeto atua como um intermediário entre o cliente e o serviço.  Ele é responsável por monitorar a conexão entre cliente e servidor.
* **Invocar o `BindService` método** &ndash; chamando `BindService` enviará a intenção e a conexão de serviço criado nas etapas anteriores para Android, que será responsável por iniciar o serviço e estabelecer a comunicação entre cliente e serviço.

A necessidade de ultrapassar os limites de processo introduz uma complexidade extra: a comunicação é unidirecional (cliente para servidor) e o cliente não é possível chamar diretamente métodos na classe de serviço. Lembre-se de que quando um serviço é executado o mesmo processo que o cliente, Android fornece um `IBinder` objeto que pode permitir a comunicação bidirecional. Isso não é o caso com o serviço está em execução em seu próprio processo. Um cliente se comunica com um serviço remoto com a Ajuda do `Android.OS.Messenger` classe.

Quando um cliente solicita para associar com o serviço remoto, Android invocará o `Service.OnBind` método de ciclo de vida, que retornará interno `IBinder` objeto que é encapsulado pelo `Messenger`. O `Messenger` é um wrapper fino sobre especial `IBinder` implementação que é fornecida pelo SDK do Android. O `Messenger` cuida da comunicação entre os dois processos diferentes. O desenvolvedor é preocupado com os detalhes de serializar uma mensagem, o empacotamento de mensagem através do limite de processo e ao desserializar no cliente. Esse trabalho é tratado pelo `Messenger` objeto. Este diagrama mostra os componentes de cliente Android envolvidas quando um cliente inicia a associação a um serviço de fora do processo:

![Diagrama que mostra as etapas e os componentes de uma associação a um serviço de cliente](out-of-process-services-images/ipc-01.png "diagrama que mostra as etapas e os componentes de uma associação a um serviço de cliente.")

O `Service` classe no processo remoto revisará os retornos de chamada do ciclo de vida mesmo que um serviço vinculado em um processo local revisará e muitas das APIs envolvidas são os mesmos. `Service.OnCreate` é usado para inicializar um `Handler` e injetar que em `Messenger` objeto. Da mesma forma, `OnBind` é substituída, mas em vez de retornar um `IBinder` objeto, o serviço retornará o `Messenger`.  Este diagrama ilustra o que acontece no serviço quando um cliente está associado a ele:

![Diagrama que mostra as etapas e os componentes de um serviço segue ao associado por um cliente remoto](out-of-process-services-images/ipc-02.png "diagrama que mostra as etapas e os componentes de um serviço segue ao associado por um cliente remoto.")

Quando um `Message` é recebida por um serviço, ela é processada na instância do `Android.OS.Handler`. O serviço implementar seu próprio `Handler` subclasse deve substituir o `HandleMessage` método. Esse método é chamado pelo `Messenger` e recebe o `Message` como um parâmetro. O `Handler` irá inspecionar o `Message` metadados e usar essas informações para invocar os métodos do serviço.

Comunicação unidirecional ocorre quando um cliente cria uma `Message` de objeto e distribui-o ao serviço usando o `Messenger.Send` método. `Messenger.Send` serializa o `Message` e disponível que dados serializado para Android, que roteará a mensagem através do limite de processo e o serviço.  O `Messenger` que é hospedado pelo serviço criará um `Message` objeto de dados de entrada. Isso `Message` é colocado em uma fila, onde as mensagens são enviados um por vez para o `Handler`. O `Handler` irá inspecionar os metadados contidos no `Message` e chamar os métodos apropriados no `Service`. O diagrama a seguir ilustra esses vários conceitos em ação:

![Diagrama mostrando como as mensagens são passadas entre processos](out-of-process-services-images/ipc-03.png "diagrama que mostra como as mensagens são passadas entre processos.")

Este guia discutir os detalhes de implementação de um serviço de fora do processo. Discutiremos como implementar um serviço que deve ser executado em seu próprio processo e como um cliente pode se comunicar com esse serviço usando o `Messenger` framework. Ele também brevemente abordará comunicação bidirecional: o cliente enviar uma mensagem para um serviço e o serviço de envio de uma mensagem de volta ao cliente. Porque os serviços podem ser compartilhados entre aplicativos diferentes, este guia também aborda uma técnica para limitar o acesso de cliente para o serviço, usando permissões Android.

> [!IMPORTANT]
> [Bugzilla 51940 - serviços com processos isolados e a classe de aplicativo personalizado não resolver corretamente o sobrecargas](https://bugzilla.xamarin.com/show_bug.cgi?id=51940) relatórios que um serviço xamarin não será iniciado corretamente quando o `IsolatedProcess` é definido como `true`. Este guia é fornecido para uma referência. Um aplicativo xamarin ainda deve ser capaz de se comunicar com um serviço de fora do processo que está escrito em Java.

## <a name="requirements"></a>Requisitos

Este guia presume familiaridade com a criação de serviços.

Embora seja possível usar tentativas implícita com aplicativos voltados para o mais antigo APIs Android, este guia se concentrar exclusivamente no uso de propósitos explícitos. Aplicativos voltados para o Android 5.0 (API nível 21) ou superior deve usar uma tentativa explícita para associar com um serviço; Essa é a técnica que será demonstrada neste guia.

## <a name="create-a-service-that-runs-in-a-separate-process"></a>Criar um serviço que é executado em um processo separado

Conforme descrito acima, o fato de que um serviço está em execução em seu próprio processo significa que algumas APIs diferentes estão envolvidos. Uma visão geral rápida, aqui estão as etapas para associar com e consumir um serviço remoto:  

* **Criar o `Service` subclasse** &ndash; subclasse o `Service` digite e implementar os métodos de ciclo de vida de um serviço vinculado. Também é necessário definir metadados que informam Android que o serviço é executado em seu próprio processo.
* **Implementar um `Handler`**  &ndash; o `Handler` é responsável por analisar as solicitações do cliente, extrair os parâmetros que foram passados do cliente e chamar os métodos apropriados no serviço.
* **Criar uma instância de um `Messenger`**  &ndash; conforme descrito acima, cada `Service` deve manter uma instância do `Messenger` classe que será rotear solicitações de cliente para o `Handler` que foi criado na etapa anterior.

Um serviço que deve ser executado em seu próprio processo, essencialmente, ainda é um serviço vinculado. A classe de serviço estenderá a base de `Service` classe e decorada com o `ServiceAttribute` que contém os metadados que Android precisa agrupar no manifesto do Android. Para começar com as seguintes propriedades da `ServiceAttribute` que são importantes para um serviço de fora do processo:

1. `Exported` &ndash; Essa propriedade deve ser definida como `true` para permitir que outros aplicativos interagirem com o serviço. O valor padrão dessa propriedade é `false`.
2. `Process` &ndash; Essa propriedade deve ser definida. Ele é usado para especificar o nome do processo que o serviço será executado.
3. `IsolatedProcess` &ndash; Essa propriedade permite segurança adicional, informando ao Android para executar o serviço em uma área restrita isolada com permissão mínima para iteract com o restante do sistema. Consulte [Bugzilla 51940 - serviços com processos isolados e a classe de aplicativo personalizado não conseguir resolver sobrecargas corretamente](https://bugzilla.xamarin.com/show_bug.cgi?id=51940).
4. `Permission` &ndash; É possível controlar o acesso de cliente para o serviço especificando uma permissão que os clientes devem solicitar (e receber).

Para executar um serviço em seu próprio processo, o `Process` propriedade o `ServiceAttribute` deve ser definido como o nome do serviço. Para interagir com aplicativos externos, o `Exported` propriedade deve ser definida como `true`. Se `Exported` é `false`, somente a clientes no mesmo APK (ou seja, o mesmo aplicativo) e em execução no mesmo processo serão capazes de interagir com o serviço.

O tipo de processo que o serviço será executado em depende do valor da `Process` propriedade. Android identifica três tipos diferentes de processos:

-   **Processo particular** &ndash; um processo particular é aquele que está disponível somente para o aplicativo que ele foi iniciado. Para identificar um processo como particular, o nome deve começar com uma **:** (ponto e vírgula). O serviço representado no trecho de código anterior e a captura de tela é um processo particular. O trecho de código a seguir é um exemplo de como o `ServiceAttribute`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process=":timestampservice_process",
             Exported=true)]
    ```

-   **Processo global** &ndash; um serviço que é executado em um processo global está acessível a todos os aplicativos em execução no dispositivo. Um processo global deve ser um nome de classe totalmente qualificado que começa com um caractere minúsculo.
    (A menos que medidas sejam tomadas para proteger o serviço, outros aplicativos podem associar e interagir com ele. A proteção do serviço contra uso não autorizado será discutida neste documento.)

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

-   **Isolada processo** &ndash; um processo isolado é um processo que é executado em seu próprio sandbox, isolada do restante do sistema e com nenhuma permissão especial de seu próprio. Para executar um serviço em um processo isolado, o `IsolatedProcess` propriedade o `ServiceAttribute` é definido como `true` conforme mostrado no trecho de código a este:
    
    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             IsolatedProcess= true,
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

> [!IMPORTANT]
> Consulte [Bugzilla 51940 - serviços com processos isolados e a classe de aplicativo personalizado não conseguir resolver sobrecargas corretamente](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)

Um serviço a isoladas é uma maneira simple de proteger um aplicativo e o dispositivo com código não confiável. Por exemplo, um aplicativo pode baixar e executar um script de um site. Nesse caso, executar isso em um processo isolado fornece uma camada adicional de segurança de código não confiável comprometer o dispositivo Android.

> [!IMPORTANT]
> Depois que um serviço foi exportado, não deve alterar o nome do serviço. Alterar o nome do serviço de outros aplicativos que estão usando o serviço pode ser quebradas.

Para ver o efeito que o `Process` tem de propriedade, a captura de tela a seguir mostra um serviço em execução em seu próprio processo particular:

![Captura de tela que mostra um serviço em execução em um processo particular](out-of-process-services-images/ipc-04.png "captura de tela que mostra um serviço em execução em um processo em particular.")

Próxima captura de tela mostra `Process="com.xamarin.xample.messengerservice.timestampservice_process"` e o serviço em execução em um processo global:

![Captura de tela de um serviço em execução em um processo global](out-of-process-services-images/ipc-05.png "captura de tela de um serviço em execução em um processo global.")

Uma vez o `ServiceAttribute` tiver sido definido, o serviço precisa implementar um `Handler`.

### <a name="implementing-a-handler"></a>Implementar um manipulador

Para processar solicitações de cliente, o serviço deve implementar um `Handler` e substituir o `HandleMessage` methodThis é o método usa um `Message` de instância que que encapsula a chamada do método do cliente e converte essa chamada em alguma ação ou tarefa que executará o serviço. O `Message` objeto expõe uma propriedade chamada `What` que é um valor inteiro, o significado dos quais é compartilhado entre o cliente e o serviço e está relacionado a uma tarefa que o serviço está para executar o cliente.

O seguinte trecho de código do aplicativo de exemplo mostra um exemplo de `HandleMessage`. Neste exemplo, há duas ações que um cliente pode solicitar do serviço:

* A primeira ação é um _Olá, mundo_ mensagem, o cliente enviou uma mensagem simples para o serviço.
* A segunda ação irá invocar um método no serviço e recuperar uma cadeia de caracteres, nesse caso, a cadeia de caracteres é uma mensagem que retorna a que horas o serviço iniciado e por quanto tempo que ele está em execução:

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
                // Call methods on the service to retrive a timestamp message.
                break;
            default:
                Log.Warn(TAG, $"Unknown messageType, ignoring the value {messageType}.");
                base.HandleMessage(msg);
                break;
        }
    }
}
```

Também é possível para os parâmetros de pacote para o serviço de `Message`. Isso será discutido neste documento. O próximo tópico considerar está criando o `Messenger` objeto para processar a entrada `Message`s.

### <a name="instantiating-the-messenger"></a>Criando o Messenger

Conforme discutido anteriormente, ao desserializar o `Message` objeto e invocar `Handler.HandleMessage` é responsabilidade do `Messenger` objeto. O `Messenger` classe também fornece um `IBinder` que o cliente usará para enviar mensagens para o serviço do objeto.  

Quando o serviço é iniciado, ele criará uma instância de `Messenger` e injetar o `Handler`. É um bom lugar para realizar essa inicialização no `OnCreate` método do serviço. Este trecho de código é um exemplo de um serviço que inicia o seu próprio `Handler` e `Messenger`:

```csharp
private Messenger messenger; // Instance variable for the Messenger

public override void OnCreate()
{
    base.OnCreate();
    messenger = new Messenger(new TimestampRequestHandler(this));
    Log.Info(TAG, $"TimestampService is running in process id {Android.OS.Process.MyPid()}.");
}
```

Neste ponto, a etapa final é para o `Service` para substituir `OnBind`.

### <a name="implementing-serviceonbind"></a>Implementando Service.OnBind

Todos os serviços associados, se ele ser executado em seu próprio processo ou não, devem implementar o `OnBind` método. O valor de retorno desse método é um objeto que o cliente pode usar para interagir com o serviço. Exatamente o que esse objeto é depende se o serviço é um serviço local ou um serviço remoto. Enquanto um serviço local retornará um personalizado `IBinder` implementação, um serviço remoto retornará o `IBinder` que é encapsulado, mas o `Messenger` que foi criado na seção anterior:

```csharp
public override IBinder OnBind(Intent intent)
{
    Log.Debug(TAG, "OnBind");
    return messenger.Binder;
}
```

Depois que essas três etapas são realizadas, o serviço remoto pode ser considerado concluído.

## <a name="consuming-the-service"></a>Consumindo o serviço

Todos os clientes devem implementar um código para poder associar e consumir o serviço remoto. Conceitualmente, do ponto de vista do cliente, há poucas diferenças entre a associação de serviço local ou um serviço remoto. O cliente chama o `BindService` método, passando uma intenção explícita para identificar o serviço e um `IServiceConnection` que ajuda a gerenciar a conexão entre o cliente e o serviço.

Este trecho de código é um exemplo de como criar um **intenção explícita** para associação a um serviço remoto. A intenção deve identificar o pacote que contém o serviço e o nome do serviço. Uma maneira de definir essas informações é usar um `Android.Content.ComponentName` objeto e fornecer que para a intenção. Este trecho de código é um exemplo:  

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

Quando o serviço está associado, o `IServiceConnection.OnServiceConnected` é chamado de método e fornece um `IBinder` a um cliente. No entanto, o cliente não diretamente usará o `IBinder`. Em vez disso, ele instanciará uma `Messenger` objeto daquele `IBinder`. Este é o `Messenger` que o cliente usará para interagir com o serviço remoto.

A seguir está um exemplo de um muito básica `IServiceConnection` implementação que demonstra como um cliente deve lidar com a conexão e desconexão de um serviço. Observe que o `OnServiceConnected` método recebe e `IBinder`, e o cliente cria uma `Messenger` do `IBinder`:

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

Depois que a conexão de serviço e a intenção são criados, é possível que o cliente chamar `BindService` e iniciar o processo de associação:

```csharp
IServiceConnection serviceConnection = new TimestampServiceConnection(this);
BindActivity(serviceToStart, serviceConnection, Bind.AutoCreate);
```

Depois que o cliente tem associado com êxito para o serviço e o `Messenger` está disponível, é possível que o cliente envie `Messages` para o serviço.

## <a name="sending-messages-to-the-service"></a>Enviar mensagens para o serviço

Depois que o cliente está conectado e tem um `Messenger` do objeto, é possível se comunicar com o serviço por expedição `Message` objetos por meio de `Messenger`. Essa comunicação é unidirecional, o cliente envia a mensagem, mas nenhuma mensagem de retorno do serviço para o cliente. Nesse sentido, o `Message` é um mecanismo disparar e esquecer.

A melhor maneira de criar um `Message` objeto é usar o [ `Message.Obtain` ](https://developer.xamarin.com/api/type/Android.OS.Message/#Public%20Methods) método de fábrica. Esse método extrairá um `Message` objeto a partir de um pool global que é mantido pelo Android. `Message.Obtain` também tem alguns métodos sobrecarregados que permitem a `Message` objeto a ser inicializado com valores de parâmetros necessários para o serviço.  Uma vez o `Message` é instanciada, ela distribuída ao serviço chamando `Messenger.Send`. Este trecho de código é um exemplo de expedição e criando uma `Message` ao processo do serviço:

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

Há várias formas diferentes do `Message.Obtain` método. O exemplo anterior usa o [ `Message.Obtain(Handler h, Int32 what)` ](https://developer.xamarin.com/api/member/Android.OS.Message.Obtain/p/Android.OS.Handler/System.Int32/). Como esta é uma solicitação assíncrona para um serviço de fora do processo. não haverá nenhuma resposta do serviço, portanto, o `Handler` é definido como `null`. O segundo parâmetro, `Int32 what`, serão armazenados no `.What` propriedade o `Message` objeto. O `.What` pelo código no processo de serviço, a propriedade é usada para chamar métodos no serviço.

O `Message` classe também expõe duas propriedades adicionais que podem ser usados para o recipent: `Arg1` e `Arg2`. Essas duas propriedades são valores inteiros que podem ter algumas especial acordado entre os valores que têm significado entre o cliente e o serviço. Por exemplo, `Arg1` pode conter uma ID de cliente e `Arg2` pode manter um número de ordem de compra do cliente. O [ `Method.Obtain(Handler h, Int32 what, Int32 arg1, Int32 arg2)` ](https://developer.xamarin.com/api/member/Android.OS.Message.Obtain/p/Android.OS.Handler/System.Int32/System.Int32/System.Int32/) pode ser usado para definir as duas propriedades quando o `Message` é criado. Outra maneira de preencher esses dois valores é definir o `.Arg` e `.Arg2` propriedades diretamente no `Message` objeto após ele ter sido criado.

### <a name="passing-additional-values-to-the-service"></a>Passando valores adicionais para o serviço

É possível passar dados mais complexos para o serviço usando um `Bundle`. Nesse caso, os valores extras podem ser colocados em um `Bundle` e enviada junto com o `Message` definindo o [ `.Data` propriedade](https://developer.xamarin.com/api/property/Android.OS.Message.Data/) propriedade antes de enviar.

```csharp
Bundle serviceParameters = new Bundle();
serviceParameters.

var msg = Message.Obtain(null, Constants.SERVICE_TASK_TO_PERFORM);
msg.Data = serviceParameters;

messenger.Send(msg);
```


> [!NOTE]
> Em geral, um `Message` não deve ter uma carga maior que 1 MB. O limite de tamanho pode variar de acordo com a versão do Android e nas alterações proprietárias do fornecedor pode ter feito para sua implementação do Android Abrir fonte de projeto (AOSP) que é fornecido com o dispositivo.

## <a name="returning-values-from-the-service"></a>Retornando valores do serviço

A arquitetura de mensagens foi discutida neste ponto é unidirecional, o cliente envia uma mensagem para o serviço. Se for necessário para o serviço retornar um valor para um cliente, em seguida, tudo o que foi discutido neste ponto é revertido. O serviço deve criar um `Message`pacote, quaisquer valores de retorno e a expedição a `Message` por meio de um `Messenger` ao cliente. No entanto, o serviço não criar seu próprio `Messenger`; em vez disso, ele se baseia no cliente criando e pacote um `Messenger` como parte da solicitação inicial. O serviço será `Send` a mensagem usando nesse fornecidas pelo cliente `Messenger`.  

A sequência de eventos para a comunicação bidirecional é a seguinte:

1. O cliente se associa ao serviço. Quando o serviço e o cliente se conectar, o `IServiceConnection` que é mantido pelo cliente terá uma referência a um `Messenger` objeto que é usado para transmitir `Message`s para o serviço. Para evitar confusão, isso será referenciado como a _serviço Messenger_.
2. Cliente instancia um `Handler` (conhecido como o _manipulador de cliente_) e a usa para inicializar o seu próprio `Messenger` (o _cliente Messenger_). Observe que o serviço Messenger e o cliente Messenger são dois objetos diferentes que manipulam o tráfego nas duas direções diferentes. O serviço Messenger manipula mensagens do cliente para o serviço, enquanto o cliente Messenger tratará mensagens do serviço ao cliente.
3. O cliente cria uma `Message` objeto e define o `ReplyTo` propriedade com o cliente Messenger. A mensagem é enviada para o serviço usando o serviço Messenger.
4. O serviço recebe a mensagem do cliente e executa o trabalho solicitado.
5. Quando for hora para o serviço enviar a resposta ao cliente, ele usará `Message.Obtain` para criar um novo `Message` objeto.
6. Para enviar a mensagem para o cliente, o serviço será extraído o cliente do Messenger a `.ReplyTo` propriedade do cliente de mensagem e usá-la para `.Send` o `Message` volta ao cliente.
7. Quando a resposta é recebida pelo cliente, ele tem seu próprio `Handler` que processará o `Message` inspecionando o `.What` propriedade (e, se necessário, extrair os parâmetros contidos a `Message`).

Esse código de exemplo demonstra como o cliente instanciará o `Message` e empacotar um `Messenger` que o serviço deve usar para sua resposta:

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

O serviço deve fazer algumas alterações ao seu próprio `Handler` para extrair o `Messenger` e usá-lo para enviar respostas ao cliente. Este trecho de código é um exemplo de como o serviço `Handler` criaria um `Message` e enviá-lo para o cliente:  

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

Observe que os exemplos de código acima, o `Messenger` instância que é criada pelo cliente for *não* o mesmo objeto que é recebido pelo serviço. Estes são dois `Messenger` objetos em execução em dois processos separados que representam o canal de comunicação.

## <a name="securing-the-service-with-android-permissions"></a>Protegendo o serviço com permissões de Android

Um serviço executado em um processo global está acessível por todos os aplicativos em execução no dispositivo Android. Em algumas situações, abertura e a disponibilidade é desejável, e é necessário proteger o serviço contra o acesso de clientes não autorizados. Uma maneira para limitar o acesso ao serviço remoto é usar permissões Android.

Permissões podem ser identificadas pelo `Permission` propriedade o `ServiceAttribute` que decora o `Service` subclasse. Isso irá nomear uma permissão que o cliente deve ser concedido ao ligar para o serviço. Se o cliente não tem as permissões apropriadas, Android lançará um `Java.Lang.SecurityException` quando o cliente tenta associar ao serviço.

Há quatro níveis de permissão diferentes Android fornece:

* **normal** &ndash; este é o nível de permissão padrão. Ele é usado para identificar as permissões de baixo risco que podem ser concedidas automaticamente pelo Android para clientes que solicitam. O usuário não precisa conceder explicitamente essas permissões, mas as permissões podem ser exibidas nas configurações do aplicativo.
* **assinatura** &ndash; isso é uma categoria especial de permissão que será concedido automaticamente pelo Android para os aplicativos assinados com o mesmo certificado. Essa permissão é projetada para tornar fácil para um desenvolvedor de aplicativos compartilhar dados entre seus aplicativos sem incomodar o usuário para aprovações de constantes ou componentes.
* **signatureOrSystem** &ndash; isso é muito semelhante do **assinatura** permissões descritas acima. Além de ser automaticamente concedida aos aplicativos que são assinados pelo mesmo certificado, essa permissão também receberá a aplicativos que são assinados o mesmo certificado que foi usado para assinar os aplicativos instalados com a imagem do sistema Android. Essa permissão normalmente é usada somente por desenvolvedores do Android ROM para permitir que seus aplicativos trabalhar com aplicativos de terceiros. Ele não costuma ser usado por aplicativos que são destinados a distribuição geral para o público em geral.
* **perigosas** &ndash; permissões perigosas são aqueles que podem causar problemas para o usuário. Por esse motivo, **perigosas** permissões devem ser aprovadas explicitamente pelo usuário.

Porque `signature` e `normal` permissões são automaticamente concedidas ao tempo instalado pelo Android, é crucial que APK que hospeda o serviço seja instalado **antes de** o APK que contém o cliente. Se o cliente é instalado pela primeira vez, o Android não conceder as permissões. Nesse caso, será necessário desinstalar o cliente APK, instalar o serviço APK e, em seguida, reinstalar o cliente APK.

Há duas maneiras de proteger um serviço com permissões Android:

1.  **Implementar a segurança em nível de assinatura** &ndash; segurança em nível de assinatura significa que é automaticamente permissão para os aplicativos que são assinados com a mesma chave que foi usada para assinar o APK mantendo o serviço. Essa é uma maneira simple para os desenvolvedores a proteger seu serviço, mas mantê-los acessíveis a partir de seus próprios aplicativos. Permissões em nível de assinatura são declaradas, definindo o `Permission` propriedade o `ServiceAttribute` para `signature`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.TimestampService.timestampservice_process",
             Permission="signature")]
    public class TimestampService : Service
    {
    }
    ```

2.  **Criar uma permissão personalizada** &ndash; é possível que o desenvolvedor do serviço criar uma permissão personalizada para o serviço. Isso é ideal para quando um desenvolvedor deseja compartilhar seus serviços com aplicativos de outros desenvolvedores. Uma permissão personalizada requer um pouco mais esforço para implementar e será abordado abaixo.

Um exemplo simplificado de criar um personalizado `normal` permissão será descrita na próxima seção. Para obter mais informações sobre permissões Android, consulte a documentação do Google para [práticas recomendadas de & segurança](https://developer.android.com/training/articles/security-tips.html). Para obter mais informações sobre permissões Android, consulte o [seção permissões](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms) da documentação do Android para o manifesto do aplicativo para obter mais informações sobre permissões Android.

> [!NOTE]
> Em geral, [Google desencoraja o uso de permissões personalizadas](https://developer.android.com/training/articles/security-tips.html#RequestingPermissions) como eles podem ser confusos para os usuários.

### <a name="creating-a-custom-permission"></a>Criar uma permissão personalizada

Para usar uma permissão personalizada, é declarada com o serviço enquanto o cliente solicite explicitamente essa permissão.

Para criar uma permissão no serviço APK, um `permission` elemento é adicionado para o `manifest` elemento em **AndroidManifest.xml**. Essa permissão deve ter o `name`, `protectionLevel`, e `label` conjunto de atributos. O `name` atributo deve ser definido como uma cadeia de caracteres que identifica exclusivamente a permissão. O nome será exibido no **App Info** exibir o **configurações do Android** (conforme mostrado na próxima seção).

O `protectionLevel` atributo deve ser definido como um dos valores de cadeia de caracteres de quatro que foram descritos acima.  O `label` e `description` devem se referir aos recursos de cadeia de caracteres e são usados para fornecer um nome amigável e uma descrição para o usuário.

Este trecho de código é um exemplo de como declarar um personalizado `permission` atributo **AndroidManifest.xml** do APK que contém o serviço:

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

Em seguida, o **AndroidManifest.xml** do cliente APK deve solicitar explicitamente essa nova permissão. Isso é feito adicionando o `users-permission` de atributo para o **AndroidManifest.xml**:

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

Para exibir as permissões concedidas a um aplicativo, abra o aplicativo de configurações do Android e selecione **aplicativos**. Localize e selecione o aplicativo na lista. Do **App Info** tela, toque em **permissões** que abrirá uma exibição que mostra todas as permissões concedidas ao aplicativo:

[![Capturas de tela de um dispositivo Android mostrando como localizar as permissões concedidas a um aplicativo](out-of-process-services-images/ipc-06-sml.png)](out-of-process-services-images/ipc-06.png#lightbox)

## <a name="summary"></a>Resumo

Este guia foi uma discussão avançada sobre como executar um serviço Android em um processo remoto. As diferenças entre um local e um serviço remoto foi explicado, juntamente com alguns motivos por que um serviço remoto pode ser útil para a estabilidade e desempenho de um aplicativo do Android. Depois de explicar como implementar um serviço remoto e como um cliente pode se comunicar com o serviço, o guia entrou em para fornecer uma maneira para limitar o acesso ao serviço de somente clientes autorizados.


## <a name="related-links"></a>Links relacionados

- [Handler](https://developer.xamarin.com/api/type/Android.OS.Handler/)
- [Message](https://developer.xamarin.com/api/type/Android.OS.Message/)
- [Messenger](https://developer.xamarin.com/api/type/Android.OS.Messenger/)
- [ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute)
- [O atributo exportados](https://developer.android.com/guide/topics/manifest/service-element.html#exported)
- [Os serviços com processos isolados e a classe de aplicativo personalizado não resolver sobrecargas corretamente](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)
- [Processos e Threads](https://developer.android.com/guide/components/processes-and-threads.html)
- [Manifesto do Android - permissões](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)
- [Dicas de segurança](https://developer.android.com/training/articles/security-tips.html)
- [MessengerServiceDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/MessengerServiceDemo/)
