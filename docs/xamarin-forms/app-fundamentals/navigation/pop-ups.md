---
title: Exibir o pop-ups
description: Xamarin. Forms fornece dois elementos de interface de usuário pop-up semelhante – um alerta e uma folha de ações. Este artigo demonstra como usar a folha de alerta e ação de APIs para fazer perguntas simples de usuários e para orientar os usuários por meio de tarefas.
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 156c2f9dca47a7755d4f810d7921a05662388ded
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996708"
---
# <a name="displaying-pop-ups"></a>Exibir o pop-ups

_Xamarin. Forms fornece dois elementos de interface de usuário pop-up semelhante – um alerta e uma folha de ações. Este artigo demonstra como usar a folha de alerta e ação de APIs para fazer perguntas simples de usuários e para orientar os usuários por meio de tarefas._

Exibindo um alerta ou pedir a um usuário faça uma escolha é uma tarefa comum de interface do usuário. Xamarin. Forms tem dois métodos [ `Page` ](xref:Xamarin.Forms.Page) classe para interagir com o usuário por meio de um pop-up: [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*) e [ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet*). Eles são processados com controles nativos apropriados em cada plataforma.

## <a name="displaying-an-alert"></a>Exibindo um alerta

Todas as plataformas com suporte para xamarin. Forms têm um pop-up modal para alertar o usuário ou fazer perguntas simples deles. Para exibir esses alertas no xamarin. Forms, use o [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) método em qualquer [`Page`](xref:Xamarin.Forms.Page). A linha de código a seguir mostra uma mensagem simples para o usuário:

```csharp
DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "Caixa de diálogo de alerta com um botão")

Este exemplo não coleta informações do usuário. Exibe o alerta de forma restrita e depois descartada o usuário continua interagindo com o aplicativo.

O [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*) método também pode ser usado para capturar a resposta do usuário, apresentando dois botões e retornando um `boolean`. Para obter uma resposta de um alerta, forneça o texto para ambos os botões e `await` o método. Depois que o usuário seleciona uma das opções que a resposta será retornada ao seu código. Observe a `async` e `await` palavras-chave no código de exemplo abaixo:

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  var answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![DisplayAlert](pop-ups-images/alert2-sml.png "caixa de diálogo com dois botões de alerta")](pop-ups-images/alert2.png#lightbox "caixa de diálogo com dois botões de alerta")

## <a name="guiding-users-through-tasks"></a>Orientando os usuários por meio de tarefas

O [UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) é um elemento de interface do usuário comum no iOS. O xamarin. Forms [ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet*) método permite que você inclua esse controle em aplicativos de plataforma cruzada, renderização alternativas nativas no Android e UWP.

Para exibir uma folha de ação, `await` [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) em qualquer [`Page`](xref:Xamarin.Forms.Page), passando a mensagem e o botão rótulos como cadeias de caracteres. O método retorna o rótulo de cadeia de caracteres do botão que foi clicado pelo usuário. Um exemplo simples é mostrado aqui:

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![](pop-ups-images/action.png "Caixa de diálogo ActionSheet")

O `destroy` botão é renderizado de forma diferente do que os outros e pode ser deixado `null` ou especificada como o terceiro parâmetro de cadeia de caracteres. O exemplo a seguir usa o `destroy` botão:

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "caixa de diálogo de folha de ação com o botão Destroy")](pop-ups-images/action2.png#lightbox "caixa de diálogo de folha de ação com o botão de destruição")

## <a name="summary"></a>Resumo

Este artigo demonstrou usando a folha de alerta e ação de APIs para fazer perguntas simples de usuários e para orientar os usuários por meio de tarefas. Xamarin. Forms tem dois métodos [ `Page` ](xref:Xamarin.Forms.Page) classe para interagir com o usuário por meio de um pop-up: [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*) e [ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet*), e eles são ambos renderizado com controles nativos apropriados em cada plataforma.



## <a name="related-links"></a>Links relacionados

- [PopupsSample](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Pop-ups/)
