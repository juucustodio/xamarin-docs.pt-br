---
title: Acesso a dados remotos
description: Este capítulo explica como o aplicativo móvel do eShopOnContainers acessa os dados dos microsserviços em contêineres.
ms.prod: xamarin
ms.assetid: 42eba6f5-9784-4e1a-9943-5c1fbeea7452
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 009a4025bc9df6f657964b7e97e559635ef0a929
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996159"
---
# <a name="accessing-remote-data"></a>Acesso a dados remotos

Muitas soluções modernas baseado na web fazer uso dos serviços da web, hospedados por servidores web, para fornecer funcionalidade para o cliente remoto a aplicativos. As operações que um serviço web expõe constituem uma API da web.

Aplicativos de cliente devem ser capazes de utilizar a API da web sem saber como os dados ou operações que expõe a API são implementadas. Isso requer que a API age de acordo com os padrões comuns que permitem que um serviço de aplicativo e web de cliente concordar sobre quais formatos de dados para uso e a estrutura dos dados que são trocados entre aplicativos cliente e o serviço web.

## <a name="introduction-to-representational-state-transfer"></a>Introdução ao Representational State Transfer

REST Representational State Transfer () é um estilo de arquitetura para a criação de sistemas distribuídos com base em hipermídia. Uma vantagem principal do modelo REST é que ele tem com base em padrões abertos e não se associa a implementação do modelo ou os aplicativos cliente que acessam a nenhuma implementação específica. Portanto, um serviço web REST poderia ser implementado usando o Microsoft ASP.NET Core MVC e poderiam estar desenvolvendo aplicativos cliente usando qualquer linguagem e o conjunto de ferramentas que pode gerar solicitações HTTP e analisar as respostas HTTP.

O modelo de REST usa um esquema de navegação para representar objetos e serviços em uma rede, chamada de recursos. Sistemas que implementam REST geralmente usam o protocolo HTTP para transmitir solicitações para acessar esses recursos. Em tais sistemas, um aplicativo cliente envia uma solicitação na forma de um URI que identifica um recurso e um método HTTP (como GET, POST, PUT ou DELETE) que indica a operação a ser executada nesse recurso. O corpo da solicitação HTTP contém todos os dados necessários para executar a operação.

> [!NOTE]
> REST define um modelo de solicitação sem monitoração de estado. Portanto, as solicitações HTTP devem ser independentes e podem ocorrer em qualquer ordem.

A resposta de uma REST solicitação faz uso de códigos de status HTTP padrão. Por exemplo, uma solicitação que retorna dados válidos deve incluir o código de resposta HTTP 200 (Okey), enquanto uma solicitação que não consegue localizar ou excluir um recurso especificado deve retornar uma resposta que inclui o código de status HTTP 404 (não encontrado).

Uma API web RESTful expõe um conjunto de recursos conectados e fornece as principais operações que permitem que um aplicativo manipular esses recursos e navegar facilmente entre eles. Por esse motivo, os URIs que constituem uma API web RESTful típica são orientados para os dados que ele expõe e use os recursos fornecidos pelo HTTP para operar em dados.

Os dados incluídos por um aplicativo de cliente em uma solicitação HTTP e as mensagens de resposta correspondente do servidor web, podem ser apresentados em uma variedade de formatos, conhecidos como tipos de mídia. Quando um aplicativo cliente envia uma solicitação que retorna dados no corpo de uma mensagem, ele pode especificar os tipos de mídia, ele pode manipular o `Accept` cabeçalho da solicitação. Se o servidor web dá suporte a esse tipo de mídia, ele pode responder com uma resposta que inclui o `Content-Type` cabeçalho que especifica o formato dos dados no corpo da mensagem. Em seguida, é responsabilidade do aplicativo cliente para analisar a mensagem de resposta e interpretar os resultados no corpo da mensagem de forma adequada.

Para obter mais informações sobre o REST, consulte [design de API](/azure/architecture/best-practices/api-design/) e [implementação da API](/azure/architecture/best-practices/api-implementation/).

## <a name="consuming-restful-apis"></a>Consumo de APIs RESTful

