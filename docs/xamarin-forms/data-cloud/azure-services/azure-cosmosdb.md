---
title: Consumir um banco de dados de documentos Azure Cosmos DB noXamarin.Forms
description: Este artigo explica como usar a biblioteca de cliente do Azure Cosmos DB .NET Standard para integrar um banco de dados de documentos Azure Cosmos DB a um Xamarin.Forms aplicativo.
ms.prod: xamarin
ms.assetid: 7C0605D9-9B7F-4002-9B60-2B5DAA3EA30C
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b8cd2ea2f54e4bfdfea4fa5787a01aca252691b4
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84564648"
---
# <a name="consume-an-azure-cosmos-db-document-database-in-xamarinforms"></a>Consumir um banco de dados de documentos Azure Cosmos DB noXamarin.Forms

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdb)

_Um banco de dados de documentos Azure Cosmos DB é um banco de dados NoSQL que fornece acesso de baixa latência a documentos JSON, oferecendo um serviço de banco de dados rápido, altamente disponível e escalonável para aplicativos que exigem dimensionamento contínuo e replicação global. Este artigo explica como usar a biblioteca de cliente do Azure Cosmos DB .NET Standard para integrar um banco de dados de documentos Azure Cosmos DB a um Xamarin.Forms aplicativo._

> [!VIDEO https://youtube.com/embed/BoVH12igmbg]

**Vídeo de Microsoft Azure Cosmos DB**

Uma conta de banco de dados de documento Azure Cosmos DB pode ser provisionada usando uma assinatura do Azure. Cada conta de banco de dados pode ter zero ou mais bancos de dados. Um banco de dados de documentos no Azure Cosmos DB é um contêiner lógico para usuários e coleções de documentos.

Um banco de dados de documentos Azure Cosmos DB pode conter zero ou mais coleções de documentos. Cada coleção de documentos pode ter um nível de desempenho diferente, permitindo que mais produtividade seja especificada para coleções acessadas com frequência e menos taxa de transferência para coleções acessadas com pouca frequência.

Cada coleção de documentos consiste em zero ou mais documentos JSON. Os documentos em uma coleção são livres de esquema e, portanto, não precisam compartilhar a mesma estrutura ou campos. À medida que os documentos são adicionados a uma coleção de documentos, Cosmos DB os indexa automaticamente e eles ficam disponíveis para serem consultados.

Para fins de desenvolvimento, um banco de dados de documentos também pode ser consumido por meio de um emulador. Usando o emulador, os aplicativos podem ser desenvolvidos e testados localmente, sem criar uma assinatura do Azure ou incorrer em custos. Para obter mais informações sobre o emulador, consulte [desenvolvendo localmente com o emulador de Azure Cosmos DB](/azure/cosmos-db/local-emulator/).

Este artigo e o aplicativo de exemplo que o acompanham demonstra um aplicativo de lista de tarefas pendentes em que elas são armazenadas em um banco de dados de Azure Cosmos DB de documentos. Para obter mais informações sobre o aplicativo de exemplo, consulte [noções básicas sobre o exemplo](~/xamarin-forms/data-cloud/web-services/introduction.md).

Para obter mais informações sobre Azure Cosmos DB, consulte a [documentação do Azure Cosmos DB](/azure/cosmos-db/).

> [!NOTE]
> Se você não tiver uma [assinatura do Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), crie uma [conta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de começar.

## <a name="setup"></a>Instalação

O processo de integração de um banco de dados de documento Azure Cosmos DB em um Xamarin.Forms aplicativo é o seguinte:

1. Crie uma conta de Cosmos DB. Para obter mais informações, consulte [criar uma conta de Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#create-an-azure-cosmos-account).
1. Adicione o pacote NuGet da [biblioteca de cliente do Azure Cosmos DB .net Standard](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core) aos projetos da plataforma na Xamarin.Forms solução.
1. Adicione `using` diretivas para os `Microsoft.Azure.Documents` `Microsoft.Azure.Documents.Client` `Microsoft.Azure.Documents.Linq` namespaces, e às classes que acessarão a conta de Cosmos DB.

Depois de executar essas etapas, a Azure Cosmos DB .NET Standard biblioteca de cliente pode ser usada para configurar e executar solicitações no banco de dados de documentos.

> [!NOTE]
> A biblioteca de cliente Azure Cosmos DB .NET Standard só pode ser instalada em projetos de plataforma e não em um projeto PCL (biblioteca de classes portátil). Portanto, o aplicativo de exemplo é um projeto de acesso compartilhado (SAP) para evitar a duplicação de código. No entanto, a `DependencyService` classe pode ser usada em um projeto PCL para invocar Azure Cosmos DB .net Standard código de biblioteca de cliente contido em projetos específicos da plataforma.

## <a name="consuming-the-azure-cosmos-db-account"></a>Consumindo a conta de Azure Cosmos DB

O `DocumentClient` tipo encapsula o ponto de extremidade, as credenciais e a política de conexão usados para acessar a conta de Azure Cosmos DB e é usado para configurar e executar solicitações em relação à conta. O exemplo de código a seguir demonstra como criar uma instância dessa classe:

```csharp
DocumentClient client = new DocumentClient(new Uri(Constants.EndpointUri), Constants.PrimaryKey);
```

O URI de Cosmos DB e a chave primária devem ser fornecidos para o `DocumentClient` Construtor. Eles podem ser obtidos no portal do Azure. Para obter mais informações, consulte [conectar-se a uma conta de Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#Connect).

### <a name="creating-a-database"></a>Criar um banco de dados

Um banco de dados de documentos é um contêiner lógico para coleções de documentos e usuários e pode ser criado no portal do Azure ou programaticamente usando o `DocumentClient.CreateDatabaseIfNotExistsAsync` método:

```csharp
public async Task CreateDatabase(string databaseName)
{
  ...
  await client.CreateDatabaseIfNotExistsAsync(new Database
  {
    Id = databaseName
  });
  ...
}
```

O `CreateDatabaseIfNotExistsAsync` método especifica um `Database` objeto como um argumento, com o `Database` objeto que especifica o nome do banco de dados como sua `Id` propriedade. O `CreateDatabaseIfNotExistsAsync` método criará o banco de dados se ele não existir, ou retornará o banco de dados se ele já existir. No entanto, o aplicativo de exemplo ignora todos os dados retornados pelo `CreateDatabaseIfNotExistsAsync` método.

> [!NOTE]
> O `CreateDatabaseIfNotExistsAsync` método retorna um `Task<ResourceResponse<Database>>` objeto e o código de status da resposta pode ser verificado para determinar se um banco de dados foi criado ou se um banco de dados existente foi retornado.

### <a name="creating-a-document-collection"></a>Criando uma coleção de documentos

Uma coleção de documentos é um contêiner para documentos JSON e pode ser criada no portal do Azure ou programaticamente usando o `DocumentClient.CreateDocumentCollectionIfNotExistsAsync` método:

```csharp
public async Task CreateDocumentCollection(string databaseName, string collectionName)
{
  ...
  // Create collection with 400 RU/s
  await client.CreateDocumentCollectionIfNotExistsAsync(
    UriFactory.CreateDatabaseUri(databaseName),
    new DocumentCollection
    {
      Id = collectionName
    },
    new RequestOptions
    {
      OfferThroughput = 400
    });
  ...
}
```

O `CreateDocumentCollectionIfNotExistsAsync` método requer dois argumentos compulsórios: um nome de banco de dados especificado como um `Uri` e um `DocumentCollection` objeto. O `DocumentCollection` objeto representa uma coleção de documentos cujo nome é especificado com a `Id` propriedade. O `CreateDocumentCollectionIfNotExistsAsync` método cria a coleção de documentos, caso ela não exista, ou retorna a coleção de documentos, caso ela já exista. No entanto, o aplicativo de exemplo ignora todos os dados retornados pelo `CreateDocumentCollectionIfNotExistsAsync` método.

> [!NOTE]
> O `CreateDocumentCollectionIfNotExistsAsync` método retorna um `Task<ResourceResponse<DocumentCollection>>` objeto e o código de status da resposta pode ser verificado para determinar se uma coleção de documentos foi criada ou se uma coleção de documentos existente foi retornada.

Opcionalmente, o `CreateDocumentCollectionIfNotExistsAsync` método também pode especificar um `RequestOptions` objeto, que encapsula opções que podem ser especificadas para solicitações emitidas para a conta de Cosmos DB. A `RequestOptions.OfferThroughput` propriedade é usada para definir o nível de desempenho da coleção de documentos e, no aplicativo de exemplo, é definida como 400 unidades de solicitação por segundo. Esse valor deve ser aumentado ou diminuído dependendo se a coleção será frequentemente ou acessada com pouca frequência.

> [!IMPORTANT]
> Observe que o `CreateDocumentCollectionIfNotExistsAsync` método criará uma nova coleção com uma taxa de transferência reservada, que tem implicações de preço.

### <a name="retrieving-document-collection-documents"></a>Recuperando documentos de coleção de documentos

O conteúdo de uma coleção de documentos pode ser recuperado criando e executando uma consulta de documento. Uma consulta de documento é criada com o `DocumentClient.CreateDocumentQuery` método:

```csharp
public async Task<List<TodoItem>> GetTodoItemsAsync()
{
  ...
  var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
            .AsDocumentQuery();
  while (query.HasMoreResults)
  {
    Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
  }
  ...
}
```

Essa consulta recupera de forma assíncrona todos os documentos da coleção especificada e coloca os documentos em uma `List<TodoItem>` coleção para exibição.

O `CreateDocumentQuery<T>` método especifica um `Uri` argumento que representa a coleção que deve ser consultada para documentos. Neste exemplo, a `collectionLink` variável é um campo de nível de classe que especifica o `Uri` que representa a coleção de documentos da qual os documentos serão recuperados:

```csharp
Uri collectionLink = UriFactory.CreateDocumentCollectionUri(Constants.DatabaseName, Constants.CollectionName);
```

O `CreateDocumentQuery<T>` método cria uma consulta executada de forma síncrona e retorna um `IQueryable<T>` objeto. No entanto, o `AsDocumentQuery` método converte o `IQueryable<T>` objeto em um `IDocumentQuery<T>` objeto que pode ser executado de forma assíncrona. A consulta assíncrona é executada com o `IDocumentQuery<T>.ExecuteNextAsync` método, que recupera a próxima página de resultados do banco de dados de documentos, com a `IDocumentQuery<T>.HasMoreResults` Propriedade indicando se há resultados adicionais a serem retornados da consulta.

Os documentos podem ser filtrados no lado do servidor, incluindo uma `Where` cláusula na consulta, que aplica um predicado de filtragem à consulta na coleção de documentos:

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
          .Where(f => f.Done != true)
          .AsDocumentQuery();
```

Essa consulta recupera todos os documentos da coleção cuja `Done` propriedade é igual a `false` .

### <a name="inserting-a-document-into-a-document-collection"></a>Inserindo um documento em uma coleção de documentos

Os documentos são conteúdo JSON definido pelo usuário e podem ser inseridos em uma coleção de documentos com o `DocumentClient.CreateDocumentAsync` método:

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.CreateDocumentAsync(collectionLink, item);
  ...
}
```

O `CreateDocumentAsync` método especifica um `Uri` argumento que representa a coleção em que o documento deve ser inserido e um `object` argumento que representa o documento a ser inserido.

### <a name="replacing-a-document-in-a-document-collection"></a>Substituindo um documento em uma coleção de documentos

Os documentos podem ser substituídos em uma coleção de documentos com o `DocumentClient.ReplaceDocumentAsync` método:

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, item.Id), item);
  ...
}
```

O `ReplaceDocumentAsync` método especifica um `Uri` argumento que representa o documento na coleção que deve ser substituído e um `object` argumento que representa os dados de documento atualizados.

### <a name="deleting-a-document-from-a-document-collection"></a>Excluindo um documento de uma coleção de documentos

Um documento pode ser excluído de uma coleção de documentos com o `DocumentClient.DeleteDocumentAsync` método:

```csharp
public async Task DeleteTodoItemAsync(string id)
{
  ...
  await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id));
  ...
}
```

O `DeleteDocumentAsync` método especifica um `Uri` argumento que representa o documento na coleção que deve ser excluída.

### <a name="deleting-a-document-collection"></a>Excluindo uma coleção de documentos

Uma coleção de documentos pode ser excluída de um banco de dados com o `DocumentClient.DeleteDocumentCollectionAsync` método:

```csharp
await client.DeleteDocumentCollectionAsync(collectionLink);
```

O `DeleteDocumentCollectionAsync` método especifica um `Uri` argumento que representa a coleção de documentos a ser excluída. Observe que a invocação desse método também excluirá os documentos armazenados na coleção.

### <a name="deleting-a-database"></a>Excluindo um banco de dados

Um banco de dados pode ser excluído de uma conta de banco de dados Cosmos DB com o `DocumentClient.DeleteDatabaesAsync` método:

```csharp
await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(Constants.DatabaseName));
```

O `DeleteDatabaseAsync` método especifica um `Uri` argumento que representa o banco de dados a ser excluído. Observe que a invocação desse método também excluirá as coleções de documentos armazenadas no banco de dados e os documentos armazenados nas coleções de documentos.

## <a name="summary"></a>Resumo

Este artigo explicou como usar a biblioteca de cliente do Azure Cosmos DB .NET Standard para integrar um banco de dados de documentos Azure Cosmos DB a um Xamarin.Forms aplicativo. Um banco de dados de documentos Azure Cosmos DB é um banco de dados NoSQL que fornece acesso de baixa latência a documentos JSON, oferecendo um serviço de banco de dados rápido, altamente disponível e escalonável para aplicativos que exigem dimensionamento contínuo e replicação global.

## <a name="related-links"></a>Links relacionados

- [Azure Cosmos DB todo (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-tododocumentdb)
- [Documentação do Banco de Dados do Azure Cosmos](/azure/cosmos-db/)
- [Biblioteca de cliente de .NET Standard Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API do Azure Cosmos DB](https://docs.microsoft.com/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)
