---
title: Ciclo de vida do aplicativo
description: Como responder ao ciclo de vida do aplicativo
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: a22ad8f3f272212f5c7f088ba2112f2771ff4a7f
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34846338"
---
# <a name="app-lifecycle"></a>Ciclo de vida do aplicativo

O [ `Application` ](xref:Xamarin.Forms.Application) classe base oferece os seguintes recursos:

* [Métodos de ciclo de vida](#Lifecycle_Methods) `OnStart`, `OnSleep`, e `OnResume`.
* [Eventos de navegação de página](#page) [ `PageAppearing` ](xref:Xamarin.Forms.Application.PageAppearing), [ `PageDisappearing` ](xref:Xamarin.Forms.Application.PageDisappearing).
* [Eventos de navegação modal](#modal) `ModalPushing`, `ModalPushed`, `ModalPopping`, e `ModalPopped`.

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>Métodos de ciclo de vida

O [ `Application` ](xref:Xamarin.Forms.Application) classe contém três métodos virtuais que podem ser substituídos para manipular os métodos de ciclo de vida:

* **OnStart** -chamado quando o aplicativo for iniciado.

* **OnSleep** -chamado sempre que o aplicativo vai para o plano de fundo.

* **OnResume** -chamado quando o aplicativo é retomado após serem enviadas para o plano de fundo.

Observe que há *sem* método para encerramento do aplicativo.
Em circunstâncias normais (ie. não é uma falha) encerramento de aplicativos acontecerá do *OnSleep* state, sem qualquer notificação adicionais ao seu código.

Para observar quando esses métodos são chamados, implementar um `WriteLine` chamar em cada (conforme mostrado abaixo) e testar em cada plataforma.

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

Ao atualizar *antigos* xamarin. Forms aplicativos (por exemplo. Criar com xamarin. Forms 1.3 ou anterior), certifique-se de que a atividade principal Android inclui `ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation` no `[Activity()]` atributo. Se isso não estiver presente você vai observar o `OnStart` método é chamado em rotação, bem como quando o aplicativo é iniciado pela primeira vez. Este atributo é automaticamente incluído nos modelos de aplicativo xamarin. Forms atuais.

<a name="page" />

## <a name="page-navigation-events"></a>Eventos de navegação de página

Existem dois eventos de [ `Application` ](xref:Xamarin.Forms.Application) classe fornecer notificação de páginas que aparece e desaparece:

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) -gerado quando uma página é exibida na tela.
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) -gerada quando uma página está prestes a desaparecem da tela.

Esses eventos podem ser usados em cenários onde você deseja controlar páginas conforme eles são exibidos na tela.

> [!NOTE]
> O [ `PageAppearing` ](xref:Xamarin.Forms.Application.PageAppearing) e [ `PageDisappearing` ](xref:Xamarin.Forms.Application.PageDisappearing) eventos são gerados a partir de [ `Page` ](xref:Xamarin.Forms.Page) classe base imediatamente após o [ `Page.Appearing` ](xref:Xamarin.Forms.Page.Appearing) e [ `Page.Disappearing` ](xref:Xamarin.Forms.Page.Disappearing) eventos, respectivamente.

<a name="modal" />

## <a name="modal-navigation-events"></a>Eventos de navegação restrita

Existem quatro eventos o [ `Application` ](xref:Xamarin.Forms.Application) classe, cada um com seus próprios argumentos de evento, que permitem que você responda às páginas modais sendo mostrado e será descartada:

* **ModalPushing** - `ModalPushingEventArgs`
* **ModalPushed** - `ModalPushedEventArgs`
* **ModalPopping** - o `ModalPoppingEventArgs` classe contém um `Cancel` propriedade. Quando `Cancel` é definido como `true` modal pop foi cancelada.
* **ModalPopped** - `ModalPoppedEventArgs`

> [!NOTE]
> Para implementar os métodos de ciclo de vida do aplicativo e eventos de navegação modal, todos os pré-`Application` métodos de criação de um aplicativo xamarin. Forms (ie. aplicativos escritos em versão 1.2 ou mais antigo que usam um estático `GetMainPage` método) foram atualizados para criar um padrão `Application` que é definido como o pai do `MainPage`.
>
> Xamarin. Forms aplicativos que usam esse comportamento herdado devem ser atualizados para um `Application` subclasse conforme descrito no [classe Application](~/xamarin-forms/app-fundamentals/application-class.md) página.
