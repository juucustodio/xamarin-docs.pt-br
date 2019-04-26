---
title: Trabalhando com alertas de tvOS no Xamarin
description: Este documento descreve como trabalhar com alertas de tvOS no Xamarin. Ele aborda exibindo um alerta, adicionando campos de texto e uma classe auxiliar.
ms.prod: xamarin
ms.assetid: F969BB28-FF2C-4A7D-88CA-F8076AD48538
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 13c5ae3fac76ec1ec1a0ade135d5919403066226
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61409781"
---
# <a name="working-with-tvos-alerts-in-xamarin"></a>Trabalhando com alertas de tvOS no Xamarin

_Este artigo aborda o trabalho com UIAlertController para exibir uma mensagem de alerta para o usuário no xamarin. tvos._

Se você precisar obter a atenção do usuário do tvOS ou peça permissão para executar uma ação destrutiva (como excluir um arquivo), você pode apresentar uma mensagem de alerta usando o `UIAlertViewController`:

[![](alerts-images/alert01.png "Um exemplo UIAlertViewController")](alerts-images/alert01.png#lightbox)

Se a adição ao exibir uma mensagem, você pode adicionar botões e campos de texto a um alerta para permitir que o usuário responder às ações e fornecer comentários.

<a name="About-Alerts" />

## <a name="about-alerts"></a>Sobre alertas

Como mencionado acima, os alertas são usados para obter a atenção do usuário e informá-los do estado do seu aplicativo ou solicitação de comentários. Alertas devem apresentar um título, pode, opcionalmente, ter uma mensagem e um ou mais botões ou campos de texto.

[![](alerts-images/alert04.png "Um exemplo de alerta")](alerts-images/alert04.png#lightbox)

Apple tem as sugestões a seguir para trabalhar com alertas:

- **Use alertas com moderação** -alertas e afetar o fluxo do usuário com o aplicativo interromper a experiência do usuário e como tal, só deve ser usados em situações importantes, como notificações de erro, compras no aplicativo e as ações destrutivas. 
- **Fornece opções úteis** - se o alerta apresenta opções para o usuário, sua deve garantir que cada opção oferece informações essenciais e fornece ações úteis para o usuário.

<a name="Alert-Titles-and-Messages" />

### <a name="alert-titles-and-messages"></a>Títulos e mensagens de alerta

Apple tem as seguintes sugestões para apresentar o título e mensagem opcional de um alerta:

- **Use títulos Multiword** -título de um alerta deve obter o ponto da situação entre claramente permanecendo ainda simples. Um título de palavra única raramente fornece informações suficientes.
- **Use títulos descritivos que não exigem uma mensagem** – sempre que possível, considere tornar o título do alerta descritivo suficiente que o texto da mensagem opcional não é necessária. 
- **Verifique a mensagem uma breve, a frase completa** - se a mensagem opcional é necessária para obter o ponto do alerta entre, mantê-lo o mais simples possível e torná-lo uma frase completa com a capitalização correta e pontuação.

<a name="Alert-Buttons" />

### <a name="alert-buttons"></a>Botões de alerta

Apple tem a seguinte sugestão para adicionar botões a um alerta:

- **Limite de dois botões** – sempre que possível, limite o alerta a um máximo de dois botões. Os alertas de botão único fornecem informações, mas nenhuma ação. Dois alertas de botão fornecem um simples Sim/não choice da ação.
- **Use Succinct, títulos de botão lógico** -simples, um ou dois títulos de botão de palavra que descrevem claramente a ação do botão funcionam melhor. Para obter mais informações, consulte nosso [trabalhando com botões](~/ios/tvos/user-interface/buttons.md) documentação.
- **Claramente marca botões destrutiva** - para botões que executam uma ação destrutiva (como excluir um arquivo) claramente marcá-los com o `UIAlertActionStyle.Destructive` estilo.

<a name="Displaying-an-Alert" />

## <a name="displaying-an-alert"></a>Exibindo um alerta

Para exibir um alerta, você pode criar uma instância do `UIAlertViewController` e configurá-lo a adicionar ações (botões) e selecionando o estilo do alerta. Por exemplo, o código a seguir exibe um alerta de Okey/Cancelar:

```csharp
const string title = "A Short Title is Best";
const string message = "A message should be a short, complete sentence.";
const string acceptButtonTitle = "OK";
const string cancelButtonTitle = "Cancel";
const string deleteButtonTitle = "Delete";
...
        
var alertController = UIAlertController.Create (title, message, UIAlertControllerStyle.Alert);

// Create the action.
var acceptAction = UIAlertAction.Create (acceptButtonTitle, UIAlertActionStyle.Default, _ => 
    Console.WriteLine ("The \"OK/Cancel\" alert's other action occurred.")
);

var cancelAction = UIAlertAction.Create (cancelButtonTitle, UIAlertActionStyle.Cancel, _ => 
    Console.WriteLine ("The \"OK/Cancel\" alert's other action occurred.")
);

// Add the actions.
alertController.AddAction (acceptAction);
alertController.AddAction (cancelAction);
PresentViewController (alertController, true, null);
```

Vamos dar uma olhada neste código detalhadamente. Primeiro, criamos um novo alerta com o título especificado e a mensagem:

```csharp
UIAlertController.Create (title, message, UIAlertControllerStyle.Alert)
```

Em seguida, para cada botão que desejamos exibir no alerta, criamos uma ação definindo o título do botão, seu estilo e a ação que desejamos assumir se o botão for pressionado:

```csharp
UIAlertAction.Create ("Button Title", UIAlertActionStyle.Default, _ => 
    // Do something when the button is pressed
    ...
);
```

O `UIAlertActionStyle` enum permitem que você defina o estilo do botão como um dos seguintes:

- **Padrão** -botão será o botão padrão selecionado quando o alerta é exibido.
- **Cancelar** -o botão é o botão Cancelar para o alerta.
- **Destrutiva** -realça o botão como uma ação destrutiva, como a exclusão de um arquivo. Atualmente, tvOS renderiza o botão destrutivo com um plano de fundo vermelho.

O `AddAction` método adiciona a ação em questão para o `UIAlertViewController` e, finalmente, o `PresentViewController (alertController, true, null)` método exibe o alerta especificado ao usuário.

<a name="Adding-Text-Fields" />

## <a name="adding-text-fields"></a>Adicionando campos de texto

Além de adicionar ações (botões) para o alerta, você pode adicionar campos de texto para o alerta para permitir que o usuário preencha informações como as IDs de usuário e senhas:

[![](alerts-images/alert02.png "Campo de texto em um alerta")](alerts-images/alert02.png#lightbox)

Se o usuário selecionar o campo de texto, o teclado de tvOS padrão será exibido, permitindo que eles a inserir um valor para o campo:

[![](alerts-images/alert03.png "Inserindo texto")](alerts-images/alert03.png#lightbox)

O código a seguir exibe um alerta de Okey/Cancelar com um único campo de texto para inserir um valor:

```csharp
UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);
UITextField field = null;

// Add and configure text field
alert.AddTextField ((textField) => {
    // Save the field
    field = textField;

    // Initialize field
    field.Placeholder = placeholder;
    field.Text = text;
    field.AutocorrectionType = UITextAutocorrectionType.No;
    field.KeyboardType = UIKeyboardType.Default;
    field.ReturnKeyType = UIReturnKeyType.Done;
    field.ClearButtonMode = UITextFieldViewMode.WhileEditing;

});

// Add cancel button
alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
    // User canceled, do something
    ...
}));

// Add ok button
alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
    // User selected ok, do something
    ...
}));

// Display the alert
controller.PresentViewController(alert,true,null);
```

O `AddTextField` método adiciona um novo campo de texto para o alerta que você pode configurar, em seguida, definindo as propriedades, como o espaço reservado para texto (o texto que aparece quando o campo estiver vazio), o valor de texto padrão e o tipo de teclado. Por exemplo:

```csharp
// Initialize field
field.Placeholder = placeholder;
field.Text = text;
field.AutocorrectionType = UITextAutocorrectionType.No;
field.KeyboardType = UIKeyboardType.Default;
field.ReturnKeyType = UIReturnKeyType.Done;
field.ClearButtonMode = UITextFieldViewMode.WhileEditing;
```

Para que podemos pode atuar no valor do campo de texto, podemos também são salvar uma cópia do usando o seguinte código:

```csharp
UITextField field = null;
...

// Add and configure text field
alert.AddTextField ((textField) => {
    // Save the field
    field = textField;
    ...
});
```

Depois que o usuário inseriu um valor no campo de texto, podemos usar o `field` variável para acessar esse valor.

<a name="Alert-View-Controller-Helper-Class" />

## <a name="alert-view-controller-helper-class"></a>Classe de auxiliar de controlador de exibição de alerta

Como exibir tipos simples e comuns de alertas usando `UIAlertViewController` pode resultar em uma grande quantidade de código duplicado, você pode usar uma classe auxiliar para reduzir a quantidade de código repetitivo. Por exemplo:

```csharp
using System;
using Foundation;
using UIKit;
using System.CodeDom.Compiler;

namespace UIKit
{
    /// <summary>
    /// Alert view controller is a reusable helper class that makes working with <c>UIAlertViewController</c> alerts
    /// easier in a tvOS app.
    /// </summary>
    public class AlertViewController
    {
        #region Static Methods
        public static UIAlertController PresentOKAlert(string title, string description, UIViewController controller) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Configure the alert
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(action) => {}));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentOKCancelAlert(string title, string description, UIViewController controller, AlertOKCancelDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false);
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
                // Any action?
                if (action!=null) {
                    action(true);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentDestructiveAlert(string title, string description, string destructiveAction, UIViewController controller, AlertOKCancelDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false);
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create(destructiveAction,UIAlertActionStyle.Destructive,(actionOK) => {
                // Any action?
                if (action!=null) {
                    action(true);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }

        public static UIAlertController PresentTextInputAlert(string title, string description, string placeholder, string text, UIViewController controller, AlertTextInputDelegate action) {
            // No, inform the user that they must create a home first
            UIAlertController alert = UIAlertController.Create(title, description, UIAlertControllerStyle.Alert);
            UITextField field = null;

            // Add and configure text field
            alert.AddTextField ((textField) => {
                // Save the field
                field = textField;

                // Initialize field
                field.Placeholder = placeholder;
                field.Text = text;
                field.AutocorrectionType = UITextAutocorrectionType.No;
                field.KeyboardType = UIKeyboardType.Default;
                field.ReturnKeyType = UIReturnKeyType.Done;
                field.ClearButtonMode = UITextFieldViewMode.WhileEditing;

            });

            // Add cancel button
            alert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel,(actionCancel) => {
                // Any action?
                if (action!=null) {
                    action(false,"");
                }
            }));

            // Add ok button
            alert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default,(actionOK) => {
                // Any action?
                if (action!=null && field !=null) {
                    action(true, field.Text);
                }
            }));

            // Display the alert
            controller.PresentViewController(alert,true,null);

            // Return created controller
            return alert;
        }
        #endregion

        #region Delegates
        public delegate void AlertOKCancelDelegate(bool OK);
        public delegate void AlertTextInputDelegate(bool OK, string text);
        #endregion
    }
}
```

O uso dessa classe, exibir e responder aos alertas simples podem ser feitos da seguinte maneira:

```csharp
#region Custom Actions
partial void DisplayDestructiveAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentDestructiveAlert("A Short Title is Best","The message should be a short, complete sentence.","Delete",this, (ok) => {
        Console.WriteLine("Destructive Alert: The user selected {0}",ok);
    });
}

partial void DisplayOkCancelAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentOKCancelAlert("A Short Title is Best","The message should be a short, complete sentence.",this, (ok) => {
        Console.WriteLine("OK/Cancel Alert: The user selected {0}",ok);
    });
}

partial void DisplaySimpleAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentOKAlert("A Short Title is Best","The message should be a short, complete sentence.",this);
}

partial void DisplayTextInputAlert (Foundation.NSObject sender) {
    // User helper class to present alert
    AlertViewController.PresentTextInputAlert("A Short Title is Best","The message should be a short, complete sentence.","placeholder", "", this, (ok, text) => {
        Console.WriteLine("Text Input Alert: The user selected {0} and entered `{1}`",ok,text);
    });
}
#endregion
```


<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo cobriu trabalhar com `UIAlertController` para exibir uma mensagem de alerta para o usuário no xamarin. tvos. Primeiro, ele mostrou como exibir um alerta simples e adicione os botões. Em seguida, ele mostrou como adicionar campos de texto a um alerta. Por fim, ele mostrou como usar uma classe auxiliar para reduzir a quantidade de código repetitivo necessária para exibir um alerta.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de Interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
