---
title: Consumir um serviço Web RESTful
description: Integrar um serviço web em um aplicativo é um cenário comum. Este artigo demonstra como consumir um serviço web RESTful em um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: B540910C-9C51-416A-AAB9-057BF76489C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/22/2018
ms.openlocfilehash: cb569a425bf636a51dd6d132f6efa539e74443a0
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644573"
---
# <a name="consume-a-restful-web-service"></a>Consumir um serviço Web RESTful

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_Integrar um serviço web em um aplicativo é um cenário comum. Este artigo demonstra como consumir um serviço web RESTful em um aplicativo xamarin. Forms._

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

Serviços web rESTful normalmente usam mensagens JSON para retornar dados ao cliente. JSON é um formato de troca de dados baseado em texto que gera conteúdos de compact, que resulta em reduzida requisitos de largura de banda ao enviar dados. O aplicativo de exemplo usa o software livre [biblioteca NewtonSoft JSON.NET](http://www.newtonsoft.com/json) para serializar e desserializar mensagens.

A simplicidade do REST ajudou a torná-lo o principal método para acessar os serviços da web em aplicativos móveis.

Quando o aplicativo de exemplo for executado, ele se conectará a um serviço REST hospedado localmente, conforme mostrado na seguinte captura de tela:

![](rest-images/portal.png "Aplicativo de exemplo")

> [!NOTE]
> No iOS 9 e superior, a segurança de transporte de aplicativo (ATS) impõe conexões seguras entre recursos da internet (como o servidor de back-end do aplicativo) e o aplicativo, impedindo assim a divulgação acidental de informações confidenciais. Desde que o ATS é habilitado por padrão em aplicativos criados para o iOS 9, todas as conexões serão sujeitos a requisitos de segurança ATS. Se as conexões não atender a esses requisitos, eles falharão com uma exceção.
>
>ATS poderá ser aceito de se ele não é possível usar o **HTTPS** de protocolo e proteger a comunicação para recursos da internet. Isso pode ser feito atualizando o aplicativo **Info. plist** arquivo. Para obter mais informações, consulte [segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Consumir o serviço Web

O serviço REST é gravado usando o ASP.NET Core e fornece as seguintes operações:

|Operação|Método HTTP|URI relativo|Parâmetros|
|--- |--- |--- |--- |
|Obter uma lista de itens pendentes|OBTER|/ API/todoitems /|
|Criar um novo item pendente|POSTAR|/ API/todoitems /|Um JSON formatado TodoItem|
|Atualizar um item pendente|PUT|/ API/todoitems /|Um JSON formatado TodoItem|
|Excluir um item pendente|DELETE|/api/todoitems/{id}|

A maioria dos URIs inclui o `TodoItem` ID no caminho. Por exemplo, para excluir o `TodoItem` cuja ID é `6bb8a868-dba1-4f1a-93b7-24ebce87e243`, o cliente envia uma solicitação de exclusão `http://hostname/api/todoitems/6bb8a868-dba1-4f1a-93b7-24ebce87e243`. Para obter mais informações sobre o modelo de dados usado no aplicativo de exemplo, consulte [modelagem de dados](~/xamarin-forms/data-cloud/web-services/introduction.md).

Quando a estrutura da API Web recebe uma solicitação, ele encaminha a solicitação para uma ação. Essas ações são simplesmente público em métodos de `TodoItemsController` classe. A estrutura usa uma tabela de roteamento para determinar qual ação a ser invocada em resposta a uma solicitação, que é mostrada no exemplo de código a seguir:

```csharp
config.Routes.MapHttpRoute(
    name: "TodoItemsApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { controller="todoitems", id = RouteParameter.Optional }
);
```

A tabela de roteamento contém um modelo de rota e quando a estrutura da API Web recebe uma solicitação HTTP, ele tenta corresponder o URI em relação ao modelo de rota na tabela de roteamento. Se uma correspondência de rota não foi encontrada que o cliente recebe um erro de 404 (não encontrado). Se uma rota correspondente for encontrada, API da Web selecionará o controlador e a ação da seguinte maneira:

- Para localizar o controlador, API da Web adiciona "controller" como o valor da *{controlador}* variável.
- Para localizar a ação, a API Web examina o método HTTP e examina a ações do controlador são decoradas com o mesmo método HTTP como um atributo.
- O *{id}* variável de espaço reservado é mapeado para um parâmetro de ação.

O serviço REST usa autenticação básica. Para obter mais informações, consulte [autenticar um serviço web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md). Para obter mais informações sobre o roteamento do ASP.NET Web API, consulte [roteamento na API Web ASP.NET](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) no site do ASP.NET. Para obter mais informações sobre como criar o serviço REST usando o ASP.NET Core, consulte [criação de serviços de back-end para aplicativos móveis nativos](/aspnet/core/mobile/native-mobile-backend/).

O `HttpClient` classe é usada para enviar e receber solicitações via HTTP. Ele fornece funcionalidade para enviar solicitações HTTP e receber respostas HTTP de um URI identificou o recurso. Cada solicitação é enviada como uma operação assíncrona. Para obter mais informações sobre as operações assíncronas, consulte [visão geral do suporte assíncrono](~/cross-platform/platform/async.md).

O `HttpResponseMessage` classe representa uma mensagem de resposta HTTP recebida do serviço da web depois que foi feita uma solicitação HTTP. Ele contém informações sobre a resposta, incluindo o código de status, cabeçalhos e qualquer corpo. O `HttpContent` classe representa o corpo de HTTP e cabeçalhos de conteúdo, como `Content-Type` e `Content-Encoding`. O conteúdo pode ser lido usando qualquer um dos `ReadAs` métodos, tais como `ReadAsStringAsync` e `ReadAsByteArrayAsync`, dependendo do formato dos dados.

### <a name="creating-the-httpclient-object"></a>Criando o objeto HTTPClient

O `HttpClient` instância é declarada no nível de classe, de modo que o objeto reside para desde que o aplicativo precisa fazer solicitações HTTP, conforme mostrado no exemplo de código a seguir:

```csharp
public class RestService : IRestService
{
  HttpClient _client;
  ...

  public RestService ()
  {
    _client = new HttpClient ();
  }
  ...
}
```

### <a name="retrieving-data"></a>Recuperando dados

O `HttpClient.GetAsync` método é usado para enviar a solicitação GET para o serviço web especificado pelo URI e, em seguida, receber a resposta do serviço da web, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  var uri = new Uri (string.Format (Constants.TodoItemsUrl, string.Empty));
  ...
  var response = await _client.GetAsync (uri);
  if (response.IsSuccessStatusCode)
  {
      var content = await response.Content.ReadAsStringAsync ();
      Items = JsonConvert.DeserializeObject <List<TodoItem>> (content);
  }
  ...
}
```

O serviço REST envia um código de status HTTP no `HttpResponseMessage.IsSuccessStatusCode` propriedade para indicar se a solicitação HTTP foi bem-sucedida ou falhou. Para esta operação restante serviço envia o código de status HTTP 200 (Okey) na resposta, que indica se a solicitação foi bem-sucedida e que as informações solicitadas estão na resposta.

Se a operação HTTP foi bem-sucedida, o conteúdo da resposta é lido, para exibição. O `HttpResponseMessage.Content` propriedade representa o conteúdo da resposta HTTP e o `HttpContent.ReadAsStringAsync` método grava de forma assíncrona o conteúdo HTTP em uma cadeia de caracteres. Este conteúdo é então convertido de JSON para um `List` de `TodoItem` instâncias.

### <a name="creating-data"></a>Criação de dados

O `HttpClient.PostAsync` método é usado para enviar a solicitação POST para o serviço web especificado pelo URI e, em seguida, para receber a resposta do serviço da web, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  var uri = new Uri (string.Format (Constants.TodoItemsUrl, string.Empty));

  ...
  var json = JsonConvert.SerializeObject (item);
  var content = new StringContent (json, Encoding.UTF8, "application/json");

  HttpResponseMessage response = null;
  if (isNewItem)
  {
    response = await _client.PostAsync (uri, content);
  }
  ...

  if (response.IsSuccessStatusCode)
  {
    Debug.WriteLine (@"\tTodoItem successfully saved.");

  }
  ...
}
```

O `TodoItem` instância é convertida em uma carga JSON para enviar para o serviço web. Essa carga, em seguida, é inserida no corpo do conteúdo HTTP que será enviado ao serviço web antes que a solicitação é feita com o `PostAsync` método.

O serviço REST envia um código de status HTTP no `HttpResponseMessage.IsSuccessStatusCode` propriedade para indicar se a solicitação HTTP foi bem-sucedida ou falhou. As respostas comuns para essa operação são:

- **201 (criado)** – a solicitação resultou em um novo recurso que está sendo criado antes que a resposta foi enviada.
- **400 (solicitação incorreta)** – a solicitação não é entendida pelo servidor.
- **409 (conflito)** – a solicitação não pôde ser realizada devido a um conflito no servidor.

### <a name="updating-data"></a>Atualizando dados

O `HttpClient.PutAsync` método é usado para enviar a solicitação PUT para o serviço web especificado pelo URI e, em seguida, receber a resposta do serviço da web, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  response = await _client.PutAsync (uri, content);
  ...
}
```
A operação do `PutAsync` método é idêntico de `PostAsync` método que é usado para a criação de dados no serviço web. No entanto, as respostas possíveis enviadas do serviço web são diferentes.

O serviço REST envia um código de status HTTP no `HttpResponseMessage.IsSuccessStatusCode` propriedade para indicar se a solicitação HTTP foi bem-sucedida ou falhou. As respostas comuns para essa operação são:

- **204 (sem conteúdo)** – a solicitação foi processada com êxito e a resposta está intencionalmente em branco.
- **400 (solicitação incorreta)** – a solicitação não é entendida pelo servidor.
- **404 (não encontrado)** – o recurso solicitado não existe no servidor.

### <a name="deleting-data"></a>Excluindo dados

O `HttpClient.DeleteAsync` método é usado para enviar a solicitação de exclusão para o serviço web especificado pelo URI e, em seguida, receber a resposta do serviço da web, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  var uri = new Uri (string.Format (Constants.TodoItemsUrl, id));
  ...
  var response = await _client.DeleteAsync (uri);
  if (response.IsSuccessStatusCode)
  {
    Debug.WriteLine (@"\tTodoItem successfully deleted.");
  }
  ...
}
```

O serviço REST envia um código de status HTTP no `HttpResponseMessage.IsSuccessStatusCode` propriedade para indicar se a solicitação HTTP foi bem-sucedida ou falhou. As respostas comuns para essa operação são:

- **204 (sem conteúdo)** – a solicitação foi processada com êxito e a resposta está intencionalmente em branco.
- **400 (solicitação incorreta)** – a solicitação não é entendida pelo servidor.
- **404 (não encontrado)** – o recurso solicitado não existe no servidor.

## <a name="related-links"></a>Links relacionados

- [Criando Serviços de Back-end para Aplicativos Móveis Nativos](/aspnet/core/mobile/native-mobile-backend/)
- [TodoREST (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todorest)
- [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)
