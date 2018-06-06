---
title: Exibindo alertas em xamarin
description: Este documento descreve como exibir alertas em xamarin usando a APIs do iOS 8 UIAlertController.
ms.prod: xamarin
ms.assetid: 61C671E9-3757-4052-86E4-28640025A34A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 88233cb1ac31b2669fdc38bbc9b0835a45c6b0ce
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34789587"
---
# <a name="displaying-alerts-in-xamarinios"></a>Exibindo alertas em xamarin

A partir do iOS 8, UIAlertController tem UIActionSheet substituído concluído e UIAlertView que agora são preteridos.

Ao contrário das classes que foi substituída, que são as subclasses de UIView, UIAlertController é uma subclasse de UIViewController.

Use `UIAlertControllerStyle` para indicar o tipo de alerta para exibir. Esses tipos de alertas são:

- **UIAlertControllerStyleActionSheet**
    * Pré-iOS 8 isso seria um UIActionSheet
- **UIAlertControllerStyleAlert**
    * Pré-iOS 8 isso teria sido UIAlertView 

Há três etapas necessárias para tomar durante a criação de um controlador de alerta:

- Crie e configure o alerta com r:
    * título
    * mensagem
    * preferredStyle
    
- (Opcional) Adicionar um campo de texto
- Adicione as ações necessárias
- Apresentar o controlador de exibição

O alerta mais simples contém um único botão, conforme mostrado nesta captura de tela:

 ![Alerta com um botão](alerts-images/alert1.png)

O código para exibir um alerta simples é o seguinte:

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

Exibindo um alerta com várias opções, é feita de maneira semelhante, mas adicionar duas ações. Por exemplo, a captura de tela a seguir mostra um alerta com dois botões:

 ![ Alerta com dois botões](alerts-images/alert2.png)

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

Botões são adicionados para o alerta com o `AddAction` método:

```csharp
actionSheetButton.TouchUpInside += ((sender, e) => {

    // Create a new Alert Controller
    UIAlertController actionSheetAlert = UIAlertController.Create("Action Sheet", "Select an item from below", UIAlertControllerStyle.ActionSheet);

    // Add Actions
    actionSheetAlert.AddAction(UIAlertAction.Create("OK",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item One pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("custom button 1",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item Two pressed.")));

    actionSheetAlert.AddAction(UIAlertAction.Create("Cancel",UIAlertActionStyle.Default, (action) => Console.WriteLine ("Item Three pressed.")));

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

- [Controles (exemplo)](https://developer.xamarin.com/samples/Controls/)
- [Controlador de alerta](https://developer.xamarin.com/recipes/ios/standard_controls/alertcontroller/)
