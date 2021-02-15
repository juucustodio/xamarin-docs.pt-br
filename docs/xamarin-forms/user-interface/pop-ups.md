---
title: Exibir pop-ups
description: Xamarin.Forms fornece três elementos de interface do usuário pop-up – um alerta, uma planilha de ação e um prompt. Este artigo demonstra como usar o alerta, a folha de ações e as APIs de prompt para exibir caixas de diálogo que perguntam aos usuários perguntas simples, orientam os usuários por meio de tarefas e exibem prompts.
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/10/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 09cffb4e5c7d8f6b78d5ab1de6ec9839c3969e87
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940103"
---
# <a name="display-pop-ups"></a>Exibir pop-ups

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)

Exibir um alerta, pedir a um usuário para fazer uma escolha ou exibir um prompt é uma tarefa comum da interface do usuário. Xamarin.Forms tem três métodos na [`Page`](xref:Xamarin.Forms.Page) classe para interagir com o usuário por meio de um pop-up: [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) , [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) e `DisplayPromptAsync` . Eles são renderizados com os controles nativos apropriados em cada plataforma.

## <a name="display-an-alert"></a>Exibir um alerta

Todas as Xamarin.Forms plataformas com suporte têm um pop-up modal para alertar o usuário ou fazer perguntas simples sobre eles. Para exibir esses alertas no Xamarin.Forms , use o [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) método em qualquer um [`Page`](xref:Xamarin.Forms.Page) . A linha de código a seguir mostra uma mensagem simples para o usuário:

```csharp
await DisplayAlert ("Alert", "You have been alerted", "OK");
```

![Caixa de diálogo de alerta com um botão](pop-ups-images/alert.png)

Este exemplo não coleta informações do usuário. O alerta é exibido de forma modal e, após descartado, o usuário continua interagindo com o aplicativo.

O [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) método também pode ser usado para capturar a resposta de um usuário apresentando dois botões e retornando um `boolean` . Para obter uma resposta a um alerta, forneça texto para os dois botões e para o método `await`. Após o usuário selecionar uma das opções, a resposta será retornada ao seu código. Observe as palavras-chave `async` e `await` no código de exemplo abaixo:

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  bool answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![Caixa de diálogo de alerta com dois botões](pop-ups-images/alert2-sml.png)](pop-ups-images/alert2.png#lightbox)

O [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) método também tem sobrecargas que aceitam um [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) argumento que especifica a direção na qual os elementos da interface do usuário fluem no alerta. Para obter mais informações sobre a direção do fluxo, consulte [localização da direita para a esquerda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

> [!WARNING]
> Por padrão, no UWP, quando um alerta é exibido, qualquer chave de acesso definida na página por trás do alerta ainda pode ser ativada. Para obter mais informações, consulte [as chaves de acesso do visualelement no Windows](~/xamarin-forms/platform/windows/visualelement-access-keys.md).

## <a name="guide-users-through-tasks"></a>Orientar os usuários por meio de tarefas

A [UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) é um elemento de interface do usuário comum no iOS. O Xamarin.Forms [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) método permite que você inclua esse controle em aplicativos de plataformas cruzadas, Renderizando alternativas nativas no Android e no UWP.

Para exibir uma folha de ação, `await` [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) em qualquer [`Page`](xref:Xamarin.Forms.Page), passando a mensagem e os rótulos do botão como cadeias de caracteres. O método retorna o rótulo de cadeia de caracteres do botão que foi clicado pelo usuário. Um exemplo simples é mostrado aqui:

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![Caixa de diálogo ActionSheet](pop-ups-images/action.png)

O botão `destroy` é renderizado de forma diferente dos outros e pode ser deixado como `null` ou ser especificado como o terceiro parâmetro de cadeia de caracteres. O exemplo a seguir usa o botão `destroy`:

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "Caixa de diálogo da folha de ações com o botão destruir")](pop-ups-images/action2.png#lightbox "Caixa de diálogo da folha de ações com o botão destruir")

O [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) método também tem uma sobrecarga que aceita um [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) argumento que especifica a direção na qual os elementos da interface do usuário fluem dentro da folha de ações. Para obter mais informações sobre a direção do fluxo, consulte [localização da direita para a esquerda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="display-a-prompt"></a>exibir um prompt

Para exibir um prompt, chame o `DisplayPromptAsync` em qualquer [`Page`](xref:Xamarin.Forms.Page) , passando um título e uma mensagem como `string` argumentos:

```csharp
string result = await DisplayPromptAsync("Question 1", "What's your name?");
```

O prompt é exibido de moda modal:

[![Captura de tela de um prompt modal, no iOS e no Android](pop-ups-images/simple-prompt.png "Prompt modal")](pop-ups-images/simple-prompt-large.png#lightbox "Prompt modal")

Se o botão OK for tocado, a resposta inserida será retornada como um `string` . Se o botão de cancelamento for tocado, `null` será retornado.

A lista completa de argumentos para o `DisplayPromptAsync` método é:

- `title`, do tipo `string` , é o título a ser exibido no prompt.
- `message`, do tipo `string` , é a mensagem a ser exibida no prompt.
- `accept`, do tipo `string` , é o texto do botão aceitar. Esse é um argumento opcional, cujo valor padrão é OK.
- `cancel`, do tipo `string` , é o texto do botão Cancelar. Esse é um argumento opcional, cujo valor padrão é cancelar.
- `placeholder`, do tipo `string` , é o texto do espaço reservado a ser exibido no prompt. Esse é um argumento opcional, cujo valor padrão é `null` .
- `maxLength`, do tipo `int` , é o comprimento máximo da resposta do usuário. Esse é um argumento opcional, cujo valor padrão é-1.
- `keyboard`, do tipo `Keyboard` , é o tipo de teclado a ser usado para a resposta do usuário. Esse é um argumento opcional, cujo valor padrão é `Keyboard.Default` .
- `initialValue`, do tipo `string` , é uma resposta predefinida que será exibida e que pode ser editada. Esse é um argumento opcional, cujo valor padrão é um vazio `string` .

O exemplo a seguir mostra a definição de alguns dos argumentos opcionais:

```csharp
string result = await DisplayPromptAsync("Question 2", "What's 5 + 5?", initialValue: "10", maxLength: 2, keyboard: Keyboard.Numeric);
```

Esse código exibe uma resposta predefinida de 10, limita o número de caracteres que podem ser inseridos em 2 e exibe o teclado numérico para a entrada do usuário:

[![Captura de tela de um prompt modal opcional, no iOS e no Android](pop-ups-images/keyboard-prompt.png "Prompt modal")](pop-ups-images/keyboard-prompt-large.png#lightbox "Prompt modal")

> [!WARNING]
> Por padrão, no UWP, quando um prompt é exibido, qualquer chave de acesso definida na página por trás do prompt ainda pode ser ativada. Para obter mais informações, consulte [as chaves de acesso do visualelement no Windows](~/xamarin-forms/platform/windows/visualelement-access-keys.md).

## <a name="related-links"></a>Links relacionados

- [PopupsSample](/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)
- [Localização da direita para a esquerda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
