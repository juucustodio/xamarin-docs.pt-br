---
title: Alertas
description: "Este artigo aborda o trabalho com alertas em um aplicativo Xamarin.Mac. Descreve criando e exibindo alertas do código c# e responder à interação do usuário."
ms.topic: article
ms.prod: xamarin
ms.assetid: F1DB93A1-7549-4540-AD5E-D7605CCD8435
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 73b0a3292d7b1681b4086e8366e8b813194969a9
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="alerts"></a>Alertas

_Este artigo aborda o trabalho com alertas em um aplicativo Xamarin.Mac. Descreve criando e exibindo alertas do código c# e responder à interação do usuário._

Ao trabalhar com c# e .NET em um aplicativo de Xamarin.Mac, você tem acesso à mesma alertas que um desenvolvedor trabalhando em *Objective-C* e *Xcode* does. 

Um alerta é um tipo especial de caixa de diálogo que aparece quando ocorre um problema sério (como um erro) ou como um aviso (como preparação para excluir um arquivo). Como um alerta é uma caixa de diálogo, ele também requer uma resposta do usuário antes de ser encerrado.

[ ![](alert-images/alert06.png "Um exemplo de alerta")](alert-images/alert06.png)

Neste artigo, vamos abordar os fundamentos de trabalhar com alertas em um aplicativo Xamarin.Mac. 

<a name="Introduction_to_Alerts" />

## <a name="introduction-to-alerts"></a>Introdução aos alertas

Um alerta é um tipo especial de caixa de diálogo que aparece quando ocorre um problema sério (como um erro) ou como um aviso (como preparação para excluir um arquivo). Como os alertas interrompem o usuário, desde que deve ser descartados antes que o usuário pode continuar em sua tarefa, evite exibindo um alerta, a menos que seja absolutamente necessário.

Apple sugerem as diretrizes a seguir:

- Não use um alerta para dar aos usuários informações.
- Não exiba um alerta para ações comuns, pode ser desfeitas. Mesmo que essa situação pode causar perda de dados.
- Se uma situação é valioso de um alerta, evite usar qualquer outro elemento de interface do usuário ou método para exibi-lo.
- O ícone de cuidados deve ser usado com moderação.
- Descreva a situação de alerta claramente e sucinta na mensagem de alerta.
- O nome do botão padrão deve corresponder à ação que descrevem na mensagem de alerta.

Para obter mais informações, consulte o [alertas](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAlerts.html#//apple_ref/doc/uid/20000957-CH44-SW1) seção da Apple [diretrizes de Interface Humana OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Anatomy_of_an_Alert" />

## <a name="anatomy-of-an-alert"></a>Anatomia de um alerta

Conforme mencionado acima, os alertas devem ser mostrados ao usuário do aplicativo quando ocorre um problema sério ou como um aviso solicitando a potencial perda de dados (como fechar um arquivo não salvo). Em Xamarin.Mac, um alerta é criado no código c#, por exemplo:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Critical,
    InformativeText = "We need to save the document here...",
    MessageText = "Save Document",
};
alert.RunModal ();
```

O código acima exibe um alerta com o ícone de aplicativos sobreposto no ícone de aviso, um título, uma mensagem de aviso e um único **Okey** botão:

[ ![](alert-images/alert01.png "Um alerta com um botão Okey")](alert-images/alert01.png)

Apple fornece várias propriedades que podem ser usadas para personalizar um alerta:

- **AlertStyle** define o tipo de um alerta como um dos seguintes:
    - **Aviso** - usadas para avisar o usuário de um evento iminente ou atual que não é crítico. Este é o estilo padrão.
    - **Informação** - usadas para avisar o usuário sobre um evento iminente ou atual. Atualmente, não há nenhuma diferença visível entre um **aviso** e um **informativo**
    - **Crítico** - usadas para avisar o usuário sobre graves consequências sobre um evento iminente (como excluir um arquivo). Este tipo de alerta deve ser usado com moderação.
- **MessageText** -esta é a mensagem principal ou o título do alerta e rapidamente deve definir a situação para o usuário.
- **InformativeText** -este é o corpo do alerta em que você deve definir a situação claramente e apresentar opções viável para o usuário.
- **Ícone** -permite que um ícone personalizado a ser exibido para o usuário.
- **HelpAnchor** & **ShowsHelp** -permite que o alerta a ser associado ao aplicativo HelpBook e exibir a Ajuda para o alerta.
- **Botões** -por padrão, um alerta tenha apenas o **Okey** botão, mas o **botões** coleção permite que você adicionar mais opções conforme necessário.
- **ShowsSuppressionButton** - se `true` exibe uma caixa de seleção que o usuário pode usar para suprimir ocorrências subsequentes do evento que disparou o alerta.
- **AccessoryView** -permite anexar outro modo de exibição secundário para o alerta para fornecer informações adicionais, como a adição de um **campo de texto** para entrada de dados. Se você definir um novo **AccessoryView** ou modificar um existente, você precisa chamar o `Layout()` método para ajustar o layout visível do alerta.

<a name="Displaying_an_Alert" />

## <a name="displaying-an-alert"></a>Exibir um alerta

Há duas maneiras diferentes que um alerta pode ser exibido, flutuante ou como uma planilha. O código a seguir exibe um alerta como flutuante:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.RunModal ();
```
Se esse código é executado, é exibido o seguinte:

