---
title: Autenticação e autorização
description: Este capítulo explica como o aplicativo móvel eShopOnContainers executa a autenticação e a autorização em relação aos microservices em contêineres.
ms.prod: xamarin
ms.assetid: e3f27b4c-f7f5-4839-a48c-30bcb919c59e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2017
ms.openlocfilehash: 528ccd66cc013f83752d93251cb9714115b29819
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78292030"
---
# <a name="authentication-and-authorization"></a>Autenticação e autorização

A autenticação é o processo de obter credenciais de identificação, como nome e senha de um usuário, e validar essas credenciais em uma autoridade. Se as credenciais forem válidas, a entidade que enviou as credenciais será considerada uma identidade autenticada. Depois que uma identidade é autenticada, um processo de autorização determina se a identidade tem acesso a um determinado recurso.

Há muitas abordagens para integrar a autenticação e a autorização em um aplicativo Xamarin. Forms que se comunica com um aplicativo Web ASP.NET MVC, incluindo o uso de identidade ASP.NET Core, provedores de autenticação externa, como Microsoft, Google, Facebook, ou Twitter, e middleware de autenticação. O aplicativo móvel eShopOnContainers executa autenticação e autorização com um microserviço de identidade em contêiner que usa o IdentityServer 4. O aplicativo móvel solicita tokens de segurança do IdentityServer, seja para autenticar um usuário ou para acessar um recurso. Para que o IdentityServer emita tokens em nome de um usuário, o usuário deve entrar no IdentityServer. No entanto, o IdentityServer não fornece uma interface do usuário ou banco de dados para autenticação. Portanto, no aplicativo de referência eShopOnContainers, ASP.NET Core identidade é usada para essa finalidade.

## <a name="authentication"></a>Autenticação

A autenticação é necessária quando um aplicativo precisa saber a identidade do usuário atual. O mecanismo principal do ASP.NET Core para identificar usuários é o sistema de associação de identidade ASP.NET Core, que armazena informações do usuário em um repositório de dados configurado pelo desenvolvedor. Normalmente, esse armazenamento de dados será um repositório do EntityFramework, embora repositórios personalizados ou pacotes de terceiros possam ser usados para armazenar informações de identidade no armazenamento do Azure, Azure Cosmos DB ou em outros locais.

Para cenários de autenticação que usam um armazenamento de dados de usuário local e que persistem informações de identidade entre solicitações por meio de cookies (como é típico em aplicativos Web ASP.NET MVC), a identidade ASP.NET Core é uma solução adequada. No entanto, os cookies nem sempre são um meio natural de persistir e transmitir dados. Por exemplo, um aplicativo Web ASP.NET Core que expõe os pontos de extremidade RESTful que são acessados de um aplicativo móvel normalmente precisará usar autenticação de token de portador, já que os cookies não podem ser usados nesse cenário. No entanto, os tokens de portador podem ser facilmente recuperados e incluídos no cabeçalho de autorização das solicitações da Web feitas do aplicativo móvel.

### <a name="issuing-bearer-tokens-using-identityserver-4"></a>Emitindo tokens de portador usando IdentityServer 4

