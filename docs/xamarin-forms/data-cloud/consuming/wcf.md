---
title: Consumir um serviço Web do Windows Communication Foundation (WCF)
description: Este artigo demonstra como consumir um serviço WCF SOAP Simple Object Access Protocol () de um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: 5696FF04-EF21-4B7A-8C8B-26DE28B5C0AD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/28/2019
ms.openlocfilehash: 7106c0aed03800d3479471caab0974be3c09c1f8
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59239908"
---
# <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>Consumir um serviço Web do Windows Communication Foundation (WCF)

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF/)

_O WCF é a estrutura unificada da Microsoft para a criação de aplicativos orientados a serviço. Ele permite aos desenvolvedores compilar aplicativos distribuídos seguros, confiáveis, transacionados e interoperáveis. Este artigo demonstra como consumir um serviço WCF SOAP Simple Object Access Protocol () de um aplicativo xamarin. Forms._

WCF descreve um serviço com uma variedade de contratos diferentes incluindo:

- **Contratos de dados** – definir as estruturas de dados que formam a base para o conteúdo dentro de uma mensagem.
- **Contratos de mensagem** – compor mensagens de contratos de dados existente.
- **Contratos de falha** – permitir que as falhas SOAP personalizadas sejam especificados.
- **Contratos de serviço** – especificar as operações que dão suporte a serviços e as mensagens necessárias para interagir com cada operação. Eles também pode especificar qualquer comportamento de falha personalizado que pode ser associado a operações em cada serviço.

