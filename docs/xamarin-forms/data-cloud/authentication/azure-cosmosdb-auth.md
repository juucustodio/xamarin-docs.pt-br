---
title: Autenticar usuários com um banco de dados de documentos Azure Cosmos DB e formulários Xamarin.Forms
description: Este artigo explica como combinar o controle de acesso com coleções particiadas do Azure Cosmos DB, para que um usuário só possa acessar seus próprios documentos em um aplicativo Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 11ED4A4C-0F05-40B2-AB06-5A0F2188EF3D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 6e79e647d64103b6d257de7233f488899bcaff40
ms.sourcegitcommit: 2a8eb8bce427e72d4e7edd06ce432e19f17dcdd7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388597"
---
# <a name="authenticate-users-with-an-azure-cosmos-db-document-database-and-xamarinforms"></a>Autenticar usuários com um banco de dados de documentos Azure Cosmos DB e formulários Xamarin.Forms

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)

_Os bancos de dados de documentos Azure Cosmos DB suportam coleções particionadas, que podem abranger vários servidores e partições, ao mesmo tempo em que suportam armazenamento e throughput ilimitados. Este artigo explica como combinar o controle de acesso com coleções particionadas, para que um usuário só possa acessar seus próprios documentos em um aplicativo Xamarin.Forms._

## <a name="overview"></a>Visão geral

Uma chave de partição deve ser especificada ao criar uma coleção particionada, e documentos com a mesma chave de partição serão armazenados na mesma partição. Portanto, especificar a identidade do usuário como uma chave de partição resultará em uma coleção particionada que armazenará apenas documentos para esse usuário. Isso também garante que o banco de dados de documentos Azure Cosmos DB será dimensionado à medida que o número de usuários e itens aumentar.

O acesso deve ser concedido a qualquer coleção, e o modelo de controle de acesso à API SQL define dois tipos de construtos de acesso:

- **As chaves-mestre** permitem acesso administrativo total a todos os recursos dentro de uma conta Do Cosmos DB e são criadas quando uma conta do Cosmos DB é criada.
- **Os tokens de recursos** capturam a relação entre o usuário de um banco de dados e a permissão que o usuário tem para um recurso específico do Cosmos DB, como uma coleção ou um documento.

Expor uma chave mestra abre uma conta do Cosmos DB para a possibilidade de uso malicioso ou negligente. No entanto, os tokens de recursos Do Azure Cosmos DB fornecem um mecanismo seguro para permitir que os clientes leiam, escrevam e excluam recursos específicos em uma conta Azure Cosmos DB de acordo com as permissões concedidas.

Uma abordagem típica para solicitar, gerar e fornecer tokens de recursos para um aplicativo móvel é usar uma corretora de tokens de recursos. O diagrama a seguir mostra uma visão geral de alto nível de como o aplicativo de exemplo usa uma corretora de tokens de recursos para gerenciar o acesso aos dados do banco de dados do documento:

![](azure-cosmosdb-auth-images/documentdb-authentication.png "Document Database Authentication Process")

O corretor de tokens de recursos é um serviço de API web intermediário, hospedado no Azure App Service, que possui a chave mestra da conta Cosmos DB. O aplicativo de exemplo usa a corretora de tokens de recursos para gerenciar o acesso aos dados do banco de dados do documento da seguinte forma:

1. No login, o aplicativo Xamarin.Forms entra em contato com o Azure App Service para iniciar um fluxo de autenticação.
1. O Azure App Service realiza um fluxo de autenticação OAuth com o Facebook. Após a conclusão do fluxo de autenticação, o aplicativo Xamarin.Forms recebe um token de acesso.
1. O aplicativo Xamarin.Forms usa o token de acesso para solicitar um token de recurso da corretora de tokens de recursos.
1. A corretora de tokens de recursos usa o token de acesso para solicitar a identidade do usuário do Facebook. A identidade do usuário é então usada para solicitar um token de recurso do Cosmos DB, que é usado para conceder acesso de leitura/gravação à coleção particionada do usuário autenticado.
1. O aplicativo Xamarin.Forms usa o token de recursos para acessar diretamente os recursos do Cosmos DB com as permissões definidas pelo token de recursos.

> [!NOTE]
> Quando o token de recurso expirar, as solicitações subsequentes do banco de dados de documentos receberão uma exceção não autorizada de 401. Neste ponto, os aplicativos Xamarin.Forms devem restabelecer a identidade e solicitar um novo token de recurso.

Para obter mais informações sobre a particionamento do Cosmos DB, consulte [Como particionar e dimensionar no Azure Cosmos DB](/azure/cosmos-db/partition-data/). Para obter mais informações sobre o controle de acesso do Cosmos DB, consulte [Proteger o acesso aos dados do Cosmos DB](/azure/cosmos-db/secure-access-to-data/) e o controle de acesso na [API SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).

