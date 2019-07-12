---
title: Autenticação e autorização
description: Este capítulo explica como o aplicativo móvel do eShopOnContainers executa autenticação e autorização contra os microsserviços em contêineres.
ms.prod: xamarin
ms.assetid: e3f27b4c-f7f5-4839-a48c-30bcb919c59e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2017
ms.openlocfilehash: efaea24e559aa2f3bdfd87c1c083ce1d777dbb3f
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832163"
---
# <a name="authentication-and-authorization"></a>Autenticação e autorização

A autenticação é o processo de obter credenciais de identificação, como o nome e a senha de um usuário e validar essas credenciais em uma autoridade. Se as credenciais forem válidas, a entidade que enviou as credenciais é considerada uma identidade autenticada. Depois que uma identidade foi autenticada, um processo de autorização determina se essa identidade tem acesso a um determinado recurso.

Há muitas abordagens para integrar a autenticação e autorização em um aplicativo xamarin. Forms que se comunica com um aplicativo web do ASP.NET MVC, incluindo o uso do ASP.NET Core Identity, provedores de autenticação externa, como Microsoft, Google, Middleware do Facebook ou Twitter e autenticação. O aplicativo móvel do eShopOnContainers executa autenticação e autorização com um microsserviço de identidade em contêineres que usa 4 IdentityServer. O aplicativo móvel solicita tokens de segurança do IdentityServer, para autenticar um usuário ou para acessar um recurso. Para o IdentityServer para emitir tokens em nome do usuário, o usuário deve entrar para IdentityServer. No entanto, o IdentityServer não fornece uma interface do usuário ou o banco de dados para autenticação. Portanto, no aplicativo eShopOnContainers de referência, o ASP.NET Core Identity é usado para essa finalidade.

## <a name="authentication"></a>Autenticação

Autenticação é necessária quando um aplicativo precisa saber a identidade do usuário atual. Mecanismo de principal do ASP.NET Core para identificar os usuários é o sistema de associação do ASP.NET Core Identity, que armazena informações de usuário em um armazenamento de dados configurado pelo desenvolvedor. Normalmente, esse armazenamento de dados será um repositório do EntityFramework, embora repositórios personalizados ou pacotes de terceiros podem ser usados para armazenar informações de identidade no armazenamento do Azure, Azure Cosmos DB ou outros locais.

Para cenários de autenticação que usam um usuário local de armazenamento de dados, e que manter as informações de identidade entre solicitações por meio de cookies (como é comum em aplicativos web ASP.NET MVC), o ASP.NET Core Identity é uma solução adequada. No entanto, os cookies nem sempre são uma maneira natural de persistir e transmitir dados. Por exemplo, um aplicativo web ASP.NET Core que expõe pontos de extremidade RESTful são acessados a partir de um aplicativo móvel normalmente precisa usar a autenticação de token de portador, desde que os cookies não podem ser usados neste cenário. No entanto, os tokens de portador facilmente podem ser recuperados e incluídos no cabeçalho de autorização de solicitações da web feita no aplicativo móvel.

### <a name="issuing-bearer-tokens-using-identityserver-4"></a>Emissão de Tokens de portador usando o IdentityServer 4

