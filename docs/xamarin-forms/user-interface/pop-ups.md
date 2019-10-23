---
title: Exibir pop-ups
description: O Xamarin. Forms fornece três elementos de interface do usuário pop-up – um alerta, uma planilha de ação e um prompt. Este artigo demonstra como usar o alerta, a folha de ações e as APIs de prompt para exibir caixas de diálogo que perguntam aos usuários perguntas simples, orientam os usuários por meio de tarefas e exibem prompts.
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2019
ms.openlocfilehash: ddf0b96295f7153803db65a1fd741cc5df473730
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697117"
---
# <a name="display-pop-ups"></a>Exibir pop-ups

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)

Exibir um alerta, pedir a um usuário para fazer uma escolha ou exibir um prompt é uma tarefa comum da interface do usuário. O Xamarin. Forms tem três métodos na classe [`Page`](xref:Xamarin.Forms.Page) para interagir com o usuário por meio de um pop-up: [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*), [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*)e `DisplayPromptAsync`. Eles são renderizados com os controles nativos apropriados em cada plataforma.

## <a name="display-an-alert"></a>Exibir um alerta

Todas as plataformas com suporte do Xamarin.Forms têm um pop-up modal para alertar o usuário ou fazer perguntas simples a ele. Para exibir esses alertas no Xamarin.Forms, use o método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) em qualquer [`Page`](xref:Xamarin.Forms.Page). A linha de código a seguir mostra uma mensagem simples para o usuário:

```csharp
await DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "Alert Dialog with One Button")

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

![](pop-ups-images/action.png "ActionSheet Dialog")

O botão `destroy` é renderizado de forma diferente dos outros e pode ser deixado como `null` ou ser especificado como o terceiro parâmetro de cadeia de caracteres. O exemplo a seguir usa o botão `destroy`:

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "Caixa de diálogo da folha de ações com o botão destruir")](pop-ups-images/action2.png#lightbox "Caixa de diálogo da folha de ações com o botão destruir")

## <a name="display-a-prompt"></a>Exibir um prompt

Para exibir um prompt, chame o `DisplayPromptAsync` em qualquer [`Page`](xref:Xamarin.Forms.Page), passando um título e uma mensagem como argumentos `string`:

```csharp
string result = await DisplayPromptAsync("Question 1", "What's your name?");
```

O prompt é exibido de moda modal:

[![Captura de tela de um prompt modal, no iOS e no Android](pop-ups-images/simple-prompt.png "Prompt modal")](pop-ups-images/simple-prompt-large.png#lightbox "Prompt modal")

Se o botão OK for tocado, a resposta inserida será retornada como um `string`. Se o botão de cancelamento for tocado, `null` será retornado.

A lista completa de argumentos para o método `DisplayPromptAsync` é:

- `title`, do tipo `string`, é o título a ser exibido no prompt.
- `message`, do tipo `string`, é a mensagem a ser exibida no prompt.
- `accept`, do tipo `string`, é o texto do botão aceitar. Esse é um argumento opcional, cujo valor padrão é OK.
- `cancel`, do tipo `string`, é o texto do botão Cancelar. Esse é um argumento opcional, cujo valor padrão é cancelar.
- `placeholder`, do tipo `string`, é o texto do espaço reservado a ser exibido no prompt. Esse é um argumento opcional, cujo valor padrão é `null`.
- `maxLength`, do tipo `int`, é o comprimento máximo da resposta do usuário. Esse é um argumento opcional, cujo valor padrão é-1.
- `keyboard`, do tipo `Keyboard`, é o tipo de teclado a ser usado para a resposta do usuário. Esse é um argumento opcional, cujo valor padrão é `Keyboard.Default`.

O exemplo a seguir mostra a definição de alguns dos argumentos opcionais:

```csharp
string result = await DisplayPromptAsync("Question 2", "What's 5 + 5?", maxLength: 2, keyboard: Keyboard.Numeric);
}
```

Esse código limita o número de caracteres que podem ser inseridos em 2 e exibe o teclado numérico para a entrada do usuário:

[![Captura de tela de um prompt modal, no iOS e no Android](pop-ups-images/keyboard-prompt.png "Prompt modal")](pop-ups-images/keyboard-prompt-large.png#lightbox "Prompt modal")

> [!NOTE]
> No momento, o método `DisplayPromptAsync` só é implementado no iOS e no Android.

## <a name="related-links"></a>Links relacionados

- [PopupsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)
