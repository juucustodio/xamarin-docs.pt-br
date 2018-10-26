---
title: Serviços do Android em execução em processos remotos
description: Em geral, todos os componentes em um aplicativo Android serão executado no mesmo processo. Serviços do Android são uma exceção notável para isso pode ser configurados para ser executado em seus próprios processos e compartilhados com outros aplicativos, incluindo aqueles de outros desenvolvedores do Android. Este guia discute como criar e usar um serviço remoto do Android usando o Xamarin.
ms.prod: xamarin
ms.assetid: 27A2E972-A690-480B-B31D-5EF1F74F673C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 3a11fe56da6076ba53e97643e6890f0fa14b8036
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113931"
---
# <a name="running-android-services-in-remote-processes"></a>Serviços do Android em execução em processos remotos

_Em geral, todos os componentes em um aplicativo Android serão executado no mesmo processo. Serviços do Android são uma exceção notável para isso pode ser configurados para ser executado em seus próprios processos e compartilhados com outros aplicativos, incluindo aqueles de outros desenvolvedores do Android. Este guia discute como criar e usar um serviço remoto do Android usando o Xamarin._

## <a name="out-of-process-services-overview"></a>Fora de visão geral dos serviços de processo

Quando um aplicativo é iniciado, o Android cria um processo no qual executar o aplicativo. Normalmente, todos os componentes o aplicativo será executado nesse processo um. Serviços do Android são uma exceção notável para isso pode ser configurados para ser executado em seus próprios processos e compartilhados com outros aplicativos, incluindo aqueles de outros desenvolvedores do Android. Esses tipos de serviços são denominados _serviços remotos_ ou _serviços de out-of-process_. O código para esses serviços estarão contido no APK mesmo que o aplicativo principal; No entanto, quando o serviço é iniciado Android criará um novo processo apenas desse serviço. Por outro lado, um serviço que é executado no mesmo processo que o restante do aplicativo é, às vezes, chamado como uma _serviço local_.

Em geral, não é necessário para um aplicativo implementar um serviço remoto. Um serviço local é suficiente (e desejável) para obter os requisitos do aplicativo em muitos casos. Um limite de processo tem seu próprio espaço de memória que deve ser gerenciado pelo Android. Embora isso introduz mais sobrecarga para o aplicativo em geral, há alguns cenários em que pode ser vantajoso para executar um serviço em seu próprio processo:

1. **Funcionalidade de compartilhamento** &ndash; alguns desenvolvedores de aplicativo podem ter vários aplicativos e funcionalidade que é compartilhada entre todos os aplicativos. Empacotar essa funcionalidade em um serviço do qual é executado em seu próprio processo pode simplificar a manutenção do aplicativo Android. Também é possível empacotar o serviço em seu próprio APK autônomo e implantá-lo separadamente do restante do aplicativo.
2. **Melhorando a experiência do usuário** &ndash; há duas maneiras possíveis que um serviço fora de processo pode melhorar a experiência do usuário do aplicativo. A primeira maneira é lida com gerenciamento de memória. Quando uma coleta de lixo (GC) ciclo ocorre Android fará uma pausa todas as atividades do processo até que o GC seja concluído. O usuário poderá perceber essa pausa como um "repetitivo" ou "jank". Quando um serviço está em execução é o próprio processo, é o processo de serviço que está em pausa, não o processo do aplicativo. Essa pausa será menos perceptível ao usuário como o processo do aplicativo (e, portanto, a interface do usuário) não está em pausa.

    Em segundo lugar, se os requisitos de memória de um processo ficar muito grande, o Android pode eliminar esse processo para liberar recursos para o dispositivo. Se um serviço tiver um grande volume de memória, e ele está em execução no mesmo processo que a interface do usuário, em seguida, quando Android forçadamente recupera esses recursos a interface do usuário será desligada, forçar o usuário para iniciar o aplicativo. No entanto, se um serviço em execução em seu próprio processo for encerrado com o Android, o processo de interface do usuário permanece afetado. A interface do usuário pode associar (e reiniciar) o serviço, transparente para o usuário e retomar o funcionamento normal.

3. **Melhorando o desempenho do aplicativo** &ndash; processo a interface do usuário pode ser encerrado ou desligar independente do processo de serviço. Movendo tarefas de inicialização longos para um serviço fora de processo, é possível que o tempo de inicialização da interface do usuário aprimorada talvez (supondo que o processo de serviço é mantido ativo entre os tempos de interface do usuário é iniciado).

