---
title: Autenticar usuários com o Azure Active Directory B2C
description: Azure Active Directory B2C do diretório é uma solução de gerenciamento de identidade de nuvem para aplicativos móveis e web voltados ao consumidor. Este artigo demonstra como usar a biblioteca de autenticação da Microsoft e o Azure Active Directory B2C para integrar o gerenciamento de identidade do consumidor em um aplicativo móvel.
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 7c12136a0dad0165c46f1559e7a2d61abaf7af1e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331469"
---
# <a name="authenticating-users-with-azure-active-directory-b2c"></a>Autenticar usuários com o Azure Active Directory B2C

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)

_Azure Active Directory B2C do diretório é uma solução de gerenciamento de identidade de nuvem para aplicativos móveis e web voltados ao consumidor. Este artigo demonstra como usar a biblioteca de autenticação da Microsoft e o Azure Active Directory B2C para integrar o gerenciamento de identidade do consumidor em um aplicativo móvel._

![](~/media/shared/preview.png "Essa API é atualmente pré-lançamento")

> [!NOTE]
> O [biblioteca de autenticação da Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client) ainda está em visualização, mas é adequado para uso em um ambiente de produção. No entanto, há podem ser alterações significativas para a API, formato de cache interno e outros mecanismos para a biblioteca, que podem afetar seu aplicativo.

## <a name="overview"></a>Visão geral

Azure Active Directory B2C do diretório é um serviço de gerenciamento de identidade para aplicativos voltados ao consumidor, que permite que os consumidores entrar no seu aplicativo por:

- Usando suas contas sociais existentes (Microsoft, Google, Facebook, Amazon, LinkedIn).
- Criando novas credenciais (endereço de email e senha, ou nome de usuário e senha). Essas credenciais são denominadas *local* contas.

O processo para integrar o serviço de gerenciamento de identidade do Azure Active Directory B2C em um aplicativo móvel é da seguinte maneira:

1. Crie um locatário do Azure Active Directory B2C. Para obter mais informações, consulte [criar um locatário do Azure Active Directory B2C no portal do Azure](/azure/active-directory-b2c/active-directory-b2c-get-started/).
1. Registre seu aplicativo móvel com o locatário do Azure Active Directory B2C. O processo de registro atribui uma **ID do aplicativo** que identifica exclusivamente o seu aplicativo e um **URL de redirecionamento** que pode ser usado para direcionar as respostas de volta ao seu aplicativo. Para obter mais informações, consulte [do Azure Active Directory B2C: Registrar seu aplicativo](/azure/active-directory-b2c/active-directory-b2c-app-registration/).
1. Crie uma política de inscrição e entrada. Essa política definirá as experiências pelas quais os consumidores passarão durante a inscrição e entrada e também especifica o conteúdo dos tokens que o aplicativo receberá bem-sucedido inscrever-se ou entrar. Para obter mais informações, consulte [do Azure Active Directory B2C: Políticas internas](/azure/active-directory-b2c/active-directory-b2c-reference-policies/).
1. Use o [biblioteca de autenticação da Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client) (MSAL) em seu aplicativo móvel para iniciar um fluxo de trabalho de autenticação com seu locatário do Azure Active Directory B2C.

