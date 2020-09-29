---
title: Alertas no Xamarin. Mac
description: Este artigo aborda como trabalhar com alertas em um aplicativo Xamarin. Mac. Ele descreve a criação e a exibição de alertas do código C# e a resposta a interações do usuário.
ms.prod: xamarin
ms.assetid: F1DB93A1-7549-4540-AD5E-D7605CCD8435
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 55c4d4e6154ef75f15b2e75edc020b7cb4c96e38
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435312"
---
# <a name="alerts-in-xamarinmac"></a>Alertas no Xamarin. Mac

_Este artigo aborda como trabalhar com alertas em um aplicativo Xamarin. Mac. Ele descreve a criação e a exibição de alertas do código C# e a resposta a interações do usuário._

Ao trabalhar com C# e .NET em um aplicativo Xamarin. Mac, você tem acesso aos mesmos alertas que um desenvolvedor trabalhando no *Objective-C* e no *Xcode* . 

Um alerta é um tipo especial de caixa de diálogo que aparece quando ocorre um problema sério (como um erro) ou como um aviso (como a preparação para excluir um arquivo). Como um alerta é uma caixa de diálogo, ele também requer uma resposta do usuário para que possa ser fechado.

[![Um alerta de exemplo](alert-images/alert06.png)](alert-images/alert06.png#lightbox)

Neste artigo, abordaremos as noções básicas de como trabalhar com alertas em um aplicativo Xamarin. Mac. 

<a name="Introduction_to_Alerts"></a>

## <a name="introduction-to-alerts"></a>Introdução aos alertas

Um alerta é um tipo especial de caixa de diálogo que aparece quando ocorre um problema sério (como um erro) ou como um aviso (como a preparação para excluir um arquivo). Como os alertas interrompem o usuário, pois eles devem ser ignorados antes que o usuário possa continuar com sua tarefa, evite exibir um alerta, a menos que seja absolutamente necessário.

A Apple sugere as seguintes diretrizes:

- Não use um alerta simplesmente para fornecer informações aos usuários.
- Não exibir um alerta para ações comuns e desfeitas. Mesmo que essa situação possa causar perda de dados.
- Se uma situação for digna de um alerta, evite usar qualquer outro elemento ou método de interface do usuário para exibi-lo.
- O ícone de cuidado deve ser usado com moderação.
- Descreva a situação de alerta de forma clara e sucinta na mensagem de alerta.
- O nome do botão padrão deve corresponder à ação que você descreveu em sua mensagem de alerta.

Para obter mais informações, consulte a seção de [alertas](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAlerts.html#//apple_ref/doc/uid/20000957-CH44-SW1) das [diretrizes de interface humana do os X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) da Apple

<a name="Anatomy_of_an_Alert"></a>

## <a name="anatomy-of-an-alert"></a>Anatomia de um alerta

Conforme mencionado acima, os alertas devem ser mostrados para o usuário do seu aplicativo quando ocorre um problema sério ou como um aviso para potencial perda de dados (como fechar um arquivo não salvo). No Xamarin. Mac, um alerta é criado no código C#, por exemplo:

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Critical,
  InformativeText = "We need to save the document here...",
  MessageText = "Save Document",
};
alert.RunModal ();
```

O código acima exibe um alerta com o ícone de aplicativos sobreposto no ícone de aviso, um título, uma mensagem de aviso e um único botão **OK** :

[![Um alerta com um botão OK](alert-images/alert01.png)](alert-images/alert01.png#lightbox)

A Apple fornece várias propriedades que podem ser usadas para personalizar um alerta:

- **Alertstyle** define o tipo de um alerta como um dos seguintes:
  - **Aviso** -usado para avisar ao usuário um evento atual ou iminente que não é crítico. Este é o estilo padrão.
  - **Informativo** -usado para avisar o usuário sobre um evento atual ou iminente. Atualmente, não há nenhuma diferença visível entre um **aviso** e uma **informação**
  - **Crítico** – usado para avisar o usuário sobre consequências graves de um evento iminente (como excluir um arquivo). Esse tipo de alerta deve ser usado com moderação.
- **MessageText** -esta é a mensagem principal ou o título do alerta e deve definir rapidamente a situação para o usuário.
- **InformativeText** -este é o corpo do alerta em que você deve definir a situação claramente e apresentar opções de viável para o usuário.
- **Ícone** – permite que um ícone personalizado seja exibido ao usuário.
- **HelpAnchor**  &  **ShowsHelp** -permite que o alerta seja vinculado ao aplicativo HelpBook e exiba a ajuda para o alerta.
- **Botões** – por padrão, um alerta tem apenas o botão **OK** , mas a coleção de **botões** permite que você adicione mais opções, conforme necessário.
- **ShowsSuppressionButton** -se `true` exibe uma caixa de seleção que o usuário pode usar para suprimir o alerta para ocorrências subsequentes do evento que o disparou.
- **AccessoryView** -permite que você anexe outra subexibição ao alerta para fornecer informações extras, como adicionar um campo de **texto** para entrada de dados. Se você definir um novo **AccessoryView** ou modificar um existente, será necessário chamar o `Layout()` método para ajustar o layout visível do alerta.

<a name="Displaying_an_Alert"></a>

## <a name="displaying-an-alert"></a>Exibindo um alerta

Há duas maneiras diferentes pelas quais um alerta pode ser exibido, flutuante livre ou como uma planilha. O código a seguir exibe um alerta como flutuante livre:

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.RunModal ();
```

Se esse código for executado, o seguinte será exibido:

[![Um alerta simples](alert-images/alert02.png)](alert-images/alert02.png#lightbox)

O código a seguir exibe o mesmo alerta como uma planilha:

```csharp
var alert = new NSAlert () {
  AlertStyle = NSAlertStyle.Informational,
  InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
  MessageText = "Alert Title",
};
alert.BeginSheet (this);
```

Se esse código for executado, o seguinte será exibido:

[![Um alerta exibido como uma planilha](alert-images/alert03.png)](alert-images/alert03.png#lightbox)

<a name="Working_with_Alert_Buttons"></a>

## <a name="working-with-alert-buttons"></a>Trabalhando com botões de alerta

Por padrão, um alerta exibe apenas o botão **OK** . No entanto, você não está limitado a isso, você pode criar botões extras acrescentando-os à coleção **Buttons** . O código a seguir cria um alerta de flutuação livre com um botão **OK**, **Cancelar** e **talvez** :

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

O primeiro botão adicionado será o _botão padrão_ que será ativado se o usuário pressionar a tecla Enter. O valor retornado será um inteiro que representa qual botão o usuário pressionou. Em nosso caso, os seguintes valores serão retornados:

- **OK** -1000.
- **Cancelar** -1001.
- **Talvez** -1002.

Se executarmos o código, o seguinte será exibido:

[![Um alerta com três opções de botão](alert-images/alert04.png)](alert-images/alert04.png#lightbox)

Este é o código para o mesmo alerta que uma planilha:

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

Se esse código for executado, o seguinte será exibido:

[![Um alerta de três botões exibido como uma planilha](alert-images/alert05.png)](alert-images/alert05.png#lightbox)

> [!IMPORTANT]
> Você nunca deve adicionar mais de três botões a um alerta.

<a name="Showing_the_Suppress_Button"></a>

## <a name="showing-the-suppress-button"></a>Mostrando o botão suprimir

Se a propriedade do alerta `ShowSuppressButton` for `true` , o alerta exibirá uma caixa de seleção que o usuário pode usar para suprimir o alerta para ocorrências subsequentes do evento que o disparou. O código a seguir exibe um alerta flutuante livre com um botão suprimir:

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

Se o valor de `alert.SuppressionButton.State` for `NSCellStateValue.On` , o usuário marcou a caixa de seleção suprimir, caso contrário, não.

Se o código for executado, o seguinte será exibido:

[![Um alerta com um botão suprimir](alert-images/alert06.png)](alert-images/alert06.png#lightbox)

Este é o código para o mesmo alerta que uma planilha:

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

Se esse código for executado, o seguinte será exibido:

[![Um alerta com um botão suprimir exibido como uma planilha](alert-images/alert07.png)](alert-images/alert07.png#lightbox)

<a name="Adding_a_Custom_SubView"></a>

## <a name="adding-a-custom-subview"></a>Adicionando uma subexibição personalizada

Os alertas têm uma `AccessoryView` propriedade que pode ser usada para personalizar ainda mais o alerta e adicionar itens como um **campo de texto** para entrada do usuário. O código a seguir cria um alerta de flutuação livre com um campo de entrada de texto adicionado:

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

As principais linhas aqui são o `var input = new NSTextField (new CGRect (0, 0, 300, 20));` que cria um novo **campo de texto** ao qual vamos adicionar o alerta. `alert.AccessoryView = input;` que anexa o **campo de texto** ao alerta e a chamada para o `Layout()` método, que é necessário para redimensionar o alerta para caber na nova subexibição.

Se executarmos o código, o seguinte será exibido:

[![Se executarmos o código, o seguinte será exibido](alert-images/alert08.png)](alert-images/alert08.png#lightbox)

Este é o mesmo alerta que uma planilha:

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

Se executarmos esse código, o seguinte será exibido:

[![Um alerta com uma exibição personalizada](alert-images/alert09.png)](alert-images/alert09.png#lightbox)

<a name="Summary"></a>

## <a name="summary"></a>Resumo

Este artigo deu uma visão detalhada de como trabalhar com alertas em um aplicativo Xamarin. Mac. Vimos os diferentes tipos e usos de alertas, como criar e personalizar alertas e como trabalhar com alertas em código C#.

## <a name="related-links"></a>Links Relacionados

- [MacWindows (exemplo)](/samples/xamarin/mac-samples/macwindows)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabalhando com o Windows](~/mac/user-interface/window.md)
- [Diretrizes de interface humana do OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [NSAlert](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSAlert_Class/index.html#//apple_ref/doc/uid/TP40004001)