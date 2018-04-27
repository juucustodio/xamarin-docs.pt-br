---
title: Autenticação de usuários com o Azure Active Directory B2C
description: B2C de diretório ativo do Azure é uma solução de gerenciamento de identidade de nuvem para aplicativos web voltado ao consumidor e móveis. Este artigo demonstra como usar a biblioteca de autenticação da Microsoft e o Azure Active Directory B2C para integrar o gerenciamento de identidades de consumidor em um aplicativo móvel.
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 627c6773c099c9cf45f871a9bb73a201bf98271a
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2018
---
# <a name="authenticating-users-with-azure-active-directory-b2c"></a>Autenticação de usuários com o Azure Active Directory B2C

_B2C de diretório ativo do Azure é uma solução de gerenciamento de identidade de nuvem para aplicativos web voltado ao consumidor e móveis. Este artigo demonstra como usar a biblioteca de autenticação da Microsoft e o Azure Active Directory B2C para integrar o gerenciamento de identidades de consumidor em um aplicativo móvel._

![](~/media/shared/preview.png "Esta API é atualmente pré-lançamento")

> [!NOTE]
> O [biblioteca de autenticação do Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client) ainda está em visualização, mas é adequado para uso em um ambiente de produção. No entanto, há pode ser alterações significativas para a API, formato de cache interno e outros mecanismos de biblioteca, o que pode afetar seu aplicativo.

## <a name="overview"></a>Visão geral

B2C de diretório ativo do Azure é um serviço de gerenciamento de identidade para aplicativos voltados para o consumidor, que permite que os consumidores entrar no seu aplicativo por:

- Usando suas contas existentes sociais (Microsoft, Google, Facebook, Amazon, LinkedIn).
- Criando novas credenciais (endereço de email e senha, ou nome de usuário e senha). Essas credenciais são chamadas de *local* contas.

O processo para integrar o serviço de gerenciamento de identidade do Azure Active Directory B2C em um aplicativo móvel é o seguinte:

1. Crie um locatário do Azure Active Directory B2C. Para obter mais informações, consulte [criar um locatário do Azure Active Directory B2C no portal do Azure](/azure/active-directory-b2c/active-directory-b2c-get-started/).
1. Registre seu aplicativo móvel com o locatário do Azure Active Directory B2C. O processo de registro atribui um **ID do aplicativo** que identifica exclusivamente o seu aplicativo e um **URL de redirecionamento** que pode ser usado para direcionar as respostas de volta para seu aplicativo. Para obter mais informações, consulte [do Azure Active Directory B2C: registrar seu aplicativo](/azure/active-directory-b2c/active-directory-b2c-app-registration/).
1. Crie uma política de inscrição e entrar. Essa política definirá as experiências que os consumidores revisará durante a inscrição e entrar e também especifica os conteúdos de tokens que o aplicativo receberá bem-sucedido inscrever-se ou entrar. Para obter mais informações, consulte [do Azure Active Directory B2C: políticas internas](/azure/active-directory-b2c/active-directory-b2c-reference-policies/).
1. Use o [biblioteca de autenticação do Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client) (MSAL) em seu aplicativo móvel para iniciar um fluxo de trabalho de autenticação com seu locatário do Azure Active Directory B2C.