> [!NOTE]
> Bem como integrar o gerenciamento de identidades do Azure Active Directory B2C em aplicativos móveis, a MSAL também pode ser usada para integrar o gerenciamento de identidade do Active Directory do Azure em aplicativos móveis. Isso pode ser feito ao registrar um aplicativo móvel com o Azure Active Directory na [Portal de registro de aplicativo](https://apps.dev.microsoft.com/). O processo de registro atribui uma **ID do aplicativo** que identifica exclusivamente o seu aplicativo, que deve ser especificado ao usar a MSAL. Para obter mais informações, consulte [como registrar um aplicativo com o ponto de extremidade v 2.0](/azure/active-directory/develop/active-directory-v2-app-registration/), e [autenticar seu Mobile Apps usando biblioteca de autenticação Microsoft](https://blog.xamarin.com/authenticate-mobile-apps-using-microsoft-authentication-library/) no blog do Xamarin.

MSAL usa um navegador da web do dispositivo para realizar a autenticação. Isso melhora a usabilidade de um aplicativo, como os usuários precisam apenas entrar depois por dispositivo, melhorar as taxas de conversão de entrada e autorização fluxos no aplicativo. O navegador do dispositivo também fornece segurança aprimorada. Depois que o usuário concluir o processo de autenticação, controle retornará para o aplicativo da guia do navegador da web. Isso é feito registrando um esquema de URL personalizado para a URL de redirecionamento que é retornada para o processo de autenticação e, em seguida, detectar e manipular a URL personalizada quando ele é enviado. Para obter mais informações sobre como escolher um esquema de URL personalizado, consulte [escolhendo um URI de redirecionamento do aplicativo nativo](/azure/active-directory-b2c/active-directory-b2c-app-registration#choosing-a-native-app-redirect-uri/).

> [!NOTE]
> O mecanismo para registrar um esquema de URL personalizado com o sistema operacional e lidar com o esquema é específico para cada plataforma.

Cada solicitação é enviada a um locatário do Azure Active Directory B2C Especifica uma *diretiva*. As políticas descrevem experiências de identidade do consumidor, como inscrição ou entrada. Por exemplo, uma política de inscrição permite que o comportamento do locatário do Azure Active Directory B2C para ser configurado por meio das seguintes configurações:

- Tipos de conta que os consumidores podem usar para entrar no aplicativo.
- Dados a serem coletados junto ao consumidor durante a inscrição.
- A autenticação multifator.
- Conteúdo da página de inscrição.
- Declarações de token que o aplicativo móvel recebe quando a política foi executada.

Um locatário do Azure Active Directory pode conter várias políticas de tipos diferentes, que podem ser usados em seu aplicativo conforme necessário. Além disso, as políticas podem ser reutilizadas em aplicativos, permitindo que você definir e modificar experiências de identidade do consumidor sem alterar seu código. Para obter mais informações sobre políticas, consulte [do Azure Active Directory B2C: Políticas internas](/azure/active-directory-b2c/active-directory-b2c-reference-policies/).

## <a name="setup"></a>Configuração

A biblioteca do NuGet da biblioteca de autenticação da Microsoft (MSAL) deve ser adicionada ao projeto de biblioteca de classe portátil (PCL) e projetos de plataforma em uma solução do xamarin. Forms. As seções a seguir fornecem instruções de configuração adicionais para usar MSAL para se comunicar com um locatário do Azure Active Directory B2C em um aplicativo móvel.

### <a name="portable-class-library"></a>Biblioteca de Classes Portátil

PCLs que consomem MSAL precisará ser redirecionados para usar Profile7. Para obter mais informações sobre PCLs, consulte [Introduction to Portable Class Libraries](~/cross-platform/app-fundamentals/pcl.md) (Introdução às bibliotecas de classes portáteis).

### <a name="ios"></a>iOS

No iOS, o esquema de URL personalizado que foi registrado com o Azure Active Directory B2C deve ser registrado no **Info. plist**, conforme mostrado na seguinte captura de tela:

![](azure-ad-b2c-images/customurl-ios.png "Registrando um esquema de URL personalizada no iOS")

Quando o Azure Active Directory B2C conclui a solicitação de autorização, ele redireciona para a URL de redirecionamento registrado. Como a URL usa um esquema personalizado, ele resulta na inicialização do aplicativo móvel do iOS, passando a URL como um parâmetro de inicialização, onde ela é processada pelo `OpenUrl` substituir da caixa de diálogo `AppDelegate` classe, que é mostrado no código a seguir exemplo:

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

O código a `OpenURL` método garante que o controle retorna para a MSAL depois que a parte interativa do fluxo de trabalho de autenticação terminou.

### <a name="android"></a>Android

No Android, o esquema de URL personalizado que foi registrado com o Azure Active Directory B2C deve ser registrado no **androidmanifest. XML**, com a adição de uma `<activity>` elemento dentro de existente `<application>` elemento. O `<activity>` elemento Especifica a `IntentFilter` sobre o `Activity` que lida com o esquema e é mostrado no exemplo a seguir:

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

Quando o Azure Active Directory B2C conclui a solicitação de autorização, ele redireciona para a URL de redirecionamento registrado. Como a URL usa um esquema personalizado, ele resulta na inicialização do aplicativo móvel do Android, passando a URL como um parâmetro de inicialização, onde ela é processada pelo `microsoft.identity.client.BrowserTabActivity`. Observe que o `data android:scheme` propriedade deve ser definida para o esquema de URL personalizado que está registrado com o aplicativo do Azure Active Directory B2C.

Além disso, o `MainActivity` classe deve ser modificada, conforme mostrado no exemplo de código a seguir:

```csharp
using Microsoft.Identity.Client;

namespace TodoAzure.Droid
{
    ...
    public class MainActivity : FormsAppCompatActivity
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

O `OnCreate` método é modificado, atribuindo um `UIParent` da instância para o `App.UiParent` propriedade. Isso garante que o fluxo de autenticação ocorre no contexto da atividade atual.

O código a `OnActivityResult` método garante que o controle retorna para a MSAL depois que a parte interativa do fluxo de trabalho de autenticação terminou.

### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

Na plataforma Universal do Windows, nenhuma configuração adicional é necessário para usar a MSAL.

## <a name="initialization"></a>Inicialização

A biblioteca de autenticação da Microsoft usa os membros de `PublicClientApplication` classe iniciar um fluxo de trabalho de autenticação. O aplicativo de exemplo declara e inicializa um `public` propriedade desse tipo, chamado `ADB2CClient`, no `AuthenticationProvider` classe. O exemplo de código a seguir mostra como essa propriedade é inicializada:

```csharp
ADB2CClient = new PublicClientApplication(Constants.ClientID, Constants.Authority);
```

Quando o aplicativo móvel foi registrado com o locatário do Azure Active Directory B2C, o processo de registro é atribuído um **ID do aplicativo**. Essa ID deve ser especificada na `PublicClientApplication` construtor, junto com um `Authority` constante que consiste em uma URL base e a política do Azure Active Directory B2C para ser executado.

## <a name="signing-in"></a>Entrar

A tela de entrada no aplicativo de exemplo é mostrada nas capturas de tela seguir:

![](azure-ad-b2c-images/login.png "Página de logon")

Entrar com provedores de identidade social, ou com uma conta local, são permitidos. Embora a Microsoft, Google e Facebook, como mostrado acima, são usados como provedores de identidade social, outros provedores de identidade também podem ser usados.

O exemplo de código a seguir mostra como o processo de logon é invocado:

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

O `AcquireTokenAsync` método inicia o navegador da web do dispositivo e exibe as opções de autenticação definidas na política do Azure Active Directory B2C é especificada pela política referenciada por meio de `Constants.Authority` constante. Essa política define as experiências pelas quais os consumidores passarão durante a inscrição e entrar e as declarações que o aplicativo receberá bem-sucedido inscrever-se ou entrar.

O resultado do `AcquireTokenAsync` chamada de método é um `AuthenticationResult` instância. Se a autenticação for bem-sucedida, o `AuthenticationResult` instância conterá um token de identidade, que será armazenado localmente. Se a autenticação for bem-sucedida, o `AuthenticationResult` instância conterá os dados que indicam por que a autenticação falhou.

No aplicativo de exemplo, se a autenticação for bem-sucedida, o `TodoList` página é navegada.

## <a name="silent-re-authentication"></a>Reautenticação silenciosa

Quando o `LoginPage` no exemplo de aplicativo for exibido, é feita uma tentativa de recuperar um token de usuário sem mostrar qualquer interface do usuário de autenticação. Isso é feito com o `AcquireTokenSilentAsync` método, conforme demonstrado no exemplo de código a seguir:

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

O `AcquireTokenSilentAsync` método tenta recuperar um token de usuário do cache, sem exigir que o usuário entrar. Ele lida com o cenário em que um token adequado pode já estar presente no cache de sessões anteriores. Se a tentativa de obter um token for bem-sucedida, o `TodoList` página é navegada. Se a tentativa de obter um token não for bem-sucedida, nada acontece e o usuário terá a opção para iniciar um novo fluxo de trabalho de autenticação.

## <a name="signing-out"></a>Sair

O exemplo de código a seguir mostra como o processo de saída é invocado:

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

Isso limpa todos os tokens de autenticação do cache local.

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar a biblioteca de autenticação da Microsoft (MSAL) e o Azure Active Directory B2C para integrar o gerenciamento de identidade do consumidor em um aplicativo móvel. Azure Active Directory B2C do diretório é uma solução de gerenciamento de identidade de nuvem para aplicativos móveis e web voltados ao consumidor.


## <a name="related-links"></a>Links relacionados

- [AzureADB2CAuth (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Biblioteca de autenticação da Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client)
