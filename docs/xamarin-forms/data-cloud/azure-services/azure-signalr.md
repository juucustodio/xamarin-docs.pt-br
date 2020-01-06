---
title: Serviço do Azure Signalr com Xamarin. Forms
description: Introdução ao serviço de Signaler do Azure e Azure Functions com Xamarin. Forms
ms.prod: xamarin
ms.assetid: 1B9A69EF-C200-41BF-B098-D978D7F9CD8F
author: profexorgeek
ms.author: jusjohns
ms.date: 06/07/2019
ms.openlocfilehash: e95dd72513562bba9fb513c4742e476bc7be0c94
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487394"
---
# <a name="azure-signalr-service-with-xamarinforms"></a>Serviço do Azure Signalr com Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azuresignalr/)

ASP.NET Core Signalr é um modelo de aplicativo que simplifica o processo de adicionar comunicação em tempo real aos aplicativos. O serviço de Signaler do Azure permite o desenvolvimento e a implantação rápidos de aplicativos Signaler escalonáveis. Azure Functions são métodos de código sem servidor de curta duração que podem ser combinados para formar aplicativos escalonáveis e orientados a eventos.

Este artigo e um exemplo mostram como combinar o serviço de sinalização do Azure e Azure Functions com o Xamarin. Forms, para fornecer mensagens em tempo real a clientes conectados.

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

