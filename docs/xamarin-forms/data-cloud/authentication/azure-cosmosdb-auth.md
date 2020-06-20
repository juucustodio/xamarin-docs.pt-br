---
title: Autenticar usuários com um banco de dados de documento Azure Cosmos DB eXamarin.Forms
description: Este artigo explica como combinar o controle de acesso com Azure Cosmos DB coleções particionadas, para que um usuário possa acessar somente seus próprios documentos em um Xamarin.Forms aplicativo.
ms.prod: xamarin
ms.assetid: 11ED4A4C-0F05-40B2-AB06-5A0F2188EF3D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7e1fb6342db4c97ea6946db366d356267c8c1b90
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84570551"
---
# <a name="authenticate-users-with-an-azure-cosmos-db-document-database-and-xamarinforms"></a>Autenticar usuários com um banco de dados de documento Azure Cosmos DB eXamarin.Forms

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)

_Azure Cosmos DB bancos de dados de documentos dão suporte a coleções particionadas, que podem abranger vários servidores e partições, ao mesmo tempo que dão suporte a armazenamento e taxa de transferência ilimitadas. Este artigo explica como combinar o controle de acesso com coleções particionadas, para que um usuário possa acessar somente seus próprios documentos em um Xamarin.Forms aplicativo._

## <a name="overview"></a>Visão geral

Uma chave de partição deve ser especificada ao criar uma coleção particionada, e os documentos com a mesma chave de partição serão armazenados na mesma partição. Portanto, especificar a identidade do usuário como uma chave de partição resultará em uma coleção particionada que armazenará apenas documentos para esse usuário. Isso também garante que o banco de dados de documentos Azure Cosmos DB será dimensionado conforme o número de usuários e itens aumentar.

O acesso deve ser concedido a qualquer coleção, e o modelo de controle de acesso à API do SQL define dois tipos de construções de acesso:

- **As chaves mestras** permitem acesso administrativo total a todos os recursos dentro de uma conta de Cosmos DB e são criadas quando uma conta de Cosmos DB é criada.
- **Tokens de recurso** capturam a relação entre o usuário de um banco de dados e a permissão que o usuário tem para um recurso de Cosmos DB específico, como uma coleção ou um documento.

Expor uma chave mestra abre uma conta de Cosmos DB para a possibilidade de uso mal-intencionado ou negligente. No entanto, Azure Cosmos DB tokens de recurso fornecem um mecanismo seguro para permitir que os clientes leiam, gravem e excluam recursos específicos em uma conta de Azure Cosmos DB de acordo com as permissões concedidas.

Uma abordagem típica para solicitar, gerar e entregar tokens de recurso a um aplicativo móvel é usar um agente de token de recurso. O diagrama a seguir mostra uma visão geral de alto nível de como o aplicativo de exemplo usa um agente de token de recurso para gerenciar o acesso aos dados do banco de dado do documento:

![](azure-cosmosdb-auth-images/documentdb-authentication.png "Document Database Authentication Process")

O agente de token de recurso é um serviço de API da Web de camada intermediária, hospedado em Azure App serviço, que possui a chave mestra da conta de Cosmos DB. O aplicativo de exemplo usa o agente de token de recurso para gerenciar o acesso ao banco de dados do documento, da seguinte maneira:

1. No logon, o Xamarin.Forms aplicativo entra em contato Azure app serviço para iniciar um fluxo de autenticação.
1. Azure App serviço executa um fluxo de autenticação OAuth com o Facebook. Após a conclusão do fluxo de autenticação, o Xamarin.Forms aplicativo recebe um token de acesso.
1. O Xamarin.Forms aplicativo usa o token de acesso para solicitar um token de recurso do agente de token de recurso.
1. O agente de token de recurso usa o token de acesso para solicitar a identidade do usuário do Facebook. Em seguida, a identidade do usuário é usada para solicitar um token de recurso de Cosmos DB, que é usado para conceder acesso de leitura/gravação à coleção particionada do usuário autenticado.
1. O Xamarin.Forms aplicativo usa o token de recurso para acessar diretamente Cosmos DB recursos com as permissões definidas pelo token de recurso.

> [!NOTE]
> Quando o token de recurso expirar, as solicitações de banco de dados de documento subsequentes receberão uma exceção de 401 não autorizada. Neste ponto, Xamarin.Forms os aplicativos devem restabelecer a identidade e solicitar um novo token de recurso.

Para obter mais informações sobre o particionamento de Cosmos DB, consulte [como particionar e dimensionar em Azure Cosmos DB](/azure/cosmos-db/partition-data/). Para obter mais informações sobre Cosmos DB controle de acesso, consulte [protegendo o acesso a Cosmos DB dados](/azure/cosmos-db/secure-access-to-data/) e [controle de acesso na API do SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).