Há diferenças entre dá suporte a serviços de Web do ASP.NET (ASMX) e o WCF, mas o WCF os mesmos recursos que fornece ASMX – mensagens SOAP sobre HTTP. Para obter mais informações sobre como consumir um serviço ASMX, consulte [consumindo ASP.NET Web Services (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md).

> [!IMPORTANT]
> O suporte de plataforma do Xamarin para o WCF é limitado a texto codificado mensagens SOAP sobre HTTP/HTTPS usando o `BasicHttpBinding` classe.
>
> Suporte do WCF requer o uso das ferramentas disponíveis somente em um ambiente do Windows para gerar o proxy e hospedar o TodoWCFService. Compilar e testar o aplicativo iOS exigirá a implantação de TodoWCFService em um computador Windows ou como um serviço web do Azure.
>
> Aplicativos nativos do Xamarin. Forms normalmente compartilham código com uma biblioteca de classes .NET Standard. No entanto, .NET Core não suporta no momento WCF para que o projeto compartilhado deve ser uma biblioteca de classes portátil herdado. Para obter informações sobre o suporte do WCF no .NET Core, consulte [escolhendo entre o .NET Core e .NET Framework para aplicativos de servidor](/dotnet/standard/choosing-core-framework-server).

A solução de aplicativo de exemplo inclui um serviço WCF que pode ser executado localmente e é mostrado na seguinte captura de tela:

![](wcf-images/portal.png "Aplicativo de exemplo")

> [!NOTE]
> No iOS 9 e superior, a segurança de transporte de aplicativo (ATS) impõe conexões seguras entre recursos da internet (como o servidor de back-end do aplicativo) e o aplicativo, impedindo assim a divulgação acidental de informações confidenciais. Desde que o ATS é habilitado por padrão em aplicativos criados para o iOS 9, todas as conexões serão sujeitos a requisitos de segurança ATS. Se as conexões não atender a esses requisitos, eles falharão com uma exceção.
>
> ATS poderá ser aceito de se ele não é possível usar o `HTTPS` de protocolo e proteger a comunicação para recursos da internet. Isso pode ser feito atualizando o aplicativo **Info. plist** arquivo. Para obter mais informações, consulte [segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md).

## <a name="consume-the-web-service"></a>Consumir o serviço web

O serviço WCF fornece as seguintes operações:

|Operação|Descrição|Parâmetros|
|--- |--- |--- |
|GetTodoItems|Obter uma lista de itens pendentes|
|CreateTodoItem|Criar um novo item pendente|Um XML serializado TodoItem|
|EditTodoItem|Atualizar um item pendente|Um XML serializado TodoItem|
|DeleteTodoItem|Excluir um item pendente|Um XML serializado TodoItem|

Para obter mais informações sobre o modelo de dados usado no aplicativo, consulte [modelagem de dados](~/xamarin-forms/data-cloud/walkthrough.md).

Um *proxy* deve ser gerado para consumir um serviço WCF, que permite que o aplicativo para se conectar ao serviço. O proxy é construído por consumo metadados de serviço que definem os métodos e a configuração de serviço associado. Esses metadados são expostos na forma de um documento de descrição linguagem WSDL (Web Services) que é gerado pelo serviço web. O proxy pode ser compilado usando o Microsoft WCF Web Service Reference Provider no Visual Studio 2017 para adicionar uma referência de serviço para o serviço web a uma biblioteca .NET Standard. Uma alternativa para criar o proxy usando o Microsoft WCF Web Service Reference Provider no Visual Studio 2017 é usar a ferramenta Utilitário de metadados de ServiceModel (svcutil.exe). Para obter mais informações, consulte [ferramenta de utilitário de metadados ServiceModel (Svcutil.exe)](/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe/).

As classes de proxy geradas fornecem métodos para consumir os serviços web que usam o padrão de design do modelo de programação assíncrona (APM). Nesse padrão, uma operação assíncrona é implementada como dois métodos chamados *BeginOperationName* e *EndOperationName*, que começam e terminam a operação assíncrona.

O *BeginOperationName* método inicia a operação assíncrona e retorna um objeto que implementa o `IAsyncResult` interface. Depois de chamar *BeginOperationName*, um aplicativo pode continuar a executar as instruções no thread de chamada, enquanto a operação assíncrona ocorre em um pool de threads.

Para cada chamada para *BeginOperationName*, o aplicativo também deve chamar *EndOperationName* para obter os resultados da operação. O valor de retorno *EndOperationName* é do mesmo tipo retornado pelo método de serviço da web síncronas. Por exemplo, o `EndGetTodoItems` método retorna uma coleção de `TodoItem` instâncias. O *EndOperationName* método também inclui um `IAsyncResult` parâmetro deve ser definido como a instância retornada pela chamada correspondente para o *BeginOperationName* método.

A tarefa paralela TPL (biblioteca) pode simplificar o processo de consumir um par de métodos de início/término do APM, encapsulando as operações assíncronas no mesmo `Task` objeto. Esse encapsulamento é fornecido pelas várias sobrecargas do `TaskFactory.FromAsync` método.

Para obter mais informações sobre o APM, consulte [modelo de programação assíncrona](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) e [.NET Framework programação assíncrona tradicional e TPL](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) no MSDN.

### <a name="create-the-todoserviceclient-object"></a>Criar o objeto TodoServiceClient

Fornece a classe proxy gerada a `TodoServiceClient` classe, que é usado para se comunicar com o serviço WCF por meio de HTTP. Ele fornece funcionalidade para invocar métodos de serviço web, como operações assíncronas de um URI identificado a instância de serviço. Para obter mais informações sobre as operações assíncronas, consulte [visão geral do suporte assíncrono](~/cross-platform/platform/async.md).

O `TodoServiceClient` instância é declarada no nível de classe, de modo que o objeto reside para desde que o aplicativo precisa consumir o serviço WCF, conforme mostrado no exemplo de código a seguir:

```csharp
public class SoapService : ISoapService
{
  ITodoService todoService;
  ...

  public SoapService ()
  {
    todoService = new TodoServiceClient (
      new BasicHttpBinding (),
      new EndpointAddress (Constants.SoapUrl));
  }
  ...
}
```

O `TodoServiceClient` instância é configurada com informações e um endereço de ponto de extremidade de associação. Uma associação é usada para especificar o transporte, codificação e detalhes de protocolo necessários para aplicativos e serviços para se comunicar entre si. O `BasicHttpBinding` Especifica que as mensagens SOAP texto codificado serão enviadas através do protocolo de transporte HTTP. Especificação de um endereço de ponto de extremidade permite que o aplicativo para se conectar a diferentes instâncias do serviço do WCF, desde que há várias instâncias publicadas.

Para obter mais informações sobre como configurar a referência de serviço, consulte [Configurando a referência de serviço](~/cross-platform/data-cloud/web-services/index.md#wcf).

### <a name="create-data-transfer-objects"></a>Criar objetos de transferência de dados

O aplicativo de exemplo usa o `TodoItem` classe para dados de modelo. Para armazenar um `TodoItem` item no serviço da web, ele deve primeiro ser convertido para o proxy gerado `TodoItem` tipo. Isso é feito usando o `ToWCFServiceTodoItem` método, conforme mostrado no exemplo de código a seguir:

```csharp
TodoWCFService.TodoItem ToWCFServiceTodoItem (TodoItem item)
{
  return new TodoWCFService.TodoItem
  {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}
```

Esse método simplesmente cria um novo `TodoWCFService.TodoItem` da instância e define cada propriedade para a propriedade idêntica do `TodoItem` instância.

Da mesma forma, quando os dados são recuperados do serviço da web, ele deve ser convertido do proxy gerado `TodoItem` de tipo para um `TodoItem` instância. Isso é feito com o `FromWCFServiceTodoItem` método, conforme mostrado no exemplo de código a seguir:

```csharp
static TodoItem FromWCFServiceTodoItem (TodoWCFService.TodoItem item)
{
  return new TodoItem
  {
    ID = item.ID,
    Name = item.Name,
    Notes = item.Notes,
    Done = item.Done
  };
}

```

Esse método simplesmente recupera os dados do proxy gerado `TodoItem` de tipo e define-a no recém-criado `TodoItem` instância.

### <a name="retrieve-data"></a>Recuperar dados

O `TodoServiceClient.BeginGetTodoItems` e `TodoServiceClient.EndGetTodoItems` métodos são usados para chamar o `GetTodoItems` operação fornecidos pelo serviço web. Esses métodos assíncronos são encapsulados em um `Task` do objeto, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync <ObservableCollection<TodoWCFService.TodoItem>> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);

  foreach (var item in todoItems)
  {
    Items.Add (FromWCFServiceTodoItem (item));
  }
  ...
}
```

O `Task.Factory.FromAsync` método cria uma `Task` que executa o `TodoServiceClient.EndGetTodoItems` método uma vez o `TodoServiceClient.BeginGetTodoItems` método é concluído, com o `null` parâmetro que indica que nenhum dado está sendo passado para o `BeginGetTodoItems` delegar. Por fim, o valor da `TaskCreationOptions` enumeração que especifica que o comportamento padrão para a criação e execução de tarefas deve ser usado.

O `TodoServiceClient.EndGetTodoItems` método retorna um `ObservableCollection` de `TodoWCFService.TodoItem` instâncias, que, em seguida, é convertido em um `List` de `TodoItem` instâncias para exibição.

### <a name="create-data"></a>Criar dados

O `TodoServiceClient.BeginCreateTodoItem` e `TodoServiceClient.EndCreateTodoItem` métodos são usados para chamar o `CreateTodoItem` operação fornecidos pelo serviço web. Esses métodos assíncronos são encapsulados em um `Task` do objeto, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToWCFServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginCreateTodoItem,
    todoService.EndCreateTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

O `Task.Factory.FromAsync` método cria uma `Task` que executa o `TodoServiceClient.EndCreateTodoItem` método uma vez o `TodoServiceClient.BeginCreateTodoItem` método é concluído, com o `todoItem` parâmetro sendo os dados que são passados para o `BeginCreateTodoItem` delegado para especificar o `TodoItem` a ser criado pelo serviço web. Por fim, o valor da `TaskCreationOptions` enumeração que especifica que o comportamento padrão para a criação e execução de tarefas deve ser usado.

A web service lança um `FaultException` se ele falhar ao criar o `TodoItem`, que é tratada pelo aplicativo.

### <a name="update-data"></a>Atualizar dados

O `TodoServiceClient.BeginEditTodoItem` e `TodoServiceClient.EndEditTodoItem` métodos são usados para chamar o `EditTodoItem` operação fornecidos pelo serviço web. Esses métodos assíncronos são encapsulados em um `Task` do objeto, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  var todoItem = ToWCFServiceTodoItem (item);
  ...
  await Task.Factory.FromAsync (
    todoService.BeginEditTodoItem,
    todoService.EndEditTodoItem,
    todoItem,
    TaskCreationOptions.None);
  ...
}
```

