---
title: Pesquisa de dados com o Azure Search
description: Este artigo demonstra como usar a biblioteca do Microsoft Azure Search para integrar o Azure Search em um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: A4AEF233-3672-4174-9DBA-15BEE3030C0B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/05/2016
ms.openlocfilehash: 640d50a45b4b7f61113b609fa6a5e4a04566b360
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61326993"
---
# <a name="searching-data-with-azure-search"></a>Pesquisa de dados com o Azure Search

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureSearch/)

_O Azure Search é um serviço de nuvem que fornece a indexação e consulta recursos para os dados carregados. Isso remove as complexidades do algoritmo de pesquisa tradicionalmente associadas ao implementar a funcionalidade de pesquisa em um aplicativo e requisitos de infraestrutura. Este artigo demonstra como usar a biblioteca do Microsoft Azure Search para integrar o Azure Search em um aplicativo xamarin. Forms._

## <a name="overview"></a>Visão geral

Dados são armazenados no Azure Search, como índices e documentos. Uma *índice* é um repositório de dados que podem ser pesquisados pelo serviço de Azure Search e é conceitualmente semelhante a uma tabela de banco de dados. Um *documento* é uma única unidade de dados pesquisáveis em um índice e é conceitualmente semelhante a uma linha do banco de dados. Quando carregar documentos e enviar consultas de pesquisa para o Azure Search, as solicitações são feitas em um índice específico no serviço de pesquisa.

Cada solicitação feita ao Azure Search deve incluir o nome do serviço e uma chave de API. Há dois tipos de chave de API:

- *Chaves de administração* conceder direitos totais para todas as operações. Isso inclui o serviço de gerenciamento, a criação e a exclusão de índices e fontes de dados.
- *Chaves de consulta* conceder acesso somente leitura a índices e documentos e deve ser usado por aplicativos que emitem solicitações de pesquisa.

A solicitação mais comum para o Azure Search é executar uma consulta. Há dois tipos de consulta que pode ser enviado:

- Um *pesquisa* consulta procura por um ou mais itens em todos os campos pesquisáveis em um índice. Consultas de pesquisa são criadas usando a sintaxe simplificada, ou a sintaxe de consulta Lucene. Para obter mais informações, consulte [sintaxe de consulta simples no Azure Search](/rest/api/searchservice/Simple-query-syntax-in-Azure-Search/), e [sintaxe de consulta Lucene no Azure Search](/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search/).
- Um *filtro* consulta avalia uma expressão booliana em todos os campos que podem ser filtrados em um índice. Consultas de filtro são criadas usando um subconjunto da linguagem de filtro OData. Para obter mais informações, consulte [sintaxe de expressão do OData para o Azure Search](/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search/).

Consultas de pesquisa e consultas de filtro podem ser usadas separadamente ou em conjunto. Quando usados juntos, a consulta de filtro é aplicada primeiro ao índice inteiro e, em seguida, a consulta de pesquisa é realizada nos resultados da consulta de filtro.

