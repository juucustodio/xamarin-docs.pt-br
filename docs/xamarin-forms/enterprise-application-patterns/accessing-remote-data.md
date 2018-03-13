---
title: Acesso a dados remotos
ms.topic: article
ms.prod: xamarin
ms.assetid: 42eba6f5-9784-4e1a-9943-5c1fbeea7452
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 0eec51a6c95894482a57bfe3bb1f95aec2045af4
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="accessing-remote-data"></a>Acesso a dados remotos

Muitas soluções baseadas na web modernas fazer uso dos serviços web, hospedadas por servidores web, para fornecer funcionalidade de cliente remoto a aplicativos. As operações que expõe um serviço web constituem uma API da web.

Aplicativos cliente devem ser capazes de usar a API da web sem saber como os dados ou operações que expõe a API são implementadas. Isso requer que a API age de acordo com os padrões comuns que permitem que um serviço de aplicativo e da web do cliente concordar com os formatos de dados para uso e a estrutura dos dados que são trocadas entre aplicativos cliente e o serviço web.

## <a name="introduction-to-representational-state-transfer"></a>Introdução ao Representational State Transfer

REST Representational State Transfer () é um estilo de arquitetura para a criação de sistemas distribuídos com base em hipermídia. Uma vantagem importante do modelo REST é que ele tem com base em padrões abertos e não associa a implementação do modelo ou os aplicativos cliente que acessá-lo para qualquer implementação específica. Portanto, um serviço web REST pode ser implementado usando o Microsoft ASP.NET Core MVC e podem estar desenvolvendo aplicativos cliente usando qualquer linguagem e o conjunto de ferramentas que pode gerar solicitações HTTP e analisar as respostas HTTP.

O modelo REST usa um esquema de navegação para representar objetos e serviços em uma rede, chamada de recursos. Sistemas que implementam REST geralmente usam o protocolo HTTP para transmitir solicitações para acessar esses recursos. Em tais sistemas, um aplicativo cliente envia uma solicitação na forma de um URI que identifica um recurso e um método HTTP (como GET, POST, PUT ou DELETE) que indica a operação a ser executada no recurso. O corpo da solicitação HTTP contém todos os dados necessários para executar a operação.

> [!NOTE]
> REST define um modelo de solicitação sem monitoração de estado. Portanto, as solicitações HTTP devem ser independentes e podem ocorrer em qualquer ordem.

A resposta de uma pausa solicitação faz uso de códigos de status HTTP padrão. Por exemplo, uma solicitação que retorna dados válido deve incluir o código de resposta HTTP 200 (Okey), enquanto uma solicitação que falha ao localizar ou excluir um recurso especificado deve retornar uma resposta que inclui o código de status HTTP 404 (não encontrado).

Uma API da web RESTful expõe um conjunto de recursos conectados e fornece as principais operações que permitem que um aplicativo manipular os recursos e navegar facilmente entre eles. Por esse motivo, os URIs que constituem uma API web RESTful típicos são orientado para os dados que ele expõe e use os recursos fornecidos pelo HTTP para operar em dados.

Os dados incluídos por um aplicativo cliente em uma solicitação HTTP e as mensagens de resposta correspondente do servidor web, podem ser apresentados em uma variedade de formatos, conhecidos como tipos de mídia. Quando um aplicativo cliente envia uma solicitação que retorna os dados no corpo de uma mensagem, ele pode especificar os tipos de mídia pode manipular o `Accept` cabeçalho da solicitação. Se o servidor web oferece suporte a esse tipo de mídia, ele pode responder com uma resposta que inclui o `Content-Type` cabeçalho que especifica o formato dos dados no corpo da mensagem. Em seguida, é responsabilidade do aplicativo cliente para analisar a mensagem de resposta e interpretar os resultados no corpo da mensagem adequadamente.

Para obter mais informações sobre REST, consulte [design API](/azure/architecture/best-practices/api-design/) e [implementação da API](/azure/architecture/best-practices/api-implementation/).