> [!NOTE]
> Assim como integrar o gerenciamento de identidades do Azure Active Directory B2C em aplicativos móveis, MSAL também pode ser usado para integrar o gerenciamento de identidades do Active Directory do Azure em aplicativos móveis. Isso pode ser feito ao registrar um aplicativo móvel com o Active Directory do Azure no [Portal de registro de aplicativo](https://apps.dev.microsoft.com/). O processo de registro atribui um **ID do aplicativo** que identifica exclusivamente o seu aplicativo, que deve ser especificado ao usar MSAL. Para obter mais informações, consulte [como registrar um aplicativo com o ponto de extremidade v 2.0](/azure/active-directory/develop/active-directory-v2-app-registration/), e [autenticar seu celular aplicativos usando autenticação biblioteca Microsoft](https://blog.xamarin.com/authenticate-mobile-apps-using-microsoft-authentication-library/) no blog do Xamarin.

MSAL usa um navegador da web do dispositivo para realizar a autenticação. Isso melhora a usabilidade de um aplicativo, como os usuários só precisam entrar depois por dispositivo, melhorando as taxas de conversão de entrada e autorização flui no aplicativo. O navegador de dispositivo também fornece maior segurança. Depois que o usuário concluir o processo de autenticação, controle retorna para o aplicativo a partir da guia do navegador da web. Isso é obtido registrando um esquema de URL personalizado para a URL de redirecionamento que é retornada o processo de autenticação e, em seguida, detectar e tratar a URL personalizada quando ele é enviado. Para obter mais informações sobre como escolher um esquema de URL personalizado, consulte [escolhendo um URI de redirecionamento do aplicativo nativo](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-native-app-redirect-uri/).

> [!NOTE]
> O mecanismo para registrar um esquema de URL personalizado com o sistema operacional e lidar com o esquema é específico para cada plataforma.

Cada solicitação que é enviada a um locatário do Azure Active Directory B2C Especifica um *política*. Políticas de descrevem as experiências de identidade de consumidor, como se inscrever ou entrar. Por exemplo, uma política de inscrição permite que o comportamento do locatário do Azure Active Directory B2C deve ser configurado por meio das seguintes configurações:

- Tipos de conta que os consumidores podem utilizar para entrar no aplicativo.
- Dados a serem coletados do consumidor durante a inscrição.
- Autenticação multifator.
- Conteúdo da página de inscrição.
- Declarações de token que o aplicativo móvel recebe quando a política foi executada.

Um locatário do Active Directory do Azure pode conter várias políticas de tipos diferentes, que podem ser usadas em seu aplicativo conforme necessário. Além disso, as políticas podem ser reutilizadas em aplicativos, permitindo que você definir e modificar as experiências de identidade de consumidor sem alterar seu código. Para obter mais informações sobre políticas, consulte [do Azure Active Directory B2C: políticas internas](/azure/active-directory-b2c/active-directory-b2c-reference-policies/).

## <a name="setup"></a>Configuração

Biblioteca do NuGet da biblioteca de autenticação da Microsoft (MSAL) deve ser adicionada ao projeto de biblioteca de classe portátil (PCL) e projetos de plataforma em uma solução xamarin. Forms. As seções a seguir fornecem instruções de configuração adicionais para usar MSAL para se comunicar com um locatário do Azure Active Directory B2C de um aplicativo móvel.

### <a name="portable-class-library"></a>Biblioteca de Classes Portátil

PCLs que consomem MSAL precisará ser redirecionado para usar Profile7. Para obter mais informações sobre PCLs, consulte [Introduction to Portable Class Libraries](~/cross-platform/app-fundamentals/pcl.md) (Introdução às bibliotecas de classes portáteis).

### <a name="ios"></a>iOS

No iOS, o esquema de URL personalizado que foi registrado com o Azure Active Directory B2C deve ser registrado no **Info. plist**, conforme mostrado na seguinte captura de tela:

![](azure-ad-b2c-images/customurl-ios.png "Registrando um esquema de URL personalizado no iOS")

Quando o Azure Active Directory B2C conclui a solicitação de autorização, ele redireciona para a URL de redirecionamento registrado. Como a URL usa um esquema personalizado resulta na inicialização do aplicativo móvel do iOS, passando a URL como um parâmetro de inicialização, onde ela é processada pelo `OpenUrl` substituir o aplicativo `AppDelegate` classe, que é mostrado no código a seguir exemplo:

```csharp
using Microsoft.Identity.Client;

namespace TodoAzure.iOS
{
    [Register("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        ...
        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
            return true;
        }
    }
}
```

O código de `OpenURL` método garante que o controle retorna para MSAL depois que a parte interativa do fluxo de trabalho de autenticação foi encerrada.

### <a name="android"></a>Android

No Android, o esquema de URL personalizado que foi registrado com o Azure Active Directory B2C deve ser registrado no **AndroidManifest.xml**, adicionando um `<activity>` elemento dentro existente `<application>` elemento. O `<activity>` elemento Especifica o `IntentFilter` no `Activity` que lida com o esquema e é mostrado no exemplo a seguir:

```xml
<application ...>
  <activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
      <action android:name="android.intent.action.VIEW" />
      <category android:name="android.intent.category.DEFAULT" />
      <category android:name="android.intent.category.BROWSABLE" />
      <data android:scheme="INSERT_URL_SCHEME_HERE" android:host="auth" />
    </intent-filter>
  </activity>
</application>
```

Quando o Azure Active Directory B2C conclui a solicitação de autorização, ele redireciona para a URL de redirecionamento registrado. Como a URL usa um esquema personalizado resulta na inicialização do aplicativo móvel do Android, passando a URL como um parâmetro de inicialização, onde ela é processada pelo `microsoft.identity.client.BrowserTabActivity`. Observe que o `data android:scheme` propriedade deve ser definida para o esquema de URL personalizado que está registrado com o aplicativo do Azure Active Directory B2C.

Além disso, a `MainActivity` classe deve ser modificada, conforme mostrado no exemplo de código a seguir:

```csharp
using Microsoft.Identity.Client;

namespace TodoAzure.Droid
{
    ...
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            global::Xamarin.Forms.Forms.Init(this, bundle);
            Microsoft.WindowsAzure.MobileServices.CurrentPlatform.Init();
            LoadApplication(new App());
            App.UiParent = new UIParent(this);
        }

        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);
            AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
        }
    }
}

```

O `OnCreate` método é modificado, atribuindo um `UIParent` de instância para o `App.UiParent` propriedade. Isso garante que o fluxo de autenticação ocorre no contexto da atividade atual.

O código de `OnActivityResult` método garante que o controle retorna para MSAL depois que a parte interativa do fluxo de trabalho de autenticação foi encerrada.

### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

Na plataforma Universal do Windows, nenhuma configuração adicional é necessária para usar MSAL.

## <a name="initialization"></a>Inicialização

A biblioteca de autenticação da Microsoft usa os membros do `PublicClientApplication` classe para iniciar um fluxo de trabalho de autenticação. O aplicativo de exemplo declara e inicializa um `public` propriedade desse tipo, denominado `ADB2CClient`, no `AuthenticationProvider` classe. O exemplo de código a seguir mostra como essa propriedade é inicializada:

```csharp
ADB2CClient = new PublicClientApplication(Constants.ClientID, Constants.Authority);
```

Quando o aplicativo móvel foi registrado com o locatário do Azure Active Directory B2C, o processo de registro atribuído um **ID do aplicativo**. Essa ID deve ser especificada no `PublicClientApplication` construtor, junto com um `Authority` constante que consiste em uma URL base e a política do Azure Active Directory B2C a ser executado.

## <a name="signing-in"></a>Entrar

Tela entrar no aplicativo de exemplo é mostrada nas capturas de tela seguir:

![](azure-ad-b2c-images/login.png "Página de logon")

Entrar com provedores de identidade de redes sociais, ou com uma conta local, são permitidos. Embora a Microsoft, Google e Facebook, como mostrado acima, são usados como provedores de identidade de redes sociais, outros provedores de identidade também podem ser usados.

O exemplo de código a seguir mostra como o processo é chamado:

```csharp
using Microsoft.Identity.Client;

public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...
    AuthenticationResult authenticationResult = await ADB2CClient.AcquireTokenAsync(
        Constants.Scopes,
        GetUserByPolicy(ADB2CClient.Users, Constants.PolicySignUpSignIn),
        App.UiParent);
    ...
}

```

O `AcquireTokenAsync` método inicia o navegador da web do dispositivo e exibe as opções de autenticação definidas na política do Azure Active Directory B2C é especificada pela política referenciada por meio de `Constants.Authority` constante. Essa diretiva define as experiências que os consumidores revisará durante a inscrição e entrar e as declarações que o aplicativo receberá bem-sucedido inscrever-se ou entrar.

O resultado da `AcquireTokenAsync` chamada de método é um `AuthenticationResult` instância. Se a autenticação for bem-sucedida, o `AuthenticationResult` instância conterá um token de identidade serão ser armazenadas em cache localmente. Se a autenticação for bem-sucedida, o `AuthenticationResult` instância conterá dados que indicam por que a autenticação falhou.

O aplicativo de exemplo, se a autenticação for bem-sucedida, o `TodoList` navega para a página.

## <a name="silent-re-authentication"></a>Reautenticação silenciosa

Quando o `LoginPage` no exemplo de aplicativo é exibido, é feita uma tentativa de recuperar um token de usuário sem exibir nenhuma interface do usuário de autenticação. Isso é feito com o `AcquireTokenSilentAsync` método, conforme demonstrado no exemplo de código a seguir:

```csharp
public async Task<bool> LoginAsync(bool useSilent = false)
{
    ...
    AuthenticationResult authenticationResult;

    if (useSilent)
    {
        authenticationResult = await ADB2CClient.AcquireTokenSilentAsync(
            Constants.Scopes,
            GetUserByPolicy(ADB2CClient.Users, Constants.PolicySignUpSignIn),
            Constants.Authority,
            false);
    }
    ...
}
```

O `AcquireTokenSilentAsync` método tenta recuperar um token de usuário do cache, sem exigir que o usuário entrar. Isso controla o cenário em que um token adequado pode já estar presente no cache de sessões anteriores. Se a tentativa de obter um token for bem-sucedida, o `TodoList` navega para a página. Se a tentativa de obter um token for bem-sucedida, nada acontece e o usuário terá a opção de iniciar um novo fluxo de trabalho de autenticação.

## <a name="signing-out"></a>Sair

O exemplo de código a seguir mostra como o processo de logout é chamado:

```csharp
public async Task<bool> LogoutAsync()
{
    ...
    foreach (var user in ADB2CClient.Users)
    {
        ADB2CClient.Remove(user);
    }
    ...
}
```

Isso limpará todos os tokens de autenticação do cache local.

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar a biblioteca de autenticação da Microsoft (MSAL) e o Azure Active Directory B2C para integrar o gerenciamento de identidades de consumidor em um aplicativo móvel. B2C de diretório ativo do Azure é uma solução de gerenciamento de identidade de nuvem para aplicativos web voltado ao consumidor e móveis.


## <a name="related-links"></a>Links relacionados

- [AzureADB2CAuth (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Biblioteca de autenticação da Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client)
