---
title: Alertas no xamarin. Mac
description: Este artigo aborda o trabalho com alertas em um aplicativo xamarin. Mac. Ele descreve a criação e exibição de alertas do C# código e responder às interações do usuário.
ms.prod: xamarin
ms.assetid: F1DB93A1-7549-4540-AD5E-D7605CCD8435
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 6545b1423b809e42293302baf3eba9521848edc1
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526384"
---
# <a name="alerts-in-xamarinmac"></a>Alertas no xamarin. Mac

_Este artigo aborda o trabalho com alertas em um aplicativo xamarin. Mac. Ele descreve a criação e exibição de alertas do C# código e responder às interações do usuário._

Ao trabalhar com C# e .NET em um aplicativo xamarin. Mac, você tem acesso para os mesmos alertas que um desenvolvedor que trabalha *Objective-C* e *Xcode* faz. 

Um alerta é um tipo especial de caixa de diálogo que aparece quando um problema sério ocorre (por exemplo, um erro) ou como um aviso (por exemplo, se preparando para excluir um arquivo). Como um alerta é uma caixa de diálogo, ele também requer uma resposta do usuário antes de ser fechado.

[![](alert-images/alert06.png "Um exemplo de alerta")](alert-images/alert06.png#lightbox)

Neste artigo, abordaremos os fundamentos de trabalhar com alertas em um aplicativo xamarin. Mac. 

<a name="Introduction_to_Alerts" />

## <a name="introduction-to-alerts"></a>Introdução aos alertas

Um alerta é um tipo especial de caixa de diálogo que aparece quando um problema sério ocorre (por exemplo, um erro) ou como um aviso (por exemplo, se preparando para excluir um arquivo). Porque alertas interrompem o usuário, pois deve ser descartados antes que o usuário pode continuar com sua tarefa, evite a exibição de um alerta, a menos que seja absolutamente necessário.

Apple sugere as diretrizes a seguir:

- Não use um alerta apenas para dar aos usuários informações.
- Não exiba um alerta para ações comuns, que podem ser desfeitas. Mesmo se essa situação pode causar perda de dados.
- Se uma situação é digna de um alerta, evite usar qualquer outro elemento de interface do usuário ou o método para exibi-lo.
- O ícone de cuidado deve ser usado com moderação.
- Descreva a situação de alerta clara e sucinta na mensagem de alerta.
- O nome do botão padrão deve corresponder à ação que você descreve na mensagem de alerta.

Para obter mais informações, consulte o [alertas](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAlerts.html#//apple_ref/doc/uid/20000957-CH44-SW1) seção da Apple [diretrizes de Interface humana dos X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Anatomy_of_an_Alert" />

## <a name="anatomy-of-an-alert"></a>Anatomia de um alerta

Conforme mencionado acima, os alertas devem ser mostrados ao usuário do seu aplicativo quando ocorre um problema sério, ou como um aviso solicitando a potencial perda de dados (como fechar um arquivo não salvo). Xamarin. Mac, um alerta é criado no C# de código, por exemplo:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Critical,
    InformativeText = "We need to save the document here...",
    MessageText = "Save Document",
};
alert.RunModal ();
```

O código acima exibirá um alerta com o ícone de aplicativos sobreposto no ícone de aviso, um título, uma mensagem de aviso e uma única **Okey** botão:

[![](alert-images/alert01.png "Um alerta com um botão Okey")](alert-images/alert01.png#lightbox)

A Apple fornece várias propriedades que podem ser usadas para personalizar um alerta:

- **AlertStyle** define o tipo de um alerta como um dos seguintes:
    - **Aviso** - usado para avisar o usuário de um evento iminente ou atual que não é crítico. Este é o estilo padrão.
    - **Informativo** - usado para avisar o usuário sobre um evento iminente ou atual. Atualmente, não há nenhuma diferença visível entre um **aviso** e um **informativo**
    - **Crítico** - usado para avisar o usuário sobre as consequências graves de um evento iminente (como excluir um arquivo). Esse tipo de alerta deve ser usado com moderação.
- **MessageText** -essa é a mensagem principal ou o título do alerta e deve definir rapidamente a situação para o usuário.
- **InformativeText** -este é o corpo do alerta em que você deve definir a situação claramente e apresentar opções viável para o usuário.
- **Ícone** -permite que um ícone personalizado a ser exibida ao usuário.
- **HelpAnchor** & **ShowsHelp** -permite que o alerta a ser associado ao aplicativo HelpBook e exibir a Ajuda para o alerta.
- **Botões** -por padrão, um alerta tem apenas o **Okey** botão, mas o **botões** coleção permite que você adicionar mais opções, conforme necessário.
- **ShowsSuppressionButton** - se `true` exibe uma caixa de seleção que o usuário pode usar para suprimir o alerta para ocorrências subsequentes do evento que disparou a ele.
- **AccessoryView** -permite que você anexe outro modo de exibição de secundário para o alerta para fornecer informações adicionais, como a adição de um **campo de texto** para entrada de dados. Se você definir uma nova **AccessoryView** ou modificar um existente, você precisa chamar o `Layout()` método para ajustar o layout visível do alerta.

<a name="Displaying_an_Alert" />

## <a name="displaying-an-alert"></a>Exibindo um alerta

Há duas maneiras diferentes que um alerta pode ser exibido, flutuante ou como uma planilha. O código a seguir exibe um alerta como flutuante:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.RunModal ();
```
Se esse código é executado, o seguinte é exibido:

[![](alert-images/alert02.png "Um alerta simples")](alert-images/alert02.png#lightbox)

O código a seguir exibe o mesmo alerta como uma planilha:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.BeginSheet (this);
```

Se esse código é executado, a seguinte mensagem será exibida:

[![](alert-images/alert03.png "Um alerta exibido como uma folha")](alert-images/alert03.png#lightbox)


<a name="Working_with_Alert_Buttons" />

## <a name="working-with-alert-buttons"></a>Trabalhando com botões de alerta

Por padrão, um alerta exibe apenas o **Okey** botão. No entanto, não há limite para que, você pode criar botões extras, acrescentando-os para o **botões** coleção. O código a seguir cria um alerta de flutuante com um **Okey**, **Cancelar** e **talvez** botão:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
var result = alert.RunModal ();
```

O primeiro botão adicionado será o _botão padrão_ que serão ativados se o usuário pressiona a tecla Enter. O valor retornado será um inteiro que representa qual botão o usuário pressionado. No nosso caso, serão retornados os valores a seguir:

- **OK** - 1000.
- **Cancelar** - 1001.
- **Talvez** - 1002.

Se executarmos o código, a seguinte mensagem será exibida:

[![](alert-images/alert04.png "Um alerta com três opções de botão")](alert-images/alert04.png#lightbox)

Aqui está o código para o mesmo alerta como uma planilha:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.BeginSheetForResponse (this, (result) => {
    Console.WriteLine ("Alert Result: {0}", result);
});
```
Se esse código é executado, a seguinte mensagem será exibida:

[![](alert-images/alert05.png "Um alerta de três botão exibido como uma planilha")](alert-images/alert05.png#lightbox)

> [!IMPORTANT]
> Você nunca deve adicionar mais de três botões a um alerta.

<a name="Showing_the_Suppress_Button" />

## <a name="showing-the-suppress-button"></a>Mostrando o botão de suprimir

Se o alerta `ShowSuppressButton` é de propriedade `true`, uma caixa de seleção que o usuário pode usar para suprimir o alerta para ocorrências subsequentes do evento que disparou o alerta é exibido. O código a seguir exibe um alerta com um botão de suprimir de flutuante:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
var result = alert.RunModal ();
Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
```

Se o valor de `alert.SuppressionButton.State` é `NSCellStateValue.On`, o usuário fez check-in na caixa de seleção suprimir, caso contrário eles não foram alterados.

Se o código for executado, a seguinte mensagem será exibida:

[![](alert-images/alert06.png "Um alerta com um botão de suprimir")](alert-images/alert06.png#lightbox)

Aqui está o código para o mesmo alerta como uma planilha:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.BeginSheetForResponse (this, (result) => {
    Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
});
```

Se esse código é executado, a seguinte mensagem será exibida:

[![](alert-images/alert07.png "Um alerta com um botão de suprimir exibir como uma planilha")](alert-images/alert07.png#lightbox)

<a name="Adding_a_Custom_SubView" />

## <a name="adding-a-custom-subview"></a>Adicionar um modo de exibição personalizado secundário

Alertas têm um `AccessoryView` propriedade que pode ser usada para personalizar ainda mais o alerta e adicionar coisas como uma **campo de texto** entrada do usuário. O código a seguir cria um alerta de flutuante com um campo de entrada de texto adicionado:

```csharp
var input = new NSTextField (new CGRect (0, 0, 300, 20));

var alert = new NSAlert () {
AlertStyle = NSAlertStyle.Informational,
InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.AccessoryView = input;
alert.Layout ();
var result = alert.RunModal ();
Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
```

As principais linhas são `var input = new NSTextField (new CGRect (0, 0, 300, 20));` que cria um novo **campo de texto** que adicionaremos o alerta. `alert.AccessoryView = input;` quais anexa a **campo de texto** para o alerta e a chamada para o `Layout()` método, que é necessário para redimensionar o alerta para caber nele é novo.

Se executarmos o código, a seguinte mensagem será exibida:

[![](alert-images/alert08.png "Se executarmos o código, a seguinte mensagem será exibida")](alert-images/alert08.png#lightbox)

Aqui está o mesmo alerta como uma planilha:

```csharp
var input = new NSTextField (new CGRect (0, 0, 300, 20));

var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.AddButton ("Ok");
alert.AddButton ("Cancel");
alert.AddButton ("Maybe");
alert.ShowsSuppressionButton = true;
alert.AccessoryView = input;
alert.Layout ();
alert.BeginSheetForResponse (this, (result) => {
    Console.WriteLine ("Alert Result: {0}, Suppress: {1}", result, alert.SuppressionButton.State == NSCellStateValue.On);
});
```

Se executarmos esse código, a seguinte mensagem será exibida:

[![](alert-images/alert09.png "Um alerta com uma exibição personalizada")](alert-images/alert09.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo apresentou uma visão detalhada de como trabalhar com alertas em um aplicativo xamarin. Mac. Vimos os diferentes tipos e os usos de alertas, como criar e personalizar alertas e como trabalhar com alertas no C# código.

## <a name="related-links"></a>Links relacionados

- [MacWindows (amostra)](https://developer.xamarin.com/samples/mac/MacWindows/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabalhando com o Windows](~/mac/user-interface/window.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [NSAlert](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSAlert_Class/index.html#//apple_ref/doc/uid/TP40004001)
