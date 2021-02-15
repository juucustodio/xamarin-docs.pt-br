---
title: Introdução aos serviços Web
description: Este guia demonstra como consumir diferentes tecnologias de serviço Web. Os tópicos abordados incluem a comunicação com serviços REST, serviços SOAP e serviços Windows Communication Foundations.
ms.prod: xamarin
ms.assetid: 72627B90-586A-02B6-E231-F7CE015A1B97
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 2131dd7611d802b8e1b1a22d1fbab42b5ac64746
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457959"
---
# <a name="introduction-to-web-services"></a>Introdução aos serviços Web

_Este guia demonstra como consumir diferentes tecnologias de serviço Web. Os tópicos abordados incluem a comunicação com serviços REST, serviços SOAP e serviços Windows Communication Foundations._

Para funcionar corretamente, muitos aplicativos móveis dependem da nuvem e, portanto, integrar os serviços da Web a aplicativos móveis é um cenário comum. A plataforma Xamarin dá suporte ao consumo de diferentes tecnologias de serviço Web e inclui suporte interno e de terceiros para consumir serviços RESTful, ASMX e Windows Communication Foundation (WCF).

Para clientes que usam o Xamarin. Forms, há exemplos completos usando cada uma dessas tecnologias na documentação [dos serviços Web Xamarin. Forms](~/xamarin-forms/data-cloud/index.yml) .

> [!IMPORTANT]
> No iOS 9, a ATS (segurança de transporte de aplicativo) impõe conexões seguras entre os recursos da Internet (como o servidor back-end do aplicativo) e o aplicativo, impedindo, assim, a divulgação acidental de informações confidenciais.
> Como o ATS está habilitado por padrão em aplicativos criados para iOS 9, todas as conexões estarão sujeitas a requisitos de segurança de ATS. Se as conexões não atenderem a esses requisitos, elas falharão com uma exceção.

Você pode recusar o ATS se não for possível usar o `HTTPS` protocolo e a comunicação segura para recursos da Internet. Isso pode ser feito atualizando o arquivo **info. plist** do aplicativo. Para obter mais informações, consulte [segurança do transporte de aplicativo](~/ios/app-fundamentals/ats.md).

## <a name="rest"></a>REST

REST é um estilo de arquitetura para a criação de serviços Web. As solicitações REST são feitas por HTTP usando os mesmos verbos HTTP que os navegadores da Web usam para recuperar páginas da Web e para enviar dados aos servidores. Os verbos são:

- **Get** – esta operação é usada para recuperar dados do serviço Web.
- **Post** – esta operação é usada para criar um novo item de dados no serviço Web.
- **Put** – esta operação é usada para atualizar um item de dados no serviço Web.
- **Patch** – esta operação é usada para atualizar um item de dados no serviço Web descrevendo um conjunto de instruções sobre como o item deve ser modificado. Esse verbo não é usado no aplicativo de exemplo.
- **Excluir** – esta operação é usada para excluir um item de dados no serviço Web.

As APIs de serviço Web que aderem ao REST são chamadas de APIs RESTful e são definidas usando:

- Um URI de base.
- Métodos HTTP, como GET, POST, PUT, PATCH ou DELETE.
- Um tipo de mídia para os dados, como JavaScript Object Notation (JSON).

A simplicidade do REST ajudou a torná-lo o principal método para acessar os serviços da Web em aplicativos móveis.

## <a name="consuming-rest-services"></a>Consumindo serviços REST

Há várias bibliotecas e classes que podem ser usadas para consumir serviços REST e as subseções a seguir os discutem. Para obter mais informações sobre como consumir um serviço REST, consulte [consumir um serviço Web RESTful](~/xamarin-forms/data-cloud/web-services/rest.md).

### <a name="httpclient"></a>HttpClient

