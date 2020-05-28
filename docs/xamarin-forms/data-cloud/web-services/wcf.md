---
title: ''
description: Este artigo demonstra como consumir um serviço de protocolo SOAP (Simple Object Access Protocol) do WCF a partir de um Xamarin.Forms aplicativo.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cf95427807e0179a608b428bc7e02499c9616fe7
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139146"
---
# <a name="consume-a-windows-communication-foundation-wcf-web-service"></a>Consumir um serviço Web Windows Communication Foundation (WCF)

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)

_O WCF é a estrutura unificada da Microsoft para a criação de aplicativos orientados a serviços. Ele permite que os desenvolvedores criem aplicativos distribuídos seguros, confiáveis, transacionados e interoperáveis. Este artigo demonstra como consumir um serviço de protocolo SOAP (Simple Object Access Protocol) do WCF a partir de um Xamarin.Forms aplicativo._

O WCF descreve um serviço com uma variedade de contratos diferentes, incluindo:

- **Contratos de dados** – defina as estruturas de dados que formam a base para o conteúdo de uma mensagem.
- **Contratos de mensagem** – compor mensagens de contratos de dados existentes.
- **Contratos de falha** – permitir que falhas SOAP personalizadas sejam especificadas.
- **Contratos de serviço** – especifique as operações que oferecem suporte aos serviços e as mensagens necessárias para interagir com cada operação. Eles também especificam qualquer comportamento de falha personalizado que possa ser associado a operações em cada serviço.

Há diferenças entre o ASMX (ASP.NET Web Services) e o WCF, mas o WCF dá suporte aos mesmos recursos que o ASMX fornece – mensagens SOAP por HTTP. Para obter mais informações sobre como consumir um serviço ASMX, consulte [consumir ASP.NET Web Services (asmx)](~/xamarin-forms/data-cloud/web-services/asmx.md).

> [!IMPORTANT]
> O suporte à plataforma Xamarin para WCF é limitado a mensagens SOAP codificadas por texto sobre HTTP/HTTPS usando a `BasicHttpBinding` classe.
>
> O suporte do WCF requer o uso de ferramentas disponíveis somente em um ambiente do Windows para gerar o proxy e hospedar o TodoWCFService. Criar e testar o aplicativo iOS exigirá a implantação do TodoWCFService em um computador Windows ou como um serviço Web do Azure.
>
> Aplicativos nativos do Xamarin Forms normalmente compartilham código com uma .NET Standard biblioteca de classes. No entanto, o .NET Core atualmente não dá suporte ao WCF, portanto, o projeto compartilhado deve ser uma biblioteca de classes portátil herdada. Para obter informações sobre o suporte do WCF no .NET Core, consulte [escolhendo entre o .NET Core e o .NET Framework para aplicativos de servidor](/dotnet/standard/choosing-core-framework-server).

A solução de aplicativo de exemplo inclui um serviço WCF que pode ser executado localmente e é mostrado na seguinte captura de tela:

![](wcf-images/portal.png "Sample Application")

> [!NOTE]
> No iOS 9 e superior, a ATS (segurança de transporte de aplicativo) impõe conexões seguras entre os recursos da Internet (como o servidor back-end do aplicativo) e o aplicativo, impedindo, assim, a divulgação acidental de informações confidenciais. Como o ATS está habilitado por padrão em aplicativos criados para iOS 9, todas as conexões estarão sujeitas a requisitos de segurança de ATS. Se as conexões não atenderem a esses requisitos, elas falharão com uma exceção.
>
> O ATS pode ser recusado se não for possível usar o `HTTPS` protocolo e a comunicação segura para recursos da Internet. Isso pode ser feito atualizando o arquivo **info. plist** do aplicativo. Para obter mais informações, consulte [segurança do transporte de aplicativo](~/ios/app-fundamentals/ats.md).

## <a name="consume-the-web-service"></a>Consumir o serviço Web

O serviço WCF fornece as seguintes operações:

|Operação|Descrição|Parâmetros|
|--- |--- |--- |
|GetTodoItems|Obter uma lista de itens pendentes|
|CreateTodoItem|Criar um novo item de tarefas pendentes|Um TodoItem serializado XML|
|EditTodoItem|Atualizar um item pendente|Um TodoItem serializado XML|
|DeleteTodoItem|Excluir um item pendente|Um TodoItem serializado XML|

Para obter mais informações sobre o modelo de dados usado no aplicativo, consulte [modelando os dados](~/xamarin-forms/data-cloud/web-services/introduction.md).

