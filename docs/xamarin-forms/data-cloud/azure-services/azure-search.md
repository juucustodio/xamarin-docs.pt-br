---
title: Pesquisar dados com Azure Search eXamarin.Forms
description: Este artigo demonstra como usar a biblioteca de pesquisa Microsoft Azure para integrar o Azure Search em um Xamarin.Forms aplicativo.
ms.prod: xamarin
ms.assetid: A4AEF233-3672-4174-9DBA-15BEE3030C0B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/05/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 33675fbb644c5967726fee29b3e235247aec633a
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84565351"
---
# <a name="search-data-with-azure-search-and-xamarinforms"></a>Pesquisar dados com Azure Search eXamarin.Forms

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azuresearch)

_Azure Search é um serviço de nuvem que fornece recursos de indexação e consulta para dados carregados. Isso remove os requisitos de infraestrutura e as complexidades de algoritmos de pesquisa tradicionalmente associadas à implementação da funcionalidade de pesquisa em um aplicativo. Este artigo demonstra como usar a biblioteca de pesquisa Microsoft Azure para integrar o Azure Search em um Xamarin.Forms aplicativo._

## <a name="overview"></a>Visão geral

Os dados são armazenados em Azure Search como índices e documentos. Um *índice* é um repositório de dados que podem ser pesquisados pelo serviço de Azure Search e é conceitualmente semelhante a uma tabela de banco de dado. Um *documento* é uma única unidade de dados pesquisáveis em um índice e é conceitualmente semelhante a uma linha de banco de dado. Ao carregar documentos e enviar consultas de pesquisa para Azure Search, as solicitações são feitas a um índice específico no serviço de pesquisa.

Cada solicitação feita para Azure Search deve incluir o nome do serviço e uma chave de API. Há dois tipos de chave de API:

- *As chaves de administração* concedem direitos totais a todas as operações. Isso inclui o gerenciamento do serviço, a criação e exclusão de índices e fontes de dados.
- *As chaves de consulta* concedem acesso somente leitura a índices e documentos e devem ser usadas por aplicativos que emitem solicitações de pesquisa.

A solicitação mais comum para Azure Search é executar uma consulta. Há dois tipos de consulta que podem ser enviados:

- Uma consulta de *pesquisa* procura um ou mais itens em todos os campos pesquisáveis em um índice. As consultas de pesquisa são criadas usando a sintaxe simplificada ou a sintaxe de consulta Lucene. Para obter mais informações, consulte [sintaxe de consulta simples em Azure Search](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search/)e [sintaxe de consulta Lucene em Azure Search](/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search/).
- Uma consulta de *filtro* avalia uma expressão booliana em todos os campos filtráveis em um índice. As consultas de filtro são criadas usando um subconjunto da linguagem de filtro OData. Para obter mais informações, consulte [sintaxe de expressão OData para Azure Search](/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search/).

Consultas de pesquisa e consultas de filtro podem ser usadas separadamente ou juntas. Quando usadas em conjunto, a consulta de filtro é aplicada primeiro ao índice inteiro e, em seguida, a consulta de pesquisa é executada nos resultados da consulta de filtro.

