---
title: Pesquisando dados com a pesquisa do Azure
description: Pesquisa do Azure é um serviço de nuvem que fornece a indexação e consulta de recursos para os dados carregados. Isso remove os requisitos de infraestrutura e as complexidades de algoritmo de pesquisa normalmente associadas ao implementar a funcionalidade de pesquisa em um aplicativo. Este artigo demonstra como usar a biblioteca de pesquisa do Microsoft Azure para integrar a pesquisa do Azure em um aplicativo xamarin. Forms.
ms.topic: article
ms.prod: xamarin
ms.assetid: A4AEF233-3672-4174-9DBA-15BEE3030C0B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/05/2016
ms.openlocfilehash: 24db1404e218eea86356f9bbc004e7d5850c2e7a
ms.sourcegitcommit: 7b76c3d761b3ffb49541e2e2bcf292de6587c4e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="searching-data-with-azure-search"></a>Pesquisando dados com a pesquisa do Azure

_Pesquisa do Azure é um serviço de nuvem que fornece a indexação e consulta de recursos para os dados carregados. Isso remove os requisitos de infraestrutura e as complexidades de algoritmo de pesquisa normalmente associadas ao implementar a funcionalidade de pesquisa em um aplicativo. Este artigo demonstra como usar a biblioteca de pesquisa do Microsoft Azure para integrar a pesquisa do Azure em um aplicativo xamarin. Forms._

## <a name="overview"></a>Visão geral

Dados são armazenados na pesquisa do Azure, como índices e documentos. Um *índice* é um repositório de dados que podem ser pesquisados pelo serviço de pesquisa do Azure e é conceitualmente semelhante a uma tabela de banco de dados. Um *documento* é uma única unidade de dados de pesquisados em um índice e é conceitualmente semelhante a uma linha de banco de dados. Quando o carregamento de documentos e envio de consultas de pesquisa para pesquisa do Azure, são feitas solicitações para um índice específico em que o serviço de pesquisa.

Cada solicitação feita para pesquisa do Azure deve incluir o nome do serviço e uma chave de API. Há dois tipos de chave de API:

- *Chaves de administração* conceder direitos totais para todas as operações. Isso inclui o gerenciamento do serviço, criar e excluir fontes de dados e índices.
- *Chaves de consulta* conceder acesso somente leitura a índices e documentos e deve ser usado por aplicativos que emitem solicitações de pesquisa.

É o mais comum de solicitação de pesquisa do Azure para executar uma consulta. Há dois tipos de consulta que podem ser enviadas:

- Um *pesquisa* consulta procura por um ou mais itens em todos os campos de pesquisa em um índice. Consultas de pesquisa são criadas usando a sintaxe simplificada, ou a sintaxe de consulta do Lucene. Para obter mais informações, consulte [sintaxe de consulta simples na pesquisa do Azure](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search/), e [Lucene sintaxe de consulta na pesquisa do Azure](/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search/).
- Um *filtro* consulta avalia uma expressão booleana em todos os campos podem ser filtrados em um índice. Consultas de filtro são criadas usando um subconjunto da linguagem de filtro de OData. Para obter mais informações, consulte [sintaxe de expressão do OData para pesquisa do Azure](/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search/).

Consultas de pesquisa e filtro de consulta pode ser usados separadamente ou em conjunto. Quando usados juntos, a consulta de filtro é aplicada primeiro ao índice inteiro e, em seguida, a consulta de pesquisa é executada nos resultados da consulta de filtro.

