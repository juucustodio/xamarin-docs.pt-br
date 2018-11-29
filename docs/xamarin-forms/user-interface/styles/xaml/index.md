---
title: Aplicar estilo a aplicativos xamarin. Forms usando os estilos XAML
description: Este guia explica como personalizar a aparência de um aplicativo xamarin. Forms usando estilos XAML.
ms.prod: xamarin
ms.assetid: 344A34AA-B19A-4765-BC8A-875D9A6B5EA8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 2607298bdc0842f60a1d1a3299bed61bbea925a1
ms.sourcegitcommit: 2f6a5c1abf90fbdb0475fd8a3ce6de3cd7c7d575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52459857"
---
# <a name="styling-xamarinforms-apps-using-xaml-styles"></a>Aplicar estilo a aplicativos xamarin. Forms usando os estilos XAML

## <a name="introductionintroductionmd"></a>[Introdução](introduction.md)

Aplicativos xamarin. Forms geralmente contêm vários controles que têm uma aparência idêntica. Definir a aparência de cada controle individual pode ser repetitiva e sujeito a erros. Em vez disso, podem ser criados estilos para personalizar a aparência do controle de agrupamento e definindo as propriedades disponíveis no tipo de controle.

## <a name="explicit-stylesexplicitmd"></a>[Estilos explícitos](explicit.md)

Uma *explícita* estilo é aquele que é aplicada aos controles seletivamente definindo suas [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) propriedades.

## <a name="implicit-stylesimplicitmd"></a>[Estilos implícitos](implicit.md)

Uma *implícita* estilo é aquele que é usado por todos os controles do mesmo [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType), sem a necessidade de cada controle para fazer referência ao estilo.

## <a name="global-stylesapplicationmd"></a>[Estilos globais](application.md)

Estilos podem ser disponibilizados globalmente ao adicioná-los para o aplicativo [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Isso ajuda a evitar a duplicação de estilos em páginas ou controles.

## <a name="style-inheritanceinheritancemd"></a>[Herança de estilo](inheritance.md)

Estilos podem herdar de outros estilos para reduzir a duplicação e habilitar a reutilização.

## <a name="dynamic-stylesdynamicmd"></a>[Estilos dinâmicos](dynamic.md)

Estilos não responder a alterações de propriedade e permanecem inalterados durante o período de um aplicativo. No entanto, os aplicativos podem responder a alterações de estilo dinamicamente em tempo de execução usando os recursos dinâmicos.

## <a name="device-stylesdevicemd"></a>[Estilos de dispositivo](device.md)

Xamarin. Forms inclui seis *dinâmica* estilos, conhecidos como *dispositivo* estilos, o [ `Devices.Styles` ](xref:Xamarin.Forms.Device.Styles) classe. Todos os seis estilos podem ser aplicados a [ `Label` ](xref:Xamarin.Forms.Label) somente instâncias.