## <a name="consuming-restful-apis"></a>Consumindo APIs RESTful

O aplicativo móvel eShopOnContainers usa o padrão Model-View-ViewModel (MVVM) e os elementos do modelo de entidades o domínio usadas no aplicativo que representa o padrão. As classes do controlador e do repositório no aplicativo eShopOnContainers Referência aceitam e retornam muitos desses objetos de modelo. Portanto, eles são usados como objetos de transferência de dados (DTOs) que contêm todos os dados que são passados entre o aplicativo móvel e o microservices em contêineres. A principal vantagem de usar DTOs para passar dados e receber dados de um serviço web é que, ao transmitir mais dados em uma única chamada remota, o aplicativo pode reduzir o número de chamadas remotas que precisam ser feitas.

### <a name="making-web-requests"></a>Como fazer solicitações da Web

Os usos de aplicativo móvel eShopOnContainers o `HttpClient` classe para fazer solicitações sobre HTTP, com JSON que está sendo usado como o tipo de mídia. Essa classe fornece funcionalidade de forma assíncrona enviar solicitações HTTP e receber respostas HTTP de um URI identificado o recurso. O `HttpResponseMessage` classe representa uma mensagem de resposta HTTP recebida de uma API REST depois que foi feita uma solicitação HTTP. Ele contém informações sobre a resposta, incluindo o código de status, cabeçalhos e qualquer corpo. O `HttpContent` classe representa o corpo HTTP e cabeçalhos de conteúdo, como `Content-Type` e `Content-Encoding`. O conteúdo pode ser lido usando qualquer um do `ReadAs` métodos, como `ReadAsStringAsync` e `ReadAsByteArrayAsync`, dependendo do formato dos dados.

<a name="making_a_get_request" />

#### <a name="making-a-get-request"></a>Fazer uma solicitação GET