Azure Search também dá suporte à recuperação de sugestões com base na entrada de pesquisa. Para obter mais informações, consulte [sugestões de consultas](#suggestion-queries).

> [!NOTE]
> Se você não tiver uma [assinatura do Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), crie uma [conta gratuita](https://aka.ms/azfree-docs-mobileapps) antes de começar.

## <a name="setup"></a>Instalação

O processo de integração de Azure Search em um Xamarin.Forms aplicativo é o seguinte:

1. Crie um serviço de Azure Search. Para obter mais informações, consulte [criar um serviço de Azure Search usando o portal do Azure](/azure/search/search-create-service-portal/).
1. Remova o Silverlight como uma estrutura de destino da Xamarin.Forms solução PCL (biblioteca de classes portátil). Isso pode ser feito alterando o perfil PCL para qualquer perfil que dê suporte ao desenvolvimento de plataforma cruzada, mas não dá suporte ao Silverlight, como o perfil 151 ou o perfil 92.
1. Adicione o pacote NuGet da [biblioteca de pesquisa Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Search) ao projeto PCL na Xamarin.Forms solução.

Depois de executar essas etapas, a API da biblioteca do Microsoft Search pode ser usada para gerenciar índices de pesquisa e fontes de dados, carregar e gerenciar documentos e executar consultas.

## <a name="creating-the-azure-search-index"></a>Criando um índice do Azure Search

Um esquema de índice deve ser definido para ser mapeado para a estrutura dos dados a serem pesquisados. Isso pode ser feito no portal do Azure ou por meio de programação usando a `SearchServiceClient` classe. Essa classe gerencia conexões para Azure Search e pode ser usada para criar um índice. O exemplo de código a seguir demonstra como criar uma instância dessa classe:

```csharp
var searchClient =
  new SearchServiceClient(Constants.SearchServiceName, new SearchCredentials(Constants.AdminApiKey));
```

A `SearchServiceClient` sobrecarga do construtor usa um nome de serviço de pesquisa e um `SearchCredentials` objeto como argumentos, com o `SearchCredentials` objeto que encapsula a *chave de administração* para o serviço de Azure Search. A *chave de administração* é necessária para criar um índice.

> [!NOTE]
> Uma única `SearchServiceClient` instância deve ser usada em um aplicativo para evitar a abertura de muitas conexões com Azure Search.

Um índice é definido pelo `Index` objeto, conforme demonstrado no exemplo de código a seguir:

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

A `Index.Name` propriedade deve ser definida como o nome do índice e a `Index.Fields` propriedade deve ser definida como uma matriz de `Field` objetos. Cada `Field` instância especifica um nome, um tipo e qualquer propriedade, que especifica como o campo é usado. Essas propriedades incluem:

- `IsKey`– indica se o campo é a chave do índice. Somente um campo no índice, do tipo `DataType.String` , deve ser designado como o campo de chave.
- `IsFacetable`– indica se é possível executar a navegação facetada nesse campo. O valor padrão é `false`.
- `IsFilterable`– indica se o campo pode ser usado em consultas de filtro. O valor padrão é `false`.
- `IsRetrievable`– indica se o campo pode ser recuperado nos resultados da pesquisa. O valor padrão é `true`.
- `IsSearchable`– indica se o campo está incluído em pesquisas de texto completo. O valor padrão é `false`.
- `IsSortable`– indica se o campo pode ser usado em `OrderBy` expressões. O valor padrão é `false`.

> [!NOTE]
> A alteração de um índice após sua implantação envolve a recriação e o recarregamento dos dados.

Um `Index` objeto pode opcionalmente especificar uma `Suggesters` propriedade, que define os campos no índice a serem usados para dar suporte a consultas preenchimento automático ou de sugestão de pesquisa. A `Suggesters` propriedade deve ser definida como uma matriz de `Suggester` objetos que definem os campos que são usados para criar os resultados da sugestão de pesquisa.

Depois de criar o `Index` objeto, o índice é criado chamando `Indexes.Create` na `SearchServiceClient` instância.

> [!NOTE]
> Ao criar um índice de um aplicativo que deve ser mantido responsivo, use o `Indexes.CreateAsync` método.

Para obter mais informações, consulte [criar um índice de Azure Search usando o SDK do .net](/azure/search/search-create-index-dotnet/).

## <a name="deleting-the-azure-search-index"></a>Excluindo o índice de Azure Search

Um índice pode ser excluído chamando `Indexes.Delete` na `SearchServiceClient` instância:

```csharp
searchClient.Indexes.Delete(Constants.Index);
```

## <a name="uploading-data-to-the-azure-search-index"></a>Carregando dados para o índice de Azure Search

Depois de definir o índice, os dados podem ser carregados para ele usando um dos dois modelos:

- **Modelo de pull** – os dados são ingeridos periodicamente a partir de Azure Cosmos DB, do Azure SQL Database, do armazenamento de BLOBs do Azure ou de SQL Server hospedados em uma máquina virtual do Azure.
- **Modelo de push** – os dados são enviados programaticamente para o índice. Este é o modelo adotado neste artigo.

Uma `SearchIndexClient` instância deve ser criada para importar dados para o índice. Isso pode ser feito chamando o `SearchServiceClient.Indexes.GetClient` método, conforme demonstrado no exemplo de código a seguir:

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

Os dados a serem importados para o índice são empacotados como um `IndexBatch` objeto, que encapsula uma coleção de `IndexAction` objetos. Cada `IndexAction` instância contém um documento e uma propriedade que informa Azure Search qual ação executar no documento. No exemplo de código acima, a `IndexAction.Upload` ação é especificada, o que faz com que o documento seja inserido no índice, se for novo, ou substituído se já existir. O `IndexBatch` objeto é então enviado para o índice chamando o `Documents.Index` método no `SearchIndexClient` objeto. Para obter informações sobre outras ações de indexação, consulte [decidir qual ação de indexação usar](/azure/search/search-import-data-dotnet#decide-which-indexing-action-to-use).

> [!NOTE]
> Somente 1000 documentos podem ser incluídos em uma única solicitação de indexação.

Observe que no exemplo de código acima, a `monkeyList` coleção é criada como um objeto anônimo de uma coleção de `Monkey` objetos. Isso cria dados para o `id` campo e resolve o mapeamento dos nomes de propriedade de caso do Pascal `Monkey` para os nomes de campo do índice de pesquisa do camel case. Como alternativa, esse mapeamento também pode ser feito adicionando o `[SerializePropertyNamesAsCamelCase]` atributo à `Monkey` classe.

Para obter mais informações, consulte [carregar dados para Azure Search usando o SDK do .net](/azure/search/search-import-data-dotnet/).

## <a name="querying-the-azure-search-index"></a>Consultando o índice de Azure Search

Uma `SearchIndexClient` instância deve ser criada para consultar um índice. Quando um aplicativo executa consultas, é aconselhável seguir o princípio de privilégios mínimos e criar um `SearchIndexClient` diretamente, passando a *chave de consulta* como um argumento. Isso garante que os usuários tenham acesso somente leitura a índices e documentos. Essa abordagem é demonstrada no exemplo de código a seguir:

```csharp
SearchIndexClient indexClient =
  new SearchIndexClient(Constants.SearchServiceName, Constants.Index, new SearchCredentials(Constants.QueryApiKey));
```

A `SearchIndexClient` sobrecarga do construtor usa um nome de serviço de pesquisa, um nome de índice e um `SearchCredentials` objeto como argumentos, com o `SearchCredentials` objeto que encapsula a *chave de consulta* para o serviço de Azure Search.

### <a name="search-queries"></a>Consultas de pesquisa

O índice pode ser consultado chamando o `Documents.SearchAsync` método na `SearchIndexClient` instância, conforme demonstrado no exemplo de código a seguir:

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

O `SearchAsync` método usa um argumento de texto de pesquisa e um `SearchParameters` objeto opcional que pode ser usado para refinar ainda mais a consulta. Uma consulta de pesquisa é especificada como o argumento de texto de pesquisa, enquanto uma consulta de filtro pode ser especificada definindo a `Filter` Propriedade do `SearchParameters` argumento. O exemplo de código a seguir demonstra os dois tipos de consulta:

```csharp
var parameters = new SearchParameters
{
  Filter = "location ne 'China' and location ne 'Vietnam'"
};
var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text, parameters);
```

Essa consulta de filtro é aplicada a todo o índice e remove documentos dos resultados em que o `location` campo não é igual à China e não é igual a Vietnã. Após a filtragem, a consulta de pesquisa é executada nos resultados da consulta de filtro.

> [!NOTE]
> Para filtrar sem Pesquisar, passe `*` como o argumento de texto de pesquisa.

O `SearchAsync` método retorna um `DocumentSearchResult` objeto que contém os resultados da consulta. Esse objeto é enumerado, com cada `Document` objeto que está sendo criado como um `Monkey` objeto e adicionado ao `Monkeys` `ObservableCollection` para exibição. As capturas de tela a seguir mostram os resultados da consulta de pesquisa retornados de Azure Search:

![](azure-search-images/search.png "Search Results")

Para obter mais informações sobre como Pesquisar e filtrar, consulte [consultar seu índice de Azure Search usando o SDK do .net](/azure/search/search-query-dotnet/).

### <a name="suggestion-queries"></a>Consultas de sugestão

Azure Search permite que sugestões sejam solicitadas com base em uma consulta de pesquisa, chamando o `Documents.SuggestAsync` método na `SearchIndexClient` instância. Isso é demonstrado no exemplo de código a seguir:

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

O `SuggestAsync` método usa um argumento de texto de pesquisa, o nome do Sugestor a ser usado (que é definido no índice) e um `SuggestParameters` objeto opcional que pode ser usado para refinar ainda mais a consulta. A `SuggestParameters` instância define as seguintes propriedades:

- `UseFuzzyMatching`– Quando definido como `true` , Azure Search encontrará sugestões mesmo se houver um caractere substituído ou ausente no texto de pesquisa.
- `HighlightPreTag`– a marca que é precedida para ocorrências de sugestões.
- `HighlightPostTag`– a marca que é acrescentada às ocorrências de sugestão.
- `MinimumCoverage`– representa a porcentagem do índice que deve ser coberta por uma consulta de sugestão para que a consulta seja relatada como êxito. O padrão é 80.
- `Top`– o número de sugestões a serem recuperadas. Ele deve ser um inteiro entre 1 e 100, com um valor padrão de 5.

O efeito geral é que os 10 principais resultados do índice serão retornados com realce de clique e os resultados incluirão documentos que incluem termos de pesquisa escritos de forma semelhante.

O `SuggestAsync` método retorna um `DocumentSuggestResult` objeto que contém os resultados da consulta. Esse objeto é enumerado, com cada `Document` objeto que está sendo criado como um `Monkey` objeto e adicionado ao `Monkeys` `ObservableCollection` para exibição. As capturas de tela a seguir mostram os resultados de sugestão retornados de Azure Search:

![](azure-search-images/suggest.png "Suggestion Results")

Observe que, no aplicativo de exemplo, o `SuggestAsync` método é invocado somente quando o usuário termina de inserir um termo de pesquisa. No entanto, ele também pode ser usado para dar suporte a consultas de pesquisa de preenchimento automático executando em cada pressionamento de tecla.

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar a biblioteca de pesquisa Microsoft Azure para integrar o Azure Search a um Xamarin.Forms aplicativo. Azure Search é um serviço de nuvem que fornece recursos de indexação e consulta para dados carregados. Isso remove os requisitos de infraestrutura e as complexidades de algoritmos de pesquisa tradicionalmente associadas à implementação da funcionalidade de pesquisa em um aplicativo.

## <a name="related-links"></a>Links relacionados

- [Azure Search (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azuresearch)
- [Documentação do Azure Search](/azure/search/)
- [Microsoft Azure biblioteca de pesquisa](https://www.nuget.org/packages/Microsoft.Azure.Search/)
