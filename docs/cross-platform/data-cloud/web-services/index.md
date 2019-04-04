---
title: Introdução aos serviços Web
description: Este guia demonstra como utilizar tecnologias de serviço da web diferente. Os tópicos abordados incluem a comunicação com os serviços REST, SOAP serviços e serviços do Windows Communication Foundation.
ms.prod: xamarin
ms.assetid: 72627B90-586A-02B6-E231-F7CE015A1B97
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: afebe7f491855844e18bf054d665cf8d54e8f353
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672385"
---
# <a name="introduction-to-web-services"></a>Introdução aos serviços Web

_Este guia demonstra como utilizar tecnologias de serviço da web diferente. Os tópicos abordados incluem a comunicação com os serviços REST, SOAP serviços e serviços do Windows Communication Foundation._

Para funcionar corretamente, muitos aplicativos móveis são dependentes de nuvem e, então, integrando serviços web em aplicativos móveis é um cenário comum. A plataforma Xamarin dá suporte ao consumo de tecnologias de serviço da web diferente e inclui suporte interno ou de terceiros para o consumo de serviços RESTful, ASMX e Windows Communication Foundation (WCF).

Para clientes que usam o xamarin. Forms, há exemplos completos usando cada uma dessas tecnologias na [xamarin. Forms Web Services](~/xamarin-forms/data-cloud/index.md) documentação.

> [!IMPORTANT]
> No iOS 9, a segurança de transporte de aplicativo (ATS) impõe conexões seguras entre recursos da internet (como o servidor de back-end do aplicativo) e o aplicativo, impedindo assim a divulgação acidental de informações confidenciais.
> Desde que o ATS é habilitado por padrão em aplicativos criados para o iOS 9, todas as conexões serão sujeitos a requisitos de segurança ATS. Se as conexões não atender a esses requisitos, eles falharão com uma exceção.

Você pode recusar ATS se ele não é possível usar o `HTTPS` de protocolo e proteger a comunicação para recursos da internet. Isso pode ser feito atualizando o aplicativo **Info. plist** arquivo. Para obter mais informações, consulte [segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md).

## <a name="rest"></a>REST

REST Representational State Transfer () é um estilo de arquitetura para a criação de serviços da web. Solicitações REST são feitas por HTTP, usando os mesmos verbos HTTP que navegadores da web usam para recuperar as páginas da web e para enviar dados para servidores. Os verbos são:

- **OBTER** – esta operação é usada para recuperar dados do serviço web.
- **POST** – esta operação é usada para criar um novo item de dados no serviço web.
- **COLOCAR** – esta operação é usada para atualizar um item de dados no serviço web.
- **PATCH** – esta operação é usada para atualizar um item de dados no serviço web, descrevendo um conjunto de instruções sobre como o item deve ser modificado. Esse verbo não é usado no aplicativo de exemplo.
- **Excluir** – esta operação é usada para excluir um item de dados no serviço web.

APIs que aderem ao restante do serviço da Web são chamados de APIs RESTful e são definidos usando:

- Um URI de base.
- Métodos HTTP, como GET, POST, PUT, PATCH ou DELETE.
- Um tipo de mídia para os dados, como o objeto notação JSON (JavaScript).

A simplicidade do REST ajudou a torná-lo o principal método para acessar os serviços da web em aplicativos móveis.

## <a name="consuming-rest-services"></a>Consumir serviços REST

Há uma série de classes que podem ser usadas para consumir serviços REST e bibliotecas e as subseções a seguir discutem-los. Para obter mais informações sobre como consumir um serviço REST, consulte [consumindo um serviço Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md).

### <a name="httpclient"></a>HttpClient

