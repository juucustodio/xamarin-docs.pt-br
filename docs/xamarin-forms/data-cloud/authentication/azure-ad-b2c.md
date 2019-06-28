---
title: Autenticar usuários com o Azure Active Directory B2C
description: B2C de diretório Active Directory do Azure fornece gerenciamento de identidades de nuvem para aplicativos móveis e web voltados ao consumidor. Este artigo mostra como usar o Azure Active Directory B2C para integrar o gerenciamento de identidade em um aplicativo móvel com a biblioteca de autenticação da Microsoft.
ms.prod: xamarin
ms.assetid: B0A5DB65-0585-4A00-B908-22CCC286E6B6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/17/2019
ms.openlocfilehash: 06f5716c8decb21de39fd46abe734b5fdcd6bd43
ms.sourcegitcommit: 0f78ec17210b915b43ddab75937de8063e472c70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2019
ms.locfileid: "67417944"
---
# <a name="authenticate-users-with-azure-active-directory-b2c"></a>Autenticar usuários com o Azure Active Directory B2C

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)

_B2C de diretório Active Directory do Azure fornece gerenciamento de identidades de nuvem para aplicativos móveis e web voltados ao consumidor. Este artigo mostra como usar o Azure Active Directory B2C para integrar o gerenciamento de identidade em um aplicativo móvel com a biblioteca de autenticação da Microsoft._

## <a name="overview"></a>Visão geral

Azure Active Directory B2C de diretório (ADB2C) é um serviço de gerenciamento de identidade para aplicativos voltados ao consumidor. Ele permite que os usuários façam logon seu aplicativo usando suas contas sociais existentes ou credenciais personalizadas, como email ou nome de usuário e senha. Contas de credenciais personalizado são denominadas _local_ contas.

O processo para integrar o serviço de gerenciamento de identidade do Azure Active Directory B2C em um aplicativo móvel é da seguinte maneira:

1. Criar um locatário do Azure Active Directory B2C
1. Registrar seu aplicativo móvel com o locatário do Azure Active Directory B2C
1. Criar políticas para inscrição e entrar e se esqueceu de fluxos de senha do usuário
1. Use a biblioteca de autenticação Microsoft (MSAL) para iniciar um fluxo de trabalho de autenticação com seu locatário do Azure Active Directory B2C.

> [!NOTE]
> B2C de diretório Active Directory do Azure dá suporte a vários provedores de identidade, incluindo a Microsoft, GitHub, Facebook, Twitter e muito mais. Para obter mais informações sobre os recursos do Azure Active Directory B2C, consulte [documentação do Azure Active Directory B2C](/azure/active-directory-b2c/).
>
> Biblioteca de autenticação da Microsoft dá suporte a várias arquiteturas de aplicativos e plataformas. Para obter informações sobre os recursos da MSAL, consulte [biblioteca de autenticação da Microsoft](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) no GitHub.

## <a name="configure-an-azure-active-directory-b2c-tenant"></a>Configurar um locatário do Azure Active Directory B2C

Para executar o projeto de exemplo, você deve criar um locatário do Azure Active Directory B2C. Para obter mais informações, consulte [criar um locatário do Azure Active Directory B2C no portal do Azure](/azure/active-directory-b2c/active-directory-b2c-get-started/).

Depois de criar um locatário, será necessário o **nome do locatário** e **ID do locatário** para configurar o aplicativo móvel. O nome e ID de locatário são definidos pelo domínio gerado quando você criou a URL do locatário. Se for sua URL de locatário gerada `https://contoso20190410tenant.onmicrosoft.com/` as **ID do locatário** é `contoso20190410tenant.onmicrosoft.com` e o **nome do locatário** é `contoso20190410tenant`. Localizar o domínio de locatário no portal do Azure clicando o **filtro de diretório e assinatura** no menu superior. Captura de tela a seguir mostra o diretório do Azure e o botão de filtro de assinatura e o domínio de locatário:

[![Nome do locatário em que a exibição de filtro de diretório e assinatura do Azure](azure-ad-b2c-images/azure-tenant-name-cropped.png)](azure-ad-b2c-images/azure-tenant-name.png#lightbox)

No projeto de exemplo, edite o **Constants.cs** para definir o `tenantName` e `tenantId` campos. O código a seguir mostra como esses valores devem ser definidos se seu domínio de locatário é `https://contoso20190410tenant.onmicrosoft.com/`, substitua esses valores com os valores do seu portal:

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    ...
}
```

## <a name="register-your-mobile-application-with-azure-active-directory-b2c"></a>Registrar seu aplicativo móvel com o Azure Active Directory B2C

Um aplicativo móvel deve ser registrado com o locatário antes que ele pode se conectar e autenticar usuários. O processo de registro atribui uma única **ID do aplicativo** para o aplicativo e um **URL de redirecionamento** que direciona as respostas de volta para o aplicativo após a autenticação. Para obter mais informações, consulte [do Azure Active Directory B2C: Registrar seu aplicativo](/azure/active-directory-b2c/active-directory-b2c-app-registration/). Você precisará saber o **ID do aplicativo** atribuído ao seu aplicativo, que é listado após o nome do aplicativo no modo de exibição de propriedades. Captura de tela a seguir mostra onde encontrar a ID do aplicativo:

[![ID do aplicativo no modo de exibição de propriedades de aplicativo do Azure](azure-ad-b2c-images/azure-application-id-cropped.png)](azure-ad-b2c-images/azure-application-id.png#lightbox)

Biblioteca de autenticação da Microsoft espera que o **URL de redirecionamento** para seu aplicativo para ser seu **ID do aplicativo** prefixada com o texto "msal" e seguido por um ponto de extremidade chamado "autenticação". Se a ID do aplicativo for "1234abcd", a URL completa deve ser `msal1234abcd://auth`. Certifique-se de que seu aplicativo tiver habilitado as **Native client** definir e criar um **URI de redirecionamento personalizado** usando a ID do aplicativo, conforme mostrado na seguinte captura de tela:

![URI de redirecionamento personalizado na exibição de propriedades de aplicativo do Azure](azure-ad-b2c-images/azure-redirect-uri.png)

A URL será usada posteriormente no Android **Applicationmanifest** e do iOS **Info. plist**.

No projeto de exemplo, edite o **Constants.cs** para definir o `clientId` campo para sua **ID do aplicativo**. O código a seguir mostra como esse valor deve ser definido se a ID do aplicativo é `1234abcd`:

```csharp
public static class Constants
{
    static readonly string tenantName = "contoso20190410tenant";
    static readonly string tenantId = "contoso20190410tenant.onmicrosoft.com";
    static readonly string clientId = "1234abcd";
    ...
}
```

## <a name="create-sign-up-and-sign-in-policies-and-forgot-password-policies"></a>Criar políticas de inscrição e entrada e se esqueceu de políticas de senha

Uma política é uma experiência que os usuários passam para concluir uma tarefa, como criar uma conta ou redefinir uma senha. Uma política também especifica o conteúdo dos tokens que o aplicativo recebe quando o usuário retornar a experiência do. Você deve definir políticas para ambas as conta de inscrição e entrada e redefinição de senha. O Azure tem políticas internas que simplificam a criação de políticas comuns. Para obter mais informações, consulte [do Azure Active Directory B2C: Políticas internas](/azure/active-directory-b2c/active-directory-b2c-reference-policies/).

Quando você concluiu a configuração de política, você deve ter duas políticas na **fluxos de usuário (diretivas)** exibição no portal do Azure. Captura de tela a seguir demonstra duas políticas configuradas no portal do Azure:

![Exibir duas políticas configuradas nos fluxos de usuário do Azure (políticas)](azure-ad-b2c-images/azure-application-policies.png)

No projeto de exemplo, edite o **Constants.cs** para definir o `policySignin` e `policyPassword` campos para refletir os nomes que você escolheu durante a configuração de política:

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

## <a name="use-the-microsoft-authentication-library-msal-for-authentication"></a>Usar a biblioteca de autenticação Microsoft (MSAL) para autenticação

O pacote NuGet da biblioteca de autenticação da Microsoft (MSAL) deve ser adicionado a compartilhado, projeto .NET Standard e os projetos de plataforma em uma solução do xamarin. Forms. MSAL inclui um `PublicClientApplicationBuilder` classe que constrói um objeto segui-las a `IPublicClientApplication` interface. Utiliza a MSAL `With` cláusulas para fornecer parâmetros adicionais para os métodos construtor e autenticação.

No projeto de exemplo, o code-behind para **App. XAML** define as propriedades estáticas chamadas `AuthenticationClient` e `UIParent`e instancia o `AuthenticationClient` objeto no construtor. O `WithIosKeychainSecurityGroup` cláusula fornece um nome de grupo de segurança para aplicativos iOS. O `WithB2CAuthority` cláusula fornece o padrão **autoridade**, ou a política, que será usada para autenticar usuários. O exemplo a seguir demonstra como criar uma instância de `PublicClientApplication`:

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

O `OnAppearing` manipulador de eventos na **LoginPage.xaml.cs** code-behind chamadas `AcquireTokenSilentAsync` para o token de autenticação para usuários que fizeram logon antes de atualizar. O processo de autenticação Redirecione para o `LogoutPage` se for bem-sucedido e não faz nada em caso de falha. O exemplo a seguir mostra o processo de reautenticação silenciosa em `OnAppearing`:

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

