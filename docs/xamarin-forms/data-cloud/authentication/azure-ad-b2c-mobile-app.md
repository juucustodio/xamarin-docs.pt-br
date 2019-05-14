---
title: A integração do Azure Active Directory B2C com aplicativos móveis do Azure
description: Azure Active Directory B2C do diretório é uma solução de gerenciamento de identidade de nuvem para aplicativos móveis e web voltados ao consumidor. Este artigo demonstra como usar o Azure Active Directory B2C para fornecer autenticação e autorização para uma instância de aplicativos móveis do Azure com o xamarin. Forms.
ms.prod: xamarin
ms.assetid: 53F52036-A997-4D0F-86B4-4302C6913136
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/22/2019
ms.openlocfilehash: 4c3cc489f7c65636ad9f2b5541e552665b10980e
ms.sourcegitcommit: 0cb62b02a7efb5426f2356d7dbdfd9afd85f2f4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/13/2019
ms.locfileid: "65557348"
---
# <a name="integrating-azure-active-directory-b2c-with-azure-mobile-apps"></a>A integração do Azure Active Directory B2C com aplicativos móveis do Azure

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)

_B2C de diretório Active Directory do Azure fornece gerenciamento de identidades de nuvem para aplicativos voltados ao consumidor. Este artigo explica como usar o Azure Active Directory B2C para integrar o gerenciamento de identidade em uma instância de aplicativos móveis do Azure com o xamarin. Forms._

## <a name="overview"></a>Visão geral

Aplicativos móveis do Azure permitem que você desenvolva aplicativos com um back-end dimensionável, hospedado no serviço de aplicativo do Azure. Ele dá suporte à autenticação móvel, sincronização offline e por push notificações. Para obter mais informações sobre aplicativos móveis do Azure, consulte [consumir o aplicativo móvel do Azure](~/xamarin-forms/data-cloud/consuming/azure.md), e [autenticando usuários com aplicativos móveis do Azure](~/xamarin-forms/data-cloud/authentication/azure.md).

Azure Active Directory B2C do diretório é um serviço de gerenciamento de identidade para aplicativos voltados ao consumidor, que permite que os consumidores:

- Entrar com suas contas sociais existentes (Microsoft, Google, Facebook, Amazon, LinkedIn).
- Crie novas credenciais (endereço de email e senha, ou nome de usuário e senha). Essas credenciais são denominadas *local* contas.