A pesquisa do Azure também oferece suporte a recuperar sugestões com base na entrada de pesquisa. Para obter mais informações, consulte [consultas de sugestão](#suggestions).

## <a name="setup"></a>Configuração

O processo para a integração de pesquisa do Azure em um aplicativo xamarin. Forms é da seguinte maneira:

1. Crie um serviço de pesquisa do Azure. Para obter mais informações, consulte [criar um serviço de pesquisa do Azure usando o Portal do Azure](/azure/search/search-create-service-portal/).
1. Remova o Silverlight como uma estrutura de destino da biblioteca de classe portátil (PCL) de solução de xamarin. Forms. Isso pode ser feito alterando o perfil PCL a qualquer perfil que dá suporte ao desenvolvimento de plataforma cruzada, mas não oferece suporte para o Silverlight, como perfil 151 ou 92.
1. Adicionar o [biblioteca de pesquisa do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Search) pacote NuGet para o projeto PCL na solução xamarin. Forms.

Depois de executar essas etapas, a API de biblioteca do Microsoft Search pode ser usada para gerenciar fontes de dados e índices de pesquisa, carregar e gerenciar documentos e executar consultas.

## <a name="creating-the-azure-search-index"></a>Criar o índice de pesquisa do Azure

Um esquema de índice deve ser definido que mapeia para a estrutura de dados a ser pesquisada. Isso pode ser realizado no Portal do Azure, ou programaticamente usando o `SearchServiceClient` classe. Essa classe gerencia conexões com a pesquisa do Azure e pode ser usada para criar um índice. O exemplo de código a seguir demonstra como criar uma instância desta classe:

```csharp
var searchClient =
  new SearchServiceClient(Constants.SearchServiceName, new SearchCredentials(Constants.AdminApiKey));
```

O `SearchServiceClient` sobrecarga de construtor aceita um nome de serviço de pesquisa e uma `SearchCredentials` do objeto como argumentos, com o `SearchCredentials` encapsulamento de objeto de *chave de administração* para o serviço de pesquisa do Azure. O *chave admin* é necessário para criar um índice.

> [!NOTE]
>  Um único `SearchServiceClient` instância deve ser usada em um aplicativo para evitar abrir muitas conexões para a pesquisa do Azure.

Um índice é definido pelo `Index` do objeto, como demonstrado no exemplo de código a seguir:

```csharp
static void CreateSearchIndex()
{
  var index = new Index()
  {
    Name = Constants.Index,
    Fields = new[]
    {
      new Field("id", DataType.String) { IsKey = true, IsRetrievable = true },
      new Field("name", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSortable = true, IsSearchable = true },
      new Field("location", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSortable = true, IsSearchable = true },
      new Field("details", DataType.String) { IsRetrievable = true, IsFilterable = true, IsSearchable = true },
      new Field("imageUrl", DataType.String) { IsRetrievable = true }
    },
    Suggesters = new[]
    {
      new Suggester("nameSuggester", SuggesterSearchMode.AnalyzingInfixMatching, new[] { "name" })
    }
  };

  searchClient.Indexes.Create(index);
}
```

O `Index.Name` propriedade deve ser definida como o nome do índice e o `Index.Fields` propriedade deve ser definida como uma matriz de `Field` objetos. Cada `Field` instância Especifica um nome, um tipo e as propriedades, que especificam como o campo é usado. Essas propriedades incluem:

- `IsKey` – Indica se o campo é a chave do índice. Apenas um campo no índice, do tipo `DataType.String`, deve ser designado como o campo de chave.
- `IsFacetable` – Indica se é possível executar a navegação facetada neste campo. O valor padrão é `false`.
- `IsFilterable` – Indica se o campo pode ser usado em consultas de filtro. O valor padrão é `false`.
- `IsRetrievable` – Indica se o campo pode ser recuperado nos resultados da pesquisa. O valor padrão é `true`.
- `IsSearchable` – Indica se o campo é incluído na pesquisa de texto completo. O valor padrão é `false`.
- `IsSortable` – Indica se o campo pode ser usado em `OrderBy` expressões. O valor padrão é `false`.

> [!NOTE]
> Alterar um índice depois de ser implantado envolve a recompilação e recarregar os dados.

Um `Index` objeto pode especificar opcionalmente um `Suggesters` propriedade, que define os campos no índice a ser usado para suporte de preenchimento automático ou consultas de sugestões de pesquisa. O `Suggesters` propriedade deve ser definida como uma matriz de `Suggester` objetos que definem os campos que são usados para construir a pesquisa resultados de sugestão.

Depois de criar o `Index` do objeto, o índice é criado chamando `Indexes.Create` no `SearchServiceClient` instância.

> [!NOTE]
> Ao criar um índice de um aplicativo que deve ser mantida em resposta, use o `Indexes.CreateAsync` método.

Para obter mais informações, consulte [criar um índice de pesquisa do Azure usando o SDK do .NET](/azure/search/search-create-index-dotnet/).

## <a name="deleting-the-azure-search-index"></a>A exclusão do índice de pesquisa do Azure

Um índice pode ser excluído chamando `Indexes.Delete` no `SearchServiceClient` instância:

```csharp
searchClient.Indexes.Delete(Constants.Index);
```

## <a name="uploading-data-to-the-azure-search-index"></a>Carregando dados para o índice de pesquisa do Azure

Depois de definir o índice, é possível carregar os dados usando um dos dois modelos:

- **Modelo de pull** – dados periodicamente é incluídos do banco de dados do Azure Cosmos, banco de dados do SQL Azure, armazenamento de BLOBs do Azure ou SQL Server hospedado em uma máquina Virtual do Azure.
- **Modelo de push** – programaticamente os dados são enviados para o índice. Este é o modelo adotado neste artigo.

Um `SearchIndexClient` instância deve ser criada para importar dados para o índice. Isso pode ser feito usando o `SearchServiceClient.Indexes.GetClient` método, conforme demonstrado no exemplo de código a seguir:

```csharp
static void UploadDataToSearchIndex()
{
  var indexClient = searchClient.Indexes.GetClient(Constants.Index);

  var monkeyList = MonkeyData.Monkeys.Select(m => new
  {
    id = Guid.NewGuid().ToString(),
    name = m.Name,
    location = m.Location,
    details = m.Details,
    imageUrl = m.ImageUrl
  });

  var batch = IndexBatch.New(monkeyList.Select(IndexAction.Upload));
  try
  {
    indexClient.Documents.Index(batch);
  }
  catch (IndexBatchException ex)
  {
    // Sometimes when the Search service is under load, indexing will fail for some
    // documents in the batch. Compensating actions like delaying and retrying should be taken.
    // Here, the failed document keys are logged.
    Console.WriteLine("Failed to index some documents: {0}",
      string.Join(", ", ex.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
  }
}
```

Dados a serem importados para o índice são empacotados como um `IndexBatch` objeto, que encapsula uma coleção de `IndexAction` objetos. Cada `IndexAction` instância contém um documento e uma propriedade que indica qual ação executar no documento de pesquisa do Azure. No exemplo de código acima, o `IndexAction.Upload` ação for especificada, o que resulta no documento que está sendo inserida no índice, se for novo, ou substituída se ele já existe. O `IndexBatch` objeto é enviado para o índice chamando o `Documents.Index` método o `SearchIndexClient` objeto. Para obter informações sobre outras ações de indexação, consulte [decidir qual ação indexação usar](/azure/search/search-import-data-dotnet#ii-decide-which-indexing-action-to-use).

> [!NOTE]
> Somente 1000 documentos podem ser incluídos em uma única solicitação de indexação.

Observe que no exemplo de código acima, o `monkeyList` coleta é criada como um objeto anônimo de uma coleção de `Monkey` objetos. Isso cria dados para o `id` campo e resolve o mapeamento do caso de Pascal `Monkey` nomes de campo de índice de pesquisa de nomes de propriedade para concatenação com maiusculas. Como alternativa, esse mapeamento também pode ser feito adicionando o `[SerializePropertyNamesAsCamelCase]` de atributo para o `Monkey` classe.

Para obter mais informações, consulte [carregar dados para pesquisa do Azure usando o SDK do .NET](/azure/search/search-import-data-dotnet/).

## <a name="querying-the-azure-search-index"></a>Consultar o índice de pesquisa do Azure

Um `SearchIndexClient` instância deve ser criada para consultar um índice. Quando um aplicativo executa consultas, é recomendável seguir o princípio de menos privilégios e criar um `SearchIndexClient` diretamente, passando o *chave de consulta* como um argumento. Isso garante que os usuários tenham acesso somente leitura a índices e documentos. Essa abordagem é demonstrada no exemplo de código a seguir:

```csharp
SearchIndexClient indexClient =
  new SearchIndexClient(Constants.SearchServiceName, Constants.Index, new SearchCredentials(Constants.QueryApiKey));
```

O `SearchIndexClient` sobrecarga de construtor tem um nome de serviço de pesquisa, o nome do índice e um `SearchCredentials` do objeto como argumentos, com o `SearchCredentials` quebra automática de objeto de *chave de consulta* para o serviço de pesquisa do Azure.

### <a name="search-queries"></a>Consultas de pesquisa

O índice pode ser consultado por meio da chamada a `Documents.SearchAsync` método o `SearchIndexClient` da instância, conforme demonstrado no exemplo de código a seguir:

```csharp
async Task AzureSearch(string text)
{
  Monkeys.Clear();

  var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text);
  foreach (SearchResult<Monkey> result in searchResults.Results)
  {
    Monkeys.Add(new Monkey
    {
      Name = result.Document.Name,
      Location = result.Document.Location,
      Details = result.Document.Details,
      ImageUrl = result.Document.ImageUrl
    });
  }
}
```

O `SearchAsync` método usa um argumento de texto de pesquisa e opcional `SearchParameters` objeto que pode ser usado para refinar ainda mais a consulta. Uma consulta de pesquisa é especificada como o argumento do texto de pesquisa, enquanto uma consulta de filtro pode ser especificada definindo a `Filter` propriedade o `SearchParameters` argumento. O exemplo de código a seguir demonstra que os dois tipos de consulta:

```csharp
var parameters = new SearchParameters
{
  Filter = "location ne 'China' and location ne 'Vietnam'"
};
var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text, parameters);
```

Esta consulta de filtro é aplicada ao índice inteiro e remove documentos dos resultados onde o `location` campo não é igual a China e não é igual a Vietnã. Após a filtragem, a consulta de pesquisa é executada nos resultados da consulta de filtro.

> [!NOTE]
> Para filtrar sem pesquisar, passar `*` como o argumento do texto de pesquisa.

O `SearchAsync` método retorna um `DocumentSearchResult` objeto que contém os resultados da consulta. Esse objeto é enumerado, com cada `Document` do objeto que está sendo criado como um `Monkey` do objeto e adicionados ao `Monkeys` `ObservableCollection` para exibição. Os seguintes capturas de tela mostram pesquisa resultados da consulta retornados da pesquisa do Azure:

![](azure-search-images/search.png "Resultados da pesquisa")

Para obter mais informações sobre pesquisa e filtragem, consulte [consultar seu índice de pesquisa do Azure usando o SDK do .NET](/azure/search/search-query-dotnet/).

<a name="suggestions" />

### <a name="suggestion-queries"></a>Consultas de sugestão

A pesquisa do Azure permite sugestões para ser solicitado com base em uma consulta de pesquisa, chamando o `Documents.SuggestAsync` método sobre o `SearchIndexClient` instância. Isso é demonstrado no exemplo de código a seguir:

```csharp
async Task AzureSuggestions(string text)
{
  Suggestions.Clear();

  var parameters = new SuggestParameters()
  {
    UseFuzzyMatching = true,
    HighlightPreTag = "[",
    HighlightPostTag = "]",
    MinimumCoverage = 100,
    Top = 10
  };

  var suggestionResults =
    await indexClient.Documents.SuggestAsync<Monkey>(text, "nameSuggester", parameters);

  foreach (var result in suggestionResults.Results)
  {
    Suggestions.Add(new Monkey
    {
      Name = result.Text,
      Location = result.Document.Location,
      Details = result.Document.Details,
      ImageUrl = result.Document.ImageUrl
    });
  }
}
```

O `SuggestAsync` método aceita um argumento de texto de pesquisa, o nome da sugestão para usar (que é definida no índice), e um opcional `SuggestParameters` objeto que pode ser usado para refinar ainda mais a consulta. O `SuggestParameters` instância define as propriedades a seguir:

- `UseFuzzyMatching` – Quando definido como `true`, pesquisa do Azure encontrará sugestões mesmo se houver um caractere ausente ou substituído no texto da pesquisa.
- `HighlightPreTag` – a marca que é anexada à acertos sugestão.
- `HighlightPostTag` – a marca é acrescentada ao ocorrências de sugestão.
- `MinimumCoverage` – representa a porcentagem do índice que deve ser abordada por uma consulta de sugestão para a consulta a ser relatado sucesso. O padrão é 80.
- `Top` – o número de sugestões a recuperar. Ele deve ser um inteiro entre 1 e 100, com um valor padrão de 5.

O efeito geral é que os 10 primeiros resultados do índice serão retornados com o realce de ocorrências e os resultados incluirão documentos que incluem escritas de maneira similar termos de pesquisa.

O `SuggestAsync` método retorna um `DocumentSuggestResult` objeto que contém os resultados da consulta. Esse objeto é enumerado, com cada `Document` do objeto que está sendo criado como um `Monkey` do objeto e adicionados ao `Monkeys` `ObservableCollection` para exibição. As capturas de tela a seguir mostram os resultados de sugestão retornados da pesquisa do Azure:

![](azure-search-images/suggest.png "Resultados de sugestão")

Observe que o aplicativo de exemplo, o `SuggestAsync` método será chamado apenas quando o usuário termina inserindo um termo de pesquisa. No entanto, ele também pode ser usado para dar suporte a consultas de pesquisa de preenchimento automático, executando em cada pressionamento de tecla.

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar a biblioteca de pesquisa do Microsoft Azure para integrar a pesquisa do Azure em um aplicativo xamarin. Forms. Pesquisa do Azure é um serviço de nuvem que fornece a indexação e consulta de recursos para os dados carregados. Isso remove os requisitos de infraestrutura e as complexidades de algoritmo de pesquisa normalmente associadas ao implementar a funcionalidade de pesquisa em um aplicativo.


## <a name="related-links"></a>Links relacionados

- [Pesquisa do Azure (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureSearch/)
- [Documentação de pesquisa do Azure](/azure/search/)
- [Biblioteca de pesquisa do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Search/)
