---
title: Tela dupla do Xamarin.Forms
description: Este guia explica como criar aplicativos Xamarin.Forms para dispositivos de tela dupla.
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 344b6293090ffa4281ea6351f7f176a5be37e5bd
ms.sourcegitcommit: 0e35d3eafad833d3f19768b001bd804ddda8b69b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165563"
---
# <a name="xamarinforms-dual-screen"></a>Tela dupla do Xamarin.Forms

![](~/media/shared/preview.png "This API is currently pre-release")

O Surface Duo (Android) e o Surface Neo (Windows 10X) introduzem novos padrões para aplicativos de toque. O Xamarin.Forms inclui as classes `TwoPaneView` e `DualScreenInfo` para que seja possível desenvolver aplicativos para esses dispositivos.

## <a name="dual-screen-design-patterns"></a>[Padrões de design de tela dupla](design-patterns.md)

Ao considerar a melhor maneira de usar várias telas em um dispositivo de tela dupla, consulte nossas diretrizes sobre os padrões para encontrar qual opção se ajusta melhor à interface do seu aplicativo.

## <a name="dual-screen-layout"></a>[Layout de tela dupla](twopaneview.md)

A classe `TwoPaneView` do Xamarin.Forms, inspirada no controle UWP de mesmo nome, é um layout multiplataforma otimizado para dispositivos de tela dupla.

## <a name="dual-screen-device-capabilities"></a>[Recursos de dispositivo de tela dupla](dual-screen-info.md)

A classe `DualScreenInfo` permite determinar o painel de sua exibição, o tamanho máximo dele, a posição do dispositivo, o ângulo da dobradiça e muito mais.

## <a name="dual-screen-platform-helpers"></a>[Auxiliares de plataforma de tela dupla](dual-screen-helper.md)

A classe `DualScreenHelper` permite verificar se a plataforma é compatível com a abertura de uma nova janela Picture in Picture. Para o Neo, isso permite abrir uma janela que será exibida no Wonderbar quando o dispositivo estiver no modo de redação.

## <a name="dual-screen-triggers"></a>[Gatilhos de tela dupla](triggers.md)

O namespace [`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen) inclui dois gatilhos de estado que disparam uma alteração de [`VisualState`](xref:Xamarin.Forms.VisualState) quando o modo de exibição do layout anexado, ou janela, é alterado.