Para obter mais informações sobre o Azure Active Directory B2C, consulte [autenticando usuários com o Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Azure Active Directory B2C do diretório pode ser usado para gerenciar o fluxo de trabalho de autenticação para o aplicativo móvel do Azure. Essa abordagem define o gerenciamento de identidade na nuvem e permite que as alterações sem modificar o código do aplicativo.


Aplicativos móveis do Azure com o Azure Active Directory B2C permite que os dois métodos de autenticação:

- [Cliente gerenciado](#client-managed-authentication) – o aplicativo móvel do xamarin. Forms inicia o processo de autenticação com o locatário do Azure Active Directory B2C e passa o token de autenticação recebidas para a instância de aplicativos móveis do Azure.
- [Servidor gerenciado](#server-managed-authentication) – a instância de aplicativos móveis do Azure usa o locatário do Azure Active Directory B2C para iniciar o processo de autenticação por meio de um fluxo de trabalho baseado na web.

Em ambos os casos, a experiência de autenticação é fornecida pelo locatário do Azure Active Directory B2C. No aplicativo de exemplo, a tela de entrada deve ser semelhante as capturas de tela a seguintes:

![](azure-ad-b2c-mobile-app-images/screenshots.png "Página de logon")

Este exemplo mostra a entrar usando uma conta local, mas você também pode habilitar o Microsoft, Google, Facebook e outros provedores de identidade.

## <a name="setup"></a>Configuração

Em ambos os fluxos de trabalho, o processo inicial para a integração com aplicativos móveis do Azure de um locatário do Azure Active Directory B2C é o seguinte:

1. Crie uma instância de aplicativos móveis do Azure no portal do Azure. Isso gera um projeto inicial semelhante para o projeto de exemplo. Para obter mais informações, consulte [consumir o aplicativo móvel do Azure](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Crie um locatário do Azure Active Directory B2C. Para obter mais informações, consulte [autenticando usuários com o Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).
1. Habilite a autenticação na instância de aplicativos móveis do Azure e o aplicativo xamarin. Forms. Para obter mais informações, consulte [autenticando usuários com aplicativos móveis do Azure](~/xamarin-forms/data-cloud/authentication/azure.md).

> [!NOTE]
> A biblioteca de autenticação Microsoft (MSAL) é necessária ao usar um fluxo de trabalho de autenticação gerenciada pelo cliente. MSAL usa um navegador da web do dispositivo para autenticar. Após a autenticação no navegador, o usuário é redirecionado para um esquema de URL personalizado. O aplicativo registra o esquema de URL personalizado, permitindo que ele recuperar o controle da experiência do usuário e responder a autenticação. Para obter mais informações sobre como usar MSAL para se comunicar com um locatário do Azure Active Directory B2C, consulte [autenticando usuários com o Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

## <a name="client-managed-authentication"></a>Autenticação gerenciada pelo cliente

Na autenticação gerenciada pelo cliente, um aplicativo xamarin. Forms entra em contato com um locatário do Azure Active Directory B2C para iniciar o fluxo de autenticação. Após o logon, o locatário do Azure Active Directory B2C retorna um token de identidade, que é fornecido para a instância de aplicativos móveis do Azure. Isso permite que o aplicativo xamarin. Forms executar ações que exigem permissões de usuário autenticado.

### <a name="azure-active-directory-b2c-client-managed-tenant-configuration"></a>Configuração de locatário gerenciada pelo cliente do B2C do Active Directory do Azure

Um fluxo de trabalho de autenticação gerenciada pelo cliente, o locatário B2C do Azure Active Directory deve ser configurado da seguinte maneira:

- Definir **incluir cliente nativo** como "Sim".
- Defina o URI de redirecionamento personalizado. A documentação de MSAL recomenda usando "msal" combinado com sua ID do aplicativo e seguido por ":/ / auth /". Para obter mais informações, consulte [URI de redirecionamento de aplicativos de cliente MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#redirect-uri).

Captura de tela a seguir demonstra essa configuração:

[!["Configuração do azure Active Directory B2C do diretório"](azure-ad-b2c-mobile-app-images/azure-redirect-uri-cropped.png)](azure-ad-b2c-mobile-app-images/azure-redirect-uri.png#lightbox "configuração do Azure Active Directory B2C")

A política de entrada usada no locatário do Azure Active Directory B2C também deve ser configurada para que a URL de resposta é definida como o mesmo esquema de URL personalizado. Isso pode ser feito selecionando **executar o fluxo de usuário** no portal do Azure para acessar as configurações de política. Salve a URL de metadados encontrada nessa tela, pois você precisará dela para a configuração de aplicativo móvel. Captura de tela a seguir demonstra essa configuração e a URL que você deve copiar:

[!["Políticas do azure Active Directory B2C"](azure-ad-b2c-mobile-app-images/client-flow-policies-cropped.png)](azure-ad-b2c-mobile-app-images/client-flow-policies.png#lightbox "configuração de política do Azure Active Directory B2C do diretório")

### <a name="azure-mobile-app-configuration"></a>Configuração de aplicativo móvel do Azure

Um fluxo de trabalho de autenticação gerenciada pelo cliente, configure a instância de aplicativos móveis do Azure da seguinte maneira:

- Autenticação do serviço de aplicativo deve ser ativada.
- A ação a ser tomada quando uma solicitação não está autenticada deve ser definida como **entrar com o Azure Active Directory**.

Captura de tela a seguir demonstra essa configuração:

[!["Configuração de autenticação de aplicativos móveis do azure"](azure-ad-b2c-mobile-app-images/ama-config-cropped.png)](azure-ad-b2c-mobile-app-images/ama-config.png#lightbox "configuração de autenticação de aplicativos móveis do Azure")

Configure a instância de aplicativos móveis do Azure para se comunicar com o locatário do Azure Active Directory B2C:

- Clique em configuração do Active Directory do Azure e habilite **avançado** modo para o provedor de autenticação do Active Directory do Azure.
- Definir **ID do cliente** para o **ID do aplicativo** do locatário do Azure Active Directory B2C.
- Defina as **Url do emissor** a URL de metadados da política que você copiou anteriormente durante a configuração de locatário.

Captura de tela a seguir demonstra essa configuração:

!["Configuração avançada de aplicativos móveis do azure"](azure-ad-b2c-mobile-app-images/ama-advanced-config.png)

### <a name="signing-in"></a>Entrando

O exemplo de código a seguir mostra as chamadas de método principal para iniciar um fluxo de trabalho de autenticação gerenciada pelo cliente:

```csharp
public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...

    authenticationResult = await ADB2CClient.AcquireTokenAsync(
        Constants.Scopes,
        string.Empty,
        UIBehavior.SelectAccount,
        string.Empty,
        App.UiParent);

    ...

    var payload = new JObject();
    if (authenticationResult != null && !string.IsNullOrWhiteSpace(authenticationResult.IdToken))
    {
        payload["access_token"] = authenticationResult.IdToken;
    }

    User = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        payload);
    success = true;

    ...
}
```

A biblioteca de autenticação Microsoft (MSAL) é usado para iniciar um fluxo de trabalho de autenticação com o locatário do Azure Active Directory B2C. O `AcquireTokenAsync` método inicia o navegador da web do dispositivo e exibe as opções de autenticação definidas na política do Azure Active Directory B2C é especificada pela política referenciada por meio de `Constants.AuthoritySignin` constante. Essa política define a experiência de entrada e inscrição do usuário e as declarações que o aplicativo recebe após a autenticação bem-sucedida.

O resultado do `AcquireTokenAsync` chamada de método é um `AuthenticationResult` instância. Se a autenticação for bem-sucedida, o `AuthenticationResult` instância conterá um token de identidade, que será armazenado localmente. Se a autenticação for bem-sucedida, o `AuthenticationResult` instância conterá os dados que indicam por que a autenticação falhou. Para obter informações sobre como usar MSAL para se comunicar com um locatário do Azure Active Directory B2C, consulte [autenticando usuários com o Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Quando o `CurrentClient.LoginAsync` método é invocado, a instância de aplicativos móveis do Azure recebe o token de identidade encapsulado em um `JObject`. A presença de um meio de token válido que a instância de aplicativos móveis do Azure não precisa iniciar o seu próprio fluxo de autenticação OAuth 2.0. Em vez disso, o `CurrentClient.LoginAsync` método retorna um `MobileServiceUser` que será armazenada na instância a `MobileServiceClient.CurrentUser` propriedade. Esta propriedade fornece `UserId` e `MobileServiceAuthenticationToken` propriedades. Elas representam o usuário autenticado e o token, que pode ser usado até que ela expire. O token de autenticação será incluído em todas as solicitações feitas para a instância de aplicativos móveis do Azure, permitindo que o aplicativo xamarin. Forms executar ações que exigem permissões de usuário autenticado.

### <a name="signing-out"></a>Sair

O exemplo de código a seguir mostra como o processo de saída gerenciada pelo cliente é invocado:

```csharp
public async Task<bool> LogoutAsync()
{
    ...

    IEnumerable<IAccount> accounts = await ADB2CClient.GetAccountsAsync();
    while (accounts.Any())
    {
        await ADB2CClient.RemoveAsync(accounts.First());
        accounts = await ADB2CClient.GetAccountsAsync();
    }
    User = null;

    ...
}
```

O `CurrentClient.LogoutAsync` método desprovisionar autentica o usuário com a instância de aplicativos móveis do Azure e, em seguida, todos os tokens de autenticação serão apagados do cache local criado pela MSAL.

## <a name="server-managed-authentication"></a>Autenticação gerenciada pelo servidor

Na autenticação de servidor gerenciado, um aplicativo xamarin. Forms entra em contato com uma instância de aplicativos móveis do Azure, que usa o locatário do Azure Active Directory B2C para gerenciar o fluxo de autenticação OAuth 2.0 exibindo uma página de entrada, conforme definido na política do B2C. Após o logon bem-sucedido, a instância de aplicativos móveis do Azure retorna um token que permite que o aplicativo xamarin. Forms executar ações que exigem permissões de usuário autenticado.

### <a name="azure-active-directory-b2c-server-managed-tenant-configuration"></a>Configuração de locatário gerenciada pelo servidor de B2C do Active Directory do Azure

Um fluxo de trabalho de autenticação gerenciada pelo servidor, o locatário B2C do Azure Active Directory deve ser configurado da seguinte maneira:

- Incluir uma aplicativo de web/API web e permitir que o fluxo implícito.
- Definir a URL de resposta para o endereço do aplicativo móvel do Azure, seguido por `/.auth/login/aad/callback`.

Captura de tela a seguir demonstra essa configuração:

[!["Configuração do azure Active Directory B2C do diretório"](azure-ad-b2c-mobile-app-images/server-flow-config-cropped.png)](azure-ad-b2c-mobile-app-images/server-flow-config.png#lightbox "configuração do Azure Active Directory B2C")

A política usada no Azure Active Directory B2C locatário precisa ter a URL de resposta configurada. Isso é feito definindo a URL de resposta para o endereço do seu aplicativo móvel do Azure, seguido por `/.auth/login/aad/callback`. Você também deve salvar a URL de metadados na parte superior da tela, pois você precisará dela para a configuração de aplicativo móvel. Captura de tela a seguir demonstra essa configuração e a URL de metadados, você deve salvar:

!["Configuração de política do azure Active Directory B2C do diretório"](azure-ad-b2c-mobile-app-images/server-flow-policies.png)

### <a name="azure-mobile-apps-instance-configuration"></a>Configuração de instância de aplicativos móveis do Azure

Um fluxo de trabalho de autenticação gerenciada pelo servidor, a instância de aplicativos móveis do Azure deve ser configurada da seguinte maneira:

- Autenticação do serviço de aplicativo deve ser ativada.
- A ação a ser tomada quando uma solicitação não está autenticada deve ser definida como **entrar com o Azure Active Directory**.

Captura de tela a seguir demonstra essa configuração:

[!["Configuração de autenticação de aplicativos móveis do azure"](azure-ad-b2c-mobile-app-images/ama-config-cropped.png)](azure-ad-b2c-mobile-app-images/ama-config.png#lightbox "configuração de autenticação de aplicativos móveis do Azure")

A instância de aplicativos móveis do Azure também deve ser configurada para se comunicar com o locatário do Azure Active Directory B2C:

- Clique em configuração do Active Directory do Azure e habilite **avançado** modo para o provedor de autenticação do Active Directory do Azure.
- Definir **ID do cliente** para o **ID do aplicativo** do locatário do Azure Active Directory B2C.
- O **Url do emissor** é a URL de metadados da política que você copiou anteriormente durante a configuração de locatário.

Captura de tela a seguir demonstra essa configuração:

!["Configuração avançada de aplicativos móveis do azure"](azure-ad-b2c-mobile-app-images/ama-advanced-config.png)

### <a name="signing-in"></a>Entrando

O exemplo de código a seguir mostra como iniciar um fluxo de trabalho de autenticação gerenciada pelo servidor:

```csharp
public async Task<bool> AuthenticateAsync()
{
    ...

    MobileServiceUser user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        Constants.URLScheme);

    ...
}
```

Quando o `CurrentClient.LoginAsync` método é invocado, a instância de aplicativos móveis do Azure executa a política do Azure Active Directory B2C vinculada, que inicia o fluxo de autenticação OAuth 2.0. Cada `AuthenticateAsync` método é específico da plataforma. No entanto, cada `AuthenticateAsync` chamadas de método a `CurrentClient.LoginAsync` método e especifica que um locatário do Azure Active Directory será usado no processo de autenticação. Para obter mais informações, consulte [fazer logon de usuários](~/xamarin-forms/data-cloud/authentication/azure.md#logging-in).

O `CurrentClient.LoginAsync` método retorna um `MobileServiceUser` que será armazenada na instância a `CurrentClient.CurrentUser` propriedade. Esta propriedade fornece `UserId` e `MobileServiceAuthenticationToken` propriedades. Elas representam o usuário autenticado e um token de autenticação para o usuário, que pode ser usado até que ela expire. O token de autenticação será incluído em todas as solicitações feitas para a instância de aplicativos móveis do Azure, permitindo que o aplicativo xamarin. Forms executar ações na instância de aplicativos móveis do Azure que exigem permissões de usuário autenticado.

### <a name="signing-out"></a>Sair

O exemplo de código a seguir mostra como o processo de saída gerenciada por servidor é invocado:

```csharp
public async Task<bool> LogoutAsync()
{
    ...

    await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();

    ...
}
```

O `MobileServiceClient.LogoutAsync` método desprovisionar autentica o usuário com a instância de aplicativos móveis do Azure. Para obter mais informações, consulte [registro em log Out usuários](~/xamarin-forms/data-cloud/authentication/azure.md#logging-out).


## <a name="related-links"></a>Links relacionados

- [TodoAzureAuth ClientFlow (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)
- [TodoAzureAuth ServerFlow (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CServerFlow/)
- [Consumir um aplicativo móvel do Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Autenticar usuários com aplicativos móveis do Azure](~/xamarin-forms/data-cloud/authentication/azure.md)
- [Autenticar usuários com o Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)
- [Pacote do nuget de biblioteca de autenticação da Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client)
- [Documentação da biblioteca de autenticação da Microsoft](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)