O Azure Search também dá suporte ao recuperar sugestões com base na entrada de pesquisa. Para obter mais informações, consulte [consultas de sugestão](#suggestions).

## <a name="setup"></a>Configuração

O processo para a integração do Azure Search em um aplicativo xamarin. Forms é da seguinte maneira:

1. Crie um serviço Azure Search. Para obter mais informações, consulte [criar um serviço de Azure Search usando o Portal do Azure](/azure/search/search-create-service-portal/).
1. Remova o Silverlight como uma estrutura de destino da biblioteca de classe portátil (PCL) da solução do xamarin. Forms. Isso pode ser feito alterando o perfil PCL para qualquer perfil que dá suporte ao desenvolvimento de plataforma cruzada, mas não oferece suporte a Silverlight, como perfil 151 ou 92.
1. Adicione a [biblioteca do Microsoft Azure Search](https://www.nuget.org/packages/Microsoft.Azure.Search) pacote NuGet ao projeto PCL na solução xamarin. Forms.

Depois de executar essas etapas, a API de biblioteca do Microsoft Search pode ser usada para gerenciar fontes de dados e índices de pesquisa, carregar e gerenciar documentos e executar consultas.

## <a name="creating-the-azure-search-index"></a>Criar o índice de Azure Search

Um esquema de índice deve ser definido que é mapeado para a estrutura dos dados a ser pesquisado. Isso pode ser realizado no Portal do Azure ou programaticamente usando o `SearchServiceClient` classe. Essa classe gerencia conexões para o Azure Search e pode ser usado para criar um índice. O exemplo de código a seguir demonstra como criar uma instância dessa classe:

```csharp
var searchClient =
  new SearchServiceClient(Constants.SearchServiceName, new SearchCredentials(Constants.AdminApiKey));
```

O `SearchServiceClient` sobrecarga de construtor aceita um nome de serviço de pesquisa e uma `SearchCredentials` como argumentos, o objeto com o `SearchCredentials` disposição do objeto a *chave de administração* para o serviço de Azure Search. O *chave de administração* é necessária para criar um índice.

> [!NOTE]
>  Um único `SearchServiceClient` instância deve ser usada em um aplicativo para evitar abrir um número excessivo de conexões para o Azure Search.

Um índice é definido pelo `Index` do objeto, conforme demonstrado no exemplo de código a seguir:

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

O `Index.Name` propriedade deve ser definida como o nome do índice e o `Index.Fields` propriedade deve ser definida como uma matriz de `Field` objetos. Cada `Field` instância Especifica um nome, um tipo e quaisquer propriedades que especificam como o campo é usado. Essas propriedades incluem:

- `IsKey` – Indica se o campo é a chave do índice. Apenas um campo no índice, do tipo `DataType.String`, deve ser designado como o campo de chave.
- `IsFacetable` – Indica se é possível executar a navegação facetada nesse campo. O valor padrão é `false`.
- `IsFilterable` – Indica se o campo pode ser usado em consultas de filtro. O valor padrão é `false`.
- `IsRetrievable` – Indica se o campo pode ser recuperado nos resultados da pesquisa. O valor padrão é `true`.
- `IsSearchable` – Indica se o campo está incluído em pesquisas de texto completo. O valor padrão é `false`.
- `IsSortable` – Indica se o campo pode ser usado em `OrderBy` expressões. O valor padrão é `false`.

> [!NOTE]
> Alterar um índice após sua implantação envolve a recriação e recarregar os dados.

Uma `Index` objeto pode especificar opcionalmente um `Suggesters` propriedade, que define os campos no índice a ser usado para dar suporte a preenchimento automático ou consultas de sugestão de pesquisa. O `Suggesters` propriedade deve ser definida como uma matriz de `Suggester` objetos que definem os campos que são usados para criar resultados de sugestão de pesquisa.

Depois de criar o `Index` do objeto, o índice é criado chamando `Indexes.Create` sobre o `SearchServiceClient` instância.

> [!NOTE]
> Ao criar um índice de um aplicativo que deve ser mantido responsivo, use o `Indexes.CreateAsync` método.

Para obter mais informações, consulte [criar um índice de Azure Search usando o SDK do .NET](/azure/search/search-create-index-dotnet/).

## <a name="deleting-the-azure-search-index"></a>A exclusão do índice de Azure Search

Um índice pode ser excluído chamando `Indexes.Delete` sobre o `SearchServiceClient` instância:

```csharp
searchClient.Indexes.Delete(Constants.Index);
```

## <a name="uploading-data-to-the-azure-search-index"></a>Carregando dados para o índice de Azure Search

Depois de definir o índice, os dados podem ser carregados usando um destes dois modelos:

- **Modelo de pull** – os dados são ingeridos periodicamente do Azure Cosmos DB, banco de dados SQL, armazenamento de BLOBs do Azure ou SQL Server hospedado em uma máquina Virtual do Azure.
- **Modelo de push** – dados por meio de programação são enviados para o índice. Esse é o modelo adotado neste artigo.

Um `SearchIndexClient` instância deve ser criada para importar dados para o índice. Isso pode ser feito chamando o `SearchServiceClient.Indexes.GetClient` método, conforme demonstrado no exemplo de código a seguir:

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

Dados a serem importados para o índice são empacotados como um `IndexBatch` objeto que encapsula uma coleção de `IndexAction` objetos. Cada `IndexAction` instância contém um documento e uma propriedade que informam ao Azure Search qual ação executar no documento. No exemplo de código acima, o `IndexAction.Upload` ação for especificada, o que resulta no documento que está sendo inserida no índice, se for novo, ou substituída se ele já existe. O `IndexBatch` objeto, em seguida, é enviado para o índice chamando o `Documents.Index` método o `SearchIndexClient` objeto. Para obter informações sobre outras ações de indexação, consulte [decidir qual ação de indexação para usar](/azure/search/search-import-data-dotnet#decide-which-indexing-action-to-use).

> [!NOTE]
> Apenas 1000 documentos podem ser incluídos em uma única solicitação de indexação.

Observe que no exemplo de código acima, o `monkeyList` coleção é criada como um objeto anônimo de uma coleção de `Monkey` objetos. Isso cria os dados para o `id` campo e resolve o mapeamento de Pascal case `Monkey` nomes de campo de índice de pesquisa de nomes de propriedade para concatenação com maiusculas. Como alternativa, esse mapeamento também pode ser feito adicionando o `[SerializePropertyNamesAsCamelCase]` de atributo para o `Monkey` classe.

Para obter mais informações, consulte [carregar dados no Azure Search usando o SDK do .NET](/azure/search/search-import-data-dotnet/).

## <a name="querying-the-azure-search-index"></a>Consultar o índice de Azure Search

Um `SearchIndexClient` instância deve ser criada para consultar um índice. Quando um aplicativo executa consultas, é recomendável seguir o princípio de privilégios mínimos e criar uma `SearchIndexClient` diretamente, passando a *chave de consulta* como um argumento. Isso garante que os usuários tenham acesso somente leitura a índices e documentos. Essa abordagem é demonstrada no exemplo de código a seguir:

```csharp
SearchIndexClient indexClient =
  new SearchIndexClient(Constants.SearchServiceName, Constants.Index, new SearchCredentials(Constants.QueryApiKey));
```

O `SearchIndexClient` sobrecarga de construtor usa um nome de serviço de pesquisa, o nome do índice e um `SearchCredentials` como argumentos, o objeto com o `SearchCredentials` disposição do objeto a *chave de consulta* para o serviço de Azure Search.

### <a name="search-queries"></a>Consultas de pesquisa

O índice pode ser consultado por meio da chamada a `Documents.SearchAsync` método no `SearchIndexClient` da instância, conforme demonstrado no exemplo de código a seguir:

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

O `SearchAsync` método usa um argumento de texto de pesquisa e um opcional `SearchParameters` objeto que pode ser usado para refinar ainda mais a consulta. Uma consulta de pesquisa é especificada como o argumento de texto de pesquisa, enquanto uma consulta de filtro pode ser especificada definindo a `Filter` propriedade do `SearchParameters` argumento. O exemplo de código a seguir demonstra que os dois tipos de consulta:

```csharp
var parameters = new SearchParameters
{
  Filter = "location ne 'China' and location ne 'Vietnam'"
};
var searchResults = await indexClient.Documents.SearchAsync<Monkey>(text, parameters);
```

Essa consulta de filtro é aplicada a todo o índice e remove documentos nos resultados em que o `location` campo não é igual a China e não é igual a Vietnã. Após a filtragem, a consulta de pesquisa é executada nos resultados da consulta de filtro.

> [!NOTE]
> Para filtrar sem pesquisar, passar `*` como o argumento de texto de pesquisa.

O `SearchAsync` método retorna um `DocumentSearchResult` objeto que contém os resultados da consulta. Esse objeto é enumerado, com cada `Document` do objeto que está sendo criado como um `Monkey` do objeto e adicionado à `Monkeys` `ObservableCollection` para exibição. Os seguir capturas de tela mostrar consulta resultados da pesquisa retornados do Azure Search:

![](azure-search-images/search.png "Resultados da pesquisa")

Para obter mais informações sobre pesquisa e filtragem, consulte [consultar seu índice de Azure Search usando o SDK do .NET](/azure/search/search-query-dotnet/).

<a name="suggestions" />

### <a name="suggestion-queries"></a>Consultas de sugestão

O Azure Search permite sugestões para ser solicitado com base em uma consulta de pesquisa, chamando o `Documents.SuggestAsync` método no `SearchIndexClient` instância. Isso é demonstrado no exemplo de código a seguir:

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

O `SuggestAsync` método aceita um argumento de texto de pesquisa, o nome do sugestor usar (que é definido no índice), e um opcional `SuggestParameters` objeto que pode ser usado para refinar ainda mais a consulta. O `SuggestParameters` instância define as propriedades a seguir:

- `UseFuzzyMatching` – Quando definido como `true`, Azure Search encontrará sugestões mesmo que haja um caractere ausente ou substituído no texto de pesquisa.
- `HighlightPreTag` – a marcação que é anexada à sugestão de ocorrências.
- `HighlightPostTag` – a marcação que é acrescentada à sugestão de ocorrências.
- `MinimumCoverage` – representa a porcentagem do índice que deve ser coberto por uma consulta de sugestão para a consulta a ser relatado um sucesso. O padrão é 80.
- `Top` – o número de sugestões a serem recuperadas. Ele deve ser um inteiro entre 1 e 100, com um valor padrão de 5.

O efeito geral é que os 10 primeiros resultados do índice serão retornados com realce de ocorrências e os resultados incluirão os documentos que incluem os termos de pesquisa a ortografia da mesma forma.

O `SuggestAsync` método retorna um `DocumentSuggestResult` objeto que contém os resultados da consulta. Esse objeto é enumerado, com cada `Document` do objeto que está sendo criado como um `Monkey` do objeto e adicionado à `Monkeys` `ObservableCollection` para exibição. As capturas de tela a seguir mostram os resultados de sugestão retornados do Azure Search:

![](azure-search-images/suggest.png "Resultados de sugestão")

Observe que o aplicativo de exemplo, o `SuggestAsync` método é chamado somente quando o usuário termina de inserir um termo de pesquisa. No entanto, ele também pode ser usado para dar suporte a consultas de pesquisa de preenchimento automático executando em cada pressionamento de tecla.

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar a biblioteca do Microsoft Azure Search para integrar o Azure Search em um aplicativo xamarin. Forms. O Azure Search é um serviço de nuvem que fornece a indexação e consulta recursos para os dados carregados. Isso remove as complexidades do algoritmo de pesquisa tradicionalmente associadas ao implementar a funcionalidade de pesquisa em um aplicativo e requisitos de infraestrutura.


## <a name="related-links"></a>Links relacionados

- [O Azure Search (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureSearch/)
- [Documentação do Azure Search](/azure/search/)
- [Biblioteca do Microsoft Azure Search](https://www.nuget.org/packages/Microsoft.Azure.Search/)
