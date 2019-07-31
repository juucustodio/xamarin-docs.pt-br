---
title: Exibir pop-ups
description: 'O Xamarin.Forms fornece dois elementos de interface do usuário semelhantes a pop-up: um alerta e uma folha de ações. Este artigo demonstra como usar as APIs de alerta e de folha de ação para exibir caixas de diálogo que perguntam aos usuários perguntas simples e orientam os usuários por meio de tarefas.'
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 3b6b2ea2d497c9fdce2c323c7f7a793a4186aa4f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656106"
---
# <a name="display-pop-ups"></a>Exibir pop-ups

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)

_O Xamarin.Forms fornece dois elementos de interface do usuário semelhantes a pop-up: um alerta e uma folha de ações. Este artigo demonstra como usar as APIs de alerta e de folha de ação para exibir caixas de diálogo que perguntam aos usuários perguntas simples e orientam os usuários por meio de tarefas._

Exibir um alerta ou pedir que um usuário faça uma escolha é uma tarefa comum da interface do usuário. O Xamarin.Forms tem dois métodos na classe [`Page`](xref:Xamarin.Forms.Page) para interagir com o usuário por meio de um pop-up: [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) e [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*). Eles são renderizados com os controles nativos apropriados em cada plataforma.

## <a name="display-an-alert"></a>Exibir um alerta

Todas as plataformas com suporte do Xamarin.Forms têm um pop-up modal para alertar o usuário ou fazer perguntas simples a ele. Para exibir esses alertas no Xamarin.Forms, use o método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) em qualquer [`Page`](xref:Xamarin.Forms.Page). A linha de código a seguir mostra uma mensagem simples para o usuário:

```csharp
DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "Caixa de diálogo de alerta com um botão")

Este exemplo não coleta informações do usuário. O alerta é exibido de forma modal e, após descartado, o usuário continua interagindo com o aplicativo.

O método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) também pode ser usado para capturar a resposta do usuário apresentando dois botões e retornando um `boolean`. Para obter uma resposta a um alerta, forneça texto para os dois botões e para o método `await`. Após o usuário selecionar uma das opções, a resposta será retornada ao seu código. Observe as palavras-chave `async` e `await` no código de exemplo abaixo:

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  bool answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![DisplayAlert](pop-ups-images/alert2-sml.png "Caixa de diálogo de alerta com dois botões")](pop-ups-images/alert2.png#lightbox "Caixa de diálogo de alerta com dois botões")

## <a name="guide-users-through-tasks"></a>Orientar os usuários por meio de tarefas

A [UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) é um elemento de interface do usuário comum no iOS. O método [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) do Xamarin.Forms permite que você inclua esse controle em aplicativos de plataforma cruzada, renderizando alternativas nativas no Android e na UWP.

Para exibir uma folha de ação, `await`[`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) em qualquer [`Page`](xref:Xamarin.Forms.Page), passando a mensagem e os rótulos do botão como cadeias de caracteres. O método retorna o rótulo de cadeia de caracteres do botão que foi clicado pelo usuário. Um exemplo simples é mostrado aqui:

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![](pop-ups-images/action.png "Caixa de diálogo ActionSheet")

O botão `destroy` é renderizado de forma diferente dos outros e pode ser deixado como `null` ou ser especificado como o terceiro parâmetro de cadeia de caracteres. O exemplo a seguir usa o botão `destroy`:

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "Caixa de diálogo de folha de ação com botão Destruir")](pop-ups-images/action2.png#lightbox "Caixa de diálogo de folha de ação com botão Destruir")

## <a name="related-links"></a>Links relacionados

- [PopupsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)
