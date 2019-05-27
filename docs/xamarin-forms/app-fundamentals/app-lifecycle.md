---
title: Ciclo de vida do aplicativo no Xamarin.Forms
description: Este artigo explica como responder ao ciclo de vida do aplicativo, incluindo métodos do ciclo de vida, eventos de notificação de página e eventos de navegação modal.
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: b298ee24633e4098a6db8cf47d8e5355d72d7c34
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970900"
---
# <a name="xamarinforms-app-lifecycle"></a>Ciclo de vida do aplicativo no Xamarin.Forms

A classe base [`Application`](xref:Xamarin.Forms.Application) fornece os seguintes recursos:

- [Métodos de ciclo de vida](#Lifecycle_Methods) `OnStart`, `OnSleep` e `OnResume`.
- [Eventos de navegação de página](#page) [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing), [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing).
- [Eventos de navegação modal](#modal) `ModalPushing`, `ModalPushed`, `ModalPopping` e `ModalPopped`.

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>Métodos de ciclo de vida

A classe [`Application`](xref:Xamarin.Forms.Application) contém três métodos virtuais que podem ser substituídos para responder às alterações de ciclo de vida:

- `OnStart` – chamado quando o aplicativo é iniciado.
- `OnSleep` – chamado sempre que o aplicativo vai para segundo plano.
- `OnResume` – chamado quando o aplicativo é retomado, após ter sido enviado para o segundo plano.

> [!NOTE]
> Não há *nenhum* método de encerramento do aplicativo. Em circunstâncias normais (ou seja, não uma falha), o encerramento do aplicativo acontecerá do estado *OnSleep*, sem notificações adicionais em seu código.

Para observar quando esses métodos são chamados, implemente uma chamada `WriteLine` em cada um (conforme mostrado abaixo) e teste em cada plataforma.

```csharp
protected override void OnStart()
{
    Debug.WriteLine ("OnStart");
}
protected override void OnSleep()
{
    Debug.WriteLine ("OnSleep");
}
protected override void OnResume()
{
    Debug.WriteLine ("OnResume");
}
```

> [!IMPORTANT]
> No Android, o método `OnStart` será chamado em rotação, assim como quando o aplicativo for iniciado pela primeira vez, se a atividade principal não tiver `ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation` no atributo `[Activity()]`.

<a name="page" />

## <a name="page-notification-events"></a>Eventos de notificação de página

Há dois eventos na classe [`Application`](xref:Xamarin.Forms.Application) que fornecem notificação quando páginas aparecem e desaparecem:

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) – gerado quando uma página está prestes a aparecer na tela.
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) – gerado quando uma página está prestes a desaparecer da tela.

Esses eventos podem ser usados em cenários em que você deseja controlar páginas à medida em que aparecem na tela.

> [!NOTE]
> Os eventos [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) e [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) são gerados da classe base [`Page`](xref:Xamarin.Forms.Page) imediatamente após os eventos [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) e [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing), respectivamente.

<a name="modal" />

## <a name="modal-navigation-events"></a>Eventos de navegação modal

Há quatro eventos na classe [`Application`](xref:Xamarin.Forms.Application), cada um com seus próprios argumentos de evento, que permitem que você responda quando páginas modais são mostradas e ignoradas:

- `ModalPushing` – acionado quando uma página é enviada por push de forma modal.
- `ModalPushed` – acionado após uma página ter sido enviada por push de forma modal.
- `ModalPopping` – acionado quando uma página é removida mais recente da pilha de forma modal.
- `ModalPopped` – acionado após uma página ter sido removida mais recente da pilha de forma modal.

> [!NOTE]
> Os argumentos de evento do evento `ModalPopping`, do tipo `ModalPoppingEventArgs`, contêm uma propriedade `Cancel`. Quando `Cancel` é definido como `true`, a remoção modal é cancelada.
