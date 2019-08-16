---
title: Acesso a dados remotos
description: Este capítulo explica como o aplicativo móvel eShopOnContainers acessa dados dos microservices em contêineres.
ms.prod: xamarin
ms.assetid: 42eba6f5-9784-4e1a-9943-5c1fbeea7452
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 7edbe96a6c32b4ebd4fc788e7361cddbae40601a
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69529186"
---
# <a name="accessing-remote-data"></a>Acesso a dados remotos

Muitas soluções modernas baseadas na Web fazem uso de serviços Web, hospedados por servidores Web, para fornecer funcionalidade para aplicativos cliente remotos. As operações que um serviço Web expõe constituem uma API da Web.

Os aplicativos cliente devem ser capazes de utilizar a API Web sem saber como os dados ou as operações que a API expõe são implementados. Isso exige que a API obedeça a padrões comuns que permitem que um aplicativo cliente e um serviço Web concordem com os formatos de dados a serem usados e a estrutura dos dados trocados entre os aplicativos cliente e o serviço Web.

## <a name="introduction-to-representational-state-transfer"></a>Introdução à transferência de estado de reapresentação

REST é um estilo de arquitetura para a criação de sistemas distribuídos com base em hipermídia. Uma vantagem principal do modelo REST é que ele se baseia em padrões abertos e não associa a implementação do modelo ou os aplicativos cliente que o acessam a qualquer implementação específica. Portanto, um serviço Web REST pode ser implementado usando Microsoft ASP.NET Core MVC, e os aplicativos cliente podem estar desenvolvendo usando qualquer linguagem e conjunto de ferramentas que podem gerar solicitações HTTP e analisar respostas HTTP.

O modelo REST usa um esquema de navegação para representar objetos e serviços em uma rede, chamados de recursos. Os sistemas que implementam o REST normalmente usam o protocolo HTTP para transmitir solicitações para acessar esses recursos. Nesses sistemas, um aplicativo cliente envia uma solicitação na forma de um URI que identifica um recurso e um método HTTP (como GET, POST, PUT ou DELETE) que indica a operação a ser executada nesse recurso. O corpo da solicitação HTTP contém todos os dados necessários para executar a operação.

> [!NOTE]
> REST define um modelo de solicitação sem estado. Portanto, as solicitações HTTP devem ser independentes e podem ocorrer em qualquer ordem.

A resposta de uma solicitação REST faz uso de códigos de status HTTP padrão. Por exemplo, uma solicitação que retorna dados válidos deve incluir o código de resposta HTTP 200 (OK), enquanto uma solicitação que não consegue localizar ou excluir um recurso especificado deve retornar uma resposta que inclui o código de status HTTP 404 (não encontrado).

Uma API Web RESTful expõe um conjunto de recursos conectados e fornece as operações principais que permitem que um aplicativo manipule esses recursos e navegue facilmente entre eles. Por esse motivo, os URIs que constituem uma API Web RESTful típica são orientadas para os dados que ele expõe e usam os recursos fornecidos pelo HTTP para operar nesses dados.

Os dados incluídos por um aplicativo cliente em uma solicitação HTTP e as mensagens de resposta correspondentes do servidor Web podem ser apresentados em uma variedade de formatos, conhecidos como tipos de mídia. Quando um aplicativo cliente envia uma solicitação que retorna dados no corpo de uma mensagem, ele pode especificar os tipos de mídia que ele pode manipular no `Accept` cabeçalho da solicitação. Se o servidor Web oferecer suporte a esse tipo de mídia, ele poderá responder com uma resposta `Content-Type` que inclui o cabeçalho que especifica o formato dos dados no corpo da mensagem. Em seguida, é responsabilidade do aplicativo cliente analisar a mensagem de resposta e interpretar os resultados no corpo da mensagem adequadamente.

Para obter mais informações sobre REST, consulte [design de API](/azure/architecture/best-practices/api-design/) e implementação de [API](/azure/architecture/best-practices/api-implementation/).

## <a name="consuming-restful-apis"></a>Consumindo APIs RESTful

