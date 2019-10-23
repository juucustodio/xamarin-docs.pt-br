---
title: Ciclo de vida do Shell do Xamarin.Forms
description: Os aplicativos do Shell respeitam o ciclo de vida do Xamarin.Forms. Um evento Appearing é gerado quando uma página está prestes a ser exibida na tela e um evento Disappearing é gerado quando a página está prestes a desaparecer da tela.
ms.prod: xamarin
ms.assetid: 4E4EE50E-3BB4-441D-8355-CD9CD26ED1D0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/25/2019
ms.openlocfilehash: 2ed51763b5866c15e91d88a6a1a58c7285fb5973
ms.sourcegitcommit: e71474f91639bb43159b22f5d534325c3270ba93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72749763"
---
# <a name="xamarinforms-shell-lifecycle"></a>Ciclo de vida do Shell do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Os aplicativos do Shell respeitam o ciclo de vida do Xamarin.Forms. Um evento `Appearing` é gerado quando uma página está prestes a ser exibida na tela e um evento `Disappearing` é gerado quando a página está prestes a desaparecer da tela. Esses eventos são propagados para páginas e podem ser manipulados substituindo os métodos [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) ou [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) na página.

> [!NOTE]
> Em um aplicativo do Shell, os eventos `Appearing` e `Disappearing` são gerados no código de plataforma cruzada, antes do código de plataforma tornar uma página visível ou de remover uma página da tela.

Para obter mais informações sobre o ciclo de vida do aplicativo do Xamarin.Forms, confira [Ciclo de vida do aplicativo do Xamarin.Forms](~/xamarin-forms/app-fundamentals/app-lifecycle.md).

## <a name="hierarchical-navigation"></a>Navegação hierárquica

Em um aplicativo do Shell, efetuar push de uma página para a pilha de navegação resultará no objeto `ShellContent` visível no momento e o conteúdo de sua página, gerando o evento `Disappearing`. Do mesmo modo, remover a página mais recente da pilha de navegação resultará no objeto `ShellContent` visível no momento e o conteúdo de sua página, gerando o evento `Appearing`.

Para saber mais sobre navegação hierárquica, confira [Navegação hierárquica do Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="modal-navigation"></a>Navegação modal

Em um aplicativo do Shell, efetuar push de uma página modal para a pilha de navegação modal fará com que todos os objetos do Shell gerem o evento `Disappearing`. Do mesmo modo, remover a página modal mais recente da pilha de navegação modal fará com que todos os objetos visíveis do Shell gerem o evento `Appearing`.

Para saber mais sobre a navegação modal, confira [Páginas modais do Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="related-links"></a>Links relacionados

- [Xaminals (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Ciclo de vida do aplicativo no Xamarin.Forms](~/xamarin-forms/app-fundamentals/app-lifecycle.md)
- [Páginas modais do Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md)
