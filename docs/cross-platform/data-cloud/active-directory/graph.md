---
title: Acessar a API do Graph
description: Este documento descreve como adicionar a autenticação do Active Directory do Azure para um aplicativo móvel criado com o Xamarin.
ms.prod: xamarin
ms.assetid: F94A9FF4-068E-4B71-81FE-46920745380D
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: c43dfa79831f22e55490b27c3c360602ae717627
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61189897"
---
# <a name="accessing-the-graph-api"></a>Acessar a API do Graph

Siga estas etapas para usar a API do Graph de dentro de um aplicativo Xamarin:

1. [Registrando com o Azure Active Directory](~/cross-platform/data-cloud/active-directory/get-started/register.md) sobre o *windowsazure.com* portal, em seguida,
2. [Configurar serviços](~/cross-platform/data-cloud/active-directory/get-started/configure.md).

## <a name="step-3-adding-active-directory-authentication-to-an-app"></a>Etapa 3. Adicionando a autenticação do Active Directory a um aplicativo

Em seu aplicativo, adicione uma referência ao **Azure Active Directory Authentication Library (ADAL do Azure)** usando o Gerenciador de pacotes do NuGet no Visual Studio ou Visual Studio para Mac.
Verifique se você selecionou **Mostrar pacotes de pré-lançamento** para incluir esse pacote, pois ele ainda está em visualização.

> [!IMPORTANT]
> Observação: 3.0 ADAL do Azure está atualmente uma visualização e pode haver alterações significativas antes do lançamento da versão final. 


![](graph-images/06.-adal-nuget-package.jpg "Adicione uma referência ao Azure Active Directory Authentication Library (ADAL do Azure)")

Em seu aplicativo, agora você precisará adicionar as seguintes variáveis de nível de classe que são necessárias para o fluxo de autenticação.

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

Uma coisa a observar aqui é `commonAuthority`. Quando o ponto de extremidade de autenticação está `common`, seu aplicativo se torna **multilocatário**, que significa que qualquer usuário pode usar o logon com suas credenciais do Active Directory. Após a autenticação, que o usuário funcionará no contexto de seu próprio Active Directory – ou seja, eles verão os detalhes relacionados ao seu Active Directory.

### <a name="write-method-to-acquire-access-token"></a>Método para adquirir um Token de acesso de gravação

O código a seguir (para Android) inicie a autenticação e após a conclusão, atribua o resultado em `authResult`. As implementações do Windows Phone e iOS diferem ligeiramente: o segundo parâmetro (`Activity`) é diferente no iOS e estavam ausentes no Windows Phone.

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

No código acima, o `AuthenticationContext` é responsável para a autenticação com commonAuthority. Ele tem um `AcquireTokenAsync` método, que usam parâmetros como um recurso que precisa ser acessado, neste caso `graphResourceUri`, `clientId`, e `returnUri`. O aplicativo será retornado para o `returnUri` quando a autenticação é concluída. Esse código permanecerá o mesmo para todas as plataformas, no entanto, o último parâmetro, `AuthorizationParameters`, será diferente em diferentes plataformas e é responsável por que rege o fluxo de autenticação.

No caso do Android ou iOS, passamos `this` parâmetro para `AuthorizationParameters(this)` compartilhar o contexto, enquanto que no Windows são passadas sem qualquer parâmetro como o novo `AuthorizationParameters()`.

### <a name="handle-continuation-for-android"></a>Identificador de continuação para Android

Após a autenticação é concluída, o fluxo deve retornar para o aplicativo. No caso do Android que ela é tratada pelo código a seguir, que devem ser adicionado ao **MainActivity.cs**:


```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
  base.OnActivityResult(requestCode, resultCode, data);
  AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);

    
}
```

### <a name="handle-continuation-for-windows-phone"></a>Identificador de continuação para Windows Phone

Para Windows Phone modificar os `OnActivated` método no **App.xaml.cs** de arquivo com o código abaixo:

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

Agora, se você executar o aplicativo, você deve ver uma caixa de diálogo de autenticação.
Após a autenticação bem-sucedida, ele solicitará suas permissões para acessar os recursos (no nosso caso, o API do Graph):

![](graph-images/08.-authentication-flow.jpg "Após a autenticação bem-sucedida, ele solicitará suas permissões para acessar os recursos em nosso caso, a API do Graph")

Se a autenticação for bem-sucedida e você autorizou o aplicativo para acessar os recursos, você deve obter um `AccessToken` e `RefreshToken` combinação em `authResult`. Esses tokens são necessários para ainda mais as chamadas de API e para autorização com o Azure Active Directory em segundo plano.

![](graph-images/07.-access-token-for-authentication.jpg "Esses tokens são necessários para chamadas à API ainda mais e para autorização com o Azure Active Directory em segundo plano")

Por exemplo, o código a seguir permite que você obtenha uma lista de usuários do Active Directory. Você pode substituir a URL da API Web com sua API Web que é protegido pelo AD do Azure.

```csharp
var client = new HttpClient();
var request = new HttpRequestMessage(HttpMethod.Get,
    "https://graph.windows.net/tendulkar.onmicrosoft.com/users?api-version=2013-04-05");
request.Headers.Authorization =
  new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
var response = await client.SendAsync(request);
var content = await response.Content.ReadAsStringAsync();
```