[Captura de tela de ![da criação do aplicativo Azure Functions](azure-signalr-images/azure-functions-app-cropped.png)](azure-signalr-images/azure-functions-app-full.png#lightbox)

O Azure Functions pode ser implantado em uma instância de aplicativo Azure Functions do Visual Studio 2019. As seções a seguir descrevem a implantação de duas funções no aplicativo de exemplo para uma instância de aplicativo Azure Functions.

### <a name="build-azure-functions-in-visual-studio-2019"></a>Criar Azure Functions no Visual Studio 2019

O aplicativo de exemplo contém uma biblioteca de classes chamada **ChatServer**, que inclui dois Azure Functions sem servidor em arquivos chamados **Negotiate.cs** e **Talk.cs**.

A função `Negotiate` responde a solicitações da Web com um objeto `SignalRConnectionInfo` que contém uma propriedade `AccessToken` e uma propriedade `Url`. O aplicativo móvel usa esses valores para se registrar no Hub do Signalr. O código a seguir mostra a função `Negotiate`:

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

A função `Talk` responde às solicitações HTTP POST que fornecem um objeto Message no corpo da POSTAgem. O corpo da POSTAgem é transformado em um `SignalRMessage` e encaminhado para o Hub do Signalr. O código a seguir mostra a função `Talk`:

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

## <a name="integrate-azure-signalr-service-with-xamarinforms"></a>Integrar o serviço do Azure Signalr com Xamarin. Forms

A integração entre o serviço do Signalr do Azure e o aplicativo Xamarin. Forms é uma classe de serviço de sinalização que é instanciada na classe `MainPage` com manipuladores de eventos atribuídos a três eventos. Para obter mais informações sobre esses manipuladores de eventos, consulte [usar a classe de serviço signalr no Xamarin. Forms](#use-the-signalr-service-class-in-xamarinforms).

O aplicativo de exemplo inclui uma classe **Constants.cs** que deve ser personalizada com o ponto de extremidade da URL do seu aplicativo Azure functions. Defina o valor da propriedade `HostName` para o endereço do aplicativo Azure Functions. O código a seguir mostra as propriedades **Constants.cs** com um exemplo `HostName` valor:

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
> A propriedade `Username` no arquivo de exemplo de aplicativo **Constants.cs** usa o valor de `RuntimePlatform` do dispositivo como o nome de usuário. Isso torna mais fácil testar dispositivos entre plataformas e identificar qual dispositivo está enviando a mensagem. Em um aplicativo do mundo real, esse valor provavelmente seria um nome de usuário exclusivo, coletado durante um processo de inscrição ou de entrada.

### <a name="the-signalr-service-class"></a>A classe de serviço Signalr

A classe `SignalRService` no projeto **ChatClient** no aplicativo de exemplo mostra uma implementação que invoca funções em um aplicativo Azure Functions para se conectar a um serviço de Signaler do Azure.

O método de `SendMessageAsync` na classe `SignalRService` é usado para enviar mensagens aos clientes conectados ao serviço de Signaler do Azure. Esse método executa uma solicitação HTTP POST para a função **Talk** hospedada no aplicativo Azure functions, incluindo um objeto `Message` SERIALIZADO em JSON como a carga de postagem. A função **Talk** passa a mensagem para o serviço de signaler do Azure para difusão a todos os clientes conectados. O código a seguir mostra o método `SendMessageAsync`:

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

O método `ConnectAsync` na classe `SignalRService` executa uma solicitação HTTP GET para a função **Negotiate** hospedada no aplicativo Azure functions. A função **Negotiate** retorna JSON que é desserializado em uma instância da classe `NegotiateInfo`. Depois que o objeto de `NegotiateInfo` é recuperado, ele é usado para se registrar diretamente com o serviço de Signaler do Azure usando uma instância da classe `HubConnection`.

ASP.NET Core Signalr converte os dados de entrada da conexão aberta em mensagens e permite que os desenvolvedores definam tipos de mensagem e associem manipuladores de eventos a mensagens de entrada por tipo. O método `ConnectAsync` registra um manipulador de eventos para o nome da mensagem definido no arquivo **Constants.cs** do aplicativo de exemplo, que é "NewMessage" por padrão.

O código a seguir mostra o método `ConnectAsync`:

```csharp
public async Task ConnectAsync()
{
    try
    {
        IsBusy = true;

        string negotiateJson = await client.GetStringAsync($"{Constants.HostName}/api/negotiate");
        NegotiateInfo negotiate = JsonConvert.DeserializeObject<NegotiateInfo>(negotiateJson);
        HubConnection connection = new HubConnectionBuilder()
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

O método `AddNewMessage` é associado como o manipulador de eventos na mensagem `ConnectAsync`, conforme mostrado no código anterior. Quando uma mensagem é recebida, o método `AddNewMessage` é chamado com os dados da mensagem fornecidos como um `JObject`. O método `AddNewMessage` converte o `JObject` em uma instância da classe `Message` e, em seguida, invoca o manipulador para `NewMessageReceived` se um tiver sido associado. O código a seguir mostra o método `AddNewMessage`:

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

### <a name="use-the-signalr-service-class-in-xamarinforms"></a>Usar a classe de serviço Signalr no Xamarin. Forms

Utilizar a classe de serviço Signalr no Xamarin. Forms é feito ligando os eventos de classe `SignalRService` na classe code-behind `MainPage`.

O evento `Connected` na classe `SignalRService` é acionado quando uma conexão de Signalr é concluída com êxito. O evento de `ConnectionFailed` na classe `SignalRService` é acionado quando uma conexão de Signalr falha. O método do manipulador de eventos `SignalR_ConnectionChanged` está associado a ambos os eventos no Construtor `MainPage`. Esse manipulador de eventos atualiza os Estados de botão conectar e enviar com base no argumento de `success` de conexão e adiciona a mensagem fornecida pelo evento à fila de chat usando o método `AddMessage`. O código a seguir mostra o `SignalR_ConnectionChanged` método do manipulador de eventos:

```csharp
void SignalR_ConnectionChanged(object sender, bool success, string message)
{
    connectButton.Text = "Connect";
    connectButton.IsEnabled = !success;
    sendButton.IsEnabled = success;

    AddMessage($"Server connection changed: {message}");
}
```

O evento `NewMessageReceived` na classe `SignalRService` é disparado quando uma nova mensagem é recebida do serviço de Signalr do Azure. O método do manipulador de eventos `SignalR_NewMessageReceived` está associado ao evento `NewMessageReceived` no Construtor `MainPage`. Esse manipulador de eventos converte o objeto de entrada `Message` em uma cadeia de caracteres e o adiciona à fila de chat usando o método `AddMessage`. O código a seguir mostra o `SignalR_NewMessageReceived` método do manipulador de eventos:

```csharp
void SignalR_NewMessageReceived(object sender, Model.Message message)
{
    string msg = $"{message.Name} ({message.TimeReceived}) - {message.Text}";
    AddMessage(msg);
}
```

O método `AddMessage` adiciona uma nova mensagem como um objeto `Label` à fila de chat. O método `AddMessage` geralmente é chamado por manipuladores de eventos de fora do thread da interface do usuário principal, portanto, ele força as atualizações da interface do usuário a ocorrer no thread principal para evitar exceções. O código a seguir mostra o método `AddMessage`:

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
