---
title: Recursos de Tela Dupla do Xamarin.Forms
description: Este guia explica como o Xamarin.Forms pode aprimorar facilmente os aplicativos para dispositivos de tela dupla.
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 1f648297a608c592d90f2c70494ae4496fe73c0f
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145510"
---
# <a name="xamarinforms-dual-screen"></a>Tela dupla do Xamarin.Forms

![](~/media/shared/preview.png "This API is currently pre-release")

O Surface Duo (Android) e o Surface Neo (Windows 10X) introduzem novos padrões para aplicativos de toque. O Xamarin.Forms apresenta `TwoPaneView` e `DualScreenInfo` para permitir que você aproveite ao máximo essas novas experiências.

## <a name="dual-screen-patternsdesign-patternsmd"></a>[Padrões de tela dupla](design-patterns.md)

Ao considerar a melhor maneira de utilizar várias telas em um dispositivo de tela dupla, veja nossas diretrizes sobre padrões para encontrar o melhor ajuste para a interface do aplicativo.

## <a name="twopaneviewtwopaneviewmd"></a>[TwoPaneView](twopaneview.md)

A classe `TwoPaneView` do Xamarin.Forms, inspirada no controle UWP de mesmo nome, apresenta um layout otimizado para dispositivos de tela dupla de uma maneira multiplataforma.

## <a name="dualscreeninfodual-screen-infomd"></a>[DualScreenInfo](dual-screen-info.md)

Para obter o máximo de dispositivos de tela dupla, talvez seja necessário saber em qual painel sua exibição está, qual é o tamanho, em que situação o dispositivo está, o ângulo da dobradiça e muito mais. `DualScreenInfo` fornece essas informações.

## <a name="dualscreenhelperdual-screen-helpermd"></a>[DualScreenHelper](dual-screen-helper.md)
`DualScreenHelper` permite verificar se a plataforma dá suporte à abertura de uma nova janela como Picture in Picture. Para o Neo, isso permite que você abra uma janela que será exibida no Wonderbar quando o dispositivo estiver no modo de composição.