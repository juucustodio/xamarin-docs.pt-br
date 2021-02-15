---
title: ID de toque e ID facial com Xamarin. iOS
description: Este documento fornece uma descrição de alto nível da autenticação biométrica no iOS.
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: profexorgeek
ms.author: jusjohns
ms.date: 12/16/2019
ms.openlocfilehash: a092b5f84ebf0481652f5093a4898e44a55e19f8
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93369552"
---
# <a name="use-touch-id-and-face-id-with-xamarinios"></a>Usar ID de toque e ID facial com Xamarin. iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/ios-samples/ios11-faceidsample/)

o iOS dá suporte a dois sistemas de autenticação biométrico:

1. A **ID de toque** usa um sensor de impressão digital no botão página inicial.
1. A **ID de face** usa sensores de câmera frontal para autenticar usuários com uma verificação facial.

A ID de toque foi introduzida no iOS 7 e na ID de face no iOS 11.

Esses sistemas de autenticação contam com um processador de segurança baseado em hardware chamado _enclave seguro_. O Secure enclave é responsável por criptografar representações matemáticas de dados de face e impressão digital e autenticar usuários usando essas informações. De acordo com a Apple, os dados de face e impressão digital não deixam o dispositivo e não são submetidos a backup no iCloud. Os aplicativos interagem com o enclave seguro por meio da API de _autenticação local_ e não podem recuperar dados de face ou impressão digital ou acessar diretamente o Secure enclave.

A ID de toque e a ID de face podem ser usadas por aplicativos para autenticar um usuário antes de fornecer acesso ao conteúdo protegido.

## <a name="local-authentication-context"></a>Contexto de autenticação local

A autenticação biométrica no iOS depende de um objeto de _contexto de autenticação local_ , que é uma instância da `LAContext` classe. A `LAContext` classe permite que você:

- Verifique a disponibilidade do hardware biométrico.
- Avalie as políticas de autenticação.
- Avaliar controles de acesso.
- Personalizar e exibir prompts de autenticação.
- Reutilizar ou invalidar um estado de autenticação.
- Gerenciar credenciais.

## <a name="detect-available-authentication-methods"></a>Detectar métodos de autenticação disponíveis

O projeto de exemplo inclui um `AuthenticationView` apoiado por um `AuthenticationViewController` . Essa classe substitui o `ViewWillAppear` método para detectar os métodos de autenticação disponíveis:

```csharp
partial class AuthenticationViewController: UIViewController
{
    // ...
    string BiometryType = "";

    public override void ViewWillAppear(bool animated)
    {
        base.ViewWillAppear(animated);
        unAuthenticatedLabel.Text = "";
    
        var context = new LAContext();
        var buttonText = "";

        // Is login with biometrics possible?
        if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, out var authError1))
        {
            // has Touch ID or Face ID
            if (UIDevice.CurrentDevice.CheckSystemVersion(11, 0))
            {
                context.LocalizedReason = "Authorize for access to secrets"; // iOS 11
                BiometryType = context.BiometryType == LABiometryType.TouchId ? "Touch ID" : "Face ID";
                buttonText = $"Login with {BiometryType}";
            }
            // No FaceID before iOS 11
            else
            {
                buttonText = $"Login with Touch ID";
            }
        }

        // Is pin login possible?
        else if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthentication, out var authError2))
        {
            buttonText = $"Login"; // with device PIN
            BiometryType = "Device PIN";
        }

        // Local authentication not possible
        else
        {
            // Application might choose to implement a custom username/password
            buttonText = "Use unsecured";
            BiometryType = "none";
        }
        AuthenticateButton.SetTitle(buttonText, UIControlState.Normal);
    }
}
```

O `ViewWillAppear` método é chamado quando a interface do usuário está prestes a ser exibida para o usuário. Esse método define uma nova instância do `LAContext` e usa o `CanEvaluatePolicy` método para determinar se a autenticação biométrica está habilitada. Nesse caso, ele verifica a versão do sistema e `BiometryType` enum para determinar quais opções biométricas estão disponíveis.

