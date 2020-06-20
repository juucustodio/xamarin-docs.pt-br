---
title: Xamarin.FormsTela dupla
description: Este guia explica como criar Xamarin.Forms aplicativos para dispositivos de tela dupla.
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: aeaaeb732adaea45446d6baf833027801abf4d2a
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138899"
---
# <a name="xamarinforms-dual-screen"></a>Xamarin.Formstela dupla

![](~/media/shared/preview.png "This API is currently pre-release")

O Surface Duo (Android) e o Surface Neo (Windows 10X) introduzem novos padrões para aplicativos de toque. Xamarin.Formsinclui `TwoPaneView` `DualScreenInfo` classes e para que você possa desenvolver aplicativos para esses dispositivos.

## <a name="dual-screen-design-patterns"></a>[Padrões de design de tela dupla](design-patterns.md)

Ao considerar a melhor maneira de usar várias telas em um dispositivo de tela dupla, consulte nossas diretrizes sobre os padrões para encontrar qual opção se ajusta melhor à interface do seu aplicativo.

## <a name="dual-screen-layout"></a>[Layout de tela dupla](twopaneview.md)

A Xamarin.Forms `TwoPaneView` classe, inspirado pelo controle UWP do mesmo nome, é um layout de plataforma cruzada otimizado para dispositivos de tela dupla.

## <a name="dual-screen-device-capabilities"></a>[Funcionalidades de tela dupla do dispositivo](dual-screen-info.md)

A classe `DualScreenInfo` permite determinar o painel de sua exibição, o tamanho máximo dele, a posição do dispositivo, o ângulo da dobradiça e muito mais.

## <a name="dual-screen-platform-helpers"></a>[Auxiliares de plataforma de tela dupla](dual-screen-helper.md)

A classe `DualScreenHelper` permite verificar se a plataforma é compatível com a abertura de uma nova janela Picture in Picture. Para o Neo, isso permite abrir uma janela que será exibida no Wonderbar quando o dispositivo estiver no modo de redação.

## <a name="dual-screen-triggers"></a>[Gatilhos de tela dupla](triggers.md)

O [`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) namespace inclui dois gatilhos de estado que disparam uma [`VisualState`](xref:Xamarin.Forms.VisualState) alteração quando o modo de exibição do layout anexado, ou janela, é alterado.
