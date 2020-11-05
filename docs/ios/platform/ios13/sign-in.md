---
title: Entre com a Apple no Xamarin. iOS
description: Entrar com a Apple fornece proteção de identidade ao usar serviços de autenticação de terceiros.
ms.prod: xamarin
ms.assetid: CDA59BBF-AAE1-4D4F-B4AE-DB37220D41EB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/10/2019
ms.openlocfilehash: 5cbe3f36d1aeb12be671b14a4f76c79764e814e6
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374999"
---
# <a name="sign-in-with-apple-in-xamarinios"></a>Entre com a Apple no Xamarin. iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/ios-samples/ios13-addingthesigninwithappleflowtoyourapp/)

Entrar com a Apple é um novo serviço que fornece proteção de identidade para usuários de serviços de autenticação de terceiros. A partir do iOS 13, a Apple exige que qualquer aplicativo novo usando serviços de autenticação de terceiros também deve fornecer entrada com a Apple. Os aplicativos existentes que estão sendo atualizados não precisam adicionar entrar com a Apple até abril de 2020.

Este documento apresenta como você pode adicionar a entrada com o Apple em aplicativos iOS 13.

## <a name="apple-developer-setup"></a>Instalação do desenvolvedor da Apple

Antes de criar e executar um aplicativo usando entrar com a Apple, você precisa concluir estas etapas. Em [certificados de desenvolvedor da Apple, identificadores & portal de perfis][5] :

1. Crie um novo identificador de **IDs de aplicativo** .
2. Defina uma descrição no campo **Descrição** .
3. Escolha uma ID de pacote **explícita** e defina `com.xamarin.AddingTheSignInWithAppleFlowToYourApp` no campo.
4. Habilite a **entrada com** o recurso Apple e registre a nova identidade.
5. Crie um novo perfil de provisionamento com a nova identidade.
6. Baixe e instale-o em seu dispositivo.
7. No Visual Studio, habilite a funcionalidade **entrar com a Apple** no arquivo **. plist de direitos** .

## <a name="check-sign-in-status"></a>Verificar status de entrada

Quando o aplicativo for iniciado, ou quando você precisar verificar o status de autenticação de um usuário, crie uma instância de `ASAuthorizationAppleIdProvider` e verifique o estado atual:

```csharp
var appleIdProvider = new ASAuthorizationAppleIdProvider ();
appleIdProvider.GetCredentialState (KeychainItem.CurrentUserIdentifier, (credentialState, error) => {
    switch (credentialState) {
    case ASAuthorizationAppleIdProviderCredentialState.Authorized:
        // The Apple ID credential is valid.
        break;
    case ASAuthorizationAppleIdProviderCredentialState.Revoked:
        // The Apple ID credential is revoked.
        break;
    case ASAuthorizationAppleIdProviderCredentialState.NotFound:
        // No credential was found, so show the sign-in UI.
        InvokeOnMainThread (() => {
            var storyboard = UIStoryboard.FromName ("Main", null);

            if (!(storyboard.InstantiateViewController (nameof (LoginViewController)) is LoginViewController viewController))
                return;

            viewController.ModalPresentationStyle = UIModalPresentationStyle.FormSheet;
            viewController.ModalInPresentation = true;
            Window?.RootViewController?.PresentViewController (viewController, true, null);
        });
        break;
    }
});
```

Nesse código, chamado durante `FinishedLaunching` no `AppDelegate.cs` , o aplicativo tratará quando um estado for `NotFound` e apresentará o `LoginViewController` para o usuário. Se o estado tiver retornado `Authorized` ou `Revoked` , uma ação diferente poderá ser apresentada ao usuário.

## <a name="a-loginviewcontroller-for-sign-in-with-apple"></a>Um LoginViewController para entrar com a Apple

O `UIViewController` que implementa a lógica de logon e oferece entrada com a Apple precisa implementar `IASAuthorizationControllerDelegate` e `IASAuthorizationControllerPresentationContextProviding` como no `LoginViewController` exemplo abaixo.

```csharp
public partial class LoginViewController : UIViewController, IASAuthorizationControllerDelegate, IASAuthorizationControllerPresentationContextProviding {
    public LoginViewController (IntPtr handle) : base (handle)
    {
    }

    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
        // Perform any additional setup after loading the view, typically from a nib.

        SetupProviderLoginView ();
    }

    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);

        PerformExistingAccountSetupFlows ();
    }

    void SetupProviderLoginView ()
    {
        var authorizationButton = new ASAuthorizationAppleIdButton (ASAuthorizationAppleIdButtonType.Default, ASAuthorizationAppleIdButtonStyle.White);
        authorizationButton.TouchUpInside += HandleAuthorizationAppleIDButtonPress;
        loginProviderStackView.AddArrangedSubview (authorizationButton);
    }

    // Prompts the user if an existing iCloud Keychain credential or Apple ID credential is found.
    void PerformExistingAccountSetupFlows ()
    {
        // Prepare requests for both Apple ID and password providers.
        ASAuthorizationRequest [] requests = {
            new ASAuthorizationAppleIdProvider ().CreateRequest (),
            new ASAuthorizationPasswordProvider ().CreateRequest ()
        };

        // Create an authorization controller with the given requests.
        var authorizationController = new ASAuthorizationController (requests);
        authorizationController.Delegate = this;
        authorizationController.PresentationContextProvider = this;
        authorizationController.PerformRequests ();
    }

    private void HandleAuthorizationAppleIDButtonPress (object sender, EventArgs e)
    {
        var appleIdProvider = new ASAuthorizationAppleIdProvider ();
        var request = appleIdProvider.CreateRequest ();
        request.RequestedScopes = new [] { ASAuthorizationScope.Email, ASAuthorizationScope.FullName };

        var authorizationController = new ASAuthorizationController (new [] { request });
        authorizationController.Delegate = this;
        authorizationController.PresentationContextProvider = this;
        authorizationController.PerformRequests ();
    }
}
```