## <a name="setup"></a>Instalação

O processo de integração do corretor de tokens de recursos em um aplicativo Xamarin.Forms é o seguinte:

1. Crie uma conta Cosmos DB que usará o controle de acesso. Para obter mais informações, consulte [Configuração Cosmos DB](#cosmosdb_configuration).
1. Crie um Serviço de Aplicativo Azure para hospedar a corretora de tokens de recursos. Para obter mais informações, consulte [a configuração do serviço do aplicativo Azure](#app_service_configuration).
1. Crie um aplicativo do Facebook para realizar a autenticação. Para obter mais informações, consulte [Configuração do aplicativo do Facebook](#facebook_configuration).
1. Configure o Azure App Service para realizar fácil autenticação com o Facebook. Para obter mais informações, consulte [a configuração de autenticação do serviço do aplicativo Azure](#app_service_authentication_configuration).
1. Configure o aplicativo de exemplo Xamarin.Forms para se comunicar com o Azure App Service e o Cosmos DB. Para obter mais informações, consulte [Configuração do aplicativo Xamarin.Forms](#forms_application_configuration).

> [!NOTE]
> Se você não tiver uma [assinatura do Azure,](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)crie uma [conta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de começar.

<a name="cosmosdb_configuration" />

### <a name="azure-cosmos-db-configuration"></a>Configuração do Azure Cosmos DB

O processo para criar uma conta Cosmos DB que usará o controle de acesso é o seguinte:

1. Crie uma conta cosmos DB. Para obter mais informações, consulte [Criar uma conta Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#step-1-create-an-azure-cosmos-db-account).
1. Na conta Cosmos DB, crie `UserItems`uma nova coleção chamada `/userid`, especificando uma chave de partição de .

<a name="app_service_configuration" />

### <a name="azure-app-service-configuration"></a>Configuração do serviço do aplicativo Azure

O processo para hospedagem da corretora de tokens de recursos no Azure App Service é o seguinte:

1. No portal Azure, crie um novo aplicativo web App Service. Para obter mais informações, consulte [Criar um aplicativo web em um ambiente de serviço de aplicativo](/azure/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase/).
1. No portal Azure, abra a lâmina Configurações do aplicativo para o aplicativo web e adicione as seguintes configurações:
    - `accountUrl`– o valor deve ser o URL da conta Cosmos DB da lâmina Keys da conta Cosmos DB.
    - `accountKey`– o valor deve ser a chave mestra Cosmos DB (primária ou secundária) da lâmina Keys da conta Cosmos DB.
    - `databaseId`– o valor deve ser o nome do banco de dados Cosmos DB.
    - `collectionId`– o valor deve ser o nome da coleção `UserItems`Cosmos DB (neste caso, ).
    - `hostUrl`– o valor deve ser a URL do aplicativo web a partir da lâmina Visão Geral da conta do App Service.

    A captura de tela a seguir demonstra essa configuração:

    [![](azure-cosmosdb-auth-images/azure-web-app-settings.png "App Service Web App Settings")](azure-cosmosdb-auth-images/azure-web-app-settings-large.png#lightbox "App Service Web App Settings")

1. Publique a solução de corretor de tokens de recursos para o aplicativo web Azure App Service.

<a name="facebook_configuration" />

### <a name="facebook-app-configuration"></a>Configuração do aplicativo do Facebook

O processo para criar um aplicativo do Facebook para realizar a autenticação é o seguinte:

1. Crie um aplicativo do Facebook. Para obter mais informações, consulte [Registrar e Configurar um aplicativo](https://developers.facebook.com/docs/apps/register) no Centro de Desenvolvedores do Facebook.
1. Adicione o produto de login do Facebook ao aplicativo. Para obter mais informações, consulte [Adicionar login do Facebook ao seu aplicativo ou site](https://developers.facebook.com/docs/facebook-login) no Centro de Desenvolvedores do Facebook.
1. Configure o Login do Facebook da seguinte forma:
   - Habilite o login do Cliente OAuth.
   - Habilite o login da Web OAuth.
   - Defina o Uri de redirecionamento do Valid OAuth `/.auth/login/facebook/callback` para o URI do aplicativo web App Service, com anexo.

  A captura de tela a seguir demonstra essa configuração:

  ![](azure-cosmosdb-auth-images/facebook-oauth-settings.png "Facebook Login OAuth Settings")

Para obter mais informações, consulte [Registre seu aplicativo no Facebook](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-nameregister-aregister-your-application-with-facebook).

<a name="app_service_authentication_configuration" />

### <a name="azure-app-service-authentication-configuration"></a>Configuração de autenticação do serviço do aplicativo Azure

O processo para configurar a autenticação fácil do App Service é o seguinte:

1. No Portal Azure, navegue até o aplicativo web App Service.
1. No Portal Azure, abra a lâmina Autenticação/Autorização e execute a seguinte configuração:
    - A autenticação do serviço de aplicativos deve ser ligada.
    - A ação a ser tomada quando uma solicitação não é autenticada deve ser definida como **Login com o Facebook**.

    A captura de tela a seguir demonstra essa configuração:

    [![](azure-cosmosdb-auth-images/app-service-authentication-settings.png "App Service Web App Authentication Settings")](azure-cosmosdb-auth-images/app-service-authentication-settings-large.png#lightbox "App Service Web App Authentication Settings")

O aplicativo web App Service também deve ser configurado para se comunicar com o aplicativo do Facebook para permitir o fluxo de autenticação. Isso pode ser feito selecionando o provedor de identidade do Facebook e inserindo os valores do **App ID** e **App Secret** a partir das configurações do aplicativo do Facebook no Centro de Desenvolvedores do Facebook. Para obter mais informações, consulte [Adicionar informações do Facebook ao seu aplicativo](/azure/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication#a-namesecrets-aadd-facebook-information-to-your-application).

<a name="forms_application_configuration" />

### <a name="xamarinforms-application-configuration"></a>Configuração do aplicativo Xamarin.Forms

O processo de configuração do aplicativo de amostra Xamarin.Forms é o seguinte:

1. Abra a solução Xamarin.Forms.
1. Abra `Constants.cs` e atualize os valores das seguintes constantes:
    - `EndpointUri`– o valor deve ser o URL da conta Cosmos DB da lâmina Keys da conta Cosmos DB.
    - `DatabaseName`– o valor deve ser o nome do banco de dados de documentos.
    - `CollectionName`– o valor deve ser o nome da coleção `UserItems`de banco de dados de documentos (neste caso, ).
    - `ResourceTokenBrokerUrl`– o valor deve ser a URL do aplicativo web da corretora de tokens de recursos da lâmina Visão Geral da conta do App Service.

## <a name="initiating-login"></a>Início do login

O aplicativo de exemplo inicia o processo de login redirecionando um navegador para uma URL do provedor de identidade, conforme demonstrado no seguinte código de exemplo:

```csharp
var auth = new Xamarin.Auth.WebRedirectAuthenticator(
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/facebook"),
  new Uri(Constants.ResourceTokenBrokerUrl + "/.auth/login/done"));
```

Isso faz com que um fluxo de autenticação OAuth seja iniciado entre o Azure App Service e o Facebook, que exibe a página de login do Facebook:

![](azure-cosmosdb-auth-images/login.png "Facebook Login")

O login pode ser cancelado pressionando o botão **Cancelar** no iOS ou pressionando o botão **Voltar** no Android, caso em que o usuário permanece sem autenticação e a interface do usuário do provedor de identidade é removida da tela.

## <a name="obtaining-a-resource-token"></a>Obtenção de um token de recursos

Após autenticação bem `WebRedirectAuthenticator.Completed` sucedida, o evento é acionado. O exemplo de código a seguir demonstra o manuseio deste evento:

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

O resultado de uma autenticação bem-sucedida é `AuthenticatorCompletedEventArgs.Account` um token de acesso, que é propriedade disponível. O token de acesso é extraído e usado em uma `resourcetoken` solicitação GET para a API da corretora de tokens de recursos.

A `resourcetoken` API usa o token de acesso para solicitar a identidade do usuário do Facebook, que por sua vez é usado para solicitar um token de recurso do Cosmos DB. Se um documento de permissão válido já existir para o usuário no banco de dados do documento, ele será recuperado e um documento JSON contendo o token de recurso será devolvido ao aplicativo Xamarin.Forms. Se um documento de permissão válido não existir para o usuário, um usuário e a permissão serão criados no banco de dados do documento e o token de recurso será extraído do documento de permissão e retornado ao aplicativo Xamarin.Forms em um documento JSON.

> [!NOTE]
> Um usuário de banco de dados de documentos é um recurso associado a um banco de dados de documentos, e cada banco de dados pode conter zero ou mais usuários. Uma permissão de banco de dados de documentos é um recurso associado a um usuário de banco de dados de documentos, e cada usuário pode conter zero ou mais permissões. Um recurso de permissão fornece acesso a um token de segurança que o usuário requer ao tentar acessar um recurso, como um documento.

Se `resourcetoken` a API for concluída com sucesso, ela enviará o código de status HTTP 200 (OK) na resposta, juntamente com um documento JSON contendo o token de recurso. Os seguintes dados do JSON mostram uma mensagem de resposta típica de sucesso:

```csharp
{
  "id": "John Smithpermission",
  "token": "type=resource&ver=1&sig=zx6k2zzxqktzvuzuku4b7y==;a74aukk99qtwk8v5rxfrfz7ay7zzqfkbfkremrwtaapvavw2mrvia4umbi/7iiwkrrq+buqqrzkaq4pp15y6bki1u//zf7p9x/aefbvqvq3tjjqiffurfx+vexa1xarxkkv9rbua9ypfzr47xpp5vmxuvzbekkwq6txme0xxxbjhzaxbkvzaji+iru3xqjp05amvq1r1q2k+qrarurhmjzah/ha0evixazkve2xk1zu9u/jpyf1xrwbkxqpzebvqwma+hyyaazemr6qx9uz9be==;",
  "expires": 4035948,
  "userid": "John Smith"
}
```

O `WebRedirectAuthenticator.Completed` manipulador de eventos lê `resourcetoken` a resposta da API e extrai o token de recurso e o id do usuário. O token de recurso é então `DocumentClient` passado como um argumento para o construtor, que encapsula o ponto final, credenciais e política de conexão usado sustais para acessar o Cosmos DB, e é usado para configurar e executar solicitações contra o Cosmos DB. O token de recurso é enviado a cada solicitação para acessar diretamente um recurso e indica que o acesso à leitura/gravação da coleção particionada dos usuários autenticados é concedido.

## <a name="retrieving-documents"></a>Recuperando documentos

A recuperação de documentos que só pertencem ao usuário autenticado pode ser obtida criando uma consulta de documento que inclua a identificação do usuário como uma chave de partição e é demonstrada no seguinte exemplo de código:

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

A consulta recupera assíncronamente todos os documentos pertencentes ao usuário autenticado, a partir `List<TodoItem>` da coleção especificada, e os coloca em uma coleção para exibição.

O `CreateDocumentQuery<T>` método especifica `Uri` um argumento que representa a coleção que deve `FeedOptions` ser consultada por documentos e um objeto. O `FeedOptions` objeto especifica que um número ilimitado de itens pode ser devolvido pela consulta e a id do usuário como uma chave de partição. Isso garante que apenas documentos na coleção particionada do usuário sejam devolvidos no resultado.

> [!NOTE]
> Observe que os documentos de permissão, que são criados pela corretora de tokens de recursos, são armazenados na mesma coleção de documentos que os documentos criados pelo aplicativo Xamarin.Forms. Portanto, a consulta documental `Where` contém uma cláusula que aplica um predicado de filtragem à consulta contra a coleta de documentos. Esta cláusula garante que os documentos de permissão não sejam devolvidos da coleta de documentos.

Para obter mais informações sobre como recuperar documentos de uma coleta de documentos, consulte [Recuperando documentos de coleta de documentos](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#document_query).

## <a name="inserting-documents"></a>Inserindo documentos

Antes de inserir um documento em `TodoItem.UserId` uma coleta de documentos, o imóvel deve ser atualizado com o valor sendo usado como chave de partição, conforme demonstrado no seguinte exemplo de código:

```csharp
item.UserId = UserId;
await client.CreateDocumentAsync(collectionLink, item);
```

Isso garante que o documento seja inserido na coleção particionada do usuário.

Para obter mais informações sobre como inserir um documento em uma coleta de documentos, consulte [Inserir um documento em uma coleta de documentos](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#inserting_document).

## <a name="deleting-documents"></a>Excluindo documentos

O valor da chave de partição deve ser especificado ao excluir um documento de uma coleção particionada, conforme demonstrado no exemplo de código a seguir:

```csharp
await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id),
                 new RequestOptions
                 {
                   PartitionKey = new PartitionKey(UserId)
                 });
```

Isso garante que o Cosmos DB saiba de qual coleção particionada para excluir o documento.

Para obter mais informações sobre a exclusão de um documento de uma coleção de documentos, consulte [Excluindo um documento de uma coleta de documentos](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md#deleting_document).

## <a name="summary"></a>Resumo

Este artigo explicou como combinar o controle de acesso com coleções particionadas, para que um usuário só possa acessar seus próprios documentos de banco de dados em um aplicativo Xamarin.Forms. Especificar a identidade do usuário como uma chave de partição garante que uma coleção particionada só pode armazenar documentos para esse usuário.

## <a name="related-links"></a>Links relacionados

- [Todo Azure Cosmos DB Auth (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdbauth)
- [Consumo de um banco de dados de documento do Azure Cosmos DB](~/xamarin-forms/data-cloud/azure-services/azure-cosmosdb.md)
- [Protegendo o acesso aos dados do Azure Cosmos DB](/azure/cosmos-db/secure-access-to-data/)
- [Controle de acesso na API SQL](/rest/api/documentdb/access-control-on-documentdb-resources/).
- [Como particionar e dimensionar no BD Cosmos do Azure](/azure/cosmos-db/partition-data/)
- [Biblioteca cliente Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API do Azure Cosmos DB](https://msdn.microsoft.com/library/azure/dn948556.aspx)
