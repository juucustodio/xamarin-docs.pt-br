---
title: Ciclo de vida de aplicativo xamarin. Forms
description: Este artigo explica como responder a vida do aplicativo, incluindo métodos de ciclo de vida, eventos de navegação de página e eventos de navegação modal.
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 5bdce8d1752b3d7273ffec233b120266909999c4
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675114"
---
# <a name="xamarinforms-app-lifecycle"></a>Ciclo de vida de aplicativo xamarin. Forms

O [ `Application` ](xref:Xamarin.Forms.Application) classe base oferece os seguintes recursos:

* [Métodos de ciclo de vida](#Lifecycle_Methods) `OnStart`, `OnSleep`, e `OnResume`.
* [Página eventos de navegação](#page) [ `PageAppearing` ](xref:Xamarin.Forms.Application.PageAppearing), [ `PageDisappearing` ](xref:Xamarin.Forms.Application.PageDisappearing).
* [Eventos de navegação modal](#modal) `ModalPushing`, `ModalPushed`, `ModalPopping`, e `ModalPopped`.

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>Métodos de ciclo de vida

O [ `Application` ](xref:Xamarin.Forms.Application) classe contém três métodos virtuais que podem ser substituídos para manipular os métodos de ciclo de vida:

* **OnStart** -chamado quando o aplicativo é iniciado.

* **OnSleep** – chamado sempre que o aplicativo vai para a tela de fundo.

* **OnResume** -chamado quando o aplicativo é retomado, após serem enviadas para o plano de fundo.

Observe que há *nenhum* método de encerramento do aplicativo.
Em circunstâncias normais (ou seja, não uma falha) encerramento do aplicativo vai acontecer do *OnSleep* state, sem qualquer notificações adicionais ao seu código.

Para observar quando esses métodos são chamados, implementar um `WriteLine` chamar em cada um (conforme mostrado abaixo) e de teste em cada plataforma.

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

Ao atualizar *mais antigas* (por exemplo, aplicativos do xamarin. Forms Criar com o xamarin. Forms 1.3 ou mais antigo), certifique-se de que a atividade principal do Android inclui `ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation` no `[Activity()]` atributo. Se isso não estiver presente você vai observar o `OnStart` método é chamado em rotação, bem como quando o aplicativo é iniciado pela primeira vez. Esse atributo é incluído automaticamente nos modelos de aplicativo do xamarin. Forms atuais.

<a name="page" />

## <a name="page-navigation-events"></a>Eventos de navegação de página

Há dois eventos sobre o [ `Application` ](xref:Xamarin.Forms.Application) classe que fornecem notificação de páginas que aparecem e desaparecem:

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) -Acionado quando uma página é exibida na tela.
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) -Acionado quando uma página está prestes a desaparecer da tela.

Esses eventos podem ser usados em cenários onde você deseja controlar páginas à medida que eles estão aparecendo na tela.

> [!NOTE]
> O [ `PageAppearing` ](xref:Xamarin.Forms.Application.PageAppearing) e [ `PageDisappearing` ](xref:Xamarin.Forms.Application.PageDisappearing) eventos são gerados a partir de [ `Page` ](xref:Xamarin.Forms.Page) classe base imediatamente após o [ `Page.Appearing` ](xref:Xamarin.Forms.Page.Appearing) e [ `Page.Disappearing` ](xref:Xamarin.Forms.Page.Disappearing) eventos, respectivamente.

<a name="modal" />

## <a name="modal-navigation-events"></a>Eventos de navegação modal

Há quatro eventos sobre o [ `Application` ](xref:Xamarin.Forms.Application) classe, cada um com seus próprios argumentos de evento, que permitem que você responda páginas modais que está sendo mostrado e será descartada:

* **ModalPushing** - `ModalPushingEventArgs`
* **ModalPushed** - `ModalPushedEventArgs`
* **ModalPopping** - o `ModalPoppingEventArgs` classe contém um `Cancel` propriedade. Quando `Cancel` é definido como `true` pop modal foi cancelado.
* **ModalPopped** - `ModalPoppedEventArgs`

> [!NOTE]
> Para implementar os métodos de ciclo de vida do aplicativo e eventos de navegação modal, todos os pré`Application` métodos de criação de um aplicativo xamarin. Forms (ou seja, aplicativos escritos em versão 1.2 ou mais antigo que usam um estático `GetMainPage` método) foram atualizados para criar um padrão `Application` que é definido como o pai do `MainPage`.
>
> Aplicativos xamarin. Forms que usam esse comportamento herdado devem ser atualizados para um `Application` subclasse, conforme descrito em de [classe Application](~/xamarin-forms/app-fundamentals/application-class.md) página.
