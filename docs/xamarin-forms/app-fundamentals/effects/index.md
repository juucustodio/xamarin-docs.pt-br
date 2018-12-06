---
title: Efeitos do Xamarin.Forms
description: Efeitos permitem que os controles nativos em cada plataforma sejam personalizados sem precisar recorrer a uma implementação de renderizador personalizado.
ms.prod: xamarin
ms.assetid: 8AF168A7-4CD9-4603-B961-15B8B1543784
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: 9d859377c40c6fca07e140c50da46d8f30aaae04
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994452"
---
# <a name="xamarinforms-effects"></a>Efeitos do Xamarin.Forms

_As interfaces do usuário do Xamarin.Forms são renderizadas usando controles nativos da plataforma de destino, permitindo que os aplicativos Xamarin.Forms mantenham a aparência apropriada para cada plataforma. Efeitos permitem que os controles nativos em cada plataforma sejam personalizados sem precisar recorrer a uma implementação de renderizador personalizado._

## <a name="introduction-to-effectsintroductionmd"></a>[Introdução aos Efeitos](introduction.md)

Efeitos permitem que os controles nativos em cada plataforma sejam personalizados e são geralmente usados para pequenas alterações de estilo. Este artigo fornece uma introdução aos efeitos, descreve o limite entre efeitos e renderizadores personalizados e descreve a classe `PlatformEffect`.

## <a name="creating-an-effectcreatingmd"></a>[Criar um efeito](creating.md)

Efeitos simplificam a personalização de um controle. Este artigo demonstra como criar um efeito que altera a cor da tela de fundo do controle [`Entry`](xref:Xamarin.Forms.Entry) quando o controle obtém foco.

## <a name="passing-parameters-to-an-effectpassing-parametersindexmd"></a>[Passar parâmetros para um efeito](passing-parameters/index.md)

Criar um efeito que é configurado por meio de parâmetros permite que o efeito seja reutilizado. Esses artigos demonstram o uso de propriedades para passar parâmetros para um efeito e a alteração de um parâmetro em tempo de execução.

## <a name="invoking-events-from-an-effecttouch-trackingmd"></a>[Invocação de eventos de um efeito](touch-tracking.md)

Efeitos podem invocar eventos. Este artigo mostra como criar um evento que implementa acompanhamento de dedos multitoque de nível baixo e sinaliza um aplicativo para pressionamentos de toque, movimentos e liberações.
