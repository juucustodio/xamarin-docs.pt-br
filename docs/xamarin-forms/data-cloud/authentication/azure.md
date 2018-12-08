---
title: Autenticar usuários com aplicativos móveis do Azure
description: Este artigo explica como usar aplicativos móveis do Azure para gerenciar o processo de autenticação em um aplicativo xamarin. Forms.
ms.prod: xamarin
ms.assetid: D50D6F56-8B19-44E7-81F3-E0E1C6E240
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/02/2017
ms.openlocfilehash: 428e536d6895ff16a928f8cc40a8a7976d087471
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53060485"
---
# <a name="authenticating-users-with-azure-mobile-apps"></a>Autenticar usuários com aplicativos móveis do Azure

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuth/)

_Aplicativos móveis do Azure usam uma variedade de provedores de identidade externos para dar suporte à autenticação e autorização de usuários do aplicativo, incluindo o Facebook, Google, Microsoft, Twitter e Active Directory do Azure. Permissões podem ser definidas em tabelas para restringir o acesso somente aos usuários autenticados. Este artigo explica como usar aplicativos móveis do Azure para gerenciar o processo de autenticação em um aplicativo xamarin. Forms._

## <a name="overview"></a>Visão geral

O processo de ter os aplicativos móveis do Azure a gerenciar o processo de autenticação em um aplicativo xamarin. Forms é da seguinte maneira:

1. Registrar seu aplicativo móvel do Azure no site do provedor de identidade e, em seguida, defina as credenciais geradas pelo provedor no back-end dos aplicativos móveis. Para obter mais informações, consulte [registrar seu aplicativo para autenticação e configurar serviços de aplicativos](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#register-your-app-for-authentication-and-configure-app-services).
1. Defina um novo esquema de URL para seu aplicativo xamarin. Forms, que permite que o sistema de autenticação redirecionar de volta para o aplicativo xamarin. Forms depois que o processo de autenticação for concluído. Para obter mais informações, consulte [adicionar seu aplicativo para a permissão URLs de redirecionamento externo](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#redirecturl).
1. Restringir o acesso para o back-end de aplicativos móveis do Azure para somente aos clientes autenticados. Para obter mais informações, consulte [restringir permissões a usuários autenticados](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#restrict-permissions-to-authenticated-users).
1. Invocar a autenticação do aplicativo xamarin. Forms. Para obter mais informações, consulte [adicionar autenticação à biblioteca de classes portátil](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-portable-class-library), [adicionar autenticação ao aplicativo do iOS](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-ios-app), [adicionar autenticação ao aplicativo do Android](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-the-android-app)e [ Adicionar autenticação a projetos de aplicativo do Windows 10](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users#add-authentication-to-windows-10-including-phone-app-projects).

> [!NOTE]
> No iOS 9 e superior, a segurança de transporte de aplicativo (ATS) impõe conexões seguras entre recursos da internet (como o servidor de back-end do aplicativo) e o aplicativo, impedindo assim a divulgação acidental de informações confidenciais. Desde que o ATS é habilitado por padrão em aplicativos criados para o iOS 9, todas as conexões serão sujeitos a requisitos de segurança ATS. Se as conexões não atender a esses requisitos, eles falharão com uma exceção.
> ATS poderá ser aceito de se ele não é possível usar o `HTTPS` de protocolo e proteger a comunicação para recursos da internet. Isso pode ser feito atualizando o aplicativo **Info. plist** arquivo. Para obter mais informações, consulte [segurança de transporte de aplicativo](~/ios/app-fundamentals/ats.md).

Historicamente, os aplicativos móveis usou modos de exibição da web incorporadas para realizar a autenticação com o provedor de identidade. Isso não é mais recomendável pelos seguintes motivos:

- O aplicativo que hospeda a exibição da web pode acessar a credencial do usuário autenticação completa, não apenas a concessão de autorização foi destinada para o aplicativo. Isso viola o princípio de privilégios mínimos, que o aplicativo tenha acesso a credenciais mais poderosas do que o necessário, possivelmente, aumentando a superfície de ataque do aplicativo.
- O aplicativo host pode capturar os nomes de usuário e senhas, automaticamente enviar formulários e ignorar o consentimento do usuário e copiar os cookies de sessão e usá-los para executar ações autenticadas como o usuário.
- Modos de exibição da web incorporadas não compartilham o estado de autenticação com outros aplicativos ou navegador da web do dispositivo, exigir que o usuário entrar para cada solicitação de autorização que é considerada uma experiência de usuário de qualidade inferior.
- Alguns pontos de extremidade de autorização tomar medidas para detectar e bloquear solicitações de autorização que vêm de exibições da web.

A alternativa é usar o navegador da web do dispositivo para realizar a autenticação, que é a abordagem usada pela versão mais recente do [SDK do Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/). Usando o navegador do dispositivo para as solicitações de autenticação melhora a usabilidade de um aplicativo, como os usuários precisam apenas entrar para o provedor de identidade uma vez por dispositivo, melhorando as taxas de conversão de fluxos de entrada e autorização no aplicativo. O navegador do dispositivo também fornece segurança aprimorada como os aplicativos capazes de inspecionar e modificar o conteúdo em uma exibição da web, mas não os conteúdo exibido no navegador.

## <a name="using-an-azure-mobile-apps-instance"></a>Usando uma instância de aplicativos móveis do Azure

O [SDK do Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) fornece o `MobileServiceClient` classe, que é usado por um aplicativo xamarin. Forms para acessar a instância de aplicativos móveis do Azure.

O aplicativo de exemplo usa o Google como provedor de identidade, que permite que os usuários com contas do Google para fazer logon no aplicativo xamarin. Forms. Enquanto o Google é usado como o provedor de identidade neste artigo, a abordagem é igualmente aplicável a outros provedores de identidade.

<a name="logging-in" />

### <a name="logging-in-users"></a>Fazer logon de usuários

A tela de logon no aplicativo de exemplo é mostrada nas capturas de tela seguir:

![](azure-images/login.png "Página de logon")

Enquanto o Google é usado como o provedor de identidade, uma variedade de outros provedores de identidade pode ser usada, incluindo a Microsoft, Facebook, Twitter e Active Directory do Azure.

O exemplo de código a seguir mostra como o processo de logon é invocado:

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

O `App.Authenticator` propriedade é um `IAuthenticate` instância que é definida por cada projeto específico da plataforma. O `IAuthenticate` interface especifica um `AuthenticateAsync` operação que deve ser fornecida por cada projeto da plataforma. Por isso, invocar o `App.Authenticator.AuthenticateAsync` método executa o `IAuthenticate.AuthenticateAsync` método em um projeto de plataforma.

Todas a plataforma `IAuthenticate.AuthenticateAsync` chamada de métodos de `MobileServiceClient.LoginAsync` método para exibir dados de interface e o cache de um logon. O seguinte exemplo de código mostra o `LoginAsync` método para a plataforma iOS:

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

Em todas as plataformas, o `MobileServiceAuthenticationProvider` enumeração é usada para especificar o provedor de identidade que será usado no processo de autenticação. Quando o `MobileServiceClient.LoginAsync` método é invocado, aplicativos móveis do Azure inicia um fluxo de autenticação, exibindo a página de logon do provedor selecionado e gerando um token de autenticação após um logon bem-sucedido com o provedor de identidade. O `MobileServiceClient.LoginAsync` método retorna um `MobileServiceUser` que será armazenada na instância a `MobileServiceClient.CurrentUser` propriedade. Esta propriedade fornece `UserId` e `MobileServiceAuthenticationToken` propriedades. Elas representam o usuário autenticado e um token de autenticação para o usuário. O token de autenticação será incluído em todas as solicitações feitas para a instância de aplicativos móveis do Azure, permitindo que o aplicativo xamarin. Forms executar ações na instância do aplicativo móvel do Azure que exigem permissões de usuário autenticado.

<a name="logging-out" />

### <a name="logging-out-users"></a>Fazer logoff de usuários

O exemplo de código a seguir mostra como o processo de logoff é invocado:

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

Todas a plataforma `IAuthenticate.LogoutAsync` chamada de métodos de `MobileServiceClient.LogoutAsync` método desprovisionar autenticar o usuário fez logon com o provedor de identidade. O seguinte exemplo de código mostra o `LogoutAsync` método para a plataforma iOS:

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

Quando o `IAuthenticate.LogoutAsync` método é invocado, os cookies definidos pelo provedor de identidade estiverem desmarcados, antes de `MobileServiceClient.LogoutAsync` método é invocado para desprovisionar autenticar o usuário fez logon com o provedor de identidade.

## <a name="summary"></a>Resumo

Este artigo explicou como usar aplicativos móveis do Azure para gerenciar o processo de autenticação em um aplicativo xamarin. Forms. Aplicativos móveis do Azure usam uma variedade de provedores de identidade externos para dar suporte à autenticação e autorização de usuários do aplicativo, incluindo o Facebook, Google, Microsoft, Twitter e Active Directory do Azure. O `MobileServiceClient` classe é usada pelo aplicativo xamarin. Forms para controlar o acesso à instância de aplicativos móveis do Azure.


## <a name="related-links"></a>Links relacionados

- [TodoAzureAuth (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuth/)
- [Consumir um aplicativo móvel do Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Adicionar autenticação ao aplicativo xamarin. Forms](/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-users/)
- [SDK de cliente móvel do Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [MobileServiceClient](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx)