Em muitos aspectos, associação a um serviço em execução em outro processo é o mesmo que [associando a um serviço local](~/android/app-fundamentals/services/creating-a-service/bound-services.md). O cliente irá chamar `BindService` ligar (e iniciar, se necessário) o serviço. Um `Android.OS.IServiceConnection` objeto será criado para gerenciar a conexão entre o cliente e o serviço. Se o cliente é associado com êxito para o serviço, o Android retornará um objeto por meio de `IServiceConnection` que pode ser usado para invocar métodos no serviço. O cliente, em seguida, interage com o serviço de uso do objeto. Para revisar, aqui estão as etapas para associar a um serviço:

* **Criar uma intenção** &ndash; uma intenção explícita deve ser usada para associação ao serviço.
* **Implemente e instanciar uma `IServiceConnection` objeto** &ndash; o `IServiceConnection` objeto atua como um intermediário entre o cliente e o serviço.  Ele é responsável por monitorar a conexão entre cliente e servidor.
* **Invocar o `BindService` método** &ndash; chamando `BindService` enviará a intenção e a conexão de serviço criado nas etapas anteriores para Android, que se encarregará de iniciar o serviço e estabelecer a comunicação entre cliente e serviço.

A necessidade de cruzar os limites do processo introduzem complexidade adicional: a comunicação é unidirecional (cliente para servidor) e o cliente não pode invocar diretamente os métodos na classe de serviço. Lembre-se de que quando um serviço está executando o mesmo processo que o cliente, o Android fornece um `IBinder` objeto que pode permitir a comunicação bidirecional. Isso não é o caso com o serviço está em execução em seu próprio processo. Um cliente se comunica com um serviço remoto com a Ajuda do `Android.OS.Messenger` classe.

Quando um cliente solicita para associar com o serviço remoto, o Android invocará o `Service.OnBind` método de ciclo de vida, que retornará o interno `IBinder` objeto encapsulado pelo `Messenger`. O `Messenger` é um wrapper fino em um especial `IBinder` implementação fornecida pelo SDK do Android. O `Messenger` se encarrega da comunicação entre os dois processos diferentes. O desenvolvedor é preocupado com os detalhes de serializar uma mensagem, marshalling a mensagem além do limite de processo e, em seguida, desserializá-lo no cliente. Esse trabalho é tratado pelo `Messenger` objeto. Este diagrama mostra os componentes de Android do lado do cliente que são envolvidos quando um cliente inicia a associação a um serviço fora de processo:

![Diagrama que mostra as etapas e os componentes de uma associação a um serviço de cliente](out-of-process-services-images/ipc-01.png "diagrama que mostra as etapas e os componentes de uma associação a um serviço de cliente.")

O `Service` classe no processo remoto revisará os retornos de chamada do ciclo de vida mesmo que um serviço vinculado em um processo local passará por, e muitas das APIs envolvidas são os mesmos. `Service.OnCreate` é usado para inicializar uma `Handler` e inseri-los em `Messenger` objeto. Da mesma forma, `OnBind` é substituída, mas em vez de retornar um `IBinder` objeto, o serviço retornará o `Messenger`.  Este diagrama ilustra o que acontece no serviço, quando um cliente está se associando a ele:

![Diagrama que mostra as etapas e os componentes de um serviço passa por quando ser associado a por um cliente remoto](out-of-process-services-images/ipc-02.png "diagrama que mostra as etapas e os componentes de um serviço passa por quando ser associado a por um cliente remoto.")

Quando um `Message` é recebido por um serviço, ela é processada na instância do `Android.OS.Handler`. O serviço irá implementar seu próprio `Handler` subclasse deve substituir o `HandleMessage` método. Este método é chamado pelo `Messenger` e recebe o `Message` como um parâmetro. O `Handler` inspecionará o `Message` metadados e usar essas informações para invocar métodos no serviço.

A comunicação unidirecional ocorre quando um cliente cria uma `Message` do objeto e a envia ao serviço usando o `Messenger.Send` método. `Messenger.Send` serializa o `Message` e mão que dados serializados para Android, que irá rotear a mensagem além do limite de processo e o serviço.  O `Messenger` que é hospedado pelo serviço criará um `Message` objeto de dados de entrada. Isso `Message` é colocado em uma fila, onde as mensagens são enviadas uma de cada vez para o `Handler`. O `Handler` inspecionará os metadados contidos na `Message` e chamar os métodos apropriados na `Service`. O diagrama a seguir ilustra esses vários conceitos em ação:

![Diagrama mostrando como as mensagens são passadas entre processos](out-of-process-services-images/ipc-03.png "diagrama que mostra como as mensagens são passadas entre processos.")

Este guia discute os detalhes de implementação de um serviço fora de processo. Ele abordará como implementar um serviço que deve ser executado em seu próprio processo e como um cliente pode se comunicar com esse serviço usando o `Messenger` framework. Ele também brevemente abordará a comunicação bidirecional: o cliente enviar uma mensagem para um serviço e o serviço enviando uma mensagem de volta ao cliente. Porque os serviços podem ser compartilhados entre aplicativos diferentes, este guia também abordará uma técnica para limitar o acesso de cliente para o serviço usando as permissões do Android.

> [!IMPORTANT]
> [Bugzilla 51940 - serviços com processos isolados e classe personalizada de aplicativo não conseguir resolver sobrecargas corretamente](https://bugzilla.xamarin.com/show_bug.cgi?id=51940) relatórios de um serviço de xamarin. Android não será iniciado corretamente quando o `IsolatedProcess` é definido como `true`. Este guia é fornecido para uma referência. Um aplicativo xamarin. Android deve ainda ser capaz de se comunicar com um serviço de fora do processo é escrito em Java.

## <a name="requirements"></a>Requisitos

Este guia presume familiaridade com a criação de serviços.

Embora seja possível usar intenções implícitas com aplicativos que se destinam mais antigo APIs do Android, este guia se concentra exclusivamente no uso de intenções explícitas. Aplicativos destinados ao Android 5.0 (API nível 21) ou superior deve usar uma intenção explícita para associar com um serviço; Essa é a técnica que será demonstrada neste guia...

## <a name="create-a-service-that-runs-in-a-separate-process"></a>Criar um serviço que é executado em um processo separado

Conforme descrito acima, o fato de que um serviço é executado em seu próprio processo significa que algumas APIs diferentes estão envolvidos. Como uma visão geral, aqui estão as etapas para associar com e consumir um serviço remoto:  

* **Criar o `Service` subclasse** &ndash; subclasse o `Service` de tipo e implemente os métodos de ciclo de vida para um serviço vinculado. Também é necessário definir os metadados que informarão Android que o serviço é executado em seu próprio processo.
* **Implementar uma `Handler`**  &ndash; o `Handler` é responsável por analisar as solicitações do cliente, extrair todos os parâmetros que foram passados do cliente e invocar os métodos apropriados no serviço.
* **Criar uma instância de um `Messenger`**  &ndash; conforme descrito acima, cada `Service` deve manter uma instância da `Messenger` classe que irá rotear solicitações de cliente para o `Handler` que foi criado na etapa anterior.

Um serviço que deve ser executado em seu próprio processo, fundamentalmente, ainda é um serviço vinculado. A classe de serviço se estenderão a base `Service` classe e é decorado com o `ServiceAttribute` que contém os metadados que Android precisa do pacote no manifesto do Android. Comece com as seguintes propriedades do `ServiceAttribute` que são importantes para um serviço fora de processo:

1. `Exported` &ndash; Essa propriedade deve ser definida como `true` para permitir que outros aplicativos interagem com o serviço. O valor padrão dessa propriedade é `false`.
2. `Process` &ndash; Essa propriedade deve ser definida. Ele é usado para especificar o nome do processo que o serviço será executado no.
3. `IsolatedProcess` &ndash; Essa propriedade será habilitar segurança extra, informando ao Android para executar o serviço em uma área restrita isolada com a permissão mínima para iteract com o restante do sistema. Ver [Bugzilla 51940 - serviços com processos isolados e classe personalizada de aplicativo não conseguir resolver sobrecargas corretamente](https://bugzilla.xamarin.com/show_bug.cgi?id=51940).
4. `Permission` &ndash; É possível controlar o acesso de cliente para o serviço especificando uma permissão que os clientes devem solicitar (e ser concedidos).

Para executar um serviço em seu próprio processo, o `Process` propriedade no `ServiceAttribute` deve ser definido como o nome do serviço. Para interagir com aplicativos externos, o `Exported` propriedade deve ser definida como `true`. Se `Exported` é `false`, somente clientes no mesmo APK (ou seja, o mesmo aplicativo) e em execução no mesmo processo poderão interagir com o serviço.

Que tipo de processo que o serviço será executado em depende do valor da `Process` propriedade. Android identifica três tipos diferentes de processos:

-   **Processo privada** &ndash; um processo privado é aquela que está disponível apenas para o aplicativo que o iniciou. Para identificar um processo como particular, seu nome deve começar com uma **:** (vírgula). O serviço representado no trecho de código anterior e a captura de tela é um processo privado. O trecho de código a seguir está um exemplo de como o `ServiceAttribute`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process=":timestampservice_process",
             Exported=true)]
    ```

-   **Processo global** &ndash; um serviço que é executado em um processo global está acessível a todos os aplicativos em execução no dispositivo. Um processo global deve ser um nome de classe totalmente qualificado que começa com um caractere minúsculo.
    (A menos que medidas sejam tomadas para proteger o serviço, outros aplicativos podem associar e interagir com ele. Protegendo o serviço contra uso não autorizado será abordada neste documento.)

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

-   **Isolado de processo** &ndash; um processo isolado é um processo que é executado em sua própria área restrita, isolada do restante do sistema e com nenhuma permissão especial de seu próprio. Para executar um serviço em um processo isolado, o `IsolatedProcess` propriedade do `ServiceAttribute` é definido como `true` conforme mostrado neste trecho de código:
    
    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             IsolatedProcess= true,
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

> [!IMPORTANT]
> Consulte [Bugzilla 51940 - serviços com processos isolados e classe personalizada de aplicativo não conseguir resolver sobrecargas corretamente](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)

Um serviço isolado é uma maneira simples de proteger um aplicativo e o dispositivo em relação ao código não confiável. Por exemplo, um aplicativo pode baixar e executar um script de um site. Nesse caso, executar isso em um processo isolado fornece uma camada adicional de segurança contra código não confiável comprometer o dispositivo Android.

> [!IMPORTANT]
> Depois que um serviço tiver sido exportado, não deve alterar o nome do serviço. Alterando o nome do serviço pode interromper outros aplicativos que estão usando o serviço.

Para ver o efeito que o `Process` tem propriedade, a captura de tela a seguir mostra um serviço em execução em seu próprio processo privado:

![Captura de tela que mostra um serviço em execução em um processo particular](out-of-process-services-images/ipc-04.png "captura de tela que mostra um serviço em execução em um processo em particular.")

Esta próxima captura de tela mostra `Process="com.xamarin.xample.messengerservice.timestampservice_process"` e o serviço em execução em um processo global:

![Captura de tela de um serviço em execução em um processo global](out-of-process-services-images/ipc-05.png "captura de tela de um serviço em execução em um processo global.")

Uma vez a `ServiceAttribute` tiver sido definido, o serviço precisa implementar um `Handler`.

### <a name="implementing-a-handler"></a>Implementar um manipulador

Para processar solicitações de cliente, o serviço deve implementar uma `Handler` e substitua o `HandleMessage` methodThis é o método usa um `Message` quais que encapsula a chamada de método do cliente e converte que chamam alguma ação de instância ou tarefa que executará o serviço. O `Message` objeto expõe uma propriedade chamada `What` que é um valor inteiro, o significado dos quais é compartilhado entre o cliente e o serviço e se relaciona com alguma tarefa que o serviço está para executar o cliente.

O seguinte trecho de código do aplicativo de exemplo mostra um exemplo de `HandleMessage`. Neste exemplo, há duas ações que o cliente pode solicitar do serviço:

* A primeira ação é um _Olá, mundo_ mensagem, o cliente enviou uma mensagem simples para o serviço.
* A segunda ação irá invocar um método no serviço e recuperar uma cadeia de caracteres, nesse caso, a cadeia de caracteres é uma mensagem que retorna a que horas o serviço iniciado e por quanto tempo que ele esteve em execução:

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

Também é possível para os parâmetros de pacote para o serviço no `Message`. Isso será discutido neste documento. O próximo tópico a considerar é a criação de `Messenger` objeto para processar a entrada `Message`s.

### <a name="instantiating-the-messenger"></a>Criando uma instância do Messenger

Conforme discutido anteriormente, ao desserializar o `Message` objeto e invoca `Handler.HandleMessage` é a responsabilidade do `Messenger` objeto. O `Messenger` classe também fornece um `IBinder` que o cliente usará para enviar mensagens para o serviço do objeto.  

Quando o serviço é iniciado, ele criará uma instância de `Messenger` e injetar o `Handler`. Um bom lugar para realizar essa inicialização é sobre o `OnCreate` método do serviço. Este trecho de código é um exemplo de um serviço que inicializa sua própria `Handler` e `Messenger`:

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

### <a name="implementing-serviceonbind"></a>Implementando Service.OnBind

Todos os serviços associados, estejam eles sendo executados em seu próprio processo ou não, devem implementar o `OnBind` método. O valor de retorno desse método é um objeto que o cliente pode usar para interagir com o serviço. Exatamente o que esse objeto é depende se o serviço é um serviço local ou um serviço remoto. Enquanto um serviço local retornará um personalizado `IBinder` implementação, um serviço remoto retornará a `IBinder` que é encapsulado, mas o `Messenger` que foi criado na seção anterior:

```csharp
public override IBinder OnBind(Intent intent)
{
    Log.Debug(TAG, "OnBind");
    return messenger.Binder;
}
```

Depois que essas três etapas são realizadas, o serviço remoto pode ser considerado concluído.

## <a name="consuming-the-service"></a>Consumindo o serviço

Todos os clientes devem implementar algum código para que seja possível associar e consumir o serviço remoto. Conceitualmente, do ponto de vista do cliente, há poucas diferenças entre associação de serviço local ou um serviço remoto. O cliente invoca o `BindService` método, passando uma intenção explícita de identificar o serviço e um `IServiceConnection` que ajuda a gerenciar a conexão entre o cliente e o serviço.

Este trecho de código é um exemplo de como criar uma **intenção explícita** para associação a um serviço remoto. A intenção deve identificar o pacote que contém o serviço e o nome do serviço. Uma forma de definir essas informações é usar um `Android.Content.ComponentName` objeto e para fornecê-lo à intenção. Este trecho de código é um exemplo:  

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

Quando o serviço está associado, o `IServiceConnection.OnServiceConnected` é chamado de método e fornece um `IBinder` para um cliente. No entanto, o cliente não diretamente usará o `IBinder`. Em vez disso, ele instanciará uma `Messenger` objeto daquele `IBinder`. Esse é o `Messenger` que o cliente usará para interagir com o serviço remoto.

A seguir está um exemplo de um bem básico `IServiceConnection` implementação que demonstra como um cliente lidaria com a conexão e desconexão de um serviço. Observe que o `OnServiceConnected` método recebe e `IBinder`, e o cliente cria um `Messenger` daquele `IBinder`:

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

Depois de criar a conexão de serviço e a intenção, é possível que o cliente chame `BindService` e iniciar o processo de associação:

```csharp
IServiceConnection serviceConnection = new TimestampServiceConnection(this);
BindActivity(serviceToStart, serviceConnection, Bind.AutoCreate);
```

Depois que o cliente foi associada corretamente para o serviço e o `Messenger` está disponível, é possível que o cliente envie `Messages` para o serviço.

## <a name="sending-messages-to-the-service"></a>Enviar mensagens para o serviço

Depois que o cliente está conectado e tem um `Messenger` do objeto, é possível se comunicar com o serviço por expedição `Message` objetos por meio de `Messenger`. Essa comunicação é unidirecional, o cliente envia a mensagem, mas não há nenhuma mensagem de retornada do serviço ao cliente. Nesse sentido, o `Message` é um mecanismo de disparar e esquecer.

A maneira preferencial para criar uma `Message` objeto é usar o [ `Message.Obtain` ](https://developer.xamarin.com/api/type/Android.OS.Message/#Public%20Methods) método de fábrica. Esse método efetuará o pull um `Message` objeto a partir de um pool global que é mantido pelo Android. `Message.Obtain` também tem alguns métodos sobrecarregados que permitem que o `Message` objeto a ser inicializado com os valores e parâmetros exigidos pelo serviço.  Uma vez a `Message` é instanciado, ele despachada para o serviço chamando `Messenger.Send`. Este trecho de código é um exemplo de criação e expedir uma `Message` ao processo do serviço:

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

Há várias formas diferentes da `Message.Obtain` método. O exemplo anterior usa o [ `Message.Obtain(Handler h, Int32 what)` ](https://developer.xamarin.com/api/member/Android.OS.Message.Obtain/p/Android.OS.Handler/System.Int32/). Como esta é uma solicitação assíncrona para um serviço de out-of-process. não haverá nenhuma resposta do serviço, portanto, o `Handler` é definido como `null`. O segundo parâmetro, `Int32 what`, serão armazenados em de `.What` propriedade do `Message` objeto. O `.What` propriedade é usada pelo código no processo de serviço para invocar métodos no serviço.

O `Message` classe também expõe duas propriedades adicionais que podem ser usados para o nome: `Arg1` e `Arg2`. Essas duas propriedades são valores inteiros que podem ter alguns especial acordados valores que têm significado entre o cliente e o serviço. Por exemplo, `Arg1` pode conter uma ID de cliente e `Arg2` pode manter um número de ordem de compra do cliente. O [ `Method.Obtain(Handler h, Int32 what, Int32 arg1, Int32 arg2)` ](https://developer.xamarin.com/api/member/Android.OS.Message.Obtain/p/Android.OS.Handler/System.Int32/System.Int32/System.Int32/) pode ser usado para definir as duas propriedades quando o `Message` é criado. Outra maneira de preencher esses dois valores é definir a `.Arg` e `.Arg2` propriedades diretamente no `Message` após ele ter sido criado do objeto.

### <a name="passing-additional-values-to-the-service"></a>Passar valores adicionais para o serviço

É possível passar dados mais complexos para o serviço usando um `Bundle`. Nesse caso, os valores extras podem ser colocados em um `Bundle` e enviada junto com o `Message` definindo o [ `.Data` propriedade](https://developer.xamarin.com/api/property/Android.OS.Message.Data/) propriedade antes de enviar.

```csharp
Bundle serviceParameters = new Bundle();
serviceParameters.