[4 IdentityServer](https://github.com/IdentityServer/IdentityServer4) é uma estrutura de código-fonte aberto OAuth 2.0 e OpenID Connect para ASP.NET Core, que pode ser usado para muitos cenários de autenticação e autorização incluindo emitir tokens de segurança para usuários do ASP.NET Core Identity locais.

> [!NOTE]
> OpenID Connect e OAuth 2.0 são muito semelhantes, embora tenha responsabilidades diferentes.

O OpenID Connect é uma camada de autenticação sobre o protocolo OAuth 2.0. OAuth 2 é um protocolo que permite que os aplicativos solicitar tokens de acesso de um serviço de token de segurança e usá-los para se comunicar com as APIs. Essa delegação reduz a complexidade em aplicativos cliente e APIs, pois podem ser centralizados de autenticação e autorização.

A combinação de OAuth 2.0 e OpenID Connect combinar as duas preocupações de segurança fundamentais de autenticação e acesso à API e IdentityServer 4 é uma implementação desses protocolos.

Em aplicativos que usam a comunicação direta de cliente e microsserviço, como o aplicativo eShopOnContainers de referência, um microsserviço de autenticação dedicado atuando como um Security Token Service (STS) pode ser usado para autenticar usuários, conforme mostrado na Figura 9-1. Para obter mais informações sobre a comunicação direta de cliente e microsserviço, consulte [comunicação entre cliente e Microsserviços](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md#communication_between_client_and_microservices).

![](authentication-and-authorization-images/authentication.png "Autenticação por um microsserviço de autenticação dedicado")

**Figura 9-1:** Autenticação por um microsserviço de autenticação dedicado

O aplicativo móvel do eShopOnContainers se comunica com o microsserviço de identidade, que usa o IdentityServer 4 para realizar a autenticação e controle de acesso para as APIs. Portanto, o aplicativo móvel solicita tokens do IdentityServer, para autenticar um usuário ou para acessar um recurso:

-   Autenticar usuários com IdentityServer é obtido com o aplicativo móvel solicitando uma *identidade* token, que representa o resultado de um processo de autenticação. No mínimo, ele contém um identificador para o usuário e obter informações sobre como e quando o usuário autenticado. Ele também pode conter dados de identidade adicional.
-   Acessando um recurso com IdentityServer é obtido com o aplicativo móvel solicitando uma *acesso* token, que permite o acesso a um recurso da API. Os clientes solicitam tokens de acesso e encaminhá-los para a API. Tokens de acesso contêm informações sobre o cliente e o usuário (se presente). APIs, em seguida, usam essas informações para autorizar o acesso aos seus dados.

> [!NOTE]
> Um cliente deve ser registrado com IdentityServer antes que ele pode solicitar tokens.

### <a name="adding-identityserver-to-a-web-application"></a>Adicionando o IdentityServer a um aplicativo Web

Em ordem para um aplicativo web ASP.NET Core usam o IdentityServer 4, ele deve ser adicionado à solução do Visual Studio do aplicativo da web. Para obter mais informações, consulte [visão geral](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) na documentação do IdentityServer.

Depois que o IdentityServer está incluído na solução do Visual Studio do aplicativo da web, ele deve ser adicionado à solicitação HTTP do aplicativo web no pipeline, de processamento para que ele possa atender solicitações para pontos de extremidade do OAuth 2.0 e OpenID Connect. Isso é feito na `Configure` método no aplicativo da web `Startup` de classe, conforme demonstrado no exemplo de código a seguir:

```csharp
public void Configure(  
    IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)  
{  
    ...  
    app.UseIdentity();  
    ...  
}
```

Ordem é importante no pipeline de processamento de solicitação HTTP do aplicativo da web. Portanto, o IdentityServer deve ser adicionado ao pipeline antes que a estrutura de interface do usuário que implementa a tela de logon.

### <a name="configuring-identityserver"></a>Configurando o IdentityServer

IdentityServer deve ser configurado na `ConfigureServices` método no aplicativo da web `Startup` classe chamando o `services.AddIdentityServer` método, conforme demonstrado no exemplo de código do aplicativo eShopOnContainers de referência:

```csharp
public void ConfigureServices(IServiceCollection services)  
{  
    ...  
    services.AddIdentityServer(x => x.IssuerUri = "null")  
        .AddSigningCredential(Certificate.Get())                 
        .AddAspNetIdentity<ApplicationUser>()  
        .AddConfigurationStore(builder =>  
            builder.UseSqlServer(connectionString, options =>  
                options.MigrationsAssembly(migrationsAssembly)))  
        .AddOperationalStore(builder =>  
            builder.UseSqlServer(connectionString, options =>  
                options.MigrationsAssembly(migrationsAssembly)))  
        .Services.AddTransient<IProfileService, ProfileService>();  
}
```

Depois de chamar o `services.AddIdentityServer` método, APIs fluentes adicionais são chamadas para configurar o seguinte:

-   Credenciais usadas para assinatura.
-   Recursos de identidade e de API que os usuários podem solicitar acessem.
-   Clientes que se conectarem ao solicitar tokens.
-   Identidade do ASP.NET Core.

> [!TIP]
> Carregar dinamicamente a configuração de IdentityServer 4. APIs do IdentityServer 4 permitem configurar o IdentityServer de uma lista na memória de objetos de configuração. No aplicativo eShopOnContainers de referência, essas coleções na memória são embutidos em código no aplicativo. Entretanto, em cenários de produção pode ser carregados dinamicamente de um arquivo de configuração ou de um banco de dados.

Para obter informações sobre como configurar o IdentityServer para usar a identidade do ASP.NET Core, consulte [usando o ASP.NET Core Identity](https://identityserver4.readthedocs.io/en/latest/quickstarts/8_aspnet_identity.html) na documentação do IdentityServer.

#### <a name="configuring-api-resources"></a>Configurando recursos de API

Ao configurar os recursos da API, o `AddInMemoryApiResources` método espera um `IEnumerable<ApiResource>` coleção. O seguinte exemplo de código mostra o `GetApis` aplicativo de referência de método que fornece esta coleção no eShopOnContainers:

```csharp
public static IEnumerable<ApiResource> GetApis()  
{  
    return new List<ApiResource>  
    {  
        new ApiResource("orders", "Orders Service"),  
        new ApiResource("basket", "Basket Service")  
    };  
}
```

Esse método Especifica que o IdentityServer deve proteger as ordens e APIs de carrinho de compras. Portanto, o IdentityServer acesso gerenciado tokens será necessários ao fazer chamadas para essas APIs. Para obter mais informações sobre o `ApiResource` de tipo, consulte [recurso da API](https://identityserver4.readthedocs.io/en/latest/reference/api_resource.html) na documentação do IdentityServer 4.

#### <a name="configuring-identity-resources"></a>Configurando recursos de identidade

Ao configurar recursos de identidade, o `AddInMemoryIdentityResources` método espera um `IEnumerable<IdentityResource>` coleção. Recursos de identidade são dados, como a ID de usuário, nome ou endereço de email. Cada recurso de identidade tem um nome exclusivo e tipos de declarações arbitrárias podem ser atribuídos a ele, que serão incluídas no token de identidade do usuário. O seguinte exemplo de código mostra o `GetResources` aplicativo de referência de método que fornece esta coleção no eShopOnContainers:

```csharp
public static IEnumerable<IdentityResource> GetResources()  
{  
    return new List<IdentityResource>  
    {  
        new IdentityResources.OpenId(),  
        new IdentityResources.Profile()  
    };  
}
```

A especificação do OpenID Connect especifica alguns [recursos de identidade padrão](https://openid.net/specs/openid-connect-core-1_0.html#ScopeClaims). O requisito mínimo é que o suporte é fornecido para emitir uma ID exclusiva para os usuários. Isso é obtido com a exposição a `IdentityResources.OpenId` recursos de identidade.

> [!NOTE]
> O `IdentityResources` classe dá suporte a todos os escopos definidos na especificação OpenID Connect (openid, email, perfil, telefone e endereço).

IdentityServer também dá suporte para definir recursos de identidade personalizada. Para obter mais informações, consulte [definindo recursos de identidade personalizada](http://docs.identityserver.io/en/latest/topics/resources.html#defining-custom-identity-resources) na documentação do IdentityServer. Para obter mais informações sobre o `IdentityResource` de tipo, consulte [recurso de identidade](https://identityserver4.readthedocs.io/en/latest/reference/identity_resource.html) na documentação do IdentityServer 4.

#### <a name="configuring-clients"></a>Configuração de clientes

Os clientes são aplicativos que podem solicitar tokens de IdentityServer. Normalmente, as configurações a seguir devem ser definidas para cada cliente, no mínimo:

-   Uma ID exclusiva do cliente.
-   As interações permitidas com o serviço de token (conhecido como o tipo de concessão).
-   O local em que os tokens de acesso e identidade são enviadas para (conhecido como um URI de redirecionamento).
-   Uma lista de recursos que o cliente tem permissão para acessar (conhecidos como escopos).

Ao configurar clientes, o `AddInMemoryClients` método espera um `IEnumerable<Client>` coleção. O exemplo de código a seguir mostra a configuração para o aplicativo móvel do eShopOnContainers no `GetClients` aplicativo de referência de método que fornece esta coleção no eShopOnContainers:

```csharp
public static IEnumerable<Client> GetClients(Dictionary<string,string> clientsUrl)
{
    return new List<Client>
    {
        ...
        new Client
        {
            ClientId = "xamarin",
            ClientName = "eShop Xamarin OpenId Client",
            AllowedGrantTypes = GrantTypes.Hybrid,
            ClientSecrets =
            {
                new Secret("secret".Sha256())
            },
            RedirectUris = { clientsUrl["Xamarin"] },
            RequireConsent = false,
            RequirePkce = true,
            PostLogoutRedirectUris = { $"{clientsUrl["Xamarin"]}/Account/Redirecting" },
            AllowedCorsOrigins = { "http://eshopxamarin" },
            AllowedScopes = new List<string>
            {
                IdentityServerConstants.StandardScopes.OpenId,
                IdentityServerConstants.StandardScopes.Profile,
                IdentityServerConstants.StandardScopes.OfflineAccess,
                "orders",
                "basket"
            },
            AllowOfflineAccess = true,
            AllowAccessTokensViaBrowser = true
        },
        ...
    };
}
```

Essa configuração especifica os dados para as seguintes propriedades:

-   `ClientId`: Uma ID exclusiva para o cliente.
-   `ClientName`: O nome de exibição de cliente, que é usado para registro em log e a tela de consentimento.
-   `AllowedGrantTypes`: Especifica como um cliente deseja interagir com IdentityServer. Para obter mais informações, consulte [Configurando o fluxo de autenticação](#configuring_the_authentication_flow).
-   `ClientSecrets`: Especifica as credenciais de segredo do cliente que são usadas ao solicitar tokens do ponto de extremidade token.
-   `RedirectUris`: Especifica os URIs permitidos para o qual retornar códigos de autorização ou tokens.
-   `RequireConsent`: Especifica se a uma tela de consentimento é necessária.
-   `RequirePkce`: Especifica se os clientes que usam um código de autorização devem enviar uma chave de prova.
-   `PostLogoutRedirectUris`: Especifica os URIs permitidos para redirecionar para após o logoff.
-   `AllowedCorsOrigins`: Especifica a origem do cliente para que o IdentityServer pode permitir chamadas entre origens da origem.
-   `AllowedScopes`: Especifica os recursos que o cliente tem acesso ao. Por padrão, um cliente não tem acesso a recursos.
-   `AllowOfflineAccess`: Especifica se o cliente pode solicitar tokens de atualização.

<a name="configuring_the_authentication_flow" />

#### <a name="configuring-the-authentication-flow"></a>Configurando o fluxo de autenticação

O fluxo de autenticação entre um cliente e o IdentityServer pode ser configurado especificando os tipos de concessão no `Client.AllowedGrantTypes` propriedade. As especificações de OAuth 2.0 e OpenID Connect definem um número de fluxos de autenticação, incluindo:

-   Implícito. Esse fluxo é otimizado para aplicativos baseados em navegador e deve ser usado para somente a autenticação de usuário ou solicitações de token de acesso e autenticação. Todos os tokens são transmitidos por meio do navegador e, portanto, recursos avançados como tokens de atualização não são permitidos.
-   Código de autorização. Esse fluxo fornece a capacidade de recuperar tokens em um canal de retorno, em vez do canal de front-navegador, e também dar suporte a autenticação de cliente.
-   Híbrido. Esse fluxo é uma combinação de implícito e tipos de concessão de código de autorização. O token de identidade é transmitido por meio do canal de navegador e contém a resposta do protocolo assinados juntamente com outros artefatos, como o código de autorização. Após a validação bem-sucedida da resposta, o canal de retorno deve ser usado para recuperar o acesso e token de atualização.

> [!TIP]
> Use o fluxo de autenticação híbrida. O fluxo de autenticação híbrida atenua um número de ataques que se aplicam ao canal de navegador e é o fluxo recomendado para aplicativos nativos que deseja recuperar tokens de acesso (e, possivelmente, tokens de atualização).

Para obter mais informações sobre fluxos de autenticação, consulte [tipos de concessão](https://identityserver4.readthedocs.io/en/latest/topics/grant_types.html) na documentação do IdentityServer 4.

### <a name="performing-authentication"></a>Executar a autenticação

Para o IdentityServer para emitir tokens em nome do usuário, o usuário deve entrar para IdentityServer. No entanto, o IdentityServer não fornece uma interface do usuário ou o banco de dados para autenticação. Portanto, no aplicativo eShopOnContainers de referência, o ASP.NET Core Identity é usado para essa finalidade.

O aplicativo móvel do eShopOnContainers autentica com IdentityServer com o fluxo de autenticação híbrida, que é ilustrado na Figura 9-2.

![](authentication-and-authorization-images/sign-in.png "Visão geral de alto nível do processo de entrada")

**Figura 9-2:** Visão geral de alto nível do processo de entrada

É feita uma solicitação de entrada para `<base endpoint>:5105/connect/authorize`. Seguindo uma autenticação bem-sucedida, o IdentityServer retorna uma resposta de autenticação que contém um código de autorização e um token de identidade. O código de autorização é enviado para `<base endpoint>:5105/connect/token`, que responde com tokens de atualização, identidade e acesso.

O eShopOnContainers aplicativo móvel sinais-out de IdentityServer enviando uma solicitação para `<base endpoint>:5105/connect/endsession`, com parâmetros adicionais. Após a saída, o IdentityServer responde enviando um URI de redirecionamento de logoff postagem de volta para o aplicativo móvel. Figura 9-3 ilustra esse processo.

![](authentication-and-authorization-images/sign-out.png "Visão geral de alto nível do processo de saída")

**Figura 9-3:** Visão geral de alto nível do processo de saída

No aplicativo móvel do eShopOnContainers, a comunicação com IdentityServer é realizada pela `IdentityService` de classe que implementa o `IIdentityService` interface. Essa interface Especifica que a classe de implementação deve fornecer `CreateAuthorizationRequest`, `CreateLogoutRequest`, e `GetTokenAsync` métodos.

#### <a name="signing-in"></a>Entrar

Quando o usuário toca a **logon** botão a `LoginView`, o `SignInCommand` no `LoginViewModel` classe é executada, que por sua vez executa o `SignInAsync` método. O seguinte exemplo de código mostra esse método:

```csharp
private async Task SignInAsync()  
{  
    ...  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    IsLogin = true;  
    ...  
}
```

Este método invoca o `CreateAuthorizationRequest` método no `IdentityService` classe, que é mostrado no exemplo de código a seguir:

```csharp
public string CreateAuthorizationRequest()
{
    // Create URI to authorization endpoint
    var authorizeRequest = new AuthorizeRequest(GlobalSetting.Instance.IdentityEndpoint);

    // Dictionary with values for the authorize request
    var dic = new Dictionary<string, string>();
    dic.Add("client_id", GlobalSetting.Instance.ClientId);
    dic.Add("client_secret", GlobalSetting.Instance.ClientSecret); 
    dic.Add("response_type", "code id_token");
    dic.Add("scope", "openid profile basket orders locations marketing offline_access");
    dic.Add("redirect_uri", GlobalSetting.Instance.IdentityCallback);
    dic.Add("nonce", Guid.NewGuid().ToString("N"));
    dic.Add("code_challenge", CreateCodeChallenge());
    dic.Add("code_challenge_method", "S256");

    // Add CSRF token to protect against cross-site request forgery attacks.
    var currentCSRFToken = Guid.NewGuid().ToString("N");
    dic.Add("state", currentCSRFToken);

    var authorizeUri = authorizeRequest.Create(dic); 
    return authorizeUri;
}

```

Esse método cria o URI para do IdentityServer [ponto de extremidade de autorização](https://identityserver4.readthedocs.io/en/latest/endpoints/authorize.html), com os parâmetros necessários. O ponto de extremidade de autorização está no `/connect/authorize` na porta 5105 do ponto de extremidade base exposto como uma configuração de usuário. Para obter mais informações sobre as configurações do usuário, consulte [gerenciamento de configuração](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> A superfície de ataque do aplicativo móvel do eShopOnContainers é reduzida, Implementando a chave de prova para a extensão de código de câmbio (PKCE) para OAuth. PKCE protege o código de autorização de que está sendo usado, se ele for interceptado. Isso é feito pelo cliente, gerando um verificador de segredo, um hash que é passado na solicitação de autorização, e que está presente sem hash quando resgatar o código de autorização. Para obter mais informações sobre PKCE, consulte [chave de prova para código Exchange por clientes públicos OAuth](https://tools.ietf.org/html/rfc7636) no site da Internet Engineering Task Force.

O URI retornado é armazenado na `LoginUrl` propriedade do `LoginViewModel` classe. Quando o `IsLogin` propriedade se torna `true`, o [ `WebView` ](xref:Xamarin.Forms.WebView) no `LoginView` se torna visível. O `WebView` associa dados seu [ `Source` ](xref:Xamarin.Forms.WebView.Source) propriedade para o `LoginUrl` propriedade do `LoginViewModel` classe e, portanto, faz uma solicitação de entrada para IdentityServer quando o `LoginUrl` estiver definida como Ponto de extremidade de autorização do IdentityServer. Quando o IdentityServer recebe essa solicitação e o usuário não está autenticado, o `WebView` será redirecionado à página de logon configurado, o que é mostrada na Figura 9-4.

![](authentication-and-authorization-images/login.png "Página de logon exibida pelo WebView")

**Figura 9-4:** Página de logon exibida pelo WebView

Depois que o logon for concluído, o [ `WebView` ](xref:Xamarin.Forms.WebView) será redirecionado para um URI de retorno. Isso `WebView` navegação fará com que o `NavigateAsync` método o `LoginViewModel` classe a ser executado, que é mostrado no exemplo de código a seguir:

```csharp
private async Task NavigateAsync(string url)  
{  
    ...  
    var authResponse = new AuthorizeResponse(url);  
    if (!string.IsNullOrWhiteSpace(authResponse.Code))  
    {  
        var userToken = await _identityService.GetTokenAsync(authResponse.Code);  
        string accessToken = userToken.AccessToken;  

        if (!string.IsNullOrWhiteSpace(accessToken))  
        {  
            Settings.AuthAccessToken = accessToken;  
            Settings.AuthIdToken = authResponse.IdentityToken;  

            await NavigationService.NavigateToAsync<MainViewModel>();  
            await NavigationService.RemoveLastFromBackStackAsync();  
        }  
    }  
    ...  
}
```

Esse método analisa a resposta de autenticação que está contida no URI de retorno, e desde que um código de autorização válido estiver presente, ele faz uma solicitação para do IdentityServer [ponto de extremidade token](https://identityserver4.readthedocs.io/en/latest/endpoints/token.html), passando o código de autorização, o Verificador de segredo PKCE e outras os parâmetros necessários. O ponto de extremidade de token está no `/connect/token` na porta 5105 do ponto de extremidade base exposto como uma configuração de usuário. Para obter mais informações sobre as configurações do usuário, consulte [gerenciamento de configuração](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!TIP]
> Valide o retorno de URIs. Embora o aplicativo móvel do eShopOnContainers não valida o URI de retorno, a prática recomendada é validar que o URI de retorno se refere a um local conhecido, para evitar ataques de redirecionamento aberto.

Se o ponto de extremidade de token recebe um código de autorização válido e o verificador de segredo PKCE, ele responde com um token de acesso, o token de identidade e o token de atualização. O token de acesso (o que permite o acesso aos recursos da API) e o token de identidade, em seguida, são armazenadas como configurações de aplicativo e navegação de página é executada. Portanto, o efeito geral no aplicativo móvel do eShopOnContainers é isso: desde que os usuários são capazes de se autenticar com êxito com IdentityServer, ele navega para o `MainView` página, que é um [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) que exibe o `CatalogView` como seu guia selecionada.

Para obter informações sobre navegação de página, consulte [navegação](~/xamarin-forms/enterprise-application-patterns/navigation.md). Para obter informações sobre como [ `WebView` ](xref:Xamarin.Forms.WebView) navegação faz com que um método de modelo de exibição ser executado, consulte [usando comportamentos de navegação invocar](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors). Para obter informações sobre as configurações do aplicativo, consulte [gerenciamento de configuração](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> O eShopOnContainers também permite que uma entrada fictício quando o aplicativo é configurado para usar os serviços fictícios no `SettingsView`. Nesse modo, o aplicativo não se comunica com IdentityServer, em vez disso, permitindo que o usuário entrar usando as credenciais.

#### <a name="signing-out"></a>Signing-out

Quando o usuário toca a **fazer logoff** botão na `ProfileView`, o `LogoutCommand` no `ProfileViewModel` classe é executada, que por sua vez executa o `LogoutAsync` método. Esse método executa a navegação de página para o `LoginView` página, passando um `LogoutParameter` instância definida como `true` como um parâmetro. Para obter mais informações sobre como passar parâmetros durante a navegação de página, consulte [passando parâmetros durante a navegação](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation).

Quando uma exibição é criada e navegada, o `InitializeAsync` método de modelo de exibição associado do modo de exibição é executado, o que, em seguida, executa o `Logout` método da `LoginViewModel` classe, que é mostrado no exemplo de código a seguir:

```csharp
private void Logout()  
{  
    var authIdToken = Settings.AuthIdToken;  
    var logoutRequest = _identityService.CreateLogoutRequest(authIdToken);  

    if (!string.IsNullOrEmpty(logoutRequest))  
    {  
        // Logout  
        LoginUrl = logoutRequest;  
    }  
    ...  
}
```

Este método invoca o `CreateLogoutRequest` método no `IdentityService` classe, passando o token de identidade é recuperado das configurações de aplicativo como um parâmetro. Para obter mais informações sobre as configurações do aplicativo, consulte [gerenciamento de configuração](~/xamarin-forms/enterprise-application-patterns/configuration-management.md). O seguinte exemplo de código mostra o método `CreateLogoutRequest`:

```csharp
public string CreateLogoutRequest(string token)  
{  
    ...  
    return string.Format("{0}?id_token_hint={1}&post_logout_redirect_uri={2}",   
        GlobalSetting.Instance.LogoutEndpoint,  
        token,  
        GlobalSetting.Instance.LogoutCallback);  
}
```

Esse método cria o URI do IdentityServer [encerrar o ponto de extremidade de sessão](https://identityserver4.readthedocs.io/en/latest/endpoints/endsession.html#refendsession), com os parâmetros necessários. O ponto de extremidade de sessão final está no `/connect/endsession` na porta 5105 do ponto de extremidade base exposto como uma configuração de usuário. Para obter mais informações sobre as configurações do usuário, consulte [gerenciamento de configuração](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

O URI retornado é armazenado na `LoginUrl` propriedade do `LoginViewModel` classe. Enquanto o `IsLogin` é de propriedade `true`, o [ `WebView` ](xref:Xamarin.Forms.WebView) no `LoginView` está visível. O `WebView` associa dados seu [ `Source` ](xref:Xamarin.Forms.WebView.Source) propriedade para o `LoginUrl` propriedade do `LoginViewModel` classe e, portanto, faz uma solicitação de saída para o IdentityServer quando o `LoginUrl` estiver definida como Ponto de extremidade do IdentityServer final sessão. Quando o IdentityServer recebe essa solicitação, desde que o usuário está conectado, ocorre logout. Autenticação é controlada com um cookie gerenciado pelo middleware de autenticação de cookie do ASP.NET Core. Portanto, saindo do IdentityServer remove o cookie de autenticação e envia um redirecionamento de logoff de postagem de URI de volta para o cliente.

No aplicativo móvel, o [ `WebView` ](xref:Xamarin.Forms.WebView) será redirecionado para o URI de redirecionamento de logoff post. Isso `WebView` navegação fará com que o `NavigateAsync` método o `LoginViewModel` classe a ser executado, que é mostrado no exemplo de código a seguir:

```csharp
private async Task NavigateAsync(string url)  
{  
    ...  
    Settings.AuthAccessToken = string.Empty;  
    Settings.AuthIdToken = string.Empty;  
    IsLogin = false;  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    ...  
}
```

Esse método limpa o token de identidade e o token de acesso de configurações do aplicativo e define a `IsLogin` propriedade para `false`, que faz com que o [ `WebView` ](xref:Xamarin.Forms.WebView) no `LoginView` página fique invisível . Por fim, o `LoginUrl` estiver definida como o URI de IdentityServer [ponto de extremidade de autorização](https://identityserver4.readthedocs.io/en/latest/endpoints/authorize.html), com os parâmetros necessários, em preparação para a próxima vez, o usuário inicia uma entrada.

Para obter informações sobre navegação de página, consulte [navegação](~/xamarin-forms/enterprise-application-patterns/navigation.md). Para obter informações sobre como [ `WebView` ](xref:Xamarin.Forms.WebView) navegação faz com que um método de modelo de exibição ser executado, consulte [usando comportamentos de navegação invocar](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors). Para obter informações sobre as configurações do aplicativo, consulte [gerenciamento de configuração](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> EShopOnContainers também permite uma simulação saída quando o aplicativo está configurado para usar serviços fictícios em SettingsView. Nesse modo, o aplicativo não se comunica com IdentityServer e em vez disso, limpa todos os tokens armazenados da configurações do aplicativo.

<a name="authorization" />

## <a name="authorization"></a>Autorização

Após a autenticação, da web do ASP.NET Core APIs geralmente precisam autorizar o acesso, que permite que um serviço disponibilize APIs disponíveis para alguns usuários autenticados, mas não para todos.

Restringindo o acesso a uma rota do ASP.NET Core MVC pode ser obtido por meio da aplicação de um atributo Authorize a um controlador ou ação, o que limita o acesso ao controlador ou ação para usuários autenticados, conforme mostrado no exemplo de código a seguir:

```csharp
[Authorize]  
public class BasketController : Controller  
{  
    ...  
}
```

Se um usuário não autorizado tentar acessar um controlador ou ação que é marcada com o `Authorize` atributo, a estrutura do MVC retorna um código de status HTTP (não autorizado) 401.

> [!NOTE]
> Parâmetros podem ser especificados no `Authorize` atributo para restringir uma API para usuários específicos. Para obter mais informações, consulte [autorização](/aspnet/core/security/authorization/introduction/).

IdentityServer pode ser integrado no fluxo de trabalho de autorização para que os tokens de acesso, ele fornece autorização de controle. Essa abordagem é mostrada na Figura 9-5.

![](authentication-and-authorization-images/authorization.png "Autorização por um token de acesso")

**Figura 9-5:** Autorização por um token de acesso

O aplicativo móvel do eShopOnContainers se comunica com o microsserviço de identidade e solicita um token de acesso como parte do processo de autenticação. O token de acesso é então encaminhado para as APIs expostas pelos microsserviços de ordenação e carrinho de compras como parte das solicitações de acesso. Tokens de acesso contêm informações sobre o cliente e o usuário. APIs, em seguida, usam essas informações para autorizar o acesso aos seus dados. Para obter informações sobre como configurar o IdentityServer para proteger as APIs, consulte [configurar recursos da API](#configuring-api-resources).

### <a name="configuring-identityserver-to-perform-authorization"></a>Configurando o IdentityServer para executar a autorização

Para executar a autorização com IdentityServer, seu middleware de autorização deve ser adicionado ao pipeline de solicitação HTTP do aplicativo da web. O middleware é adicionado a `ConfigureAuth` método no aplicativo da web `Startup` classe, que é invocado do `Configure` método e é demonstrado no exemplo de código do aplicativo eShopOnContainers de referência:

```csharp
protected virtual void ConfigureAuth(IApplicationBuilder app)  
{  
    var identityUrl = Configuration.GetValue<string>("IdentityUrl");  
    app.UseIdentityServerAuthentication(new IdentityServerAuthenticationOptions  
    {  
        Authority = identityUrl.ToString(),  
        ScopeName = "basket",  
        RequireHttpsMetadata = false  
    });  
} 
```

Esse método garante que a API só pode ser acessada com um token de acesso válido. O middleware valida o token de entrada para garantir que ele seja enviado de um emissor confiável e valida que o token é válido a ser usado com a API que recebe. Portanto, navegando até o controlador de carrinho de compras ou de ordenação retornará um 401 (não autorizado) código de status HTTP, indicando que um token de acesso é necessário.

> [!NOTE]
> Middleware de autorização do IdentityServer deve ser adicionado ao pipeline de solicitação HTTP do aplicativo da web antes de adicionar MVC com `app.UseMvc()` ou `app.UseMvcWithDefaultRoute()`.

### <a name="making-access-requests-to-apis"></a>Fazer solicitações de acesso a APIs

Ao fazer solicitações para os microsserviços de ordenação e carrinho de compras, o acesso de token, obtido IdentityServer durante o processo de autenticação, deve ser incluído na solicitação, conforme mostrado no exemplo de código a seguir:

```csharp
var authToken = Settings.AuthAccessToken;  
Order = await _ordersService.GetOrderAsync(Convert.ToInt32(order.OrderNumber), authToken);
```

O token de acesso é armazenado como uma configuração de aplicativo e é recuperado do armazenamento específico da plataforma e incluído na chamada para o `GetOrderAsync` método no `OrderService` classe.

Da mesma forma, o token de acesso deve ser incluído ao enviar dados para um IdentityServer protegidas API, conforme mostrado no exemplo de código a seguir:

```csharp
var authToken = Settings.AuthAccessToken;  
await _basketService.UpdateBasketAsync(new CustomerBasket  
{  
    BuyerId = userInfo.UserId,   
    Items = BasketItems.ToList()  
}, authToken);
```

O token de acesso é recuperado do armazenamento específico da plataforma e incluído na chamada para o `UpdateBasketAsync` método no `BasketService` classe.

O `RequestProvider` classe, no aplicativo móvel do eShopOnContainers usa a `HttpClient` classe para fazer solicitações para as APIs RESTful expostas pelo aplicativo eShopOnContainers de referência. Ao fazer solicitações para o pedido e o carrinho de compras APIs, que exigem autorização, um token de acesso válido deve ser incluído na solicitação. Isso é feito adicionando o token de acesso aos cabeçalhos do `HttpClient` da instância, conforme demonstrado no exemplo de código a seguir:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
```

O `DefaultRequestHeaders` propriedade do `HttpClient` classe expõe os cabeçalhos que são enviados com cada solicitação, e o token de acesso é adicionado para o `Authorization` cabeçalho prefixado com a cadeia de caracteres `Bearer`. Quando a solicitação é enviada para uma API RESTful, o valor da `Authorization` cabeçalho é extraído e validado para garantir que esta foi enviada de um emissor confiável e usada para determinar se o usuário tem permissão para invocar a API que recebe.

Para obter mais informações sobre como o aplicativo móvel do eShopOnContainers faz solicitações da web, consulte [acessar dados remotos](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md).

## <a name="summary"></a>Resumo

Há muitas abordagens para integrar a autenticação e autorização em um aplicativo xamarin. Forms que se comunica com um aplicativo web ASP.NET MVC. O aplicativo móvel do eShopOnContainers executa autenticação e autorização com um microsserviço de identidade em contêineres que usa 4 IdentityServer. IdentityServer é uma estrutura de OAuth 2.0 e OpenID Connect do código-fonte aberto para o ASP.NET Core se integra ao ASP.NET Core Identity para realizar a autenticação de token de portador.

O aplicativo móvel solicita tokens de segurança do IdentityServer, para autenticar um usuário ou para acessar um recurso. Ao acessar um recurso, um token de acesso deve ser incluído na solicitação para APIs que exigem autorização. Middleware do IdentityServer valida os tokens de acesso de entrada para garantir que eles sejam enviados de um emissor confiável, e se eles são válidos a serem usados com a API que recebe-los.


## <a name="related-links"></a>Links relacionados

- [Baixe o livro eletrônico (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (amostra)](https://github.com/dotnet-architecture/eShopOnContainers)
