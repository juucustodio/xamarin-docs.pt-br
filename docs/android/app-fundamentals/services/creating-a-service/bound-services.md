---
title: Serviços vinculados no Xamarin. Android
description: Os serviços vinculados são serviços Android que fornecem uma interface de cliente-servidor que um cliente (como uma atividade do Android) pode interagir com o. Este guia discutirá os principais componentes envolvidos na criação de um serviço vinculado e como usá-lo em um aplicativo Xamarin. Android.
ms.prod: xamarin
ms.assetid: 809ECE88-EF08-4E9A-B389-A2DC08C51A6E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/04/2018
ms.openlocfilehash: 3aacb0f9b3aca0c8b64e01eb79270e73750c9f3d
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455490"
---
# <a name="bound-services-in-xamarinandroid"></a>Serviços vinculados no Xamarin. Android

_Os serviços vinculados são serviços Android que fornecem uma interface de cliente-servidor que um cliente (como uma atividade do Android) pode interagir com o. Este guia discutirá os principais componentes envolvidos na criação de um serviço vinculado e como usá-lo em um aplicativo Xamarin. Android._

## <a name="bound-services-overview"></a>Visão geral dos serviços associados

Os serviços que fornecem uma interface de cliente-servidor para que os clientes interajam diretamente com o serviço são chamados de  _serviços associados_.  Pode haver vários clientes conectados a uma única instância de um serviço ao mesmo tempo. O serviço ligado e o cliente são isolados uns dos outros. Em vez disso, o Android fornece uma série de objetos intermediários que gerenciam o estado da conexão entre os dois. Esse estado é mantido por um objeto que implementa a [`Android.Content.IServiceConnection`](xref:Android.Content.IServiceConnection) interface.  Esse objeto é criado pelo cliente e passado como um parâmetro para o [`BindService`](xref:Android.Content.Context.BindService*) método. O `BindService` está disponível em qualquer [`Android.Content.Context`](xref:Android.Content.Context) objeto (como uma atividade). É uma solicitação para o sistema operacional Android iniciar o serviço e associar um cliente a ele. Há três maneiras de um cliente ser associado a um serviço usando o `BindService` método:

- **Um associador** &ndash; de serviço Um associador de serviço é uma classe que implementa a [`Android.OS.IBinder`](xref:Android.OS.IBinder) interface. A maioria dos aplicativos não implementará essa interface diretamente, em vez disso, elas vão estender a [`Android.OS.Binder`](xref:Android.OS.Binder) classe. Essa é a abordagem mais comum e é adequada para quando o serviço e o cliente existem no mesmo processo.
- **Usando um Messenger** &ndash; Essa técnica é adequada para quando o serviço pode existir em um processo separado. Em vez disso, as solicitações de serviço são marshalled entre o cliente e o serviço por meio de um [`Android.OS.Messenger`](xref:Android.OS.Messenger) . Um [`Android.OS.Handler`](xref:Android.OS.Handler) é criado no serviço que processará as `Messenger` solicitações. Isso será abordado em outro guia.
- **Usando AIDL (linguagem de definição de interface)** &ndash; do Android [AIDL](https://developer.android.com/guide/components/aidl) é uma técnica avançada que não será abordada neste guia.

Depois que um cliente tiver sido associado a um serviço, a comunicação entre os dois ocorrerá por meio do `Android.OS.IBinder` objeto.  Esse objeto é responsável pela interface que permitirá que o cliente interaja com o serviço. Não é necessário que cada aplicativo Xamarin. Android Implemente essa interface do zero, o SDK do Android fornece a [`Android.OS.Binder`](xref:Android.OS.Binder) classe que cuida da maior parte do código necessário com a Marshalling do objeto entre o cliente e o serviço.

Quando um cliente é concluído com o serviço, ele deve desassociá-lo chamando o `UnbindService` método. Depois que o último cliente tiver desassociado de um serviço, o Android irá parar e descartar o serviço associado.

Este diagrama ilustra como a atividade, a conexão de serviço, o fichário e o serviço estão todos relacionados entre si:

![Um diagrama que mostra como os componentes de serviço se relacionam entre si](bound-services-images/bound-services-02.png "Um diagrama que mostra como os componentes de serviço se relacionam entre si.")

Este guia explicará como estender a `Service` classe para implementar um serviço associado. Ele também abrangerá `IServiceConnection` a implementação e `Binder` a extensão para permitir que um cliente se comunique com o serviço. Um aplicativo de exemplo acompanha este guia, que contém uma solução com um único projeto Xamarin. Android chamado **[BoundServiceDemo](https://github.com/xamarin/monodroid-samples/tree/master/ApplicationFundamentals/ServiceSamples/BoundServiceDemo)** . Este é um aplicativo muito básico que demonstra como implementar um serviço e como associar uma atividade a ele. O serviço ligado tem uma API muito simples com apenas um método, `GetFormattedTimestamp` , que retorna uma cadeia de caracteres que informa ao usuário quando o serviço foi iniciado e por quanto tempo ele está em execução. O aplicativo também permite que o usuário desassocie manualmente e associe-se ao serviço.

[![Captura de tela do aplicativo em execução em um telefone Android](bound-services-images/bound-services-03-sml.png)](bound-services-images/bound-services-03.png#lightbox)

## <a name="implementing-and-consuming-a-bound-service"></a>Implementando e consumindo um serviço associado

Há três componentes que devem ser implementados para que um aplicativo Android consuma um serviço associado:

1. **Estender a `Service` classe e implementar os métodos** &ndash; de retorno de chamada do ciclo de vida Essa classe conterá o código que executará o trabalho que será solicitado ao serviço. Isso será abordado em mais detalhes abaixo.
2. **Criar uma classe que implementa `IServiceConnection` ** &ndash;Essa interface fornece métodos de retorno de chamada que serão invocados pelo Android para notificar o cliente quando a conexão com o serviço for alterada, ou seja, o cliente se conectou ou desconectou ao serviço. A conexão de serviço também fornecerá uma referência a um objeto que o cliente pode usar para interagir diretamente com o serviço. Essa referência é conhecida como o _associador_.
3. **Criar uma classe que implementa `IBinder` ** &ndash;Uma implementação de _fichário_ fornece a API que um cliente usa para se comunicar com o serviço. O associador pode fornecer uma referência ao serviço associado, permitindo que os métodos sejam invocados diretamente ou que o associador possa fornecer uma API de cliente que encapsula e oculta o serviço vinculado do aplicativo. Um `IBinder` deve fornecer o código necessário para chamadas de procedimento remoto. Não é necessário (ou recomendado) implementar a `IBinder` interface diretamente. Em vez disso, os aplicativos devem estender o `Binder` tipo que fornece a maior parte da funcionalidade base exigida por um `IBinder` .
4. **Iniciando e ligando a um serviço** &ndash; Depois que a conexão de serviço, o fichário e o serviço tiverem sido criados, o aplicativo Android será responsável por iniciar o serviço e associá-lo a ele.

Cada uma dessas etapas será discutida nas seções a seguir mais detalhadamente.

### <a name="extend-the-service-class"></a>Estender a `Service` classe

Para criar um serviço usando o Xamarin. Android, é necessário para `Service` subclasse e adornar a classe com o [`ServiceAttribute`](xref:Android.App.ServiceAttribute) . O atributo é usado pelas ferramentas de Build do Xamarin. Android para registrar corretamente o serviço no arquivo de **AndroidManifest.xml** do aplicativo de forma muito semelhante a uma atividade, um serviço associado tem seus próprios métodos de ciclo de vida e de retorno de chamada associados aos eventos significativos no ciclo de vida. A lista a seguir é um exemplo de alguns dos métodos de retorno de chamada mais comuns que um serviço irá implementar:

- `OnCreate`&ndash;Esse método é invocado pelo Android, pois ele está instanciando o serviço. Ele é usado para inicializar variáveis ou objetos que são exigidos pelo serviço durante o tempo de vida. Esse método é opcional.
- `OnBind`&ndash;Esse método deve ser implementado por todos os serviços associados. Ele é invocado quando o primeiro cliente tenta se conectar ao serviço. Retornará uma instância do `IBinder` para que o cliente possa interagir com o serviço. Desde que o serviço esteja em execução, o `IBinder` objeto será usado para atender a quaisquer solicitações futuras do cliente para associar ao serviço.
- `OnUnbind`&ndash;Esse método é chamado quando todos os clientes associados estão desvinculados. Ao retornar  `true` desse método, o serviço será chamado posteriormente `OnRebind` com a intenção passada ao `OnUnbind` quando novos clientes se associarem a ele. Você faria isso quando um serviço continuar a ser executado depois que ele tiver sido desassociado. Isso aconteceria se o serviço recentemente desvinculado fosse também um serviço iniciado, e  `StopService` ou não  `StopSelf` tivesse sido chamado. Nesse cenário,  `OnRebind` permite que a intenção seja recuperada. O padrão retorna  `false` , o que não faz nada. Opcional.
- `OnDestroy`&ndash;Esse método é chamado quando o Android está destruindo o serviço. Qualquer limpeza necessária, como liberar recursos, deve ser executada nesse método. Opcional.

Os eventos de ciclo de vida da chave de um serviço associado são mostrados neste diagrama:

![Um diagrama que mostra a ordem na qual os métodos de ciclo de vida são chamados](bound-services-images/bound-services-01.png "Um diagrama que mostra a ordem na qual os métodos de ciclo de vida são chamados.")

O trecho de código a seguir, do aplicativo complementar que acompanha este guia, mostra como implementar um serviço associado no Xamarin. Android:

```csharp
using Android.App;
using Android.Util;
using Android.Content;
using Android.OS;

namespace BoundServiceDemo
{
    [Service(Name="com.xamarin.ServicesDemo1")]
    public class TimestampService : Service, IGetTimestamp
    {
        static readonly string TAG = typeof(TimestampService).FullName;
        IGetTimestamp timestamper;

        public IBinder Binder { get; private set; }

        public override void OnCreate()
        {
            // This method is optional to implement
            base.OnCreate();
            Log.Debug(TAG, "OnCreate");
            timestamper = new UtcTimestamper();
        }

        public override IBinder OnBind(Intent intent)
        {
            // This method must always be implemented
            Log.Debug(TAG, "OnBind");
            this.Binder = new TimestampBinder(this);
            return this.Binder;
        }

        public override bool OnUnbind(Intent intent)
        {
            // This method is optional to implement
            Log.Debug(TAG, "OnUnbind");
            return base.OnUnbind(intent);
        }

        public override void OnDestroy()
        {
            // This method is optional to implement
            Log.Debug(TAG, "OnDestroy");
            Binder = null;
            timestamper = null;
            base.OnDestroy();
        }

        /// <summary>
        /// This method will return a formatted timestamp to the client.
        /// </summary>
        /// <returns>A string that details what time the service started and how long it has been running.</returns>
        public string GetFormattedTimestamp()
        {
            return timestamper?.GetFormattedTimestamp();
        }
    }
}
```

No exemplo, o `OnCreate` método inicializa um objeto que mantém a lógica para recuperar e formatar um carimbo de data/hora que seria solicitado por um cliente. Quando o primeiro cliente tenta se associar ao serviço, o Android invoca o `OnBind` método. Esse serviço criará uma instância `TimestampBinder` de um objeto que permitirá que os clientes acessem essa instância do serviço em execução. A `TimestampBinder` classe é discutida na próxima seção.

### <a name="implementing-ibinder"></a>Implementando IBinder

Como mencionado, um `IBinder` objeto fornece o canal de comunicação entre um cliente e um serviço. Os aplicativos Android não devem implementar a `IBinder` interface, o que [`Android.OS.Binder`](xref:Android.OS.Binder) deve ser estendido. A `Binder` classe fornece grande parte da infraestrutura necessária, que é necessária para realizar marshaling do objeto do fichário do serviço (que pode estar sendo executado em um processo separado) para o cliente. Na maioria dos casos, a `Binder` subclasse é apenas algumas linhas de código e encapsula uma referência ao serviço. Neste exemplo, `TimestampBinder` tem uma propriedade que expõe o `TimestampService` ao cliente:

```csharp
public class TimestampBinder : Binder
{
    public TimestampBinder(TimestampService service)
    {
        this.Service = service;
    }

    public TimestampService Service { get; private set; }
}
```

Isso `Binder` possibilita invocar os métodos públicos no serviço; por exemplo:

```csharp
string currentTimestamp = serviceConnection.Binder.Service.GetFormattedTimestamp()
```

Uma vez `Binder` estendido, é necessário implementar `IServiceConnection` para conectar tudo.

### <a name="creating-the-service-connection"></a>Criando a conexão de serviço

O apresentará `IServiceConnection` | introduzir | expor | conectar o `Binder` objeto ao cliente. Além de implementar a `IServiceConnection` interface, a classe deve ser estendida `Java.Lang.Object` . A conexão de serviço também deve fornecer alguma maneira de o cliente poder acessar o `Binder` (e, portanto, se comunicar com o serviço vinculado).

Esse código é do projeto de exemplo que o acompanha é uma maneira possível de implementar `IServiceConnection` :

```csharp
using Android.Util;
using Android.OS;
using Android.Content;

namespace BoundServiceDemo
{
    public class TimestampServiceConnection : Java.Lang.Object, IServiceConnection, IGetTimestamp
    {
        static readonly string TAG = typeof(TimestampServiceConnection).FullName;

        MainActivity mainActivity;
        public TimestampServiceConnection(MainActivity activity)
        {
            IsConnected = false;
            Binder = null;
            mainActivity = activity;
        }

        public bool IsConnected { get; private set; }
        public TimestampBinder Binder { get; private set; }

        public void OnServiceConnected(ComponentName name, IBinder service)
        {
            Binder = service as TimestampBinder;
            IsConnected = this.Binder != null;

            string message = "onServiceConnected - ";
            Log.Debug(TAG, $"OnServiceConnected {name.ClassName}");

            if (IsConnected)
            {
                message = message + " bound to service " + name.ClassName;
                mainActivity.UpdateUiForBoundService();
            }
            else
            {
                message = message + " not bound to service " + name.ClassName;
                mainActivity.UpdateUiForUnboundService();
            }

            Log.Info(TAG, message);
            mainActivity.timestampMessageTextView.Text = message;

        }

        public void OnServiceDisconnected(ComponentName name)
        {
            Log.Debug(TAG, $"OnServiceDisconnected {name.ClassName}");
            IsConnected = false;
            Binder = null;
            mainActivity.UpdateUiForUnboundService();
        }

        public string GetFormattedTimestamp()
        {
            if (!IsConnected)
            {
                return null;
            }

            return Binder?.GetFormattedTimestamp();
        }
    }
}

```

Como parte do processo de associação, o Android invocará o `OnServiceConnected` método, fornecendo o `name` do serviço que está sendo associado e o `binder` que mantém uma referência ao próprio serviço. Neste exemplo, a conexão de serviço tem duas propriedades, uma que mantém uma referência ao fichário e um sinalizador booliano para se o cliente estiver conectado ao serviço ou não.

O `OnServiceDisconnected` método é invocado somente quando a conexão entre um cliente e um serviço é perdida ou interrompida inesperadamente. Esse método permite ao cliente a oportunidade de responder à interrupção no serviço.  

## <a name="starting-and-binding-to-a-service-with-an-explicit-intent"></a>Iniciando e ligando a um serviço com uma intenção explícita

Para usar um serviço associado, um cliente (como uma atividade) deve criar uma instância de um objeto que implementa `Android.Content.IServiceConnection` e invoca o `BindService` método. `BindService` retornará `true` se o serviço estiver associado, `false` se não for. O `BindService` método usa três parâmetros:

- **Um `Intent` ** &ndash;A intenção deve identificar explicitamente a qual serviço se conectar.
- **Um `IServiceConnection` Objeto** &ndash; esse objeto é um intermediário que fornece métodos de retorno de chamada para notificar o cliente quando o serviço ligado é iniciado e interrompido.
- ** [`Android.Content.Bind`](xref:Android.Content.Bind) enumeração de** &ndash; Esse parâmetro é um conjunto de sinalizadores usado pelo sistema para quando associar o objeto. O valor mais comumente usado é [`Bind.AutoCreate`](xref:Android.Content.Bind.AutoCreate) , que iniciará automaticamente o serviço se ele ainda não estiver em execução.

O trecho de código a seguir é um exemplo de como iniciar um serviço associado em uma atividade usando uma intenção explícita:

```csharp
protected override void OnStart ()
{
    base.OnStart ();

    if (serviceConnection == null)
    {
        this.serviceConnection = new TimestampServiceConnection(this);
    }

    Intent serviceToStart = new Intent(this, typeof(TimestampService));
    BindService(serviceToStart, this.serviceConnection, Bind.AutoCreate);

}
```

> [!IMPORTANT]
> A partir do Android 5,0 (nível da API 21), só é possível associar a um serviço com uma intenção explícita.

## <a name="architectural-notes-about-the-service-connection-and-the-binder"></a>Observações sobre arquitetura sobre a conexão de serviço e o associador.

Algumas os puristas OOP podem Desaprovamos da implementação anterior da classe, `TimestampBinder` pois ela é uma violação da [lei de Deméter](https://en.wikipedia.org/wiki/Law_of_Demeter) que, nos Estados de forma mais simples, não se comunicam com estranhos; Fale apenas com seus amigos ". Essa implementação específica expõe a `TimestampService` classe concreta a todos os clientes.

Estritamente falando, não é necessário que o cliente saiba sobre o `TimestampService` e expor essa classe concreta para os clientes pode tornar um aplicativo mais frágil e mais difícil de manter em seu tempo de vida. Uma abordagem alternativa é usar uma interface que expõe o `GetFormattedTimestamp()` método e chamadas de proxy para o serviço por meio da `Binder` (ou possível a classe de conexão de serviço):  

```csharp
public class TimestampBinder : Binder, IGetTimestamp
{
    TimestampService service;
    public TimestampBinder(TimestampService service)
    {
        this.service = service;
    }

    public string GetFormattedTimestamp()
    {
        return service?.GetFormattedTimestamp();
    }
}
```

Este exemplo específico permite que uma atividade invoque métodos no próprio serviço:

```csharp
// In this example the Activity is only talking to a friend, i.e. the IGetTimestamp interface provided by the Binder.
string currentTimestamp = serviceConnection.Binder.GetFormattedTimestamp()
```

## <a name="related-links"></a>Links Relacionados

- [Android. app. Service](xref:Android.App.Service)
- [Android. Content. bind](xref:Android.Content.Bind)
- [Android. Content. Context](xref:Android.Content.Context)
- [Android. Content. IServiceConnection](xref:Android.Content.IServiceConnection)
- [Android. OS. Binder](xref:Android.OS.Binder)
- [Android. OS. IBinder](xref:Android.OS.IBinder)
- [BoundServiceDemo (exemplo)](/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-boundservicedemo)