---
title: Acessar a API do Graph
description: Este documento descreve como adicionar Azure Active Directory autenticação a um aplicativo móvel criado com o Xamarin.
ms.prod: xamarin
ms.assetid: F94A9FF4-068E-4B71-81FE-46920745380D
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 3b8852b088470a3db74a35d852ce62d2852dd737
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86931008"
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

![Adicionar uma referência à biblioteca de autenticação Azure Active Directory (Azure ADAL)](graph-images/06.-adal-nuget-package.jpg)

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

Uma coisa a ser observada aqui é `commonAuthority` . Quando o ponto de extremidade de autenticação é `common` , seu aplicativo se torna **multilocatário**, o que significa que qualquer usuário pode usar o logon com suas credenciais de Active Directory. Após a autenticação, esse usuário funcionará no contexto de seus próprios Active Directory – ou seja, eles verão detalhes relacionados ao seu Active Directory.

### <a name="write-method-to-acquire-access-token"></a>Método Write para adquirir token de acesso

O código a seguir (para Android) iniciará a autenticação e, após a conclusão, atribuirá o resultado em `authResult` . As implementações de iOS e Windows Phone diferem ligeiramente: o segundo parâmetro ( `Activity` ) é diferente no Ios e está ausente em Windows Phone.

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

No código acima, o `AuthenticationContext` é responsável pela autenticação com commonAuthority. Ele tem um `AcquireTokenAsync` método, que assume parâmetros como um recurso que precisa ser acessado, nesse caso `graphResourceUri` , `clientId` e `returnUri` . O aplicativo retornará ao quando a `returnUri` autenticação for concluída. Esse código permanecerá o mesmo para todas as plataformas, no entanto, o último parâmetro, `AuthorizationParameters` , será diferente em plataformas diferentes e será responsável por governar o fluxo de autenticação.

No caso do Android ou iOS, passamos `this` o parâmetro para `AuthorizationParameters(this)` para compartilhar o contexto, enquanto no Windows ele é passado sem nenhum parâmetro como novo `AuthorizationParameters()` .

### <a name="handle-continuation-for-android"></a>Continuação de identificador para Android

Após a conclusão da autenticação, o fluxo deve retornar ao aplicativo. No caso do Android, ele é tratado pelo código a seguir, que deve ser adicionado ao **MainActivity.cs**:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
  base.OnActivityResult(requestCode, resultCode, data);
  AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
```

### <a name="handle-continuation-for-windows-phone"></a>Continuação de identificador para Windows Phone

Para Windows Phone modifique o `OnActivated` método no arquivo **app.XAML.cs** com o código abaixo:

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

Agora, se você executar o aplicativo, verá uma caixa de diálogo de autenticação.
Após a autenticação bem-sucedida, ele solicitará suas permissões para acessar os recursos (em nosso caso API do Graph):

![Após a autenticação bem-sucedida, ele solicitará suas permissões para acessar os recursos em nosso caso API do Graph](graph-images/08.-authentication-flow.jpg)

Se a autenticação for bem-sucedida e você tiver autorizado o aplicativo para acessar os recursos, você deverá obter `AccessToken` uma `RefreshToken` combinação de e no `authResult` . Esses tokens são necessários para chamadas de API adicionais e para autorização com Azure Active Directory nos bastidores.

![Esses tokens são necessários para chamadas de API adicionais e para autorização com Azure Active Directory nos bastidores](graph-images/07.-access-token-for-authentication.jpg)

Por exemplo, o código a seguir permite que você obtenha uma lista de usuários do Active Directory. Você pode substituir a URL da API da Web pela API da Web que é protegida pelo Azure AD.

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get,
    "https://graph.windows.net/tendulkar.onmicrosoft.com/users?api-version=2013-04-05");
request.Headers.Authorization =
  new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
var response = await client.SendAsync(request);
var content = await response.Content.ReadAsStringAsync();
```
