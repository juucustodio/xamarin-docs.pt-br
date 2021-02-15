---
title: Consumir um serviço Web RESTful
description: A integração de um serviço Web em um aplicativo é um cenário comum. Este artigo demonstra como consumir um serviço Web RESTful de um Xamarin.Forms aplicativo.
ms.prod: xamarin
ms.assetid: B540910C-9C51-416A-AAB9-057BF76489C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/03/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a3fd59ecbaf85f24515deba8562060aadc6d2165
ms.sourcegitcommit: 10c7dd16fe78226053d1d036492b6c9102fc421b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99540955"
---
# <a name="consume-a-restful-web-service"></a>Consumir um serviço Web RESTful

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/webservices-todorest)

_A integração de um serviço Web em um aplicativo é um cenário comum. Este artigo demonstra como consumir um serviço Web RESTful de um Xamarin.Forms aplicativo._

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

Os serviços Web RESTful normalmente usam mensagens JSON para retornar dados ao cliente. JSON é um formato de intercâmbio de dados baseado em texto que produz cargas compactadas, o que resulta em requisitos de largura de banda reduzidos ao enviar dados. O aplicativo de exemplo usa a [biblioteca NewtonSoft JSON.net](https://www.newtonsoft.com/json) de código aberto para serializar e desserializar mensagens.

A simplicidade do REST ajudou a torná-lo o principal método para acessar os serviços da Web em aplicativos móveis.

Quando o aplicativo de exemplo for executado, ele se conectará a um serviço REST hospedado localmente, conforme mostrado na seguinte captura de tela:

![Aplicativo de exemplo](rest-images/portal.png)

> [!NOTE]
> No iOS 9 e superior, a ATS (segurança de transporte de aplicativo) impõe conexões seguras entre os recursos da Internet (como o servidor back-end do aplicativo) e o aplicativo, impedindo, assim, a divulgação acidental de informações confidenciais. Como o ATS está habilitado por padrão em aplicativos criados para iOS 9, todas as conexões estarão sujeitas a requisitos de segurança de ATS. Se as conexões não atenderem a esses requisitos, elas falharão com uma exceção.
>
>O ATS pode ser recusado se não for possível usar o protocolo **https** e a comunicação segura para recursos da Internet. Isso pode ser feito atualizando o arquivo **info. plist** do aplicativo. Para obter mais informações, consulte [segurança do transporte de aplicativo](~/ios/app-fundamentals/ats.md).

## <a name="consuming-the-web-service"></a>Consumindo o serviço Web

O serviço REST é escrito usando ASP.NET Core e fornece as seguintes operações:

|Operação|Método HTTP|URI relativo|Parâmetros|
|--- |--- |--- |--- |
|Obter uma lista de itens pendentes|GET|/api/todoitems/|
|Criar um novo item de tarefas pendentes|POST|/api/todoitems/|Um TodoItem formatado em JSON|
|Atualizar um item pendente|PUT|/api/todoitems/|Um TodoItem formatado em JSON|
|Excluir um item pendente|Delete (excluir)|/api/todoitems/{id}|

A maioria dos URIs inclui a `TodoItem` ID no caminho. Por exemplo, para excluir o `TodoItem` cuja ID é `6bb8a868-dba1-4f1a-93b7-24ebce87e243` , o cliente envia uma solicitação de exclusão para `http://hostname/api/todoitems/6bb8a868-dba1-4f1a-93b7-24ebce87e243` . Para obter mais informações sobre o modelo de dados usado no aplicativo de exemplo, consulte [modelando os dados](~/xamarin-forms/data-cloud/web-services/introduction.md).

Quando a estrutura da API Web recebe uma solicitação, ela roteia a solicitação para uma ação. Essas ações são simplesmente métodos públicos na `TodoItemsController` classe. A estrutura usa uma tabela de roteamento para determinar qual ação invocar em resposta a uma solicitação, que é mostrada no exemplo de código a seguir:

```csharp
config.Routes.MapHttpRoute(
    name: "TodoItemsApi",
    routeTemplate: "api/{controller}/{id}",
    defaults: new { controller="todoitems", id = RouteParameter.Optional }
);
```

A tabela de roteamento contém um modelo de rota e, quando a estrutura da API Web recebe uma solicitação HTTP, ele tenta corresponder o URI em relação ao modelo de rota na tabela de roteamento. Se não for possível encontrar uma rota correspondente, o cliente receberá um erro 404 (não encontrado). Se uma rota correspondente for encontrada, a API da Web selecionará o controlador e a ação da seguinte maneira:

- Para localizar o controlador, a API da Web adiciona "controlador" ao valor da variável *{Controller}* .
- Para localizar a ação, a API Web examina o método HTTP e examina as ações do controlador que são decoradas com o mesmo método HTTP que um atributo.
- A variável de espaço reservado *{ID}* é mapeada para um parâmetro de ação.

O serviço REST usa a autenticação básica. Para obter mais informações, consulte [Autenticando um serviço Web RESTful](~/xamarin-forms/data-cloud/authentication/rest.md). Para obter mais informações sobre roteamento de ASP.NET Web API, consulte [Roteamento em ASP.NET Web API](https://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) no site ASP.net. Para obter mais informações sobre como criar o serviço REST usando ASP.NET Core, consulte [criando serviços de back-end para aplicativos móveis nativos](/aspnet/core/mobile/native-mobile-backend/).

A `HttpClient` classe é usada para enviar e receber solicitações por http. Ele fornece a funcionalidade para enviar solicitações HTTP e receber respostas HTTP de um recurso de URI identificado. Cada solicitação é enviada como uma operação assíncrona. Para obter mais informações sobre operações assíncronas, consulte [visão geral do suporte assíncrono](~/cross-platform/platform/async.md).

A `HttpResponseMessage` classe representa uma mensagem de resposta http recebida do serviço Web depois que uma solicitação HTTP é feita. Ele contém informações sobre a resposta, incluindo o código de status, os cabeçalhos e qualquer corpo. A `HttpContent` classe representa o corpo de http e cabeçalhos de conteúdo, como `Content-Type` e `Content-Encoding` . O conteúdo pode ser lido usando qualquer um dos `ReadAs` métodos, como `ReadAsStringAsync` e `ReadAsByteArrayAsync` , dependendo do formato dos dados.

### <a name="creating-the-httpclient-object"></a>Criando o objeto HTTPClient

A `HttpClient` instância é declarada no nível de classe para que o objeto fique por enquanto o aplicativo precisar fazer solicitações HTTP, conforme mostrado no exemplo de código a seguir:

```csharp
public class RestService : IRestService
{
  HttpClient client;
  ...

  public RestService ()
  {
    client = new HttpClient ();
  }
  ...
}
```

### <a name="retrieving-data"></a>Recuperando dados

O `HttpClient.GetAsync` método é usado para enviar a solicitação get para o serviço Web especificado pelo URI e, em seguida, receber a resposta do serviço Web, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task<List<TodoItem>> RefreshDataAsync ()
{
  ...
  Uri uri = new Uri (string.Format (Constants.TodoItemsUrl, string.Empty));
  ...
  HttpResponseMessage response = await client.GetAsync (uri);
  if (response.IsSuccessStatusCode)
  {
      string content = await response.Content.ReadAsStringAsync ();
      Items = JsonConvert.DeserializeObject <List<TodoItem>> (content);
  }
  ...
}
```

O serviço REST envia um código de status HTTP na `HttpResponseMessage.IsSuccessStatusCode` propriedade, para indicar se a solicitação HTTP foi bem-sucedida ou falhou. Para essa operação, o serviço REST envia o código de status HTTP 200 (OK) na resposta, o que indica que a solicitação foi bem-sucedida e que as informações solicitadas estão na resposta.

Se a operação HTTP tiver sido bem-sucedida, o conteúdo da resposta será lido, para exibição. A `HttpResponseMessage.Content` propriedade representa o conteúdo da resposta http e o `HttpContent.ReadAsStringAsync` método grava o conteúdo http de forma assíncrona em uma cadeia de caracteres. Esse conteúdo é então convertido de JSON em um `List` de `TodoItem` instâncias.

> [!WARNING]
> Usar o `ReadAsStringAsync` método para recuperar uma grande resposta pode ter um impacto negativo no desempenho. Nessas circunstâncias, a resposta deve ser desserializada diretamente para evitar ter que armazenar totalmente o buffer.

### <a name="creating-data"></a>Criando dados

O `HttpClient.PostAsync` método é usado para enviar a solicitação post para o serviço Web especificado pelo URI e, em seguida, receber a resposta do serviço Web, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  Uri uri = new Uri (string.Format (Constants.TodoItemsUrl, string.Empty));

  ...
  string json = JsonConvert.SerializeObject (item);
  StringContent content = new StringContent (json, Encoding.UTF8, "application/json");

  HttpResponseMessage response = null;
  if (isNewItem)
  {
    response = await client.PostAsync (uri, content);
  }
  ...

  if (response.IsSuccessStatusCode)
  {
    Debug.WriteLine (@"\tTodoItem successfully saved.");
  }
  ...
}
```

A `TodoItem` instância é convertida em uma carga JSON para envio ao serviço Web. Essa carga é inserida no corpo do conteúdo HTTP que será enviado ao serviço Web antes que a solicitação seja feita com o `PostAsync` método.

O serviço REST envia um código de status HTTP na `HttpResponseMessage.IsSuccessStatusCode` propriedade, para indicar se a solicitação HTTP foi bem-sucedida ou falhou. As respostas comuns para essa operação são:

- **201 (criado)** – a solicitação resultou em um novo recurso que está sendo criado antes que a resposta fosse enviada.
- **400 (solicitação inválida)** – a solicitação não é compreendida pelo servidor.
- **409 (conflito)** – a solicitação não pôde ser executada devido a um conflito no servidor.

### <a name="updating-data"></a>Atualizando dados

O `HttpClient.PutAsync` método é usado para enviar a solicitação Put para o serviço Web especificado pelo URI e, em seguida, receber a resposta do serviço Web, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task SaveTodoItemAsync (TodoItem item, bool isNewItem = false)
{
  ...
  response = await client.PutAsync (uri, content);
  ...
}
```

A operação do `PutAsync` método é idêntica ao `PostAsync` método usado para criar dados no serviço Web. No entanto, as possíveis respostas enviadas do serviço Web são diferentes.

O serviço REST envia um código de status HTTP na `HttpResponseMessage.IsSuccessStatusCode` propriedade, para indicar se a solicitação HTTP foi bem-sucedida ou falhou. As respostas comuns para essa operação são:

- **204 (sem conteúdo)** – a solicitação foi processada com êxito e a resposta está intencionalmente em branco.
- **400 (solicitação inválida)** – a solicitação não é compreendida pelo servidor.
- **404 (não encontrado)** – o recurso solicitado não existe no servidor.

### <a name="deleting-data"></a>Excluindo dados

O `HttpClient.DeleteAsync` método é usado para enviar a solicitação de exclusão para o serviço Web especificado pelo URI e, em seguida, receber a resposta do serviço Web, conforme mostrado no exemplo de código a seguir:

```csharp
public async Task DeleteTodoItemAsync (string id)
{
  Uri uri = new Uri (string.Format (Constants.TodoItemsUrl, id));
  ...
  HttpResponseMessage response = await client.DeleteAsync (uri);
  if (response.IsSuccessStatusCode)
  {
    Debug.WriteLine (@"\tTodoItem successfully deleted.");
  }
  ...
}
```

O serviço REST envia um código de status HTTP na `HttpResponseMessage.IsSuccessStatusCode` propriedade, para indicar se a solicitação HTTP foi bem-sucedida ou falhou. As respostas comuns para essa operação são:

- **204 (sem conteúdo)** – a solicitação foi processada com êxito e a resposta está intencionalmente em branco.
- **400 (solicitação inválida)** – a solicitação não é compreendida pelo servidor.
- **404 (não encontrado)** – o recurso solicitado não existe no servidor.

### <a name="local-development"></a>Desenvolvimento local

Se você estiver desenvolvendo seu serviço Web REST localmente com uma estrutura como ASP.NET Core API Web, você pode depurar seu serviço Web e aplicativo móvel ao mesmo tempo. Nesse cenário, você deve habilitar o tráfego HTTP de texto não criptografado para o iOS Simualtor e o Android Emulator. Para obter informações sobre como configurar seu projeto para permitir a comunicação, consulte [conectar-se a serviços Web locais](~/cross-platform/deploy-test/connect-to-local-web-services.md).

## <a name="related-links"></a>Links Relacionados

- [Microsoft Learn: consumir serviços Web REST em aplicativos Xamarin](/learn/modules/consume-rest-services/)
- [Microsoft Learn: criar uma API Web com ASP.NET Core](/learn/modules/build-web-api-aspnet-core/)
- [Criando serviços de back-end para aplicativos móveis nativos](/aspnet/core/mobile/native-mobile-backend/)
- [TodoREST (amostra)](/samples/xamarin/xamarin-forms-samples/webservices-todorest)
- [API HttpClient](xref:System.Net.Http.HttpClient)
- [Configuração de segurança de rede do Android](https://devblogs.microsoft.com/xamarin/cleartext-http-android-network-security/)
- [Segurança de transporte de aplicativo iOS](~/ios/app-fundamentals/ats.md)
- [Conectar-se aos serviços Web locais](~/cross-platform/deploy-test/connect-to-local-web-services.md)
