---
title: Xamarin.FormsEffect
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a6206d2c561df74a01b7d7408e8d542f1e2189d3
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139328"
---
# <a name="xamarinforms-effects"></a>Xamarin.FormsEffect

_As interfaces de usuário do Xamarin. Forms são renderizadas usando os controles nativos da plataforma de destino, permitindo que os Xamarin.Forms aplicativos mantenham a aparência apropriada para cada plataforma. Os efeitos permitem que os controles nativos em cada plataforma sejam personalizados sem a necessidade de recorrer a uma implementação de processador personalizado._

## <a name="introduction-to-effects"></a>[Introdução aos efeitos](introduction.md)

Efeitos permitem que os controles nativos em cada plataforma sejam personalizados e são geralmente usados para pequenas alterações de estilo. Este artigo fornece uma introdução aos efeitos, descreve o limite entre efeitos e renderizadores personalizados e descreve a classe `PlatformEffect`.

## <a name="creating-an-effect"></a>[Criando um efeito](creating.md)

Efeitos simplificam a personalização de um controle. Este artigo demonstra como criar um efeito que altera a cor do plano de fundo do [`Entry`](xref:Xamarin.Forms.Entry) controle quando o controle ganha foco.

## <a name="passing-parameters-to-an-effect"></a>[Passar parâmetros para um efeito](passing-parameters/index.md)

Criar um efeito que é configurado por meio de parâmetros permite que o efeito seja reutilizado. Esses artigos demonstram o uso de propriedades para passar parâmetros para um efeito e a alteração de um parâmetro em runtime.

## <a name="invoking-events-from-an-effect"></a>[Invocação de eventos de um efeito](touch-tracking.md)

Efeitos podem invocar eventos. Este artigo mostra como criar um evento que implementa acompanhamento de dedos multitoque de nível baixo e sinaliza um aplicativo para pressionamentos de toque, movimentos e liberações.

## <a name="reusable-roundeffect"></a>[RoundEffect reutilizável](reusable-roundeffect.md)

RoundEffect é um efeito reutilizável que pode ser aplicado a qualquer controle derivado de Visualelement para renderizar o controle como um círculo. Esse efeito pode ser usado para criar imagens circulares, botões circulares ou outros controles circulares.
