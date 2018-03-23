---
title: Autenticação de usuários com um banco de dados de documento de banco de dados do Cosmos do Azure
description: Bancos de dados de documento Cosmos banco de dados do Azure oferecem suporte a coleções particionadas, o que podem abranger vários servidores e partições, e dar suporte a taxa de transferência e armazenamento ilimitado. Este artigo explica como combinar o controle de acesso com coleções particionadas, para que um usuário pode acessar somente seus próprios documentos em um aplicativo xamarin. Forms.
ms.topic: article
ms.prod: xamarin
ms.assetid: 11ED4A4C-0F05-40B2-AB06-5A0F2188EF3D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 8de64d6489b4022e43bcf694f3b13d6f7eaaecbd
ms.sourcegitcommit: 7b76c3d761b3ffb49541e2e2bcf292de6587c4e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="authenticating-users-with-an-azure-cosmos-db-document-database"></a>Autenticação de usuários com um banco de dados de documento de banco de dados do Cosmos do Azure

_Bancos de dados de documento Cosmos banco de dados do Azure oferecem suporte a coleções particionadas, o que podem abranger vários servidores e partições, e dar suporte a taxa de transferência e armazenamento ilimitado. Este artigo explica como combinar o controle de acesso com coleções particionadas, para que um usuário pode acessar somente seus próprios documentos em um aplicativo xamarin. Forms._

## <a name="overview"></a>Visão geral

Uma chave de partição deve ser especificada ao criar uma coleção particionada e documentos com a mesma chave de partição serão armazenados na mesma partição. Portanto, especificar a identidade do usuário como uma chave de partição resultará em uma coleção particionada que armazenará os documentos para esse usuário. Isso também garante que o banco de dados de documento de banco de dados do Azure Cosmos dimensionará como o número de usuários e itens aumentam.

Acesso deve ser concedido a nenhuma coleção e o modelo de controle de acesso de API de SQL define dois tipos de construções de acesso:

- **Chaves mestras** habilitar acesso administrativo completo a todos os recursos dentro de uma conta de banco de dados do Cosmos e são criadas quando uma conta de banco de dados do Cosmos é criada.
- **Tokens de recurso** capturam a relação entre o usuário de um banco de dados e a permissão que o usuário tem para um recurso de banco de dados do Cosmos específico, como uma coleção ou um documento.

Expor uma chave mestra, uma conta de banco de dados do Cosmos sobre a possibilidade de uso mal-intencionado ou negligente é aberto. No entanto, os tokens de recurso de banco de dados do Azure Cosmos fornecem um mecanismo seguro para permitir que os clientes ler, gravar e excluir recursos específicos em uma conta de banco de dados do Azure Cosmos acordo com as permissões concedidas.

Uma abordagem comum para solicitar, gerar e fornecer tokens de recurso para um aplicativo móvel é usar um agente de token de recurso. O diagrama a seguir mostra uma visão geral de como o aplicativo de exemplo usa um agente de token de recursos para gerenciar o acesso aos dados do banco de dados de documento:

![](authentication-images/documentdb-authentication.png "Processo de autenticação de banco de dados de documento")

O agente de token de recurso é um serviço de API da Web da camada intermediária, hospedado no serviço de aplicativo do Azure, que possui a chave mestra da conta do banco de dados do Cosmos. O aplicativo de exemplo usa o agente de token de recursos para gerenciar o acesso aos dados do banco de dados de documento da seguinte maneira:

1. Em logon, o aplicativo xamarin. Forms contata o serviço de aplicativo do Azure para iniciar um fluxo de autenticação.
1. Serviço de aplicativo do Azure executa um fluxo de autenticação OAuth com o Facebook. Depois que o fluxo de autenticação é concluída, o aplicativo xamarin. Forms recebe um token de acesso.
1. O aplicativo xamarin. Forms usa o token de acesso para solicitar um token de recurso do agente de token de recurso.
1. O agente de token de recurso usa o token de acesso para solicitar a identidade do usuário do Facebook. A identidade do usuário, em seguida, é usada para solicitar um token de recurso do banco de dados do Cosmos, que é usado para conceder acesso de leitura/gravação a coleção particionada do usuário autenticado.
1. O aplicativo xamarin. Forms usa o token de recurso para acessar diretamente os recursos de banco de dados do Cosmos com as permissões definidas pelo token de recurso.

