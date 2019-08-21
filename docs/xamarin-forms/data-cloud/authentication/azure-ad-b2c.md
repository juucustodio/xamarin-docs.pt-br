---
title: Autenticar usuários com Azure Active Directory B2C
description: O Azure Active Directory B2C fornece gerenciamento de identidade de nuvem para aplicativos Web e móveis voltados para o consumidor. Este artigo mostra como usar Azure Active Directory B2C para integrar o gerenciamento de identidades em um aplicativo móvel com a biblioteca de autenticação da Microsoft.
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/17/2019
ms.openlocfilehash: a13501218b6d3039f189693512d185a9d546d23f
ms.sourcegitcommit: 3434624a36a369986b6aeed7959dae60f7112a14
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69629654"
---
# <a name="authenticate-users-with-azure-active-directory-b2c"></a>Autenticar usuários com Azure Active Directory B2C

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azureadb2cauth)

_O Azure Active Directory B2C fornece gerenciamento de identidade de nuvem para aplicativos Web e móveis voltados para o consumidor. Este artigo mostra como usar Azure Active Directory B2C para integrar o gerenciamento de identidades em um aplicativo móvel com a biblioteca de autenticação da Microsoft._

## <a name="overview"></a>Visão geral

O Azure Active Directory B2C (ADB2C) é um serviço de gerenciamento de identidade para aplicativos voltados para o consumidor. Ele permite que os usuários entrem em seu aplicativo usando suas contas sociais existentes ou credenciais personalizadas, como email ou nome de usuário e senha. As contas de credenciais personalizadas são chamadas de contas _locais_ .

O processo para integrar o serviço de gerenciamento de identidade do Azure Active Directory B2C em um aplicativo móvel é da seguinte maneira:

1. Criar um locatário Azure Active Directory B2C
1. Registrar seu aplicativo móvel com o locatário Azure Active Directory B2C
1. Criar políticas para inscrever-se e entrar e esquecer fluxos de usuário de senha
1. Use a MSAL (biblioteca de autenticação da Microsoft) para iniciar um fluxo de trabalho de autenticação com seu locatário Azure Active Directory B2C.

> [!NOTE]
> O Azure Active Directory B2C dá suporte a vários provedores de identidade, incluindo Microsoft, GitHub, Facebook, Twitter e muito mais. Para obter mais informações sobre recursos de Azure Active Directory B2C, consulte [Azure Active Directory B2C documentação](/azure/active-directory-b2c/).
>
> A biblioteca de autenticação da Microsoft dá suporte a várias arquiteturas e plataformas de aplicativos. Para obter informações sobre os recursos do MSAL, consulte [biblioteca de autenticação da Microsoft](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) no github.

## <a name="configure-an-azure-active-directory-b2c-tenant"></a>Configurar um locatário Azure Active Directory B2C

Para executar o projeto de exemplo, você deve criar um locatário Azure Active Directory B2C. Para obter mais informações, consulte [criar um locatário do Azure Active Directory B2C no portal do Azure](/azure/active-directory-b2c/active-directory-b2c-get-started/).

Depois de criar um locatário, você precisará do **nome do locatário** e da **ID do locatário** para configurar o aplicativo móvel. A ID e o nome do locatário são definidos pelo domínio gerado quando você criou a URL do locatário. Se a URL do locatário gerada `https://contoso20190410tenant.onmicrosoft.com/` for **a ID** do `contoso20190410tenant.onmicrosoft.com` locatário e o nome do `contoso20190410tenant` **locatário** for. Localize o domínio do locatário no portal do Azure clicando no **filtro diretório e assinatura** no menu superior. A captura de tela a seguir mostra o diretório do Azure e o botão de filtro de assinatura e o domínio do locatário:

[![Nome do locatário no diretório do Azure e no modo de exibição de filtro de assinatura](azure-ad-b2c-images/azure-tenant-name-cropped.png)](azure-ad-b2c-images/azure-tenant-name.png#lightbox)

No projeto de exemplo, edite o arquivo **Constants.cs** para definir `tenantName` os `tenantId` campos e. O código a seguir mostra como esses valores devem ser definidos se o seu domínio `https://contoso20190410tenant.onmicrosoft.com/`de locatário for, substitua esses valores por valores do seu portal:

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    ...
}
```

## <a name="register-your-mobile-application-with-azure-active-directory-b2c"></a>Registre seu aplicativo móvel com o Azure Active Directory B2C

Um aplicativo móvel deve ser registrado com o locatário antes de poder se conectar e autenticar usuários. O processo de registro atribui uma ID de **aplicativo** exclusiva ao aplicativo e uma **URL** de redirecionamento que direciona as respostas de volta para o aplicativo após a autenticação. Para obter mais informações, [consulte Azure Active Directory B2C: Registre seu aplicativo](/azure/active-directory-b2c/active-directory-b2c-app-registration/). Você precisará saber a **ID do aplicativo** atribuída ao seu aplicativo, que é listada após o nome do aplicativo na exibição Propriedades. A captura de tela a seguir mostra onde encontrar a ID do aplicativo:

[![ID do aplicativo no modo de exibição de propriedades do aplicativo do Azure](azure-ad-b2c-images/azure-application-id-cropped.png)](azure-ad-b2c-images/azure-application-id.png#lightbox)

A biblioteca de autenticação da Microsoft espera que a **URL** de redirecionamento do seu aplicativo seja a **ID do aplicativo** prefixada com o texto "MSAL" e seguida por um ponto de extremidade chamado "auth". Se a ID do aplicativo for "1234abcd", a URL completa deverá `msal1234abcd://auth`ser. Verifique se o aplicativo habilitou a configuração **nativa do cliente** e crie um **URI** de redirecionamento personalizado usando a ID do aplicativo, conforme mostrado na seguinte captura de tela:

![URI de redirecionamento personalizado na exibição de propriedades do aplicativo do Azure](azure-ad-b2c-images/azure-redirect-uri.png)

A URL será usada posteriormente no Android **ApplicationManifest. xml** e no Ios **info. plist**.

No projeto de exemplo, edite o arquivo **Constants.cs** para definir `clientId` o campo para a **ID do aplicativo**. O código a seguir mostra como esse valor deve ser definido se a ID do `1234abcd`aplicativo for:

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    static readonly string clientId = "1234abcd";
    ...
}
```

## <a name="create-sign-up-and-sign-in-policies-and-forgot-password-policies"></a>Criar políticas de inscrição e de entrada e esqueceu as políticas de senha

Uma política é uma experiência que os usuários passam para concluir uma tarefa, como criar uma conta ou redefinir uma senha. Uma política também especifica o conteúdo dos tokens que o aplicativo recebe quando o usuário retorna da experiência. Você deve configurar políticas para inscrição e entrada da conta e redefinir a senha. O Azure tem políticas internas que simplificam a criação de políticas comuns. Para obter mais informações, [consulte Azure Active Directory B2C: Políticas](/azure/active-directory-b2c/active-directory-b2c-reference-policies/)internas.

Quando você tiver concluído a configuração da política, deverá ter duas políticas no modo de exibição **fluxos de usuário (políticas)** no portal do Azure. A captura de tela a seguir demonstra duas políticas configuradas no portal do Azure:

![Duas políticas configuradas na exibição de fluxos de usuário do Azure (políticas)](azure-ad-b2c-images/azure-application-policies.png)

No projeto de exemplo, edite o arquivo **Constants.cs** para definir `policySignin` os `policyPassword` campos e para refletir os nomes que você escolheu durante a configuração da política:

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    static readonly string clientId = "1234abcd";
    static readonly string policySignin = "B2C_1_signupsignin1";
    static readonly string policyPassword = "B2C_1_passwordreset";
    ...
}
```

## <a name="use-the-microsoft-authentication-library-msal-for-authentication"></a>Usar a MSAL (biblioteca de autenticação da Microsoft) para autenticação

O pacote NuGet da MSAL (biblioteca de autenticação da Microsoft) deve ser adicionado ao projeto compartilhado, .NET Standard e aos projetos de plataforma em uma solução Xamarin. Forms. MSAL inclui uma `PublicClientApplicationBuilder` classe que constrói um objeto que adere `IPublicClientApplication` à interface. O MSAL utiliza `With` cláusulas para fornecer parâmetros adicionais ao construtor e aos métodos de autenticação.

No projeto de exemplo, o code-behind para **app. XAML** define propriedades estáticas `UIParent`nomeadas `AuthenticationClient` e e cria `AuthenticationClient` uma instância do objeto no construtor. A `WithIosKeychainSecurityGroup` cláusula fornece um nome de grupo de segurança para aplicativos Ios. A `WithB2CAuthority` cláusula fornece a **autoridade**padrão ou a política que será usada para autenticar usuários. O exemplo a seguir demonstra como criar uma `PublicClientApplication`instância de:

```csharp
public partial class App : Application
{
    public static IPublicClientApplication AuthenticationClient { get; private set; }

    public static object UIParent { get; set; } = null;

    public App()
    {
        InitializeComponent();

        AuthenticationClient = PublicClientApplicationBuilder.Create(Constants.ClientId)
            .WithIosKeychainSecurityGroup(Constants.IosKeychainSecurityGroups)
            .WithB2CAuthority(Constants.AuthoritySignin)
            .Build();

        MainPage = new NavigationPage(new LoginPage());
    }

    ...
```

O `OnAppearing` manipulador de eventos no código **LoginPage.XAML.cs** por trás `AcquireTokenSilentAsync` de chamadas para atualizar o token de autenticação para usuários que fizeram logon antes. O processo de autenticação redireciona para o `LogoutPage` se bem-sucedido e não faz nada em caso de falha. O exemplo a seguir mostra o processo de reautenticação `OnAppearing`silenciosa no:

```csharp
public partial class LoginPage : ContentPage
{
    ...

    protected override async void OnAppearing()
    {
        try
        {
            // Look for existing account
            IEnumerable<IAccount> accounts = await App.AuthenticationClient.GetAccountsAsync();

            AuthenticationResult result = await App.AuthenticationClient
                .AcquireTokenSilent(Constants.Scopes, accounts.FirstOrDefault())
                .ExecuteAsync();

            await Navigation.PushAsync(new LogoutPage(result));
        }
        catch
        {
            // Do nothing - the user isn't logged in
        }
        base.OnAppearing();
    }

    ...
}
```

O `OnLoginButtonClicked` manipulador de eventos (acionado quando o botão de logon é `AcquireTokenAsync`clicado) chama. A biblioteca MSAL abre automaticamente o navegador do dispositivo móvel e navega até a página de logon. A URL de entrada, chamada de **autoridade**, é uma combinação do nome do locatário e das políticas definidas no arquivo **Constants.cs** . Se o usuário escolher a opção esqueceu a senha, ele será retornado ao aplicativo com uma exceção, que iniciará a experiência de senha esquecida. O exemplo a seguir mostra o processo de autenticação:

```csharp
public partial class LoginPage : ContentPage
{
    ...

    async void OnLoginButtonClicked(object sender, EventArgs e)
    {
        AuthenticationResult result;
        try
        {
            result = await App.AuthenticationClient
                .AcquireTokenInteractive(Constants.Scopes)
                .WithPrompt(Prompt.SelectAccount)
                .WithParentActivityOrWindow(App.UIParent)
                .ExecuteAsync();
    
            await Navigation.PushAsync(new LogoutPage(result));
        }
        catch (MsalException ex)
        {
            if (ex.Message != null && ex.Message.Contains("AADB2C90118"))
            {
                result = await OnForgotPassword();
                await Navigation.PushAsync(new LogoutPage(result));
            }
            else if (ex.ErrorCode != "authentication_canceled")
            {
                await DisplayAlert("An error has occurred", "Exception message: " + ex.Message, "Dismiss");
            }
        }
    }

    ...
}
```

O `OnForgotPassword` método é semelhante ao processo de entrada, mas implementa uma política personalizada. `OnForgotPassword`usa uma sobrecarga diferente de `AcquireTokenAsync`, que permite que você forneça uma **autoridade**específica. O exemplo a seguir mostra como fornecer uma **autoridade** personalizada ao adquirir um token:

```csharp
public partial class LoginPage : ContentPage
{
    ...
    async Task<AuthenticationResult> OnForgotPassword()
    {
        try
        {
            return await App.AuthenticationClient
                .AcquireTokenInteractive(Constants.Scopes)
                .WithPrompt(Prompt.SelectAccount)
                .WithParentActivityOrWindow(App.UIParent)
                .WithB2CAuthority(Constants.AuthorityPasswordReset)
                .ExecuteAsync();
        }
        catch (MsalException)
        {
            // Do nothing - ErrorCode will be displayed in OnLoginButtonClicked
            return null;
        }
    }
}
```

A parte final da autenticação é o processo de saída. O `OnLogoutButtonClicked` método é chamado quando o usuário pressiona o botão sair. Ele executa um loop em todas as contas e garante que seus tokens foram invalidados. O exemplo a seguir demonstra a implementação de sair:

```csharp
public partial class LogoutPage : ContentPage
{
    ...
    async void OnLogoutButtonClicked(object sender, EventArgs e)
    {
        IEnumerable<IAccount> accounts = await App.AuthenticationClient.GetAccountsAsync();

        while (accounts.Any())
        {
            await App.AuthenticationClient.RemoveAsync(accounts.First());
            accounts = await App.AuthenticationClient.GetAccountsAsync();
        }

        await Navigation.PopAsync();
    }
}
```

### <a name="ios"></a>iOS

No iOS, o esquema de URL personalizado que foi registrado com Azure Active Directory B2C deve ser registrado em **info. plist**. MSAL espera que o esquema de URL obedeça a um padrão específico, descrito anteriormente em [registrar seu aplicativo móvel com Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c). A captura de tela a seguir mostra o esquema de URL personalizado em **info. plist**.

!["Registrando um esquema de URL personalizado no iOS"](azure-ad-b2c-images/customurl-ios.png)

O MSAL também exige direitos de conjunto de chaves no iOS, registrados no **Entitilements. plist**, conforme mostrado na seguinte captura de tela:

!["Configurando direitos de aplicativo no iOS"](azure-ad-b2c-images/entitlements-ios.png)

Quando o Azure Active Directory B2C conclui a solicitação de autorização, ele redireciona para a URL de redirecionamento registrado. O esquema de URL personalizado resulta no Ios iniciando o aplicativo móvel e passando a URL como um parâmetro de inicialização, onde ele é processado pela `OpenUrl` substituição da classe do `AppDelegate` aplicativo e retorna o controle da experiência para MSAL. A `OpenUrl` implementação é mostrada no exemplo de código a seguir:

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
            return base.OpenUrl(app, url, options);
        }
    }
}
```

### <a name="android"></a>Android

No Android, o esquema de URL personalizado que foi registrado com Azure Active Directory B2C deve ser registrado no **AndroidManifest. xml**. MSAL espera que o esquema de URL obedeça a um padrão específico, descrito anteriormente em [registrar seu aplicativo móvel com Azure Active Directory B2C](~/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c). O exemplo a seguir mostra o esquema de URL personalizado no **AndroidManifest. xml**.

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.xamarin.adb2cauthorization">
  <uses-sdk android:minSdkVersion="15" />
  <application android:label="ADB2CAuthorization">
    <activity android:name="microsoft.identity.client.BrowserTabActivity">
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <!-- example -->
        <!-- <data android:scheme="msalaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" android:host="auth" /> -->
        <data android:scheme="INSERT_URI_SCHEME_HERE" android:host="auth" />
      </intent-filter>
    </activity>"
  </application>
</manifest>
```

A `MainActivity` classe deve ser modificada para fornecer `UIParent` o objeto ao aplicativo durante a `OnCreate` chamada. Quando Azure Active Directory B2C conclui a solicitação de autorização, ela redireciona para o esquema de URL registrado do **AndroidManifest. xml**. O esquema de URI registrado resulta no Android chamando `OnActivityResult` o método com a URL como um parâmetro de inicialização, onde ele é processado `SetAuthenticationContinuationEventArgs` pelo método.

```csharp
public class MainActivity : FormsAppCompatActivity
{
    protected override void OnCreate(Bundle bundle)
    {
        TabLayoutResource = Resource.Layout.Tabbar;
        ToolbarResource = Resource.Layout.Toolbar;

        base.OnCreate(bundle);

        Forms.Init(this, bundle);
        LoadApplication(new App());
        App.UIParent = this;
    }

    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
    }
}
```

### <a name="universal-windows-platform"></a>Plataforma Universal do Windows

Nenhuma configuração adicional é necessária para usar o MSAL no Plataforma Universal do Windows

## <a name="run-the-project"></a>Executar o projeto

Execute o aplicativo em um dispositivo físico ou virtual. Tocar no botão de **logon** deve abrir o navegador e navegar até uma página na qual você pode entrar ou criar uma conta. Depois de concluir o processo de entrada, você deverá retornar à página de logout do aplicativo. A captura de tela a seguir mostra a entrada de logon do usuário em execução no Android e no iOS:

!["Tela de entrada do Azure ADB2C no Android e iOS"](azure-ad-b2c-images/login.png)

## <a name="related-links"></a>Links relacionados

- [AzureADB2CAuth (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-azureadb2cauth)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Biblioteca de autenticação da Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client)
- [Documentação da biblioteca de autenticação da Microsoft](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)
