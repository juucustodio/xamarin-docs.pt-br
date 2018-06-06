---
title: Tratamento de toque em aplicativos xamarin
description: Links neste documento em guias que descrevem como trabalhar com o toque, multitoque, gestos e 3D toque em um aplicativo xamarin.
ms.prod: xamarin
ms.assetid: E3904713-6018-4755-A315-EB045DFB3500
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/23/2017
ms.openlocfilehash: eb8dce8b13345c13a6f95ae7784bd135e7d1f1f5
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34784149"
---
# <a name="handling-touch-in-xamarinios-apps"></a>Tratamento de toque em aplicativos xamarin

Como outras plataformas móveis, iOS possui um número de formas de lidar com toque. Ela pode suportar multitoque — muitos pontos de contato na tela — e gestos complexas. Este guia apresenta alguns conceitos, bem como os particularities da implementação de toque e gestos em iOS.

iOS encapsula os dados de toque no `UITouch` classe, que é disponibilizado para aplicativos por meio de uma série de `UIResponder` métodos. Aplicativos podem substituir esses métodos em subclasses de `UIView` e `UIViewController`, que herdam de `UIResponder`.

Além de capturar dados de toque, o iOS fornece os meios para interpretar padrões de toques para gestos. Esses identificadores de gesto por sua vez podem ser usados para interpretar os comandos específicos do aplicativo, como uma rotação de uma imagem ou uma vez de uma página. iOS fornece um rico conjunto de classes para manipular gestos comuns com o código adicionado mínimo.

A escolha entre ajustes e reconhecedores de gestos pode ser confuso. Este guia recomenda que em geral, preferência deve ser fornecida para reconhecedores de gestos. Identificadores de gesto são implementados como classes discretos, que fornecem maior separação de preocupações e encapsulamento melhor. Isso facilita compartilhar a lógica entre os diferentes modos de exibição, minimizando a quantidade de código escrito.

No entanto, há ocasiões em que você precisa para usar o processamento de nível baixo de toque e até mesmo controlar vários dedos, por exemplo, para criar um programa finger-paint.

## <a name="sections"></a>Seções

-  [Toque no iOS](touch-in-ios.md)
-  [Passo a passo: Usando toque no iOS](ios-touch-walkthrough.md)
-  [Controle de multitoque](touch-tracking.md)

Este guia serve como introdução ao toque no iOS. Para obter mais informações sobre como usar 3D Touch e comentários Haptic no iOS, que foram introduzido no iOS 9 e 10 respectivamente, consulte os guias específicos abaixo:

* [3D Touch](~/ios/platform/3d-touch.md)
* [Fornecer comentários hápticos](~/ios/user-interface/ios-ui/haptic-feedback.md)

## <a name="related-links"></a>Links relacionados

- [iOS Touch iniciar (exemplo)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start)
- [iOS toque Final (exemplo)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final)
- [Pintura a dedo (exemplo)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)