O `CatalogService` classe é usada para gerenciar o processo de recuperação de dados de microsserviço o catálogo. No `RegisterDependencies` método no `ViewModelLocator` classe, o `CatalogService` classe está registrada como um mapeamento de tipo em relação a `ICatalogService` tipo com o contêiner de injeção de dependência de Autofac. Em seguida, quando uma instância do `CatalogViewModel` classe é criada, seu construtor aceita um `ICatalogService` de tipo, que resolve Autofac, retornando uma instância do `CatalogService` classe. Para obter mais informações sobre injeção de dependência, consulte [Introdução a injeção de dependência](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

Figura 10-1 mostra a interação de classes que leem dados do catálogo do microsserviço catálogo de exibição o `CatalogView`.

[![](accessing-remote-data-images/catalogdata.png "Recuperando dados de microsserviço o catálogo")](accessing-remote-data-images/catalogdata-large.png#lightbox "recuperar dados de microsserviço o catálogo")

**Figura 10-1**: recuperando dados de microsserviço o catálogo

Quando o `CatalogView` navegada, o `OnInitialize` método o `CatalogViewModel` classe é chamada. Esse método recupera dados de catálogo de microsserviço catálogo, conforme demonstrado no exemplo de código a seguir:

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    ...  
    Products = await _productsService.GetCatalogAsync();  
    ...  
}
```

Este método chama o `GetCatalogAsync` método o `CatalogService` instância foi injetada o `CatalogViewModel` por Autofac. O seguinte exemplo de código mostra o `GetCatalogAsync` método:

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

Esse método cria o URI que identifica o recurso a solicitação será enviada para e usa o `RequestProvider` classe para invocar o método HTTP GET no recurso, antes de retornar os resultados para o `CatalogViewModel`. O `RequestProvider` classe contém funcionalidade que envia uma solicitação na forma de um URI que identifica um recurso, um método HTTP que indica a operação a ser executada no recurso, e um corpo que contém todos os dados necessários para executar a operação. Para obter informações sobre como o `RequestProvider` classe é injetada o `CatalogService class`, consulte [Introdução a injeção de dependência](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

O seguinte exemplo de código mostra o `GetAsync` método o `RequestProvider` classe:

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

Este método chama o `CreateHttpClient` método, que retorna uma instância do `HttpClient` classe com o conjunto de cabeçalhos apropriados. Ele envia uma solicitação GET assíncrona para o recurso identificado pelo URI, com a resposta sendo armazenada na `HttpResponseMessage` instância. O `HandleResponse` método, em seguida, é chamado, o que gera uma exceção se a resposta não incluir um código de status HTTP de sucesso. Em seguida, a resposta é lido como uma cadeia de caracteres convertida de JSON para um `CatalogRoot` de objeto e retornado para o `CatalogService`.

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

Esse método cria uma nova instância do `HttpClient` classe e define o `Accept` cabeçalho de todas as solicitações feitas pelo `HttpClient` instância para `application/json`, que indica que ele espera que o conteúdo de qualquer resposta sejam formatadas usando JSON. Se um token de acesso foi passado como um argumento para o `CreateHttpClient` método, ele é adicionado a `Authorization` cabeçalho de todas as solicitações feitas pelo `HttpClient` instância, prefixada com a cadeia de caracteres `Bearer`. Para obter mais informações sobre autorização, consulte [autorização](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

Quando o `GetAsync` método no `RequestProvider` classe chamadas `HttpClient.GetAsync`, o `Items` método o `CatalogController` classe no projeto Catalog.API é chamado, o que é mostrado no exemplo de código a seguir:

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

Esse método recupera os dados do catálogo do banco de dados SQL usando o EntityFramework e o retorna como uma mensagem de resposta que inclui um código de status HTTP de sucesso, e uma coleção de JSON formatado `CatalogItem` instâncias.

#### <a name="making-a-post-request"></a>Fazer uma solicitação POST

O `BasketService` classe é usada para gerenciar a recuperação de dados e o processo de atualização com o microsserviço da cesta. No `RegisterDependencies` método no `ViewModelLocator` classe, o `BasketService` classe está registrada como um mapeamento de tipo em relação a `IBasketService` tipo com o contêiner de injeção de dependência de Autofac. Em seguida, quando uma instância do `BasketViewModel` classe é criada, seu construtor aceita um `IBasketService` de tipo, que resolve Autofac, retornando uma instância do `BasketService `classe. Para obter mais informações sobre injeção de dependência, consulte [Introdução a injeção de dependência](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection).

Figura 10-2 mostra a interação de classes que enviar os dados do carrinho exibidos pelo `BasketView`, para o carrinho de microsserviço.

[![](accessing-remote-data-images/basketdata.png "Enviando dados para o carrinho de microsserviço")](accessing-remote-data-images/basketdata-large.png#lightbox "enviando dados para o microsserviço carrinho")

**Figura 10-2**: enviar dados para o microsserviço carrinho

Quando um item é adicionado ao carrinho de compras, o `ReCalculateTotalAsync` método o `BasketViewModel` classe é chamada. Este método atualiza o valor total de itens da cesta e envia os dados de carrinho de microsserviço carrinho, conforme demonstrado no exemplo de código a seguir:

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

Este método chama o `UpdateBasketAsync` método o `BasketService` instância foi injetada o `BasketViewModel` por Autofac. O método a seguir mostra o `UpdateBasketAsync` método:

```csharp
public async Task<CustomerBasket> UpdateBasketAsync(CustomerBasket customerBasket, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    string uri = builder.ToString();  
    var result = await _requestProvider.PostAsync(uri, customerBasket, token);  
    return result;  
}
```

Esse método cria o URI que identifica o recurso a solicitação será enviada para e usa o `RequestProvider` classe para invocar o método HTTP POST no recurso, antes de retornar os resultados para o `BasketViewModel`. Observe que um token de acesso, obtido IdentityServer durante o processo de autenticação, é necessário para autorizar solicitações de microsserviço o carrinho. Para obter mais informações sobre autorização, consulte [autorização](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

O exemplo de código a seguir mostra uma da `PostAsync` métodos de `RequestProvider` classe:

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

Este método chama o `CreateHttpClient` método, que retorna uma instância do `HttpClient` classe com o conjunto de cabeçalhos apropriados. Ele envia uma solicitação POST assíncrona para o recurso identificado pelo URI, com os dados serializados carrinho sendo enviados no formato JSON e a resposta sendo armazenados na `HttpResponseMessage` instância. O `HandleResponse` método, em seguida, é chamado, o que gera uma exceção se a resposta não incluir um código de status HTTP de sucesso. Em seguida, a resposta é lido como uma cadeia de caracteres convertida de JSON para um `CustomerBasket` de objeto e retornado para o `BasketService`. Para obter mais informações sobre o `CreateHttpClient` método, consulte [fazendo uma solicitação GET](#making_a_get_request).

Quando o `PostAsync` método no `RequestProvider` classe chamadas `HttpClient.PostAsync`, o `Post` método o `BasketController` classe no projeto Basket.API é chamado, o que é mostrado no exemplo de código a seguir:

```csharp
[HttpPost]  
public async Task<IActionResult> Post([FromBody]CustomerBasket value)  
{  
    var basket = await _repository.UpdateBasketAsync(value);  
    return Ok(basket);  
}
```

Esse método usa uma instância do `RedisBasketRepository` de classe para persistir os dados de compras para o cache Redis e o retorna como uma mensagem de resposta que inclui um código de status HTTP de êxito e JSON formatados `CustomerBasket` instância.

#### <a name="making-a-delete-request"></a>Fazer uma solicitação de exclusão

Figura 10-3 mostra as interações de classes que excluir dados de carrinho de microsserviço carrinho, para o `CheckoutView`.

![](accessing-remote-data-images/checkoutdata.png "Dados de exclusão de microsserviço o carrinho")

**Figura 10-3**: excluindo dados de microsserviço o carrinho

Quando o processo de check-out é chamado, o `CheckoutAsync` método o `CheckoutViewModel` classe é chamada. Esse método cria um novo pedido, antes de desmarcar o carrinho de compras, conforme demonstrado no exemplo de código a seguir:

```csharp
private async Task CheckoutAsync()  
{  
    ...  
    await _basketService.ClearBasketAsync(_shippingAddress.Id.ToString(), authToken);  
    ...  
}
```

Este método chama o `ClearBasketAsync` método o `BasketService` instância foi injetada o `CheckoutViewModel` por Autofac. O método a seguir mostra o `ClearBasketAsync` método:

```csharp
public async Task ClearBasketAsync(string guidUser, string token)  
{  
    UriBuilder builder = new UriBuilder(GlobalSetting.Instance.BasketEndpoint);  
    builder.Path = guidUser;  
    string uri = builder.ToString();  
    await _requestProvider.DeleteAsync(uri, token);  
}
```

Esse método cria o URI que identifica o recurso que a solicitação será enviada para e usa o `RequestProvider` classe para invocar o método HTTP DELETE no recurso. Observe que um token de acesso, obtido IdentityServer durante o processo de autenticação, é necessário para autorizar solicitações de microsserviço o carrinho. Para obter mais informações sobre autorização, consulte [autorização](~/xamarin-forms/enterprise-application-patterns/authentication-and-authorization.md#authorization).

O seguinte exemplo de código mostra o `DeleteAsync` método o `RequestProvider` classe:

```csharp
public async Task DeleteAsync(string uri, string token = "")  
{  
    HttpClient httpClient = CreateHttpClient(token);  
    await httpClient.DeleteAsync(uri);  
}
```

Este método chama o `CreateHttpClient` método, que retorna uma instância do `HttpClient` classe com o conjunto de cabeçalhos apropriados. Em seguida, ele envia uma solicitação de exclusão assíncrona para o recurso identificado pelo URI. Para obter mais informações sobre o `CreateHttpClient` método, consulte [fazendo uma solicitação GET](#making_a_get_request).

Quando o `DeleteAsync` método no `RequestProvider` classe chamadas `HttpClient.DeleteAsync`, o `Delete` método o `BasketController` classe no projeto Basket.API é chamado, o que é mostrado no exemplo de código a seguir:

```csharp
[HttpDelete("{id}")]  
public void Delete(string id)  
{  
    _repository.DeleteBasketAsync(id);  
}
```

Esse método usa uma instância do `RedisBasketRepository` classe para excluir os dados da cesta do cache Redis.

## <a name="caching-data"></a>Armazenando dados em cache

O desempenho de um aplicativo pode ser melhorado armazenando em cache dados acessados com frequência para armazenamento rápido está localizado para fechar o aplicativo. Se o armazenamento rápido está localizado próximo ao aplicativo que a fonte original, o cache pode melhorar significativamente a resposta quando a recuperação de dados.

A forma mais comum de cache é Read-through cache, onde um aplicativo recupera dados consultando o cache. Se os dados não estiverem no cache, ele tem recuperada do armazenamento de dados e adicionado ao cache. Aplicativos podem implementar cache Read-through com o padrão de cache-aside. Esse padrão determina se o item está atualmente no cache. Se o item não estiver no cache, ele tem lidas do repositório de dados e adicionado ao cache. Para obter mais informações, consulte o [Cache-Aside](/azure/architecture/patterns/cache-aside/) padrão.

> [!TIP]
> Dados de cache que são lidos com frequência e que são alterados com frequência. Esses dados podem ser adicionados ao cache sob demanda na primeira vez que ele é recuperado por um aplicativo. Isso significa que o aplicativo precisa para buscar os dados apenas uma vez o armazenamento de dados, e que o acesso subsequente pode ser atendido usando o cache.

Aplicativos distribuídos, como os eShopOnContainers Referência de aplicativo, devem fornecer um ou mais dos caches do seguintes:

-   Um cache compartilhado, que pode ser acessado por vários processos ou computadores.
-   Um cache privado, onde os dados são mantidos localmente no dispositivo que está executando o aplicativo.

O aplicativo móvel eShopOnContainers usa um cache privado, onde os dados são mantidos localmente no dispositivo que está executando uma instância do aplicativo. Para obter informações sobre o cache usado pelo aplicativo eShopOnContainers referência, consulte [Microservices .NET: arquitetura de aplicativos do .NET em contêineres](https://aka.ms/microservicesebook).

> [!TIP]
> Considere o cache como um repositório de dados temporários que pode desaparecer a qualquer momento. Certifique-se de que os dados são mantidos no armazenamento de dados original, bem como o cache. A possibilidade de perda de dados é minimizada, em seguida, se o cache ficar indisponível.

### <a name="managing-data-expiration"></a>Gerenciar a expiração de dados

Não é prático esperar que os dados armazenados em cache sempre sejam consistentes com os dados originais. Dados no repositório de dados originais podem ser alterados depois que ele é foi armazenado em cache, fazendo com que os dados em cache para se tornar obsoleta. Portanto, aplicativos devem implementar uma estratégia que ajuda a garantir que os dados no cache são tão atualizados quanto possível, mas pode também detectar e lidar com situações que podem surgir quando os dados no cache se tornou obsoletos. Mecanismos de cache mais habilitar o cache a ser configurado para expirar dados e, portanto, reduzem o período no qual os dados podem estar desatualizados.

> [!TIP]
> Um padrão de validade do conjunto de tempo ao configurar um cache. Vários caches implementam vencimento, o que invalida a dados e a remove do cache se ele não é acessado por um período especificado. No entanto, deve ter cuidado ao escolher o período de validade. Se ela se torna muito curto, dados expirará muito rapidamente e os benefícios do cache serão reduzidos. Se ela se torna muito longo, os riscos de dados obsoletos. Portanto, o tempo de expiração deve corresponder ao padrão de acesso para aplicativos que usam os dados.

Quando dados armazenados em cache expirarem, ela deve ser removida do cache e o aplicativo deve recuperar os dados dos dados originais armazenarem e colocá-lo novamente no cache.

Também é possível que um cache pode preencher se dados são permitidos por um período muito longo. Portanto, as solicitações para adicionar novos itens no cache podem ser necessárias para remover alguns itens em um processo conhecido como *remoção*. Serviços de Caching normalmente remover dados em uma base usados menos recentemente. No entanto, há outras políticas de remoção, incluindo usados mais recentemente e o primeiro a entrar, primeiro a sair. Para obter mais informações, consulte [guia cache](/azure/architecture/best-practices/caching/).

<a name="caching_images" />

### <a name="caching-images"></a>Cache de imagens

O aplicativo móvel eShopOnContainers consome imagens de produto remoto que se beneficiam de serem armazenados em cache. Essas imagens são exibidas pelo [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) controle e o `CachedImage` controle fornecido pelo [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) biblioteca.

O xamarin. Forms [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) controle oferece suporte ao cache de imagens baixadas. O cache é habilitado por padrão e armazenará a imagem localmente por 24 horas. Além disso, o tempo de expiração pode ser configurado com o [ `CacheValidity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CacheValidity/) propriedade. Para obter mais informações, consulte [cache de imagem baixada](~/xamarin-forms/user-interface/images.md#Image_Caching).

Do FFImageLoading `CachedImage` controle é uma substituição para o xamarin. Forms [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) controle, fornecendo propriedades adicionais que habilitar a funcionalidade de suplementar. Entre essa funcionalidade, o controle fornece cache configurável, ao erro de suporte e carregando os espaços reservados de imagens. O exemplo de código a seguir mostra como o aplicativo móvel eShopOnContainers usa o `CachedImage` controlar o `ProductTemplate`, que é o modelo de dados usado pelo [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) controlar o `CatalogView`:

```xaml
<ffimageloading:CachedImage
    Grid.Row="0"
    Source="{Binding PictureUri}"     
    Aspect="AspectFill">
    <ffimageloading:CachedImage.LoadingPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="default_campaign" />
            <On Platform="UWP, WinRT, WinPhone" Value="Assets/default_campaign.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.LoadingPlaceholder>
    <ffimageloading:CachedImage.ErrorPlaceholder>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="noimage" />
            <On Platform="UWP, WinRT, WinPhone" Value="Assets/noimage.png" />
        </OnPlatform>
    </ffimageloading:CachedImage.ErrorPlaceholder>
</ffimageloading:CachedImage>
```

O `CachedImage` conjuntos de controles de `LoadingPlaceholder` e `ErrorPlaceholder` propriedades para imagens específicas da plataforma. O `LoadingPlaceholder` propriedade especifica a imagem a ser exibida enquanto a imagem especificada pelo `Source` propriedade é recuperada e o `ErrorPlaceholder` propriedade especifica a imagem a ser exibida se ocorrer um erro ao tentar recuperar a imagem especificado pelo `Source` propriedade.

Como o nome implica, o `CachedImage` controle armazena em cache as imagens remotas no dispositivo durante o tempo especificado pelo valor da `CacheDuration` propriedade. Quando esse valor de propriedade não é definido explicitamente, o valor padrão de 30 dias é aplicado.

## <a name="increasing-resilience"></a>Aumentando a resiliência

Todos os aplicativos que se comunicam com os recursos e serviços remotos devem ser sensíveis a falhas transitórias. Falhas transitórias incluem a perda momentânea de conectividade de rede para serviços, a indisponibilidade temporária de um serviço ou tempos limite que surgem quando um serviço está ocupado. Essas falhas geralmente são autogerenciamento e se a ação é repetida após um intervalo adequado é provável que tenha êxito.

Falhas transitórias podem ter um grande impacto sobre a qualidade percebida de um aplicativo, mesmo se ela foi testada totalmente em todas as circunstâncias previsíveis. Para garantir que um aplicativo que se comunica com os serviços remotos opera de forma confiável, ele deve ser capaz de fazer o seguinte:

-   Detecte falhas quando eles ocorrem e determinam se as falhas são provavelmente transitório.
-   Repita a operação se determinar que a falha é a probabilidade de ser transitório e controlar o número de vezes que a operação foi repetida.
-   Use uma estratégia de repetição apropriada, que especifica o número de repetições, o atraso entre cada tentativa e as ações a serem seguidas depois de uma falha na tentativa.

Esse tratamento de falhas transitórias pode ser obtido com quebra automática de todas as tentativas de acessar um serviço remoto no código que implementa o padrão de repetição.

### <a name="retry-pattern"></a>Repita padrão

Se um aplicativo detectar uma falha ao tentar enviar uma solicitação para um serviço remoto, ele pode lidar com falhas em qualquer uma das seguintes maneiras:

-   Repetir a operação. O aplicativo pode repetir a solicitação com falha imediatamente.
-   Repetindo a operação após um atraso. O aplicativo deve esperar por uma quantidade adequada de tempo antes de tentar novamente a solicitação.
-   Cancelar a operação. O aplicativo deve cancelar a operação e uma exceção de relatório.

A estratégia de repetição deve ser ajustada para corresponder aos requisitos de negócios do aplicativo. Por exemplo, é importante otimizar a contagem de repetição e o intervalo para a operação de tentativas de repetição. Se a operação for parte de uma interação do usuário, o intervalo de repetição deve ser curto e apenas algumas novas tentativas para evitar fazer com que os usuários aguardar uma resposta. Se a operação for parte de um fluxo de trabalho de longa duração, em que cancelar ou reiniciar o fluxo de trabalho é cara ou demorada, é apropriado para esperar mais entre as tentativas e repita mais vezes.

> [!NOTE]
> Uma estratégia de repetição agressiva com atraso mínimo entre tentativas e um grande número de repetições, poderá prejudicar a um serviço remoto que esteja executando fechada ou a capacidade. Além disso, essa estratégia de repetição também pode afetar a capacidade de resposta do aplicativo se ele está tentando continuamente para executar uma operação com falha.

Se uma solicitação ainda falhar após um número de tentativas, é melhor para o aplicativo para evitar que solicitações adicionais para o mesmo recurso e relatar uma falha. Em seguida, após um período definido, o aplicativo pode fazer uma ou mais solicitações para o recurso para ver se elas são bem-sucedidas. Para obter mais informações, consulte [padrão de disjuntor](#circuit_breaker_pattern).

> [!TIP]
> Nunca implemente um mecanismo de repetição infinita. Use um número finito de tentativas de ou implementar o [disjuntor](/azure/architecture/patterns/circuit-breaker/) padrão para permitir que um serviço de recuperação.

O aplicativo móvel eShopOnContainers não implementar o padrão de repetição no momento ao fazer solicitações da web RESTful. No entanto, o `CachedImage` controle fornecido pelo [FFImageLoading](https://www.nuget.org/packages/Xamarin.FFImageLoading.Forms/) biblioteca oferece suporte ao tratamento de falhas transitórias repetindo o carregamento da imagem. Se o carregamento da imagem falhar, serão feitas tentativas adicionais. O número de tentativas é especificado pelo `RetryCount` propriedade e repetições ocorrerá após um atraso especificado pelo `RetryDelay` propriedade. Se esses valores de propriedade não são explicitamente definidas, padrão são aplicadas – 3 para o `RetryCount` propriedade e 250ms para o `RetryDelay` propriedade. Para obter mais informações sobre o `CachedImage` , consulte [cache imagens](#caching_images).

O aplicativo de referência eShopOnContainers implementar o padrão de repetição. Para obter mais informações, incluindo uma discussão sobre como combinar o padrão de repetição com o `HttpClient` de classe, consulte [Microservices .NET: arquitetura de aplicativos do .NET em contêineres](https://aka.ms/microservicesebook).

Para obter mais informações sobre o padrão de repetição, consulte o [novamente](/azure/architecture/patterns/retry/) padrão.

<a name="circuit_breaker_pattern" />

### <a name="circuit-breaker-pattern"></a>Padrão de Disjuntor

Em algumas situações, podem ocorrer falhas devido a eventos antecipados que levar mais tempo para corrigir. Essas falhas podem variar de uma perda parcial de conectividade para a falha completa de um serviço. Nessas situações, é inútil para um aplicativo repetir uma operação menor probabilidade de êxito e em vez disso, deve aceitar que a operação falhou e lidar com essa falha adequadamente.

O padrão de disjuntor pode impedir que um aplicativo repetidamente ao tentar executar uma operação que é provavelmente falharão, enquanto também permite o aplicativo detectar se a falha foi resolvida.

> [!NOTE]
> A finalidade do padrão de disjuntor é diferente do padrão de repetição. O padrão de repetição permite que um aplicativo repetir uma operação na expectativa de que ele terá êxito. O padrão de disjuntor impede que um aplicativo executando uma operação que é provavelmente falharão.

Um disjuntor atua como um proxy para operações que podem falhar. O proxy deve monitorar o número de falhas recentes que ocorreram e usar essas informações para decidir se deseja permitir que a operação para continuar, ou para retornar uma exceção imediatamente.

O aplicativo móvel eShopOnContainers não implementa o padrão de disjuntor no momento. No entanto, o eShopOnContainers faz. Para obter mais informações, consulte [Microservices .NET: arquitetura de aplicativos do .NET em contêineres](https://aka.ms/microservicesebook).

> [!TIP]
> Combine os padrões de disjuntor e tente novamente. Um aplicativo pode combinar os padrões de disjuntor e tente novamente usando o padrão de repetição para invocar uma operação por meio de um disjuntor. No entanto, a lógica de repetição deve ser sensível a qualquer exceção retornadas pelo disjuntor e abandonar tentativas de repetição se o disjuntor indica que uma falha não é transitória.

Para obter mais informações sobre o padrão de Disjuntor, consulte o [disjuntor](/azure/architecture/patterns/circuit-breaker/) padrão.

## <a name="summary"></a>Resumo

Muitas soluções baseadas na web modernas fazer uso dos serviços web, hospedadas por servidores web, para fornecer funcionalidade de cliente remoto a aplicativos. As operações que expõe um serviço web constituem uma API da web e aplicativos de cliente devem ser capazes de usar a API da web sem saber como os dados ou operações que expõe a API são implementadas.

O desempenho de um aplicativo pode ser melhorado armazenando em cache dados acessados com frequência para armazenamento rápido está localizado para fechar o aplicativo. Aplicativos podem implementar cache Read-through com o padrão de cache-aside. Esse padrão determina se o item está atualmente no cache. Se o item não estiver no cache, ele tem lidas do repositório de dados e adicionado ao cache.

Ao se comunicar com APIs da web, os aplicativos devem ser sensíveis a falhas transitórias. Falhas transitórias incluem a perda momentânea de conectividade de rede para serviços, a indisponibilidade temporária de um serviço ou tempos limite que surgem quando um serviço está ocupado. Essas falhas geralmente são autogerenciamento e se a ação é repetida após um intervalo adequado, em seguida, é provável que tenha êxito. Portanto, aplicativos devem incluir todas as tentativas de acessar uma API da web no código que implementa uma mecanismo de tratamento de falhas transitórias.


## <a name="related-links"></a>Links relacionados

- [Baixar eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