[ ![](alert-images/alert02.png "Um alerta simples")](alert-images/alert02.png)

O código a seguir exibe o alerta mesmo como uma folha:

```csharp
var alert = new NSAlert () {
    AlertStyle = NSAlertStyle.Informational,
    InformativeText = "This is the body of the alert where you describe the situation and any actions to correct it.",
    MessageText = "Alert Title",
};
alert.BeginSheet (this);
```

Se esse código for executado, a seguinte mensagem será exibida:

[ ![](alert-images/alert03.png "Um alerta exibido como uma folha")](alert-images/alert03.png)


<a name="Working_with_Alert_Buttons" />

## <a name="working-with-alert-buttons"></a>Trabalhando com os botões de alertas

Por padrão, um alerta exibe apenas o **Okey** botão. No entanto, não há limite para que, você pode criar botões extras acrescentando-los para o **botões** coleção. O código a seguir cria um alerta flutuante com um **Okey**, **Cancelar** e **talvez** botão:

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

O primeiro botão adicionado será o _botão padrão_ que será ativado se o usuário pressionar a tecla Enter. O valor retornado será um inteiro que representa a qual botão o usuário pressionado. Em nosso caso os valores a seguir serão retornados:

- **OK** - 1000.
- **Cancelar** - 1001.
- **Talvez** - 1002.

Se executarmos o código, a seguinte mensagem será exibida:

[ ![](alert-images/alert04.png "Um alerta com três opções de botão")](alert-images/alert04.png)

Aqui está o código para o mesmo alerta como uma folha:

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
Se esse código for executado, a seguinte mensagem será exibida:

[ ![](alert-images/alert05.png "Um alerta de três botão exibido como uma folha")](alert-images/alert05.png)

> [!IMPORTANT]
> Você nunca deve adicionar mais de três botões a um alerta.

<a name="Showing_the_Suppress_Button" />

## <a name="showing-the-suppress-button"></a>Mostrar botão suprimir

Se o alerta `ShowSuppressButton` é de propriedade `true`, uma caixa de seleção que o usuário pode usar para suprimir o alerta para ocorrências subsequentes do evento que disparou o alerta é exibido. O código a seguir mostra um alerta flutuante com um botão de suprimir:

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

Se o valor de `alert.SuppressionButton.State` é `NSCellStateValue.On`, o usuário foi marcada a caixa de seleção suprimir, caso contrário não tiverem.

Se o código for executado, a seguinte mensagem será exibida:

[ ![](alert-images/alert06.png "Um alerta com um botão de suprimir")](alert-images/alert06.png)

Aqui está o código para o mesmo alerta como uma folha:

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

Se esse código for executado, a seguinte mensagem será exibida:

[ ![](alert-images/alert07.png "Exibir um alerta com um botão de suprimir como uma planilha")](alert-images/alert07.png)

<a name="Adding_a_Custom_SubView" />

## <a name="adding-a-custom-subview"></a>Adicionar um modo de exibição personalizado secundário

Alertas têm um `AccessoryView` propriedade que pode ser usada para personalizar ainda mais o alerta e adicionar itens como um **campo de texto** entrada do usuário. O código a seguir cria um alerta flutuante com um campo de entrada de texto adicionado:

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

As linhas de chave aqui são `var input = new NSTextField (new CGRect (0, 0, 300, 20));` que cria um novo **campo de texto** que adicionaremos o alerta. `alert.AccessoryView = input;` quais anexa o **campo de texto** para o alerta e a chamada para o `Layout()` método, que é necessário para redimensionar o alerta para o novo modo de exibição secundário.

Se executarmos o código, a seguinte mensagem será exibida:

[ ![](alert-images/alert08.png "Se executarmos o código, a seguinte mensagem será exibida")](alert-images/alert08.png)

Aqui está o mesmo alerta como uma folha:

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

[ ![](alert-images/alert09.png "Um alerta com uma exibição personalizada")](alert-images/alert09.png)

<a name="Summary" />

## <a name="summary"></a>Resumo

Este artigo obteve uma visão detalhada de como trabalhar com alertas em um aplicativo Xamarin.Mac. Vimos os diferentes tipos e usos de alertas, como criar e personalizar alertas e como trabalhar com alertas no código c#.

## <a name="related-links"></a>Links relacionados

- [MacWindows (exemplo)](https://developer.xamarin.com/samples/mac/MacWindows/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabalhando com o Windows](~/mac/user-interface/window.md)
- [Diretrizes de Interface do sistema operacional X humanos](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Introdução ao Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [NSAlert](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSAlert_Class/index.html#//apple_ref/doc/uid/TP40004001)