O `Task.Factory.FromAsync` método cria uma `Task` que executa o `TodoServiceClient.EndEditTodoItem` método uma vez o `TodoServiceClient.BeginCreateTodoItem` método é concluído, com o `todoItem` parâmetro sendo os dados que são passados para o `BeginEditTodoItem` delegado para especificar o `TodoItem` a ser atualizado pelo serviço web. Por fim, o valor da `TaskCreationOptions` enumeração que especifica que o comportamento padrão para a criação e execução de tarefas deve ser usado.

A web service lança um `FaultException` se ele não conseguir localizar ou atualizar o `TodoItem`, que é tratada pelo aplicativo.

### <a name="delete-data"></a>Excluir dados

O `TodoServiceClient.BeginDeleteTodoItem` e `TodoServiceClient.EndDeleteTodoItem` métodos são usados para chamar o `DeleteTodoItem` operação fornecidos pelo serviço web. Esses métodos assíncronos são encapsulados em um `Task` do objeto, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  ...
  await Task.Factory.FromAsync (
    todoService.BeginDeleteTodoItem,
    todoService.EndDeleteTodoItem,
    id,
    TaskCreationOptions.None);
  ...
}
```

O `Task.Factory.FromAsync` método cria uma `Task` que executa o `TodoServiceClient.EndDeleteTodoItem` método uma vez o `TodoServiceClient.BeginDeleteTodoItem` método é concluído, com o `id` parâmetro sendo os dados que são passados para o `BeginDeleteTodoItem` delegado para especificar o `TodoItem` a ser excluído pelo serviço web. Por fim, o valor da `TaskCreationOptions` enumeração que especifica que o comportamento padrão para a criação e execução de tarefas deve ser usado.

A web service lança um `FaultException` se ele não conseguir localizar ou excluir o `TodoItem`, que é tratada pelo aplicativo.

## <a name="configure-remote-access-to-iis-express"></a>Configurar o acesso remoto para o IIS Express
No Visual Studio 2017 ou Visual Studio de 2019, você deve ser capaz de testar o aplicativo UWP em um PC sem nenhuma configuração adicional. Testar os clientes do Android e iOS pode exigir que as etapas adicionais nesta seção. Ver [conectar-se a serviços da Web locais do iOS simuladores e emuladores Android](~/cross-platform/deploy-test/connect-to-local-web-services.md) para obter mais informações.

Por padrão, o IIS Express só responderá a solicitações para `localhost`. Dispositivos remotos (como um dispositivo Android, um iPhone ou até mesmo um simulador) não terá acesso ao seu serviço WCF local. Você precisará saber seu endereço IP de estação de trabalho do Windows 10 na rede local. Nesse exemplo, suponha que sua estação de trabalho tem o endereço IP `192.168.1.143`. As etapas a seguir explicam como configurar o Windows 10 e o IIS Express para aceitar conexões remotas e conecte-se ao serviço de um dispositivo físico ou virtual:

1. **Adicionar uma exceção ao Firewall do Windows**. Você deve abrir uma porta por meio do Firewall do Windows que os aplicativos em sua sub-rede podem usar para se comunicar com o serviço WCF. Crie uma regra de entrada abrindo 49393 de porta no firewall. Em um prompt de comando administrativo, execute este comando:
    ```
    netsh advfirewall firewall add rule name="TodoWCFService" dir=in protocol=tcp localport=49393 profile=private remoteip=localsubnet action=allow
    ```

1. **Configurar o IIS Express para conexões remotas aceitar**. Você pode configurar o IIS Express editando o arquivo de configuração para o IIS Express na **[diretório da solução]\.vs\config\applicationhost.config**. Localizar o `site` elemento com o nome `TodoWCFService`. Ele deve ser semelhante ao seguinte XML:

    ```xml
    <site name="TodoWCFService" id="2">
        <application path="/" applicationPool="Clr4IntegratedAppPool">
            <virtualDirectory path="/" physicalPath="C:\Users\tom\TodoWCF\TodoWCFService\TodoWCFService" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:49393:localhost" />
        </bindings>
    </site>
    ```

    Você precisará adicionar dois `binding` elementos para abrir a porta 49393 para o tráfego externo e o emulador do Android. A associação usa um `[IP address]:[port]:[hostname]` formato que especifica como o IIS Express irão responder às solicitações. As solicitações externas terão nomes de host que devem ser especificado como um `binding`. Adicione o seguinte XML para o `bindings` elemento, substituindo o endereço IP com seu próprio endereço IP:

    ```xml
    <binding protocol="http" bindingInformation="*:49393:192.168.1.143" />
    <binding protocol="http" bindingInformation="*:49393:127.0.0.1" />
    ```

    Depois das alterações a `bindings` elemento deve ser semelhante ao seguinte:

    ```xml
    <site name="TodoWCFService" id="2">
        <application path="/" applicationPool="Clr4IntegratedAppPool">
            <virtualDirectory path="/" physicalPath="C:\Users\tom\TodoWCF\TodoWCFService\TodoWCFService" />
        </application>
        <bindings>
            <binding protocol="http" bindingInformation="*:49393:localhost" />
            <binding protocol="http" bindingInformation="*:49393:192.168.1.143" />
            <binding protocol="http" bindingInformation="*:49393:127.0.0.1" />
        </bindings>
    </site>
    ```

    >[!IMPORTANT]
    >Por padrão, o IIS Express não aceita conexões de fontes externas, por motivos de segurança. Para habilitar conexões de dispositivos remotos você deve executar o IIS Express com permissões administrativas. A maneira mais fácil de fazer isso é executar o Visual Studio 2017 com permissões administrativas. Isso inicializará o IIS Express com permissões administrativas ao executar o TodoWCFService.

    Com essas etapas concluídas, você deve ser capaz de executar o TodoWCFService e conecte-se com outros dispositivos na sua sub-rede. Você pode testar isso executando seu aplicativo e visitar `http://localhost:49393/TodoService.svc`. Se você receber um **solicitação incorreta** erros ao visitar essa URL e sua `bindings` pode estar incorreto na configuração IIS Express (a solicitação está alcançando o IIS Express, mas está sendo rejeitada). Se você receber um erro diferente pode ser que seu aplicativo não está em execução ou seu firewall está configurado incorretamente.

    Para permitir que o IIS Express manter em execução e que atende o serviço, desative o **editar e continuar** opção **propriedades do projeto > Web > depuradores**.