var msg = Message.Obtain(null, Constants.SERVICE_TASK_TO_PERFORM);
msg.Data = serviceParameters;

messenger.Send(msg);
```


> [!NOTE]
> Em geral, um `Message` não deve ter uma carga maior que 1 MB. O limite de tamanho pode variar de acordo com a versão do Android e quaisquer alterações de proprietário, o fornecedor pode ter feito a suas implementações do Android Abrir fonte de projeto (AOSP) que é fornecido com o dispositivo.

## <a name="returning-values-from-the-service"></a>Retornar valores de serviço

A arquitetura de mensagens que foi discutida até o momento é unidirecional, o cliente envia uma mensagem para o serviço. Se for necessário para o serviço retornar um valor para um cliente, em seguida, tudo o que foi discutido até o momento é revertido. O serviço deve criar uma `Message`pacote, quaisquer valores de retorno e a expedição a `Message` por meio de um `Messenger` ao cliente. No entanto, o serviço não criar seu próprio `Messenger`; em vez disso, ele se baseia no cliente instanciando e pacote um `Messenger` como parte da solicitação inicial. O serviço irá `Send` a mensagem usando nesse fornecido pelo cliente `Messenger`.  

A sequência de eventos para a comunicação bidirecional é esta:

1. O cliente é associado ao serviço. Quando o serviço e o cliente se conectar, o `IServiceConnection` que é mantido pelo cliente terá uma referência a um `Messenger` objeto que é usado para transmitir `Message`s para o serviço. Para evitar confusão, isso será referenciado como o _serviço de Mensageiro_.
2. Cria uma instância do cliente uma `Handler` (conhecido como o _manipulador de cliente_) e usa isso para inicializar sua própria `Messenger` (o _cliente Messenger_). Observe que o serviço Messenger e o Messenger do cliente são dois objetos diferentes que lidar com o tráfego em duas direções diferentes. O serviço Messenger lida com mensagens do cliente para o serviço, enquanto o Messenger cliente irá manipular mensagens do serviço ao cliente.
3. O cliente cria uma `Message` objeto e define o `ReplyTo` propriedade com o Messenger do cliente. A mensagem é enviada para o serviço usando o serviço Messenger.
4. O serviço recebe a mensagem do cliente e executa o trabalho solicitado.
5. Quando chegou a hora para o serviço enviar a resposta ao cliente, ele usará `Message.Obtain` para criar um novo `Message` objeto.
6. Para enviar esta mensagem para o cliente, o serviço irá extrair o cliente do Messenger a `.ReplyTo` propriedade do cliente de mensagem e usá-lo para `.Send` o `Message` volta ao cliente.
7. Quando a resposta é recebida pelo cliente, ele tem seu próprio `Handler` que processará a `Message` inspecionando o `.What` propriedade (e se necessário, extrair os parâmetros contidos pelo `Message`).

Esse código de exemplo demonstra como o cliente instanciará o `Message` e empacote um `Messenger` que o serviço deve usar para sua resposta:

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

O serviço deve fazer algumas alterações para seu próprio `Handler` para extrair o `Messenger` e usá-lo para enviar respostas para o cliente. Este trecho de código é um exemplo de como o serviço `Handler` criaria um `Message` e enviá-lo de volta para o cliente:  

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

Observe que, nos exemplos de código acima, o `Messenger` é de instância que é criada pelo cliente *não* o mesmo objeto que é recebido pelo serviço. Essas são duas diferentes `Messenger` objetos que são executados em dois processos separados que representam o canal de comunicação.

## <a name="securing-the-service-with-android-permissions"></a>Protegendo o serviço com as permissões do Android

Um serviço executado em um processo global está acessível por todos os aplicativos em execução em que o dispositivo Android. Em algumas situações, abertura e a disponibilidade é indesejável e é necessário proteger o serviço contra o acesso de clientes não autorizados. Uma maneira de limitar o acesso ao serviço remoto é usar as permissões do Android.

As permissões podem ser identificadas pela `Permission` propriedade do `ServiceAttribute` que decora o `Service` subclasse. Isso irá nomear uma permissão que o cliente deve ser concedido ao ligar para o serviço. Se o cliente não tem as permissões apropriadas, o Android lançará um `Java.Lang.SecurityException` quando o cliente tenta associar-se ao serviço.

Há quatro diferentes níveis de permissão que o Android fornece:

* **normal** &ndash; este é o nível de permissão padrão. Ele é usado para identificar as permissões de baixo risco que podem ser concedidas automaticamente pelo Android para clientes que solicitam a ele. O usuário não precisa conceder explicitamente a essas permissões, mas as permissões podem ser exibidas nas configurações do aplicativo.
* **assinatura** &ndash; isso é uma categoria especial de permissão será concedida automaticamente pelo Android a aplicativos que são assinados com o mesmo certificado. Essa permissão foi projetada para torná-lo facilmente para um desenvolvedor de aplicativos compartilhar dados entre seus aplicativos sem incomodar o usuário para aprovações de constante ou componentes.
* **signatureOrSystem** &ndash; isso é muito semelhante para o **assinatura** permissões descritas acima. Além de ser automaticamente concedido a aplicativos que são assinados pelo mesmo certificado, essa permissão também receberá a aplicativos que são assinados o mesmo certificado que foi usado para assinar os aplicativos instalados com a imagem do sistema Android. Essa permissão normalmente é usada somente por desenvolvedores de Android ROM para permitir que seus aplicativos para trabalhar com aplicativos de terceiros. Ele não é comumente usado por aplicativos que destinam-se a distribuição geral para o público em geral.
* **perigoso** &ndash; permissões perigosas são aquelas que podem causar problemas para o usuário. Por esse motivo, **perigoso** permissões devem ser explicitamente aprovadas pelo usuário.

Porque `signature` e `normal` permissões são automaticamente concedidas ao tempo instalado pelo Android, é crucial que o APK que hospeda o serviço seja instalado **antes de** o APK que contém o cliente. Se o cliente é instalado pela primeira vez, o Android não concede as permissões. Nesse caso, será necessário desinstalar o cliente do APK, instalar o serviço de APK e, em seguida, reinstale o cliente do APK.

Há duas maneiras de proteger um serviço com as permissões do Android:

1.  **Implementar a segurança em nível de assinatura** &ndash; segurança em nível de assinatura significa que é automaticamente permissão para os aplicativos que são assinados com a mesma chave que foi usada para assinar o APK que contém o serviço. Essa é uma maneira simple para desenvolvedores de proteger seu serviço e ainda mantê-los acessíveis a partir de seus próprios aplicativos. Permissões de nível de assinatura são declaradas, definindo o `Permission` propriedade do `ServiceAttribute` para `signature`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.TimestampService.timestampservice_process",
             Permission="signature")]
    public class TimestampService : Service
    {
    }
    ```