> [!NOTE]
> Quando o token de recurso expira, solicitações de banco de dados de documento subsequentes receberá uma exceção de 401 não autorizado. Neste ponto, xamarin. Forms aplicativos devem restabelecer a identidade e solicite um novo token de recurso.

Para obter mais informações sobre o particionamento do banco de dados do Cosmos, consulte [como partição e escala no banco de dados do Azure Cosmos](/azure/cosmos-db/partition-data/). Para obter mais informações sobre o controle de acesso de banco de dados do Cosmos, consulte [proteger o acesso aos dados do banco de dados do Cosmos](/azure/cosmos-db/secure-access-to-data/) e [controle de acesso na API do SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).

## <a name="setup"></a>Configuração

O processo para integrar o agente de token de recurso em um aplicativo xamarin. Forms é da seguinte maneira:

1. Crie uma conta de banco de dados do Cosmos que usarão o controle de acesso. Para obter mais informações, consulte [Cosmos DB configuração](#cosmosdb_configuration).
1. Crie um serviço de aplicativo do Azure para hospedar o agente de token de recurso. Para obter mais informações, consulte [configuração de serviço de aplicativo do Azure](#app_service_configuration).
1. Crie um aplicativo do Facebook para realizar a autenticação. Para obter mais informações, consulte [Facebook App Configuration](#facebook_configuration).
1. Configure o serviço de aplicativo do Azure para realizar a autenticação fácil com o Facebook. Para obter mais informações, consulte [configuração de autenticação do serviço de aplicativo do Azure](#app_service_authentication_configuration).
1. Configure o aplicativo de exemplo xamarin. Forms para se comunicar com o serviço de aplicativo do Azure e banco de dados do Cosmos. Para obter mais informações, consulte [configuração de aplicativo xamarin. Forms](#forms_application_configuration).

<a name="cosmosdb_configuration" />

### <a name="azure-cosmos-db-configuration"></a>Configuração de banco de dados do Cosmos do Azure

O processo para criar uma conta de banco de dados do Cosmos que usarão o controle de acesso é da seguinte maneira:

1. Crie uma conta de banco de dados do Cosmos. Para obter mais informações, consulte [criar uma conta de banco de dados do Azure Cosmos](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account).
1. A conta de banco de dados do Cosmos, cria uma nova coleção chamada `UserItems`, especificando uma chave de partição de `/userid`.

<a name="app_service_configuration" />

### <a name="azure-app-service-configuration"></a>Configuração do serviço de aplicativo do Azure

O processo para hospedar o agente de token de recurso no serviço de aplicativo do Azure é o seguinte:

1. No portal do Azure, crie um novo aplicativo web do serviço de aplicativo. Para obter mais informações, consulte [criar um aplicativo web em um ambiente de serviço de aplicativo](/azure/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase/).
1. No portal do Azure, abra a folha de configurações do aplicativo para o aplicativo web e adicione as seguintes configurações:
    - `accountUrl` – o valor deve ser a URL de conta do banco de dados do Cosmos na folha de chaves da conta do banco de dados do Cosmos.
    - `accountKey` – o valor deve ser a chave mestra de banco de dados do Cosmos (primária ou secundária) na folha de chaves da conta do banco de dados do Cosmos.
    - `databaseId` – o valor deve ser o nome do banco de dados do banco de dados do Cosmos.
    - `collectionId` – o valor deve ser o nome da coleção de banco de dados do Cosmos (nesse caso, `UserItems`).
    - `hostUrl` – o valor deve ser a URL do aplicativo web da folha de visão geral da conta de serviço de aplicativo.

    Captura de tela a seguir demonstra essa configuração:

    [![](authentication-images/azure-web-app-settings.png "Configurações do aplicativo do serviço de aplicativo Web")](authentication-images/azure-web-app-settings-large.png#lightbox "configurações de aplicativo do serviço de aplicativo Web")

1. Publica a solução de agente de token de recurso para o aplicativo da web do serviço de aplicativo do Azure.

<a name="facebook_configuration" />

### <a name="facebook-app-configuration"></a>Configuração de aplicativo do Facebook

O processo de criação de um aplicativo do Facebook para realizar a autenticação é o seguinte:

1. Crie um aplicativo do Facebook. Para obter mais informações, consulte [registrar e configurar um aplicativo](https://developers.facebook.com/docs/apps/register) no Centro de desenvolvedores do Facebook.
1. Adicione o produto de logon do Facebook para o aplicativo. Para obter mais informações, consulte [adicionar logon do Facebook para seu aplicativo ou site](https://developers.facebook.com/docs/facebook-login) no Centro de desenvolvedores do Facebook.
1. Configure o logon do Facebook como segue:
  - Habilite logon do cliente OAuth.
  - Habilite logon do Web OAuth.
  - Definir o URI para o URI do aplicativo do serviço de aplicativo web, de redirecionamento OAuth válido com `/.auth/login/facebook/callback` anexado.

  Captura de tela a seguir demonstra essa configuração:

  ![](authentication-images/facebook-oauth-settings.png "Configurações de OAuth do logon do Facebook")

Para obter mais informações, consulte [registrar seu aplicativo com o Facebook](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-nameregister-aregister-your-application-with-facebook).

<a name="app_service_authentication_configuration" />

### <a name="azure-app-service-authentication-configuration"></a>Configuração de autenticação do serviço de aplicativo do Azure

O processo de configuração fácil de autenticação do serviço de aplicativo é o seguinte:

1. No Portal do Azure, navegue até o aplicativo do serviço de aplicativo web.
1. No Portal do Azure, abra a autenticação / folha de autorização e execute a seguinte configuração:
  - Autenticação do serviço de aplicativo deve ser ativada.
  - A ação a ser tomada quando uma solicitação for autenticada não deve ser definida como **logon com o Facebook**.

  Captura de tela a seguir demonstra essa configuração:

  [![](authentication-images/app-service-authentication-settings.png "Configurações de autenticação do aplicativo do serviço de aplicativo Web")](authentication-images/app-service-authentication-settings-large.png#lightbox "configurações de autenticação do aplicativo do serviço de aplicativo Web")

O aplicativo do serviço de aplicativo web também deve ser configurado para se comunicar com o aplicativo do Facebook para habilitar o fluxo de autenticação. Isso pode ser feito selecionando o provedor de identidade do Facebook e inserindo o **ID do aplicativo** e **segredo do aplicativo** valores de configurações do aplicativo Facebook no Centro de desenvolvedores do Facebook. Para obter mais informações, consulte [Facebook adicionar informações ao seu aplicativo](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-namesecrets-aadd-facebook-information-to-your-application).

<a name="forms_application_configuration" />

### <a name="xamarinforms-application-configuration"></a>Configuração de aplicativo xamarin. Forms

O processo para configurar o aplicativo de exemplo xamarin. Forms é da seguinte maneira:

1. Abra a solução xamarin. Forms.
1. Abra `Constants.cs` e atualize os valores das seguintes constantes:
  - `EndpointUri` – o valor deve ser a URL de conta do banco de dados do Cosmos na folha de chaves da conta do banco de dados do Cosmos.
  - `DatabaseName` – o valor deve ser o nome do banco de dados de documento.
  - `CollectionName` – o valor deve ser o nome da coleção de banco de dados de documento (nesse caso, `UserItems`).
  - `ResourceTokenBrokerUrl` – o valor deve ser a URL do aplicativo recurso broker token da web da folha de visão geral da conta de serviço de aplicativo.

## <a name="initiating-login"></a>Logon inicial

O aplicativo de exemplo inicia o processo de logon usando Xamarin.Auth para redirecionar um navegador para uma URL do provedor de identidade, conforme demonstrado no código de exemplo a seguir:

```csharp
var auth = new Xamarin.Auth.WebRedirectAuthenticator(
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/facebook"),
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/done"));
```

Isso faz com que um fluxo de autenticação OAuth ser iniciada entre o serviço de aplicativo do Azure e o Facebook, que exibe a página de logon do Facebook:

![](authentication-images/login.png "Logon do Facebook")

O logon pode ser cancelado, pressionando a **Cancelar** botão no iOS ou pressionando o **novamente** botão no Android, caso em que o usuário permanece não autenticado e a interface de usuário do provedor de identidade é removido da tela.

Para obter mais informações sobre Xamarin.Auth, consulte [autenticando usuários com um provedor de identidade](~/xamarin-forms/data-cloud/authentication/oauth.md).

## <a name="obtaining-a-resource-token"></a>Como obter um Token de recurso

Após a autenticação bem-sucedida, o `WebRedirectAuthenticator.Completed` evento ser acionado. O exemplo de código a seguir demonstra o tratamento desse evento:

```csharp
auth.Completed += async (sender, e) =>
{
  if (e.IsAuthenticated && e.Account.Properties.ContainsKey("token"))
  {
    var easyAuthResponseJson = JsonConvert.DeserializeObject<JObject>(e.Account.Properties["token"]);
    var easyAuthToken = easyAuthResponseJson.GetValue("authenticationToken").ToString();

    // Call the ResourceBroker to get the resource token
    using (var httpClient = new HttpClient())
    {
      httpClient.DefaultRequestHeaders.Add("x-zumo-auth", easyAuthToken);
      var response = await httpClient.GetAsync(Constants.ResourceTokenBrokerUrl + "/api/resourcetoken/");
      var jsonString = await response.Content.ReadAsStringAsync();
      var tokenJson = JsonConvert.DeserializeObject<JObject>(jsonString);
      resourceToken = tokenJson.GetValue("token").ToString();
      UserId = tokenJson.GetValue("userid").ToString();

      if (!string.IsNullOrWhiteSpace(resourceToken))
      {
        client = new DocumentClient(new Uri(Constants.EndpointUri), resourceToken);
        ...
      }
      ...
    }
  }
};
```

O resultado de uma autenticação bem-sucedida é um token de acesso, que está disponível `AuthenticatorCompletedEventArgs.Account` propriedade. O token de acesso é extraído e usado em uma solicitação GET para do recurso token broker `resourcetoken` API.

O `resourcetoken` API usa o token de acesso para solicitar a identidade do usuário do Facebook, que por sua vez, é usado para solicitar um token de recurso do banco de dados do Cosmos. Se já existe um documento válido de permissão para o usuário no banco de dados de documento, ele será recuperado e um documento JSON que contém o token de recurso é retornado para o aplicativo xamarin. Forms. Se não existir um documento válido de permissão do usuário, um usuário e a permissão é criada no banco de dados de documento e o token de recurso é extraído do documento de permissão e retornado para o aplicativo xamarin. Forms em um documento JSON.

> [!NOTE]
> Um usuário de banco de dados de documento é um recurso associado a um banco de dados de documento, e cada banco de dados pode conter zero ou mais usuários. Uma permissão de banco de dados de documento é um recurso associado a um usuário de banco de dados de documento, e cada usuário pode conter zero ou mais permissões. Um recurso de permissão fornece acesso a um token de segurança requer que o usuário durante a tentativa de acessar um recurso, como um documento.

Se o `resourcetoken` API é concluída com êxito, ele enviará o código de status HTTP 200 (Okey) na resposta, juntamente com um documento JSON que contém o token de recurso. Os dados JSON a seguir mostram uma mensagem de resposta bem-sucedida típico:

```csharp
{
  "id": "John Smithpermission",
  "token": "type=resource&ver=1&sig=zx6k2zzxqktzvuzuku4b7y==;a74aukk99qtwk8v5rxfrfz7ay7zzqfkbfkremrwtaapvavw2mrvia4umbi/7iiwkrrq+buqqrzkaq4pp15y6bki1u//zf7p9x/aefbvqvq3tjjqiffurfx+vexa1xarxkkv9rbua9ypfzr47xpp5vmxuvzbekkwq6txme0xxxbjhzaxbkvzaji+iru3xqjp05amvq1r1q2k+qrarurhmjzah/ha0evixazkve2xk1zu9u/jpyf1xrwbkxqpzebvqwma+hyyaazemr6qx9uz9be==;",
  "expires": 4035948,
  "userid": "John Smith"
}
```

O `WebRedirectAuthenticator.Completed` a resposta de leituras de manipulador de eventos de `resourcetoken` API e extrai o token de recurso e a id de usuário. O token de recurso é então passado como um argumento para o `DocumentClient` construtor, que encapsula o ponto de extremidade, credenciais e política de conexão usada para acessar o banco de dados do Cosmos e é usado para configurar e executar solicitações no banco de dados do Cosmos. O token de recurso é enviado com cada solicitação para acessar diretamente um recurso e indica que o acesso de leitura/gravação à coleção particionada os usuários autenticados é concedido.

## <a name="retrieving-documents"></a>Recuperação de documentos

Recuperação de documentos que pertencem somente para o usuário autenticado pode ser obtida por meio da criação de uma consulta de documento que inclui a id do usuário como uma chave de partição e é demonstrada no exemplo de código a seguir:

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink,
                        new FeedOptions
                        {
                          MaxItemCount = -1,
                          PartitionKey = new PartitionKey(UserId)
                        })
          .Where(item => !item.Id.Contains("permission"))
          .AsDocumentQuery();
while (query.HasMoreResults)
{
  Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
}
```

A consulta recupera todos os documentos pertencentes ao usuário autenticado, da coleção especificada e de forma assíncrona coloca em um `List<TodoItem>` coleção para exibição.

O `CreateDocumentQuery<T>` método Especifica um `Uri` argumento que representa a coleção que deve ser consultada para documentos, e um `FeedOptions` objeto. O `FeedOptions` objeto Especifica que um número ilimitado de itens pode ser retornado pela consulta e a id do usuário como uma chave de partição. Isso garante que somente os documentos na coleção particionada do usuário são retornados no resultado.

> [!NOTE]
> Observe que os documentos de permissão, que são criados pelo agente de token de recurso, são armazenados no mesmo conjunto de documento como os documentos criados pelo aplicativo xamarin. Forms. Portanto, a consulta de documento contém um `Where` cláusula que aplica um predicado de filtragem para a consulta em relação à coleção de documentos. Essa cláusula garante que os documentos de permissão não são retornados da coleção de documentos.

Para obter mais informações sobre como recuperar os documentos de um conjunto de documentos, consulte [recuperar documentos de coleção](~/xamarin-forms/data-cloud/cosmosdb/consuming.md#document_query).

## <a name="inserting-documents"></a>Inserção de documentos

Antes de inserir um documento em uma coleção de documentos, o `TodoItem.UserId` propriedade deve ser atualizada com o valor que está sendo usado como a chave de partição, conforme demonstrado no exemplo de código a seguir:

```csharp
item.UserId = UserId;
await client.CreateDocumentAsync(collectionLink, item);
```

Isso garante que o documento será inserido na coleção particionada do usuário.

Para obter mais informações sobre como inserir um documento em uma coleção de documentos, consulte [inserindo um documento em uma coleção de documentos](~/xamarin-forms/data-cloud/cosmosdb/consuming.md#inserting_document).

## <a name="deleting-documents"></a>Excluir documentos

O valor de chave de partição deve ser especificado quando a exclusão de um documento de uma coleção particionada, como demonstrado no exemplo de código a seguir:

```csharp
await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id),
                 new RequestOptions
                 {
                   PartitionKey = new PartitionKey(UserId)
                 });
```

Isso garante que Cosmos DB sabe qual particionada coleção ao excluir o documento do.

Para obter mais informações sobre como excluir um documento de um conjunto de documentos, consulte [exclusão de um documento de um conjunto de documentos](~/xamarin-forms/data-cloud/cosmosdb/consuming.md#deleting_document).

## <a name="summary"></a>Resumo

Este artigo explicou como combinar o controle de acesso com coleções particionadas, para que um usuário pode acessar somente seus próprios documentos de banco de dados de documento em um aplicativo xamarin. Forms. Especificando a identidade do usuário como uma chave de partição garante que uma coleção particionada só pode armazenar documentos para esse usuário.


## <a name="related-links"></a>Links relacionados

- [Tarefas do Azure Cosmos DB Auth (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoDocumentDBAuth/)
- [Consumo de um banco de dados de documento do Azure Cosmos DB](~/xamarin-forms/data-cloud/cosmosdb/consuming.md)
- [Protegendo o acesso a dados do banco de dados do Azure Cosmos](/azure/cosmos-db/secure-access-to-data/)
- [Controle de acesso na API do SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).
- [A partição e escala no banco de dados do Azure Cosmos](/azure/cosmos-db/partition-data/)
- [Biblioteca de cliente do banco de dados do Cosmos do Azure](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API do Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn948556.aspx)
