---
title: Acessar a API do Graph
description: Este documento descreve como adicionar Azure Active Directory autenticação a um aplicativo móvel criado com o Xamarin.
ms.prod: xamarin
ms.assetid: F94A9FF4-068E-4B71-81FE-46920745380D
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 7ccfc082f86d0a0c6f8d29a477101edb72f9c92f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016625"
---
# <a name="accessing-the-graph-api"></a>Acessar a API do Graph

Siga estas etapas para usar o API do Graph de dentro de um aplicativo Xamarin:

1. [Registrando com Azure Active Directory](~/cross-platform/data-cloud/active-directory/get-started/register.md) no portal do *WindowsAzure.com* , em seguida,
2. [Configurar serviços](~/cross-platform/data-cloud/active-directory/get-started/configure.md).

## <a name="step-3-adding-active-directory-authentication-to-an-app"></a>Etapa 3. Adicionando Active Directory autenticação a um aplicativo

Em seu aplicativo, adicione uma referência a **Azure Active Directory biblioteca de autenticação (Adal do Azure)** usando o Gerenciador de pacotes NuGet no Visual Studio ou Visual Studio para Mac.
Certifique-se de selecionar **Mostrar pacotes de pré-lançamento** para incluir este pacote, pois ele ainda está na versão prévia.

> [!IMPORTANT]
> Observação: o Azure ADAL 3,0 é atualmente uma versão prévia e pode haver alterações significativas antes que a versão final seja liberada. 

![](graph-images/06.-adal-nuget-package.jpg "Add a reference to Azure Active Directory Authentication Library (Azure ADAL)")

Em seu aplicativo, agora será necessário adicionar as seguintes variáveis de nível de classe que são necessárias para o fluxo de autenticação.

```csharp
//Client ID
public static string clientId = "25927d3c-.....-63f2304b90de";
public static string commonAuthority = "https://login.windows.net/common"
//Redirect URI
public static Uri returnUri = new Uri("http://xam-demo-redirect");
//Graph URI if you've given permission to Azure Active Directory
const string graphResourceUri = "https://graph.windows.net";
public static string graphApiVersion = "2013-11-08";
//AuthenticationResult will hold the result after authentication completes
AuthenticationResult authResult = null;
```

Uma coisa a ser observada aqui é `commonAuthority`. Quando o ponto de extremidade de autenticação é `common`, seu aplicativo se torna **multilocatário**, o que significa que qualquer usuário pode usar o logon com suas credenciais de Active Directory. Após a autenticação, esse usuário funcionará no contexto de seus próprios Active Directory – ou seja, eles verão detalhes relacionados ao seu Active Directory.

### <a name="write-method-to-acquire-access-token"></a>Método Write para adquirir token de acesso

O código a seguir (para Android) iniciará a autenticação e, após a conclusão, atribuirá o resultado em `authResult`. As implementações de iOS e Windows Phone diferem ligeiramente: o segundo parâmetro (`Activity`) é diferente no iOS e está ausente no Windows Phone.

```csharp
public static async Task<AuthenticationResult> GetAccessToken
            (string serviceResourceId, Activity activity)
{
    authContext = new AuthenticationContext(Authority);
    if (authContext.TokenCache.ReadItems().Count() > 0)
        authContext = new AuthenticationContext(authContext.TokenCache.ReadItems().First().Authority);
    var authResult = await authContext.AcquireTokenAsync(serviceResourceId, clientId, returnUri, new AuthorizationParameters(activity));
    return authResult;
}  
```

No código acima, o `AuthenticationContext` é responsável pela autenticação com commonAuthority. Ele tem um método `AcquireTokenAsync`, que assume parâmetros como um recurso que precisa ser acessado, nesse caso `graphResourceUri`, `clientId`e `returnUri`. O aplicativo retornará ao `returnUri` quando a autenticação for concluída. Esse código permanecerá o mesmo para todas as plataformas, no entanto, o último parâmetro, `AuthorizationParameters`, será diferente em plataformas diferentes e será responsável por governar o fluxo de autenticação.

In the case of Android or iOS, we pass `this` parameter to `AuthorizationParameters(this)` to share the context, whereas in Windows it is passed without any parameter as new `AuthorizationParameters()`.

### <a name="handle-continuation-for-android"></a>Handle continuation for Android

After authentication is complete, the flow should return to the app. In the case of Android it is handled by following code, which should be added to **MainActivity.cs**:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
  base.OnActivityResult(requestCode, resultCode, data);
  AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
```

### <a name="handle-continuation-for-windows-phone"></a>Handle continuation for Windows Phone

For Windows Phone modify the `OnActivated` method in the **App.xaml.cs** file with the below code:

```csharp
protected override void OnActivated(IActivatedEventArgs args)
{
#if WINDOWS_PHONE_APP
  if (args is IWebAuthenticationBrokerContinuationEventArgs)
  {
     WebAuthenticationBrokerContinuationHelper.SetWebAuthenticationBrokerContinuationEventArgs(args as IWebAuthenticationBrokerContinuationEventArgs);
  }
#endif
  base.OnActivated(args);
}
```

Now if you run the application, you should see an authentication dialog.
Upon successful authentication, it will ask your permissions to access the resources (in our case Graph API):

![](graph-images/08.-authentication-flow.jpg "Upon successful authentication, it will ask your permissions to access the resources in our case Graph API")

If authentication is successful and you’ve authorized the app to access the resources, you should get an `AccessToken` and `RefreshToken` combo in `authResult`. These tokens are required for further API calls and for authorization with Azure Active Directory behind the scenes.

![](graph-images/07.-access-token-for-authentication.jpg "These tokens are   required for further API calls and for authorization with Azure Active Directory behind the scenes")

For example, the code below allows you to get a user list from Active Directory. You can replace the Web API URL with your Web API which is protected by Azure AD.

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get,
    "https://graph.windows.net/tendulkar.onmicrosoft.com/users?api-version=2013-04-05");
request.Headers.Authorization =
  new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
var response = await client.SendAsync(request);
var content = await response.Content.ReadAsStringAsync();
```
