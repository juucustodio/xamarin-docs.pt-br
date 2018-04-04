---
title: Autenticação e autorização
ms.prod: xamarin
ms.assetid: e3f27b4c-f7f5-4839-a48c-30bcb919c59e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2017
ms.openlocfilehash: 9c6f3ae19b3e1b89220cbdf0985f4bdf789f2209
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="authentication-and-authorization"></a>Autenticação e autorização

A autenticação é o processo de obter credenciais de identificação, como nome e senha de um usuário e validar essas credenciais em uma autoridade. Se as credenciais forem válidas, a entidade que emitiu as credenciais é considerada uma identidade autenticada. Depois que uma identidade autenticada, o processo de autorização determina se essa identidade tem acesso a um determinado recurso.

Há muitas abordagens para a integração de autenticação e autorização em um aplicativo xamarin. Forms que se comunica com um aplicativo web do ASP.NET MVC, incluindo o uso do ASP.NET Core Identity, provedores de autenticação externa, como Microsoft, Google, Middleware do Facebook ou Twitter e autenticação. O aplicativo móvel eShopOnContainers executa autenticação e autorização com um microsserviço de identidade em contêineres que usa 4 IdentityServer. O aplicativo móvel solicita tokens de segurança de IdentityServer, para autenticar um usuário ou para acessar um recurso. Para IdentityServer para emitir tokens em nome do usuário, o usuário deve entrar no IdentityServer. No entanto, IdentityServer não fornece uma interface de usuário ou o banco de dados para autenticação. Portanto, o aplicativo de referência eShopOnContainers, a identidade do ASP.NET Core é usada para essa finalidade.

## <a name="authentication"></a>Autenticação

A autenticação é necessária quando um aplicativo precisa saber a identidade do usuário atual. Mecanismo de principal do ASP.NET Core para identificar os usuários é o sistema de associação do ASP.NET Core Identity, que armazena informações de usuário em um repositório de dados configurado pelo desenvolvedor. Normalmente, esse repositório de dados será um repositório EntityFramework, embora repositórios personalizados ou pacotes de terceiros podem ser usados para armazenar informações de identidade no armazenamento do Azure, o banco de dados do Azure Cosmos ou outros locais.

Para cenários de autenticação que fazem usam de um repositório de dados de usuário local, e que persiste as informações de identidade entre solicitações por meio de cookies (como é comum em aplicativos web ASP.NET MVC), a identidade do ASP.NET Core é uma solução adequada. No entanto, os cookies nem sempre são uma maneira natural de persistência e transmissão de dados. Por exemplo, um aplicativo web do ASP.NET Core que expõe pontos de extremidade RESTful que são acessados de um aplicativo móvel normalmente precisa usar a autenticação de token de portador, desde que os cookies não podem ser usados neste cenário. No entanto, os tokens de portador facilmente podem ser recuperados e incluídos no cabeçalho de autorização de solicitações da web feitas do aplicativo móvel.

### <a name="issuing-bearer-tokens-using-identityserver-4"></a>Emissão de Tokens de portador usando IdentityServer 4

