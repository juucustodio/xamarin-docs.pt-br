---
title: Exibindo pop-ups
description: Xamarin. Forms fornece dois elementos de interface de usuário pop o tipo – um alerta e uma folha de ação. Este artigo demonstra como usar a planilha de alerta e a ação APIs para solicitar aos usuários perguntas simples e orientar os usuários por meio de tarefas.
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 97f0917e4e8670ab379aae1b2707ae08cb29bb70
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2018
---
# <a name="displaying-pop-ups"></a>Exibindo pop-ups

_Xamarin. Forms fornece dois elementos de interface de usuário pop o tipo – um alerta e uma folha de ação. Este artigo demonstra como usar a planilha de alerta e a ação APIs para solicitar aos usuários perguntas simples e orientar os usuários por meio de tarefas._

Exibindo um alerta ou solicitando que um usuário faça uma escolha é uma tarefa comum de interface do usuário. Xamarin. Forms tem dois métodos [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) classe para interagir com o usuário por meio de um pop-up: [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/) e [ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/). Eles são processados com controles nativos apropriados em cada plataforma.

## <a name="displaying-an-alert"></a>Exibir um alerta

Todas as plataformas com suporte de xamarin. Forms têm um pop-up modal para alertar o usuário ou fazer perguntas simples deles. Para exibir esses alertas no xamarin. Forms, use o [`DisplayAlert`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/) método em qualquer [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/). A linha de código a seguir mostra uma mensagem simples para o usuário:

```csharp
DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "Caixa de diálogo alerta com um botão")

Este exemplo não coleta informações do usuário. O alerta é exibido modalmente e depois descartada o usuário continua interagindo com o aplicativo.

O [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/) método também pode ser usado para capturar a resposta do usuário, apresentando dois botões e retornando um `boolean`. Para obter uma resposta de um alerta, forneça o texto para ambos os botões e `await` o método. Depois que o usuário seleciona uma das opções que de resposta será retornada ao seu código. Observe o `async` e `await` palavras-chave no código de exemplo abaixo:

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  var answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![DisplayAlert](pop-ups-images/alert2-sml.png "caixa de diálogo com dois botões de alerta")](pop-ups-images/alert2.png#lightbox "caixa de diálogo com dois botões de alerta")

## <a name="guiding-users-through-tasks"></a>Orientando os usuários por meio de tarefas

O [UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) é um elemento de interface do usuário comum em iOS. O xamarin. Forms [ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/) método permite que você inclua esse controle nos aplicativos entre plataformas, renderização alternativas nativo no Android e UWP.

Para exibir uma folha de ação, `await` [`DisplayActionSheet`](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/) em qualquer [`Page`](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), passando a mensagem e o botão rótulos como cadeias de caracteres. O método retorna o rótulo de cadeia de caracteres do botão que foi clicado pelo usuário. Um exemplo simples é mostrado aqui:

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![](pop-ups-images/action.png "Caixa de diálogo ActionSheet")

O `destroy` botão é renderizado de forma diferente de outros e pode ser deixado `null` ou especificado como o terceiro parâmetro de cadeia de caracteres. O exemplo a seguir usa o `destroy` botão:

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "caixa de diálogo de folha de ação com o botão de destruição")](pop-ups-images/action2.png#lightbox "caixa de diálogo de folha de ação com o botão de destruição")

## <a name="summary"></a>Resumo

Este artigo demonstrou usando a folha de alerta e ação APIs para solicitar aos usuários perguntas simples e orientar os usuários por meio de tarefas. Xamarin. Forms tem dois métodos [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) classe para interagir com o usuário por meio de um pop-up: [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/) e [ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/), e eles são ambos renderizada com controles nativos apropriados em cada plataforma.



## <a name="related-links"></a>Links relacionados

- [PopupsSample](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Pop-ups/)
