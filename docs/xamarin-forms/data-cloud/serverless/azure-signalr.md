---
title: Serviço Azure SignalR com xamarin. Forms
description: Introdução ao serviço do Azure SignalR e Azure Functions com o xamarin. Forms
ms.prod: xamarin
ms.assetid: 1B9A69EF-C200-41BF-B098-D978D7F9CD8F
author: profexorgeek
ms.author: jusjohns
ms.date: 06/07/2019
ms.openlocfilehash: d79ffb844a65c145fd6cb22a5a3e1dfc7a6bfe41
ms.sourcegitcommit: 0f78ec17210b915b43ddab75937de8063e472c70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2019
ms.locfileid: "67418119"
---
# <a name="azure-signalr-service-with-xamarinforms"></a>Serviço Azure SignalR com xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/AzureSignalR)

SignalR do ASP.NET Core é um modelo de aplicativo que simplifica o processo de adição de comunicação em tempo real aos aplicativos. Azure SignalR Service permite o rápido desenvolvimento e implantação de aplicativos escaláveis do SignalR. O Azure Functions são métodos de código sem servidor, e de curta duração que podem ser combinados para aplicativos de forma escalonável, controlado por evento.

Este artigo e o exemplo mostram como combinar o serviço do Azure SignalR e Azure Functions com o xamarin. Forms para entregar mensagens em tempo real para os clientes conectados.

## <a name="create-an-azure-signalr-service-and-azure-functions-app"></a>Criar um serviço Azure SignalR e o aplicativo do Azure Functions

O aplicativo de exemplo consiste em três componentes principais: um hub de serviço do Azure SignalR, uma instância do Azure Functions com duas funções e um aplicativo móvel que pode enviar e receber mensagens. Esses componentes interagem da seguinte maneira:

1. O aplicativo móvel chama um **Negotiate** Azure Function para obter informações sobre o hub do SignalR.
1. O aplicativo móvel usa as informações de negociação para se registrar com o hub do SignalR e uma conexão de formulários.
1. Após o registro, o aplicativo móvel envia mensagens para o **falar** função do Azure.
1. O **falar** função passa a mensagem de entrada para o hub do SignalR.
1. O hub do SignalR transmite a mensagem para todas as instâncias de aplicativo móvel conectado, incluindo o remetente original.