O aplicativo móvel do eShopOnContainers usa o padrão Model-View-ViewModel (MVVM) e os elementos de modelo de representam o padrão que as entidades de domínio usadas no aplicativo. As classes de controlador e o repositório no aplicativo eShopOnContainers de referência aceitam e retornam muitos desses objetos de modelo. Portanto, eles são usados como objetos de transferência de dados (DTOs) que contêm todos os dados que são passados entre o aplicativo móvel e os microsserviços em contêineres. O principal benefício do uso de DTOs para passar dados para e receber dados de um serviço web é que, pela transmissão de mais dados em uma única chamada remota, o aplicativo pode reduzir o número de chamadas remotas, que precisam ser feitas.

### <a name="making-web-requests"></a>Como fazer solicitações da Web

O aplicativo móvel de eShopOnContainers usa a `HttpClient` classe para fazer solicitações sobre HTTP, JSON, que está sendo usado como o tipo de mídia. Essa classe fornece funcionalidade de forma assíncrona enviar solicitações HTTP e receber respostas HTTP de um URI identificou o recurso. O `HttpResponseMessage` classe representa uma mensagem de resposta HTTP recebida de uma API REST, depois que foi feita uma solicitação HTTP. Ele contém informações sobre a resposta, incluindo o código de status, cabeçalhos e qualquer corpo. O `HttpContent` classe representa o corpo de HTTP e cabeçalhos de conteúdo, como `Content-Type` e `Content-Encoding`. O conteúdo pode ser lido usando qualquer um dos `ReadAs` métodos, tais como `ReadAsStringAsync` e `ReadAsByteArrayAsync`, dependendo do formato dos dados.

<a name="making_a_get_request" />

#### <a name="making-a-get-request"></a>Fazer uma solicitação GET