1. **Personalizar o ponto de extremidade dispositivos usam para acessar o serviço**. Esta etapa envolve a configuração do aplicativo cliente, em execução em um dispositivo físico ou emulado, para acessar o serviço do WCF.

    O emulador do Android utiliza um proxy interno que impede que o emulador acessem diretamente da máquina host `localhost` endereço. Em vez disso, o endereço `10.0.2.2` o emulador é roteado para `localhost` no computador host por meio de um proxy interno. Essas solicitações com proxy terá `127.0.0.1` como o nome do host no cabeçalho da solicitação, que é por isso que você criou a associação do IIS Express para esse nome de host nas etapas acima.

    O iOS Simulator é executado em um Mac build host, mesmo se você estiver usando o [remoto de iOS Simulator para Windows](~/tools/ios-simulator/index.md). Solicitações de rede pelo simulador terá o IP de estação de trabalho na rede local como o nome do host (neste exemplo tem `192.168.1.143`, mas seu endereço IP real provavelmente será diferente). Isso é por isso que você criou a associação do IIS Express para esse nome de host nas etapas acima.

    Verifique se o `SoapUrl` propriedade no **Constants.cs** arquivo no projeto TodoWCF (portátil) têm valores que estão corretos para sua rede:

    ```csharp
    public static string SoapUrl
    {
        get
        {
            var defaultUrl = "http://localhost:49393/TodoService.svc";

            if (Device.RuntimePlatform == Device.Android)
            {
                defaultUrl = "http://10.0.2.2:49393/TodoService.svc";
            }
            else if (Device.RuntimePlatform == Device.iOS)
            {
                defaultUrl = "http://192.168.1.143:49393/TodoService.svc";
            }

            return defaultUrl;
        }
    }
    ```

    Depois de configurar o **Constants.cs** com pontos de extremidade apropriados, você deve ser capaz de conectar-se para o TodoWCFService em execução em sua estação de trabalho do Windows 10 de dispositivos físicos ou virtuais.

## <a name="related-links"></a>Links relacionados

- [TodoWCF (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoWCF/)
- [Como: Criar um cliente do Windows Communication Foundation](https://docs.microsoft.com/dotnet/framework/wcf/how-to-create-a-wcf-client)
- [Ferramenta de utilitário de metadados ServiceModel (svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
