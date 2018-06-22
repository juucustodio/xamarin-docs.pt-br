---
title: Integração do Azure Active Directory B2C com aplicativos móveis do Azure
description: B2C de diretório ativo do Azure é uma solução de gerenciamento de identidade de nuvem para aplicativos web voltado ao consumidor e móveis. Este artigo demonstra como usar o Azure Active Directory B2C para fornecer autenticação e autorização para uma instância de aplicativos do Azure Mobile xamarin. Forms.
ms.prod: xamarin
ms.assetid: 53F52036-A997-4D0F-86B4-4302C6913136
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: cafc1e78779dc393fa0409daa08b3daa8948a1ee
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30787968"
---
# <a name="integrating-azure-active-directory-b2c-with-azure-mobile-apps"></a>Integração do Azure Active Directory B2C com aplicativos móveis do Azure

_B2C de diretório ativo do Azure é uma solução de gerenciamento de identidade de nuvem para aplicativos web voltado ao consumidor e móveis. Este artigo demonstra como usar o Azure Active Directory B2C para fornecer autenticação e autorização para uma instância de aplicativos do Azure Mobile xamarin. Forms._

![](~/media/shared/preview.png "Esta API é atualmente pré-lançamento")

> [!NOTE]
> O [biblioteca de autenticação do Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client) ainda está em visualização, mas é adequado para uso em um ambiente de produção. No entanto, há pode ser alterações significativas para a API, formato de cache interno e outros mecanismos de biblioteca, o que pode afetar seu aplicativo.

## <a name="overview"></a>Visão geral

Aplicativos móveis do Azure permitem que você desenvolva aplicativos com o back-ends escalonável hospedado no serviço de aplicativo do Azure, com suporte para autenticação móvel, sincronização offline e notificações por push. Para obter mais informações sobre os aplicativos móveis do Azure, consulte [consumindo um aplicativo do Azure Mobile](~/xamarin-forms/data-cloud/consuming/azure.md), e [autenticando usuários com aplicativos móveis do Azure](~/xamarin-forms/data-cloud/authentication/azure.md).

B2C de diretório ativo do Azure é um serviço de gerenciamento de identidade para aplicativos voltados para o consumidor, que permite que os consumidores entrar no seu aplicativo por:

- Usando suas contas existentes sociais (Microsoft, Google, Facebook, Amazon, LinkedIn).
- Criando novas credenciais (endereço de email e senha, ou nome de usuário e senha). Essas credenciais são chamadas de *local* contas.

