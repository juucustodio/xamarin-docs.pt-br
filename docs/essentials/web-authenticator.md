---
title: 'Xamarin.Essentials: Autenticador da Web'
description: Este documento descreve a classe WebAuthenticator em Xamarin.Essentials, que permite iniciar fluxos de autenticação baseados no navegador que escutam um retorno de chamada para o aplicativo.
ms.assetid: 3D95371E-5D59-440E-8D31-F3C04E493DC1
author: redth
ms.author: jodick
ms.date: 03/26/2020
ms.openlocfilehash: b090ea8491afccb7078de8333a44a4888819a46a
ms.sourcegitcommit: ddd2cb3a102df339bb269380cb2c0617dbb1acb7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81688257"
---
# <a name="xamarinessentials-web-authenticator"></a>Xamarin.Essentials: Autenticador da Web

A classe **WebAuthenticator** permite que você inicie fluxos baseados no navegador que ouçam um retorno de chamada para uma URL específica registrada no aplicativo.

## <a name="overview"></a>Visão geral

Muitos aplicativos exigem a adição de autenticação do usuário, e isso muitas vezes significa permitir que seus usuários entrem em suas contas existentes da Microsoft, Facebook, Google e agora Apple Sign In.

[A Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) fornece uma excelente solução de turn-key para adicionar autenticação ao seu aplicativo. Há até suporte para aplicativos Xamarin em seu pacote NuGet cliente.

Se você estiver interessado em usar seu próprio serviço web para autenticação, é possível usar o **WebAuthenticator** para implementar a funcionalidade do lado do cliente.

## <a name="why-use-a-server-back-end"></a>Por que usar um back-end do servidor?

Muitos provedores de autenticação passaram a oferecer apenas fluxos de autenticação explícitos ou de duas pernas para garantir uma melhor segurança. Isso significa que você precisará de um _'segredo de cliente'_ do provedor para concluir o fluxo de autenticação. Infelizmente, os aplicativos móveis não são um ótimo lugar para armazenar segredos e qualquer coisa armazenada no código de um aplicativo móvel, binários ou de outra forma é geralmente considerada insegura.

A melhor prática aqui é usar um backend web como uma camada média entre seu aplicativo móvel e o provedor de autenticação.

> [!IMPORTANT]
> Recomendamos fortemente contra o uso de bibliotecas e padrões de autenticação somente para dispositivos móveis mais antigos que não aproveitam um backend da Web no fluxo de autenticação devido à sua inerente falta de segurança para armazenar segredos de clientes.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

Para acessar a funcionalidade **do WebAuthenticator,** é necessária a seguinte configuração específica da plataforma.

# <a name="android"></a>[Android](#tab/android)

O Android requer uma configuração do Filtro de Intenções para lidar com o uri de retorno de chamada. Isso é facilmente realizado subclassificando a `WebAuthenticatorCallbackActivity` classe:

> [!NOTE]
> Você deve considerar a implementação do [Android App Links](https://developer.android.com/training/app-links/) para lidar com o URI de retorno de chamada e garantir que seu aplicativo seja o único que pode se registrar para lidar com o URI de retorno de chamada.

```csharp
const string CALLBACK_SCHEME = "myapp";

[Activity(NoHistory = true, LaunchMode = LaunchMode.SingleTop)]
[IntentFilter(new[] { Intent.ActionView },
    Categories = new[] { Intent.CategoryDefault, Intent.CategoryBrowsable },
    DataScheme = CALLBACK_SCHEME)]
public class WebAuthenticationCallbackActivity : Xamarin.Essentials.WebAuthenticatorCallbackActivity
{
}
```

Você também precisará ligar de volta `OnResume` para essentials `MainActivity`a partir da substituição em seu :

```csharp
protected override void OnResume()
{
    base.OnResume();

    Xamarin.Essentials.Platform.OnResume();
}
```

# <a name="ios"></a>[iOS](#tab/ios)

No iOS, você precisará adicionar o padrão URI de retorno de chamada do seu aplicativo ao seu Info.plist.

> [!NOTE]
> Você deve considerar o uso do [Universal App Links](https://developer.apple.com/documentation/uikit/inter-process_communication/allowing_apps_and_websites_to_link_to_your_content) para registrar o URI de retorno de chamada do seu aplicativo como uma prática recomendada.

Você também precisará substituir `AppDelegate`o `OpenUrl` método de sua chamada para chamar o Essentials:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    if (Xamarin.Essentials.Platform.OpenUrl(app, url, options))
        return true;

    return base.OpenUrl(app, url, options);
}
```

# <a name="uwp"></a>[UWP](#tab/uwp)

Para UWP, você precisará declarar seu URI `Package.appxmanifest` de retorno de chamada em seu arquivo:

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

## <a name="using-webauthenticator"></a>Usando o WebAuthenticator

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

A API consiste principalmente em `AuthenticateAsync` um único método que leva dois parâmetros: o url que deve ser usado para iniciar o fluxo do navegador da Web, e o Uri que você espera que o fluxo finalmente ligue de volta e que seu aplicativo está registrado para ser capaz de lidar.

O resultado `WebAuthenticatorResult` é um que inclui quaisquer parâmetros de consulta analisados a partir do URI de retorno de chamada:

```csharp
var authResult = await WebAuthenticator.AuthenticateAsync(
        new Uri("https://mysite.com/mobileauth/Microsoft"),
        new Uri("myapp://"));

var accessToken = authResult?.AccessToken;
```

A `WebAuthenticator` API cuida de iniciar o url no navegador e esperar até que o retorno de chamada seja recebido:

![Fluxo típico de autenticação da Web](images/web-authenticator.png)

Se o usuário cancelar o fluxo `null` em qualquer ponto, um resultado será devolvido.

## <a name="platform-differences"></a>Diferenças de plataforma

# <a name="android"></a>[Android](#tab/android)

As guias personalizadas são usadas sempre que disponíveis, caso contrário, uma Intenção é iniciada para a URL.

# <a name="ios"></a>[iOS](#tab/ios)

No iOS 12 `ASWebAuthenticationSession` ou superior, é usado.  No iOS 11, `SFAuthenticationSession` é usado.  Em versões `SFSafariViewController` mais antigas do iOS, é usado se disponível, caso contrário, o Safari é usado.

# <a name="uwp"></a>[UWP](#tab/uwp)

No UWP, `WebAuthenticationBroker` o é usado se suportado, caso contrário o navegador do sistema é usado.

-----

## <a name="apple-sign-in"></a>Apple Sign In

De acordo com [as diretrizes de revisão da Apple,](https://developer.apple.com/app-store/review/guidelines/#sign-in-with-apple)se o seu aplicativo usa qualquer serviço de login social para autenticar, ele também deve oferecer o Apple Sign In como uma opção.

Para adicionar o Apple Login aos seus aplicativos, primeiro você precisará [configurar seu aplicativo para usar o Apple Login](https://docs.microsoft.com/xamarin/ios/platform/ios13/sign-in).

Para o iOS 13 e superior `AppleSignInAuthenticator.AuthenticateAsync()` você vai querer chamar o método. Isso usará o Apple Sign nativo em API's sob o capô para que seus usuários obtenham a melhor experiência possível nesses dispositivos. Você pode escrever seu código compartilhado para usar a API certa em tempo de execução como esta:

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

var accessToken = r?.AccessToken;
```

> [!TIP]
> Para dispositivos não iOS 13, isso iniciará o fluxo de autenticação da Web, que também pode ser usado para ativar o Apple Login em seus dispositivos Android e UWP.
> Você pode entrar na sua conta do iCloud no simulador do iOS para testar o Apple Login.

-----

## <a name="aspnet-core-server-back-end"></a>ASP.NET back-end do servidor central

É possível usar a `WebAuthenticator` API com qualquer serviço de back-end da Web.  Para usá-lo com um aplicativo ASP.NET principal, primeiro você precisa configurar o aplicativo web com as seguintes etapas:

1. Configure seus [provedores de autenticação social externos desejados](https://docs.microsoft.com/aspnet/core/security/authentication/social/?view=aspnetcore-3.1&tabs=visual-studio) em um aplicativo web ASP.NET Core.
2. Defina o Esquema `CookieAuthenticationDefaults.AuthenticationScheme` de `.AddAuthentication()` Autenticação Padrão em sua chamada.
3. Use `.AddCookies()` em `.AddAuthentication()` sua chamada Startup.cs.
4. Todos os provedores `.SaveTokens = true;`devem ser configurados com .

> [!TIP]
> Se você quiser incluir o Apple Login, `AspNet.Security.OAuth.Apple` você pode usar o pacote NuGet.  Você pode visualizar a amostra completa [Startup.cs](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/Startup.cs#L32-L60) no repositório Essentials GitHub.

### <a name="add-a-custom-mobile-auth-controller"></a>Adicione um controlador auth móvel personalizado

Com um fluxo de autenticação móvel, geralmente é desejável iniciar o fluxo diretamente para um provedor que o usuário escolheu (por exemplo, clicando em um botão "Microsoft" no sinal na tela do aplicativo).  Também é importante poder retornar informações relevantes ao seu aplicativo em um URI de retorno de chamada específico para encerrar o fluxo de autenticação.

Para isso, use um controlador de API personalizado:

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

O objetivo deste controlador é inferir o esquema (provedor) que o aplicativo está solicitando, e iniciar o fluxo de autenticação com o provedor social. Quando o provedor liga de volta para o backend da Web, o controlador analisa o resultado e redireciona para o URI de retorno de chamada do aplicativo com parâmetros.

Às vezes, você pode querer retornar `access_token` dados como o retorno do provedor ao aplicativo que você pode fazer através dos parâmetros de consulta do URI de retorno de chamada. Ou, em vez disso, você pode querer criar sua própria identidade em seu servidor e repassar seu próprio token para o aplicativo. O que e como você faz esta parte é com você!

Confira a [amostra completa](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/Controllers/MobileAuthController.cs) do controlador no repositório Essentials.

-----
## <a name="api"></a>API

- [Código-fonte do WebAuthenticator](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/WebAuthenticator)
- [Documentação da API do Autenticador web](xref:Xamarin.Essentials.WebAuthenticator)
- [ASP.NET amostra do servidor principal](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/)