O `CatalogService` classe é usada para gerenciar o processo de recuperação de dados de microsserviço de catálogo. No `RegisterDependencies` método no `ViewModelLocator` classe, o `CatalogService` classe é registrada como um mapeamento de tipo em relação a `ICatalogService` tipo com o contêiner de injeção de dependência do Autofac. Em seguida, quando uma instância das `CatalogViewModel` classe é criada, seu construtor aceita um `ICatalogService` de tipo, que resolve Autofac, retornando uma instância da `CatalogService` classe. Para obter mais informações sobre injeção de dependência, consulte [Introdução à injeção de dependência](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

A Figura 10-1 mostra a interação de classes que leem dados do catálogo do microsserviço de catálogo para a exibição pelo `CatalogView`.

[![](accessing-remote-data-images/catalogdata.png "Recuperando dados de microsserviço de catálogo")](accessing-remote-data-images/catalogdata-large.png#lightbox "recuperando dados de microsserviço de catálogo")

**Figura 10-1**: recuperando dados de microsserviço de catálogo

Quando o `CatalogView` é acessada, o `OnInitialize` método no `CatalogViewModel` classe é chamada. Esse método recupera dados de catálogo do microsserviço de catálogo, conforme demonstrado no exemplo de código a seguir:

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    ...  
    Products = await _productsService.GetCatalogAsync();  
    ...  
}
```

Este método chama o `GetCatalogAsync` método da `CatalogService` que foi injetada na instância a `CatalogViewModel` por Autofac. O seguinte exemplo de código mostra o `GetCatalogAsync` método:

```csharp
public async Task<ObservableCollection<CatalogItem>> GetCatalogAsync()  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.CatalogEndpoint);  
    builder.Path = "api/v1/catalog/items";  
    string uri = builder.ToString();  

    CatalogRoot catalog = await _requestProvider.GetAsync<CatalogRoot>(uri);  
    ...  
    return catalog?.Data.ToObservableCollection();            
}
```

Esse método cria o URI que identifica o recurso a solicitação será enviada para e usa o `RequestProvider` classe para invocar o método HTTP GET no recurso, antes de retornar os resultados para o `CatalogViewModel`. O `RequestProvider` classe contém a funcionalidade que envia uma solicitação na forma de um URI que identifica um recurso, um método HTTP que indica a operação a ser executada nesse recurso, e um corpo que contém todos os dados necessários para executar a operação. Para obter informações sobre como o `RequestProvider` classe é injetada a `CatalogService class`, consulte [Introdução à injeção de dependência](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

O seguinte exemplo de código mostra a `GetAsync` método no `RequestProvider` classe:

```csharp
public async Task<TResult> GetAsync<TResult>(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    HttpResponseMessage response = await httpClient.GetAsync(uri);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>   
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

Este método chama o `CreateHttpClient` método, que retorna uma instância da `HttpClient` classe com o conjunto de cabeçalhos apropriados. Ele envia uma solicitação GET assíncrona para o recurso identificado pelo URI, com a resposta sendo armazenada no `HttpResponseMessage` instância. O `HandleResponse` método, em seguida, é invocado, o que gera uma exceção se a resposta não incluir um código de status HTTP de sucesso. Em seguida, a resposta é lido como uma cadeia de caracteres convertida de JSON para um `CatalogRoot` do objeto e retornado para o `CatalogService`.

O `CreateHttpClient` método é mostrado no exemplo de código a seguir:

```csharp
private HttpClient CreateHttpClient(string token = "")  
{  
    var httpClient = new HttpClient();  
    httpClient.DefaultRequestHeaders.Accept.Add(  
        new MediaTypeWithQualityHeaderValue("application/json"));  

    if (!string.IsNullOrEmpty(token))  
    {  
        httpClient.DefaultRequestHeaders.Authorization =   
            new AuthenticationHeaderValue("Bearer", token);  
    }  
    return httpClient;  
}
```

Esse método cria uma nova instância dos `HttpClient` classe e conjuntos do `Accept` cabeçalho de quaisquer solicitações feitas pela `HttpClient` da instância para `application/json`, que indica que ele espera que o conteúdo de qualquer resposta a ser formatado usando JSON. Então, se um token de acesso foi passado como um argumento para o `CreateHttpClient` método, ele é adicionado a `Authorization` cabeçalho de quaisquer solicitações feitas pela `HttpClient` instância, prefixada com a cadeia de caracteres `Bearer`. Para obter mais informações sobre a autorização, consulte [autorização](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

Quando o `GetAsync` método na `RequestProvider` chamado pela classe `HttpClient.GetAsync`, o `Items` método no `CatalogController` classe no projeto Catalog. API será invocado, que é mostrado no exemplo de código a seguir:

```csharp
[HttpGet]  
[Route("[action]")]  
public async Task<IActionResult> Items(  
    [FromQuery]int pageSize = 10, [FromQuery]int pageIndex = 0)  
{  
    var totalItems = await _catalogContext.CatalogItems  
        .LongCountAsync();  

    var itemsOnPage = await _catalogContext.CatalogItems  
        .OrderBy(c=>c.Name)  
        .Skip(pageSize * pageIndex)  
        .Take(pageSize)  
        .ToListAsync();  

    itemsOnPage = ComposePicUri(itemsOnPage);  
    var model = new PaginatedItemsViewModel<CatalogItem>(  
        pageIndex, pageSize, totalItems, itemsOnPage);             

    return Ok(model);  
}
```

Esse método recupera os dados do catálogo do banco de dados SQL usando o EntityFramework e o retorna como uma mensagem de resposta que inclui um código de status HTTP de sucesso e uma coleção de JSON formatado `CatalogItem` instâncias.

#### <a name="making-a-post-request"></a>Fazer uma solicitação POST

O `BasketService` classe é usada para gerenciar a recuperação de dados e o processo de atualização com o microsserviço carrinho de compras. No `RegisterDependencies` método no `ViewModelLocator` classe, o `BasketService` classe é registrada como um mapeamento de tipo em relação a `IBasketService` tipo com o contêiner de injeção de dependência do Autofac. Em seguida, quando uma instância das `BasketViewModel` classe é criada, seu construtor aceita um `IBasketService` de tipo, que resolve Autofac, retornando uma instância da `BasketService `classe. Para obter mais informações sobre injeção de dependência, consulte [Introdução à injeção de dependência](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

Figura 10-2 mostra a interação de classes que envie os dados de carrinho de compras exibidos pelo `BasketView`, o microsserviço carrinho de compras.

[![](accessing-remote-data-images/basketdata.png "Enviando dados para o microsserviço carrinho de compras")](accessing-remote-data-images/basketdata-large.png#lightbox "enviando dados para o microsserviço carrinho de compras")

**Figura 10-2**: enviando dados para o microsserviço carrinho de compras

Quando um item é adicionado ao carrinho de compras, o `ReCalculateTotalAsync` método no `BasketViewModel` classe é chamada. Esse método atualiza o valor total de itens no carrinho de compras e envia os dados de carrinho de compras para o microsserviço de cesta de compras, conforme demonstrado no exemplo de código a seguir:

```csharp
private async Task ReCalculateTotalAsync()  
{  
    ...  
    await _basketService.UpdateBasketAsync(new CustomerBasket  
    {  
        BuyerId = userInfo.UserId,   
        Items = BasketItems.ToList()  
    }, authToken);  
}
```

Este método chama o `UpdateBasketAsync` método da `BasketService` que foi injetada na instância a `BasketViewModel` por Autofac. O método a seguir mostra o `UpdateBasketAsync` método:

```csharp
public async Task<CustomerBasket> UpdateBasketAsync(CustomerBasket customerBasket, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    string uri = builder.ToString();  
    var result = await _requestProvider.PostAsync(uri, customerBasket, token);  
    return result;  
}
```

Esse método cria o URI que identifica o recurso a solicitação será enviada para e usa o `RequestProvider` classe para invocar o método HTTP POST no recurso, antes de retornar os resultados para o `BasketViewModel`. Observe que um token de acesso obtido IdentityServer durante o processo de autenticação, é necessário para autorizar solicitações para o microsserviço carrinho de compras. Para obter mais informações sobre a autorização, consulte [autorização](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

O exemplo de código a seguir mostra um dos `PostAsync` métodos no `RequestProvider` classe:

```csharp
public async Task<TResult> PostAsync<TResult>(  
    string uri, TResult data, string token = "", string header = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    ...  
    var content = new StringContent(JsonConvert.SerializeObject(data));  
    content.Headers.ContentType = new MediaTypeHeaderValue("application/json");  
    HttpResponseMessage response = await httpClient.PostAsync(uri, content);  

    await HandleResponse(response);  
    string serialized = await response.Content.ReadAsStringAsync();  

    TResult result = await Task.Run(() =>  
        JsonConvert.DeserializeObject<TResult>(serialized, _serializerSettings));  

    return result;  
}
```

Este método chama o `CreateHttpClient` método, que retorna uma instância da `HttpClient` classe com o conjunto de cabeçalhos apropriados. Ele envia uma solicitação POST assíncrona para o recurso identificado pelo URI, com os dados serializados do carrinho de compras que está sendo enviados no formato JSON e a resposta sendo armazenados no `HttpResponseMessage` instância. O `HandleResponse` método, em seguida, é invocado, o que gera uma exceção se a resposta não incluir um código de status HTTP de sucesso. Em seguida, a resposta é lido como uma cadeia de caracteres convertida de JSON para um `CustomerBasket` do objeto e retornado para o `BasketService`. Para obter mais informações sobre o `CreateHttpClient` método, consulte [fazendo uma solicitação GET](#making_a_get_request).

Quando o `PostAsync` método na `RequestProvider` chamado pela classe `HttpClient.PostAsync`, o `Post` método no `BasketController` classe no projeto basket. API será invocado, que é mostrado no exemplo de código a seguir:

```csharp
[HttpPost]  
public async Task<IActionResult> Post([FromBody]CustomerBasket value)  
{  
    var basket = await _repository.UpdateBasketAsync(value);  
    return Ok(basket);  
}
```

Esse método usa uma instância das `RedisBasketRepository` de classe para manter os dados de carrinho de compras para o cache Redis e o retorna como uma mensagem de resposta que inclui um código de status HTTP de sucesso e um JSON formatado `CustomerBasket` instância.

#### <a name="making-a-delete-request"></a>Fazer uma solicitação de exclusão

Figura 10-3 mostra as interações de classes que excluir dados de carrinho de compras de microsserviço carrinho de compras, o `CheckoutView`.

![](accessing-remote-data-images/checkoutdata.png "Dados de exclusão de microsserviço carrinho de compras")

**Figura 10-3**: excluindo dados de microsserviço carrinho de compras

Quando o processo de check-out é invocado, o `CheckoutAsync` método no `CheckoutViewModel` classe é chamada. Esse método cria um novo pedido, antes de limpar o carrinho de compras, conforme demonstrado no exemplo de código a seguir:

```csharp
private async Task CheckoutAsync()  
{  
    ...  
    await _basketService.ClearBasketAsync(_shippingAddress.Id.ToString(), authToken);  
    ...  
}
```

Este método chama o `ClearBasketAsync` método da `BasketService` que foi injetada na instância a `CheckoutViewModel` por Autofac. O método a seguir mostra o `ClearBasketAsync` método:

```csharp
public async Task ClearBasketAsync(string guidUser, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    builder.Path = guidUser;  
    string uri = builder.ToString();  
    await _requestProvider.DeleteAsync(uri, token);  
}
```

Esse método cria o URI que identifica o recurso que a solicitação será enviada para e usa o `RequestProvider` classe para invocar o método HTTP DELETE no recurso. Observe que um token de acesso obtido IdentityServer durante o processo de autenticação, é necessário para autorizar solicitações para o microsserviço carrinho de compras. Para obter mais informações sobre a autorização, consulte [autorização](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

O seguinte exemplo de código mostra a `DeleteAsync` método no `RequestProvider` classe:

```csharp
public async Task DeleteAsync(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    await httpClient.DeleteAsync(uri);  
}
```

Este método chama o `CreateHttpClient` método, que retorna uma instância da `HttpClient` classe com o conjunto de cabeçalhos apropriados. Em seguida, ele envia uma solicitação de exclusão assíncrona para o recurso identificado pelo URI. Para obter mais informações sobre o `CreateHttpClient` método, consulte [fazendo uma solicitação GET](#making_a_get_request).

Quando o `DeleteAsync` método na `RequestProvider` chamado pela classe `HttpClient.DeleteAsync`, o `Delete` método no `BasketController` classe no projeto basket. API será invocado, que é mostrado no exemplo de código a seguir:

```csharp
[HttpDelete("{id}")]  
public void Delete(string id)  
{  
    _repository.DeleteBasketAsync(id);  
}
```

Esse método usa uma instância da `RedisBasketRepository` classe para excluir os dados de carrinho de compras do cache Redis.

## <a name="caching-data"></a>Armazenando dados em cache

O desempenho de um aplicativo pode ser melhorado ao armazenar em cache dados acessados com frequência para armazenamento rápido localizado próximo ao aplicativo. Se o armazenamento rápido está localizado o mais próximo ao aplicativo que a fonte original, o cache pode melhorar significativamente a resposta ao recuperar dados de tempo.

A forma mais comum de armazenamento em cache é cache de Read-through, onde um aplicativo recupera dados referenciando o cache. Se os dados não estiverem no cache, ele tem recuperados do repositório de dados e adicionado ao cache. Aplicativos podem implementar o cache de Read-through com o padrão cache-aside. Esse padrão determina se o item está atualmente no cache. Se o item não estiver no cache, ele tem ler do armazenamento de dados e adicionado ao cache. Para obter mais informações, consulte o [Cache-Aside](/azure/architecture/patterns/cache-aside/) padrão.

> [!TIP]
> Armazenar em cache os dados que são lidos com frequência e que mudam com pouca frequência. Esses dados podem ser adicionados ao cache sob demanda na primeira vez que ela é recuperada por um aplicativo. Isso significa que o aplicativo precisa para buscar os dados apenas uma vez do armazenamento de dados, e que o acesso subsequente pode ser atendido pelo uso do cache.

Aplicativos distribuídos, como o eShopOnContainers Referência de aplicativo, devem fornecer um ou ambos os seguintes caches:

-   Um cache compartilhado, que pode ser acessado por vários processos ou computadores.
-   Um cache privado, onde os dados são mantidos localmente no dispositivo que executa o aplicativo.

O aplicativo móvel do eShopOnContainers usa um cache privado, onde os dados são mantidos localmente no dispositivo que está executando uma instância do aplicativo. Para obter informações sobre o cache usado pelo aplicativo eShopOnContainers de referência, consulte [Microsserviços do .NET: arquitetura para aplicativos .NET em contêineres](https://aka.ms/microservicesebook).

> [!TIP]
> Considere o cache como um armazenamento de dados temporários que pode desaparecer a qualquer momento. Certifique-se de que os dados são mantidos no armazenamento de dados original, bem como o cache. A possibilidade de perda de dados, em seguida, é minimizada se o cache ficar indisponível.

### <a name="managing-data-expiration"></a>Gerenciar a expiração de dados

Não é prático esperar que os dados armazenados em cache sempre sejam consistentes com os dados originais. Dados no armazenamento de dados original podem mudar depois que ele é foi armazenado em cache, fazendo com que os dados em cache tornem-se obsoletos. Portanto, aplicativos devem implementar uma estratégia que ajuda a garantir que os dados no cache sejam tão atualizados quanto possível, mas pode também detectar e lidar com situações que podem surgir quando os dados no cache se tornarem obsoletos. Mecanismos de cache mais habilitam o cache ser configurada para expirar os dados e, portanto, reduzem o período para o qual os dados podem estar desatualizados.

> [!TIP]
> Definir uma expiração padrão tempo ao configurar um cache. Vários caches implementam uma expiração, o que invalida os dados e o remove do cache se ele não é acessado por um período especificado. No entanto, cuidado ao escolher o período de validade. Se for feita muito curto, dados expirará muito rapidamente e os benefícios do armazenamento em cache serão reduzidos. Se for feita muito longo, os riscos de dados se tornarem obsoletos. Portanto, o tempo de expiração deve corresponder ao padrão de acesso para aplicativos que usam os dados.

Quando os dados armazenados em cache expiram, ela deverá ser removida do cache e o aplicativo deve recuperar os dados dos dados originais armazenarem e colocá-lo de volta para o cache.

Também é possível que um cache fique cheio, caso os dados possam permanecer por um período muito longo. Portanto, as solicitações para adicionar novos itens ao cache podem ser necessárias para remover alguns itens em um processo conhecido como *remoção*. Serviços de Caching normalmente remover os dados em uma base de usados menos recentemente. No entanto, há outras políticas de remoção, incluindo usado mais recentemente e o primeiro a entrar, primeiro a sair. Para obter mais informações, consulte [diretrizes de Caching](/azure/architecture/best-practices/caching/).

<a name="caching_images" />

### <a name="caching-images"></a>Cache de imagens

O aplicativo móvel do eShopOnContainers consome as imagens de produto remoto que se beneficiam de serem armazenados em cache. Essas imagens são exibidas pela [ `Image` ](xref:Xamarin.Forms.Image) controle e o `CachedImage` controle fornecido pelo [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) biblioteca.

O xamarin. Forms [ `Image` ](xref:Xamarin.Forms.Image) controle dá suporte ao cache de imagens baixadas. Armazenamento em cache é habilitado por padrão e armazenará a imagem localmente para 24 horas. Além disso, o tempo de expiração pode ser configurado com o [ `CacheValidity` ](xref:Xamarin.Forms.UriImageSource.CacheValidity) propriedade. Para obter mais informações, consulte [cache de imagem baixado](~/xamarin-forms/user-interface/images.md#Image_Caching).

Do FFImageLoading `CachedImage` controle é uma substituição para o xamarin. Forms [ `Image` ](xref:Xamarin.Forms.Image) controle, fornecendo propriedades adicionais que habilitar a funcionalidade de suplementar. Entre essa funcionalidade, o controle fornece cache configurável, ao mesmo tempo que dão suporte a erros e carregar os espaços reservados de imagens. O exemplo de código a seguir mostra como o aplicativo móvel do eShopOnContainers usa a `CachedImage` controlar na `ProductTemplate`, que é o modelo de dados usado pelo [ `ListView` ](xref:Xamarin.Forms.ListView) no controlar o `CatalogView`:

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

O `CachedImage` conjuntos de controles de `LoadingPlaceholder` e `ErrorPlaceholder` propriedades para imagens específicas da plataforma. O `LoadingPlaceholder` propriedade especifica a imagem a ser exibido enquanto a imagem especificada pela `Source` propriedade é recuperada e o `ErrorPlaceholder` propriedade especifica a imagem a ser exibida se ocorrer um erro ao tentar recuperar a imagem especificado pelo `Source` propriedade.

Como o nome sugere, o `CachedImage` controle armazena em cache imagens remotas no dispositivo durante o tempo especificado pelo valor da `CacheDuration` propriedade. Quando o valor dessa propriedade não for definido explicitamente, o valor padrão de 30 dias é aplicado.

## <a name="increasing-resilience"></a>Aumentar a resiliência

Todos os aplicativos que se comunicam com serviços e recursos remotos são suscetíveis a falhas transitórias. Falhas transitórias incluem perda momentânea de conectividade de rede para serviços, indisponibilidade temporária de um serviço ou tempos limite que surgem quando um serviço está ocupado. Essas falhas geralmente são autocorretivas e, se a ação for repetida após um atraso provavelmente tenha êxito.

Falhas transitórias podem ter um grande impacto sobre a qualidade percebida de um aplicativo, mesmo que ele tenha sido integralmente testado sob todas as circunstâncias previsíveis. Para garantir que um aplicativo que se comunica com serviços remotos opera de forma confiável, ele deve ser capaz de fazer o seguinte:

-   Detecte falhas quando elas ocorrerem e determinam se as falhas são possivelmente é transitório.
-   Repita a operação se determinar que a falha é a probabilidade de ser transitória e acompanhar o número de vezes que a operação foi repetida.
-   Use uma estratégia de repetição apropriada, que especifica o número de repetições, o atraso entre cada tentativa e as ações a serem seguidas depois de uma falha na tentativa.

Esse tratamento de falhas transitórias pode ser obtido ao encapsular todas as tentativas de acessar um serviço remoto no código que implementa o padrão de repetição.

### <a name="retry-pattern"></a>Padrão de repetição

Se um aplicativo detectar uma falha ao tentar enviar uma solicitação para um serviço remoto, ele pode tratar a falha em qualquer uma das seguintes maneiras:

-   Repetindo a operação. O aplicativo pode repetir a solicitação com falha imediatamente.
-   Repetindo a operação após um atraso. O aplicativo deve esperar por uma quantidade adequada de tempo antes de tentar novamente a solicitação.
-   Cancelando a operação. O aplicativo deve cancelar a operação e relatar uma exceção.

A estratégia de repetição deve ser ajustada para atender aos requisitos de negócios do aplicativo. Por exemplo, é importante otimizar a contagem de repetições e intervalo para a operação de tentativa de repetição. Se a operação fizer parte de uma interação do usuário, o intervalo de repetição deve ser curto e apenas algumas tentativas tentadas Evite fazer com que os usuários aguardar uma resposta. Se a operação fizer parte de um fluxo de trabalho de longa execução, em que o cancelamento ou reiniciar o fluxo de trabalho é caro ou demorado, é apropriado aguardar mais tempo entre tentativas e repetir mais vezes.

> [!NOTE]
> Uma estratégia de repetição agressiva com atraso mínimo entre as tentativas e um grande número de repetições, poderá prejudicar a um serviço remoto que está executando ou próximo a capacidade máxima. Além disso, essa estratégia de repetição também pode afetar a capacidade de resposta do aplicativo se ele tentar continuamente executar uma operação com falha.

Se uma solicitação ainda falhar após um número de novas tentativas, é melhor para o aplicativo para evitar solicitações adicionais, vai para o mesmo recurso e para relatar uma falha. Em seguida, após um período definido, o aplicativo pode fazer uma ou mais solicitações para o recurso para ver se elas são bem-sucedidas. Para obter mais informações, consulte [padrão de disjuntor](#circuit_breaker_pattern).

> [!TIP]
> Nunca implemente um mecanismo de repetição infinita. Usar um número finito de repetições, ou implementar o [disjuntor](/azure/architecture/patterns/circuit-breaker/) padrão para permitir que um serviço de recuperação.

O aplicativo móvel do eShopOnContainers não implementar o padrão de repetição no momento ao fazer solicitações da web RESTful. No entanto, o `CachedImage` controle, fornecido pelo [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) biblioteca dá suporte a tratamento de falhas transitórias ao repetir de carregamento de imagem. Se o carregamento da imagem falhe, serão feitas tentativas adicionais. O número de tentativas é especificado pelo `RetryCount` propriedade e repetições ocorrerá após um atraso especificado pelo `RetryDelay` propriedade. Se esses valores de propriedade não definido explicitamente, seu valores são aplicados – do padrão 3 para o `RetryCount` propriedade e 250 MS para o `RetryDelay` propriedade. Para obter mais informações sobre o `CachedImage` de controle, consulte [cache de imagens](#caching_images).

O aplicativo de referência eShopOnContainers implementa o padrão de repetição. Para obter mais informações, incluindo uma discussão sobre como combinar o padrão de repetição com o `HttpClient` classe, consulte [Microsserviços do .NET: arquitetura para aplicativos .NET em contêineres](https://aka.ms/microservicesebook).

Para obter mais informações sobre o padrão de repetição, consulte a [Repita](/azure/architecture/patterns/retry/) padrão.

<a name="circuit_breaker_pattern" />

### <a name="circuit-breaker-pattern"></a>Padrão de Disjuntor

Em algumas situações, as falhas podem ocorrer devido a eventos antecipados que levam mais tempo para corrigir. Essas falhas podem variar de uma perda parcial de conectividade até falha total de um serviço. Nessas situações, é inútil para um aplicativo repetir uma operação que provavelmente não tiver êxito e em vez disso, deve aceitar a operação falhou e lidar com essa falha adequadamente.

O padrão de disjuntor pode impedir que um aplicativo tente executar uma operação que provavelmente falhará, enquanto também permite o aplicativo detectar se a falha foi resolvida repetidamente.

> [!NOTE]
> A finalidade do padrão de disjuntor é diferente do padrão de repetição. O padrão de repetição permite que um aplicativo repetir uma operação na expectativa de que haverá êxito. O padrão de disjuntor impede que um aplicativo executando uma operação que provavelmente falhará.

Um disjuntor atua como um proxy para operações que podem falhar. O proxy deve monitorar o número de falhas recentes ocorridas e usar essas informações para decidir se deseja permitir que a operação para continuar, ou para retornar uma exceção imediatamente.

O aplicativo móvel do eShopOnContainers implementa o padrão de disjuntor no momento. No entanto, o eShopOnContainers faz. Para obter mais informações, consulte [Microsserviços do .NET: arquitetura para aplicativos .NET em contêineres](https://aka.ms/microservicesebook).

> [!TIP]
> Combine os padrões de disjuntor e tente novamente. Um aplicativo pode combinar os padrões de disjuntor e tente novamente usando o padrão de repetição para invocar uma operação por meio de um disjuntor. No entanto, a lógica de repetição deve ser sensível às exceções retornadas pelo disjuntor e abandonar tentativas de repetição se o disjuntor indica que uma falha não é transitória.

Para obter mais informações sobre o padrão de Disjuntor, consulte o [disjuntor](/azure/architecture/patterns/circuit-breaker/) padrão.

## <a name="summary"></a>Resumo

Muitas soluções modernas baseado na web fazer uso dos serviços da web, hospedados por servidores web, para fornecer funcionalidade para o cliente remoto a aplicativos. As operações que um serviço web expõe constituem uma API da web e aplicativos de cliente devem ser capazes de utilizar a API da web sem saber como os dados ou operações que expõe a API são implementadas.

O desempenho de um aplicativo pode ser melhorado ao armazenar em cache dados acessados com frequência para armazenamento rápido localizado próximo ao aplicativo. Aplicativos podem implementar o cache de Read-through com o padrão cache-aside. Esse padrão determina se o item está atualmente no cache. Se o item não estiver no cache, ele tem ler do armazenamento de dados e adicionado ao cache.

Ao se comunicar com as APIs da web, aplicativos devem ser sensíveis a falhas transitórias. Falhas transitórias incluem perda momentânea de conectividade de rede para serviços, indisponibilidade temporária de um serviço ou tempos limite que surgem quando um serviço está ocupado. Essas falhas geralmente são autocorretivas e, se a ação for repetida após um atraso, em seguida, é provável seja bem-sucedida. Portanto, os aplicativos devem encapsular todas as tentativas de acessar uma API da web no código que implementa uma mecanismo de tratamento de falhas transitórias.


## <a name="related-links"></a>Links relacionados

- [Baixe o livro eletrônico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (amostra)](https://github.com/dotnet-architecture/eShopOnContainers)