Para obter mais informações sobre o Azure Active Directory B2C, consulte [autenticando usuários com o Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

B2C de diretório ativo do Azure pode ser usado para gerenciar o fluxo de trabalho de autenticação para um aplicativo do Azure Mobile. Com essa abordagem, a experiência de gerenciamento de identidade está totalmente definida na nuvem e pode ser modificada sem alterar o código do aplicativo móvel.

Há dois fluxos de trabalho de autenticação que podem ser adotados ao integrar um locatário do Azure Active Directory B2C com uma instância de aplicativos do Azure Mobile:

- [Cliente gerenciado](#client_managed) – neste abordar o inicia de aplicativos móveis xamarin. Forms o processo de autenticação com o locatário do Azure Active Directory B2C e passa o token de autenticação recebido para a instância os aplicativos móveis do Azure.
- [Servidor gerenciado](#server_managed) – essa abordagem de aplicativos móveis do Azure instância usa o locatário do Azure Active Directory B2C para iniciar o processo de autenticação por meio de um fluxo de trabalho baseado na web.

Em ambos os casos, a experiência de autenticação é fornecida pelo locatário do Azure Active Directory B2C. O aplicativo de exemplo, isso resulta na tela de mostrado nas capturas de tela seguir:

![](azure-ad-b2c-mobile-app-images/screenshots.png "Página de logon")

Entrar com provedores de identidade de redes sociais, ou com uma conta local, são permitidos. Enquanto o Facebook, Google e Microsoft são usados como provedores de identidade de redes sociais neste exemplo, outros provedores de identidade também podem ser usados.

## <a name="setup"></a>Configuração

Independentemente do fluxo de trabalho de autenticação usado, o processo inicial para a integração de um locatário do Azure Active Directory B2C com uma instância de aplicativos do Azure Mobile é o seguinte:

1. Crie uma instância dos aplicativos móveis do Azure. Para obter mais informações, consulte [consumindo um aplicativo do Azure Mobile](~/xamarin-forms/data-cloud/consuming/azure.md).
1. Habilite a autenticação na instância os aplicativos móveis do Azure e o aplicativo xamarin. Forms. Para obter mais informações, consulte [autenticando usuários com aplicativos móveis do Azure](~/xamarin-forms/data-cloud/authentication/azure.md).
1. Crie um locatário do Azure Active Directory B2C. Para obter mais informações, consulte [autenticando usuários com o Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Observe que a biblioteca de autenticação da Microsoft (MSAL) é necessária ao usar um fluxo de trabalho de autenticação de cliente gerenciado. MSAL usa um navegador da web do dispositivo para realizar a autenticação. Isso melhora a usabilidade de um aplicativo, como os usuários só precisam entrar depois por dispositivo, melhorando as taxas de conversão de entrada e autorização flui no aplicativo. O navegador de dispositivo também fornece maior segurança. Depois que o usuário concluir o processo de autenticação, controle retorna para o aplicativo a partir da guia do navegador da web. Isso é obtido registrando um esquema de URL personalizado para a URL de redirecionamento que é retornada o processo de autenticação e, em seguida, detectar e tratar a URL personalizada quando ele é enviado. Para obter mais informações sobre como usar MSAL para se comunicar com um locatário do Azure Active Directory B2C, consulte [autenticando usuários com o Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

<a name="client_managed" />

## <a name="client-managed-authentication"></a>Autenticação de cliente gerenciado

Na autenticação de cliente gerenciado, um aplicativo móvel do xamarin. Forms entra em contato com um locatário do Azure Active Directory B2C para iniciar um fluxo de autenticação. Após o logon bem-sucedido do B2C do Azure Active Directory locatário retorna um token de identidade que é então fornecido durante a entrada para a instância os aplicativos móveis do Azure. Isso permite que o aplicativo xamarin. Forms executar ações na instância de aplicativos do Azure Mobile que requer permissões de usuário autenticado.

### <a name="azure-active-directory-b2c-tenant-configuration"></a>Configuração de locatário do Azure Active Directory B2C

Para um fluxo de trabalho de autenticação de cliente gerenciado, o locatário do Azure Active Directory B2C deve ser configurado da seguinte maneira:

- Inclua um cliente nativo.
- Definir o URI de redirecionamento personalizado a um esquema de URL que identifica exclusivamente o aplicativo móvel, seguido por `://auth/`. Para obter mais informações sobre como escolher um esquema de URL personalizado, consulte [escolhendo um URI de redirecionamento do aplicativo nativo](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-native-app-redirect-uri).

Captura de tela a seguir demonstra essa configuração:

[![](azure-ad-b2c-mobile-app-images/client-flow-config-sml.png "Configuração do Azure Active Directory B2C")](azure-ad-b2c-mobile-app-images/client-flow-config.png#lightbox "configuração do Azure Active Directory B2C")

A política usada no Azure Active Directory B2C locatário também deve ser configurado de forma que a URL de resposta é definida como o mesmo esquema de URL personalizado, seguido por `://auth/`. Captura de tela a seguir demonstra essa configuração:

![](azure-ad-b2c-mobile-app-images/client-flow-policies.png "O Azure Active Directory B2C políticas")

### <a name="azure-mobile-app-configuration"></a>Configuração do aplicativo móvel do Azure

Para um fluxo de trabalho de autenticação de cliente gerenciado, a instância do Azure Mobile aplicativos deve ser configurada da seguinte maneira:

- Autenticação do serviço de aplicativo deve ser ativada.
- A ação a ser tomada quando uma solicitação for autenticada não deve ser definida como **fazer logon com Active Directory do Azure**.

Captura de tela a seguir demonstra essa configuração:

![](azure-ad-b2c-mobile-app-images/client-flow-ama-config.png "Configuração de aplicativos móveis do Azure")

A instância de aplicativos do Azure Mobile também deve ser configurada para se comunicar com o locatário do Azure Active Directory B2C. Isso pode ser feito Ativando **avançado** modo para o provedor de autenticação do Active Directory do Azure, com o **ID do cliente** sendo o **ID do aplicativo** do Azure Locatário do Active Directory B2C e o **Url do emissor** sendo o ponto de extremidade de metadados para a política do Azure Active Directory B2C. Captura de tela a seguir demonstra essa configuração:

![](azure-ad-b2c-mobile-app-images/client-flow-ama-advanced-config.png "Aplicativos móveis do Azure configuração avançada")

### <a name="signing-in"></a>Entrar

O exemplo de código a seguir mostra como iniciar um fluxo de trabalho de autenticação de cliente gerenciado:

```csharp
public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...
    AuthenticationResult authenticationResult = await ADB2CClient.AcquireTokenAsync(
        Constants.Scopes,
        GetUserByPolicy(ADB2CClient.Users, Constants.PolicySignUpSignIn),
        App.UiParent);

    ...
    var payload = new JObject();
    payload["access_token"] = authenticationResult.IdToken;

    User = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        payload);
    ...
}
```

A biblioteca de autenticação da Microsoft (MSAL) é usado para iniciar um fluxo de trabalho de autenticação com o locatário do Azure Active Directory B2C. O `AcquireTokenAsync` método inicia o navegador da web do dispositivo e exibe as opções de autenticação definidas na política do Azure Active Directory B2C é especificada pela política referenciada por meio de `Constants.Authority` constante. Essa diretiva define as experiências que os consumidores revisará durante a inscrição e entrar e as declarações que o aplicativo receberá bem-sucedido inscrever-se ou entrar.

O resultado da `AcquireTokenAsync` chamada de método é um `AuthenticationResult` instância. Se a autenticação for bem-sucedida, o `AuthenticationResult` instância conterá um token de identidade serão ser armazenadas em cache localmente. Se a autenticação for bem-sucedida, o `AuthenticationResult` instância conterá dados que indicam por que a autenticação falhou. Para obter informações sobre como usar MSAL para se comunicar com um locatário do Azure Active Directory B2C, consulte [autenticando usuários com o Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md).

Quando o `MobileServiceClient.LoginAsync` método é chamado, a instância de aplicativos do Azure Mobile recebe o token de identidade encapsulado em um `JObject`. A presença de um meio de token válido que a instância de aplicativos do Azure Mobile não precisa iniciar o seu próprio fluxo de autenticação OAuth 2.0. Em vez disso, o `MobileServiceClient.LoginAsync` método retorna um `MobileServiceUser` instância será armazenada no `MobileServiceClient.CurrentUser` propriedade. Esta propriedade fornece `UserId` e `MobileServiceAuthenticationToken` propriedades. Eles representam o usuário autenticado e um token de autenticação para o usuário, que pode ser usado até que ela expire. O token de autenticação será incluído em todas as solicitações feitas para a instância os aplicativos móveis do Azure, permitindo que o aplicativo xamarin. Forms executar ações na instância do Azure Mobile aplicativos que exigem permissões de usuário autenticado.

### <a name="signing-out"></a>Sair

O exemplo de código a seguir mostra como o processo de logout cliente gerenciado é invocado:

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();

    foreach (var user in ADB2CClient.Users)
    {
        ADB2CClient.Remove(user);
    }
    ...
}
```

O `MobileServiceClient.LogoutAsync` método eliminação autentica o usuário com a instância os aplicativos móveis do Azure e, em seguida, todos os tokens de autenticação serão apagados do cache local criado por MSAL.

<a name="server_managed" />

## <a name="server-managed-authentication"></a>Autenticação de servidor gerenciado

Na autenticação de servidor gerenciado, um aplicativo xamarin. Forms entra em contato com uma instância de aplicativos do Azure Mobile, que usa o locatário do Azure Active Directory B2C para gerenciar o fluxo de autenticação OAuth 2.0, exibindo uma página de entrada, conforme definido na política B2C. Após o logon bem-sucedido, a instância os aplicativos móveis do Azure retorna um token que permite que o aplicativo xamarin. Forms executar ações na instância do Azure Mobile aplicativos que exigem permissões de usuário autenticado.

### <a name="azure-active-directory-b2c-tenant-configuration"></a>Configuração de locatário do Azure Active Directory B2C

Para um fluxo de trabalho de autenticação de servidor gerenciado, o locatário do Azure Active Directory B2C deve ser configurado da seguinte maneira:

- Incluir uma aplicativo web/da web API e permitir que o fluxo implícito.
- Definir a URL de resposta para o endereço do aplicativo móvel do Azure, seguido por `/.auth/login/aad/callback`.

Captura de tela a seguir demonstra essa configuração:

[![](azure-ad-b2c-mobile-app-images/server-flow-config-sml.png "Configuração do Azure Active Directory B2C")](azure-ad-b2c-mobile-app-images/server-flow-config.png#lightbox "configuração do Azure Active Directory B2C")

A política usada no Azure Active Directory B2C locatário também deve ser configurado de forma que a URL de resposta é definida como o endereço do aplicativo móvel do Azure, seguido por `/.auth/login/aad/callback`. Captura de tela a seguir demonstra essa configuração:

![](azure-ad-b2c-mobile-app-images/server-flow-policies.png "O Azure Active Directory B2C políticas")

### <a name="azure-mobile-apps-instance-configuration"></a>Configuração da instância de aplicativos móveis do Azure

Para um fluxo de trabalho de autenticação de servidor gerenciado, a instância do Azure Mobile aplicativos deve ser configurada da seguinte maneira:

- Autenticação do serviço de aplicativo deve ser ativada.
- A ação a ser tomada quando uma solicitação for autenticada não deve ser definida como **fazer logon com Active Directory do Azure**.

Captura de tela a seguir demonstra essa configuração:

![](azure-ad-b2c-mobile-app-images/server-flow-ama-config.png "Configuração de aplicativos móveis do Azure")

A instância de aplicativos do Azure Mobile também deve ser configurada para se comunicar com o locatário do Azure Active Directory B2C. Isso pode ser feito Ativando **avançado** modo para o provedor de autenticação do Active Directory do Azure, com o **ID do cliente** sendo o **ID do aplicativo** do Azure Locatário do Active Directory B2C e o **Url do emissor** sendo o ponto de extremidade de metadados para a política do Azure Active Directory B2C. Captura de tela a seguir demonstra essa configuração:

![](azure-ad-b2c-mobile-app-images/server-flow-ama-advanced-config.png "Aplicativos móveis do Azure configuração avançada")

### <a name="signing-in"></a>Entrar

O exemplo de código a seguir mostra como iniciar um fluxo de trabalho de autenticação de servidor gerenciado:

```csharp
public async Task<bool> AuthenticateAsync()
{
    ...
    MobileServiceUser user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(
        UIApplication.SharedApplication.KeyWindow.RootViewController,
        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory,
        Constants.URLScheme);
    ...
}
```

Quando o `MobileServiceClient.LoginAsync` método é chamado, a instância de aplicativos do Azure Mobile executa a política do Azure Active Directory B2C vinculada, que inicia o fluxo de autenticação OAuth 2.0. Observe que cada `AuthenticateAsync` método é específica da plataforma. No entanto, cada `AuthenticateAsync` método usa o `MobileServiceClient.LoginAsync` método e especifica que um locatário do Active Directory do Azure será usado no processo de autenticação. Para obter mais informações, consulte [login de usuários](~/xamarin-forms/data-cloud/authentication/azure.md#logging-in).

O `MobileServiceClient.LoginAsync` método retorna um `MobileServiceUser` instância será armazenada no `MobileServiceClient.CurrentUser` propriedade. Esta propriedade fornece `UserId` e `MobileServiceAuthenticationToken` propriedades. Eles representam o usuário autenticado e um token de autenticação para o usuário, que pode ser usado até que ela expire. O token de autenticação será incluído em todas as solicitações feitas para a instância os aplicativos móveis do Azure, permitindo que o aplicativo xamarin. Forms executar ações na instância do Azure Mobile aplicativos que exigem permissões de usuário autenticado.

### <a name="signing-out"></a>Sair

O exemplo de código a seguir mostra como o processo de logout servidor gerenciado é invocado:

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    await TodoItemManager.DefaultManager.CurrentClient.LogoutAsync();
    ...
}
```

O `MobileServiceClient.LogoutAsync` método eliminação autentica o usuário com a instância os aplicativos móveis do Azure. Para obter mais informações, consulte [log Out usuários](~/xamarin-forms/data-cloud/authentication/azure.md#logging-out).

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar o Azure Active Directory B2C para fornecer autenticação e autorização para uma instância de aplicativos do Azure Mobile xamarin. Forms. B2C de diretório ativo do Azure é uma solução de gerenciamento de identidade de nuvem para aplicativos web voltado ao consumidor e móveis.


## <a name="related-links"></a>Links relacionados

- [TodoAzureAuth ServerFlow (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CServerFlow/)
- [TodoAzureAuth ClientFlow (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/TodoAzureAuthADB2CClientFlow/)
- [Consumo de um aplicativo móvel do Azure](~/xamarin-forms/data-cloud/consuming/azure.md)
- [Autenticação de usuários com aplicativos móveis do Azure](~/xamarin-forms/data-cloud/authentication/azure.md)
- [Autenticação de usuários com o Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md)
- [Biblioteca de autenticação da Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client)
