---
title: Use entrar com a Apple para Xamarin. Forms
description: Saiba como implementar a entrada com a Apple em seus aplicativos móveis do Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 2E47E7F2-93D4-4CA3-9E66-247466D25E4D
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 09/10/2019
ms.openlocfilehash: df011a6eb72b6eb30af0a197d4be48b0f2494384
ms.sourcegitcommit: fc689c1a6b641c124378dedc1bd157d96fc759a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71319517"
---
# <a name="use-sign-in-with-apple-in-xamarinforms"></a>Use entrar com a Apple no Xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/signinwithapple/)

Entrar com a Apple é para todos os novos aplicativos no iOS 13 que usam serviços de autenticação de terceiros. Os detalhes de implementação entre o iOS e o Android são bastante diferentes. Este guia explica como você pode fazer isso hoje em Xamarin. Forms.

Neste guia e exemplo, os serviços de plataforma específicos são usados para manipular a entrada com a Apple:

- Android usando um serviço Web genérico conversando com Azure Functions com OpenID/OpenAuth
- o iOS usa a API nativa para autenticação no iOS 13 e faz fallback para um serviço Web genérico para iOS 12 e inferior

## <a name="a-sample-apple-sign-in-flow"></a>Um exemplo de fluxo de entrada da Apple

Este exemplo oferece uma implementação conceituada para que a entrada da Apple funcione em seu aplicativo Xamarin. Forms.

Usamos dois Azure Functions para ajudar com o fluxo de autenticação:

1. `applesignin_auth`-Gera a URL de autorização de entrada da Apple e redireciona para ela.  Fazemos isso no lado do servidor, em vez do aplicativo móvel, para que possamos armazenar em cache `state` e validá-lo quando os servidores da Apple enviarem um retorno de chamada.
2. `applesignin_callback`-Manipula o retorno de chamada de POSTAgem da Apple e troca com segurança o código de autorização para um token de acesso e token de ID.  Por fim, ele redireciona de volta para o esquema de URI do aplicativo, passando os tokens em um fragmento de URL.

O aplicativo móvel se registra para manipular o esquema de URI personalizado que selecionamos (nesse caso `xamarinformsapplesignin://`) para que `applesignin_callback` a função possa Retransmitir os tokens para ele.

Quando o usuário inicia a autenticação, ocorrem as seguintes etapas:

1. O aplicativo móvel gera um `nonce` valor `state` e e os passa para a `applesignin_auth` função do Azure.
2. A `applesignin_auth` função do Azure gera uma URL de autorização de entrada da Apple ( `state` usando `nonce`a fornecida e) e redireciona o navegador de aplicativos móveis para ele.
3. O usuário insere suas credenciais com segurança na página de autorização de entrada da Apple hospedada nos servidores da Apple.
4. Após a conclusão do fluxo de entrada da Apple nos servidores da Apple, a Apple redireciona para `redirect_uri` o que será a `applesignin_callback` função do Azure.
5. A solicitação da Apple enviada para a `applesignin_callback` função é validada para garantir que `state` o correto seja retornado e que as declarações de token de ID sejam válidas.
6. A `applesignin_callback` função do Azure troca `code` o Postado para ele pela Apple, para um _token de acesso_, _token de atualização_e _token de ID_ (que contém declarações sobre a ID de usuário, o nome e o email).
7. A `applesignin_callback` função do Azure finalmente redireciona de volta para o esquema de URI do aplicativo`xamarinformsapplesignin://`() acrescentando um fragmento de URI com `xamarinformsapplesignin://#access_token=...&refresh_token=...&id_token=...`os tokens (por exemplo,).
8. O aplicativo móvel analisa o fragmento de URI em um `AppleAccount` e valida a `nonce` declaração recebida corresponde `nonce` ao gerado no início do fluxo.
9. O aplicativo móvel agora está autenticado!

## <a name="azure-functions"></a>Verificação de

Este exemplo usa Azure Functions. Como alternativa, um controlador ASP.NET Core ou uma solução de servidor Web semelhante pode fornecer a mesma funcionalidade.

### <a name="configuration"></a>Configuração