O aplicativo móvel eShopOnContainers usa o padrão Model-View-ViewModel (MVVM) e os elementos de modelo do padrão representam as entidades de domínio usadas no aplicativo. As classes de controlador e de repositório no aplicativo de referência eShopOnContainers aceitam e retornam muitos desses objetos de modelo. Portanto, eles são usados como DTOs (objetos de transferência de dados) que contêm todos os dados que são transmitidos entre o aplicativo móvel e os microserviços em contêineres. O principal benefício de usar DTOs para passar dados e receber dados de um serviço Web é que, ao transmitir mais dados em uma única chamada remota, o aplicativo pode reduzir o número de chamadas remotas que precisam ser feitas.

### <a name="making-web-requests"></a>Como fazer solicitações da Web

O aplicativo móvel eShopOnContainers usa a `HttpClient` classe para fazer solicitações por http, com JSON sendo usado como o tipo de mídia. Essa classe fornece a funcionalidade de enviar solicitações HTTP de forma assíncrona e receber respostas HTTP de um recurso de URI identificado. A `HttpResponseMessage` classe representa uma mensagem de resposta http recebida de uma API REST depois que uma solicitação HTTP é feita. Ele contém informações sobre a resposta, incluindo o código de status, cabeçalhos e qualquer corpo. O `HttpContent` classe representa o corpo de HTTP e cabeçalhos de conteúdo, como `Content-Type` e `Content-Encoding`. O conteúdo pode ser lido usando qualquer um dos `ReadAs` métodos, `ReadAsStringAsync` como e `ReadAsByteArrayAsync`, dependendo do formato dos dados.

<a name="making_a_get_request" />

#### <a name="making-a-get-request"></a>Fazendo uma solicitação GET