O [Microsoft HTTP Client Libraries](https://www.nuget.org/packages/Microsoft.Net.Http) fornece o `HttpClient` classe, que é usado para enviar e receber solicitações via HTTP. Ele fornece funcionalidade para enviar solicitações HTTP e receber respostas HTTP de um recurso identificado pelo URI. Cada solicitação é enviada como uma operação assíncrona. Para obter mais informações sobre as operações assíncronas, consulte [visão geral do suporte assíncrono](~/cross-platform/platform/async.md).

O `HttpResponseMessage` classe representa uma mensagem de resposta HTTP recebida do serviço da web depois que foi feita uma solicitação HTTP. Ele contém informações sobre a resposta, incluindo o código de status, cabeçalhos e corpo. O `HttpContent` classe representa o corpo de HTTP e cabeçalhos de conteúdo, como `Content-Type` e `Content-Encoding`. O conteúdo pode ser lido usando qualquer um dos `ReadAs` métodos, tais como `ReadAsStringAsync` e `ReadAsByteArrayAsync`, dependendo do formato dos dados.

Para obter mais informações sobre o `HttpClient` classe, consulte [criando o objeto HTTPClient](~/xamarin-forms/data-cloud/consuming/rest.md).

<a name="Using_HTTPWebRequest" />

### <a name="httpwebrequest"></a>HTTPWebRequest

Chamando serviços web com `HTTPWebRequest` envolve:

-  Criando a instância de solicitação para um URI específico.
-  Definindo várias propriedades HTTP na instância de solicitação.
-  Recuperando um `HttpWebResponse` da solicitação.
-  Lendo dados da resposta.

Por exemplo, o código a seguir recupera os dados dos Estados Unidos Serviço de web biblioteca de medicina nacional:

```csharp
var rxcui = "198440";
var request = HttpWebRequest.Create(string.Format(@"http://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui));
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

O exemplo acima cria um `HttpWebRequest` que retornará dados formatados como JSON. Os dados são retornados em uma `HttpWebResponse`, do qual um `StreamReader` pode ser obtido para ler os dados.

<a name="Using_RESTSHARP" />

### <a name="restsharp"></a>RestSharp

Outra abordagem para consumir serviços REST está usando o [RestSharp](http://restsharp.org/) biblioteca. RestSharp encapsula solicitações HTTP, incluindo suporte para recuperar os resultados como conteúdo de cadeia de caracteres bruta ou como um desserializado C# objeto. Por exemplo, o código a seguir faz uma solicitação para os EUA Cadeia de caracteres formatada de serviço web de biblioteca da medicina, nacionais e recupera os resultados como JSON:

```csharp
var request = new RestRequest(string.Format("{0}/allinfo", rxcui));
request.RequestFormat = DataFormat.Json;
var response = Client.Execute(request);
if(string.IsNullOrWhiteSpace(response.Content) || response.StatusCode != System.Net.HttpStatusCode.OK) {
       return null;
}
rxTerm = DeserializeRxTerm(response.Content);
```

`DeserializeRxTerm` é um método que levará a cadeia de caracteres JSON bruta do `RestSharp.RestResponse.Content` propriedade e convertê-lo em um C# objeto. Desserializar os dados retornados de serviços da web é discutido posteriormente neste artigo.

<a name="Using_NSUrlconnection" />

### <a name="nsurlconnection"></a>NSUrlConnection

Além das classes disponíveis na base de Mono biblioteca de classes (BCL), como `HttpWebRequest`e de terceiros C# bibliotecas, como RestSharp, classes específicas de plataforma também estão disponíveis para consumir serviços web. Por exemplo, no iOS, o `NSUrlConnection` e `NSMutableUrlRequest` classes podem ser usadas.

O exemplo de código a seguir mostra como chamar dos EUA Serviço de web do National biblioteca da medicina usando classes do iOS:

```csharp
var rxcui = "198440";
var request = new NSMutableUrlRequest(new NSUrl(string.Format("http://rxnav.nlm.nih.gov/REST/RxTerms/rxcui/{0}/allinfo", rxcui)),
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

Geralmente, as classes específicas da plataforma para consumir serviços web devem ser limitadas a cenários em que o código nativo está sendo movido para o C#. Sempre que possível, código de acesso do serviço web devem ser portáteis para que ele possa ser compartilhado entre plataformas.

<a name="Using_ServiceStack_Client" />

### <a name="servicestack"></a>ServiceStack

Outra opção para chamar serviços da web é o [pilha de serviço](http://www.servicestack.net/) biblioteca. Por exemplo, o código a seguir mostra como usar a pilha de serviço `IServiceClient.GetAsync` método emitir uma solicitação de serviço:

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
> Enquanto as ferramentas, como o ServiceStack e RestSharp facilitam chamar e consumir POSICIONAR os serviços, às vezes, é incomum para consumir XML ou JSON que não estão em conformidade com o padrão _DataContract_ convenções de serialização. Se necessário, invocar a solicitação e manipular a serialização apropriada explicitamente usando a biblioteca de ServiceStack.Text discutida abaixo.


<a name="Options_for_consuming_RESTful_data" />

## <a name="consuming-restful-data"></a>Consumindo dados RESTful

Serviços web rESTful normalmente usam mensagens JSON para retornar dados ao cliente. JSON é uma baseado em texto, troca de dados formato que produz compact cargas, que resulta nos requisitos de largura de banda reduzidos ao enviar dados. Nesta seção, mecanismos para o consumo de RESTful respostas em JSON e XML de antigo sem formatação (POX) serão examinados.

<a name="Using_System.JSON" />

### <a name="systemjson"></a>System.JSON

A plataforma Xamarin é fornecido com suporte para JSON fora da caixa. Usando um `JsonObject`, os resultados podem ser recuperados, conforme mostrado no exemplo de código a seguir:

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

No entanto, é importante estar ciente de que o `System.Json` ferramentas carregam a totalidade dos dados na memória.

<a name="Using_JSON.NET" />

### <a name="jsonnet"></a>JSON.NET

O [biblioteca NewtonSoft JSON.NET](http://www.newtonsoft.com/json) é uma biblioteca amplamente usada para serialização e desserialização de mensagens JSON. O exemplo de código a seguir mostra como usar o JSON.NET para desserializar uma mensagem JSON em um C# objeto:

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

<a name="Using_ServiceStack.Text" />

### <a name="servicestacktext"></a>ServiceStack.Text

ServiceStack.Text é uma biblioteca de serialização JSON projetada para trabalhar com a biblioteca ServiceStack. O exemplo de código a seguir mostra como analisar o JSON usando um `ServiceStack.Text.JsonObject`:

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

<a name="Using_System.Xml.Linq" />

### <a name="systemxmllinq"></a>System.Xml.Linq

No caso de consumir um serviço da web REST baseado em XML, LINQ to XML pode ser usado para analisar o XML e preencher um C# do objeto em linha, conforme demonstrado no exemplo de código a seguir:

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

<a name="asmx" />

## <a name="aspnet-web-service-asmx"></a>Serviço Web do ASP.NET (ASMX)

ASMX fornece a capacidade de criar serviços web que enviam mensagens usando o SOAP Simple Object Access Protocol (). SOAP é um protocolo independente de plataforma e linguagem para criar e acessar serviços web. Os consumidores de um serviço ASMX não precisam saber nada sobre a plataforma, um modelo de objeto ou uma linguagem de programação usada para implementar o serviço. Eles só precisam entender como enviar e receber mensagens SOAP.

Uma mensagem SOAP é um documento XML que contém os seguintes elementos:

- Um elemento raiz chamado *Envelope* que identifica o documento XML como uma mensagem SOAP.
- Um recurso opcional *cabeçalho* elemento que contém informações específicas do aplicativo, como dados de autenticação. Se o *cabeçalho* elemento está presente ele deve ser o primeiro elemento filho do *Envelope* elemento.
- Obrigatória *corpo* elemento que contém a mensagem SOAP para o destinatário.
- Um recurso opcional *falha* elemento que é usado para indicar as mensagens de erro. Se o *falhas* elemento estiver presente, ele deve ser um elemento filho do *corpo* elemento.

SOAP pode operar em vários protocolos de transporte, incluindo HTTP, SMTP, TCP e UDP. No entanto, um serviço ASMX só pode operar sobre HTTP. A plataforma Xamarin dá suporte a implementações padrão de SOAP 1.1 via HTTP, e isso inclui suporte para muitas das configurações de serviço padrão do ASMX.

### <a name="generating-a-proxy"></a>Gerar um Proxy

Um *proxy* deve ser gerado para consumir um serviço ASMX, que permite que o aplicativo para se conectar ao serviço. O proxy é construído por consumo metadados de serviço que define os métodos e a configuração de serviço associado. Esses metadados são expostos como um documento de descrição linguagem WSDL (Web Services) que é gerado pelo serviço web. O proxy é criado usando o Visual Studio para Mac ou Visual Studio para adicionar uma referência da web para o serviço web para os projetos específicos da plataforma.

A URL do serviço web pode ser uma origem remota hospedada ou recurso do sistema de arquivos local acessível por meio de `file:///` prefixo de caminho, por exemplo:

```csharp
file:///Users/myUserName/projects/MyProjectName/service.wsdl
```

[![](images/add-webreference-dialog.png "A URL do serviço web pode ser uma fonte remota hospedado ou acessível por meio do prefixo de caminho do arquivo de recurso do sistema de arquivos local")](images/add-webreference-dialog.png#lightbox)

Isso gera o proxy na pasta do projeto da Web ou referências de serviço. Como um proxy é gerado no código, ele não deve ser modificado.

<a name="Manually_adding_a_proxy_to_a_project" />

#### <a name="manually-adding-a-proxy-to-a-project"></a>Adicionar manualmente um Proxy para um projeto

Se você tiver um proxy existente que foi gerado usando ferramentas compatíveis, essa saída pode ser consumida quando incluídas como parte do seu projeto. No Visual Studio para Mac, use o **adicionar arquivos...** opção de menu para adicionar o proxy. Além disso, isso exige *System* seja referenciada explicitamente usando o **adicionar referências...** caixa de diálogo.

### <a name="consuming-the-proxy"></a>Consumindo o Proxy

As classes de proxy geradas fornecem métodos para consumir o serviço web que usam o padrão de design do modelo de programação assíncrona (APM). Nesse padrão de uma operação assíncrona é implementada como dois métodos chamados *BeginOperationName* e *EndOperationName*, que começam e terminam a operação assíncrona.

O *BeginOperationName* método inicia a operação assíncrona e retorna um objeto que implementa o `IAsyncResult` interface. Depois de chamar *BeginOperationName*, um aplicativo pode continuar a executar as instruções no thread de chamada, enquanto a operação assíncrona ocorre em um pool de threads.

Para cada chamada para *BeginOperationName*, o aplicativo também deve chamar *EndOperationName* para obter os resultados da operação. O valor de retorno *EndOperationName* é do mesmo tipo retornado pelo método de serviço da web síncronas. O exemplo de código a seguir mostra um exemplo disso:

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

A tarefa paralela TPL (biblioteca) pode simplificar o processo de consumir um par de métodos de início/término do APM, encapsulando as operações assíncronas no mesmo `Task` objeto. Esse encapsulamento é fornecido pelas várias sobrecargas do `Task.Factory.FromAsync` método. Esse método cria uma `Task` que executa o `TodoService.EndGetTodoItems` método uma vez a `TodoService.BeginGetTodoItems` método é concluído, com o `null` parâmetro que indica que nenhum dado está sendo passado para o `BeginGetTodoItems` delegar. Por fim, o valor da `TaskCreationOptions` enumeração que especifica que o comportamento padrão para a criação e execução de tarefas deve ser usado.

Para obter mais informações sobre o APM, consulte [modelo de programação assíncrona](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) e [.NET Framework programação assíncrona tradicional e TPL](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) no MSDN.

Para obter mais informações sobre como consumir um serviço ASMX, consulte [consumindo um serviço Web do ASP.NET (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md).

<a name="wcf" />

## <a name="windows-communication-foundation-wcf"></a>Windows Communication Foundation (WCF)

O WCF é a estrutura unificada da Microsoft para a criação de aplicativos orientados a serviço. Ele permite aos desenvolvedores compilar aplicativos distribuídos seguros, confiáveis, transacionados e interoperáveis.

WCF descreve um serviço com uma variedade de contratos diferentes que incluem o seguinte:

- **Contratos de dados** – definir as estruturas de dados que formam a base para o conteúdo dentro de uma mensagem.
- **Contratos de mensagem** – compor mensagens de contratos de dados existente.
- **Contratos de falha** – permitir que as falhas SOAP personalizadas sejam especificados.
- **Contratos de serviço** – especificar as operações que dão suporte a serviços e as mensagens necessárias para interagir com cada operação. Eles também pode especificar qualquer comportamento de falha personalizado que pode ser associado a operações em cada serviço.

Há diferenças entre os serviços de Web do ASP.NET (ASMX) e WCF, mas é importante entender que o WCF oferece suporte os mesmos recursos que fornece ASMX – mensagens SOAP sobre HTTP.

> [!IMPORTANT]
> O suporte de plataforma do Xamarin para o WCF é limitado a texto codificado mensagens SOAP sobre HTTP/HTTPS usando o `BasicHttpBinding` classe. Além disso, o suporte do WCF requer o uso das ferramentas disponíveis somente em um ambiente do Windows para gerar o proxy.

### <a name="generating-a-proxy"></a>Gerar um Proxy

Um *proxy* deve ser gerado para consumir um serviço WCF, que permite que o aplicativo para se conectar ao serviço. O proxy é construído por consumo metadados de serviço que definem os métodos e a configuração de serviço associado. Esses metadados são expostos na forma de um documento de descrição linguagem WSDL (Web Services) que é gerado pelo serviço web. O proxy pode ser compilado usando o Microsoft WCF Web Service Reference Provider no Visual Studio 2017 para adicionar uma referência de serviço para o serviço web a uma biblioteca .NET Standard.

Uma alternativa para criar o proxy usando o Microsoft WCF Web Service Reference Provider no Visual Studio 2017 é usar a ferramenta Utilitário de metadados de ServiceModel (svcutil.exe). Para obter mais informações, consulte [ferramenta de utilitário de metadados ServiceModel (Svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

### <a name="configuring-the-proxy"></a>Configurando o Proxy

Configurando o proxy gerado geralmente leva dois argumentos de configuração (dependendo do SOAP 1.1/ASMX ou WCF) durante a inicialização: o `EndpointAddress` e/ou as informações de associação associados, conforme mostrado no exemplo a seguir:

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

Uma associação é usada para especificar o transporte, codificação e detalhes de protocolo necessários para aplicativos e serviços para se comunicar entre si. O `BasicHttpBinding` Especifica que as mensagens SOAP texto codificado serão enviadas através do protocolo de transporte HTTP. Especificação de um endereço de ponto de extremidade permite que o aplicativo para se conectar a diferentes instâncias do serviço do WCF, desde que há várias instâncias publicadas.

### <a name="consuming-the-proxy"></a>Consumindo o Proxy

As classes de proxy geradas fornecem métodos para consumir os serviços web que usam o padrão de design do modelo de programação assíncrona (APM). Nesse padrão, uma operação assíncrona é implementada como dois métodos chamados *BeginOperationName* e *EndOperationName*, que começam e terminam a operação assíncrona.

O *BeginOperationName* método inicia a operação assíncrona e retorna um objeto que implementa o `IAsyncResult` interface. Depois de chamar *BeginOperationName*, um aplicativo pode continuar a executar as instruções no thread de chamada, enquanto a operação assíncrona ocorre em um pool de threads.

Para cada chamada para *BeginOperationName*, o aplicativo também deve chamar *EndOperationName* para obter os resultados da operação. O valor de retorno *EndOperationName* é do mesmo tipo retornado pelo método de serviço da web síncronas. O exemplo de código a seguir mostra um exemplo disso:

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

A tarefa paralela TPL (biblioteca) pode simplificar o processo de consumir um par de métodos de início/término do APM, encapsulando as operações assíncronas no mesmo `Task` objeto. Esse encapsulamento é fornecido pelas várias sobrecargas do `Task.Factory.FromAsync` método. Esse método cria uma `Task` que executa o `TodoServiceClient.EndGetTodoItems` método uma vez a `TodoServiceClient.BeginGetTodoItems` método é concluído, com o `null` parâmetro que indica que nenhum dado está sendo passado para o `BeginGetTodoItems` delegar. Por fim, o valor da `TaskCreationOptions` enumeração que especifica que o comportamento padrão para a criação e execução de tarefas deve ser usado.

Para obter mais informações sobre o APM, consulte [modelo de programação assíncrona](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) e [.NET Framework programação assíncrona tradicional e TPL](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) no MSDN.

Para obter mais informações sobre como consumir um serviço WCF, consulte [consumindo um serviço de Web do Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/consuming/wcf.md).

<a name="Calling_a_WCF_Service_with_Transport_Security" />

#### <a name="using-transport-security"></a>Usando a segurança de transporte

Os serviços WCF podem empregar a segurança em nível de transporte para se proteger contra interceptação de mensagens. A plataforma Xamarin dá suporte a associações que emprega segurança em nível de transporte usando SSL. No entanto, pode haver casos em que a pilha talvez seja necessário validar o certificado, o que resulta em comportamento imprevisto. A validação pode ser substituída, registrando um `ServerCertificateValidationCallback` delegado antes de invocar o serviço, conforme demonstrado no exemplo de código a seguir:

```csharp
System.Net.ServicePointManager.ServerCertificateValidationCallback +=
(se, cert, chain, sslerror) => { return true; };
```

Isso mantém a criptografia de transporte, ignorando a validação do certificado do lado do servidor. No entanto, essa abordagem efetivamente ignora as questões de confiança associadas ao certificado e pode não ser apropriada. Para obter mais informações, consulte [usando Respectfully raízes confiáveis](https://www.mono-project.com/UsingTrustedRootsRespectfully) nos [mono project.com](https://www.mono-project.com).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

#### <a name="using-client-credential-security"></a>Usando a segurança de credencial de cliente

Os serviços WCF também podem exigir os clientes de serviço autenticar usando credenciais. A plataforma Xamarin não suporta o protocolo WS-Security, que permite que os clientes enviem credenciais dentro do envelope SOAP da mensagem. No entanto, a plataforma Xamarin dá suporte a capacidade de enviar as credenciais de autenticação básica HTTP para o servidor, especificando o apropriada `ClientCredentialType`:

```csharp
basicHttpBinding.Security.Transport.ClientCredentialType = HttpClientCredentialType.Basic;
```

Em seguida, é podem especificar credenciais de autenticação básica:

```csharp
client.ClientCredentials.UserName.UserName = @"foo";
client.ClientCredentials.UserName.Password = @"mrsnuggles";
```

No exemplo acima, se você receber a mensagem "Ficou sem trampolines 0 do tipo" você pode aumentar o número de trampolines tipo 0, adicionando o `–aot “trampolines={number of trampolines}”` argumento para a compilação. Para obter mais informações, confira [Solução de problemas](~/ios/troubleshooting/troubleshooting.md#trampolines).

Para obter mais informações sobre a autenticação básica HTTP, embora no contexto de um serviço web REST, consulte [autenticar um serviço Web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md).

## <a name="related-links"></a>Links relacionados

- [Serviços Web no xamarin. Forms](~/xamarin-forms/data-cloud/index.md)
- [Ferramenta de utilitário de metadados ServiceModel (svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
- [BasicHttpBinding](https://msdn.microsoft.com/library/system.servicemodel.basichttpbinding.aspx)