Um *proxy* deve ser gerado para consumir um serviço WCF, que permite que o aplicativo se conecte ao serviço. O proxy é construído consumindo metadados de serviço que definem os métodos e a configuração de serviço associada. Esses metadados são expostos na forma de um documento WSDL (linguagem de descrição de serviços Web) gerado pelo serviço Web. O proxy pode ser criado usando o Microsoft WCF Web Service Reference Provider no Visual Studio 2017 para adicionar uma referência de serviço para o serviço Web a uma biblioteca .NET Standard. Uma alternativa para criar o proxy usando o Microsoft WCF Web Service Reference Provider no Visual Studio 2017 é usar a ferramenta de utilitário de metadados ServiceModel (svcutil. exe). Para obter mais informações, consulte [ferramenta de utilitário de metadados ServiceModel (svcutil. exe)](/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe/).

As classes de proxy geradas fornecem métodos para consumir os serviços Web que usam o padrão de design do modelo de programação assíncrona (APM). Nesse padrão, uma operação assíncrona é implementada como dois métodos chamados *BeginOperationName* e *EndOperationName*, que começam e terminam a operação assíncrona.

O método *BeginOperationName* inicia a operação assíncrona e retorna um objeto que implementa a `IAsyncResult` interface. Depois de chamar *BeginOperationName*, um aplicativo pode continuar executando instruções no thread de chamada, enquanto a operação assíncrona ocorre em um thread do pool de threads.

Para cada chamada para *BeginOperationName*, o aplicativo também deve chamar *endoperable* para obter os resultados da operação. O valor de retorno de *Endoperable* é o mesmo tipo retornado pelo método de serviço Web síncrono. Por exemplo, o `EndGetTodoItems` método retorna uma coleção de `TodoItem` instâncias. O método *endoperation* também inclui um `IAsyncResult` parâmetro que deve ser definido para a instância retornada pela chamada correspondente ao método *BeginOperationName* .

A TPL (biblioteca paralela de tarefas) pode simplificar o processo de consumo de um par de métodos de início/término do APM encapsulando as operações assíncronas no mesmo `Task` objeto. Esse encapsulamento é fornecido por várias sobrecargas do `TaskFactory.FromAsync` método.

Para obter mais informações sobre o APM, consulte [modelo de programação assíncrona](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) e [tpl e programação assíncrona de .NET Framework tradicional](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) no msdn.

### <a name="create-the-todoserviceclient-object"></a>Criar o objeto TodoServiceClient

A classe de proxy gerada fornece a `TodoServiceClient` classe, que é usada para se comunicar com o serviço WCF por http. Ele fornece a funcionalidade para invocar métodos de serviço Web como operações assíncronas de uma instância de serviço identificada por URI. Para obter mais informações sobre operações assíncronas, consulte [visão geral do suporte assíncrono](~/cross-platform/platform/async.md).

A `TodoServiceClient` instância é declarada no nível de classe para que o objeto fique por enquanto o aplicativo precisar consumir o serviço WCF, conforme mostrado no exemplo de código a seguir:

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

A `TodoServiceClient` instância é configurada com informações de associação e um endereço de ponto de extremidade. Uma associação é usada para especificar os detalhes de transporte, codificação e protocolo necessários para que aplicativos e serviços se comuniquem entre si. O `BasicHttpBinding` especifica que as mensagens SOAP codificadas por texto serão enviadas pelo protocolo de transporte http. A especificação de um endereço de ponto de extremidade permite que o aplicativo se conecte a diferentes instâncias do serviço WCF, desde que haja várias instâncias publicadas.