A `CatalogService` classe é usada para gerenciar o processo de recuperação de dados do microserviço de catálogo. No método na classe, a `CatalogService` classe é registrada como um mapeamento de tipo em relação `ICatalogService` ao tipo com o contêiner de injeção de dependência Autofac. `ViewModelLocator` `RegisterDependencies` Em seguida, quando uma instância da `CatalogViewModel` classe é criada, seu construtor aceita um `ICatalogService` tipo, que Autofac resolve, `CatalogService` retornando uma instância da classe. Para obter mais informações sobre injeção de dependência, consulte [introdução à injeção de dependência](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

A Figura 10-1 mostra a interação das classes que lêem os dados do catálogo do microserviço de catálogo para `CatalogView`exibição pelo.

[Recuperando dados do microserviço de catálogo ![(accessing-remote-data-images/catalogdata.png " ")]] (accessing-remote-data-images/catalogdata-large.png#lightbox "Recuperando dados do microserviço de catálogo")

**Figura 10-1**: Recuperando dados do microserviço de catálogo

Quando o `CatalogView` é navegado para, o `OnInitialize` método na `CatalogViewModel` classe é chamado. Esse método recupera dados de catálogo do microserviço de catálogo, conforme demonstrado no exemplo de código a seguir:

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    ...  
    Products = await _productsService.GetCatalogAsync();  
    ...  
}
```

Esse método chama o `GetCatalogAsync` método `CatalogService` da instância que `CatalogViewModel` foi injetada no por Autofac. O seguinte exemplo de código mostra o método `GetCatalogAsync`:

```csharp
public async Task<ObservableCollection<CatalogItem>> GetCatalogAsync()  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.CatalogEndpoint);  
    builder.Path = "api/v1/catalog/items";  
    string uri = builder.ToString();  

    CatalogRoot catalog = await _requestProvider.GetAsync<CatalogRoot>(uri);  
    ...  
    return catalog?.Data.ToObservableCollection();            
}
```

Esse método cria o URI que identifica o recurso ao qual a solicitação será enviada e usa a `RequestProvider` classe para invocar o método HTTP Get no recurso, antes de retornar os resultados para o. `CatalogViewModel` A `RequestProvider` classe contém a funcionalidade que envia uma solicitação na forma de um URI que identifica um recurso, um método http que indica a operação a ser executada nesse recurso e um corpo que contém os dados necessários para executar a operação. Para obter informações sobre como `RequestProvider` a classe é injetada `CatalogService class`no, consulte [introdução à injeção de dependência](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

O exemplo de código a seguir `GetAsync` mostra o método `RequestProvider` na classe:

```csharp
public async Task<TResult> GetAsync<TResult>(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    HttpResponseMessage response = await httpClient.GetAsync(uri);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>   
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

Esse método chama o `CreateHttpClient` método, que retorna uma instância `HttpClient` da classe com os cabeçalhos apropriados definidos. Em seguida, ele envia uma solicitação get assíncrona para o recurso identificado pelo URI, com a resposta armazenada na `HttpResponseMessage` instância. O `HandleResponse` método é invocado, o que gera uma exceção se a resposta não incluir um código de status HTTP de êxito. Em seguida, a resposta é lida como uma cadeia de caracteres, convertida de JSON em um `CatalogRoot` objeto e retornada para o. `CatalogService`

O `CreateHttpClient` método é mostrado no exemplo de código a seguir:

```csharp
private HttpClient CreateHttpClient(string token = "")  
{  
    var httpClient = new HttpClient();  
    httpClient.DefaultRequestHeaders.Accept.Add(  
        new MediaTypeWithQualityHeaderValue("application/json"));  

    if (!string.IsNullOrEmpty(token))  
    {  
        httpClient.DefaultRequestHeaders.Authorization =   
            new AuthenticationHeaderValue("Bearer", token);  
    }  
    return httpClient;  
}
```

Esse método cria uma nova instância `HttpClient` da classe e define o `Accept` cabeçalho de `HttpClient` todas as solicitações feitas pela instância para `application/json`, o que indica que espera-se que o conteúdo de qualquer resposta seja formatado usando JSON. Em seguida, se um token de acesso foi passado como um argumento `CreateHttpClient` para o método, ele é adicionado `Authorization` ao cabeçalho de `HttpClient` quaisquer solicitações feitas pela instância, prefixada com a `Bearer`cadeia de caracteres. Para obter mais informações sobre autorização, consulte [Authorization](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

Quando o `GetAsync` `RequestProvider` método na classe chama `HttpClient.GetAsync`, o `Items` método na `CatalogController` classe no projeto Catalog. API é invocado, que é mostrado no exemplo de código a seguir:

```csharp
[HttpGet]  
[Route("[action]")]  
public async Task<IActionResult> Items(  
    [FromQuery]int pageSize = 10, [FromQuery]int pageIndex = 0)  
{  
    var totalItems = await _catalogContext.CatalogItems  
        .LongCountAsync();  

    var itemsOnPage = await _catalogContext.CatalogItems  
        .OrderBy(c=>c.Name)  
        .Skip(pageSize * pageIndex)  
        .Take(pageSize)  
        .ToListAsync();  

    itemsOnPage = ComposePicUri(itemsOnPage);  
    var model = new PaginatedItemsViewModel<CatalogItem>(  
        pageIndex, pageSize, totalItems, itemsOnPage);             

    return Ok(model);  
}
```

Esse método recupera os dados do catálogo do banco de dados SQL usando o EntityFramework e o retorna como uma mensagem de resposta que inclui um código de status HTTP de êxito e uma `CatalogItem` coleção de instâncias formatadas em JSON.

#### <a name="making-a-post-request"></a>Fazendo uma solicitação POST

A `BasketService` classe é usada para gerenciar a recuperação de dados e o processo de atualização com o microserviço basket. No método na classe, a `BasketService` classe é registrada como um mapeamento de tipo em relação `IBasketService` ao tipo com o contêiner de injeção de dependência Autofac. `ViewModelLocator` `RegisterDependencies` Em seguida, quando uma instância da `BasketViewModel` classe é criada, seu construtor aceita um `IBasketService` tipo, que Autofac resolve, `BasketService` retornando uma instância da classe. Para obter mais informações sobre injeção de dependência, consulte [introdução à injeção de dependência](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

A Figura 10-2 mostra a interação de classes que enviam os dados da cesta `BasketView`exibidos pelo, ao microserviço da cesta.

[Enviando dados para o microserviço da cesta ![(accessing-remote-data-images/basketdata.png " ")]] (accessing-remote-data-images/basketdata-large.png#lightbox "Enviando dados para o microserviço da cesta")

**Figura 10-2**: Enviando dados para o microserviço da cesta

Quando um item é adicionado à cesta de compras, o `ReCalculateTotalAsync` método `BasketViewModel` na classe é chamado. Esse método atualiza o valor total dos itens na cesta e envia os dados da cesta para o microserviço da cesta, conforme demonstrado no exemplo de código a seguir:

```csharp
private async Task ReCalculateTotalAsync()  
{  
    ...  
    await _basketService.UpdateBasketAsync(new CustomerBasket  
    {  
        BuyerId = userInfo.UserId,   
        Items = BasketItems.ToList()  
    }, authToken);  
}
```

Esse método chama o `UpdateBasketAsync` método `BasketService` da instância que `BasketViewModel` foi injetada no por Autofac. O método a seguir mostra `UpdateBasketAsync` o método:

```csharp
public async Task<CustomerBasket> UpdateBasketAsync(CustomerBasket customerBasket, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    string uri = builder.ToString();  
    var result = await _requestProvider.PostAsync(uri, customerBasket, token);  
    return result;  
}
```

Esse método cria o URI que identifica o recurso ao qual a solicitação será enviada e usa a `RequestProvider` classe para invocar o método http post no recurso, antes de retornar os resultados para o. `BasketViewModel` Observe que um token de acesso, obtido do IdentityServer durante o processo de autenticação, é necessário para autorizar solicitações ao microserviço da cesta. Para obter mais informações sobre autorização, consulte [Authorization](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

O exemplo de código a seguir mostra um `PostAsync` dos métodos `RequestProvider` na classe:

```csharp
public async Task<TResult> PostAsync<TResult>(  
    string uri, TResult data, string token = "", string header = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    ...  
    var content = new StringContent(JsonConvert.SerializeObject(data));  
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");  
    HttpResponseMessage response = await httpClient.PostAsync(uri, content);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>  
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

Esse método chama o `CreateHttpClient` método, que retorna uma instância `HttpClient` da classe com os cabeçalhos apropriados definidos. Em seguida, ele envia uma solicitação post assíncrona para o recurso identificado pelo URI, com os dados do carrinho serializado sendo enviados no formato JSON e a resposta armazenada na `HttpResponseMessage` instância. O `HandleResponse` método é invocado, o que gera uma exceção se a resposta não incluir um código de status HTTP de êxito. Em seguida, a resposta é lida como uma cadeia de caracteres, convertida `CustomerBasket` de JSON em um objeto e `BasketService`retornada para o. Para obter mais informações sobre `CreateHttpClient` o método, consulte [fazendo uma solicitação get](#making_a_get_request).

Quando o `PostAsync` `RequestProvider` método na classe chama `HttpClient.PostAsync`, o `Post` método na `BasketController` classe no projeto basket. API é invocado, que é mostrado no exemplo de código a seguir:

```csharp
[HttpPost]  
public async Task<IActionResult> Post([FromBody]CustomerBasket value)  
{  
    var basket = await _repository.UpdateBasketAsync(value);  
    return Ok(basket);  
}
```

Esse método usa uma instância da `RedisBasketRepository` classe para persistir os dados da cesta para o cache Redis e o retorna como uma mensagem de resposta que inclui um código de status HTTP de êxito e uma instância formatada `CustomerBasket` JSON.

#### <a name="making-a-delete-request"></a>Fazendo uma solicitação de exclusão

A Figura 10-3 mostra as interações de classes que excluem dados de cesta do microserviço da `CheckoutView`cesta para o.

![](accessing-remote-data-images/checkoutdata.png "Excluindo dados do microserviço da cesta")

**Figura 10-3**: Excluindo dados do microserviço da cesta

Quando o processo de check-out é `CheckoutAsync` invocado, `CheckoutViewModel` o método na classe é chamado. Esse método cria um novo pedido, antes de limpar a cesta de compras, conforme demonstrado no exemplo de código a seguir:

```csharp
private async Task CheckoutAsync()  
{  
    ...  
    await _basketService.ClearBasketAsync(_shippingAddress.Id.ToString(), authToken);  
    ...  
}
```

Esse método chama o `ClearBasketAsync` método `BasketService` da instância que `CheckoutViewModel` foi injetada no por Autofac. O método a seguir mostra `ClearBasketAsync` o método:

```csharp
public async Task ClearBasketAsync(string guidUser, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    builder.Path = guidUser;  
    string uri = builder.ToString();  
    await _requestProvider.DeleteAsync(uri, token);  
}
```

Esse método cria o URI que identifica o recurso ao qual a solicitação será enviada e usa a `RequestProvider` classe para invocar o método http Delete no recurso. Observe que um token de acesso, obtido do IdentityServer durante o processo de autenticação, é necessário para autorizar solicitações ao microserviço da cesta. Para obter mais informações sobre autorização, consulte [Authorization](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

O exemplo de código a seguir `DeleteAsync` mostra o método `RequestProvider` na classe:

```csharp
public async Task DeleteAsync(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    await httpClient.DeleteAsync(uri);  
}
```

Esse método chama o `CreateHttpClient` método, que retorna uma instância `HttpClient` da classe com os cabeçalhos apropriados definidos. Em seguida, ele envia uma solicitação de exclusão assíncrona para o recurso identificado pelo URI. Para obter mais informações sobre `CreateHttpClient` o método, consulte [fazendo uma solicitação get](#making_a_get_request).

Quando o `DeleteAsync` `RequestProvider` método na classe chama `HttpClient.DeleteAsync`, o `Delete` método na `BasketController` classe no projeto basket. API é invocado, que é mostrado no exemplo de código a seguir:

```csharp
[HttpDelete("{id}")]  
public void Delete(string id)  
{  
    _repository.DeleteBasketAsync(id);  
}
```

Esse método usa uma instância da `RedisBasketRepository` classe para excluir os dados da cesta do cache Redis.

## <a name="caching-data"></a>Armazenando dados em cache

O desempenho de um aplicativo pode ser melhorado pelo armazenamento em cache de dados acessados com frequência para um armazenamento rápido localizado próximo ao aplicativo. Se o armazenamento rápido estiver mais próximo do aplicativo do que a fonte original, o Caching poderá melhorar significativamente os tempos de resposta ao recuperar dados.

A forma mais comum de cache é o cache de leitura, em que um aplicativo recupera dados referenciando o cache. Se os dados não estiverem no cache, eles serão recuperados do armazenamento de dados e adicionados ao cache. Os aplicativos podem implementar o cache de leitura com o padrão de reserva de cache. Esse padrão determina se o item está atualmente no cache. Se o item não estiver no cache, ele será lido no repositório de dados e adicionado ao cache. Para obter mais informações, consulte o padrão de [reserva de cache](/azure/architecture/patterns/cache-aside/) .

> [!TIP]
> Armazene em cache os dados que são lidos com frequência e que são alterados com pouca frequência. Esses dados podem ser adicionados ao cache sob demanda na primeira vez que são recuperados por um aplicativo. Isso significa que o aplicativo precisa buscar os dados apenas uma vez no armazenamento de dados, e esse acesso subsequente pode ser satisfeito usando o cache.

Aplicativos distribuídos, como o aplicativo de referência eShopOnContainers, devem fornecer um ou ambos os caches a seguir:

- Um cache compartilhado, que pode ser acessado por vários processos ou máquinas.
- Um cache privado, no qual os dados são mantidos localmente no dispositivo que executa o aplicativo.

O aplicativo móvel eShopOnContainers usa um cache privado, no qual os dados são mantidos localmente no dispositivo que está executando uma instância do aplicativo. Para obter informações sobre o cache usado pelo aplicativo de referência eShopOnContainers, [consulte microservices do .net: Arquitetura para aplicativos .NET em contêineres](https://aka.ms/microservicesebook).

> [!TIP]
> Considere o cache como um armazenamento de dados transitório que poderia desaparecer a qualquer momento. Verifique se os dados são mantidos no repositório de dados original, bem como no cache. As chances de perder dados serão minimizadas se o cache ficar indisponível.

### <a name="managing-data-expiration"></a>Gerenciando expiração de dados

É impraticável esperar que os dados armazenados em cache sejam sempre consistentes com os dados originais. Os dados no repositório de dados original podem ser alterados depois de serem armazenados em cache, fazendo com que os dados armazenados em cache se tornem obsoletos. Portanto, os aplicativos devem implementar uma estratégia que ajuda a garantir que os dados no cache estejam tão atualizados quanto possível, mas também podem detectar e lidar com situações que surgem quando os dados no cache se tornaram obsoletos. A maioria dos mecanismos de cache permite que o cache seja configurado para expirar dados e, portanto, reduzir o período para o qual os dados podem estar desatualizados.

> [!TIP]
> Defina um tempo de expiração padrão ao configurar um cache. Muitos caches implementam a expiração, que invalida os dados e os remove do cache se ele não for acessado por um período especificado. No entanto, deve-se ter cuidado ao escolher o período de validade. Se isso for feito muito, os dados expirarão muito rapidamente e os benefícios do armazenamento em cache serão reduzidos. Se isso for feito por muito tempo, os riscos de dados se tornam obsoletos. Portanto, o tempo de expiração deve corresponder ao padrão de acesso para aplicativos que usam os dados.

Quando os dados armazenados em cache expiram, eles devem ser removidos do cache e o aplicativo deve recuperar os dados do repositório de dados original e colocá-los novamente no cache.

Também é possível que um cache seja preenchido se os dados tiverem permissão para permanecer por um período muito longo. Portanto, as solicitações para adicionar novos itens ao cache podem ser necessárias para remover alguns itens de um processo conhecido como *remoção*. Os serviços de cache normalmente removem dados de forma menos usada recentemente. No entanto, há outras políticas de remoção, incluindo a mais recente, e primeiro a entrar, primeiro a sair. Para obter mais informações, consulte [diretrizes de cache](/azure/architecture/best-practices/caching/).

<a name="caching_images" />

### <a name="caching-images"></a>Imagens de cache

O aplicativo móvel eShopOnContainers consome imagens de produtos remotos que se beneficiam do armazenamento em cache. Essas imagens são exibidas pelo [`Image`](xref:Xamarin.Forms.Image) controle `CachedImage` e pelo controle fornecido pela biblioteca [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) .

O controle Xamarin. [`Image`](xref:Xamarin.Forms.Image) Forms dá suporte ao cache de imagens baixadas. O Caching é habilitado por padrão e armazenará a imagem localmente por 24 horas. Além disso, o tempo de expiração pode ser configurado [`CacheValidity`](xref:Xamarin.Forms.UriImageSource.CacheValidity) com a propriedade. Para obter mais informações, consulte [Caching de imagem baixado](~/xamarin-forms/user-interface/images.md#downloaded-image-caching).

O controle `CachedImage` de FFImageLoading é uma substituição para o controle Xamarin [`Image`](xref:Xamarin.Forms.Image) . Forms, fornecendo propriedades adicionais que habilitam a funcionalidade suplementar. Entre essa funcionalidade, o controle fornece cache configurável, enquanto dá suporte a erros e carregamento de espaços reservados de imagem. O exemplo de código a seguir mostra como o aplicativo móvel eShopOnContainers `CachedImage` usa o controle `ProductTemplate`no, que é o modelo de [`ListView`](xref:Xamarin.Forms.ListView) dados usado `CatalogView`pelo controle no:

```xaml
<ffimageloading:CachedImage
    Grid.Row="0"
    Source="{Binding PictureUri}"     
    Aspect="AspectFill">
    <ffimageloading:CachedImage.LoadingPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="default_campaign" />
            <On Platform="UWP" Value="Assets/default_campaign.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.LoadingPlaceholder>
    <ffimageloading:CachedImage.ErrorPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="noimage" />
            <On Platform="UWP" Value="Assets/noimage.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.ErrorPlaceholder>
</ffimageloading:CachedImage>
```

O `CachedImage` controle define as `LoadingPlaceholder` propriedades `ErrorPlaceholder` e para imagens específicas da plataforma. A `LoadingPlaceholder` propriedade especifica a imagem a ser exibida enquanto a imagem especificada `Source` pela propriedade é recuperada e a `ErrorPlaceholder` propriedade especifica a imagem a ser exibida se ocorrer um erro ao tentar recuperar a imagem especificado pela `Source` propriedade.

Como o nome indica, o `CachedImage` controle armazena em cache as imagens remotas no dispositivo para o tempo especificado pelo valor `CacheDuration` da propriedade. Quando esse valor de propriedade não está explicitamente definido, o valor padrão de 30 dias é aplicado.

## <a name="increasing-resilience"></a>Aumentando a resiliência

Todos os aplicativos que se comunicam com serviços e recursos remotos devem ser sensíveis a falhas transitórias. As falhas transitórias incluem a perda momentânea de conectividade de rede para serviços, a indisponibilidade temporária de um serviço ou tempos limite que surgem quando um serviço está ocupado. Essas falhas costumam ser autocorrigidas e, se a ação for repetida após um atraso adequado, provavelmente terá sucesso.

As falhas transitórias podem ter um enorme impacto sobre a qualidade percebida de um aplicativo, mesmo que ele tenha sido totalmente testado em todas as circunstâncias previstas. Para garantir que um aplicativo que se comunica com serviços remotos opere de forma confiável, ele deve ser capaz de fazer o seguinte:

- Detectar falhas quando elas ocorrerem e determinar se as falhas provavelmente serão transitórias.
- Repita a operação se determinar que a falha é provavelmente transitória e mantenha o controle do número de vezes que a operação foi repetida.
- Use uma estratégia de repetição apropriada, que especifica o número de repetições, o atraso entre cada tentativa e as ações a serem executadas após uma tentativa com falha.

Esse tratamento de falhas transitórias pode ser obtido encapsulando todas as tentativas de acesso a um serviço remoto no código que implementa o padrão de repetição.

### <a name="retry-pattern"></a>Padrão de repetição

Se um aplicativo detectar uma falha ao tentar enviar uma solicitação para um serviço remoto, ele poderá lidar com a falha de uma das seguintes maneiras:

- Repetindo a operação. O aplicativo pode repetir a solicitação com falha imediatamente.
- Repetir a operação após um atraso. O aplicativo deve aguardar por um período de tempo adequado antes de repetir a solicitação.
- Cancelando a operação. O aplicativo deve cancelar a operação e relatar uma exceção.

A estratégia de repetição deve ser ajustada para corresponder aos requisitos de negócios do aplicativo. Por exemplo, é importante otimizar a contagem de repetição e o intervalo de repetição para a operação que está sendo tentada. Se a operação fizer parte de uma interação do usuário, o intervalo de repetição deverá ser curto e apenas algumas tentativas tentarão evitar que os usuários aguardem uma resposta. Se a operação fizer parte de um fluxo de trabalho de longa execução, onde o cancelamento ou a reinicialização do fluxo de trabalho é caro ou demorado, é apropriado aguardar mais tempo entre as tentativas e repetir mais vezes.

> [!NOTE]
> Uma estratégia de repetição agressiva com atraso mínimo entre as tentativas e um grande número de tentativas, pode degradar um serviço remoto que está sendo executado perto de ou em capacidade. Além disso, essa estratégia de repetição também poderia afetar a capacidade de resposta do aplicativo se estiver tentando executar uma operação com falha continuamente.

Se uma solicitação ainda falhar após várias tentativas, é melhor que o aplicativo impeça que outras solicitações vá para o mesmo recurso e relate uma falha. Depois, após um período definido, o aplicativo pode fazer uma ou mais solicitações ao recurso para ver se elas foram bem-sucedidas. Para obter mais informações, consulte [padrão de disjuntor](#circuit_breaker_pattern).

> [!TIP]
> Nunca implemente um mecanismo de repetição infinito. Use um número finito de repetições ou implemente o padrão de [disjuntor](/azure/architecture/patterns/circuit-breaker/) para permitir a recuperação de um serviço.

O aplicativo móvel eShopOnContainers atualmente não implementa o padrão de repetição ao fazer solicitações da Web RESTful. No entanto `CachedImage` , o controle, fornecido pela biblioteca [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) , dá suporte ao tratamento de falhas transitórias, repetindo o carregamento da imagem. Se o carregamento da imagem falhar, outras tentativas serão feitas. O número de tentativas é especificado pela `RetryCount` Propriedade e as repetições ocorrerão após um atraso especificado `RetryDelay` pela propriedade. Se esses valores de propriedade não estiverem explicitamente definidos, seus valores padrão serão aplicados – 3 `RetryCount` para a propriedade e 250 MS para `RetryDelay` a propriedade. Para obter mais informações sobre `CachedImage` o controle, consulte [Caching images](#caching_images).

O aplicativo de referência eShopOnContainers implementa o padrão de repetição. Para obter mais informações, incluindo uma discussão sobre como combinar o padrão de repetição com `HttpClient` a classe, [consulte microservices do .net: Arquitetura para aplicativos .NET em contêineres](https://aka.ms/microservicesebook).

Para obter mais informações sobre o padrão de repetição, consulte o padrão de [repetição](/azure/architecture/patterns/retry/) .

<a name="circuit_breaker_pattern" />

### <a name="circuit-breaker-pattern"></a>Padrão de disjuntor

Em algumas situações, podem ocorrer falhas devido a eventos previstos que levam mais tempo para serem corrigidos. Essas falhas podem variar de uma perda parcial de conectividade com a falha completa de um serviço. Nessas situações, é inútil que um aplicativo tente novamente uma operação que seja improvável de ser bem sucedida e, em vez disso, deve aceitar que a operação falhou e tratar dessa falha adequadamente.

O padrão de disjuntor pode impedir que um aplicativo tente executar repetidamente uma operação que provavelmente falhará, enquanto também permite que o aplicativo detecte se a falha foi resolvida.

> [!NOTE]
> A finalidade do padrão de disjuntor é diferente do padrão de repetição. O padrão de repetição permite que um aplicativo Repita uma operação na expectativa de que ele terá sucesso. O padrão de disjuntor impede que um aplicativo execute uma operação que provavelmente falhará.

Um disjuntor atua como um proxy para operações que podem falhar. O proxy deve monitorar o número de falhas recentes que ocorreram e usar essas informações para decidir se deseja permitir que a operação continue ou retornar uma exceção imediatamente.

O aplicativo móvel eShopOnContainers atualmente não implementa o padrão de disjuntor. No entanto, o eShopOnContainers faz. Para obter mais informações, [consulte microservices do .net: Arquitetura para aplicativos .NET em contêineres](https://aka.ms/microservicesebook).

> [!TIP]
> Combine os padrões de repetição e disjuntor. Um aplicativo pode combinar os padrões de repetição e de disjuntor usando o padrão de repetição para invocar uma operação por meio de um disjuntor. No entanto, a lógica de repetição deve ser sensível a quaisquer exceções retornadas pelo disjuntor e abandonar tentativas de repetição se o disjuntor indicar que uma falha não é transitória.

Para obter mais informações sobre o padrão de disjuntor, consulte o padrão de [disjuntor](/azure/architecture/patterns/circuit-breaker/) .

## <a name="summary"></a>Resumo

Muitas soluções modernas baseadas na Web fazem uso de serviços Web, hospedados por servidores Web, para fornecer funcionalidade para aplicativos cliente remotos. As operações que um serviço Web expõe constituem uma API Web, e os aplicativos cliente devem ser capazes de utilizar a API Web sem saber como os dados ou as operações que a API expõe são implementados.

O desempenho de um aplicativo pode ser melhorado pelo armazenamento em cache de dados acessados com frequência para um armazenamento rápido localizado próximo ao aplicativo. Os aplicativos podem implementar o cache de leitura com o padrão de reserva de cache. Esse padrão determina se o item está atualmente no cache. Se o item não estiver no cache, ele será lido no repositório de dados e adicionado ao cache.

Ao se comunicar com APIs Web, os aplicativos devem ser sensíveis a falhas transitórias. As falhas transitórias incluem a perda momentânea de conectividade de rede para serviços, a indisponibilidade temporária de um serviço ou tempos limite que surgem quando um serviço está ocupado. Essas falhas costumam ser autocorrigidas e, se a ação for repetida após um atraso adequado, provavelmente terá sucesso. Portanto, os aplicativos devem encapsular todas as tentativas de acessar uma API Web no código que implementa um mecanismo transitório de tratamento de falhas.


## <a name="related-links"></a>Links relacionados

- [Download do eBook (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