Se a autenticação biométrica não estiver habilitada, o aplicativo tentará fazer fallback para a autenticação de PIN. Se nenhuma autenticação biométrica nem de PIN estiver disponível, o proprietário do dispositivo não terá habilitado os recursos de segurança e o conteúdo não poderá ser protegido por meio da autenticação local.

## <a name="authenticate-a-user"></a>Autenticar um usuário

O `AuthenticationViewController` no projeto de exemplo inclui um `AuthenticateMe` método, que é responsável por autenticar o usuário:

```csharp
partial class AuthenticationViewController: UIViewController
{
    // ...
    string BiometryType = "";

    partial void AuthenticateMe(UIButton sender)
    {
        var context = new LAContext();
        NSError AuthError;
        var localizedReason = new NSString("To access secrets");
    
        // Because LocalAuthentication APIs have been extended over time,
        // you must check iOS version before setting some properties
        context.LocalizedFallbackTitle = "Fallback";
    
        if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
        {
            context.LocalizedCancelTitle = "Cancel";
        }
        if (UIDevice.CurrentDevice.CheckSystemVersion(11, 0))
        {
            context.LocalizedReason = "Authorize for access to secrets";
            BiometryType = context.BiometryType == LABiometryType.TouchId ? "TouchID" : "FaceID";
        }
    
        // Check if biometric authentication is possible
        if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, out AuthError))
        {
            replyHandler = new LAContextReplyHandler((success, error) =>
            {
                // This affects UI and must be run on the main thread
                this.InvokeOnMainThread(() =>
                {
                    if (success)
                    {
                        PerformSegue("AuthenticationSegue", this);
                    }
                    else
                    {
                        unAuthenticatedLabel.Text = $"{BiometryType} Authentication Failed";
                    }
                });
    
            });
            context.EvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, localizedReason, replyHandler);
        }

        // Fall back to PIN authentication
        else if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthentication, out AuthError))
        {
            replyHandler = new LAContextReplyHandler((success, error) =>
            {
                // This affects UI and must be run on the main thread
                this.InvokeOnMainThread(() =>
                {
                    if (success)
                    {
                        PerformSegue("AuthenticationSegue", this);
                    }
                    else
                    {
                        unAuthenticatedLabel.Text = "Device PIN Authentication Failed";
                        AuthenticateButton.Hidden = true;
                    }
                });
    
            });
            context.EvaluatePolicy(LAPolicy.DeviceOwnerAuthentication, localizedReason, replyHandler);
        }

        // User hasn't configured any authentication: show dialog with options
        else
        {
            unAuthenticatedLabel.Text = "No device auth configured";
            var okCancelAlertController = UIAlertController.Create("No authentication", "This device does't have authentication configured.", UIAlertControllerStyle.Alert);
            okCancelAlertController.AddAction(UIAlertAction.Create("Use unsecured", UIAlertActionStyle.Default, alert => PerformSegue("AuthenticationSegue", this)));
            okCancelAlertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, alert => Console.WriteLine("Cancel was clicked")));
            PresentViewController(okCancelAlertController, true, null);
        }
    } 
}
```

O `AuthenticateMe` método é chamado em resposta ao usuário tocando em um botão de **logon** . Um novo `LAContext` objeto é instanciado e a versão do dispositivo é verificada para determinar quais propriedades definir no contexto de autenticação local.

O `CanEvaluatePolicy` método é chamado para verificar se a autenticação biométrica está habilitada, retornar à autenticação de PIN, se possível, e finalmente oferecer um modo desprotegido se nenhuma autenticação estiver disponível. Se um método de autenticação estiver disponível, o `EvaluatePolicy` método será usado para mostrar a interface do usuário e concluir o processo de autenticação.

O projeto de exemplo contém dados fictícios e uma exibição para exibir os dados se a autenticação for bem-sucedida.

## <a name="related-links"></a>Links relacionados

- [Autenticação local usando a ID de toque ou a amostra de ID de face](/samples/xamarin/ios-samples/ios11-faceidsample/)
- [Sobre o Touch ID](https://support.apple.com/en-us/HT204587) em support.Apple.com
- [Sobre a ID de face](https://support.apple.com/en-us/HT208108) em support.Apple.com