> [!NOTE]
> O **Negotiate** e **falar** funções no aplicativo de exemplo podem ser executadas localmente usando o Visual Studio de 2019 e as ferramentas de tempo de execução do Azure. No entanto, o serviço do Azure SignalR não pode ser emulado localmente e é difícil expor hospedadas localmente Azure Functions para dispositivos físicos ou virtuais para teste. É recomendável que você implante as funções do Azure a uma instância de aplicativo do Azure Functions, pois isso permite que os testes de plataforma cruzada. Para obter detalhes de implantação, consulte [implantar Azure Functions com o Visual Studio de 2019](#deploy-azure-functions-with-visual-studio-2019).

### <a name="create-an-azure-signalr-service"></a>Criar um serviço Azure SignalR

Um serviço do Azure SignalR podem ser criado, escolhendo **criar um recurso** no canto superior esquerdo do portal do Azure e procurando **SignalR**. O serviço do Azure SignalR podem ser criado na camada gratuita. O serviço do Azure SignalR deve estar no **sem servidor** modo de serviço. Se você escolher acidentalmente o padrão ou modo de serviço clássico, você pode alterá-lo mais tarde nas propriedades do serviço do Azure SignalR.

Captura de tela a seguir mostra a criação de um novo serviço SignalR do Azure:

![Criação de captura de tela de serviço do Azure SignalR no portal do Azure](azure-signalr-images/azure-signalr-create.png "criando um serviço do Azure SignalR")

Depois de criado, o **teclas** seção de um serviço do Azure SignalR contém um **cadeia de caracteres de Conexão**, que é usado para conectar o aplicativo de funções do Azure ao hub SignalR. Captura de tela a seguir mostra onde encontrar a cadeia de caracteres de conexão no serviço Azure SignalR:

![Captura de tela do Azure SignalR cadeia de caracteres de conexão no portal do Azure](azure-signalr-images/azure-signalr-connection-string.png "cadeia de caracteres de conexão do Azure SignalR")

Essa cadeia de caracteres de conexão é usada para [implantar Azure Functions com o Visual Studio de 2019](#deploy-azure-functions-with-visual-studio-2019).

### <a name="create-an-azure-functions-app"></a>Criar um aplicativo de funções do Azure

Para testar o aplicativo de exemplo, você deve criar um novo aplicativo de funções do Azure no portal do Azure. Anote o **nome do aplicativo** como esta URL é usada no aplicativo de exemplo **Constants.cs** arquivo. Captura de tela a seguir mostra a criação de um novo aplicativo de funções do Azure chamado "xdocsfunctions":

[![Criação de captura de tela de aplicativo do Azure Functions](azure-signalr-images/azure-functions-app-cropped.png)](azure-signalr-images/azure-functions-app-full.png#lightbox)

O Azure functions podem ser implantado em uma instância de aplicativo do Azure Functions de 2019 do Visual Studio. As seções a seguir descrevem a implantação de duas funções no aplicativo de exemplo para uma instância de aplicativo do Azure Functions.

### <a name="build-azure-functions-in-visual-studio-2019"></a>Criar Azure Functions no Visual Studio de 2019

O aplicativo de exemplo contém uma biblioteca de classes chamada **ChatServer**, que inclui dois Azure Functions sem servidor nos arquivos chamados **Negotiate.cs** e **Talk.cs**.

O `Negotiate` função responde a solicitações da web com um `SignalRConnectionInfo` objeto que contém uma `AccessToken` propriedade e um `Url` propriedade. O aplicativo móvel usa esses valores para se registrar com o hub do SignalR. O seguinte código mostra o `Negotiate` função:

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

O `Talk` função responde a solicitações HTTP POST que fornecem um objeto de mensagem no corpo da POSTAGEM. O corpo do POST é transformado em um `SignalRMessage` e encaminhado para o hub do SignalR. O seguinte código mostra o `Talk` função:

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

Para saber mais sobre o Azure functions e aplicativos de funções do Azure, consulte [documentação do Azure Functions](/azure/azure-functions/).

### <a name="deploy-azure-functions-with-visual-studio-2019"></a>Implantar o Azure Functions com o Visual Studio de 2019

2019 do Visual Studio permite que você implante funções em um aplicativo de funções do Azure. Hospedado no Azure functions facilitam o teste de plataforma cruzada, fornecendo um ponto de extremidade de teste acessível para todos os dispositivos.

Clicando duas vezes o aplicativo de funções de exemplo e escolhendo **publicar** inicia a caixa de diálogo Publicar funções ao aplicativo de funções do Azure. Se você seguiu as etapas anteriores para configurar um aplicativo de funções do Azure, você pode escolher **selecionar existente** para publicar os aplicativos de exemplo para seu aplicativo de funções do Azure. Captura de tela a seguir mostra as opções de caixa de diálogo Publicar no Visual Studio de 2019:

![Caixa de diálogo de opções de publicação no Visual Studio de 2019](azure-signalr-images/vs-publish-target.png "opções de publicação no Visual Studio de 2019")

Depois de entrar sua conta da Microsoft, você pode localizar e escolher o seu aplicativo de funções do Azure como o destino de publicação. Captura de tela a seguir mostra um exemplo de aplicativo do Azure Functions na caixa de diálogo publicação 2019 do Visual Studio:

![Um aplicativo de funções do Azure na caixa de diálogo de publicação do Visual Studio de 2019](azure-signalr-images/vs-app-selection.png "aplicativo de funções do Azure na caixa de diálogo de publicação do Visual Studio de 2019")

Depois de selecionar um aplicativo de funções do Azure, instância, a URL do Site, configuração e outras informações sobre o aplicativo de funções do Azure de destino são exibidos. Escolher **editar configurações de serviço de aplicativo do Azure** e insira sua cadeia de conexão na **remoto** campo. A cadeia de conexão é usada pelo **Negotiate** e **falar** funciona para se conectar ao serviço Azure SignalR e está disponível na **chaves** seção do Azure SignalR Serviço no portal do Azure. Para obter mais informações sobre a cadeia de caracteres de conexão, consulte [criar um serviço do Azure SignalR](#create-an-azure-signalr-service).

Depois que você inseriu a cadeia de caracteres de conexão, você pode clicar em **publicar** para implantar suas funções para o aplicativo de funções do Azure. Uma vez concluído, as funções serão listadas no aplicativo de funções do Azure no portal do Azure. Captura de tela a seguir mostra as funções publicadas no portal do Azure:

![Funções publicadas no aplicativo de funções do Azure](azure-signalr-images/azure-functions-deployed.png "funções publicadas no aplicativo de funções do Azure")

## <a name="integrate-azure-signalr-service-with-xamarinforms"></a>Integrar o serviço Azure SignalR com xamarin. Forms

A integração entre o serviço do Azure SignalR e o aplicativo xamarin. Forms é uma classe de serviço do SignalR é instanciada no `MainPage` classe com manipuladores de eventos atribuídos aos três eventos. Para obter mais informações sobre esses manipuladores de eventos, consulte [usar a classe de serviço SignalR no xamarin. Forms](#use-the-signalr-service-class-in-xamarinforms).

O aplicativo de exemplo inclui um **Constants.cs** classe deve ser personalizado com o ponto de extremidade de URL do seu aplicativo de funções do Azure. Defina o valor da `HostName` propriedade para o seu endereço de aplicativo do Azure Functions. O código a seguir mostra a **Constants.cs** propriedades com um exemplo `HostName` valor:

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
> O `Username` propriedade no aplicativo de exemplo **Constants.cs** arquivo usa o dispositivo `RuntimePlatform` valor como o nome de usuário. Isso torna mais fácil para plataformas de dispositivos de teste e identificar qual dispositivo está enviando a mensagem. Em um aplicativo do mundo real, esse valor provavelmente seria um nome de usuário exclusivo, coletados durante um sinal de inscrever ou entrar no processo.

### <a name="the-signalr-service-class"></a>A classe de serviço SignalR

O `SignalRService` classe de **ChatClient** projeto no aplicativo de exemplo mostra uma implementação que invoca as funções em um aplicativo de funções do Azure para se conectar a um serviço do Azure SignalR.

O `SendMessageAsync` método no `SignalRService` classe é usada para enviar mensagens para os clientes conectados ao serviço Azure SignalR. Esse método executa uma solicitação HTTP POST para o **falar** função hospedada no aplicativo do Azure Functions, incluindo um serializada pelo JSON `Message` objeto como a carga de POSTAGEM. O **falar** função passa a mensagem para o serviço do Azure SignalR para clientes de difusão para todos. O seguinte código mostra o `SendMessageAsync` método:

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

O `ConnectAsync` método na `SignalRService` classe executa uma solicitação HTTP GET para o **Negotiate** função hospedada no aplicativo de funções do Azure. O **Negotiate** função retorna o JSON é desserializado em uma instância da `NegotiateInfo` classe. Uma vez a `NegotiateInfo` objeto é recuperado, ele é usado para registrar-se diretamente com o serviço do Azure SignalR usando uma instância do `HubConnection` classe.

SignalR do ASP.NET Core converte dados de entrada da conexão aberta em mensagens e permite aos desenvolvedores definir tipos de mensagem e ligar manipuladores de eventos para mensagens de entrada por tipo. O `ConnectAsync` método registra um manipulador de eventos para o nome de mensagem definido no aplicativo de exemplo **Constants.cs** arquivo, que é "newMessage" por padrão.

O seguinte código mostra o `ConnectAsync` método:

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

O `AddNewMessage` método está vinculado como o manipulador de eventos no `ConnectAsync` conforme mostrado no código anterior da mensagem. Quando uma mensagem é recebida, o `AddNewMessage` método é chamado com os dados da mensagem fornecidos como um `JObject`. O `AddNewMessage` método converte o `JObject` a uma instância das `Message` de classe e, em seguida, invoca o manipulador para `NewMessageReceived` se uma tiver sido associada. O seguinte código mostra o `AddNewMessage` método:

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

### <a name="use-the-signalr-service-class-in-xamarinforms"></a>Use a classe de serviço SignalR no xamarin. Forms

Utilizando a classe de serviço SignalR no xamarin. Forms é realizado pela associação a `SignalRService` eventos de classe a `MainPage` classe code-behind.

O `Connected` evento no `SignalRService` classe é acionado quando uma conexão SignalR é concluído com êxito. O `ConnectionFailed` evento no `SignalRService` classe é acionado quando uma conexão SignalR falha. O `SignalR_ConnectionChanged` método manipulador de eventos está associado a ambos os eventos no `MainPage` construtor. Esse manipulador de eventos atualiza os estados do botão connect e envio com base em que a conexão `success` argumento e adiciona a mensagem fornecida pelo evento para a fila de bate-papo usando o `AddMessage` método. O seguinte código mostra o `SignalR_ConnectionChanged` método manipulador de eventos:

```csharp
void SignalR_ConnectionChanged(object sender, bool success, string message)
{
    connectButton.Text = "Connect";
    connectButton.IsEnabled = !success;
    sendButton.IsEnabled = success;

    AddMessage($"Server connection changed: {message}");
}
```

O `NewMessageReceived` evento no `SignalRService` classe é disparado quando uma nova mensagem é recebida do serviço Azure SignalR. O `SignalR_NewMessageReceived` método manipulador de eventos está associado a `NewMessageReceived` evento no `MainPage` construtor. Esse manipulador de eventos converte de entrada `Message` em uma cadeia de caracteres do objeto e o adiciona à fila de bate-papo usando o `AddMessage` método. O seguinte código mostra o `SignalR_NewMessageReceived` método manipulador de eventos:

```csharp
void SignalR_NewMessageReceived(object sender, Model.Message message)
{
    string msg = $"{message.Name} ({message.TimeReceived}) - {message.Text}";
    AddMessage(msg);
}
```

O `AddMessage` método adiciona uma nova mensagem como um `Label` objeto para a fila de bate-papo. O `AddMessage` método geralmente é chamado por manipuladores de eventos de fora do thread principal da interface do usuário, para que ele força as atualizações da interface do usuário para ocorrer no thread principal para impedir exceções. O seguinte código mostra o `AddMessage` método:

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

O aplicativo de chat SignalR pode ser testado no iOS, Android e UWP desde que você tenha:

1. Criado um serviço Azure SignalR.
1. Criou um aplicativo de funções do Azure.
1. Personalizou a **Constants.cs** arquivo com o ponto de extremidade do aplicativo do Azure Functions.

Depois que essas etapas forem concluídas e o aplicativo é executado, clicando na **Connect** forma de botão uma conexão com o serviço do Azure SignalR. Digitando uma mensagem e clicando na **enviar** conectado de resultados de botão em mensagens que aparecem na fila de bate-papo em qualquer aplicativo móvel.

## <a name="related-links"></a>Links relacionados

* [Criação de aplicativos móveis em tempo real com o Xamarin e SignalR](https://mybuild.techcommunity.microsoft.com/sessions/77333/)
* [Introdução ao SignalR](/aspnet/signalr/overview/getting-started/introduction-to-signalr)
* [Introdução ao Azure Functions](/azure/azure-functions/functions-overview)
* [Documentação de funções do Azure](/azure/azure-functions/)
* [Exemplo de chat SignalR MVVM](https://github.com/lbugnion/sample-xamarin-signalr)
