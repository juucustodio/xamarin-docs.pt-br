---
title: Ciclo de vida do aplicativo
description: Como responder ao ciclo de vida do aplicativo
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2016
ms.openlocfilehash: 511591482a0e7512be34f6a210c6f44a1826be24
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="app-lifecycle"></a>Ciclo de vida do aplicativo

O `Application` classe base oferece os seguintes recursos:

* [Métodos de ciclo de vida](#Lifecycle_Methods) `OnStart`, `OnSleep`, e `OnResume`.
* [Eventos de navegação modal](#modal) `ModalPushing`, `ModalPushed`, `ModalPopping`, e `ModalPopped`.

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>Métodos de ciclo de vida

O `Application` classe contém três métodos virtuais que podem ser substituídos para manipular os métodos de ciclo de vida:

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

<a name="modal" />

## <a name="modal-navigation-events"></a>Eventos de navegação restrita

Existem quatro novos eventos de `Application` classe 1.4 xamarin. Forms, cada um com seus próprios argumentos de evento:

* **ModalPushing** - `ModalPushingEventArgs`
* **ModalPushed** - `ModalPushedEventArgs`
* **ModalPopping** - o `ModalPoppingEventArgs` classe contém um `Cancel` propriedade. Quando `Cancel` é definido como `true` modal pop foi cancelada.
* **ModalPopped** - `ModalPoppedEventArgs`

Esses eventos, ajudará você a gerenciar melhor o seu ciclo de vida do aplicativo, permitindo que você responder modais páginas sendo mostrado e será descartada.

> [!NOTE]
> Para implementar os métodos de ciclo de vida do aplicativo e eventos de navegação modal, todos os pré-`Application` métodos de criação de um aplicativo xamarin. Forms (ie. aplicativos escritos em versão 1.2 ou mais antigo que usam um estático `GetMainPage` método) foram atualizados para criar um padrão `Application` que é definido como o pai do `MainPage`.
>
> Xamarin. Forms aplicativos que usam esse comportamento herdado devem ser atualizados para um `Application` subclasse conforme descrito no [classe Application](~/xamarin-forms/app-fundamentals/application-class.md) página.