Para obter mais informações sobre como configurar a referência de serviço, consulte [Configurando a referência de serviço](~/cross-platform/data-cloud/web-services/index.md#wcf).

### <a name="create-data-transfer-objects"></a>Criar objetos de transferência de dados

O aplicativo de exemplo usa a `TodoItem` classe para modelar dados. Para armazenar um `TodoItem` item no serviço Web, primeiro ele deve ser convertido para o tipo gerado pelo proxy `TodoItem` . Isso é feito pelo `ToWCFServiceTodoItem` método, conforme mostrado no exemplo de código a seguir:

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

Esse método simplesmente cria uma nova `TodoWCFService.TodoItem` instância e define cada propriedade para a propriedade idêntica da `TodoItem` instância.

Da mesma forma, quando os dados são recuperados do serviço Web, eles devem ser convertidos do tipo gerado pelo proxy `TodoItem` em uma `TodoItem` instância do. Isso é feito com o `FromWCFServiceTodoItem` método, conforme mostrado no exemplo de código a seguir:

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

Esse método simplesmente recupera os dados do tipo gerado por proxy `TodoItem` e os define na `TodoItem` instância recém-criada.

### <a name="retrieve-data"></a>Recuperar dados

Os `TodoServiceClient.BeginGetTodoItems` `TodoServiceClient.EndGetTodoItems` métodos e são usados para chamar a `GetTodoItems` operação fornecida pelo serviço Web. Esses métodos assíncronos são encapsulados em um `Task` objeto, conforme mostrado no exemplo de código a seguir:

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

O `Task.Factory.FromAsync` método cria um `Task` que executa o `TodoServiceClient.EndGetTodoItems` método depois que o `TodoServiceClient.BeginGetTodoItems` método é concluído, com o `null` parâmetro indicando que nenhum dado está sendo passado para o `BeginGetTodoItems` delegado. Por fim, o valor da `TaskCreationOptions` enumeração Especifica que o comportamento padrão para a criação e execução de tarefas deve ser usado.

O `TodoServiceClient.EndGetTodoItems` método retorna um `ObservableCollection` de `TodoWCFService.TodoItem` instâncias, que é então convertido em uma `List` das `TodoItem` instâncias para exibição.

### <a name="create-data"></a>Criar dados

Os `TodoServiceClient.BeginCreateTodoItem` `TodoServiceClient.EndCreateTodoItem` métodos e são usados para chamar a `CreateTodoItem` operação fornecida pelo serviço Web. Esses métodos assíncronos são encapsulados em um `Task` objeto, conforme mostrado no exemplo de código a seguir:

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

O `Task.Factory.FromAsync` método cria um `Task` que executa o `TodoServiceClient.EndCreateTodoItem` método depois que o `TodoServiceClient.BeginCreateTodoItem` método é concluído, com o `todoItem` parâmetro sendo os dados passados para o `BeginCreateTodoItem` delegado para especificar o `TodoItem` a ser criado pelo serviço Web. Por fim, o valor da `TaskCreationOptions` enumeração Especifica que o comportamento padrão para a criação e execução de tarefas deve ser usado.

O serviço Web gera um `FaultException` se falhar ao criar o `TodoItem` , que é manipulado pelo aplicativo.

### <a name="update-data"></a>Atualizar dados

Os `TodoServiceClient.BeginEditTodoItem` `TodoServiceClient.EndEditTodoItem` métodos e são usados para chamar a `EditTodoItem` operação fornecida pelo serviço Web. Esses métodos assíncronos são encapsulados em um `Task` objeto, conforme mostrado no exemplo de código a seguir:

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

O `Task.Factory.FromAsync` método cria um `Task` que executa o `TodoServiceClient.EndEditTodoItem` método depois que o `TodoServiceClient.BeginCreateTodoItem` método é concluído, com o `todoItem` parâmetro sendo os dados passados para o `BeginEditTodoItem` delegado para especificar o `TodoItem` a ser atualizado pelo serviço Web. Por fim, o valor da `TaskCreationOptions` enumeração Especifica que o comportamento padrão para a criação e execução de tarefas deve ser usado.

O serviço Web gera um `FaultException` caso ele não consegue localizar ou atualizar o `TodoItem` , que é manipulado pelo aplicativo.

### <a name="delete-data"></a>Excluir dados

Os `TodoServiceClient.BeginDeleteTodoItem` `TodoServiceClient.EndDeleteTodoItem` métodos e são usados para chamar a `DeleteTodoItem` operação fornecida pelo serviço Web. Esses métodos assíncronos são encapsulados em um `Task` objeto, conforme mostrado no exemplo de código a seguir:

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

O `Task.Factory.FromAsync` método cria um `Task` que executa o `TodoServiceClient.EndDeleteTodoItem` método depois que o `TodoServiceClient.BeginDeleteTodoItem` método é concluído, com o `id` parâmetro sendo os dados passados para o `BeginDeleteTodoItem` delegado para especificar o `TodoItem` a ser excluído pelo serviço Web. Por fim, o valor da `TaskCreationOptions` enumeração Especifica que o comportamento padrão para a criação e execução de tarefas deve ser usado.

O serviço Web gera um `FaultException` caso ele não consegue localizar ou excluir o `TodoItem` , que é manipulado pelo aplicativo.

## <a name="configure-remote-access-to-iis-express"></a>Configurar o acesso remoto para IIS Express
No Visual Studio 2017 ou no Visual Studio 2019, você deve ser capaz de testar o aplicativo UWP em um PC sem nenhuma configuração adicional. O teste de clientes Android e iOS pode exigir as etapas adicionais nesta seção. Consulte [conectar-se a serviços Web locais de simuladores do IOS e de emuladores do Android](~/cross-platform/deploy-test/connect-to-local-web-services.md) para obter mais informações.

Por padrão, IIS Express responderá apenas às solicitações para `localhost` . Dispositivos remotos (como um dispositivo Android, um iPhone ou até mesmo um simulador) não terão acesso ao serviço WCF local. Você precisará saber o endereço IP da estação de trabalho do Windows 10 na rede local. Para fins deste exemplo, suponha que sua estação de trabalho tenha o endereço IP `192.168.1.143` . As etapas a seguir explicam como configurar o Windows 10 e o IIS Express para aceitar conexões remotas e conectar-se ao serviço de um dispositivo físico ou virtual:

1. **Adicione uma exceção ao firewall do Windows**. Você deve abrir uma porta por meio do firewall do Windows que os aplicativos em sua sub-rede podem usar para se comunicar com o serviço WCF. Crie uma regra de entrada abrindo a porta 49393 no firewall. Em um prompt de comando administrativo, execute este comando:

    ```
    netsh advfirewall firewall add rule name="TodoWCFService" dir=in protocol=tcp localport=49393 profile=private remoteip=localsubnet action=allow
    ```

1. **Configure IIS Express para aceitar conexões remotas**. Você pode configurar IIS Express editando o arquivo de configuração para IIS Express em **[diretório de solução] \. vs\config\applicationhost.config**. Localize o `site` elemento com o nome `TodoWCFService` . Ele deve ser semelhante ao seguinte XML:

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

    Você precisará adicionar dois `binding` elementos para abrir a porta 49393 para o tráfego externo e o emulador do Android. A associação usa um `[IP address]:[port]:[hostname]` formato que especifica como IIS Express responderá às solicitações. As solicitações externas terão nomes de host que devem ser especificados como um `binding` . Adicione o seguinte XML ao `bindings` elemento, substituindo o endereço IP pelo seu próprio endereço IP:

    ```xml
    <binding protocol="http" bindingInformation="*:49393:192.168.1.143" />
    <binding protocol="http" bindingInformation="*:49393:127.0.0.1" />
    ```

    Após as alterações, o `bindings` elemento deve ser semelhante ao seguinte:

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
    >Por padrão, IIS Express não aceitará conexões de fontes externas por motivos de segurança. Para habilitar conexões de dispositivos remotos, você deve executar IIS Express com permissões administrativas. A maneira mais fácil de fazer isso é executar o Visual Studio 2017 com permissões administrativas. Isso iniciará IIS Express com permissões administrativas ao executar o TodoWCFService.

    Com essas etapas concluídas, você deve ser capaz de executar o TodoWCFService e conectar-se de outros dispositivos em sua sub-rede. Você pode testar isso executando seu aplicativo e visitando `http://localhost:49393/TodoService.svc` . Se você receber um erro de **solicitação** incorreta ao visitar essa URL, seu `bindings` pode estar incorreto na configuração de IIS Express (a solicitação está atingindo IIS Express mas está sendo rejeitada). Se você receber um erro diferente, pode ser que seu aplicativo não esteja em execução ou que o firewall esteja configurado incorretamente.

    Para permitir que IIS Express continuem executando e servindo o serviço, desative a opção **Editar e continuar** nas **propriedades do projeto > depuradores de > Web**.

1. **Personalizar os dispositivos de ponto de extremidade use para acessar o serviço**. Esta etapa envolve a configuração do aplicativo cliente, em execução em um dispositivo físico ou emulado, para acessar o serviço WCF.

    O emulador do Android utiliza um proxy interno que impede que o emulador acesse diretamente o endereço da máquina host `localhost` . Em vez disso, o endereço `10.0.2.2` no emulador é roteado para `localhost` no computador host por meio de um proxy interno. Essas solicitações com proxy terão `127.0.0.1` como o nome do host no cabeçalho da solicitação, que é por isso que você criou a associação de IIS Express para esse nome de host nas etapas acima.

    O simulador do iOS é executado em um host de Build do Mac, mesmo se você estiver usando o [simulador do IOS remoto para Windows](~/tools/ios-simulator/index.md). As solicitações de rede do simulador terão o IP da estação de trabalho na rede local como o nome do host (neste exemplo `192.168.1.143` , é, mas seu endereço IP real provavelmente será diferente). É por isso que você criou a associação de IIS Express para esse nome de host nas etapas acima.

    Verifique se a `SoapUrl` propriedade no arquivo **Constants.cs** no projeto TodoWCF (portátil) tem valores que estão corretos para sua rede:

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

    Depois de configurar o **Constants.cs** com os pontos de extremidade apropriados, você poderá se conectar ao TodoWCFService em execução na estação de trabalho do Windows 10 de dispositivos físicos ou virtuais.

## <a name="related-links"></a>Links relacionados

- [TodoWCF (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todowcf)
- [Como criar um cliente do Windows Communication Foundation](https://docs.microsoft.com/dotnet/framework/wcf/how-to-create-a-wcf-client)
- [Ferramenta de utilitário de metadados ServiceModel (svcutil. exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
