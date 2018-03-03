---
title: "Autenticação de usuários com aplicativos móveis do Azure"
description: "Aplicativos móveis do Azure usam uma variedade de provedores de identidade externa para dar suporte a autenticação e autorização de usuários do aplicativo, incluindo o Facebook, Google, Microsoft, Twitter e Active Directory do Azure. Permissões podem ser definidas em tabelas para restringir o acesso a somente os usuários autenticados. Este artigo explica como usar os aplicativos móveis do Azure para gerenciar o processo de autenticação em um aplicativo xamarin. Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: D50D6F56-8B19-44E7-81F3-E0E1C6E240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/02/2017
ms.openlocfilehash: 823dcdfdaca79045a407b62ec7e75079ee25d72f
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="authenticating-users-with-azure-mobile-apps"></a>Autenticação de usuários com aplicativos móveis do Azure

_Aplicativos móveis do Azure usam uma variedade de provedores de identidade externa para dar suporte a autenticação e autorização de usuários do aplicativo, incluindo o Facebook, Google, Microsoft, Twitter e Active Directory do Azure. Permissões podem ser definidas em tabelas para restringir o acesso a somente os usuários autenticados. Este artigo explica como usar os aplicativos móveis do Azure para gerenciar o processo de autenticação em um aplicativo xamarin. Forms._

## <a name="overview"></a>Visão geral

O processo de ter que gerenciar o processo de autenticação em um aplicativo xamarin. Forms aplicativos móveis do Azure é o seguinte:

1. Registrar seu aplicativo de celular do Azure no site do provedor de identidade e, em seguida, defina as credenciais gerado pelo provedor no back-end aplicativos móveis. Para obter mais informações, consulte [registrar seu aplicativo para autenticação e configurar serviços de aplicativos](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#register-your-app-for-authentication-and-configure-app-services).
1. Defina um novo esquema de URL para o aplicativo xamarin. Forms, que permite que o sistema de autenticação redirecionar para o aplicativo xamarin. Forms quando o processo de autenticação for concluído. Para obter mais informações, consulte [adicionar seu aplicativo para a permissão redirecionar URLs externas](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#redirecturl).
1. Restringir o acesso ao back-end os aplicativos móveis do Azure para somente aos clientes autenticados. Para obter mais informações, consulte [restringir permissões para usuários autenticados](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#restrict-permissions-to-authenticated-users).
1. Invocação de autenticação do aplicativo xamarin. Forms. Para obter mais informações, consulte [adicionar autenticação para a biblioteca de classes portátil](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-portable-class-library), [adicionar autenticação ao aplicativo do iOS](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-ios-app), [adicionar autenticação ao aplicativo do Android](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-android-app)e [ Adicionar autenticação a projetos de aplicativo do Windows 10](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-windows-10-including-phone-app-projects).

> [!NOTE]
> No iOS 9 e superior, a segurança de transporte do aplicativo (ATS) impõe conexões seguras entre os recursos de internet (como o servidor de back-end do aplicativo) e o aplicativo, impedindo assim a divulgação acidental de informações confidenciais. Como ATS está habilitado por padrão em aplicativos criados para o iOS 9, todas as conexões serão sujeitos a requisitos de segurança ATS. Se as conexões não atender a esses requisitos, eles falham com uma exceção.
> ATS pode ser incluído em se não for possível usar o `HTTPS` de protocolo e proteger a comunicação para recursos da internet. Isso pode ser obtido pela atualização do aplicativo **Info. plist** arquivo. Para obter mais informações, consulte [segurança de transporte do aplicativo](~/ios/app-fundamentals/ats.md).

Historicamente, aplicativos móveis usaram modos de exibição da web interno para realizar a autenticação com o provedor de identidade. Isso não é recomendado pelos seguintes motivos:

- O aplicativo que hospeda a exibição da web pode acessar as credenciais de autenticação completa do usuário, não apenas a concessão de autorização que foi criada para o aplicativo. Isso viola o princípio de menos privilégios, como o aplicativo tem acesso a credenciais mais eficientes que o necessário, aumentar potencialmente a superfície de ataque do aplicativo.
- O aplicativo host pode capturar os nomes de usuário e senhas, automaticamente enviar formulários e ignorar o consentimento do usuário e copiar os cookies de sessão e usá-los para executar ações autenticadas como o usuário.
- Modos de exibição da web interno não compartilhem o estado de autenticação com outros aplicativos ou navegador da web do dispositivo, exigir que o usuário entrar para cada solicitação de autorização que é considerada uma experiência de usuário de qualidade inferior.
- Alguns pontos de extremidade de autorização adotar medidas para detectar e bloquear solicitações de autorização que vêm de exibições da web.

A alternativa é usar o navegador da web do dispositivo para executar autenticação, que é a abordagem usada pela versão mais recente do [cliente SDK do Azure Mobile](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/). Usando o navegador de dispositivo para solicitações de autenticação melhora a usabilidade de um aplicativo, como os usuários só precisam entrar no provedor de identidade uma vez por dispositivo, melhorando as taxas de conversão de fluxos de entrada e autorização no aplicativo. O navegador de dispositivo também fornece maior segurança como aplicativos são capazes de inspecionar e modificar o conteúdo em uma exibição da web, mas não o conteúdo exibido no navegador.

## <a name="using-an-azure-mobile-apps-instance"></a>Usando uma instância de aplicativos móveis do Azure

O [cliente SDK do Azure Mobile](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) fornece o `MobileServiceClient` classe, que é usado por um aplicativo xamarin. Forms para acessar a instância os aplicativos móveis do Azure.

O aplicativo de exemplo usa o Google como provedor de identidade, que permite que os usuários com contas do Google para fazer logon no aplicativo xamarin. Forms. Enquanto o Google é usado como o provedor de identidade neste artigo, a abordagem é igualmente aplicável para outros provedores de identidade.

<a name="logging-in" />

### <a name="logging-in-users"></a>Registro em log em usuários

A tela de logon no aplicativo de exemplo é mostrada nas capturas de tela seguir:

![](azure-images/login.png "Página de logon")

Enquanto o Google é usado como o provedor de identidade, uma variedade de outros provedores de identidade pode ser usada, incluindo Microsoft, Facebook, Twitter e Active Directory do Azure.

O exemplo de código a seguir mostra como o processo de logon é chamado:

```csharp
async void OnLoginButtonClicked(object sender, EventArgs e)
{
  ...
  if (App.Authenticator != null)
  {
    authenticated = await App.Authenticator.AuthenticateAsync();
  }
  ...
}
```

O `App.Authenticator` propriedade é um `IAuthenticate` instância que é definida por cada projeto específico de plataforma. O `IAuthenticate` interface especifica um `AuthenticateAsync` operação que deve ser fornecida por cada projeto da plataforma. Por isso, invocar o `App.Authenticator.AuthenticateAsync` método executa o `IAuthenticate.AuthenticateAsync` método em um projeto de plataforma.

Todas as plataformas `IAuthenticate.AuthenticateAsync` chamada de métodos de `MobileServiceClient.LoginAsync` método para exibir dados de interface e o cache de um logon. O seguinte exemplo de código mostra o `LoginAsync` método para a plataforma iOS:

```csharp
public async Task<bool> AuthenticateAsync()
{
  ...
  // The authentication provider could also be Facebook, Twitter, or Microsoft
  user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
    UIApplication.SharedApplication.KeyWindow.RootViewController,
    MobileServiceAuthenticationProvider.Google,
    Constants.URLScheme);
  ...
}
```

O seguinte exemplo de código mostra o `LoginAsync` método para a plataforma Android:

```csharp
public async Task<bool> AuthenticateAsync()
{
  ...
  // The authentication provider could also be Facebook, Twitter, or Microsoft
  user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
    this,
    MobileServiceAuthenticationProvider.Google,
    Constants.URLScheme);
  ...
}
```

O seguinte exemplo de código mostra o `LoginAsync` método para a plataforma Universal do Windows:

```csharp
public async Task<bool> AuthenticateAsync()
{
  ...
  // The authentication provider could also be Facebook, Twitter, or Microsoft
  user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
    MobileServiceAuthenticationProvider.Google,
    Constants.URLScheme);
  ...
}
```

Em todas as plataformas, o `MobileServiceAuthenticationProvider` enumeração é usada para especificar o provedor de identidade que será usado no processo de autenticação. Quando o `MobileServiceClient.LoginAsync` método é invocado, aplicativos móveis do Azure inicia um fluxo de autenticação, exibindo a página de logon do provedor selecionado e gerar um token de autenticação após o logon bem-sucedido com o provedor de identidade. O `MobileServiceClient.LoginAsync` método retorna um `MobileServiceUser` instância será armazenada no `MobileServiceClient.CurrentUser` propriedade. Esta propriedade fornece `UserId` e `MobileServiceAuthenticationToken` propriedades. Eles representam o usuário autenticado e um token de autenticação do usuário. O token de autenticação será incluído em todas as solicitações feitas para a instância os aplicativos móveis do Azure, permitindo que o aplicativo xamarin. Forms executar ações na instância do aplicativo do Azure Mobile que exigem permissões de usuário autenticado.

<a name="logging-out" />

### <a name="logging-out-users"></a>Logoff de usuários

O exemplo de código a seguir mostra como o processo de logoff é chamado:

```csharp
async void OnLogoutButtonClicked(object sender, EventArgs e)
{
  bool loggedOut = false;

  if (App.Authenticator != null)
  {
    loggedOut = await App.Authenticator.LogoutAsync ();
  }
  ...
}
```

O `App.Authenticator` propriedade é um `IAuthenticate` instância que é definida por cada platformproject. O `IAuthenticate` interface especifica um `LogoutAsync` operação que deve ser fornecida por cada projeto da plataforma. Por isso, invocar o `App.Authenticator.LogoutAsync` método executa o `IAuthenticate.LogoutAsync` método em um projeto de plataforma.

Todas as plataformas `IAuthenticate.LogoutAsync` chamada de métodos de `MobileServiceClient.LogoutAsync` método eliminação autenticar o usuário fez logon com o provedor de identidade. O seguinte exemplo de código mostra o `LogoutAsync` método para a plataforma iOS:

```csharp
public async Task<bool> LogoutAsync()
{
  ...
  foreach (var cookie in NSHttpCookieStorage.SharedStorage.Cookies)
  {
    NSHttpCookieStorage.SharedStorage.DeleteCookie (cookie);
  }
  await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
  ...
}
```

O seguinte exemplo de código mostra o `LogoutAsync` método para a plataforma Android:

```csharp
public async Task<bool> LogoutAsync()
{
  ...
  CookieManager.Instance.RemoveAllCookie();
  await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
  ...
}
```

O seguinte exemplo de código mostra o `LogoutAsync` método para a plataforma Universal do Windows:

```csharp
public async Task<bool> LogoutAsync()
{
  ...
  await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
  ...
}
```

Quando o `IAuthenticate.LogoutAsync` método é invocado, os cookies definidos pelo provedor de identidade são limpas, antes de `MobileServiceClient.LogoutAsync` método é invocado para desalocar autenticar o usuário fez logon com o provedor de identidade.

## <a name="summary"></a>Resumo

Este artigo explicou como usar aplicativos móveis do Azure para gerenciar o processo de autenticação em um aplicativo xamarin. Forms. Aplicativos móveis do Azure usam uma variedade de provedores de identidade externa para dar suporte a autenticação e autorização de usuários do aplicativo, incluindo o Facebook, Google, Microsoft, Twitter e Active Directory do Azure. O `MobileServiceClient` classe é usada pelo aplicativo xamarin. Forms para controlar o acesso à instância os aplicativos móveis do Azure.


## <a name="related-links"></a>Links relacionados

- [TodoAzureAuth (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuth/)
- [Consumo de um aplicativo móvel do Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Adicionar autenticação ao seu aplicativo xamarin. Forms](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users/)
- [SDK de cliente móvel do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
