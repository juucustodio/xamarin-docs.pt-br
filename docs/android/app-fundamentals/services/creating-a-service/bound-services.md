---
title: Serviços vinculados a xamarin
description: Os serviços associados são Android que fornecem uma interface de cliente-servidor que um cliente (como uma atividade Android) pode interagir com. Este guia aborda os principais componentes envolvidos na criação de um serviço vinculado e como usá-lo em um aplicativo xamarin.
ms.prod: xamarin
ms.assetid: 809ECE88-EF08-4E9A-B389-A2DC08C51A6E
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 05/04/2018
ms.openlocfilehash: f4fe1bd753260f05dedb452655572d290c0781d0
ms.sourcegitcommit: daa089d41cfe1ed0456d6de2f8134cf96ae072b1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33850568"
---
# <a name="bound-services-in-xamarinandroid"></a>Serviços vinculados a xamarin

_Os serviços associados são Android que fornecem uma interface de cliente-servidor que um cliente (como uma atividade Android) pode interagir com. Este guia aborda os principais componentes envolvidos na criação de um serviço vinculado e como usá-lo em um aplicativo xamarin._

## <a name="bound-services-overview"></a>Visão geral dos serviços associados

Serviços que fornecem uma interface de cliente-servidor para que os clientes interagem diretamente com o serviço são referidos como _associado serviços_.  Pode haver vários clientes conectados a uma única instância de um serviço ao mesmo tempo. O cliente e o serviço vinculado são isoladas uma da outra. Em vez disso, o Android fornece uma série de objetos intermediários que gerencia o estado da conexão entre os dois. Esse estado é mantido por um objeto que implementa o [ `Android.Content.IServiceConnection` ](https://developer.xamarin.com/api/type/Android.Content.IServiceConnection/) interface.  Esse objeto é criado pelo cliente e passado como um parâmetro para o [ `BindService` ](https://developer.xamarin.com/api/member/Android.Content.Context.BindService/) método. O `BindService` está disponível em qualquer [ `Android.Content.Context` ](https://developer.xamarin.com/api/type/Android.Content.Context) objeto (como uma atividade). Esta é uma solicitação para o sistema operacional Android para iniciar o serviço e um cliente se associar a ele. Há três maneiras de um cliente podem vincular a um serviço usando o `BindService` método:

* **Um associador de serviço** &ndash; um associador de serviço é uma classe que implementa o [ `Android.OS.IBinder` ](https://developer.xamarin.com/api/type/Android.OS.IBinder) interface. A maioria dos aplicativos não implementar essa interface diretamente, em vez disso, eles se estenderá o [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder) classe. Essa é a abordagem mais comum e é adequada para quando o cliente e o serviço existem no mesmo processo.
* **Usando um Messenger** &ndash; essa técnica é adequada para quando o serviço pode existir em um processo separado. Em vez disso, as solicitações de serviço são marshaling entre o cliente e o serviço por meio de um [ `Android.OS.Messenger` ](https://developer.xamarin.com/api/type/Android.OS.Messenger). Um [ `Android.OS.Handler` ](https://developer.xamarin.com/api/type/Android.OS.Handler) é criada no serviço que manipulará o `Messenger` solicitações. Isso será abordado em outra guia.
* **Usando AIDL** &ndash; isso atinja o terrorismo no centro da maioria dos desenvolvedores Android e não será abordado neste guia.

Quando um cliente for associado a um serviço, a comunicação entre os dois é ocorre por meio de `Android.OS.IBinder` objeto.  Esse objeto é responsável para a interface que permitirá que o cliente interagir com o serviço. Não é necessário para cada aplicativo xamarin implementar esta interface a partir do zero, o Android SDK fornece o [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder) classe que cuida da maior parte do código necessário com o objeto entre o empacotamento o cliente e o serviço.

Quando um cliente é feito com o serviço, ele deve desassociar dela chamando o `UnbindService` método. Depois que o último cliente tem vinculados de um serviço, o Android parar e descartar o serviço vinculado.

Este diagrama ilustra como a atividade, conexão de serviço, Associador e serviço relacionam entre si:

![Um diagrama que mostra como os componentes de serviço se relacionam entre si](bound-services-images/bound-services-02.png "um diagrama que mostra como os componentes de serviço se relacionam entre si.")

Este guia discutiremos como estender o `Service` classe para implementar um serviço vinculado. Ele também aborda a implementação `IServiceConnection` e estendendo `Binder` para permitir que um cliente para se comunicar com o serviço. Um aplicativo de exemplo acompanha este guia, que contém uma solução com um único projeto xamarin chamado **[BoundServiceDemo](https://github.com/xamarin/monodroid-samples/tree/master/ApplicationFundamentals/ServiceSamples/BoundServiceDemo)** . Este é um aplicativo muito básico que demonstra como implementar um serviço e como vincular uma atividade a ela. O serviço vinculado tem uma API muito simple com apenas um método, `GetFormattedTimestamp`, que retorna uma cadeia de caracteres que informa ao usuário quando o serviço foi iniciado e quanto tempo ele esteve em execução. O aplicativo também permite que o usuário manualmente desassocie e associe ao serviço.

[![Captura de tela do aplicativo em execução em um telefone Android](bound-services-images/bound-services-03-sml.png)](bound-services-images/bound-services-03.png#lightbox)

## <a name="implementing-and-consuming-a-bound-service"></a>Implementando e consumir um serviço vinculado

Há três componentes que devem ser implementados para que um aplicativo do Android consumir um serviço vinculado:

1. **Estender o `Service` classe e implementar os métodos de retorno de chamada do ciclo de vida** &ndash; essa classe conterá o código que executará o trabalho será solicitado do serviço. Isso será abordado em mais detalhes abaixo.
2. **Criar uma classe que implementa `IServiceConnection`**  &ndash; essa interface fornece métodos de retorno de chamada serão invocado pelo Android para notificar o cliente quando a conexão para o serviço foi alterada, ou seja, o cliente conectado ou desconectado para o serviço. A conexão de serviço também fornece uma referência a um objeto que o cliente pode usar para interagir diretamente com o serviço. Essa referência é conhecida como o _associador_.
3. **Criar uma classe que implementa `IBinder`**  &ndash; um _associador_ implementação fornece a API que um cliente usa para se comunicar com o serviço. O associador pode fornecer uma referência para o serviço vinculado, permitindo que os métodos para ser chamado diretamente ou o associador pode fornecer uma API que encapsula e oculta o serviço vinculado do aplicativo cliente. Um `IBinder` deve fornecer o código necessário para chamadas de procedimento remoto. Não é necessário (ou recomendados) para implementar o `IBinder` interface diretamente. Em vez disso, os aplicativos devem estender o `Binder` tipo que fornece a maioria da funcionalidade base exigida por um `IBinder`.
4. **Iniciando e associação a um serviço** &ndash; depois que a conexão de serviço, o associador e o serviço foi criados o aplicativo Android é responsável por iniciar o serviço e associação para ele.

Cada uma dessas etapas será discutida nas seções a seguir mais detalhadamente.

### <a name="extend-the-service-class"></a>Estender o `Service` classe

Para criar um serviço usando o xamarin, é necessário para a subclasse `Service` e Adornar a classe com o [ `ServiceAttribute` ](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute). O atributo é usado pelas ferramentas de build xamarin para registrar corretamente o serviço do aplicativo em **AndroidManifest.xml** muito parecida com uma atividade de arquivos, um serviço vinculado tem seu próprio ciclo de vida e retorno de chamada de métodos associados a eventos significativos no ciclo de vida. A lista a seguir é um exemplo de alguns dos métodos mais comuns de retorno de chamada que implementa um serviço:

* `OnCreate` &ndash; Esse método é chamado pelo Android conforme é criar uma instância de serviço. Ele é usado para inicializar quaisquer variáveis ou os objetos que são exigidos pelo serviço durante seu ciclo de vida. Esse método é opcional.
* `OnBind` &ndash; Esse método deve ser implementado por todos os serviços associados. Ele é invocado quando o primeiro cliente tenta se conectar ao serviço. Ela retornará uma instância de `IBinder` para que o cliente pode interagir com o serviço. Desde que o serviço está em execução, o `IBinder` objeto será usado para atender as solicitações de cliente futura para ligação com o serviço.
* `OnUnbind` &ndash; Esse método é chamado quando todos os clientes associados têm não associado. Retornando `true` desse método, o serviço mais tarde chamará `OnRebind` com a intenção passada para `OnUnbind` quando novos clientes se associar a ele. Você faria isso quando um serviço continua em execução depois que ele foi desassociado. Isso ocorrerá se o serviço recentemente não associado também um serviço iniciado, e `StopService` ou `StopSelf` não tivesse sido chamado. Nesse cenário, `OnRebind` permite que a intenção de ser recuperada. Retorna o padrão `false` , que não faz nada. Opcional.
* `OnDestroy` &ndash; Esse método é chamado quando o Android é destruir o serviço. Qualquer limpeza necessária, como liberar recursos, deve ser executada nesse método. Opcional.

Os eventos de ciclo de vida de um serviço vinculado são mostrados neste diagrama:

![Um diagrama que mostra a ordem na qual os métodos de ciclo de vida são chamados](bound-services-images/bound-services-01.png "um diagrama que mostra a ordem em que são chamados os métodos de ciclo de vida.")

O seguinte trecho de código do aplicativo complementar que acompanha este guia mostra como implementar um serviço vinculado no xamarin:

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

No exemplo, o `OnCreate` método inicializa um objeto que contém a lógica para recuperar e formatar um carimbo de hora que eram solicitado por um cliente. Quando o primeiro cliente tenta fazer ligação com o serviço, o Android invocará o `OnBind` método. Esse serviço instanciará uma `TimestampServiceBinder` objeto que permitirá que os clientes para acessar a instância do serviço em execução. O `TimestampServiceBinder` classe é abordada na próxima seção.

### <a name="implementing-ibinder"></a>Implementando IBinder

Conforme mencionado, um `IBinder` objeto fornece o canal de comunicação entre um cliente e um serviço. Aplicativos do Android não devem implementar o `IBinder` interface, o [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder/) deve ser estendido. O `Binder` classe muito a infraestrutura necessária, que é necessário marshaling fornece o objeto de fichário do serviço (que pode estar em execução em um processo separado) para o cliente. Na maioria dos casos, o `Binder` subclasse é apenas algumas linhas de código e encapsula uma referência para o serviço. Neste exemplo, `TimestampBinder` tem uma propriedade que expõe o `TimestampService` para o cliente:

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

Isso `Binder` torna possível invocar os métodos públicos no serviço; por exemplo:

```csharp
string currentTimestamp = serviceConnection.Binder.Service.GetFormattedTimestamp()
```

Uma vez `Binder` foi estendido, é necessário para implementar `IServiceConnection` conectar tudo.

### <a name="creating-the-service-connection"></a>Criando a Conexão de serviço

O `IServiceConnection` apresentará | introduzir | expor | conectar-se a `Binder` objeto para o cliente. Além de implementar o `IServiceConnection` deve se estender a interface, a classe `Java.Lang.Object`. A conexão de serviço também deve fornecer uma maneira do cliente pode acessar o `Binder` (e, portanto, se comunicar com o serviço vinculado).

Esse código é de que o acompanha o projeto de exemplo é uma forma possível implementar `IServiceConnection`:

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

Como parte do processo de associação, o Android invocará o `OnServiceConnected` método, fornecendo o `name` do serviço que está sendo associado e o `binder` que contém uma referência para o próprio serviço. Neste exemplo, a conexão de serviço tem duas propriedades, que contém uma referência para o associador e um sinalizador booliano se o cliente está conectado ao serviço ou não.

O `OnServiceDisconnected` método será chamado apenas quando a conexão entre um cliente e um serviço inesperadamente for perdido ou interrompida. Esse método permite que o cliente a oportunidade de responder a interrupção do serviço.  

## <a name="starting-and-binding-to-a-service-with-an-explicit-intent"></a>Iniciando e associação a um serviço com uma intenção explícita

Para usar um serviço vinculado, um cliente (como uma atividade) deve instanciar um objeto que implementa `Android.Content.IServiceConnection` e invocar o `BindService` método.` BindService` retornará `true` se o serviço está vinculado, `false` se não for. O `BindService` método usa três parâmetros:

* **Um `Intent`**  &ndash; a intenção explicitamente deve identificar qual serviço para se conectar ao.
* **Um `IServiceConnection` objeto** &ndash; este objeto é um intermediário que fornece métodos de retorno de chamada para notificar o cliente quando o serviço vinculado é iniciado e parado.
* **[`Android.Content.Bind`](https://developer.xamarin.com/api/type/Android.Content.Bind/) enumeração** &ndash; esse parâmetro é um conjunto de sinalizadores são usados pelo sistema ao associar o objeto. O valor mais comumente usado é [ `Bind.AutoCreate` ](https://developer.xamarin.com/api/field/Android.Content.Bind.AutoCreate/), que iniciará automaticamente o serviço se não estiver sendo executado.

O trecho de código a seguir é um exemplo de como iniciar um serviço vinculado em uma atividade com uma intenção explícita:

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
> Começando com o Android 5.0 (API nível 21) só é possível vincular a um serviço com uma intenção explícita.

## <a name="architectural-notes-about-the-service-connection-and-the-binder"></a>Arquitetura Observações sobre a Conexão de serviço e o associador.

Alguns os puristas do OOP podem desaprovar da implementação anterior do `TimestampBinder` classe conforme ele é uma violação do [lei de Deméter](https://en.wikipedia.org/wiki/Law_of_Demeter) , em sua forma mais simples indicando "não falar com estranhos; apenas se comunicar com seus amigos". Essa implementação específica expõe concretos `TimestampService` classe para todos os clientes.

Estritamente falando, não é necessário para o cliente saber sobre o `TimestampService` e expondo classe concreta para clientes que um aplicativo mais frágil e mais difícil de manter ao longo do tempo de vida de TI. Uma abordagem alternativa é usar uma interface que expõe o `GetFormattedTimestamp()` método e chamadas de proxy para o serviço por meio de `Binder` (ou possível a classe de conexão de serviço):  

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

Esse exemplo específico permitir uma atividade para chamar métodos no próprio serviço:

```csharp
// In this example the Activity is only talking to a friend, i.e. the IGetTimestamp interface provided by the Binder.
string currentTimestamp = serviceConnection.Binder.GetFormattedTimestamp()
```


## <a name="related-links"></a>Links relacionados

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.Content.Bind](https://developer.xamarin.com/api/type/Android.Content.Bind/)
- [Android.Content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [Android.Content.IServiceConnection](https://developer.xamarin.com/api/type/Android.Content.IServiceConnection/)
- [Android.OS.Binder](https://developer.xamarin.com/api/type/Android.OS.Binder/)
- [Android.OS.IBinder](https://developer.xamarin.com/api/type/Android.OS.IBinder)
- [BoundServiceDemo (exemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/BoundServiceDemo/)
