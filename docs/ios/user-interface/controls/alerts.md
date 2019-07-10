---
title: Exibição de alertas no xamarin. IOS
description: Este documento descreve como exibir alertas no xamarin. IOS usando a APIs do iOS 8 UIAlertController.
ms.prod: xamarin
ms.assetid: 61C671E9-3757-4052-86E4-28640025A34A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 576de9a46fc89fe01c564c05568d4331a32dbd96
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674405"
---
# <a name="displaying-alerts-in-xamarinios"></a>Exibição de alertas no xamarin. IOS

Começando com o iOS 8, UIAlertController tem UIActionSheet substituído concluído e UIAlertView que agora são preteridos.

Ao contrário das classes que foi substituída, que são subclasses de UIView, UIAlertController é uma subclasse de UIViewController.

Use `UIAlertControllerStyle` para indicar o tipo de alerta para exibir. Esses tipos de alertas são:

- **UIAlertControllerStyleActionSheet**
    * Pré-iOS 8 isso teria sido um UIActionSheet
- **UIAlertControllerStyleAlert**
    * Pré-iOS 8 isso teria sido UIAlertView 

Há três etapas necessárias a serem executadas durante a criação de um controlador de alerta:

- Criar e configurar o alerta com r:
    * título
    * mensagem
    * preferredStyle
    
- (Opcional) Adicionar um campo de texto
- Adicione as ações necessárias
- Apresentar o controlador de exibição

O alerta mais simples contém um único botão, conforme mostrado nesta captura de tela:

 ![Alerta com um botão](alerts-images/alert1.png)

O código para exibir um alerta simples é da seguinte maneira:

```csharp
okayButton.TouchUpInside += (sender, e) => {

    //Create Alert
    var okAlertController = UIAlertController.Create ("Title", "The message", UIAlertControllerStyle.Alert);

    //Add Action
    okAlertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Default, null));

    // Present Alert
    PresentViewController (okAlertController, true, null);
};
```

Exibindo um alerta com várias opções, é feito de maneira semelhante, mas adicionar duas ações. Por exemplo, a captura de tela a seguir mostra um alerta com dois botões:

 ![Alerta com dois botões](alerts-images/alert2.png)

```csharp
okayCancelButton.TouchUpInside += ((sender, e) => {

    //Create Alert
    var okCancelAlertController = UIAlertController.Create("Alert Title", "Choose from two buttons", UIAlertControllerStyle.Alert);

    //Add Actions
    okCancelAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, alert => Console.WriteLine ("Okay was clicked")));
    okCancelAlertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, alert => Console.WriteLine ("Cancel was clicked")));

    //Present Alert
    PresentViewController(okCancelAlertController, true, null);
});
```

Alertas também podem exibir uma folha de ação, semelhante à captura de tela abaixo:

 ![Alerta de folha de ação](alerts-images/alert3.png)

Botões são adicionados ao alerta com o `AddAction` método:

```csharp
actionSheetButton.TouchUpInside += ((sender, e) => {

    // Create a new Alert Controller
    UIAlertController actionSheetAlert = UIAlertController.Create("Action Sheet", "Select an item from below", UIAlertControllerStyle.ActionSheet);

    // Add Actions
    actionSheetAlert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item One pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("custom button 1",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item Two pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Cancel, (action) => Console.WriteLine ("Cancel button pressed.")));

    // Required for iPad - You must specify a source for the Action Sheet since it is
    // displayed as a popover
    UIPopoverPresentationController presentationPopover = actionSheetAlert.PopoverPresentationController;
    if (presentationPopover!=null) {
        presentationPopover.SourceView = this.View;
        presentationPopover.PermittedArrowDirections = UIPopoverArrowDirection.Up;
    }

    // Display the alert
    this.PresentViewController(actionSheetAlert,true,null);
});
```

## <a name="related-links"></a>Links relacionados

- [Controles (amostra)](https://developer.xamarin.com/samples/monotouch/Controls/)
- [Controlador de alerta](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
