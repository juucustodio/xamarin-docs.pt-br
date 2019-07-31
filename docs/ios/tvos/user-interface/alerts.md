---
title: Trabalhando com alertas do tvOS no Xamarin
description: Este documento descreve como trabalhar com alertas do tvOS no Xamarin. Ele aborda a exibição de um alerta, a adição de campos de texto e uma classe auxiliar.
ms.prod: xamarin
ms.assetid: F969BB28-FF2C-4A7D-88CA-F8076AD48538
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 3f1761df5c2b638c5777e6384f4c0c06e9b3071f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657297"
---
# <a name="working-with-tvos-alerts-in-xamarin"></a>Trabalhando com alertas do tvOS no Xamarin

_Este artigo aborda como trabalhar com UIAlertController para exibir uma mensagem de alerta para o usuário no Xamarin. tvOS._

Se você precisar obter a atenção do usuário tvOS ou pedir permissão para executar uma ação destrutiva (como excluir um arquivo), poderá apresentar uma mensagem de `UIAlertViewController`alerta usando:

[![](alerts-images/alert01.png "Um exemplo de UIAlertViewController")](alerts-images/alert01.png#lightbox)

Se a adição de uma mensagem for adicionada, você poderá adicionar botões e campos de texto a um alerta para permitir que o usuário responda às ações e forneça comentários.

<a name="About-Alerts" />

## <a name="about-alerts"></a>Sobre alertas

Conforme mencionado acima, os alertas são usados para obter a atenção do usuário e informá-los do estado do seu aplicativo ou de comentários de solicitação. Os alertas devem apresentar um título, opcionalmente, podem ter uma mensagem e um ou mais botões ou campos de texto.

[![](alerts-images/alert04.png "Um alerta de exemplo")](alerts-images/alert04.png#lightbox)

A Apple tem as seguintes sugestões para trabalhar com alertas:

- **Usar alertas** com moderação-alertas interrompe o fluxo do usuário com o aplicativo e interrompe a experiência do usuário e, como tal, deve ser usado apenas para situações importantes, como notificações de erro, compras no aplicativo e ações destrutivas. 
- **Fornece opções úteis** – se o alerta apresentar opções para o usuário, você deve garantir que cada opção ofereça informações críticas e forneça ações úteis para o usuário tomar.

<a name="Alert-Titles-and-Messages" />

### <a name="alert-titles-and-messages"></a>Títulos e mensagens de alerta

A Apple tem as seguintes sugestões para apresentar o título e a mensagem opcional de um alerta:

- **Usar títulos** de várias palavras – o título de um alerta deve obter o ponto da situação com clareza, embora ainda permaneça simples. Um título de palavra única raramente fornece informações suficientes.
- **Use títulos descritivos que não exigem uma mensagem** -sempre que possível, considere tornar o título do alerta descritivo o suficiente para que o texto opcional da mensagem não seja necessário. 
- **Transforme a mensagem uma frase curta e completa** -se a mensagem opcional for necessária para obter o ponto do alerta, mantenha-a tão simples quanto possível e torne-a uma frase completa com maiúsculas e minúsculas corretas.

<a name="Alert-Buttons" />

### <a name="alert-buttons"></a>Botões de alerta

A Apple tem a seguinte sugestão para adicionar botões a um alerta:

- **Limite para dois botões** – sempre que possível, limite o alerta a um máximo de dois botões. Alertas de botão único fornecem informações, mas nenhuma ação. Dois alertas de botão fornecem uma opção simples de ação Sim/não.
- **Use os títulos de botão** de redescrição e lógica – um para dois títulos de botão do Word que descrevem claramente a ação do botão funcionar melhor. Para obter mais informações, consulte [a documentação trabalhando com botões](~/ios/tvos/user-interface/buttons.md) .
- **Marque claramente os botões destrutivos** – para botões que executam uma ação destrutiva (como excluir um arquivo) claramente marcá- `UIAlertActionStyle.Destructive` los com o estilo.

<a name="Displaying-an-Alert" />

## <a name="displaying-an-alert"></a>Exibindo um alerta

Para exibir um alerta, crie uma instância do e configure `UIAlertViewController` -a adicionando ações (botões) e selecionando o estilo do alerta. Por exemplo, o código a seguir exibe um alerta OK/cancelar:

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

Vamos dar uma olhada nesse código detalhadamente. Primeiro, criamos um novo alerta com o título e a mensagem fornecidos:

```csharp
UIAlertController.Create (title, message, UIAlertControllerStyle.Alert)
```

Em seguida, para cada botão que desejamos exibir no alerta, criamos uma ação que define o título do botão, seu estilo e a ação que desejamos tomar se o botão for pressionado:

```csharp
UIAlertAction.Create ("Button Title", UIAlertActionStyle.Default, _ => 
    // Do something when the button is pressed
    ...
);
```

A `UIAlertActionStyle` enumeração permite que você defina o estilo do botão como um dos seguintes:

- **Padrão** -o botão será o botão padrão selecionado quando o alerta for exibido.
- **Cancelar** -o botão é o botão Cancelar do alerta.
- **Destrutivo** – realça o botão como uma ação destrutiva, como excluir um arquivo. Atualmente, tvOS renderiza o botão destrutivo com um plano de fundo vermelho.

O `AddAction` método adiciona a ação fornecida `UIAlertViewController` ao e, por fim `PresentViewController (alertController, true, null)` , o método exibe o alerta fornecido ao usuário.

<a name="Adding-Text-Fields" />

## <a name="adding-text-fields"></a>Adicionando campos de texto

Além de adicionar ações (botões) ao alerta, você pode adicionar campos de texto ao alerta para permitir que o usuário preencha informações como IDs de usuário e senhas:

[![](alerts-images/alert02.png "Campo de texto em um alerta")](alerts-images/alert02.png#lightbox)

Se o usuário selecionar o campo de texto, o teclado tvOS padrão será exibido, permitindo que eles insiram um valor para o campo:

[![](alerts-images/alert03.png "Inserindo texto")](alerts-images/alert03.png#lightbox)

O código a seguir exibe um alerta OK/cancelar com um único campo de texto para inserir um valor:

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

O `AddTextField` método adiciona um novo campo de texto ao alerta que você pode definir definindo propriedades como o texto do espaço reservado (o texto que aparece quando o campo está vazio), o valor de texto padrão e o tipo de teclado. Por exemplo:

```csharp
// Initialize field
field.Placeholder = placeholder;
field.Text = text;
field.AutocorrectionType = UITextAutocorrectionType.No;
field.KeyboardType = UIKeyboardType.Default;
field.ReturnKeyType = UIReturnKeyType.Done;
field.ClearButtonMode = UITextFieldViewMode.WhileEditing;
```

Para que possamos agir no valor do campo de texto mais tarde, também estamos salvando uma cópia do usando o código a seguir:

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

Depois que o usuário inserir um valor no campo de texto, podemos usar a `field` variável para acessar esse valor.

<a name="Alert-View-Controller-Helper-Class" />

## <a name="alert-view-controller-helper-class"></a>Classe auxiliar do controlador de exibição de alerta

Como a exibição de tipos simples e comuns de `UIAlertViewController` alertas usando o pode resultar em um pouco de código duplicado, você pode usar uma classe auxiliar para reduzir a quantidade de código repetitivo. Por exemplo:

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

Usar essa classe, exibir e responder a alertas simples pode ser feito da seguinte maneira:

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

Este artigo abordou o trabalho `UIAlertController` com para exibir uma mensagem de alerta para o usuário no Xamarin. tvOS. Primeiro, ele mostrou como exibir um alerta simples e adicionar botões. Em seguida, ele mostrou como adicionar campos de texto a um alerta. Por fim, ele mostrou como usar uma classe auxiliar para reduzir a quantidade de código repetitivo necessária para exibir um alerta.



## <a name="related-links"></a>Links relacionados

- [Amostras do tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guias de interface humana do tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guia de programação de aplicativo para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
