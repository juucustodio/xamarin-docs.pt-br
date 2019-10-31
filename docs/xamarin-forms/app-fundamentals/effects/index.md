---
title: Efeitos do Xamarin.Forms
description: Efeitos permitem que os controles nativos em cada plataforma sejam personalizados sem precisar recorrer a uma implementação de renderizador personalizado.
ms.prod: xamarin
ms.assetid: 8AF168A7-4CD9-4603-B961-15B8B1543784
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: 2de1d1dd065a01bb457ebf03acdc0c01529abf7b
ms.sourcegitcommit: 1242d32b7f072c837005cdee174abe6c0d1d0c68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73083832"
---
# <a name="xamarinforms-effects"></a>Efeitos do Xamarin.Forms

_As interfaces do usuário xamarin. Forms são renderizadas usando os controles nativos da plataforma de destino, permitindo que os aplicativos Xamarin. Forms mantenham a aparência apropriada para cada plataforma. Os efeitos permitem que os controles nativos em cada plataforma sejam personalizados sem a necessidade de recorrer a uma implementação de processador personalizado._

## <a name="introduction-to-effectsintroductionmd"></a>[Introdução aos Efeitos](introduction.md)

Efeitos permitem que os controles nativos em cada plataforma sejam personalizados e são geralmente usados para pequenas alterações de estilo. Este artigo fornece uma introdução aos efeitos, descreve o limite entre efeitos e renderizadores personalizados e descreve a classe `PlatformEffect`.

## <a name="creating-an-effectcreatingmd"></a>[Criar um efeito](creating.md)

Efeitos simplificam a personalização de um controle. Este artigo demonstra como criar um efeito que altera a cor da tela de fundo do controle [`Entry`](xref:Xamarin.Forms.Entry) quando o controle obtém foco.

## <a name="passing-parameters-to-an-effectpassing-parametersindexmd"></a>[Passar parâmetros para um efeito](passing-parameters/index.md)

Criar um efeito que é configurado por meio de parâmetros permite que o efeito seja reutilizado. Esses artigos demonstram o uso de propriedades para passar parâmetros para um efeito e a alteração de um parâmetro em runtime.

## <a name="invoking-events-from-an-effecttouch-trackingmd"></a>[Invocação de eventos de um efeito](touch-tracking.md)

Efeitos podem invocar eventos. Este artigo mostra como criar um evento que implementa acompanhamento de dedos multitoque de nível baixo e sinaliza um aplicativo para pressionamentos de toque, movimentos e liberações.

## <a name="reusable-roundeffectreusable-roundeffectmd"></a>[RoundEffect reutilizável](reusable-roundeffect.md)

RoundEffect é um efeito reutilizável que pode ser aplicado a qualquer controle derivado de Visualelement para renderizar o controle como um círculo. Esse efeito pode ser usado para criar imagens circulares, botões circulares ou outros controles circulares.