Várias configurações de aplicativo precisam ser configuradas ao usar Azure Functions:

- `APPLE_SIGNIN_KEY_ID`-Este é o `KeyId` de antes.
- `APPLE_SIGNIN_TEAM_ID`-Normalmente, essa _ID de equipe_ é encontrada em seu [perfil de associação](https://developer.apple.com/account/#/membership)
- `APPLE_SIGNIN_SERVER_ID`: Isso é o `ServerId` de antes.  *Não* é a ID do _pacote_de aplicativo, mas sim o *identificador* da *ID de serviços* que você criou.
- `APPLE_SIGNIN_APP_CALLBACK_URI`-Este é o esquema de URI personalizado para o qual você deseja redirecionar para seu aplicativo.  Neste exemplo `xamarinformsapplesignin://` , é usado.
- `APPLE_SIGNIN_REDIRECT_URI`-A *URL de redirecionamento* que você configura ao criar sua *ID de serviços* na seção de configuração de *entrada da Apple* .  Para testar, ele pode ser semelhante a:`http://local.test:7071/api/applesignin_callback`
- `APPLE_SIGNIN_P8_KEY`-O conteúdo de texto do `.p8` arquivo, com todas as `\n` novas linhas removidas, portanto, é uma cadeia de caracteres longa

### <a name="security-considerations"></a>Considerações sobre segurança

**Nunca** armazene sua chave P8 dentro do código do aplicativo. O código do aplicativo é fácil de baixar e desmontar. 

Ela também é considerada uma prática inadequada para usar `WebView` um para hospedar o fluxo de autenticação e para interceptar eventos de navegação de URL para obter o código de autorização. No momento, não há nenhuma maneira totalmente segura de manipular a entrada com a Apple em dispositivos não iOS13 + sem hospedar algum código em um servidor para lidar com a troca de tokens. É recomendável hospedar o código de geração de URL de autorização em um servidor para que você possa armazenar em cache o estado e validá-lo quando a Apple emitir um retorno de chamada POST para o servidor.

## <a name="a-cross-platform-sign-in-service"></a>Um serviço de conexão entre plataformas

Usando o Xamarin. Forms DependencyService, você pode criar serviços de autenticação separados que usam os serviços de plataforma no iOS e um serviço Web genérico para Android e outras plataformas que não sejam iOS com base em uma interface compartilhada.

```csharp
public interface IAppleSignInService
{
    bool Callback(string url);

    Task<AppleAccount> SignInAsync();
}
```

No iOS, as APIs nativas são usadas:

```csharp
public class AppleSignInServiceiOS : IAppleSignInService
{
#if __IOS__13
    AuthManager authManager;
#endif

    bool Is13 => UIDevice.CurrentDevice.CheckSystemVersion(13, 0);
    WebAppleSignInService webSignInService;

    public AppleSignInServiceiOS()
    {
        if (!Is13)
            webSignInService = new WebAppleSignInService();
    }

    public async Task<AppleAccount> SignInAsync()
    {
        // Fallback to web for older iOS versions
        if (!Is13)
            return await webSignInService.SignInAsync();

        AppleAccount appleAccount = default;

#if __IOS__13
        var provider = new ASAuthorizationAppleIdProvider();
        var req = provider.CreateRequest();

        authManager = new AuthManager(UIApplication.SharedApplication.KeyWindow);

        req.RequestedScopes = new[] { ASAuthorizationScope.FullName, ASAuthorizationScope.Email };
        var controller = new ASAuthorizationController(new[] { req });

        controller.Delegate = authManager;
        controller.PresentationContextProvider = authManager;

        controller.PerformRequests();

        var creds = await authManager.Credentials;

        if (creds == null)
            return null;

        appleAccount = new AppleAccount();
        appleAccount.IdToken = JwtToken.Decode(new NSString(creds.IdentityToken, NSStringEncoding.UTF8).ToString());
        appleAccount.Email = creds.Email;
        appleAccount.UserId = creds.User;
        appleAccount.Name = NSPersonNameComponentsFormatter.GetLocalizedString(creds.FullName, NSPersonNameComponentsFormatterStyle.Default, NSPersonNameComponentsFormatterOptions.Phonetic);
        appleAccount.RealUserStatus = creds.RealUserStatus.ToString();
#endif

        return appleAccount;
    }

    public bool Callback(string url) => true;
}

#if __IOS__13
class AuthManager : NSObject, IASAuthorizationControllerDelegate, IASAuthorizationControllerPresentationContextProviding
{
    public Task<ASAuthorizationAppleIdCredential> Credentials
        => tcsCredential?.Task;

    TaskCompletionSource<ASAuthorizationAppleIdCredential> tcsCredential;

    UIWindow presentingAnchor;

    public AuthManager(UIWindow presentingWindow)
    {
        tcsCredential = new TaskCompletionSource<ASAuthorizationAppleIdCredential>();
        presentingAnchor = presentingWindow;
    }

    public UIWindow GetPresentationAnchor(ASAuthorizationController controller)
        => presentingAnchor;

    [Export("authorizationController:didCompleteWithAuthorization:")]
    public void DidComplete(ASAuthorizationController controller, ASAuthorization authorization)
    {
        var creds = authorization.GetCredential<ASAuthorizationAppleIdCredential>();
        tcsCredential?.TrySetResult(creds);
    }

    [Export("authorizationController:didCompleteWithError:")]
    public void DidComplete(ASAuthorizationController controller, NSError error)
        => tcsCredential?.TrySetException(new Exception(error.LocalizedDescription));
}
#endif
```

O sinalizador `__IOS__13` de compilação é usado para fornecer suporte para IOS 13, bem como versões herdadas que fallback para o serviço Web genérico.

No Android, o serviço Web genérico com Azure Functions é usado:

```csharp
public class WebAppleSignInService : IAppleSignInService
{
    // IMPORTANT: This is what you register each native platform's url handler to be
    public const string CallbackUriScheme = "xamarinformsapplesignin";
    public const string InitialAuthUrl = "http://local.test:7071/api/applesignin_auth";

    string currentState;
    string currentNonce;

    TaskCompletionSource<AppleAccount> tcsAccount = null;

    public bool Callback(string url)
    {
        // Only handle the url with our callback uri scheme
        if (!url.StartsWith(CallbackUriScheme + "://"))
            return false;

        // Ensure we have a task waiting
        if (tcsAccount != null && !tcsAccount.Task.IsCompleted)
        {
            try
            {
                // Parse the account from the url the app opened with
                var account = AppleAccount.FromUrl(url);

                // IMPORTANT: Validate the nonce returned is the same as our originating request!!
                if (!account.IdToken.Nonce.Equals(currentNonce))
                    tcsAccount.TrySetException(new InvalidOperationException("Invalid or non-matching nonce returned"));

                // Set our account result
                tcsAccount.TrySetResult(account);
            }
            catch (Exception ex)
            {
                tcsAccount.TrySetException(ex);
            }
        }

        tcsAccount.TrySetResult(null);
        return false;
    }

    public async Task<AppleAccount> SignInAsync()
    {
        tcsAccount = new TaskCompletionSource<AppleAccount>();

        // Generate state and nonce which the server will use to initial the auth
        // with Apple.  The nonce should flow all the way back to us when our function
        // redirects to our app
        currentState = Util.GenerateState();
        currentNonce = Util.GenerateNonce();

        // Start the auth request on our function (which will redirect to apple)
        // inside a browser (either SFSafariViewController, Chrome Custom Tabs, or native browser)
        await Xamarin.Essentials.Browser.OpenAsync($"{InitialAuthUrl}?&state={currentState}&nonce={currentNonce}",
            Xamarin.Essentials.BrowserLaunchMode.SystemPreferred);

        return await tcsAccount.Task;
    }
}
```

## <a name="summary"></a>Resumo

Este artigo descreveu as etapas necessárias para configurar a entrada com a Apple para uso em seus aplicativos Xamarin. Forms.

## <a name="related-links"></a>Links relacionados

- [XamarinFormsAppleSignIn (exemplo)](https://github.com/Redth/Xamarin.AppleSignIn.Sample)
- [Entre com as diretrizes da Apple](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/)
