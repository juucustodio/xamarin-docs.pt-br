---
title: Ciclo de vida do aplicativo no Xamarin.Forms
description: Este artigo explica como responder ao ciclo de vida do aplicativo, incluindo métodos do ciclo de vida, eventos de notificação de página e eventos de navegação modal.
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 41e8d073982bf7963b3a77a939bf28e52e86feaa
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "67675184"
---
# <a name="xamarinforms-app-lifecycle"></a>Ciclo de vida do aplicativo no Xamarin.Forms

A [`Application`](xref:Xamarin.Forms.Application) classe base fornece os seguintes recursos:

- [Métodos de ciclo de](#Lifecycle_Methods) `OnStart`vida, `OnSleep`e `OnResume`.
- [Eventos de navegação de](#page) [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing)página , [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing).
- Eventos `ModalPushing`de navegação `ModalPushed` `ModalPopping` [modal,](#modal) , e `ModalPopped`.

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>Métodos de ciclo de vida

A [`Application`](xref:Xamarin.Forms.Application) classe contém três métodos virtuais que podem ser substituídos para responder às alterações do ciclo de vida:

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

Existem dois eventos [`Application`](xref:Xamarin.Forms.Application) na classe que fornecem notificação de páginas que aparecem e desaparecem:

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing)- levantada quando uma página está prestes a aparecer na tela.
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing)- levantado quando uma página está prestes a desaparecer da tela.

Esses eventos podem ser usados em cenários em que você deseja controlar páginas à medida em que aparecem na tela.

> [!NOTE]
> Os [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) eventos são levantados [`Page`](xref:Xamarin.Forms.Page) da classe base [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing) imediatamente após os eventos, respectivamente.

<a name="modal" />

## <a name="modal-navigation-events"></a>Eventos de navegação modal

Há quatro eventos [`Application`](xref:Xamarin.Forms.Application) na classe, cada um com seus próprios argumentos de evento, que permitem que você responda às páginas modais sendo mostradas e descartadas:

- `ModalPushing` – acionado quando uma página é enviada por push de forma modal.
- `ModalPushed` – acionado após uma página ter sido enviada por push de forma modal.
- `ModalPopping` – acionado quando uma página é removida mais recente da pilha de forma modal.
- `ModalPopped` – acionado após uma página ter sido removida mais recente da pilha de forma modal.

> [!NOTE]
> Os argumentos do evento `ModalPopping`, do tipo `ModalPoppingEventArgs`, contêm uma propriedade `Cancel`. Quando `Cancel` é definido como `true`, a remoção modal é cancelada.