O [IdentityServer 4](https://github.com/IdentityServer/IdentityServer4) é uma estrutura de software livre do OpenID Connect e do OAuth 2,0 para ASP.NET Core, que pode ser usado para muitos cenários de autenticação e autorização, incluindo a emissão de tokens de segurança para usuários de identidade de ASP.NET Core local.

> [!NOTE]
> O OpenID Connect e o OAuth 2,0 são muito semelhantes, embora tenham responsabilidades diferentes.

O OpenID Connect é uma camada de autenticação sobre o protocolo OAuth 2,0. O OAuth 2 é um protocolo que permite aos aplicativos solicitar tokens de acesso de um serviço de token de segurança e usá-los para se comunicar com APIs. Essa delegação reduz a complexidade em aplicativos cliente e APIs, pois a autenticação e a autorização podem ser centralizadas.

A combinação do OpenID Connect e do OAuth 2,0 combina as duas preocupações fundamentais de segurança de autenticação e acesso à API, e o IdentityServer 4 é uma implementação desses protocolos.

Em aplicativos que usam comunicação direta de cliente para microserviço, como o aplicativo de referência eShopOnContainers, um microserviço de autenticação dedicado agindo como um serviço de token de segurança (STS) pode ser usado para autenticar usuários, como mostrado na figura 9-1. Para obter mais informações sobre comunicação direta entre cliente e microserviço, consulte [comunicação entre o cliente e os microserviços](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md#communication_between_client_and_microservices).

![](authentication-and-authorization-images/authentication.png "Authentication by a dedicated authentication microservice")

**Figura 9-1:** Autenticação por um microserviço de autenticação dedicado

O aplicativo móvel eShopOnContainers se comunica com o microserviço de identidade, que usa o IdentityServer 4 para executar a autenticação e o controle de acesso para APIs. Portanto, o aplicativo móvel solicita tokens de IdentityServer, seja para autenticar um usuário ou para acessar um recurso:

- A autenticação de usuários com IdentityServer é obtida pelo aplicativo móvel solicitando um token de *identidade* , que representa o resultado de um processo de autenticação. No mínimo, ele contém um identificador para o usuário e informações sobre como e quando o usuário é autenticado. Ele também pode conter dados de identidade adicionais.
- O acesso a um recurso com IdentityServer é obtido pelo aplicativo móvel solicitando um token de *acesso* , que permite o acesso a um recurso de API. Os clientes solicitam tokens de acesso e os encaminham para a API. Os tokens de acesso contêm informações sobre o cliente e o usuário (se houver). As APIs usam essas informações para autorizar o acesso aos seus dados.

> [!NOTE]
> Um cliente deve ser registrado com IdentityServer antes de poder solicitar tokens.

### <a name="adding-identityserver-to-a-web-application"></a>Adicionando IdentityServer a um aplicativo Web

Para que um aplicativo Web ASP.NET Core use o IdentityServer 4, ele deve ser adicionado à solução do Visual Studio do aplicativo Web. Para obter mais informações, consulte [visão geral](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) na documentação do IdentityServer.

Depois que o IdentityServer está incluído na solução do Visual Studio do aplicativo Web, ele deve ser adicionado ao pipeline de processamento de solicitação HTTP do aplicativo Web, para que ele possa atender solicitações ao OpenID Connect e a pontos de extremidade do OAuth 2,0. Isso é obtido no método de `Configure` na classe `Startup` do aplicativo Web, conforme demonstrado no exemplo de código a seguir:

```csharp
public void Configure(  
    IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)  
{  
    ...  
    app.UseIdentity();  
    ...  
}
```

Ordenar as coisas no pipeline de processamento de solicitação HTTP do aplicativo Web. Portanto, IdentityServer deve ser adicionado ao pipeline antes da estrutura da interface do usuário que implementa a tela de logon.

### <a name="configuring-identityserver"></a>Configurando o IdentityServer

IdentityServer deve ser configurado no método `ConfigureServices` na classe `Startup` do aplicativo Web chamando o método `services.AddIdentityServer`, conforme demonstrado no exemplo de código a seguir do aplicativo de referência eShopOnContainers:

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

Depois de chamar o método `services.AddIdentityServer`, APIs fluentes adicionais são chamadas para configurar o seguinte:

- Credenciais usadas para assinatura.
- Recursos de API e de identidade aos quais os usuários podem solicitar acesso.
- Clientes que se conectarão a tokens de solicitação.
- ASP.NET Core identidade.

> [!TIP]
> Carregar dinamicamente a configuração do IdentityServer 4. As APIs de IdentityServer 4 permitem configurar o IdentityServer de uma lista de objetos de configuração na memória. No aplicativo de referência do eShopOnContainers, essas coleções na memória são embutidas em código no aplicativo. No entanto, em cenários de produção eles podem ser carregados dinamicamente de um arquivo de configuração ou de um banco de dados.

Para obter informações sobre como configurar o IdentityServer para usar a identidade ASP.NET Core, consulte [usando a identidade ASP.NET Core](https://identityserver4.readthedocs.io/en/latest/quickstarts/6_aspnet_identity.html) na documentação do IdentityServer.

#### <a name="configuring-api-resources"></a>Configurando recursos de API

Ao configurar recursos de API, o método `AddInMemoryApiResources` espera uma coleção de `IEnumerable<ApiResource>`. O exemplo de código a seguir mostra o método `GetApis` que fornece essa coleção no aplicativo de referência eShopOnContainers:

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

Esse método especifica que IdentityServer deve proteger as APIs de pedidos e de cesta. Portanto, os tokens de acesso gerenciado IdentityServer serão necessários ao fazer chamadas para essas APIs. Para obter mais informações sobre o tipo de `ApiResource`, consulte [recursos de API](https://identityserver4.readthedocs.io/en/latest/reference/api_resource.html) na documentação do IdentityServer 4.

#### <a name="configuring-identity-resources"></a>Configurando recursos de identidade

Ao configurar recursos de identidade, o método `AddInMemoryIdentityResources` espera uma coleção de `IEnumerable<IdentityResource>`. Os recursos de identidade são dados como ID de usuário, nome ou endereço de email. Cada recurso de identidade tem um nome exclusivo, e tipos de declaração arbitrários podem ser atribuídos a ele, que serão incluídos no token de identidade para o usuário. O exemplo de código a seguir mostra o método `GetResources` que fornece essa coleção no aplicativo de referência eShopOnContainers:

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

A especificação do OpenID Connect especifica alguns [recursos de identidade padrão](https://openid.net/specs/openid-connect-core-1_0.html#ScopeClaims). O requisito mínimo é que o suporte seja fornecido para emitir uma ID exclusiva para os usuários. Isso é obtido com a exposição do recurso de identidade `IdentityResources.OpenId`.

> [!NOTE]
> A classe `IdentityResources` dá suporte a todos os escopos definidos na especificação do OpenID Connect (OpenID, email, perfil, telefone e endereço).

O IdentityServer também dá suporte à definição de recursos de identidade personalizados. Para obter mais informações, consulte [definindo recursos de identidade personalizados](http://docs.identityserver.io/en/latest/topics/resources.html#defining-custom-identity-resources) na documentação do IdentityServer. Para obter mais informações sobre o tipo de `IdentityResource`, consulte [recursos de identidade](https://identityserver4.readthedocs.io/en/latest/reference/identity_resource.html) na documentação do IdentityServer 4.

#### <a name="configuring-clients"></a>Configurando clientes

Os clientes são aplicativos que podem solicitar tokens do IdentityServer. Normalmente, as seguintes configurações devem ser definidas para cada cliente como um mínimo:

- Uma ID de cliente exclusiva.
- As interações permitidas com o serviço de token (conhecido como tipo de concessão).
- O local onde os tokens de identidade e de acesso são enviados (conhecido como URI de redirecionamento).
- Uma lista de recursos aos quais o cliente tem permissão de acesso (conhecido como escopos).

Ao configurar clientes, o método `AddInMemoryClients` espera uma coleção de `IEnumerable<Client>`. O exemplo de código a seguir mostra a configuração do aplicativo móvel eShopOnContainers no método `GetClients` que fornece essa coleção no aplicativo de referência eShopOnContainers:

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

Esta configuração especifica dados para as seguintes propriedades:

- `ClientId`: uma ID exclusiva para o cliente.
- `ClientName`: o nome de exibição do cliente, que é usado para registro em log e a tela de consentimento.
- `AllowedGrantTypes`: especifica como um cliente deseja interagir com o IdentityServer. Para obter mais informações, consulte [Configurando o fluxo de autenticação](#configuring_the_authentication_flow).
- `ClientSecrets`: especifica as credenciais de segredo do cliente que são usadas ao solicitar tokens do ponto de extremidade do token.
- `RedirectUris`: especifica os URIs permitidos para os quais retornar tokens ou códigos de autorização.
- `RequireConsent`: especifica se uma tela de consentimento é necessária.
- `RequirePkce`: especifica se os clientes que usam um código de autorização devem enviar uma chave de prova.
- `PostLogoutRedirectUris`: especifica os URIs permitidos para redirecionar após o logout.
- `AllowedCorsOrigins`: especifica a origem do cliente para que o IdentityServer possa permitir chamadas entre origens da origem.
- `AllowedScopes`: especifica os recursos aos quais o cliente tem acesso. Por padrão, um cliente não tem acesso a nenhum recurso.
- `AllowOfflineAccess`: especifica se o cliente pode solicitar tokens de atualização.

<a name="configuring_the_authentication_flow" />

#### <a name="configuring-the-authentication-flow"></a>Configurando o fluxo de autenticação

O fluxo de autenticação entre um cliente e o IdentityServer pode ser configurado especificando os tipos de concessão na propriedade `Client.AllowedGrantTypes`. As especificações do OpenID Connect e do OAuth 2,0 definem um número de fluxos de autenticação, incluindo:

- Localiza. Esse fluxo é otimizado para aplicativos baseados em navegador e deve ser usado somente para autenticação de usuário ou solicitações de token de acesso e autenticação. Todos os tokens são transmitidos por meio do navegador e, portanto, recursos avançados como tokens de atualização não são permitidos.
- Código de autorização. Esse fluxo fornece a capacidade de recuperar Tokens em um canal traseiro, ao contrário do canal frontal do navegador, enquanto também oferece suporte à autenticação do cliente.
- Híbrido. Esse fluxo é uma combinação dos tipos de concessão de código implícito e de autorização. O token de identidade é transmitido por meio do canal do navegador e contém a resposta do protocolo assinado junto com outros artefatos, como o código de autorização. Após a validação bem-sucedida da resposta, o canal traseiro deve ser usado para recuperar o token de acesso e de atualização.

> [!TIP]
> Use o fluxo de autenticação híbrida. O fluxo de autenticação híbrida reduz vários ataques que se aplicam ao canal do navegador e é o fluxo recomendado para aplicativos nativos que desejam recuperar Tokens de acesso (e possivelmente os tokens de atualização).

Para obter mais informações sobre fluxos de autenticação, consulte [conceder tipos](https://identityserver4.readthedocs.io/en/latest/topics/grant_types.html) na documentação do IdentityServer 4.

### <a name="performing-authentication"></a>Executando a autenticação

Para que o IdentityServer emita tokens em nome de um usuário, o usuário deve entrar no IdentityServer. No entanto, o IdentityServer não fornece uma interface do usuário ou banco de dados para autenticação. Portanto, no aplicativo de referência eShopOnContainers, ASP.NET Core identidade é usada para essa finalidade.

O aplicativo móvel eShopOnContainers autentica com IdentityServer com o fluxo de autenticação híbrida, ilustrado na Figura 9-2.

![](authentication-and-authorization-images/sign-in.png "High-level overview of the sign-in process")

**Figura 9-2:** Visão geral de alto nível do processo de entrada

Uma solicitação de entrada é feita para `<base endpoint>:5105/connect/authorize`. Após a autenticação bem-sucedida, IdentityServer retorna uma resposta de autenticação que contém um código de autorização e um token de identidade. O código de autorização é enviado para `<base endpoint>:5105/connect/token`, que responde com tokens de acesso, identidade e atualização.

O aplicativo móvel eShopOnContainers se desconecta do IdentityServer enviando uma solicitação para `<base endpoint>:5105/connect/endsession`, com parâmetros adicionais. Depois que a saída ocorre, o IdentityServer responde enviando um URI de redirecionamento de logout post para o aplicativo móvel. A Figura 9-3 ilustra esse processo.

![](authentication-and-authorization-images/sign-out.png "High-level overview of the sign-out process")

**Figura 9-3:** Visão geral de alto nível do processo de saída

No aplicativo móvel eShopOnContainers, a comunicação com o IdentityServer é executada pela classe `IdentityService`, que implementa a interface `IIdentityService`. Essa interface especifica que a classe de implementação deve fornecer os métodos `CreateAuthorizationRequest`, `CreateLogoutRequest`e `GetTokenAsync`.

#### <a name="signing-in"></a>Entrando

Quando o usuário toca no botão de **logon** na `LoginView`, o `SignInCommand` na classe `LoginViewModel` é executado, o que, por sua vez, executa o método `SignInAsync`. O seguinte exemplo de código mostra esse método:

```csharp
private async Task SignInAsync()  
{  
    ...  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    IsLogin = true;  
    ...  
}
```

Esse método invoca o método `CreateAuthorizationRequest` na classe `IdentityService`, que é mostrada no exemplo de código a seguir:

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
    dic.Add("redirect_uri", GlobalSetting.Instance.Callback);
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

Esse método cria o URI para o [ponto de extremidade de autorização](https://identityserver4.readthedocs.io/en/latest/endpoints/authorize.html)do IdentityServer, com os parâmetros necessários. O ponto de extremidade de autorização está em `/connect/authorize` na porta 5105 do ponto de extremidade base exposto como uma configuração de usuário. Para obter mais informações sobre configurações de usuário, consulte [Gerenciamento de configuração](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> A superfície de ataque do aplicativo móvel eShopOnContainers é reduzida com a implementação da chave de prova da extensão de troca de código (PKCE) para o OAuth. O PKCE protege o código de autorização de ser usado se ele for interceptado. Isso é obtido pelo cliente que gera um verificador secreto, um hash que é passado na solicitação de autorização e que é apresentado sem hash ao resgatar o código de autorização. Para obter mais informações sobre PKCE, consulte [chave de prova para a troca de código por clientes públicos do OAuth](https://tools.ietf.org/html/rfc7636) no site de engenharia de tarefas da Internet.

O URI retornado é armazenado na propriedade `LoginUrl` da classe `LoginViewModel`. Quando a propriedade `IsLogin` se torna `true`, a [`WebView`](xref:Xamarin.Forms.WebView) no `LoginView` se torna visível. O `WebView` dados vincula sua propriedade [`Source`](xref:Xamarin.Forms.WebView.Source) à propriedade `LoginUrl` da classe `LoginViewModel` e, portanto, faz uma solicitação de entrada para IdentityServer quando a propriedade `LoginUrl` é definida como o ponto de extremidade de autorização do IdentityServer. Quando o IdentityServer receber essa solicitação e o usuário não for autenticado, o `WebView` será redirecionado para a página de logon configurada, que é mostrada na Figura 9-4.

![](authentication-and-authorization-images/login.png "Login page displayed by the WebView")

**Figura 9-4:** Página de logon exibida pelo WebView

Quando o logon for concluído, o [`WebView`](xref:Xamarin.Forms.WebView) será redirecionado para um URI de retorno. Essa `WebView` navegação fará com que o método `NavigateAsync` na classe `LoginViewModel` seja executado, o que é mostrado no exemplo de código a seguir:

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

Esse método analisa a resposta de autenticação contida no URI de retorno e desde que um código de autorização válido esteja presente, ele faz uma solicitação para o [ponto de extremidade do token](https://identityserver4.readthedocs.io/en/latest/endpoints/token.html)do IdentityServer, passando o código de autorização, o verificador de segredo do PKCE e outros parâmetros necessários. O ponto de extremidade do token está em `/connect/token` na porta 5105 do ponto de extremidade base exposto como uma configuração de usuário. Para obter mais informações sobre configurações de usuário, consulte [Gerenciamento de configuração](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!TIP]
> Validar URIs de retorno. Embora o aplicativo móvel eShopOnContainers não valide o URI de retorno, a prática recomendada é validar que o URI de retorno se refere a um local conhecido, para evitar ataques de redirecionamento aberto.

Se o ponto de extremidade do token receber um código de autorização válido e um verificador de segredo PKCE, ele responderá com um token de acesso, token de identidade e token de atualização. O token de acesso (que permite o acesso aos recursos da API) e o token de identidade são armazenados como configurações do aplicativo e a navegação da página é executada. Portanto, o efeito geral no aplicativo móvel eShopOnContainers é isto: desde que os usuários possam se autenticar com êxito com o IdentityServer, eles são navegados para a página `MainView`, que é uma [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) que exibe a `CatalogView` como sua guia selecionada.

Para obter informações sobre navegação de página, consulte [Navigation](~/xamarin-forms/enterprise-application-patterns/navigation.md). Para obter informações sobre como [`WebView`](xref:Xamarin.Forms.WebView) navegação faz com que um método de modelo de exibição seja executado, consulte [invocando a navegação usando comportamentos](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors). Para obter informações sobre configurações de aplicativo, consulte [Gerenciamento de configuração](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> O eShopOnContainers também permite uma entrada fictícia quando o aplicativo é configurado para usar serviços fictícios no `SettingsView`. Nesse modo, o aplicativo não se comunica com o IdentityServer, permitindo que o usuário entre usando qualquer credencial.

#### <a name="signing-out"></a>Signing-out

Quando o usuário toca no botão **fazer logoff** na `ProfileView`, o `LogoutCommand` na classe `ProfileViewModel` é executado, o que, por sua vez, executa o método `LogoutAsync`. Esse método executa a navegação de página na página `LoginView`, passando uma instância `LogoutParameter` definida como `true` como um parâmetro. Para obter mais informações sobre como passar parâmetros durante a navegação da página, consulte [passando parâmetros durante a navegação](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation).

Quando uma exibição é criada e navegada, o método `InitializeAsync` do modelo de exibição associado do modo de exibição é executado, que, em seguida, executa o método `Logout` da classe `LoginViewModel`, que é mostrada no exemplo de código a seguir:

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

Esse método invoca o método `CreateLogoutRequest` na classe `IdentityService`, passando o token de identidade recuperado das configurações do aplicativo como um parâmetro. Para obter mais informações sobre configurações de aplicativo, consulte [Gerenciamento de configuração](~/xamarin-forms/enterprise-application-patterns/configuration-management.md). O seguinte exemplo de código mostra o método `CreateLogoutRequest`:

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

Esse método cria o URI para o [ponto de extremidade de sessão final](https://identityserver4.readthedocs.io/en/latest/endpoints/endsession.html#refendsession)do IdentityServer, com os parâmetros necessários. O ponto de extremidade da sessão final está em `/connect/endsession` na porta 5105 do ponto de extremidade base exposto como uma configuração de usuário. Para obter mais informações sobre configurações de usuário, consulte [Gerenciamento de configuração](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

O URI retornado é armazenado na propriedade `LoginUrl` da classe `LoginViewModel`. Enquanto a propriedade `IsLogin` é `true`, a [`WebView`](xref:Xamarin.Forms.WebView) no `LoginView` é visível. O `WebView` dados associa sua propriedade [`Source`](xref:Xamarin.Forms.WebView.Source) à propriedade `LoginUrl` da classe `LoginViewModel` e, portanto, faz uma solicitação de saída para IdentityServer quando a propriedade `LoginUrl` é definida como o ponto de extremidade da sessão final do IdentityServer. Quando o IdentityServer recebe essa solicitação, desde que o usuário esteja conectado, ocorre uma saída. A autenticação é controlada com um cookie gerenciado pelo middleware de autenticação de cookie do ASP.NET Core. Portanto, a saída de IdentityServer remove o cookie de autenticação e envia um URI de redirecionamento de logout de postagem de volta para o cliente.

No aplicativo móvel, o [`WebView`](xref:Xamarin.Forms.WebView) será redirecionado para o URI de redirecionamento de logout posterior. Essa `WebView` navegação fará com que o método `NavigateAsync` na classe `LoginViewModel` seja executado, o que é mostrado no exemplo de código a seguir:

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

Esse método limpa o token de identidade e o token de acesso das configurações do aplicativo e define a propriedade `IsLogin` como `false`, o que faz com que o [`WebView`](xref:Xamarin.Forms.WebView) na página de `LoginView` fique invisível. Por fim, a propriedade `LoginUrl` é definida como o URI do [ponto de extremidade de autorização](https://identityserver4.readthedocs.io/en/latest/endpoints/authorize.html)do IdentityServer, com os parâmetros necessários, na preparação para a próxima vez que o usuário iniciar uma entrada.

Para obter informações sobre navegação de página, consulte [Navigation](~/xamarin-forms/enterprise-application-patterns/navigation.md). Para obter informações sobre como [`WebView`](xref:Xamarin.Forms.WebView) navegação faz com que um método de modelo de exibição seja executado, consulte [invocando a navegação usando comportamentos](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors). Para obter informações sobre configurações de aplicativo, consulte [Gerenciamento de configuração](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> O eShopOnContainers também permite uma saída fictícia quando o aplicativo é configurado para usar serviços fictícios no SettingsView. Nesse modo, o aplicativo não se comunica com o IdentityServer e, em vez disso, limpa todos os tokens armazenados das configurações do aplicativo.

<a name="authorization" />

## <a name="authorization"></a>Autorização

Após a autenticação, ASP.NET Core APIs Web geralmente precisam autorizar o acesso, o que permite que um serviço disponibilize APIs para alguns usuários autenticados, mas não para todos.

Restringir o acesso a uma rota ASP.NET Core MVC pode ser obtido aplicando um atributo de autorização a um controlador ou uma ação, o que limita o acesso ao controlador ou à ação para usuários autenticados, conforme mostrado no exemplo de código a seguir:

```csharp
[Authorize]  
public class BasketController : Controller  
{  
    ...  
}
```

Se um usuário não autorizado tentar acessar um controlador ou uma ação marcada com o atributo `Authorize`, o MVC Framework retornará um código de status HTTP 401 (não autorizado).

> [!NOTE]
> Os parâmetros podem ser especificados no atributo `Authorize` para restringir uma API a usuários específicos. Para obter mais informações, consulte [Authorization](/aspnet/core/security/authorization/introduction/).

O IdentityServer pode ser integrado ao fluxo de trabalho de autorização para que os tokens de acesso forneçam autorização de controle. Essa abordagem é mostrada na Figura 9-5.

![](authentication-and-authorization-images/authorization.png "Authorization by access token")

**Figura 9-5:** Autorização por token de acesso

O aplicativo móvel eShopOnContainers comunica-se com o microserviço de identidade e solicita um token de acesso como parte do processo de autenticação. O token de acesso é encaminhado para as APIs expostas pelos microserviços de classificação e cesta como parte das solicitações de acesso. Os tokens de acesso contêm informações sobre o cliente e o usuário. As APIs usam essas informações para autorizar o acesso aos seus dados. Para obter informações sobre como configurar o IdentityServer para proteger APIs, consulte [configurando recursos de API](#configuring-api-resources).

### <a name="configuring-identityserver-to-perform-authorization"></a>Configurando o IdentityServer para executar a autorização

Para executar a autorização com IdentityServer, seu middleware de autorização deve ser adicionado ao pipeline de solicitação HTTP do aplicativo Web. O middleware é adicionado no método `ConfigureAuth` na classe `Startup` do aplicativo Web, que é invocada a partir do método `Configure`, e é demonstrado no exemplo de código a seguir do aplicativo de referência eShopOnContainers:

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

Esse método garante que a API só possa ser acessada com um token de acesso válido. O middleware valida o token de entrada para garantir que ele seja enviado de um emissor confiável e valida que o token é válido para ser usado com a API que o recebe. Portanto, navegar até o controlador de classificação ou de cesta retornará um código de status HTTP 401 (não autorizado), indicando que um token de acesso é necessário.

> [!NOTE]
> O middleware de autorização do IdentityServer deve ser adicionado ao pipeline de solicitação HTTP do aplicativo Web antes de adicionar o MVC com `app.UseMvc()` ou `app.UseMvcWithDefaultRoute()`.

### <a name="making-access-requests-to-apis"></a>Fazendo solicitações de acesso às APIs

Ao fazer solicitações para os microserviços de classificação e cesta, o token de acesso, obtido do IdentityServer durante o processo de autenticação, deve ser incluído na solicitação, conforme mostrado no exemplo de código a seguir:

```csharp
var authToken = Settings.AuthAccessToken;  
Order = await _ordersService.GetOrderAsync(Convert.ToInt32(order.OrderNumber), authToken);
```

O token de acesso é armazenado como uma configuração de aplicativo e é recuperado do armazenamento específico da plataforma e incluído na chamada para o método `GetOrderAsync` na classe `OrderService`.

Da mesma forma, o token de acesso deve ser incluído ao enviar dados para uma API protegida por IdentityServer, conforme mostrado no exemplo de código a seguir:

```csharp
var authToken = Settings.AuthAccessToken;  
await _basketService.UpdateBasketAsync(new CustomerBasket  
{  
    BuyerId = userInfo.UserId,   
    Items = BasketItems.ToList()  
}, authToken);
```

O token de acesso é recuperado do armazenamento específico da plataforma e incluído na chamada para o método `UpdateBasketAsync` na classe `BasketService`.

A classe `RequestProvider`, no aplicativo móvel eShopOnContainers, usa a classe `HttpClient` para fazer solicitações às APIs RESTful expostas pelo aplicativo de referência eShopOnContainers. Ao fazer solicitações às APIs de ordenação e de cesta, que exigem autorização, um token de acesso válido deve ser incluído com a solicitação. Isso é obtido com a adição do token de acesso aos cabeçalhos da instância de `HttpClient`, conforme demonstrado no exemplo de código a seguir:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
```

A propriedade `DefaultRequestHeaders` da classe `HttpClient` expõe os cabeçalhos que são enviados com cada solicitação e o token de acesso é adicionado ao cabeçalho `Authorization` prefixado com a cadeia de caracteres `Bearer`. Quando a solicitação é enviada a uma API RESTful, o valor do cabeçalho de `Authorization` é extraído e validado para garantir que ele seja enviado de um emissor confiável e usado para determinar se o usuário tem permissão para invocar a API que o recebe.

Para obter mais informações sobre como o aplicativo móvel eShopOnContainers faz solicitações da Web, consulte [acessando dados remotos](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md).

## <a name="summary"></a>Resumo

Há muitas abordagens para integrar a autenticação e a autorização em um aplicativo Xamarin. Forms que se comunica com um aplicativo Web ASP.NET MVC. O aplicativo móvel eShopOnContainers executa autenticação e autorização com um microserviço de identidade em contêiner que usa o IdentityServer 4. IdentityServer é uma estrutura de software livre do OpenID Connect e do OAuth 2,0 para ASP.NET Core que se integra com a identidade ASP.NET Core para executar a autenticação de token de portador.

O aplicativo móvel solicita tokens de segurança do IdentityServer, seja para autenticar um usuário ou para acessar um recurso. Ao acessar um recurso, um token de acesso deve ser incluído na solicitação para APIs que exigem autorização. O middleware da IdentityServer valida tokens de acesso de entrada para garantir que eles sejam enviados de um emissor confiável e que sejam válidos para serem usados com a API que os recebe.

## <a name="related-links"></a>Links relacionados

- [Download do eBook (PDF de 2Mb)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
