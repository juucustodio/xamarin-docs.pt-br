---
title: Exibindo alertas no Xamarin. iOS
description: Este documento descreve como exibir alertas no Xamarin. iOS usando as APIs UIAlertController introduzidas no iOS 8.
ms.prod: xamarin
ms.assetid: 61C671E9-3757-4052-86E4-28640025A34A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: ec2a35a33682ec7bda2840cfb4bc6b4908fc65b0
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292382"
---
# <a name="displaying-alerts-in-xamarinios"></a>Exibindo alertas no Xamarin. iOS

A partir do iOS 8, o UIAlertController foi concluído, o UIActionSheet e o UIAlertView já foram substituídos.

Ao contrário das classes substituídas, que são subclasses de UIView, UIAlertController é uma subclasse de UIViewController.

Use `UIAlertControllerStyle` para indicar o tipo de alerta a ser exibido. Esses tipos de alertas são:

- **UIAlertControllerStyleActionSheet**
  - Antes do iOS 8, isso teria sido um UIActionSheet
- **UIAlertControllerStyleAlert**
  - Antes do iOS 8, isso teria sido UIAlertView 

Há três etapas necessárias a serem seguidas ao criar um controlador de alerta:

- Crie e configure o alerta com um:
  - título
  - mensagem
  - preferredStyle

- Adicional Adicionar um campo de texto
- Adicionar as ações necessárias
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

Exibir um alerta com várias opções é feito de maneira semelhante, mas adiciona duas ações. Por exemplo, a captura de tela a seguir mostra um alerta com dois botões:

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

Os alertas também podem exibir uma planilha de ações, semelhante à captura de tela abaixo:

 ![Alerta de planilha de ações](alerts-images/alert3.png)

Os botões são adicionados ao alerta com o `AddAction` método:

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

- [Controles (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
- [Controlador de alerta](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller)
