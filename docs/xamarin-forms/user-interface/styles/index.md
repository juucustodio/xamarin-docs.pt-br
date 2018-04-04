---
title: Estilos
description: Usando estilos para personalizar a aparência
ms.prod: xamarin
ms.assetid: 344A34AA-B19A-4765-BC8A-875D9A6B5EA8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 7a19f7597ee17282bc8b41e7f0e7e3ade2361a50
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="styles"></a>Estilos

## <a name="introductionintroductionmd"></a>[Introdução](introduction.md)

Aplicativos xamarin. Forms geralmente contêm vários controles que têm uma aparência idêntica. Definir a aparência de cada controle individual pode ser repetitiva e propenso a erros. Em vez disso, estilos podem ser criados que personalizam a aparência do controle de agrupamento e propriedades de configurações disponíveis no tipo de controle.

## <a name="explicit-stylesexplicitmd"></a>[Estilos explícitos](explicit.md)

Um *explícita* estilo é aquele que é aplicada seletivamente a controles, definindo suas [ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/) propriedades.

## <a name="implicit-stylesimplicitmd"></a>[Estilos implícitos](implicit.md)

Um *implícita* estilo é aquele que é usado por todos os controles do mesmo [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/), sem a necessidade de cada controle para referenciar o estilo.

## <a name="global-stylesapplicationmd"></a>[Estilos globais](application.md)

Estilos podem ser disponibilizados globalmente ao adicioná-los para o aplicativo [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/). Isso ajuda a evitar a duplicação de estilos em páginas ou controles.

## <a name="style-inheritanceinheritancemd"></a>[Herança de estilo](inheritance.md)

Estilos podem herdar de outros estilos para reduzir a duplicação e habilitar a reutilização.

## <a name="dynamic-stylesdynamicmd"></a>[Estilos dinâmicos](dynamic.md)

Estilos não responder a alterações de propriedade e permanecem inalterados para a duração de um aplicativo. No entanto, os aplicativos podem responder a alterações de estilo dinamicamente em tempo de execução usando recursos dinâmicos.

## <a name="device-stylesdevicemd"></a>[Estilos de dispositivo](device.md)

Xamarin. Forms inclui seis *dinâmico* estilos, conhecidos como *dispositivo* estilos, o [ `Devices.Styles` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/) classe. Todos os seis estilos podem ser aplicados a [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) somente instâncias.
