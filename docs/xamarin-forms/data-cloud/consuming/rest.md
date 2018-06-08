---
title: Consumir um serviço Web RESTful
description: Integrar um serviço da web em um aplicativo é um cenário comum. Este artigo demonstra como consumir um serviço web RESTful a partir de um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: B540910C-9C51-416A-AAB9-057BF76489C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 7857f3d4c76fe7d8589c25e4f7fb079f06e136e7
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34846613"
---
# <a name="consuming-a-restful-web-service"></a>Consumir um serviço Web RESTful

_Integrar um serviço da web em um aplicativo é um cenário comum. Este artigo demonstra como consumir um serviço web RESTful a partir de um aplicativo xamarin. Forms._

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

Serviços web rESTful normalmente usam mensagens JSON para retornar dados ao cliente. JSON é um formato de troca de dados baseado em texto que produz compact cargas, que resulta em reduzido requisitos de largura de banda ao enviar dados. O aplicativo de exemplo usa o código-fonte aberto [NewtonSoft JSON.NET biblioteca](http://www.newtonsoft.com/json) para serializar e desserializar de mensagens.

A simplicidade do REST ajudou a torná-lo o principal método para acessar os serviços da web em aplicativos móveis.

As instruções sobre como configurar o serviço REST podem ser encontradas no arquivo Leiame que acompanha o aplicativo de exemplo. No entanto, quando o aplicativo de exemplo é executado, ele se conectará a um serviço hospedado Xamarin REST que fornece acesso somente leitura aos dados, conforme mostrado na seguinte captura de tela:

![](rest-images/portal.png "Aplicativo de exemplo")

> [!NOTE]
> No iOS 9 e superior, a segurança de transporte do aplicativo (ATS) impõe conexões seguras entre os recursos de internet (como o servidor de back-end do aplicativo) e o aplicativo, impedindo assim a divulgação acidental de informações confidenciais. Como ATS está habilitado por padrão em aplicativos criados para o iOS 9, todas as conexões serão sujeitos a requisitos de segurança ATS. Se as conexões não atender a esses requisitos, eles falham com uma exceção.
>
>ATS pode ser incluído em se não for possível usar o **HTTPS** protocolo e proteger a comunicação para recursos da internet. Isso pode ser obtido pela atualização do aplicativo **Info. plist** arquivo. Para obter mais informações, consulte [segurança de transporte do aplicativo](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Consumindo o serviço Web

O serviço REST é gravado usando o ASP.NET Core e fornece as seguintes operações:

|Operação|Método HTTP|URI relativo|Parâmetros|
|--- |--- |--- |--- |
|Obter uma lista de itens pendentes|OBTER|/API todoitems /|
|Criar um novo item de tarefas pendentes|POSTAR|/API todoitems /|JSON formatado TodoItem|
|Atualizar um item pendente|PUT|/API todoitems /|JSON formatado TodoItem|
|Excluir um item pendente|DELETE|/api/todoitems/{id}|

A maioria dos URIs incluem o `TodoItem` ID no caminho. Por exemplo, para excluir o `TodoItem` cuja ID é `6bb8a868-dba1-4f1a-93b7-24ebce87e243`, o cliente envia uma solicitação de exclusão para `http://hostname/api/todoitems/6bb8a868-dba1-4f1a-93b7-24ebce87e243`. Para obter mais informações sobre o modelo de dados usado no aplicativo de amostra, consulte [modelagem de dados](~/xamarin-forms/data-cloud/walkthrough.md).

Quando a estrutura da API Web recebe uma solicitação, ele encaminha a solicitação para uma ação. Essas ações são simplesmente públicos métodos no `TodoItemsController` classe. A estrutura usa uma tabela de roteamento para determinar qual ação a ser invocada em resposta a uma solicitação, que é mostrada no exemplo de código a seguir:

```csharp
config.Routes.MapHttpRoute(
    name: "TodoItemsApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { controller="todoitems", id = RouteParameter.Optional }
);
```

A tabela de roteamento contém um modelo de rota, e quando a estrutura da API Web recebe uma solicitação HTTP, ele tenta corresponder o URI em relação ao modelo de rota na tabela de roteamento. Se uma correspondência de rota não foi encontrada que o cliente recebe um erro de 404 (não encontrado). Se uma rota correspondente for encontrada, API da Web seleciona o controlador e a ação da seguinte maneira:

- Para localizar o controlador, API da Web adiciona "controller" como o valor da *{controlador}* variável.
- Para encontrar a ação, a API da Web examina o método HTTP e analisa ações do controlador são decoradas com o mesmo método HTTP como um atributo.
- O *{id}* variável de espaço reservado é mapeado para um parâmetro de ação.

O serviço REST usa autenticação básica. Para obter mais informações, consulte [autenticar um serviço web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md). Para obter mais informações sobre roteamento API Web do ASP.NET, consulte [roteamento na API da Web ASP.NET](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) no site do ASP.NET. Para obter mais informações sobre o serviço REST usando o ASP.NET Core, consulte [criando serviços de back-end para aplicativos móveis nativos](/aspnet/core/mobile/native-mobile-backend/).

> [!NOTE]
> O aplicativo de exemplo consome o serviço hospedado Xamarin REST que fornece acesso somente leitura para o serviço web. Portanto, as operações que criar, atualizar e excluam dados não alterará os dados consumidos no aplicativo. No entanto, uma versão de núcleo do serviço REST está disponível na **TodoRESTService** pasta em que o acompanha [código de exemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/).
> Se você hospedar o serviço REST por conta própria, completo permite criar, atualizar, ler e excluir o acesso aos dados.

O `HttpClient` classe é usada para enviar e receber solicitações via HTTP. Ele fornece funcionalidade para enviar solicitações HTTP e receber respostas HTTP de um URI identificado o recurso. Cada solicitação é enviada como uma operação assíncrona. Para obter mais informações sobre as operações assíncronas, consulte [visão geral de suporte assíncrono](~/cross-platform/platform/async.md).

O `HttpResponseMessage` classe representa uma mensagem de resposta HTTP recebida do serviço da web depois que foi feita uma solicitação HTTP. Ele contém informações sobre a resposta, incluindo o código de status, cabeçalhos e qualquer corpo. O `HttpContent` classe representa o corpo HTTP e cabeçalhos de conteúdo, como `Content-Type` e `Content-Encoding`. O conteúdo pode ser lido usando qualquer um do `ReadAs` métodos, como `ReadAsStringAsync` e `ReadAsByteArrayAsync`, dependendo do formato dos dados.

### <a name="creating-the-httpclient-object"></a>Criando o objeto HTTPClient

O `HttpClient` instância é declarada no nível de classe para que o objeto reside para desde que o aplicativo precisa para fazer solicitações HTTP, conforme mostrado no exemplo de código a seguir:

```csharp
public class RestService : IRestService
{
  HttpClient client;
  ...

  public RestService ()
  {
    client = new HttpClient ();
    client.MaxResponseContentBufferSize = 256000;
  }
  ...
}
```

O `HttpClient.MaxResponseContentBufferSize` propriedade é usada para especificar o número máximo de bytes em buffer ao ler o conteúdo da mensagem de resposta HTTP. O tamanho padrão desta propriedade é o tamanho máximo de um número inteiro. Portanto, a propriedade é definida como um valor menor, como proteção, para limitar a quantidade de dados que o aplicativo irá aceitar como uma resposta do serviço da web.

### <a name="retrieving-data"></a>Recuperando dados

O `HttpClient.GetAsync` método é usado para enviar a solicitação GET para o serviço web especificado pelo URI e, em seguida, receber a resposta do serviço da web, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  // RestUrl = http://developer.xamarin.com:8081/api/todoitems/
  var uri = new Uri (string.Format (Constants.RestUrl, string.Empty));
  ...
  var response = await client.GetAsync (uri);
  if (response.IsSuccessStatusCode) {
      var content = await response.Content.ReadAsStringAsync ();
      Items = JsonConvert.DeserializeObject <List<TodoItem>> (content);
  }
  ...
}
```

O serviço REST envia um código de status HTTP no `HttpResponseMessage.IsSuccessStatusCode` propriedade, para indicar se a solicitação HTTP foi bem-sucedida ou falhou. Para essa operação, o restante serviço envia o código de status HTTP 200 (Okey) na resposta, que indica que a solicitação foi bem-sucedida e que as informações solicitadas na resposta.

Se a operação de HTTP foi bem-sucedida, o conteúdo da resposta é lida, para exibição. O `HttpResponseMessage.Content` propriedade representa o conteúdo da resposta HTTP e o `HttpContent.ReadAsStringAsync` método grava de maneira assíncrona o conteúdo HTTP para uma cadeia de caracteres. Este conteúdo é, em seguida, convertido de JSON para um `List` de `TodoItem` instâncias.

### <a name="creating-data"></a>Criação de dados

O `HttpClient.PostAsync` método é usado para enviar a solicitação POST para o serviço web especificado pelo URI e, em seguida, para receber a resposta do serviço da web, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  // RestUrl = http://developer.xamarin.com:8081/api/todoitems/
  var uri = new Uri (string.Format (Constants.RestUrl, string.Empty));

  ...
  var json = JsonConvert.SerializeObject (item);
  var content = new StringContent (json, Encoding.UTF8, "application/json");

  HttpResponseMessage response = null;
  if (isNewItem) {
    response = await client.PostAsync (uri, content);
  }
  ...

  if (response.IsSuccessStatusCode) {
    Debug.WriteLine (@"                TodoItem successfully saved.");

  }
  ...
}
```

O `TodoItem` instância é convertida em uma carga JSON para enviar para o serviço web. Essa carga, em seguida, é inserida no corpo do conteúdo HTTP que será enviado ao serviço web antes que a solicitação é feita com o `PostAsync` método.

O serviço REST envia um código de status HTTP no `HttpResponseMessage.IsSuccessStatusCode` propriedade, para indicar se a solicitação HTTP foi bem-sucedida ou falhou. As respostas comuns para essa operação são:

- **201 (criado)** – a solicitação resultou em um novo recurso que está sendo criado antes que a resposta foi enviada.
- **400 (solicitação incorreta)** – a solicitação não é compreendida pelo servidor.
- **409 (conflito)** – a solicitação não pôde ser realizada devido a um conflito no servidor.

### <a name="updating-data"></a>Atualizando dados

O `HttpClient.PutAsync` método é usado para enviar a solicitação PUT para o serviço web especificado pelo URI e, em seguida, receber a resposta do serviço da web, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  response = await client.PutAsync (uri, content);
  ...
}
```
A operação do `PutAsync` método é idêntico de `PostAsync` método que é usado para a criação de dados no serviço da web. No entanto, as respostas possíveis enviadas do serviço web são diferentes.

O serviço REST envia um código de status HTTP no `HttpResponseMessage.IsSuccessStatusCode` propriedade, para indicar se a solicitação HTTP foi bem-sucedida ou falhou. As respostas comuns para essa operação são:

- **204 (sem conteúdo)** – a solicitação foi processada com êxito e a resposta é intencionalmente em branco.
- **400 (solicitação incorreta)** – a solicitação não é compreendida pelo servidor.
- **404 (não encontrado)** – o recurso solicitado não existe no servidor.

### <a name="deleting-data"></a>Excluindo dados

O `HttpClient.DeleteAsync` método é usado para enviar a solicitação de exclusão para o serviço web especificado pelo URI e, em seguida, receber a resposta do serviço da web, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  // RestUrl = http://developer.xamarin.com:8081/api/todoitems/{0}
  var uri = new Uri (string.Format (Constants.RestUrl, id));
  ...
  var response = await client.DeleteAsync (uri);
  if (response.IsSuccessStatusCode) {
    Debug.WriteLine (@"                TodoItem successfully deleted.");
  }
  ...
}
```

O serviço REST envia um código de status HTTP no `HttpResponseMessage.IsSuccessStatusCode` propriedade, para indicar se a solicitação HTTP foi bem-sucedida ou falhou. As respostas comuns para essa operação são:

- **204 (sem conteúdo)** – a solicitação foi processada com êxito e a resposta é intencionalmente em branco.
- **400 (solicitação incorreta)** – a solicitação não é compreendida pelo servidor.
- **404 (não encontrado)** – o recurso solicitado não existe no servidor.

## <a name="summary"></a>Resumo

Este artigo examinamos como consumir um serviço web RESTful de um aplicativo xamarin. Forms, usando o `HttpClient` classe. A simplicidade do REST ajudou a torná-lo o principal método para acessar os serviços da web em aplicativos móveis.


## <a name="related-links"></a>Links relacionados

- [Criando Serviços de Back-end para Aplicativos Móveis Nativos](/aspnet/core/mobile/native-mobile-backend/)
- [TodoREST (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoREST/)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
