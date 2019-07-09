---
title: Consumir um banco de dados de documentos do Azure Cosmos DB no xamarin. Forms
description: Este artigo explica como usar a biblioteca de cliente .NET Standard do Azure Cosmos DB para integrar um banco de dados de documentos do Azure Cosmos DB em um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: 7C0605D9-9B7F-4002-9B60-2B5DAA3EA30C
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
ms.openlocfilehash: 0f0a9d2a5c7afb54256c7c2d3087b7912e833e3f
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659223"
---
# <a name="consume-an-azure-cosmos-db-document-database-in-xamarinforms"></a>Consumir um banco de dados de documentos do Azure Cosmos DB no xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoDocumentDB/)

_Um banco de dados de documentos do Azure Cosmos DB é um banco de dados NoSQL que fornece acesso de baixa latência para documentos JSON, oferecendo um serviço de banco de dados rápida, altamente disponível e escalonável para aplicativos que exigem dimensionamento perfeito e replicação global. Este artigo explica como usar a biblioteca de cliente .NET Standard do Azure Cosmos DB para integrar um banco de dados de documentos do Azure Cosmos DB em um aplicativo xamarin. Forms._

> [!VIDEO https://youtube.com/embed/BoVH12igmbg]

**Vídeo do Microsoft Azure Cosmos DB**

Uma conta de banco de dados de documento do Azure Cosmos DB pode ser provisionada usando uma assinatura do Azure. Cada conta de banco de dados pode ter zero ou mais bancos de dados. Um banco de dados de documentos no Azure Cosmos DB é um contêiner lógico para os usuários e coleções de documento.

Um banco de dados de documentos do Azure Cosmos DB pode conter zero ou mais coleções de documento. Cada coleção de documentos pode ter um nível de desempenho diferentes, permitindo que mais taxa de transferência deve ser especificado para coleções acessadas com frequência e menos produtividade para coleções acessadas com pouca frequência.

Cada coleção de documento consiste em zero ou mais documentos JSON. Documentos em uma coleção são esquemas e portanto, não precisará compartilhar a mesma estrutura ou campos. Como os documentos são adicionados a uma coleção de documentos, o Cosmos DB indexa automaticamente-los e eles se tornam disponíveis para serem consultados.

Para fins de desenvolvimento, um banco de dados de documentos também pode ser consumido por meio de um emulador. Usando o emulador, aplicativos podem ser desenvolvidos e testados localmente, sem criar uma assinatura do Azure ou incorrer em custos. Para obter mais informações sobre o emulador, consulte [desenvolver localmente com o emulador do Azure Cosmos DB](/azure/cosmos-db/local-emulator/).

Este artigo e que acompanha o aplicativo de exemplo demonstra um aplicativo de lista de tarefas em que as tarefas são armazenadas em um banco de dados de documentos do Azure Cosmos DB. Para obter mais informações sobre o aplicativo de exemplo, consulte [Noções básicas da amostra](~/xamarin-forms/data-cloud/web-services/introduction.md).

Para obter mais informações sobre o Azure Cosmos DB, consulte a [documentação do Azure Cosmos DB](/azure/cosmos-db/).

## <a name="setup"></a>Configuração

O processo para a integração de um banco de dados de documentos do Azure Cosmos DB em um aplicativo xamarin. Forms é da seguinte maneira:

1. Crie uma conta do Cosmos DB. Para obter mais informações, consulte [criar uma conta do Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#create-an-azure-cosmos-account).
1. Adicione a [biblioteca de cliente do .NET Standard do Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core) pacote do NuGet para os projetos de plataforma da solução do xamarin. Forms.
1. Adicione `using` diretivas para o `Microsoft.Azure.Documents`, `Microsoft.Azure.Documents.Client`, e `Microsoft.Azure.Documents.Linq` namespaces para classes que acessarão a conta do Cosmos DB.

Depois de executar essas etapas, a biblioteca de cliente .NET Standard do Azure Cosmos DB pode ser usada para configurar e executar solicitações no banco de dados de documentos.

> [!NOTE]
> A biblioteca de cliente .NET Standard do Azure Cosmos DB só pode ser instalada em projetos de plataforma e não em um projeto de biblioteca de classe portátil (PCL). Portanto, o aplicativo de exemplo é um projeto de acesso compartilhado (SAP) para evitar a duplicação de código. No entanto, o `DependencyService` classe pode ser usada em um projeto PCL para invocar o código da biblioteca cliente .NET Standard do Azure Cosmos DB contido em projetos específicos da plataforma.

## <a name="consuming-the-azure-cosmos-db-account"></a>Consumo de conta do Azure Cosmos DB

O `DocumentClient` tipo encapsula o ponto de extremidade, credenciais e política de conexão usada para acessar a conta do Azure Cosmos DB e é usado para configurar e executar solicitações em relação a conta. O exemplo de código a seguir demonstra como criar uma instância dessa classe:

```csharp
DocumentClient client = new DocumentClient(new Uri(Constants.EndpointUri), Constants.PrimaryKey);
```

O Uri do Cosmos DB e a chave primária devem ser fornecidas para o `DocumentClient` construtor. Eles podem ser obtidos do Portal do Azure. Para obter mais informações, consulte [conectar-se a uma conta do Azure Cosmos DB](/azure/cosmos-db/sql-api-dotnetcore-get-started#Connect).

### <a name="creating-a-database"></a>Criando um banco de dados

Um banco de dados do documento é um contêiner lógico para os usuários e coleções de documento e pode ser criado no Portal do Azure ou programaticamente usando o `DocumentClient.CreateDatabaseIfNotExistsAsync` método:

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

O `CreateDatabaseIfNotExistsAsync` método Especifica um `Database` do objeto como um argumento, com o `Database` objeto que especifica o nome do banco de dados como seu `Id` propriedade. O `CreateDatabaseIfNotExistsAsync` método cria o banco de dados se ele não existir ou retorna o banco de dados se ele já existe. No entanto, o aplicativo de exemplo ignora todos os dados retornados pelo `CreateDatabaseIfNotExistsAsync` método.

> [!NOTE]
> O `CreateDatabaseIfNotExistsAsync` método retorna um `Task<ResourceResponse<Database>>` objeto e o código de status da resposta podem ser verificada para determinar se um banco de dados foi criado ou um banco de dados foi retornado.

### <a name="creating-a-document-collection"></a>Criando uma coleção de documentos

Uma coleção de documentos é um contêiner para documentos JSON e pode ser criado no Portal do Azure ou programaticamente usando o `DocumentClient.CreateDocumentCollectionIfNotExistsAsync` método:

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

O `CreateDocumentCollectionIfNotExistsAsync` método requer dois argumentos compulsórios – um nome de banco de dados especificado como uma `Uri`e um `DocumentCollection` objeto. O `DocumentCollection` objeto representa uma coleção de documentos cujo nome é especificado com o `Id` propriedade. O `CreateDocumentCollectionIfNotExistsAsync` método cria a coleção de documentos, se ele não existir ou retorna a coleção de documentos, se ele já existe. No entanto, o aplicativo de exemplo ignora todos os dados retornados pelo `CreateDocumentCollectionIfNotExistsAsync` método.

> [!NOTE]
> O `CreateDocumentCollectionIfNotExistsAsync` método retorna um `Task<ResourceResponse<DocumentCollection>>` objeto e o código de status da resposta podem ser verificada para determinar se uma coleção de documentos foi criada ou uma coleção de documento existente foi retornada.

Opcionalmente, o `CreateDocumentCollectionIfNotExistsAsync` método também pode especificar um `RequestOptions` objeto, que encapsula as opções que podem ser especificadas para solicitações emitidas para a conta do Cosmos DB. O `RequestOptions.OfferThroughput` propriedade é usada para definir o nível de desempenho da coleção de documentos e o exemplo de aplicativo, é definido como 400 unidades de solicitação por segundo. Esse valor deve seja aumentado ou diminuído, dependendo se a coleção serão com frequência ou raramente acessada.

> [!IMPORTANT]
> Observe que o `CreateDocumentCollectionIfNotExistsAsync` método criará uma nova coleção com uma taxa de transferência reservada, que tem implicações de preço.

<a name="document_query" />

### <a name="retrieving-document-collection-documents"></a>Recuperando documentos da coleção do documento

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

Essa consulta assíncrona recupera todos os documentos da coleção especificada e coloca os documentos em um `List<TodoItem>` coleção para exibição.

O `CreateDocumentQuery<T>` método Especifica um `Uri` argumento que representa a coleção que deve ser consultada para documentos. Neste exemplo, o `collectionLink` variável é um campo de nível de classe que especifica o `Uri` que representa a coleção de documentos para recuperar os documentos a partir de:

```csharp
Uri collectionLink = UriFactory.CreateDocumentCollectionUri(Constants.DatabaseName, Constants.CollectionName);
```

O `CreateDocumentQuery<T>` método cria uma consulta que é executada de forma síncrona e retorna um `IQueryable<T>` objeto. No entanto, o `AsDocumentQuery` método converte os `IQueryable<T>` do objeto para um `IDocumentQuery<T>` objeto que pode ser executado de forma assíncrona. A consulta assíncrona é executada com o `IDocumentQuery<T>.ExecuteNextAsync` método, que recupera a próxima página de resultados de banco de dados de documento, com o `IDocumentQuery<T>.HasMoreResults` propriedade que indica se há resultados adicionais a serem retornados da consulta.

Os documentos podem ser filtradas do lado do servidor, incluindo um `Where` cláusula na consulta, que aplica um predicado de filtragem à consulta na coleção de documentos:

```csharp
var query = client.CreateDocumentQuery<TodoItem>(collectionLink)
          .Where(f => f.Done != true)
          .AsDocumentQuery();
```

Essa consulta recupera todos os documentos da coleção cuja `Done` propriedade é igual a `false`.

<a name="inserting_document" />

### <a name="inserting-a-document-into-a-document-collection"></a>Inserindo um documento em uma coleção de documentos

Documentos são conteúdo JSON de definidas pelo usuário e podem ser inseridos em uma coleção de documentos com o `DocumentClient.CreateDocumentAsync` método:

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.CreateDocumentAsync(collectionLink, item);
  ...
}
```

O `CreateDocumentAsync` método Especifica um `Uri` argumento que representa a coleção em que o documento deve ser inserido no, e um `object` argumento que representa o documento a ser inserido.

### <a name="replacing-a-document-in-a-document-collection"></a>Substituição de um documento em uma coleção de documentos

Documentos podem ser substituídos em uma coleção de documentos com o `DocumentClient.ReplaceDocumentAsync` método:

```csharp
public async Task SaveTodoItemAsync(TodoItem item, bool isNewItem = false)
{
  ...
  await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, item.Id), item);
  ...
}
```

O `ReplaceDocumentAsync` método Especifica um `Uri` argumento que representa o documento na coleção que deve ser substituído, e um `object` argumento que representa os dados de documento atualizado.

<a name="deleting_document" />

### <a name="deleting-a-document-from-a-document-collection"></a>Excluir um documento de uma coleção de documentos

Um documento pode ser excluído de uma coleção de documentos com o `DocumentClient.DeleteDocumentAsync` método:

```csharp
public async Task DeleteTodoItemAsync(string id)
{
  ...
  await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(Constants.DatabaseName, Constants.CollectionName, id));
  ...
}
```

O `DeleteDocumentAsync` método Especifica um `Uri` argumento que representa o documento na coleção que deve ser excluído.

### <a name="deleting-a-document-collection"></a>Excluindo uma coleção de documentos

Uma coleção de documentos pode ser excluída do banco de dados com o `DocumentClient.DeleteDocumentCollectionAsync` método:

```csharp
await client.DeleteDocumentCollectionAsync(collectionLink);
```

O `DeleteDocumentCollectionAsync` método Especifica um `Uri` argumento que representa a coleção de documento a ser excluído. Observe que a invocar esse método também excluirá os documentos armazenados na coleção.

### <a name="deleting-a-database"></a>Excluir um banco de dados

Um banco de dados pode ser excluído de uma conta de banco de dados do Cosmos DB com o `DocumentClient.DeleteDatabaesAsync` método:

```csharp
await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(Constants.DatabaseName));
```

O `DeleteDatabaseAsync` método Especifica um `Uri` argumento que representa o banco de dados a ser excluído. Observe que invocar esse método também excluirá as coleções de documentos armazenadas no banco de dados e os documentos armazenados nas coleções de documento.

## <a name="summary"></a>Resumo

Este artigo explicou como usar a biblioteca de cliente .NET Standard do Azure Cosmos DB para integrar um banco de dados de documentos do Azure Cosmos DB em um aplicativo xamarin. Forms. Um banco de dados de documentos do Azure Cosmos DB é um banco de dados NoSQL que fornece acesso de baixa latência para documentos JSON, oferecendo um serviço de banco de dados rápida, altamente disponível e escalonável para aplicativos que exigem dimensionamento perfeito e replicação global.


## <a name="related-links"></a>Links relacionados

- [Todo Azure Cosmos DB (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoDocumentDB/)
- [Documentação do Azure Cosmos DB](/azure/cosmos-db/)
- [Biblioteca de cliente do Azure Cosmos DB .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core)
- [API do Azure Cosmos DB](https://docs.microsoft.com/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)
