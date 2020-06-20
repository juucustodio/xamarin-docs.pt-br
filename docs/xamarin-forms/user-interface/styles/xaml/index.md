---
title: Estilizando Xamarin.Forms aplicativos usando estilos XAML
description: Este guia explica como personalizar a aparência de um Xamarin.Forms aplicativo usando estilos XAML.
ms.prod: xamarin
ms.assetid: 344A34AA-B19A-4765-BC8A-875D9A6B5EA8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 72effe15d3456b5a48cbf5d09e889600134ac686
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84903084"
---
# <a name="styling-xamarinforms-apps-using-xaml-styles"></a>Estilizando Xamarin.Forms aplicativos usando estilos XAML

## <a name="introduction"></a>[Introdução](introduction.md)

Xamarin.Formsos aplicativos geralmente contêm vários controles que têm uma aparência idêntica. Definir a aparência de cada controle individual pode ser repetitivo e propenso a erros. Em vez disso, os estilos podem ser criados para personalizar a aparência do controle agrupando e definindo as propriedades disponíveis no tipo de controle.

## <a name="explicit-styles"></a>[Estilos explícitos](explicit.md)

Um estilo *explícito* é aquele aplicado seletivamente aos controles definindo suas [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Propriedades.

## <a name="implicit-styles"></a>[Estilos implícitos](implicit.md)

Um estilo *implícito* é aquele usado por todos os controles do mesmo [`TargetType`](xref:Xamarin.Forms.Style.TargetType) , sem a necessidade de cada controle para referenciar o estilo.

## <a name="global-styles"></a>[Estilos globais](application.md)

Os estilos podem ser disponibilizados globalmente adicionando-os ao aplicativo [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . Isso ajuda a evitar a duplicação de estilos em páginas ou controles.

## <a name="style-inheritance"></a>[Herança de estilo](inheritance.md)

Os estilos podem herdar de outros estilos para reduzir a duplicação e habilitar a reutilização.

## <a name="dynamic-styles"></a>[Estilos dinâmicos](dynamic.md)

Os estilos não respondem às alterações de propriedade e permanecem inalterados durante o período de um aplicativo. No entanto, os aplicativos podem responder a alterações de estilo dinamicamente em tempo de execução usando recursos dinâmicos.

## <a name="device-styles"></a>[Estilos de dispositivo](device.md)

Xamarin.Formsinclui seis estilos *dinâmicos* , conhecidos como estilos de *dispositivo* , na [`Devices.Styles`](xref:Xamarin.Forms.Device.Styles) classe. Todos os seis estilos podem ser aplicados [`Label`](xref:Xamarin.Forms.Label) somente a instâncias.

## <a name="style-classes"></a>[Classes de estilos](style-class.md)

Xamarin.Formsas classes de estilo permitem que vários estilos sejam aplicados a um controle, sem reclassificar para a herança de estilo.