O `OnLoginButtonClicked` manipulador de eventos (acionado quando se clica no botão de logon) chamadas `AcquireTokenAsync`. A biblioteca MSAL automaticamente abre o navegador de dispositivo móvel e navega para a página de logon. A URL de entrada, chamado de um **autoridade**, é uma combinação do nome do locatário e as diretivas definidas na **Constants.cs** arquivo. Se o usuário escolhe o esqueci a opção de senha, eles são retornados para o aplicativo com uma exceção, que inicia o esqueci a experiência de senha. O exemplo a seguir mostra o processo de autenticação:

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

O `OnForgotPassword` método é semelhante ao processo de entrada, mas implementa uma política personalizada. `OnForgotPassword` usa uma sobrecarga diferente `AcquireTokenAsync`, que permite que você forneça um determinado **autoridade**. O exemplo a seguir mostra como fornecer um personalizado **autoridade** ao adquirir um token:

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

A parte final de autenticação é o processo de saída. O `OnLogoutButtonClicked` método é chamado quando o usuário pressiona o botão de sair. Ele executa um loop em todas as contas e garante que seus tokens foram invalidados. O exemplo a seguir demonstra a implementação de saída:

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

No iOS, o esquema de URL personalizado que foi registrado com o Azure Active Directory B2C deve ser registrado no **Info. plist**. MSAL espera que o esquema de URL aderir a um padrão específico, descrito anteriormente [registrar seu aplicativo móvel com o Azure Active Directory B2C](/docs/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c). Captura de tela a seguir mostra o esquema de URL personalizado na **Info. plist**.

!["Registrando um esquema de URL personalizado no iOS"](azure-ad-b2c-images/customurl-ios.png)

A MSAL também exige direitos chaves no iOS, registrado na **Entitilements.plist**, conforme mostrado na seguinte captura de tela:

!["Definindo direitos de aplicativo no iOS"](azure-ad-b2c-images/entitlements-ios.png)

Quando o Azure Active Directory B2C conclui a solicitação de autorização, ele redireciona para a URL de redirecionamento registrado. O esquema de URL personalizado resulta em iOS inicializar o aplicativo móvel e passando a URL como um parâmetro de inicialização, onde ela é processada pelo `OpenUrl` substituir da caixa de diálogo `AppDelegate` classe e retorna o controle da experiência a MSAL. O `OpenUrl` implementação é mostrada no exemplo de código a seguir:

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

No Android, o esquema de URL personalizado que foi registrado com o Azure Active Directory B2C deve ser registrado na **androidmanifest. XML**. MSAL espera que o esquema de URL aderir a um padrão específico, descrito anteriormente [registrar seu aplicativo móvel com o Azure Active Directory B2C](/docs/xamarin-forms/data-cloud/authentication/azure-ad-b2c.md#register-your-mobile-application-with-azure-active-directory-b2c). O exemplo a seguir mostra o esquema de URL personalizado de **androidmanifest. XML**.

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
        <data android:scheme="INSERT_URI_SCHEME_HERE" android:host="auth" />"
      </intent-filter>
    </activity>"
  </application>
</manifest>
```

O `MainActivity` classe deve ser modificado para fornecer o `UIParent` objeto para o aplicativo durante o `OnCreate` chamar. Quando o Azure Active Directory B2C conclui a solicitação de autorização, ele redireciona para o esquema de URL registrado do **androidmanifest. XML**. O esquema de URI registrado resulta no Android que chama o `OnActivityResult` método com a URL como um parâmetro de inicialização, onde ela é processada pelo `SetAuthenticationContinuationEventArgs` método.

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

Nenhuma configuração adicional é necessária para usar a MSAL na plataforma Universal do Windows

## <a name="run-the-project"></a>Executar o projeto

Execute o aplicativo em um dispositivo físico ou virtual. Tocar o **Login** botão deve abrir o navegador e navegue até uma página onde você pode entrar ou criar uma conta. Depois de concluir o processo de entrada, você deve ser retornado para a página de logoff do aplicativo. Captura de tela a seguir mostra a entrada do usuário na tela em execução no Android e iOS:

!["Azure ADB2C tela de entrada no Android e iOS"](azure-ad-b2c-images/login.png)

## <a name="related-links"></a>Links relacionados

- [AzureADB2CAuth (amostra)](https://developer.xamarin.com/samples/xamarin-forms/WebServices/AzureADB2CAuth/)
- [Azure Active Directory B2C](/azure/active-directory-b2c/)
- [Biblioteca de autenticação da Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client)
- [Documentação da biblioteca de autenticação da Microsoft](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)
