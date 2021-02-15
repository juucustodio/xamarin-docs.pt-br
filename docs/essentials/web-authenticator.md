---
title: 'Xamarin.Essentials: Autenticador da Web'
description: Este documento descreve a classe webauthenticator no Xamarin.Essentials , que permite que você inicie fluxos de autenticação baseados em navegador que escutam um retorno de chamada para o aplicativo.
ms.assetid: 3D95371E-5D59-440E-8D31-F3C04E493DC1
author: redth
ms.author: jodick
ms.date: 03/26/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4ab7c5dab6a414e15531e0e0e812d604e05ab1cc
ms.sourcegitcommit: 3edcc63fcf86409b73cd6e5dc77f0093a99b3f87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2021
ms.locfileid: "98062596"
---
# <a name="no-locxamarinessentials-web-authenticator"></a>Xamarin.Essentials: Autenticador da Web

A classe **webauthenticator** permite que você inicie fluxos baseados em navegador que escutam um retorno de chamada para uma URL específica registrada para o aplicativo.

## <a name="overview"></a>Visão geral

Muitos aplicativos exigem a adição de autenticação de usuário, e isso geralmente significa permitir que seus usuários entrem nas contas existentes da Microsoft, Facebook, Google e agora Apple.

A [MSAL (biblioteca de autenticação da Microsoft)](/azure/active-directory/develop/msal-overview) fornece uma excelente solução de chave para adicionar autenticação ao seu aplicativo. Há até mesmo suporte para aplicativos Xamarin em seu pacote NuGet do cliente.

Se você estiver interessado em usar seu próprio serviço Web para autenticação, é possível usar o **webauthenticator** para implementar a funcionalidade do lado do cliente.

## <a name="why-use-a-server-back-end"></a>Por que usar um back-end do servidor?

Muitos provedores de autenticação foram movidos para apenas a oferta de fluxos de autenticação explícitos ou com duas pernas para garantir uma melhor segurança. Isso significa que você precisará de um _' segredo do cliente '_ do provedor para concluir o fluxo de autenticação. Infelizmente, os aplicativos móveis não são um ótimo lugar para armazenar segredos, e tudo o que é armazenado em um código, binários ou de outro aplicativo móvel é considerado inseguro.

A prática recomendada aqui é usar um back-end da Web como uma camada intermediária entre seu aplicativo móvel e o provedor de autenticação.

> [!IMPORTANT]
> É altamente recomendável usar bibliotecas de autenticação somente móveis e padrões mais antigos, que não aproveitam um back-end da Web no fluxo de autenticação devido à falta de segurança inerente ao armazenamento de segredos do cliente.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

Para acessar a funcionalidade **Webauthenticator** , é necessária a configuração específica da plataforma a seguir.

# <a name="android"></a>[Android](#tab/android)

O Android requer uma configuração de filtro de intenção para manipular o URI de retorno de chamada. Isso é facilmente conseguido por meio da subclasse da `WebAuthenticatorCallbackActivity` classe:

```csharp
const string CALLBACK_SCHEME = "myapp";

[Activity(NoHistory = true, LaunchMode = LaunchMode.SingleTop)]
[IntentFilter(new[] { Android.Content.Intent.ActionView },
    Categories = new[] { Android.Content.Intent.CategoryDefault, Android.Content.Intent.CategoryBrowsable },
    DataScheme = CALLBACK_SCHEME)]
public class WebAuthenticationCallbackActivity : Xamarin.Essentials.WebAuthenticatorCallbackActivity
{
}
```
Se a versão do Android de destino do seu projeto estiver definida como **Android 11 (API R 30)** , você deverá atualizar seu manifesto do Android com consultas que são usadas com os novos [requisitos de visibilidade de pacote](https://developer.android.com/preview/privacy/package-visibility).

Abra o arquivo **AndroidManifest.xml** na pasta Propriedades e adicione o seguinte dentro do nó do manifesto:
```XML
<queries>
    <intent>
        <action android:name="android.support.customtabs.action.CustomTabsService" />
    </intent>
</queries>
```

# <a name="ios"></a>[iOS](#tab/ios)

No iOS, você precisará adicionar o padrão de URI de retorno de chamada do aplicativo ao seu info. plist, como:

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLName</key>
        <string>xamarinessentials</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>xamarinessentials</string>
        </array>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
    </dict>
</array>
```

Você também precisará substituir seus `AppDelegate` `OpenUrl` `ContinueUserActivity` métodos e para chamar no Essentials:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    if (Xamarin.Essentials.Platform.OpenUrl(app, url, options))
        return true;

    return base.OpenUrl(app, url, options);
}

public override bool ContinueUserActivity(UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    if (Xamarin.Essentials.Platform.ContinueUserActivity(application, userActivity, completionHandler))
        return true;
    return base.ContinueUserActivity(application, userActivity, completionHandler);
}
```

# <a name="uwp"></a>[UWP](#tab/uwp)

Para UWP, você precisará declarar seu URI de retorno de chamada em seu `Package.appxmanifest` arquivo:

```xml
    <Extensions>
        <uap:Extension Category="windows.protocol">
            <uap:Protocol Name="myapp">
                <uap:DisplayName>My App</uap:DisplayName>
            </uap:Protocol>
        </uap:Extension>
    </Extensions>
```

-----

## <a name="using-webauthenticator"></a>Usando webauthenticator

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A API consiste principalmente em um único método `AuthenticateAsync` que usa dois parâmetros: a URL que deve ser usada para iniciar o fluxo do navegador da Web e o URI que você espera que o fluxo finalmente chame de volta e qual seu aplicativo está registrado para ser capaz de lidar.

O resultado é um `WebAuthenticatorResult` que inclui qualquer parâmetro de consulta analisado a partir do URI de retorno de chamada:

```csharp
var authResult = await WebAuthenticator.AuthenticateAsync(
        new Uri("https://mysite.com/mobileauth/Microsoft"),
        new Uri("myapp://"));

var accessToken = authResult?.AccessToken;
```

A `WebAuthenticator` API se encarrega de iniciar a URL no navegador e aguardar até que o retorno de chamada seja recebido:

![Fluxo de autenticação da Web típico](images/web-authenticator.png)

Se o usuário cancelar o fluxo em qualquer ponto, um `TaskCanceledException` será lançado.

## <a name="platform-differences"></a>Diferenças de plataforma

# <a name="android"></a>[Android](#tab/android)

As guias personalizadas são usadas sempre que disponíveis, caso contrário, uma intenção é iniciada para a URL.

# <a name="ios"></a>[iOS](#tab/ios)

No iOS 12 ou superior, `ASWebAuthenticationSession` é usado.  No iOS 11, `SFAuthenticationSession` é usado.  Em versões anteriores do iOS, `SFSafariViewController` é usado se estiver disponível; caso contrário, o Safari será usado.

# <a name="uwp"></a>[UWP](#tab/uwp)

No UWP, o `WebAuthenticationBroker` é usado se houver suporte, caso contrário, o navegador do sistema será usado.

-----

## <a name="apple-sign-in"></a>Entrada na Apple

De acordo com as [diretrizes de revisão da Apple](https://developer.apple.com/app-store/review/guidelines/#sign-in-with-apple), se seu aplicativo usar qualquer serviço de logon social para autenticar, ele também deverá oferecer a entrada da Apple como uma opção.

Para adicionar a entrada da Apple aos seus aplicativos, primeiro você precisará [configurar seu aplicativo para usar a entrada da Apple](../ios/platform/ios13/sign-in.md).

Para o iOS 13 e superior, você desejará chamar o `AppleSignInAuthenticator.AuthenticateAsync()` método. Isso usará as APIs de entrada nativas da Apple nos bastidores para que os usuários obtenham a melhor experiência possível nesses dispositivos. Você pode escrever seu código compartilhado para usar a API correta em tempo de execução da seguinte maneira:

```csharp
var scheme = "..."; // Apple, Microsoft, Google, Facebook, etc.
WebAuthenticatorResult r = null;

if (scheme.Equals("Apple")
    && DeviceInfo.Platform == DevicePlatform.iOS
    && DeviceInfo.Version.Major >= 13)
{
    // Use Native Apple Sign In API's
    r = await AppleSignInAuthenticator.AuthenticateAsync();
}
else
{
    // Web Authentication flow
    var authUrl = new Uri(authenticationUrl + scheme);
    var callbackUrl = new Uri("xamarinessentials://");

    r = await WebAuthenticator.AuthenticateAsync(authUrl, callbackUrl);
}

var authToken = string.Empty;
if (r.Properties.TryGetValue("name", out var name) && !string.IsNullOrEmpty(name))
    authToken += $"Name: {name}{Environment.NewLine}";
if (r.Properties.TryGetValue("email", out var email) && !string.IsNullOrEmpty(email))
    authToken += $"Email: {email}{Environment.NewLine}";

// Note that Apple Sign In has an IdToken and not an AccessToken
authToken += r?.AccessToken ?? r?.IdToken;
```

> [!TIP]
> Para dispositivos que não sejam iOS 13, isso iniciará o fluxo de autenticação da Web, que também pode ser usado para habilitar a entrada da Apple em seus dispositivos Android e UWP.
> Você pode entrar em sua conta do iCloud no simulador de iOS para testar a entrada da Apple.

-----

## <a name="aspnet-core-server-back-end"></a>Back-end do servidor do ASP.NET Core

É possível usar a `WebAuthenticator` API com qualquer serviço de back-end da Web.  Para usá-lo com um aplicativo ASP.NET Core, primeiro você precisa configurar o aplicativo Web com as seguintes etapas:

1. Configure os [provedores de autenticação social externos](/aspnet/core/security/authentication/social/?tabs=visual-studio) desejados em um aplicativo Web ASP.NET Core.
2. Defina o esquema de autenticação padrão como `CookieAuthenticationDefaults.AuthenticationScheme` em sua `.AddAuthentication()` chamada.
3. Use `.AddCookie()` em sua chamada do Startup.cs `.AddAuthentication()` .
4. Todos os provedores devem ser configurados com `.SaveTokens = true;` .


```csharp
services.AddAuthentication(o =>
    {
        o.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddFacebook(fb =>
    {
        fb.AppId = Configuration["FacebookAppId"];
        fb.AppSecret = Configuration["FacebookAppSecret"];
        fb.SaveTokens = true;
    });
```

> [!TIP]
> Se você quiser incluir a entrada da Apple, poderá usar o `AspNet.Security.OAuth.Apple` pacote NuGet.  Você pode exibir o [exemplo de startup.cs](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/Startup.cs#L32-L60) completo no repositório GitHub do Essentials.

### <a name="add-a-custom-mobile-auth-controller"></a>Adicionar um controlador de autenticação móvel personalizado

Com um fluxo de autenticação móvel, geralmente é desejável iniciar o fluxo diretamente para um provedor que o usuário escolheu (por exemplo, clicando em um botão "Microsoft" na tela de entrada do aplicativo).  Também é importante poder retornar informações relevantes para seu aplicativo em um URI de retorno de chamada específico para finalizar o fluxo de autenticação.

Para conseguir isso, use um controlador de API personalizado:

```csharp
[Route("mobileauth")]
[ApiController]
public class AuthController : ControllerBase
{
    const string callbackScheme = "myapp";

    [HttpGet("{scheme}")] // eg: Microsoft, Facebook, Apple, etc
    public async Task Get([FromRoute]string scheme)
    {
        // 1. Initiate authentication flow with the scheme (provider)
        // 2. When the provider calls back to this URL
        //    a. Parse out the result
        //    b. Build the app callback URL
        //    c. Redirect back to the app
    }
}
```

A finalidade desse controlador é inferir o esquema (provedor) que o aplicativo está solicitando e iniciar o fluxo de autenticação com o provedor social. Quando o provedor chama de volta para o back-end da Web, o controlador analisa o resultado e redireciona para o URI de retorno de chamada do aplicativo com parâmetros.

Às vezes, talvez você queira retornar dados como o provedor de `access_token` volta para o aplicativo, o que pode ser feito por meio dos parâmetros de consulta do URI de retorno de chamada. Ou talvez você queira, em vez disso, criar sua própria identidade no servidor e retornar seu próprio token para o aplicativo. O que e como você faz essa parte é de sua escolha!

Confira o [exemplo de controlador completo](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/Controllers/MobileAuthController.cs) no repositório do Essentials.

> [!NOTE]
> O exemplo acima demonstra como retornar o token de acesso do provedor de autenticação de terceiros (IE: OAuth). Para obter um token que você pode usar para autorizar solicitações da Web ao próprio back-end da Web, você deve criar seu próprio token em seu aplicativo Web e, em vez disso, retornar isso.  A [visão geral da autenticação ASP.NET Core](/aspnet/core/security/authentication) tem mais informações sobre cenários de autenticação avançada no ASP.NET Core.

-----
## <a name="api"></a>API

- [Código-fonte webauthenticator](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/WebAuthenticator)
- [Documentação da API webauthenticator](xref:Xamarin.Essentials.WebAuthenticator)
- [Exemplo do ASP.NET Core Server](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/)
