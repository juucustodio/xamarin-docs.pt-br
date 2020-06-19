---
title: Serviço do Azure Signalr comXamarin.Forms
description: Introdução ao serviço de Signaler do Azure e Azure Functions comXamarin.Forms
ms.prod: xamarin
ms.assetid: 1B9A69EF-C200-41BF-B098-D978D7F9CD8F
author: profexorgeek
ms.author: jusjohns
ms.date: 06/07/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ffa44beb68dc845a64d8bf2a9f86f6d7e56df8f9
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139432"
---
# <a name="azure-signalr-service-with-xamarinforms"></a>Serviço do Azure Signalr comXamarin.Forms

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azuresignalr/)

ASP.NET Core Signalr é um modelo de aplicativo que simplifica o processo de adicionar comunicação em tempo real aos aplicativos. O serviço de Signaler do Azure permite o desenvolvimento e a implantação rápidos de aplicativos Signaler escalonáveis. Azure Functions são métodos de código sem servidor de curta duração que podem ser combinados para formar aplicativos escalonáveis e orientados a eventos.

Este artigo e um exemplo mostram como combinar o serviço de sinalizador do Azure e Azure Functions com o Xamarin.Forms , para entregar mensagens em tempo real a clientes conectados.

> [!NOTE]
> Se você não tiver uma [assinatura do Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), crie uma [conta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de começar.

## <a name="create-an-azure-signalr-service-and-azure-functions-app"></a>Criar um serviço de Signaler do Azure e Azure Functions aplicativo

O aplicativo de exemplo consiste em três componentes principais: um hub de serviço do Signalr do Azure, uma instância de Azure Functions com duas funções e um aplicativo móvel que pode enviar e receber mensagens. Esses componentes interagem da seguinte maneira:

1. O aplicativo móvel invoca uma função **Negotiate** do Azure para obter informações sobre o Hub do signalr.
1. O aplicativo móvel usa as informações de negociação para se registrar no Hub do Signalr e forma uma conexão.
1. Após o registro, o aplicativo móvel posta mensagens na função do Azure **Talk** .
1. A função **Talk** passa a mensagem de entrada para o Hub signalr.
1. O Hub Signalr transmite a mensagem para todas as instâncias de aplicativo móvel conectadas, incluindo o remetente original.

> [!IMPORTANT]
> As funções **Negotiate** e **Talk** no aplicativo de exemplo podem ser executadas localmente usando o Visual Studio 2019 e as ferramentas de tempo de execução do Azure. No entanto, o serviço de sinalizador do Azure não pode ser emulado localmente e é difícil expor Azure Functions hospedado localmente para dispositivos físicos ou virtuais para teste. É recomendável que você implante o Azure Functions em uma instância de aplicativo Azure Functions, pois isso permite o teste entre plataformas. Para obter detalhes de implantação, consulte [implantar Azure Functions com o Visual Studio 2019](#deploy-azure-functions-with-visual-studio-2019).

### <a name="create-an-azure-signalr-service"></a>Criar um serviço de Signaler do Azure

Um serviço de Signaler do Azure pode ser criado escolhendo **criar um recurso** no canto superior esquerdo do portal do Azure e procurando por **signalr**. O serviço de Signaler do Azure pode ser criado na camada gratuita. O serviço de Signaler do Azure deve estar no modo de serviço sem **servidor** . Se você escolher acidentalmente o modo de serviço padrão ou clássico, poderá alterá-lo mais tarde nas propriedades do serviço de Signaler do Azure.

A captura de tela a seguir mostra a criação de um novo serviço de Signaler do Azure:

![Captura de tela da criação do serviço de Signaler do Azure no portal do Azure](azure-signalr-images/azure-signalr-create.png "Criando um serviço de Signaler do Azure")

Depois de criada, a seção **chaves** de um serviço de signaler do Azure contém uma **cadeia de conexão**, que é usada para conectar o aplicativo Azure Functions ao Hub do signalr. A captura de tela a seguir mostra onde encontrar a cadeia de conexão no serviço de Signaler do Azure:

![Captura de tela da cadeia de conexão do Signalr do Azure no portal do Azure](azure-signalr-images/azure-signalr-connection-string.png "Cadeia de conexão do Azure Signalr")

Essa cadeia de conexão é usada para [implantar Azure Functions com o Visual Studio 2019](#deploy-azure-functions-with-visual-studio-2019).

### <a name="create-an-azure-functions-app"></a>Criar um aplicativo Azure Functions

Para testar o aplicativo de exemplo, você deve criar um novo aplicativo Azure Functions no portal do Azure. Anote o **nome do aplicativo** , pois essa URL é usada no arquivo de exemplo do aplicativo **Constants.cs** . A captura de tela a seguir mostra a criação de um novo aplicativo Azure Functions chamado "xdocsfunctions":

[![Captura de tela da criação do aplicativo Azure Functions](azure-signalr-images/azure-functions-app-cropped.png)](azure-signalr-images/azure-functions-app-full.png#lightbox)

O Azure Functions pode ser implantado em uma instância de aplicativo Azure Functions do Visual Studio 2019. As seções a seguir descrevem a implantação de duas funções no aplicativo de exemplo para uma instância de aplicativo Azure Functions.

### <a name="build-azure-functions-in-visual-studio-2019"></a>Criar Azure Functions no Visual Studio 2019

O aplicativo de exemplo contém uma biblioteca de classes chamada **ChatServer**, que inclui dois Azure Functions sem servidor em arquivos chamados **Negotiate.cs** e **Talk.cs**.

A `Negotiate` função responde a solicitações da Web com um `SignalRConnectionInfo` objeto que contém uma `AccessToken` propriedade e uma `Url` propriedade. O aplicativo móvel usa esses valores para se registrar no Hub do Signalr. O código a seguir mostra a `Negotiate` função:

```csharp
[FunctionName("Negotiate")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous,"get",Route = "negotiate")]
    HttpRequest req,
    [SignalRConnectionInfo(HubName = "simplechat")]
    SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

A `Talk` função responde a solicitações HTTP post que fornecem um objeto Message no corpo da postagem. O corpo da POSTAgem é transformado em um `SignalRMessage` e encaminhado para o Hub do signalr. O código a seguir mostra a `Talk` função:

```csharp
[FunctionName("Talk")]
public static async Task<IActionResult> Run(
    [HttpTrigger(
        AuthorizationLevel.Anonymous,
        "post",
        Route = "talk")]
    HttpRequest req,
    [SignalR(HubName = "simplechat")]
    IAsyncCollector<SignalRMessage> questionR,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    try
    {
        string json = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic obj = JsonConvert.DeserializeObject(json);

        var name = obj.name.ToString();
        var text = obj.text.ToString();

        var jObject = new JObject(obj);

        await questionR.AddAsync(
            new SignalRMessage
            {
                Target = "newMessage",
                Arguments = new[] { jObject }
            });

        return new OkObjectResult($"Hello {name}, your message was '{text}'");
    }
    catch (Exception ex)
    {
        return new BadRequestObjectResult("There was an error: " + ex.Message);
    }
}
```

Para saber mais sobre o Azure Functions e aplicativos Azure Functions, consulte [Azure Functions documentação](/azure/azure-functions/).

### <a name="deploy-azure-functions-with-visual-studio-2019"></a>Implantar Azure Functions com o Visual Studio 2019

O Visual Studio 2019 permite implantar funções em um aplicativo Azure Functions. As funções hospedadas no Azure facilitam o teste de plataforma cruzada fornecendo um ponto de extremidade de teste acessível para todos os dispositivos.

Clicar com o botão direito do mouse no aplicativo de funções de exemplo e escolher **publicar** inicia a caixa de diálogo para publicar funções em seu aplicativo Azure functions. Se você seguiu as etapas anteriores para configurar uma Aplicativo de funções do Azure, você pode escolher **selecionar existente** para publicar os aplicativos de exemplo em seu aplicativo Azure functions. A captura de tela a seguir mostra as opções da caixa de diálogo publicar no Visual Studio 2019:

![Caixa de diálogo Publicar opções no Visual Studio 2019](azure-signalr-images/vs-publish-target.png "Opções de publicação no Visual Studio 2019")

Depois de entrar no seu conta Microsoft, você pode localizar e escolher seu aplicativo Azure Functions como o destino de publicação. A captura de tela a seguir mostra um exemplo Azure Functions aplicativo na caixa de diálogo de publicação do Visual Studio 2019:

![Um aplicativo Azure Functions na caixa de diálogo de publicação do Visual Studio 2019](azure-signalr-images/vs-app-selection.png "Azure Functions aplicativo na caixa de diálogo de publicação do Visual Studio 2019")

Depois de selecionar uma instância de aplicativo Azure Functions, a URL do site, a configuração e outras informações sobre o aplicativo de Azure Functions de destino são exibidas. Escolha **editar Azure app configurações de serviço** e insira a cadeia de conexão no campo **remoto** . A cadeia de conexão é usada pelas funções **Negotiate** e **Talk** para se conectar ao serviço de signaler do Azure e está disponível na seção **chaves** do serviço de signaler do Azure em seu portal do Azure. Para obter mais informações sobre a cadeia de conexão, consulte [criar um serviço de signaler do Azure](#create-an-azure-signalr-service).

Depois de inserir a cadeia de conexão, você pode clicar em **publicar** para implantar suas funções no aplicativo Azure functions. Após a conclusão, as funções serão listadas no aplicativo Azure Functions no portal do Azure. A captura de tela a seguir mostra as funções publicadas no portal do Azure:

![Funções publicadas no aplicativo Azure Functions](azure-signalr-images/azure-functions-deployed.png "Funções publicadas no aplicativo Azure Functions")

## <a name="integrate-azure-signalr-service-with-xamarinforms"></a>Integrar o serviço do Azure Signalr comXamarin.Forms

A integração entre o serviço do Signalr do Azure e o Xamarin.Forms aplicativo é uma classe de serviço de sinalização que é instanciada na `MainPage` classe com manipuladores de eventos atribuídos a três eventos. Para obter mais informações sobre esses manipuladores de eventos, consulte [usar a classe de serviço Xamarin.Forms signalr no ](#use-the-signalr-service-class-in-xamarinforms).

O aplicativo de exemplo inclui uma classe **Constants.cs** que deve ser personalizada com o ponto de extremidade da URL do seu aplicativo Azure functions. Defina o valor da `HostName` propriedade para seu Azure Functions endereço do aplicativo. O código a seguir mostra as propriedades **Constants.cs** com um `HostName` valor de exemplo:

```csharp
public static class Constants
{
    public static string HostName { get; set; } = "https://example-functions-app.azurewebsites.net/";

    // Used to differentiate message types sent via SignalR. This
    // sample only uses a single message type.
    public static string MessageName { get; set; } = "newMessage";

    public static string Username
    {
        get
        {
            return $"{Device.RuntimePlatform} User";
        }
    }
}
```

> [!NOTE]
> A `Username` propriedade no arquivo **Constants.cs** do aplicativo de exemplo usa o valor do dispositivo `RuntimePlatform` como o nome de usuário. Isso torna mais fácil testar dispositivos entre plataformas e identificar qual dispositivo está enviando a mensagem. Em um aplicativo do mundo real, esse valor provavelmente seria um nome de usuário exclusivo, coletado durante um processo de inscrição ou de entrada.

### <a name="the-signalr-service-class"></a>A classe de serviço Signalr

A `SignalRService` classe no projeto **ChatClient** no aplicativo de exemplo mostra uma implementação que invoca funções em um aplicativo Azure Functions para se conectar a um serviço de signaler do Azure.

O `SendMessageAsync` método na `SignalRService` classe é usado para enviar mensagens aos clientes conectados ao serviço de Signaler do Azure. Esse método executa uma solicitação HTTP POST para a função **Talk** hospedada no aplicativo Azure functions, incluindo um objeto SERIALIZADO em JSON `Message` como a carga de postagem. A função **Talk** passa a mensagem para o serviço de signaler do Azure para difusão a todos os clientes conectados. O código a seguir mostra o `SendMessageAsync` método:

```csharp
public async Task SendMessageAsync(string username, string message)
{
    IsBusy = true;

    var newMessage = new Message
    {
        Name = username,
        Text = message
    };

    var json = JsonConvert.SerializeObject(newMessage);
    var content = new StringContent(json, Encoding.UTF8, "application/json");
    var result = await client.PostAsync($"{Constants.HostName}/api/talk", content);

    IsBusy = false;
}
```

O `ConnectAsync` método na `SignalRService` classe executa uma solicitação HTTP Get para a função **Negotiate** hospedada no aplicativo Azure functions. A função **Negotiate** retorna JSON que é desserializado em uma instância da `NegotiateInfo` classe. Depois que o `NegotiateInfo` objeto é recuperado, ele é usado para se registrar diretamente com o serviço de signaler do Azure usando uma instância da `HubConnection` classe.

ASP.NET Core Signalr converte os dados de entrada da conexão aberta em mensagens e permite que os desenvolvedores definam tipos de mensagem e associem manipuladores de eventos a mensagens de entrada por tipo. O `ConnectAsync` método registra um manipulador de eventos para o nome da mensagem definido no arquivo **Constants.cs** do aplicativo de exemplo, que é "NewMessage" por padrão.

O código a seguir mostra o `ConnectAsync` método:

```csharp
public async Task ConnectAsync()
{
    try
    {
        IsBusy = true;

        string negotiateJson = await client.GetStringAsync($"{Constants.HostName}/api/negotiate");
        NegotiateInfo negotiate = JsonConvert.DeserializeObject<NegotiateInfo>(negotiateJson);
        HubConnection connection = new HubConnectionBuilder()
            .AddNewtonsoftJsonProtocol()
            .WithUrl(negotiate.Url, options =>
            {
                options.AccessTokenProvider = async () => negotiate.AccessToken;
            })
            .Build();

        connection.On<JObject>(Constants.MessageName, AddNewMessage);
        await connection.StartAsync();

        IsConnected = true;
        IsBusy = false;

        Connected?.Invoke(this, true, "Connection successful.");
    }
    catch (Exception ex)
    {
        ConnectionFailed?.Invoke(this, false, ex.Message);
    }
}
```

> [!NOTE]
> O serviço Signalr usa `System.Text.Json` para serializar e desserializar o JSON por padrão. Os dados serializados com outras bibliotecas, como Newtonsoft, podem falhar ao serem desserializados pelo serviço Signalr. A `HubConnection` instância no projeto de exemplo inclui uma chamada para `AddNewtonsoftJsonProtocol` para especificar o serializador JSON. Esse método é definido em um pacote NuGet especial chamado **Microsoft. AspNetCore. signalr. Protocols. NewtonsoftJson** que deve ser incluído no projeto. Se você estiver usando `System.Text.Json` o para serializar/desserializar dados JSON, esse método e o pacote NuGet não deverão ser usados.

O `AddNewMessage` método é associado como o manipulador de eventos na `ConnectAsync` mensagem, conforme mostrado no código anterior. Quando uma mensagem é recebida, o `AddNewMessage` método é chamado com os dados da mensagem fornecidos como um `JObject` . O `AddNewMessage` método converte o `JObject` em uma instância da `Message` classe e, em seguida, invoca o manipulador para `NewMessageReceived` se um tiver sido associado. O código a seguir mostra o `AddNewMessage` método:

```csharp
public void AddNewMessage(JObject message)
{
    Message messageModel = new Message
    {
        Name = message.GetValue("name").ToString(),
        Text = message.GetValue("text").ToString(),
        TimeReceived = DateTime.Now
    };

    NewMessageReceived?.Invoke(this, messageModel);
}
```

### <a name="use-the-signalr-service-class-in-xamarinforms"></a>Usar a classe de serviço Signalr emXamarin.Forms

A utilização da classe de serviço Signalr no Xamarin.Forms é realizada pela Associação dos `SignalRService` eventos de classe na `MainPage` classe code-behind.

O `Connected` evento na `SignalRService` classe é acionado quando uma conexão de signalr é concluída com êxito. O `ConnectionFailed` evento na `SignalRService` classe é acionado quando uma conexão de signalr falha. O `SignalR_ConnectionChanged` método do manipulador de eventos está associado a ambos os eventos no `MainPage` Construtor. Esse manipulador de eventos atualiza os Estados de botão conectar e enviar com base no argumento de conexão `success` e adiciona a mensagem fornecida pelo evento à fila de chat usando o `AddMessage` método. O código a seguir mostra o `SignalR_ConnectionChanged` método do manipulador de eventos:

```csharp
void SignalR_ConnectionChanged(object sender, bool success, string message)
{
    connectButton.Text = "Connect";
    connectButton.IsEnabled = !success;
    sendButton.IsEnabled = success;

    AddMessage($"Server connection changed: {message}");
}
```

O `NewMessageReceived` evento na `SignalRService` classe é acionado quando uma nova mensagem é recebida do serviço de signaler do Azure. O `SignalR_NewMessageReceived` método do manipulador de eventos está associado ao `NewMessageReceived` evento no `MainPage` Construtor. Esse manipulador de eventos converte o `Message` objeto de entrada em uma cadeia de caracteres e o adiciona à fila de chat usando o `AddMessage` método. O código a seguir mostra o `SignalR_NewMessageReceived` método do manipulador de eventos:

```csharp
void SignalR_NewMessageReceived(object sender, Model.Message message)
{
    string msg = $"{message.Name} ({message.TimeReceived}) - {message.Text}";
    AddMessage(msg);
}
```

O `AddMessage` método adiciona uma nova mensagem como um `Label` objeto à fila de chat. O `AddMessage` método é geralmente chamado por manipuladores de eventos de fora do thread da interface do usuário principal, portanto, ele força as atualizações da interface do usuário a ocorrer no thread principal para evitar exceções. O código a seguir mostra o `AddMessage` método:

```csharp
void AddMessage(string message)
{
    Device.BeginInvokeOnMainThread(() =>
    {
        Label label = new Label
        {
            Text = message,
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.Start
        };

        messageList.Children.Add(label);
    });
}
```

## <a name="test-the-application"></a>Testar o aplicativo

O aplicativo de chat do Signalr pode ser testado no iOS, Android e UWP, desde que você tenha:

1. Criou um serviço de Signaler do Azure.
1. Criou um aplicativo Azure Functions.
1. Personalizado o arquivo **Constants.cs** com o ponto de extremidade do aplicativo Azure functions.

Depois que essas etapas forem concluídas e o aplicativo for executado, clicar no botão **conectar** formará uma conexão com o serviço de Signaler do Azure. Digitar uma mensagem e clicar no botão **Enviar** resulta em mensagens que aparecem na fila de chat em qualquer aplicativo móvel conectado.

## <a name="related-links"></a>Links relacionados

* [Criando aplicativos móveis em tempo real com o Xamarin e o Signalr](https://mybuild.techcommunity.microsoft.com/sessions/77333/)
* [Introdução ao SignalR](/aspnet/signalr/overview/getting-started/introduction-to-signalr)
* [Introdução ao Azure Functions](/azure/azure-functions/functions-overview)
* [Documentação do Azure Functions](/azure/azure-functions/)
* [Exemplo de chat do Signalr MVVM](https://github.com/lbugnion/sample-xamarin-signalr)