2.  **Criar uma permissão personalizada** &ndash; é possível que o desenvolvedor do serviço criar uma permissão personalizada para o serviço. Isso é melhor para quando um desenvolvedor deseja compartilhar seu serviço com aplicativos de outros desenvolvedores. Uma permissão personalizada requer um pouco mais de esforço para implementar e será abordado abaixo.

Um exemplo simplificado de criar um personalizado `normal` permissão será descrita na próxima seção. Para obter mais informações sobre as permissões do Android, consulte a documentação do Google para [práticas recomendadas de & segurança](https://developer.android.com/training/articles/security-tips.html). Para obter mais informações sobre as permissões do Android, consulte o [seção permissões](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms) da documentação do Android para o manifesto do aplicativo para obter mais informações sobre as permissões do Android.

> [!NOTE]
> Em geral, [Google desencoraja o uso de permissões personalizadas](https://developer.android.com/training/articles/security-tips.html#RequestingPermissions) como eles podem provar confusos para os usuários.

### <a name="creating-a-custom-permission"></a>Criação de uma permissão personalizada

Para usar uma permissão personalizada, ele é declarado pelo serviço enquanto o cliente solicita explicitamente essa permissão.

Para criar uma permissão no serviço do APK, um `permission` elemento é adicionado para o `manifest` elemento no **androidmanifest. XML**. Essa permissão deve ter o `name`, `protectionLevel`, e `label` conjunto de atributos. O `name` atributo deve ser definido como uma cadeia de caracteres que identifica exclusivamente a permissão. O nome será exibido na **App Info** modo de exibição da **configurações do Android** (conforme mostrado na próxima seção).

O `protectionLevel` atributo deve ser definido como um dos valores de cadeia de caracteres de quatro que foram descritos acima.  O `label` e `description` deve se referir aos recursos de cadeia de caracteres e são usados para fornecer um nome amigável e uma descrição para o usuário.

Este trecho de código é um exemplo de como declarar um personalizado `permission` de atributo em **androidmanifest. XML** do APK que contém o serviço:

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

Em seguida, o **androidmanifest. XML** do cliente APK deve solicitar explicitamente essa nova permissão. Isso é feito pela adição de `users-permission` de atributo para o **androidmanifest. XML**:

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

Para exibir as permissões que você recebeu um aplicativo, abra o aplicativo de configurações do Android e selecione **aplicativos**. Localize e selecione o aplicativo na lista. Dos **App Info** tela, toque em **permissões** que abrirá uma exibição que mostra todas as permissões concedidas ao aplicativo:

[![Capturas de tela de um dispositivo Android mostrando como localizar as permissões concedidas a um aplicativo](out-of-process-services-images/ipc-06-sml.png)](out-of-process-services-images/ipc-06.png#lightbox)

## <a name="summary"></a>Resumo

Este guia foi uma discussão avançada sobre como executar um serviço do Android em um processo remoto. As diferenças entre um local e um serviço remoto foi explicado, juntamente com alguns motivos por que um serviço remoto pode ser útil para estabilidade e desempenho de um aplicativo Android. Depois de explicar como implementar um serviço remoto e como um cliente pode se comunicar com o serviço, o guia passou para fornecer uma maneira de limitar o acesso ao serviço de somente clientes autorizados.


## <a name="related-links"></a>Links relacionados

- [Manipulador](https://developer.xamarin.com/api/type/Android.OS.Handler/)
- [Message](https://developer.xamarin.com/api/type/Android.OS.Message/)
- [Messenger](https://developer.xamarin.com/api/type/Android.OS.Messenger/)
- [ServiceAttribute](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute)
- [O atributo exportados](https://developer.android.com/guide/topics/manifest/service-element.html#exported)
- [Serviços com processos isolados e classe personalizada de aplicativo não conseguir resolver sobrecargas corretamente](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)
- [Processos e Threads](https://developer.android.com/guide/components/processes-and-threads.html)
- [Manifesto do Android - permissões](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)
- [Dicas de segurança](https://developer.android.com/training/articles/security-tips.html)
- [MessengerServiceDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/MessengerServiceDemo/)
