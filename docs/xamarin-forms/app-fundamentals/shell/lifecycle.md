---
title: Xamarin.Forms Ciclo de vida do Shell
description: Os aplicativos de shell respeitam o Xamarin.Forms ciclo de vida e um evento exibido é gerado quando uma página está prestes a aparecer na tela, e um evento desaparecendo é gerado quando uma página está prestes a desaparecer da tela.
ms.prod: xamarin
ms.assetid: 4E4EE50E-3BB4-441D-8355-CD9CD26ED1D0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/25/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1b2abf7925eabb79b1918a9b9fedb0ba7ecced38
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563153"
---
# <a name="no-locxamarinforms-shell-lifecycle"></a>Xamarin.Forms Ciclo de vida do Shell

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Os aplicativos de shell respeitam o Xamarin.Forms ciclo de vida e um `Appearing` evento é gerado quando uma página está prestes a aparecer na tela e um `Disappearing` evento é gerado quando uma página está prestes a desaparecer da tela. Esses eventos são propagados para páginas e podem ser manipulados pela substituição [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) dos [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) métodos ou na página.

> [!NOTE]
> Em um aplicativo do Shell, os eventos `Appearing` e `Disappearing` são gerados no código de plataforma cruzada, antes do código de plataforma tornar uma página visível ou de remover uma página da tela.

Para obter mais informações sobre o Xamarin.Forms ciclo de vida do aplicativo, consulte [ Xamarin.Forms ciclo de vida do aplicativo](~/xamarin-forms/app-fundamentals/app-lifecycle.md).

## <a name="hierarchical-navigation"></a>Navegação hierárquica

Em um aplicativo do Shell, efetuar push de uma página para a pilha de navegação resultará no objeto `ShellContent` visível no momento e o conteúdo de sua página, gerando o evento `Disappearing`. Do mesmo modo, remover a página mais recente da pilha de navegação resultará no objeto `ShellContent` visível no momento e o conteúdo de sua página, gerando o evento `Appearing`.

Para obter mais informações sobre navegação hierárquica, consulte [ Xamarin.Forms navegação hierárquica](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="modal-navigation"></a>Navegação modal

Em um aplicativo do Shell, efetuar push de uma página modal para a pilha de navegação modal fará com que todos os objetos do Shell gerem o evento `Disappearing`. Do mesmo modo, remover a página modal mais recente da pilha de navegação modal fará com que todos os objetos visíveis do Shell gerem o evento `Appearing`.

Para obter mais informações sobre a navegação modal, consulte [ Xamarin.Forms páginas modais](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="related-links"></a>Links relacionados

- [Xaminals (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Xamarin.Forms Ciclo de vida do aplicativo](~/xamarin-forms/app-fundamentals/app-lifecycle.md)
- [Xamarin.Forms Páginas modais](~/xamarin-forms/app-fundamentals/navigation/modal.md)