[4 IdentityServer](https://github.com/IdentityServer/IdentityServer4) é uma estrutura de OpenID Connect e OAuth 2.0 do código-fonte aberto para ASP.NET Core, que pode ser usada para muitos cenários de autenticação e autorização incluindo emitir tokens de segurança para usuários de identidade do ASP.NET Core locais.

> [!NOTE]
> Conecte-se de OpenID e OAuth 2.0 são muito semelhantes, embora tenha responsabilidades diferentes.

OpenID Connect é uma camada de autenticação sobre o protocolo OAuth 2.0. OAuth 2 é um protocolo que permite que aplicativos solicitar tokens de acesso de um serviço de token de segurança e usá-las para se comunicar com APIs. Essa delegação reduz a complexidade em aplicativos cliente e APIs desde que a autenticação e autorização podem ser centralizados.

A combinação de OpenID Connect e OAuth 2.0 combinar duas preocupações de segurança de autenticação e o acesso à API e IdentityServer 4 é uma implementação desses protocolos.

Em aplicativos que usam comunicação de cliente para microsserviço direta, como o aplicativo de referência eShopOnContainers, um microsserviço autenticação dedicado agindo como um Token de segurança Service (STS) pode ser usado para autenticar usuários, conforme mostrado na Figura 9-1. Para obter mais informações sobre a comunicação de cliente para microsserviço direta, consulte [comunicação entre cliente e Microservices](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md#communication_between_client_and_microservices).

![](authentication-and-authorization-images/authentication.png "Autenticação por um microsserviço autenticação dedicado")

**Figura 9-1:** autenticação por um microsserviço autenticação dedicado

O aplicativo móvel eShopOnContainers se comunica com o microsserviço de identidade, que usa 4 IdentityServer para executar a autenticação e controle de acesso para APIs. Portanto, o aplicativo móvel solicita tokens de IdentityServer, para autenticar um usuário ou para acessar um recurso:

-   Autenticar usuários com IdentityServer é obtida com o aplicativo móvel solicitando uma *identidade* token, que representa o resultado de um processo de autenticação. No mínimo, ele contém um identificador para o usuário e obter informações sobre como e quando o usuário autenticado. Ele também pode conter dados de identidade adicional.
-   Acessando um recurso com IdentityServer é obtida com o aplicativo móvel solicitando uma *acesso* token, que permite o acesso a um recurso da API. Os clientes solicitar tokens de acesso e encaminhá-los para a API. Tokens de acesso contêm informações sobre o cliente e o usuário (se houver). APIs, em seguida, usam essas informações para autorizar o acesso aos seus dados.

> [!NOTE]
> Um cliente deve ser registrado com IdentityServer antes que ele pode solicitar tokens.

### <a name="adding-identityserver-to-a-web-application"></a>Adicionando IdentityServer a um aplicativo Web

Em ordem para um aplicativo web ASP.NET Core usam IdentityServer 4, ele deve ser adicionado à solução do Visual Studio do aplicativo da web. Para obter mais informações, consulte [visão geral e instalação](https://identityserver4.readthedocs.io/en/release/quickstarts/0_overview.html) na documentação do IdentityServer.

Quando IdentityServer estiver incluído na solução do Visual Studio do aplicativo da web, deve ser adicionado ao pipeline de processamento de solicitação HTTP do aplicativo da web para que ele possa servir solicitações para pontos de extremidade OpenID Connect e OAuth 2.0. Isso é feito no `Configure` método no aplicativo da web `Startup` classe, conforme demonstrado no exemplo de código a seguir:

```csharp
public void Configure(  
    IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)  
{  
    ...  
    app.UseIdentity();  
    ...  
}
```

Ordem é importante no pipeline de processamento de solicitação HTTP do aplicativo da web. Portanto, IdentityServer deve ser adicionado ao pipeline antes que a estrutura de interface do usuário que implementa a tela de logon.

### <a name="configuring-identityserver"></a>Configurando IdentityServer

IdentityServer deve ser configurado no `ConfigureServices` método no aplicativo da web `Startup` classe chamando o `services.AddIdentityServer` método, conforme demonstrado no seguinte exemplo de código do aplicativo eShopOnContainers Referência:

```csharp
public void ConfigureServices(IServiceCollection services)  
{  
    ...  
    services.AddIdentityServer(x => x.IssuerUri = "null")  
        .AddSigningCredential(Certificate.Get())                 
        .AddAspNetIdentity<ApplicationUser>()  
        .AddConfigurationStore(builder =>  
            builder.UseSqlServer(connectionString, options =>  
                options.MigrationsAssembly(migrationsAssembly)))  
        .AddOperationalStore(builder =>  
            builder.UseSqlServer(connectionString, options =>  
                options.MigrationsAssembly(migrationsAssembly)))  
        .Services.AddTransient<IProfileService, ProfileService>();  
}
```

Depois de chamar o `services.AddIdentityServer` método, APIs fluentes adicionais são chamados para configurar o seguinte:

-   Credenciais usadas para a assinatura.
-   Recursos de API e identidade que os usuários podem solicitar acessem.
-   Clientes que se conectarem ao solicitar tokens.
-   Identidade do ASP.NET Core.

>💡 **Dica**: carregar dinamicamente a configuração de IdentityServer 4. APIs do IdentityServer 4 permitem configurar IdentityServer de uma lista de memória de objetos de configuração. No aplicativo de referência eShopOnContainers, essas coleções na memória são embutidos em código no aplicativo. No entanto, em cenários de produção eles podem ser carregados dinamicamente de um arquivo de configuração ou de um banco de dados.

Para obter informações sobre como configurar IdentityServer para usar a identidade do ASP.NET Core, consulte [usando o ASP.NET Core identidade](https://identityserver4.readthedocs.io/en/release/quickstarts/6_aspnet_identity.html) na documentação do IdentityServer.

#### <a name="configuring-api-resources"></a>Configurando recursos de API

Ao configurar os recursos da API, o `AddInMemoryApiResources` método espera um `IEnumerable<ApiResource>` coleção. O seguinte exemplo de código mostra o `GetApis` método que fornece a esta coleção no eShopOnContainers Referência aplicativo:

```csharp
public static IEnumerable<ApiResource> GetApis()  
{  
    return new List<ApiResource>  
    {  
        new ApiResource("orders", "Orders Service"),  
        new ApiResource("basket", "Basket Service")  
    };  
}
```

Esse método Especifica que IdentityServer deve proteger os pedidos e APIs do carrinho. Portanto, o acesso de gerenciado IdentityServer tokens será necessários ao fazer chamadas para essas APIs. Para obter mais informações sobre o `ApiResource` de tipo, consulte [recurso da API](https://identityserver4.readthedocs.io/en/release/reference/api_resource.html#refapiresource) na documentação do IdentityServer 4.

#### <a name="configuring-identity-resources"></a>Configurando recursos de identidade

Ao configurar recursos de identidade, o `AddInMemoryIdentityResources` método espera um `IEnumerable<IdentityResource>` coleção. Recursos de identidade são dados como a ID de usuário, nome ou endereço de email. Cada recurso de identidade tem um nome exclusivo e tipos de declaração arbitrário podem ser atribuídos a ele, que serão incluídas no token de identidade do usuário. O seguinte exemplo de código mostra o `GetResources` método que fornece a esta coleção no eShopOnContainers Referência aplicativo:

```csharp
public static IEnumerable<IdentityResource> GetResources()  
{  
    return new List<IdentityResource>  
    {  
        new IdentityResources.OpenId(),  
        new IdentityResources.Profile()  
    };  
}
```

A especificação do OpenID Connect especifica alguns [recursos de identidade padrão](https://openid.net/specs/openid-connect-core-1_0.html#ScopeClaims). O requisito mínimo é que o suporte é fornecido para a emissão de uma ID exclusiva para os usuários. Isso é obtido com a exposição a `IdentityResources.OpenId` recursos de identidade.

> [!NOTE]
> O `IdentityResources` classe oferece suporte a todos os escopos definidos na especificação OpenID Connect (openid, email, perfil, telefone e endereço).

IdentityServer também dá suporte para definir recursos de identidade personalizada. Para obter mais informações, consulte [definir recursos de identidade personalizado](https://identityserver4.readthedocs.io/en/release/topics/resources.html#defining-custom-identity-resources) na documentação do IdentityServer. Para obter mais informações sobre o `IdentityResource` de tipo, consulte [recursos de identidade](https://identityserver4.readthedocs.io/en/release/reference/identity_resource.html) na documentação do IdentityServer 4.

#### <a name="configuring-clients"></a>Configuração de clientes

Os clientes são aplicativos que podem solicitar tokens de IdentityServer. Normalmente, as configurações a seguir devem ser definidas para cada cliente, no mínimo:

-   Uma ID de cliente exclusivo.
-   As interações permitidas com o serviço de token (conhecido como o tipo de concessão).
-   O local onde os tokens de identidade e acesso são enviados (conhecido como um URI de redirecionamento).
-   Uma lista de recursos que o cliente tem permissão para acessar (conhecidos como escopos).

Ao configurar clientes, o `AddInMemoryClients` método espera um `IEnumerable<Client>` coleção. O exemplo de código a seguir mostra a configuração para o aplicativo móvel eShopOnContainers no `GetClients` método que fornece a esta coleção no eShopOnContainers Referência aplicativo:

```csharp
public static IEnumerable<Client> GetClients(Dictionary<string,string> clientsUrl)
{
    return new List<Client>
    {
        ...
        new Client
        {
            ClientId = "xamarin",
            ClientName = "eShop Xamarin OpenId Client",
            AllowedGrantTypes = GrantTypes.Hybrid,
            ClientSecrets =
            {
                new Secret("secret".Sha256())
            },
            RedirectUris = { clientsUrl["Xamarin"] },
            RequireConsent = false,
            RequirePkce = true,
            PostLogoutRedirectUris = { $"{clientsUrl["Xamarin"]}/Account/Redirecting" },
            AllowedCorsOrigins = { "http://eshopxamarin" },
            AllowedScopes = new List<string>
            {
                IdentityServerConstants.StandardScopes.OpenId,
                IdentityServerConstants.StandardScopes.Profile,
                IdentityServerConstants.StandardScopes.OfflineAccess,
                "orders",
                "basket"
            },
            AllowOfflineAccess = true,
            AllowAccessTokensViaBrowser = true
        },
        ...
    };
}
```

Essa configuração especifica os dados para as seguintes propriedades:

-   `ClientId`: Uma ID exclusiva para o cliente.
-   `ClientName`: O nome de exibição de cliente, que é usado para registro em log e a tela de consentimento.
-   `AllowedGrantTypes`: Especifica como um cliente deseja interagir com IdentityServer. Para obter mais informações, consulte [Configurando o fluxo de autenticação](#configuring_the_authentication_flow).
-   `ClientSecrets`: Especifica as credenciais de segredo do cliente que são usadas ao solicitar tokens de ponto de extremidade token.
-   `RedirectUris`: Especifica os URIs permitidos para o qual retornar tokens ou códigos de autorização.
-   `RequireConsent`: Especifica se uma tela de consentimento é necessária.
-   `RequirePkce`: Especifica se os clientes usando um código de autorização devem enviar uma chave de prova.
-   `PostLogoutRedirectUris`: Especifica os URIs permitidos para redirecionar para após o logoff.
-   `AllowedCorsOrigins`: Especifica a origem do cliente para que IdentityServer pode permitir chamadas entre origens da origem.
-   `AllowedScopes`: Especifica os recursos que o cliente tem acesso ao. Por padrão, um cliente não tem acesso a todos os recursos.
-   `AllowOfflineAccess`: Especifica se o cliente pode solicitar tokens de atualização.

<a name="configuring_the_authentication_flow" />

#### <a name="configuring-the-authentication-flow"></a>Configurando o fluxo de autenticação

O fluxo de autenticação entre um cliente e IdentityServer podem ser configuradas, especificando os tipos de concessão no `Client.AllowedGrantTypes` propriedade. As especificações de OpenID Connect e OAuth 2.0 definem uma série de fluxos de autenticação, incluindo:

-   Implícita. Esse fluxo é otimizado para aplicativos baseados em navegador e deve ser usado para somente a autenticação de usuário ou solicitações de token de acesso e autenticação. Todos os tokens são transmitidos por meio do navegador e avançados, portanto, recursos como tokens de atualização não são permitidos.
-   Código de autorização. Esse fluxo fornece a capacidade de recuperar tokens de um canal de retorno, em vez do canal frontal do navegador, e também dar suporte a autenticação de cliente.
-   Híbrido. Esse fluxo é uma combinação da implícita e tipos de concessão de código de autorização. O token de identidade é transmitido por meio do canal de navegador e contém a resposta do protocolo assinado juntamente com outros artefatos, como o código de autorização. Após a validação bem-sucedida da resposta, o canal de retorno deve ser usado para recuperar o acesso e token de atualização.

> [!TIP]
> Use o fluxo de autenticação híbrida. O fluxo de autenticação híbrida reduz um número de ataques que se aplicam ao canal de navegador e é o fluxo recomendado para aplicativos nativos que deseja recuperar tokens de acesso (e possivelmente tokens de atualização).

Para obter mais informações sobre fluxos de autenticação, consulte [tipos de concessão](https://identityserver4.readthedocs.io/en/release/topics/grant_types.html) na documentação do IdentityServer 4.

### <a name="performing-authentication"></a>Executar a autenticação

Para IdentityServer para emitir tokens em nome do usuário, o usuário deve entrar no IdentityServer. No entanto, IdentityServer não fornece uma interface de usuário ou o banco de dados para autenticação. Portanto, o aplicativo de referência eShopOnContainers, a identidade do ASP.NET Core é usada para essa finalidade.

O aplicativo móvel eShopOnContainers autentica com IdentityServer com o fluxo de autenticação híbrido, que é ilustrado na Figura 9-2.

![](authentication-and-authorization-images/sign-in.png "Visão geral de alto nível do processo de logon")

**Figura 9-2:** visão geral de alto nível do processo de logon

Uma solicitação é feita para `<base endpoint>:5105/connect/authorize`. Após a autenticação bem-sucedida, IdentityServer retorna uma resposta de autenticação que contém um código de autorização e um token de identidade. O código de autorização é enviado para `<base endpoint>:5105/connect/token`, que responde com tokens de atualização, identidade e acesso.

O eShopOnContainers aplicativo móvel sinais-out de IdentityServer enviando uma solicitação para `<base endpoint>:5105/connect/endsession`, com parâmetros adicionais. Após a saída, IdentityServer responde enviando um URI de redirecionamento de logout postagem de volta para o aplicativo móvel. Figura 9-3 ilustra esse processo.

![](authentication-and-authorization-images/sign-out.png "Visão geral do processo de saída")

**Figura 9-3:** visão geral do processo de saída

No aplicativo móvel do eShopOnContainers, comunicação com IdentityServer é executada pelo `IdentityService` de classe que implementa o `IIdentityService` interface. Essa interface Especifica que a classe de implementação deve fornecer `CreateAuthorizationRequest`, `CreateLogoutRequest`, e `GetTokenAsync` métodos.

#### <a name="signing-in"></a>Entrar no

Quando o usuário toca o **logon** botão o `LoginView`, o `SignInCommand` no `LoginViewModel` classe for executada, que por sua vez executa o `SignInAsync` método. O exemplo de código a seguir mostra esse método:

```csharp
private async Task SignInAsync()  
{  
    ...  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    IsLogin = true;  
    ...  
}
```

Este método chama o `CreateAuthorizationRequest` método o `IdentityService` classe, que é mostrado no exemplo de código a seguir:

```csharp
public string CreateAuthorizationRequest()
{
    // Create URI to authorization endpoint
    var authorizeRequest = new AuthorizeRequest(GlobalSetting.Instance.IdentityEndpoint);

    // Dictionary with values for the authorize request
    var dic = new Dictionary<string, string>();
    dic.Add("client_id", GlobalSetting.Instance.ClientId);
    dic.Add("client_secret", GlobalSetting.Instance.ClientSecret); 
    dic.Add("response_type", "code id_token");
    dic.Add("scope", "openid profile basket orders locations marketing offline_access");
    dic.Add("redirect_uri", GlobalSetting.Instance.IdentityCallback);
    dic.Add("nonce", Guid.NewGuid().ToString("N"));
    dic.Add("code_challenge", CreateCodeChallenge());
    dic.Add("code_challenge_method", "S256");

    // Add CSRF token to protect against cross-site request forgery attacks.
    var currentCSRFToken = Guid.NewGuid().ToString("N");
    dic.Add("state", currentCSRFToken);

    var authorizeUri = authorizeRequest.Create(dic); 
    return authorizeUri;
}

```

Esse método cria o URI para do IdentityServer [ponto de extremidade de autorização](https://identityserver4.readthedocs.io/en/release/endpoints/authorize.html), com os parâmetros necessários. O ponto de extremidade de autorização está no `/connect/authorize` na porta 5105 do ponto de extremidade base exposto como uma configuração de usuário. Para obter mais informações sobre as configurações do usuário, consulte [gerenciamento de configuração](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> A superfície de ataque do aplicativo móvel eShopOnContainers é reduzida, Implementando a chave de prova para a extensão de troca de código (PKCE) para OAuth. PKCE impede que o código de autorização sendo usados, isso será interceptado. Isso é obtido pelo cliente gera um verificador secreto, um hash que é passado na solicitação de autorização, e que está presente sem hash quando resgatar o código de autorização. Para obter mais informações sobre PKCE, consulte [chave de prova para troca de código pelos clientes pública OAuth](https://tools.ietf.org/html/rfc7636) no site da Internet Engineering Task Force.

O URI retornado é armazenado no `LoginUrl` propriedade o `LoginViewModel` classe. Quando o `IsLogin` propriedade torna-se `true`, o [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) no `LoginView` se torna visível. O `WebView` associa dados seu [ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.Source/) propriedade para o `LoginUrl` propriedade do `LoginViewModel` de classe e então faz uma solicitação de entrada para IdentityServer quando o `LoginUrl` estiver definida como Ponto de extremidade de autorização do IdentityServer. Quando IdentityServer recebe essa solicitação e o usuário não está autenticado, o `WebView` será redirecionado para a página de logon configurado, que é mostrada na Figura 9-4.

![](authentication-and-authorization-images/login.png "Página de logon exibida pelo WebView")

**Figura 9-4:** página de logon exibida pelo WebView

Depois que o logon for concluído, o [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) será redirecionado para um URI de retorno. Isso `WebView` navegação fará com que o `NavigateAsync` método o `LoginViewModel` classe a ser executada, que é mostrado no exemplo de código a seguir:

```csharp
private async Task NavigateAsync(string url)  
{  
    ...  
    var authResponse = new AuthorizeResponse(url);  
    if (!string.IsNullOrWhiteSpace(authResponse.Code))  
    {  
        var userToken = await _identityService.GetTokenAsync(authResponse.Code);  
        string accessToken = userToken.AccessToken;  

        if (!string.IsNullOrWhiteSpace(accessToken))  
        {  
            Settings.AuthAccessToken = accessToken;  
            Settings.AuthIdToken = authResponse.IdentityToken;  

            await NavigationService.NavigateToAsync<MainViewModel>();  
            await NavigationService.RemoveLastFromBackStackAsync();  
        }  
    }  
    ...  
}
```

Esse método analisa a resposta de autenticação que está contida no URI de retorno, e desde que um código de autorização válido estiver presente, ele faz uma solicitação para do IdentityServer [ponto de extremidade token](https://identityserver4.readthedocs.io/en/release/endpoints/token.html), passando o código de autorização, o Verificador de segredo PKCE e outros os parâmetros necessários. É o ponto de extremidade token no `/connect/token` na porta 5105 do ponto de extremidade base exposto como uma configuração de usuário. Para obter mais informações sobre as configurações do usuário, consulte [gerenciamento de configuração](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

>💡 **Dica**: validar URIs de retorno. Embora o aplicativo móvel eShopOnContainers não valida o URI de retorno, a prática recomendada é validar que o URI de retorno se refere a um local conhecido, para evitar ataques de redirecionamento de abrir.

Se o ponto de extremidade token recebe um código de autorização válido e o verificador de segredo PKCE, ele responde com um token de acesso, o token de identidade e o token de atualização. O token de acesso (o que permite o acesso aos recursos da API) e o token de identidade são armazenadas como configurações de aplicativo e navegação de página é executada. Portanto, é o efeito geral no aplicativo móvel eShopOnContainers isso: desde que os usuários possam autenticar com êxito com IdentityServer, ele navega para o `MainView` página, que é um [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) que exibe o `CatalogView` como seu guia selecionada.

Para obter informações sobre a navegação de página, consulte [navegação](~/xamarin-forms/enterprise-application-patterns/navigation.md). Para obter informações sobre como [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) navegação faz com que um método de modelo de exibição ser executado, consulte [usando comportamentos de navegação invocar](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors). Para obter informações sobre configurações de aplicativo, consulte [gerenciamento de configuração](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> O eShopOnContainers também permite que uma entrada fictícia quando o aplicativo está configurado para usar os serviços de simulação no `SettingsView`. Nesse modo, o aplicativo não se comunicará com IdentityServer, em vez disso, permitindo que o usuário entrar usando as credenciais.

#### <a name="signing-out"></a>Signing-out

Quando o usuário toca o **fazer logoff** no botão de `ProfileView`, o `LogoutCommand` no `ProfileViewModel` classe for executada, que por sua vez executa o `LogoutAsync` método. Esse método executa a navegação de página para o `LoginView` página, passando um `LogoutParameter` instância definida como `true` como um parâmetro. Para obter mais informações sobre como passar parâmetros durante a navegação de página, consulte [passando parâmetros durante a navegação](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation).

Quando uma exibição é criada e navegar, o `InitializeAsync` método do modelo de exibição associada do modo de exibição é executado, que executa o `Logout` método o `LoginViewModel` classe, que é mostrado no exemplo de código a seguir:

```csharp
private void Logout()  
{  
    var authIdToken = Settings.AuthIdToken;  
    var logoutRequest = _identityService.CreateLogoutRequest(authIdToken);  

    if (!string.IsNullOrEmpty(logoutRequest))  
    {  
        // Logout  
        LoginUrl = logoutRequest;  
    }  
    ...  
}
```

Este método chama o `CreateLogoutRequest` método o `IdentityService` classe, passando o token de identidade é recuperado das configurações de aplicativo como um parâmetro. Para obter mais informações sobre configurações de aplicativo, consulte [gerenciamento de configuração](~/xamarin-forms/enterprise-application-patterns/configuration-management.md). O seguinte exemplo de código mostra o `CreateLogoutRequest` método:

```csharp
public string CreateLogoutRequest(string token)  
{  
    ...  
    return string.Format("{0}?id_token_hint={1}&post_logout_redirect_uri={2}",   
        GlobalSetting.Instance.LogoutEndpoint,  
        token,  
        GlobalSetting.Instance.LogoutCallback);  
}
```

Esse método cria o URI para do IdentityServer [terminar o ponto de extremidade de sessão](https://identityserver4.readthedocs.io/en/release/endpoints/endsession.html#refendsession), com os parâmetros necessários. É o ponto de extremidade de sessão final na `/connect/endsession` na porta 5105 do ponto de extremidade base exposto como uma configuração de usuário. Para obter mais informações sobre as configurações do usuário, consulte [gerenciamento de configuração](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

O URI retornado é armazenado no `LoginUrl` propriedade o `LoginViewModel` classe. Enquanto o `IsLogin` é de propriedade `true`, o [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) no `LoginView` está visível. O `WebView` associa dados seu [ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.Source/) propriedade para o `LoginUrl` propriedade do `LoginViewModel` de classe e então faz uma solicitação de logout para IdentityServer quando o `LoginUrl` estiver definida como Ponto de extremidade de IdentityServer fim sessão. Quando IdentityServer recebe essa solicitação, desde que o usuário está conectado, ocorre logout. A autenticação é controlada com um cookie gerenciado pelo middleware de autenticação de cookie do ASP.NET Core. Portanto, sair IdentityServer remove o cookie de autenticação e envia um redirecionamento de logoff de postagem de URI de volta ao cliente.

No aplicativo móvel, o [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) será redirecionado para o URI de redirecionamento de logout post. Isso `WebView` navegação fará com que o `NavigateAsync` método o `LoginViewModel` classe a ser executada, que é mostrado no exemplo de código a seguir:

```csharp
private async Task NavigateAsync(string url)  
{  
    ...  
    Settings.AuthAccessToken = string.Empty;  
    Settings.AuthIdToken = string.Empty;  
    IsLogin = false;  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    ...  
}
```

Esse método limpa o token de identidade e o token de acesso em configurações do aplicativo e define o `IsLogin` propriedade `false`, que faz com que o [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) no `LoginView` página para se tornar invisíveis . Por fim, o `LoginUrl` está definida como o URI de IdentityServer [ponto de extremidade de autorização](https://identityserver4.readthedocs.io/en/release/endpoints/authorize.html), com os parâmetros necessários, em preparação para a próxima vez, o usuário inicia uma entrada.

Para obter informações sobre a navegação de página, consulte [navegação](~/xamarin-forms/enterprise-application-patterns/navigation.md). Para obter informações sobre como [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/) navegação faz com que um método de modelo de exibição ser executado, consulte [usando comportamentos de navegação invocar](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors). Para obter informações sobre configurações de aplicativo, consulte [gerenciamento de configuração](~/xamarin-forms/enterprise-application-patterns/configuration-management.md).

> [!NOTE]
> O eShopOnContainers também permite que uma simulação saída quando o aplicativo está configurado para usar serviços de simulação de SettingsView. Nesse modo, o aplicativo não se comunicará com IdentityServer e, em vez disso, limpa todos os tokens armazenados em configurações do aplicativo.

<a name="authorization" />

## <a name="authorization"></a>Autorização

Após a autenticação, da web do ASP.NET Core APIs geralmente precisam autorizar o acesso, que permite que um serviço de APIs disponível para alguns usuários autenticados, mas não para todos.

Restringir o acesso a uma rota do MVC do ASP.NET Core pode ser obtida aplicando um atributo de autorizar a um controlador ou ação, o que limita o acesso ao controlador de ou para usuários autenticados, conforme mostrado no exemplo de código a seguir:

```csharp
[Authorize]  
public class BasketController : Controller  
{  
    ...  
}
```

Se um usuário não autorizado tentar acessar um controlador ou ação que está marcada com o `Authorize` atributo, a estrutura do MVC retorna um código de status HTTP (não autorizado) 401.

> [!NOTE]
> Parâmetros podem ser especificados no `Authorize` atributo para restringir uma API para usuários específicos. Para obter mais informações, consulte [autorização](/aspnet/core/security/authorization/introduction/).

IdentityServer pode ser integrado no fluxo de trabalho de autorização para que os tokens de acesso que ele fornece autorização de controle. Essa abordagem é mostrada na Figura 9-5.

![](authentication-and-authorization-images/authorization.png "Autorização por um token de acesso")

**Figura 9-5:** autorização por um token de acesso

O aplicativo móvel eShopOnContainers se comunica com o microsserviço de identidade e solicita um token de acesso como parte do processo de autenticação. O token de acesso é então encaminhado para as APIs expostas pelo microservices a ordenação e cesta como parte das solicitações de acesso. Tokens de acesso contêm informações sobre o cliente e o usuário. APIs, em seguida, usam essas informações para autorizar o acesso aos seus dados. Para obter informações sobre como configurar IdentityServer para proteger APIs, consulte [Configurando recursos da API](#configuring-api-resources).

### <a name="configuring-identityserver-to-perform-authorization"></a>Configurando IdentityServer para executar a autorização

Para executar a autorização com IdentityServer, seu middleware de autorização deve ser adicionado ao pipeline de solicitação HTTP do aplicativo da web. O middleware é adicionado a `ConfigureAuth` método no aplicativo da web `Startup` classe, que é invocado do `Configure` método e é demonstrado no seguinte exemplo de código do aplicativo eShopOnContainers Referência:

```csharp
protected virtual void ConfigureAuth(IApplicationBuilder app)  
{  
    var identityUrl = Configuration.GetValue<string>("IdentityUrl");  
    app.UseIdentityServerAuthentication(new IdentityServerAuthenticationOptions  
    {  
        Authority = identityUrl.ToString(),  
        ScopeName = "basket",  
        RequireHttpsMetadata = false  
    });  
} 
```

Esse método garante que a API só pode ser acessada com um token de acesso válido. O middleware valida o token de entrada para garantir que ele é enviado de um emissor confiável e valida que o token é válido a ser usado com a API que recebe. Portanto, navegando para o controlador de ordenação ou carrinho retornará um 401 (não autorizado) código de status HTTP, indicando que um token de acesso é necessário.

> [!NOTE]
> Middleware de autorização do IdentityServer deve ser adicionado ao pipeline de solicitação HTTP do aplicativo da web antes de adicionar MVC com `app.UseMvc()` ou `app.UseMvcWithDefaultRoute()`.

### <a name="making-access-requests-to-apis"></a>Fazer solicitações de acesso a APIs

Ao fazer solicitações para microservices carrinho e ordenação, o acesso de token, obtido IdentityServer durante o processo de autenticação, deve ser incluído na solicitação, conforme mostrado no exemplo de código a seguir:

```csharp
var authToken = Settings.AuthAccessToken;  
Order = await _ordersService.GetOrderAsync(Convert.ToInt32(order.OrderNumber), authToken);
```

O token de acesso é armazenado como uma configuração de aplicativo e é recuperado do armazenamento específico da plataforma e incluído na chamada para o `GetOrderAsync` método o `OrderService` classe.

Da mesma forma, o token de acesso deve ser incluído ao enviar dados para um IdentityServer protegidas API, conforme mostrado no exemplo de código a seguir:

```csharp
var authToken = Settings.AuthAccessToken;  
await _basketService.UpdateBasketAsync(new CustomerBasket  
{  
    BuyerId = userInfo.UserId,   
    Items = BasketItems.ToList()  
}, authToken);
```

O token de acesso é recuperado do armazenamento específico da plataforma e incluído na chamada para o `UpdateBasketAsync` método o `BasketService` classe.

O `RequestProvider` classe, no aplicativo móvel do eShopOnContainers, usa o `HttpClient` classe para fazer solicitações para as APIs RESTful expostas pelo aplicativo eShopOnContainers referência. Quando fazer uma solicitação para a ordem e o carrinho de APIs, o que requer autorização, um token de acesso válido deve ser incluído na solicitação. Isso é feito adicionando o token de acesso para os cabeçalhos da `HttpClient` instância, conforme demonstrado no exemplo de código a seguir:

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
```

O `DefaultRequestHeaders` propriedade do `HttpClient` classe expõe os cabeçalhos que são enviados com cada solicitação, e o token de acesso é adicionado para o `Authorization` cabeçalho prefixado com a cadeia de caracteres `Bearer`. Quando a solicitação é enviada para uma API RESTful, o valor de `Authorization` cabeçalho é extraído e validado para garantir que ele foi enviado de um emissor confiável e usada para determinar se o usuário tem permissão para chamar a API que recebe.

Para obter mais informações sobre como o aplicativo móvel eShopOnContainers faz solicitações da web, consulte [acessar dados remotos](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md).

## <a name="summary"></a>Resumo

Há várias abordagens para a integração de autenticação e autorização em um aplicativo xamarin. Forms que se comunica com um aplicativo ASP.NET MVC. O aplicativo móvel eShopOnContainers executa autenticação e autorização com um microsserviço de identidade em contêineres que usa 4 IdentityServer. IdentityServer é uma estrutura de OpenID Connect e OAuth 2.0 do código-fonte aberto para ASP.NET Core que se integra a identidade do ASP.NET Core para realizar a autenticação de token de portador.

O aplicativo móvel solicita tokens de segurança de IdentityServer, para autenticar um usuário ou para acessar um recurso. Ao acessar um recurso, um token de acesso deve ser incluído na solicitação para APIs que exigem autorização. Middleware do IdentityServer valida os tokens de acesso de entrada para garantir que elas são enviadas de um emissor confiável e se são válidos para ser usado com a API que recebe.


## <a name="related-links"></a>Links relacionados

- [Baixar eBook (2Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (sample)](https://github.com/dotnet-architecture/eShopOnContainers)
