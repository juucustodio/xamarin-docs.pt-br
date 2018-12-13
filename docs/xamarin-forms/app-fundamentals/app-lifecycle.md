---
title: Ciclo de vida do aplicativo no Xamarin.Forms
description: Este artigo explica como responder ao ciclo de vida do aplicativo, incluindo métodos do ciclo de vida, eventos de navegação de página e eventos de navegação modal.
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 5bdce8d1752b3d7273ffec233b120266909999c4
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675114"
---
# <a name="xamarinforms-app-lifecycle"></a>Ciclo de vida do aplicativo no Xamarin.Forms

A classe base [`Application`](xref:Xamarin.Forms.Application) oferece os seguintes recursos:

* [Métodos de ciclo de vida](#Lifecycle_Methods) `OnStart`, `OnSleep` e `OnResume`.
* [Eventos de navegação de página](#page) [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing), [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing).
* [Eventos de navegação modal](#modal) `ModalPushing`, `ModalPushed`, `ModalPopping` e `ModalPopped`.

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>Métodos de ciclo de vida

A classe [`Application`](xref:Xamarin.Forms.Application) contém três métodos virtuais que podem ser substituídos para manipular os métodos de ciclo de vida:

* **OnStart** – ocorre quando o aplicativo é iniciado.

* **OnSleep** – chamado sempre que o aplicativo vai para segundo plano.

* **OnResume** – chamado quando o aplicativo é retomado, após ser enviado para o segundo plano.

Observe que *não* há nenhum método de encerramento do aplicativo.
Em circunstâncias normais (ou seja, não uma falha), o encerramento do aplicativo acontecerá do estado *OnSleep*, sem notificações adicionais em seu código.

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

Ao atualizar aplicativos *mais antigos* do Xamarin.Forms (por exemplo, criado com o Xamarin.Forms 1.3 ou mais antigo), verifique se a atividade principal do Android inclui `ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation` no atributo `[Activity()]`. Se ele não estiver presente, você observará que o método `OnStart` é chamado em rotação e quando o aplicativo é iniciado pela primeira vez. Esse atributo é incluído automaticamente nos modelos de aplicativo atuais do Xamarin.Forms.

<a name="page" />

## <a name="page-navigation-events"></a>Eventos de navegação de página

Há dois eventos na classe [`Application`](xref:Xamarin.Forms.Application) que fornecem notificação quando páginas aparecem e desaparecem:

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) – gerado quando uma página está prestes a aparecer na tela.
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) – gerado quando uma página está prestes a desaparecer da tela.

Esses eventos podem ser usados em cenários em que você deseja controlar páginas à medida que elas aparecem na tela.

> [!NOTE]
> Os eventos [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) e [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) são gerados da classe base [`Page`](xref:Xamarin.Forms.Page) imediatamente após os eventos [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) e [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing), respectivamente.

<a name="modal" />

## <a name="modal-navigation-events"></a>Eventos de navegação modal

Há quatro eventos na classe [`Application`](xref:Xamarin.Forms.Application), cada um com seus próprios argumentos de evento, que permitem que você responda quando páginas modais são mostradas e ignoradas:

* **ModalPushing** - `ModalPushingEventArgs`
* **ModalPushed** - `ModalPushedEventArgs`
* **ModalPopping** – a classe `ModalPoppingEventArgs` contém uma propriedade `Cancel`. Quando `Cancel` é definido como `true`, a remoção modal é cancelada.
* **ModalPopped** - `ModalPoppedEventArgs`

> [!NOTE]
> Para implementar os métodos de ciclo de vida do aplicativo e eventos de navegação modal, todos os métodos pré`Application` para criar um aplicativo do Xamarin.Forms (ou seja, aplicativos escritos na versão 1.2 ou mais antiga que usam um método `GetMainPage` estático) foram atualizados para criar um `Application` padrão que é definido como pai do `MainPage`.
>
> Aplicativos do Xamarin.Forms que usam esse comportamento herdado devem ser atualizados para uma subclasse de `Application`, conforme descrito na página [Classe do aplicativo](~/xamarin-forms/app-fundamentals/application-class.md).