As [bibliotecas de cliente http da Microsoft](https://www.nuget.org/packages/Microsoft.Net.Http) fornecem a `HttpClient` classe, que é usada para enviar e receber solicitações por http. Ele fornece a funcionalidade para enviar solicitações HTTP e receber respostas HTTP de um recurso identificado por URI. Cada solicitação é enviada como uma operação assíncrona. Para obter mais informações sobre operações assíncronas, consulte [visão geral do suporte assíncrono](~/cross-platform/platform/async.md).

A `HttpResponseMessage` classe representa uma mensagem de resposta http recebida do serviço Web depois que uma solicitação HTTP é feita. Ele contém informações sobre a resposta, incluindo o código de status, os cabeçalhos e o corpo. A `HttpContent` classe representa o corpo de http e cabeçalhos de conteúdo, como `Content-Type` e `Content-Encoding` . O conteúdo pode ser lido usando qualquer um dos `ReadAs` métodos, como `ReadAsStringAsync` e `ReadAsByteArrayAsync` , dependendo do formato dos dados.

Para obter mais informações sobre a `HttpClient` classe, consulte [criando o objeto HttpClient](~/xamarin-forms/data-cloud/web-services/rest.md).

<a name="Using_HTTPWebRequest"></a>

### <a name="httpwebrequest"></a>HTTPWebRequest

Chamar serviços Web com o `HTTPWebRequest` envolve:

- Criando a instância de solicitação para um URI específico.
- Definindo várias propriedades HTTP na instância de solicitação.
- Recuperando um  `HttpWebResponse` da solicitação.
- Lendo dados fora da resposta.

Por exemplo, o código a seguir recupera dados da biblioteca nacional dos EUA do serviço Web de medicina:

```csharp
var rxcui = "198440";
var request = HttpWebRequest.Create(string.Format(@"https://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui));
request.ContentType = "application/json";
request.Method = "GET";

using (HttpWebResponse response = request.GetResponse() as HttpWebResponse)
{
  if (response.StatusCode != HttpStatusCode.OK)
     Console.Out.WriteLine("Error fetching data. Server returned status code: {0}", response.StatusCode);
  using (StreamReader reader = new StreamReader(response.GetResponseStream()))
  {
               var content = reader.ReadToEnd();
               if(string.IsNullOrWhiteSpace(content)) {
                       Console.Out.WriteLine("Response contained empty body...");
               }
               else {
                       Console.Out.WriteLine("Response Body: \r\n {0}", content);
               }

               Assert.NotNull(content);
  }
}
```

O exemplo acima cria um `HttpWebRequest` que retornará dados formatados como JSON. Os dados são retornados em um `HttpWebResponse` , a partir do qual um `StreamReader` pode ser obtido para ler os dados.

<a name="Using_RESTSHARP"></a>

### <a name="restsharp"></a>RestSharp

Outra abordagem para consumir serviços REST é usar a biblioteca [RestSharp](http://restsharp.org/) . O RestSharp encapsula solicitações HTTP, incluindo suporte para recuperação de resultados como um conteúdo bruto de cadeia de caracteres ou como um objeto C# desserializado. Por exemplo, o código a seguir faz uma solicitação para a biblioteca nacional dos EUA do serviço Web de medicina e recupera os resultados como uma cadeia de caracteres formatada em JSON:

```csharp
var request = new RestRequest(string.Format("{0}/allinfo", rxcui));
request.RequestFormat = DataFormat.Json;
var response = Client.Execute(request);
if(string.IsNullOrWhiteSpace(response.Content) || response.StatusCode != System.Net.HttpStatusCode.OK) {
       return null;
}
rxTerm = DeserializeRxTerm(response.Content);
```

`DeserializeRxTerm` é um método que usará a cadeia de caracteres JSON bruta da `RestSharp.RestResponse.Content` propriedade e convertê-la em um objeto C#. A desserialização de dados retornados de serviços Web é discutida posteriormente neste artigo.

<a name="Using_NSUrlconnection"></a>

### <a name="nsurlconnection"></a>NSUrlConnection

Além das classes disponíveis na BCL (biblioteca de classes base) mono, como `HttpWebRequest` , e bibliotecas C# de terceiros, como RestSharp, as classes específicas da plataforma também estão disponíveis para o consumo de serviços da Web. Por exemplo, no iOS, as `NSUrlConnection` `NSMutableUrlRequest` classes e podem ser usadas.

O exemplo de código a seguir mostra como chamar a biblioteca nacional dos EUA do serviço Web de medicina usando classes do iOS:

```csharp
var rxcui = "198440";
var request = new NSMutableUrlRequest(new NSUrl(string.Format("https://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui)),
       NSUrlRequestCachePolicy.ReloadRevalidatingCacheData, 20);
request["Accept"] = "application/json";

var connectionDelegate = new RxTermNSURLConnectionDelegate();
var connection = new NSUrlConnection(request, connectionDelegate);
connection.Start();

public class RxTermNSURLConnectionDelegate : NSUrlConnectionDelegate
{
       StringBuilder _ResponseBuilder;
       public bool IsFinishedLoading { get; set; }
       public string ResponseContent { get; set; }

       public RxTermNSURLConnectionDelegate()
               : base()
       {
               _ResponseBuilder = new StringBuilder();
       }

       public override void ReceivedData(NSUrlConnection connection, NSData data)
       {
               if(data != null) {
                       _ResponseBuilder.Append(data.ToString());
               }
       }
       public override void FinishedLoading(NSUrlConnection connection)
       {
               IsFinishedLoading = true;
               ResponseContent = _ResponseBuilder.ToString();
       }
}
```

Geralmente, as classes específicas da plataforma para consumir serviços Web devem ser limitadas a cenários em que o código nativo está sendo portado para C#. Sempre que possível, o código de acesso do serviço Web deve ser portátil para que ele possa ser compartilhado entre plataformas.

<a name="Using_ServiceStack_Client"></a>

### <a name="servicestack"></a>Perstack

Outra opção para chamar serviços Web é a biblioteca de [pilha de serviço](https://servicestack.net) . Por exemplo, o código a seguir mostra como usar o método da pilha de serviço `IServiceClient.GetAsync` para emitir uma solicitação de serviço:

```csharp
client.GetAsync<CustomersResponse>("",
          (response) => {
               foreach(var c in response.Customers) {
                       Console.WriteLine(c.CompanyName);
               }
       },
       (response, ex) => {
               Console.WriteLine(ex.Message);
       });
```

> [!IMPORTANT]
> Embora ferramentas como o perstack e o RestSharp facilitem a chamada e o consumo de serviços REST, às vezes não é trivial consumir XML ou JSON que não esteja em conformidade com as convenções de serialização _DataContract_ padrão. Se necessário, invoque a solicitação e manipule a serialização apropriada explicitamente usando a biblioteca de perstacks. Text discutida abaixo.

<a name="Options_for_consuming_RESTful_data"></a>

## <a name="consuming-restful-data"></a>Consumindo dados RESTful

Os serviços Web RESTful normalmente usam mensagens JSON para retornar dados ao cliente. JSON é um formato de intercâmbio de dados baseado em texto que produz cargas compactadas, o que resulta em requisitos de largura de banda reduzidos ao enviar dados. Nesta seção, os mecanismos para consumo de respostas RESTful no JSON e no antigo-XML (POX) serão examinados.

<a name="Using_System.JSON"></a>

### <a name="systemjson"></a>System.JSEM

A plataforma Xamarin é fornecida com suporte para JSON pronto para uso. Usando um `JsonObject` , os resultados podem ser recuperados conforme mostrado no exemplo de código a seguir:

```csharp
var obj = JsonObject.Parse(json);
var properties = obj["rxtermsProperties"];
term.BrandName = properties["brandName"];
term.DisplayName = properties["displayName"];
term.Synonym = properties["synonym"];
term.FullName = properties["fullName"];
term.FullGenericName = properties["fullGenericName"];
term.Strength = properties["strength"];
```

No entanto, é importante estar ciente de que as `System.Json` ferramentas carregam integralmente os dados na memória.

<a name="Using_JSON.NET"></a>

### <a name="jsonnet"></a>JSON.NET

A [biblioteca NewtonSoft JSON.net](https://www.newtonsoft.com/json) é uma biblioteca amplamente usada para serializar e desserializar mensagens JSON. O exemplo de código a seguir mostra como usar JSON.NET para desserializar uma mensagem JSON em um objeto C#:

```csharp
var term = new RxTerm();
var properties = JObject.Parse(json)["rxtermsProperties"];
term.BrandName = properties["brandName"].Value<string>();
term.DisplayName = properties["displayName"].Value<string>();
term.Synonym = properties["synonym"].Value<string>();;
term.FullName = properties["fullName"].Value<string>();;
term.FullGenericName = properties["fullGenericName"].Value<string>();;
term.Strength = properties["strength"].Value<string>();
term.RxCUI = properties["rxcui"].Value<string>();
```

<a name="Using_ServiceStack.Text"></a>

### <a name="servicestacktext"></a>Perstack. Text

O perstack. Text é uma biblioteca de serialização JSON projetada para funcionar com a biblioteca do enstack. O exemplo de código a seguir mostra como analisar JSON usando um `ServiceStack.Text.JsonObject` :

```csharp
var result = JsonObject.Parse(json).Object("rxtermsProperties")
       .ConvertTo(x => new RxTerm {
               BrandName = x.Get("brandName"),
               DisplayName = x.Get("displayName"),
               Synonym = x.Get("synonym"),
               FullName = x.Get("fullName"),
               FullGenericName = x.Get("fullGenericName"),
               Strength = x.Get("strength"),
               RxTermDoseForm = x.Get("rxtermsDoseForm"),
               Route = x.Get("route"),
               RxCUI = x.Get("rxcui"),
               RxNormDoseForm = x.Get("rxnormDoseForm"),
       });
```

<a name="Using_System.Xml.Linq"></a>

### <a name="systemxmllinq"></a>System.Xml.Linq

No caso de consumo de um serviço Web REST baseado em XML, LINQ to XML pode ser usado para analisar o XML e preencher um objeto C# embutido, conforme demonstrado no exemplo de código a seguir:

```csharp
var doc = XDocument.Parse(xml);
var result = doc.Root.Descendants("rxtermsProperties")
.Select(x=> new RxTerm()
       {
               BrandName = x.Element("brandName").Value,
               DisplayName = x.Element("displayName").Value,
               Synonym = x.Element("synonym").Value,
               FullName = x.Element("fullName").Value,
               FullGenericName = x.Element("fullGenericName").Value,
               //bind more here...
               RxCUI = x.Element("rxcui").Value,
       });
```

<a name="asmx"></a>

## <a name="aspnet-web-service-asmx"></a>Serviço Web ASP.NET (ASMX)

O ASMX fornece a capacidade de criar serviços Web que enviam mensagens usando o protocolo SOAP (Simple Object Access Protocol). SOAP é um protocolo independente de plataforma e independente de linguagem para criar e acessar serviços Web. Os consumidores de um serviço ASMX não precisam saber nada sobre a plataforma, o modelo de objeto ou a linguagem de programação usada para implementar o serviço. Eles só precisam entender como enviar e receber mensagens SOAP.

Uma mensagem SOAP é um documento XML que contém os seguintes elementos:

- Um elemento raiz chamado *envelope* que identifica o documento XML como uma mensagem SOAP.
- Um elemento de *cabeçalho* opcional que contém informações específicas do aplicativo, como dados de autenticação. Se o elemento de *cabeçalho* estiver presente, ele deverá ser o primeiro elemento filho do elemento *envelope* .
- Um elemento *Body* necessário que contém a mensagem SOAP destinada ao destinatário.
- Um elemento de *falha* opcional que é usado para indicar mensagens de erro. Se o elemento *Fault* estiver presente, ele deverá ser um elemento filho do elemento *Body* .

O SOAP pode operar em vários protocolos de transporte, incluindo HTTP, SMTP, TCP e UDP. No entanto, um serviço ASMX só pode operar via HTTP. A plataforma Xamarin dá suporte a implementações SOAP 1,1 padrão sobre HTTP, e isso inclui suporte para muitas das configurações de serviço ASMX padrão.

### <a name="generating-a-proxy"></a>Gerando um proxy

Um *proxy* deve ser gerado para consumir um serviço ASMX, que permite que o aplicativo se conecte ao serviço. O proxy é construído consumindo metadados de serviço que definem os métodos e a configuração de serviço associada. Esses metadados são expostos como um documento WSDL (linguagem de descrição de serviços Web) que é gerado pelo serviço Web. O proxy é criado usando Visual Studio para Mac ou o Visual Studio para adicionar uma referência Web para o serviço Web aos projetos específicos da plataforma.

A URL do serviço Web pode ser uma fonte remota hospedada ou um recurso do sistema de arquivos local acessível por meio do `file:///` prefixo do caminho, por exemplo:

```csharp
file:///Users/myUserName/projects/MyProjectName/service.wsdl
```

[![A URL do serviço Web pode ser uma fonte remota hospedada ou um recurso do sistema de arquivos local acessível por meio do prefixo do caminho do arquivo](images/add-webreference-dialog.png)](images/add-webreference-dialog.png#lightbox)

Isso gera o proxy na pasta de referências de serviço ou Web do projeto. Como um proxy é gerado pelo código, ele não deve ser modificado.

<a name="Manually_adding_a_proxy_to_a_project"></a>

#### <a name="manually-adding-a-proxy-to-a-project"></a>Adicionando manualmente um proxy a um projeto

Se você tiver um proxy existente que foi gerado usando ferramentas compatíveis, essa saída poderá ser consumida quando incluída como parte do seu projeto. Em Visual Studio para Mac, use **Adicionar arquivos...** opção de menu para adicionar o proxy. Além disso, isso requer que *System.Web.Services.dll* seja referenciado explicitamente usando a **adição de referências..** . .

### <a name="consuming-the-proxy"></a>Consumindo o proxy

As classes de proxy geradas fornecem métodos para consumir o serviço Web que usa o padrão de design do modelo de programação assíncrona (APM). Nesse padrão, uma operação assíncrona é implementada como dois métodos chamados *BeginOperationName* e *EndOperationName*, que começam e terminam a operação assíncrona.

O método *BeginOperationName* inicia a operação assíncrona e retorna um objeto que implementa a `IAsyncResult` interface. Depois de chamar *BeginOperationName*, um aplicativo pode continuar executando instruções no thread de chamada, enquanto a operação assíncrona ocorre em um thread do pool de threads.

Para cada chamada para *BeginOperationName*, o aplicativo também deve chamar *endoperable* para obter os resultados da operação. O valor de retorno de *Endoperable* é o mesmo tipo retornado pelo método de serviço Web síncrono. O código a seguir mostra um exemplo disso:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync<ASMXService.TodoItem[]> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);
  ...
}
```

A TPL (biblioteca paralela de tarefas) pode simplificar o processo de consumo de um par de métodos de início/término do APM encapsulando as operações assíncronas no mesmo `Task` objeto. Esse encapsulamento é fornecido por várias sobrecargas do `Task.Factory.FromAsync` método. Esse método cria um `Task` que executa o `TodoService.EndGetTodoItems` método depois que o `TodoService.BeginGetTodoItems` método é concluído, com o `null` parâmetro indicando que nenhum dado está sendo passado para o `BeginGetTodoItems` delegado. Por fim, o valor da `TaskCreationOptions` enumeração Especifica que o comportamento padrão para a criação e execução de tarefas deve ser usado.

Para obter mais informações sobre o APM, consulte [modelo de programação assíncrona](/dotnet/standard/asynchronous-programming-patterns/asynchronous-programming-model-apm) e [tpl e programação assíncrona de .NET Framework tradicional](/dotnet/standard/parallel-programming/tpl-and-traditional-async-programming) no msdn.

Para obter mais informações sobre o consumo de um serviço ASMX, consulte [consumir um serviço Web ASP.net (asmx)](~/xamarin-forms/data-cloud/web-services/asmx.md).

<a name="wcf"></a>

## <a name="windows-communication-foundation-wcf"></a>Windows Communication Foundation (WCF)

O WCF é a estrutura unificada da Microsoft para a criação de aplicativos orientados a serviços. Ele permite que os desenvolvedores criem aplicativos distribuídos seguros, confiáveis, transacionados e interoperáveis.

O WCF descreve um serviço com uma variedade de contratos diferentes que incluem o seguinte:

- **Contratos de dados** – defina as estruturas de dados que formam a base para o conteúdo de uma mensagem.
- **Contratos de mensagem** – compor mensagens de contratos de dados existentes.
- **Contratos de falha** – permitir que falhas SOAP personalizadas sejam especificadas.
- **Contratos de serviço** – especifique as operações que oferecem suporte aos serviços e as mensagens necessárias para interagir com cada operação. Eles também especificam qualquer comportamento de falha personalizado que possa ser associado a operações em cada serviço.

Há diferenças entre o ASMX (ASP.NET Web Services) e o WCF, mas é importante entender que o WCF dá suporte aos mesmos recursos que o ASMX fornece – mensagens SOAP por HTTP.

> [!IMPORTANT]
> O suporte à plataforma Xamarin para WCF é limitado a mensagens SOAP codificadas por texto sobre HTTP/HTTPS usando a `BasicHttpBinding` classe. Além disso, o suporte do WCF requer o uso de ferramentas disponíveis somente em um ambiente do Windows para gerar o proxy.

### <a name="generating-a-proxy"></a>Gerando um proxy

Um *proxy* deve ser gerado para consumir um serviço WCF, que permite que o aplicativo se conecte ao serviço. O proxy é construído consumindo metadados de serviço que definem os métodos e a configuração de serviço associada. Esses metadados são expostos na forma de um documento WSDL (linguagem de descrição de serviços Web) gerado pelo serviço Web. O proxy pode ser criado usando o Microsoft WCF Web Service Reference Provider no Visual Studio 2017 para adicionar uma referência de serviço para o serviço Web a uma biblioteca .NET Standard.

Uma alternativa para criar o proxy usando o Microsoft WCF Web Service Reference Provider no Visual Studio 2017 é usar a ferramenta de utilitário de metadados ServiceModel (svcutil.exe). Para obter mais informações, consulte [ferramenta de utilitário de metadados ServiceModel (Svcutil.exe)](/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security"></a>

### <a name="configuring-the-proxy"></a>Configurando o proxy

Configurar o proxy gerado geralmente usará dois argumentos de configuração (dependendo do SOAP 1.1/ASMX ou WCF) durante a inicialização: a `EndpointAddress` e/ou as informações de associação associadas, conforme mostrado no exemplo abaixo:

```csharp
var binding = new BasicHttpBinding () {
       Name= "basicHttpBinding",
       MaxReceivedMessageSize = 67108864,
};

binding.ReaderQuotas = new System.Xml.XmlDictionaryReaderQuotas() {
       MaxArrayLength = 2147483646,
       MaxStringContentLength = 5242880,
};

var timeout = new TimeSpan(0,1,0);
binding.SendTimeout= timeout;
binding.OpenTimeout = timeout;
binding.ReceiveTimeout = timeout;

client = new Service1Client (binding, new EndpointAddress ("http://192.168.1.100/Service1.svc"));
```

Uma associação é usada para especificar os detalhes de transporte, codificação e protocolo necessários para que aplicativos e serviços se comuniquem entre si. O `BasicHttpBinding` especifica que as mensagens SOAP codificadas por texto serão enviadas pelo protocolo de transporte http. A especificação de um endereço de ponto de extremidade permite que o aplicativo se conecte a diferentes instâncias do serviço WCF, desde que haja várias instâncias publicadas.

### <a name="consuming-the-proxy"></a>Consumindo o proxy

As classes de proxy geradas fornecem métodos para consumir os serviços Web que usam o padrão de design do modelo de programação assíncrona (APM). Nesse padrão, uma operação assíncrona é implementada como dois métodos chamados *BeginOperationName* e *EndOperationName*, que começam e terminam a operação assíncrona.

O método *BeginOperationName* inicia a operação assíncrona e retorna um objeto que implementa a `IAsyncResult` interface. Depois de chamar *BeginOperationName*, um aplicativo pode continuar executando instruções no thread de chamada, enquanto a operação assíncrona ocorre em um thread do pool de threads.

Para cada chamada para *BeginOperationName*, o aplicativo também deve chamar *endoperable* para obter os resultados da operação. O valor de retorno de *Endoperable* é o mesmo tipo retornado pelo método de serviço Web síncrono. O código a seguir mostra um exemplo disso:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var todoItems = await Task.Factory.FromAsync <ObservableCollection<TodoWCFService.TodoItem>> (
    todoService.BeginGetTodoItems,
    todoService.EndGetTodoItems,
    null,
    TaskCreationOptions.None);
  ...
}
```

A TPL (biblioteca paralela de tarefas) pode simplificar o processo de consumo de um par de métodos de início/término do APM encapsulando as operações assíncronas no mesmo `Task` objeto. Esse encapsulamento é fornecido por várias sobrecargas do `Task.Factory.FromAsync` método. Esse método cria um `Task` que executa o `TodoServiceClient.EndGetTodoItems` método depois que o `TodoServiceClient.BeginGetTodoItems` método é concluído, com o `null` parâmetro indicando que nenhum dado está sendo passado para o `BeginGetTodoItems` delegado. Por fim, o valor da `TaskCreationOptions` enumeração Especifica que o comportamento padrão para a criação e execução de tarefas deve ser usado.

Para obter mais informações sobre o APM, consulte [modelo de programação assíncrona](/dotnet/standard/asynchronous-programming-patterns/asynchronous-programming-model-apm) e [tpl e programação assíncrona de .NET Framework tradicional](/dotnet/standard/parallel-programming/tpl-and-traditional-async-programming) no msdn.

Para obter mais informações sobre como consumir um serviço WCF, consulte [consumir um serviço Web do Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/web-services/wcf.md).

<a name="Calling_a_WCF_Service_with_Transport_Security"></a>

#### <a name="using-transport-security"></a>Usando segurança de transporte

Os serviços WCF podem empregar a segurança de nível de transporte para proteger contra a interceptação de mensagens. A plataforma Xamarin dá suporte a associações que empregam a segurança de nível de transporte usando SSL. No entanto, pode haver casos em que a pilha pode precisar validar o certificado, o que resulta em um comportamento não previsto. A validação pode ser substituída pelo registro de um `ServerCertificateValidationCallback` delegado antes de invocar o serviço, conforme demonstrado no exemplo de código a seguir:

```csharp
System.Net.ServicePointManager.ServerCertificateValidationCallback +=
(se, cert, chain, sslerror) => { return true; };
```

Isso mantém a criptografia de transporte ao ignorar a validação do certificado do lado do servidor. No entanto, essa abordagem efetivamente desconsidera as preocupações de confiança associadas ao certificado e pode não ser apropriada. Para obter mais informações, consulte [usando raízes confiáveis respectfully](https://www.mono-project.com/UsingTrustedRootsRespectfully) em [mono-Project.com](https://www.mono-project.com).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security"></a>

#### <a name="using-client-credential-security"></a>Usando a segurança de credencial do cliente

Os serviços WCF também podem exigir que os clientes de serviço se autentiquem usando credenciais. A plataforma Xamarin não dá suporte ao protocolo WS-Security, que permite que os clientes enviem credenciais dentro do envelope da mensagem SOAP. No entanto, a plataforma Xamarin oferece suporte à capacidade de enviar credenciais de autenticação básica HTTP para o servidor, especificando o apropriado `ClientCredentialType` :

```csharp
basicHttpBinding.Security.Transport.ClientCredentialType = HttpClientCredentialType.Basic;
```

Em seguida, as credenciais de autenticação básica podem ser especificadas:

```csharp
client.ClientCredentials.UserName.UserName = @"foo";
client.ClientCredentials.UserName.Password = @"mrsnuggles";
```

Para obter mais informações sobre a autenticação básica HTTP, embora no contexto de um serviço Web REST, consulte [Autenticando um serviço Web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md).

## <a name="related-links"></a>Links Relacionados

- [Serviços Web no Xamarin. Forms](~/xamarin-forms/data-cloud/index.yml)
- [Ferramenta de utilitário de metadados ServiceModel (svcutil.exe)](/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
- [BasicHttpBinding](/dotnet/api/system.servicemodel.basichttpbinding)