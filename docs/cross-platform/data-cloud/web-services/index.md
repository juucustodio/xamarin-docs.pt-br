---
title: Introdução aos serviços Web
description: Este guia demonstra como utilizar tecnologias de serviço web diferente. Os tópicos abordados incluem a se comunicar com serviços REST, serviços SOAP e serviços do Windows Communication Foundation.
ms.prod: xamarin
ms.assetid: 72627B90-586A-02B6-E231-F7CE015A1B97
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 5c07443519391192f7bdb62cc19ff8b1f87b2558
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="introduction-to-web-services"></a>Introdução aos serviços Web

_Este guia demonstra como utilizar tecnologias de serviço web diferente. Os tópicos abordados incluem a se comunicar com serviços REST, serviços SOAP e serviços do Windows Communication Foundation._

Para funcionar corretamente, muitos aplicativos móveis são dependentes na nuvem, e então integrar serviços web aplicativos móveis é um cenário comum. A plataforma Xamarin dá suporte a tecnologias de serviço web de diferentes de consumo e inclui suporte interno e de terceiros para consumir serviços RESTful, ASMX e Windows Communication Foundation (WCF).

Este artigo aborda os seguintes tópicos:

- [Serviços REST](#rest)
- [Serviços Web do ASP.Net (ASMX)](#asmx)
- [Serviços WCF](#wcf)

Para clientes que usam o xamarin. Forms, há exemplos completos usando cada uma dessas tecnologias no [xamarin. Forms Web Services](~/xamarin-forms/data-cloud/index.md) documentação.

> [!IMPORTANT]
> No iOS 9, a segurança de transporte do aplicativo (ATS) impõe conexões seguras entre os recursos de internet (como o servidor de back-end do aplicativo) e o aplicativo, impedindo assim a divulgação acidental de informações confidenciais.
> Como ATS está habilitado por padrão em aplicativos criados para o iOS 9, todas as conexões serão sujeitos a requisitos de segurança ATS. Se as conexões não atender a esses requisitos, eles falham com uma exceção.

Você pode recusar ATS se não for possível usar o `HTTPS` de protocolo e proteger a comunicação para recursos da internet. Isso pode ser obtido pela atualização do aplicativo **Info. plist** arquivo. Para obter mais informações, consulte [segurança de transporte do aplicativo](~/ios/app-fundamentals/ats.md).



<a name="rest" />

## <a name="rest"></a>REST

REST Representational State Transfer () é um estilo de arquitetura para a criação de serviços web. Solicitações REST são feitas via HTTP usando os mesmos verbos HTTP que navegadores da web usam para recuperar as páginas da web e para enviar dados para servidores. Os verbos são:

- **OBTER** – esta operação é usada para recuperar dados do serviço da web.
- **POST** – esta operação é usada para criar um novo item de dados no serviço da web.
- **COLOCAR** – esta operação é usada para atualizar um item de dados no serviço da web.
- **PATCH** – esta operação é usada para atualizar um item de dados no serviço da web, que descreve um conjunto de instruções sobre como o item deve ser modificado. Este verbo não é usado no aplicativo de exemplo.
- **Excluir** – esta operação é usada para excluir um item de dados no serviço da web.

APIs que seguem REST do serviço Web são chamadas de APIs REST e são definidos usando:

- Um URI de base.
- Métodos HTTP, como GET, POST, PUT, PATCH ou DELETE.
- Um tipo de mídia para os dados, como JSON JavaScript Object Notation ().

A simplicidade do REST ajudou a torná-lo o principal método para acessar os serviços da web em aplicativos móveis.

## <a name="consuming-rest-services"></a>Consumir serviços REST

Há um número de classes que podem ser usadas para consumir serviços REST e bibliotecas e as subseções a seguir discutem-los. Para obter mais informações sobre como consumir um serviço REST, consulte [consumir um serviço Web RESTful](~/xamarin-forms/data-cloud/consuming/rest.md).

### <a name="httpclient"></a>HttpClient

O [bibliotecas de cliente HTTP Microsoft](https://www.nuget.org/packages/Microsoft.Net.Http) fornece o `HttpClient` classe, que é usada para enviar e receber solicitações via HTTP. Ele fornece funcionalidade para enviar solicitações HTTP e receber respostas HTTP de um recurso identificado pelo URI. Cada solicitação é enviada como uma operação assíncrona. Para obter mais informações sobre as operações assíncronas, consulte [visão geral de suporte assíncrono](~/cross-platform/platform/async.md).

O `HttpResponseMessage` classe representa uma mensagem de resposta HTTP recebida do serviço da web depois que foi feita uma solicitação HTTP. Ele contém informações sobre a resposta, incluindo o código de status, cabeçalhos e corpo. O `HttpContent` classe representa o corpo HTTP e cabeçalhos de conteúdo, como `Content-Type` e `Content-Encoding`. O conteúdo pode ser lido usando qualquer um do `ReadAs` métodos, como `ReadAsStringAsync` e `ReadAsByteArrayAsync`, dependendo do formato dos dados.

Para obter mais informações sobre o `HttpClient` de classe, consulte [criar o objeto HTTPClient](~/xamarin-forms/data-cloud/consuming/rest.md).

<a name="Using_HTTPWebRequest" />

### <a name="httpwebrequest"></a>HTTPWebRequest

Chamada de serviços web com `HTTPWebRequest` envolve:

-  Criar a instância de solicitação para um URI específico.
-  Definindo várias propriedades HTTP na instância de solicitação.
-  Recuperando um `HttpWebResponse` da solicitação.
-  Lendo dados de resposta.

Por exemplo, o código a seguir recupera os dados dos Estados Unidos Serviço de web biblioteca de medicina National:

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

Outra abordagem para consumir serviços REST é usando o [RestSharp](http://restsharp.org/) biblioteca. RestSharp encapsula solicitações HTTP, incluindo suporte para recuperar os resultados como conteúdo de cadeia de caracteres bruta ou como um objeto desserializado do c#. Por exemplo, o código a seguir faz uma solicitação para os EUA Cadeia de caracteres formatada de serviço da web de biblioteca de medicina nacional e recupera os resultados como JSON:

```csharp
var request = new RestRequest(string.Format("{0}/allinfo", rxcui));
request.RequestFormat = DataFormat.Json;
var response = Client.Execute(request);
if(string.IsNullOrWhiteSpace(response.Content) || response.StatusCode != System.Net.HttpStatusCode.OK) {
       return null;
}
rxTerm = DeserializeRxTerm(response.Content);
```

`DeserializeRxTerm` é um método que levará a cadeia de caracteres JSON bruta do `RestSharp.RestResponse.Content` propriedade e convertê-la em um objeto do c#. Desserializar os dados retornados de serviços da web é abordado posteriormente neste artigo.

<a name="Using_NSUrlconnection" />

### <a name="nsurlconnection"></a>NSUrlConnection

Além das classes disponíveis na base de Mono classe BCL (biblioteca), como `HttpWebRequest`e bibliotecas de terceiros c#, como RestSharp, classes específicas da plataforma também estão disponíveis para consumir serviços da web. Por exemplo, no iOS, o `NSUrlConnection` e `NSMutableUrlRequest` classes podem ser usadas.

O exemplo de código a seguir mostra como chamar dos EUA National biblioteca de medicina web service usando classes do iOS:

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

Em geral, classes específicas da plataforma para consumir serviços da web devem ser limitados a cenários onde o código nativo está sendo movido para o c#. Sempre que possível, código de acesso do serviço web devem ser portáteis, para que ele possa ser compartilhado entre plataformas.

<a name="Using_ServiceStack_Client" />

### <a name="servicestack"></a>ServiceStack

Outra opção para chamar serviços da web é o [serviço pilha](http://www.servicestack.net/) biblioteca. Por exemplo, o código a seguir mostra como usar a pilha de serviço `IServiceClient.GetAsync` método para emitir uma solicitação de serviço:

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
> Embora ferramentas como ServiceStack e RestSharp facilitam a chamada e consumir REST de serviços, às vezes, é incomum para consumir XML ou JSON que não estão em conformidade com o padrão de _DataContract_ convenções de serialização. Se necessário, invocar a solicitação e lidar com a serialização apropriada explicitamente usando a biblioteca de ServiceStack.Text discutida abaixo.


<a name="Options_for_consuming_RESTful_data" />

## <a name="consuming-restful-data"></a>Consumo de dados RESTful

Serviços web rESTful normalmente usam mensagens JSON para retornar dados ao cliente. JSON é um baseado em texto de intercâmbio de dados formato que produz compact cargas, que resulta em requisitos de largura de banda reduzido ao enviar dados. Nesta seção, mecanismos para consumir RESTful respostas em JSON e XML de antigo simples (POX) serão examinados.

<a name="Using_System.JSON" />

### <a name="systemjson"></a>System.JSON

A plataforma Xamarin vem com suporte para JSON fora da caixa. Usando um `JsonObject`, é possível recuperar resultados conforme mostrado no exemplo de código a seguir:

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

O [NewtonSoft JSON.NET biblioteca](http://www.newtonsoft.com/json) é uma biblioteca amplamente usada para serialização e desserialização JSON mensagens. O exemplo de código a seguir mostra como usar o JSON.NET para desserializar uma mensagem JSON em um objeto do c#:

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

No caso de consumir um serviço da web REST baseado em XML, LINQ to XML pode ser usado para analisar o XML e popular um c# embutido de objeto, como demonstrado no exemplo de código a seguir:

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

ASMX fornece a capacidade de criar serviços web que enviam mensagens usando o protocolo de acesso de objeto simples (SOAP). SOAP é um protocolo independente de plataforma e independente de linguagem para criar e acessar serviços da web. Não é necessário que os consumidores de um serviço ASMX sabe nada sobre a plataforma, um modelo de objeto ou uma linguagem de programação usada para implementar o serviço. Eles só precisam entender como enviar e receber mensagens SOAP.

Uma mensagem SOAP é um documento XML que contém os seguintes elementos:

- Um elemento raiz chamado *Envelope* que identifica o documento XML como uma mensagem SOAP.
- Um recurso opcional *cabeçalho* elemento que contém informações específicas do aplicativo, como dados de autenticação. Se o *cabeçalho* estiver presente ele deve ser o primeiro elemento filho do *Envelope* elemento.
- Necessário *corpo* elemento que contém a mensagem SOAP destinada para o destinatário.
- Um recurso opcional *falha* elemento que é usado para indicar as mensagens de erro. Se o *falha* estiver presente, ele deve ser um elemento filho do *corpo* elemento.

SOAP pode operar em vários protocolos de transporte, incluindo HTTP, SMTP, TCP e UDP. No entanto, um serviço ASMX só pode operar em HTTP. A plataforma Xamarin dá suporte a implementações padrão de SOAP 1.1 em HTTP, e isso inclui suporte para muitas das configurações de serviço ASMX padrão.

### <a name="generating-a-proxy"></a>Gerar um Proxy

Um *proxy* devem ser geradas para consumir um serviço ASMX, que permite que o aplicativo se conecte ao serviço. O proxy é construído por consumo metadados de serviço que define os métodos e a configuração de serviço associado. Esses metadados são expostos como um documento WSDL Web Services Description Language () que é gerado pelo serviço da web. O proxy é criado usando o Visual Studio para Mac ou o Visual Studio para adicionar uma referência da web para o serviço web para os projetos de plataforma específica.

A URL do serviço web pode ser uma origem remota hospedada ou recurso do sistema de arquivos local acessível por meio de `file:///` prefixo de caminho, por exemplo:

```csharp
file:///Users/myUserName/projects/MyProjectName/service.wsdl
```

[![](images/add-webreference-dialog.png "A URL do serviço web pode ser uma fonte remota hospedado ou acessível por meio do prefixo de caminho do arquivo de recurso do sistema de arquivos local")](images/add-webreference-dialog.png#lightbox)

Isso gera o proxy na pasta da Web ou serviço referências do projeto. Como um proxy gerado código, não deve ser modificado.

<a name="Manually_adding_a_proxy_to_a_project" />

#### <a name="manually-adding-a-proxy-to-a-project"></a>Adicionar manualmente um Proxy para um projeto

Se você tiver um proxy existente que foi gerado usando ferramentas compatíveis, essa saída pode ser consumida quando incluídos como parte do seu projeto. No Visual Studio para Mac, use o **adicionar arquivos...** opção de menu para adicionar o proxy. Além disso, isso requer *System.Web.Services.dll* seja referenciada explicitamente usando o **adicionar referências...** caixa de diálogo.

### <a name="consuming-the-proxy"></a>Consumindo o Proxy

As classes de proxy gerado fornecem métodos para consumir o serviço web que usam o padrão de design de modelo de programação assíncrona (APM). Nesse padrão de uma operação assíncrona é implementada como dois métodos chamados *BeginOperationName* e *EndOperationName*, que começam e terminam a operação assíncrona.

O *BeginOperationName* método inicia a operação assíncrona e retorna um objeto que implementa o `IAsyncResult` interface. Depois de chamar *BeginOperationName*, um aplicativo pode continuar a executar as instruções no thread de chamada, enquanto a operação assíncrona ocorre em um pool de threads.

Cada chamada para *BeginOperationName*, o aplicativo também deve chamar *EndOperationName* para obter os resultados da operação. O valor de retorno *EndOperationName* é do mesmo tipo retornado pelo método de serviço da web síncrono. O exemplo de código a seguir mostra um exemplo disso:

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

O biblioteca paralela de tarefas (TPL) pode simplificar o processo de consumir um par de métodos begin/end APM encapsulando as operações assíncronas no mesmo `Task` objeto. Esse encapsulamento é fornecido por várias sobrecargas do `Task.Factory.FromAsync` método. Esse método cria um `Task` que executa o `TodoService.EndGetTodoItems` método uma vez o `TodoService.BeginGetTodoItems` método é concluído, com o `null` parâmetro indicando que nenhum dado está sendo passado para o `BeginGetTodoItems` delegar. Por fim, o valor da `TaskCreationOptions` enumeração Especifica que o comportamento padrão para a criação e execução de tarefas deve ser usado.

Para obter mais informações sobre o APM, consulte [modelo de programação assíncrona](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) e [TPL e estrutura de programação assíncrona tradicional .NET](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) no MSDN.

Para obter mais informações sobre como consumir um serviço ASMX, consulte [consumir um serviço Web do ASP.NET (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md).

<a name="wcf" />

## <a name="windows-communication-foundation-wcf"></a>Windows Communication Foundation (WCF)

O WCF é a estrutura unificada da Microsoft para criar aplicativos orientados a serviços. Ele permite aos desenvolvedores compilar aplicativos distribuídos seguros, confiáveis, transacionados e interoperáveis.

WCF descreve um serviço com uma variedade de diferentes contratos que incluem o seguinte:

- **Contratos de dados** – defina as estruturas de dados que formam a base para o conteúdo dentro de uma mensagem.
- **Contratos de mensagem** – compor mensagens de contratos de dados existente.
- **Contratos de falha** – permitem que as falhas de SOAP personalizadas seja especificada.
- **Contratos de serviço** – especifique as operações que oferecem suporte a serviços e as mensagens necessárias para interagir com cada operação. Eles também pode especificar qualquer comportamento personalizado falhas que pode ser associado a operações em cada serviço.

Há diferenças entre os serviços de Web do ASP.NET (ASMX) e WCF, mas é importante entender que o WCF suporta os mesmos recursos que fornece ASMX – mensagens SOAP sobre HTTP.

Em geral, a plataforma de Xamarin aceita o mesmo subconjunto de cliente do WCF que acompanha o runtime do Silverlight. Isso inclui as implementações de codificação e o protocolo mais comuns do WCF — texto codificado mensagens SOAP sobre HTTP usando o protocolo de transporte de `BasicHttpBinding` classe. Além disso, o suporte do WCF requer o uso de ferramentas disponíveis somente em um ambiente do Windows para gerar o proxy.

Para obter mais informações sobre como usar a plataforma Xamarin para consumir um WCF web service com a `BasicHttpBinding` de classe, consulte [passo a passo - Trabalhando com o WCF](walkthrough-working-with-wcf.md).

### <a name="generating-a-proxy"></a>Gerar um Proxy

Um *proxy* devem ser geradas para consumir um serviço WCF, que permite que o aplicativo se conecte ao serviço. O proxy é construído por consumo metadados de serviço que define os métodos e a configuração de serviço associado. Esses metadados são expostos na forma de um documento WSDL Web Services Description Language () que é gerada pelo serviço da web. O proxy pode ser criado usando o provedor de referência de serviço Web do Microsoft WCF no Visual Studio de 2017 para adicionar uma referência de serviço para o serviço web a uma biblioteca do .NET padrão.

Uma alternativa para criar o proxy usando o provedor de referência de serviço Web do Microsoft WCF no Visual Studio de 2017 é usar a ferramenta de utilitário de metadados do ServiceModel (svcutil.exe). Para obter mais informações, consulte [Ferramenta Utilitária de metadados ServiceModel (Svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

### <a name="configuring-the-proxy"></a>Configurando o Proxy

Configurar o proxy gerado geralmente usam dois argumentos de configuração (dependendo do SOAP 1.1/ASMX ou WCF) durante a inicialização: o `EndpointAddress` e/ou as informações de associação associada, conforme mostrado no exemplo a seguir:

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

Uma associação é usada para especificar o transporte, a codificação e detalhes de protocolo necessárias para aplicativos e serviços para se comunicar entre si. O `BasicHttpBinding` Especifica que as mensagens SOAP texto codificado serão enviadas através do protocolo de transporte HTTP. Especificação de um endereço de ponto de extremidade permite que o aplicativo se conectar a diferentes instâncias do serviço WCF, desde que há várias instâncias publicadas.

### <a name="consuming-the-proxy"></a>Consumindo o Proxy

As classes de proxy gerado fornecem métodos para consumir os serviços web que usam o padrão de design de modelo de programação assíncrona (APM). Nesse padrão, uma operação assíncrona é implementada como dois métodos chamados *BeginOperationName* e *EndOperationName*, que começam e terminam a operação assíncrona.

O *BeginOperationName* método inicia a operação assíncrona e retorna um objeto que implementa o `IAsyncResult` interface. Depois de chamar *BeginOperationName*, um aplicativo pode continuar a executar as instruções no thread de chamada, enquanto a operação assíncrona ocorre em um pool de threads.

Cada chamada para *BeginOperationName*, o aplicativo também deve chamar *EndOperationName* para obter os resultados da operação. O valor de retorno *EndOperationName* é do mesmo tipo retornado pelo método de serviço da web síncrono. O exemplo de código a seguir mostra um exemplo disso:

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

O biblioteca paralela de tarefas (TPL) pode simplificar o processo de consumir um par de métodos begin/end APM encapsulando as operações assíncronas no mesmo `Task` objeto. Esse encapsulamento é fornecido por várias sobrecargas do `Task.Factory.FromAsync` método. Esse método cria um `Task` que executa o `TodoServiceClient.EndGetTodoItems` método uma vez o `TodoServiceClient.BeginGetTodoItems` método é concluído, com o `null` parâmetro indicando que nenhum dado está sendo passado para o `BeginGetTodoItems` delegar. Por fim, o valor da `TaskCreationOptions` enumeração Especifica que o comportamento padrão para a criação e execução de tarefas deve ser usado.

Para obter mais informações sobre o APM, consulte [modelo de programação assíncrona](https://msdn.microsoft.com/library/ms228963(v=vs.110).aspx) e [TPL e estrutura de programação assíncrona tradicional .NET](https://msdn.microsoft.com/library/dd997423(v=vs.110).aspx) no MSDN.

Para obter mais informações sobre como consumir um serviço WCF, consulte [consumir um serviço de Web do Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/consuming/wcf.md).

<a name="Calling_a_WCF_Service_with_Transport_Security" />

#### <a name="using-transport-security"></a>Usando a segurança de transporte

Serviços WCF podem empregar segurança em nível de transporte para proteger contra interceptação de mensagens. A plataforma Xamarin dá suporte a associações que utilizam segurança em nível de transporte usando SSL. No entanto, pode haver casos em que a pilha pode ser necessário validar o certificado, o que resulta em comportamento imprevisto. A validação pode ser substituída pelo Registrando um `ServerCertificateValidationCallback` delegado antes de chamar o serviço, conforme demonstrado no exemplo de código a seguir:

```csharp
System.Net.ServicePointManager.ServerCertificateValidationCallback +=
(se, cert, chain, sslerror) => { return true; };
```

Isso mantém a criptografia de transporte ignorando a validação do certificado do servidor. No entanto, essa abordagem efetivamente ignora as questões de confiança associadas ao certificado e pode não ser apropriada. Para obter mais informações, consulte [usando Respectfully raízes confiáveis](http://www.mono-project.com/UsingTrustedRootsRespectfully) na [project.com mono](http://www.mono-project.com).

<a name="Calling_a_WCF_Service_with_Client_Credential_Security" />

#### <a name="using-client-credential-security"></a>Usando a segurança de credencial de cliente

Os serviços WCF também podem exigir os clientes de serviço para autenticar usando credenciais. A plataforma Xamarin não suporta o protocolo WS-Security, que permite que os clientes enviem credenciais dentro do envelope SOAP da mensagem. No entanto, a plataforma Xamarin oferece suporte a capacidade de enviar credenciais de autenticação básica HTTP para o servidor especificando apropriada `ClientCredentialType`:

```csharp
basicHttpBinding.Security.Transport.ClientCredentialType = HttpClientCredentialType.Basic;
```

Em seguida, as credenciais de autenticação básica podem ser especificadas:

```csharp
client.ClientCredentials.UserName.UserName = @"foo";
client.ClientCredentials.UserName.Password = @"mrsnuggles";
```

No exemplo acima, se você receber a mensagem "Ficou sem trampolines do tipo 0" você pode aumentar o número de trampolines do tipo 0, adicionando o `–aot “trampolines={number of trampolines}”` argumento para a compilação. Para obter mais informações, consulte [solução de problemas](~/ios/troubleshooting/troubleshooting.md#trampolines).

Para obter mais informações sobre autenticação básica HTTP, embora no contexto de um serviço web REST, consulte [autenticar um serviço Web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md).

## <a name="summary"></a>Resumo

Este guia demonstrou como utilizar tecnologias de serviço web diferente. Os tópicos abordados incluem a se comunicar com serviços REST, serviços SOAP e serviços do Windows Communication Foundation.

## <a name="related-links"></a>Links relacionados

- [Exemplo de WebServices](https://developer.xamarin.com/samples/mobile/WebServices/WebServiceSamples/)
- [Serviços Web no xamarin. Forms](~/xamarin-forms/data-cloud/index.md)
- [Ferramenta de utilitário de metadados ServiceModel (svcutil.exe)](https://docs.microsoft.com/dotnet/framework/wcf/servicemodel-metadata-utility-tool-svcutil-exe)
- [BasicHttpBinding](http://msdn.microsoft.com/library/system.servicemodel.basichttpbinding.aspx)