![Animação do aplicativo de exemplo usando entrar com a Apple](sign-in-images/sign-in-flow.png)

Este código de exemplo verifica o status de logon atual no `PerformExistingAccountSetupFlows` e conecta-se ao modo de exibição atual como um delegado. Se uma credencial existente do conjunto de chaves do iCloud ou uma credencial de ID da Apple for encontrada, o usuário será solicitado a usá-la.

A Apple fornece `ASAuthorizationAppleIdButton` um botão especificamente para essa finalidade. Quando tocado, o botão irá disparar o fluxo de trabalho manipulado no método `HandleAuthorizationAppleIDButtonPress` .

## <a name="handling-authorization"></a>Manipulando a autorização

Em `IASAuthorizationController` implementar qualquer lógica personalizada para armazenar a conta do usuário. O exemplo a seguir armazena a conta do usuário no conjunto de chaves, o próprio serviço de armazenamento da Apple.

```csharp
#region IASAuthorizationController Delegate

[Export ("authorizationController:didCompleteWithAuthorization:")]
public void DidComplete (ASAuthorizationController controller, ASAuthorization authorization)
{
    if (authorization.GetCredential<ASAuthorizationAppleIdCredential> () is ASAuthorizationAppleIdCredential appleIdCredential) {
        var userIdentifier = appleIdCredential.User;
        var fullName = appleIdCredential.FullName;
        var email = appleIdCredential.Email;

        // Create an account in your system.
        // For the purpose of this demo app, store the userIdentifier in the keychain.
        try {
            new KeychainItem ("com.example.apple-samplecode.juice", "userIdentifier").SaveItem (userIdentifier);
        } catch (Exception) {
            Console.WriteLine ("Unable to save userIdentifier to keychain.");
        }

        // For the purpose of this demo app, show the Apple ID credential information in the ResultViewController.
        if (!(PresentingViewController is ResultViewController viewController))
            return;

        InvokeOnMainThread (() => {
            viewController.UserIdentifierText = userIdentifier;
            viewController.GivenNameText = fullName?.GivenName ?? "";
            viewController.FamilyNameText = fullName?.FamilyName ?? "";
            viewController.EmailText = email ?? "";

            DismissViewController (true, null);
        });
    } else if (authorization.GetCredential<ASPasswordCredential> () is ASPasswordCredential passwordCredential) {
        // Sign in using an existing iCloud Keychain credential.
        var username = passwordCredential.User;
        var password = passwordCredential.Password;

        // For the purpose of this demo app, show the password credential as an alert.
        InvokeOnMainThread (() => {
            var message = $"The app has received your selected credential from the keychain. \n\n Username: {username}\n Password: {password}";
            var alertController = UIAlertController.Create ("Keychain Credential Received", message, UIAlertControllerStyle.Alert);
            alertController.AddAction (UIAlertAction.Create ("Dismiss", UIAlertActionStyle.Cancel, null));

            PresentViewController (alertController, true, null);
        });
    }
}

[Export ("authorizationController:didCompleteWithError:")]
public void DidComplete (ASAuthorizationController controller, NSError error)
{
    Console.WriteLine (error);
}

#endregion
```

## <a name="authorization-controller"></a>Controlador de autorização

A parte final dessa implementação é o `ASAuthorizationController` que gerencia as solicitações de autorização para o provedor.

```csharp
#region IASAuthorizationControllerPresentation Context Providing

public UIWindow GetPresentationAnchor (ASAuthorizationController controller) => View.Window;

#endregion
```

## <a name="related-links"></a>Links relacionados

* [Entre com as diretrizes da Apple](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/)
* [Entre com a autorização da Apple.][2]
* [WWDC 2019 sessão 706: introdução à entrada com a Apple.][3]
* [Entrar com a Apple para Xamarin. Forms][4]

[1]: https://developer.apple.com/documentation/authenticationservices/adding_the_sign_in_with_apple_flow_to_your_app
[2]: https://developer.apple.com/documentation/bundleresources/entitlements/com_apple_developer_applesignin
[3]: https://developer.apple.com/videos/play/wwdc19/706/
[4]: ~/xamarin-forms/platform/sign-in-with-apple/setup.md
[5]: https://developer.apple.com/account/resources/identifiers/list