## <a name="setup"></a>Instalação

O processo de integração do agente de token de recurso em um Xamarin.Forms aplicativo é o seguinte:

1. Crie uma conta de Cosmos DB que usará o controle de acesso. Para obter mais informações, consulte [configuração de Azure Cosmos DB](#azure-cosmos-db-configuration).
1. Crie um serviço de Azure App para hospedar o agente de token de recurso. Para obter mais informações, consulte [Azure app Service Configuration](#azure-app-service-configuration).
1. Crie um aplicativo do Facebook para realizar a autenticação. Para obter mais informações, consulte [configuração de aplicativo do Facebook](#facebook-app-configuration).
1. Configure o serviço de Azure App para executar a autenticação fácil com o Facebook. Para obter mais informações, consulte [Azure app configuração de autenticação do serviço](#azure-app-service-authentication-configuration).
1. Configure o Xamarin.Forms aplicativo de exemplo para se comunicar com o serviço Azure app e Cosmos DB. Para obter mais informações, consulte [ Xamarin.Forms configuração do aplicativo](#xamarinforms-application-configuration).

> [!NOTE]
> Se você não tiver uma [assinatura do Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), crie uma [conta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de começar.

### <a name="azure-cosmos-db-configuration"></a>Configuração de Azure Cosmos DB

O processo para criar uma conta de Cosmos DB que usará o controle de acesso é o seguinte:

1. Crie uma conta de Cosmos DB. Para obter mais informações, consulte [criar uma conta de Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account).
1. Na conta Cosmos DB, crie uma nova coleção denominada `UserItems` especificando uma chave de partição de `/userid` .

### <a name="azure-app-service-configuration"></a>Configuração do serviço de Azure App

O processo para hospedar o agente de token de recurso no serviço Azure App é o seguinte:

1. No portal do Azure, crie um novo aplicativo Web do serviço de aplicativo. Para obter mais informações, consulte [criar um aplicativo Web em um ambiente do serviço de aplicativo](/azure/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase/).
1. Na portal do Azure, abra a folha configurações do aplicativo para o aplicativo Web e adicione as seguintes configurações:
    - `accountUrl`– o valor deve ser o Cosmos DB URL da conta da folha chaves da conta Cosmos DB.
    - `accountKey`– o valor deve ser a Cosmos DB chave mestra (primária ou secundária) da folha chaves da conta de Cosmos DB.
    - `databaseId`– o valor deve ser o nome do banco de dados Cosmos DB.
    - `collectionId`– o valor deve ser o nome da coleção de Cosmos DB (nesse caso, `UserItems` ).
    - `hostUrl`– o valor deve ser a URL do aplicativo Web na folha de visão geral da conta do serviço de aplicativo.

    A captura de tela a seguir demonstra essa configuração:

    [![](azure-cosmosdb-auth-images/azure-web-app-settings.png "App Service Web App Settings")](azure-cosmosdb-auth-images/azure-web-app-settings-large.png#lightbox "App Service Web App Settings")

1. Publique a solução do agente de token de recurso no aplicativo Web do serviço de Azure App.

### <a name="facebook-app-configuration"></a>Configuração de aplicativo do Facebook

O processo para criar um aplicativo do Facebook para realizar a autenticação é o seguinte:

1. Crie um aplicativo do Facebook. Para obter mais informações, consulte [registrar e configurar um aplicativo](https://developers.facebook.com/docs/apps/register) no centro de desenvolvimento do Facebook.
1. Adicione o produto de logon do Facebook ao aplicativo. Para obter mais informações, consulte [Adicionar logon do Facebook ao seu aplicativo ou site](https://developers.facebook.com/docs/facebook-login) no centro de desenvolvimento do Facebook.
1. Configure o logon do Facebook da seguinte maneira:
   - Habilite o logon OAuth do cliente.
   - Habilitar logon OAuth da Web.
   - Defina o URI de redirecionamento OAuth válido para o URI do aplicativo Web do serviço de aplicativo, com `/.auth/login/facebook/callback` anexado.

  A captura de tela a seguir demonstra essa configuração:

  ![](azure-cosmosdb-auth-images/facebook-oauth-settings.png "Facebook Login OAuth Settings")

Para obter mais informações, consulte [registrar seu aplicativo com o Facebook](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-nameregister-aregister-your-application-with-facebook).

### <a name="azure-app-service-authentication-configuration"></a>Configuração de autenticação do serviço Azure App

O processo para configurar a autenticação fácil do serviço de aplicativo é o seguinte:

1. No portal do Azure, navegue até o aplicativo Web do serviço de aplicativo.
1. No portal do Azure, abra a folha autenticação/autorização e execute a seguinte configuração:
    - A autenticação do serviço de aplicativo deve ser ativada.
    - A ação a ser tomada quando uma solicitação não é autenticada deve ser definida para **fazer logon com o Facebook**.

    A captura de tela a seguir demonstra essa configuração:

    [![](azure-cosmosdb-auth-images/app-service-authentication-settings.png "App Service Web App Authentication Settings")](azure-cosmosdb-auth-images/app-service-authentication-settings-large.png#lightbox "App Service Web App Authentication Settings")

O aplicativo Web do serviço de aplicativo também deve ser configurado para se comunicar com o aplicativo do Facebook para habilitar o fluxo de autenticação. Isso pode ser feito selecionando o provedor de identidade do Facebook e inserindo os valores de **ID** do aplicativo e **segredo do aplicativo** nas configurações do aplicativo do Facebook no centro de desenvolvimento do Facebook. Para obter mais informações, consulte [adicionar informações do Facebook ao seu aplicativo](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-namesecrets-aadd-facebook-information-to-your-application).

### <a name="xamarinforms-application-configuration"></a>Xamarin.FormsConfiguração do aplicativo

O processo de configuração do Xamarin.Forms aplicativo de exemplo é o seguinte:

1. Abra a Xamarin.Forms solução.
1. Abra `Constants.cs` e atualize os valores das seguintes constantes:
    - `EndpointUri`– o valor deve ser o Cosmos DB URL da conta da folha chaves da conta Cosmos DB.
    - `DatabaseName`– o valor deve ser o nome do banco de dados de documento.
    - `CollectionName`– o valor deve ser o nome da coleção de banco de dados do documento (nesse caso, `UserItems` ).
    - `ResourceTokenBrokerUrl`– o valor deve ser a URL do aplicativo Web do agente de token de recurso da folha visão geral da conta do serviço de aplicativo.

## <a name="initiating-login"></a>Iniciando logon

O aplicativo de exemplo inicia o processo de logon redirecionando um navegador para uma URL do provedor de identidade, conforme demonstrado no seguinte código de exemplo:

```csharp
var auth = new Xamarin.Auth.WebRedirectAuthenticator(
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/facebook"),
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/done"));
```

Isso faz com que um fluxo de autenticação OAuth seja iniciado entre Azure App serviço e o Facebook, que exibe a página de logon do Facebook:

![](azure-cosmosdb-auth-images/login.png "Facebook Login")

O logon pode ser cancelado pressionando-se o botão **Cancelar** no Ios ou pressionando o botão **voltar** no Android, caso em que o usuário permanece sem autenticação e a interface do usuário do provedor de identidade é removida da tela.

## <a name="obtaining-a-resource-token"></a>Obtendo um token de recurso

Após a autenticação bem-sucedida, o `WebRedirectAuthenticator.Completed` evento é acionado. O exemplo de código a seguir demonstra como tratar este evento:

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

O resultado de uma autenticação bem-sucedida é um token de acesso, que é uma `AuthenticatorCompletedEventArgs.Account` Propriedade disponível. O token de acesso é extraído e usado em uma solicitação GET para a API do agente de token de recurso `resourcetoken` .

A `resourcetoken` API usa o token de acesso para solicitar a identidade do usuário do Facebook, que, por sua vez, é usada para solicitar um token de recurso de Cosmos DB. Se um documento de permissão válido já existir para o usuário no banco de dados de documento, ele será recuperado e um documento JSON que contém o token de recurso é retornado ao Xamarin.Forms aplicativo. Se um documento de permissão válido não existir para o usuário, um usuário e uma permissão serão criados no banco de dados de documentos e o token de recurso será extraído do documento de permissão e retornado ao Xamarin.Forms aplicativo em um documento JSON.

> [!NOTE]
> Um usuário de banco de dados de documento é um recurso associado a um banco de dados de documento, e cada banco de dados pode conter zero ou mais usuários. Uma permissão de banco de dados de documento é um recurso associado a um usuário de banco de dados de documento, e cada usuário pode conter zero ou mais permissões. Um recurso de permissão fornece acesso a um token de segurança que o usuário requer ao tentar acessar um recurso, como um documento.

Se a `resourcetoken` API for concluída com êxito, ela enviará o código de status HTTP 200 (OK) na resposta, juntamente com um documento JSON que contém o token de recurso. Os dados JSON a seguir mostram uma mensagem típica de resposta bem-sucedida:

```csharp
{
  "id": "John Smithpermission",
  "token": "type=resource&ver=1&sig=zx6k2zzxqktzvuzuku4b7y==;a74aukk99qtwk8v5rxfrfz7ay7zzqfkbfkremrwtaapvavw2mrvia4umbi/7iiwkrrq+buqqrzkaq4pp15y6bki1u//zf7p9x/aefbvqvq3tjjqiffurfx+vexa1xarxkkv9rbua9ypfzr47xpp5vmxuvzbekkwq6txme0xxxbjhzaxbkvzaji+iru3xqjp05amvq1r1q2k+qrarurhmjzah/ha0evixazkve2xk1zu9u/jpyf1xrwbkxqpzebvqwma+hyyaazemr6qx9uz9be==;",
  "expires": 4035948,
  "userid": "John Smith"
}
```

O `WebRedirectAuthenticator.Completed` manipulador de eventos lê a resposta da `resourcetoken` API e extrai o token de recurso e a ID de usuário. O token de recurso é passado como um argumento para o `DocumentClient` Construtor, que encapsula o ponto de extremidade, as credenciais e a política de conexão usados para acessar Cosmos DB e é usado para configurar e executar solicitações em relação a Cosmos DB. O token de recurso é enviado com cada solicitação para acessar diretamente um recurso e indica que o acesso de leitura/gravação à coleção particionada de usuários autenticados é concedido.

## <a name="retrieving-documents"></a>Recuperando documentos

A recuperação de documentos que pertencem somente ao usuário autenticado pode ser obtida com a criação de uma consulta de documento que inclui a ID do usuário como uma chave de partição e é demonstrada no exemplo de código a seguir:

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

A consulta recupera de forma assíncrona todos os documentos pertencentes ao usuário autenticado, da coleção especificada e os coloca em uma `List<TodoItem>` coleção para exibição.

O `CreateDocumentQuery<T>` método especifica um `Uri` argumento que representa a coleção que deve ser consultada para documentos e um `FeedOptions` objeto. O `FeedOptions` objeto especifica que um número ilimitado de itens pode ser retornado pela consulta e a ID do usuário como uma chave de partição. Isso garante que apenas os documentos na coleção particionada do usuário sejam retornados no resultado.

> [!NOTE]
> Observe que os documentos de permissão, que são criados pelo agente de token de recurso, são armazenados na mesma coleção de documentos que os documentos criados pelo Xamarin.Forms aplicativo. Portanto, a consulta de documento contém uma `Where` cláusula que aplica um predicado de filtragem à consulta na coleção de documentos. Essa cláusula garante que os documentos de permissão não sejam retornados da coleção de documentos.

Para obter mais informações sobre como recuperar documentos de uma coleção de documentos, consulte [recuperando documentos de coleção de documentos](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#retrieving-document-collection-documents).

## <a name="inserting-documents"></a>Inserindo documentos

Antes de inserir um documento em uma coleção de documentos, a `TodoItem.UserId` propriedade deve ser atualizada com o valor que está sendo usado como a chave de partição, conforme demonstrado no exemplo de código a seguir:

```csharp
item.UserId = UserId;
await client.CreateDocumentAsync(collectionLink, item);
```

Isso garante que o documento será inserido na coleção particionada do usuário.

Para obter mais informações sobre como inserir um documento em uma coleção de documentos, consulte [inserindo um documento em uma coleção de documentos](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#inserting-a-document-into-a-document-collection).

## <a name="deleting-documents"></a>Excluindo documentos

O valor da chave de partição deve ser especificado ao excluir um documento de uma coleção particionada, conforme demonstrado no exemplo de código a seguir:

```csharp
await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id),
                 new RequestOptions
                 {
                   PartitionKey = new PartitionKey(UserId)
                 });
```

Isso garante que Cosmos DB saiba em qual coleção particionada excluir o documento.

Para obter mais informações sobre como excluir um documento de uma coleção de documentos, consulte [excluindo um documento de uma coleção de documentos](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#deleting-a-document-from-a-document-collection).

## <a name="summary"></a>Resumo

Este artigo explicou como combinar o controle de acesso com coleções particionadas, para que um usuário só possa acessar seus próprios documentos de banco de dados de documentos em um Xamarin.Forms aplicativo. Especificar a identidade do usuário como uma chave de partição garante que uma coleção particionada só possa armazenar documentos para esse usuário.

## <a name="related-links"></a>Links relacionados

- [Autenticação de Azure Cosmos DB todo (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)
- [Consumo de um banco de dados de documento do Azure Cosmos DB](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md)
- [Protegendo o acesso aos dados do Azure Cosmos DB](/azure/cosmos-db/secure-access-to-data/)
- [Controle de acesso na API do SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).
- [Como particionar e dimensionar no BD Cosmos do Azure](/azure/cosmos-db/partition-data/)
- [Biblioteca de cliente Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API do Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn948556.aspx)
