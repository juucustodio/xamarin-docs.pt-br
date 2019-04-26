---
title: Manipulação de toque em aplicativos xamarin. IOS
description: Este documento leva a guias que descrevem como trabalhar com o toque, multitoque, gestos e toque 3D em um aplicativo xamarin. IOS.
ms.prod: xamarin
ms.assetid: E3904713-6018-4755-A315-EB045DFB3500
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 01/23/2017
ms.openlocfilehash: 5aabc3a3c2ffbcffc0e12379989f7eb43b03a902
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61399285"
---
# <a name="handling-touch-in-xamarinios-apps"></a>Manipulação de toque em aplicativos xamarin. IOS

Como outras plataformas móveis, iOS tem várias maneiras de lidar com toque. Ele pode dar suporte a multitoque — muitos pontos de contato na tela — e gestos complexos. Este guia apresenta alguns dos conceitos, bem como os particularities da implementação de toque e gestos no iOS.

encapsula o toque de dados no iOS a `UITouch` classe, que está disponível para aplicativos por meio de uma série de `UIResponder` métodos. Aplicativos podem substituir esses métodos em subclasses `UIView` e `UIViewController`, que herdam de `UIResponder`.

Além de capturar dados de toque, o iOS fornece meios para interpretar padrões de toques em gestos. Esses reconhecedores de gestos, por sua vez podem ser usados para interpretar os comandos específicos do aplicativo, como uma rotação de uma imagem ou um turno de uma página. iOS fornece um rico conjunto de classes para manipular gestos comuns com o código adicionado mínimo.

A escolha entre toques e reconhecedores de gestos pode ser um confuso. Este guia recomenda que em geral, preferência deve ser fornecida para reconhecedores de gestos. Reconhecedores de gestos são implementados como classes distintas, que fornecem maior separação de preocupações e melhor encapsulamento. Isso torna muito fácil compartilhar a lógica entre os diferentes modos de exibição, minimizando a quantidade de código escrito.

No entanto, há ocasiões em que você precisa para usar o processamento de toque de nível baixo e até mesmo controlar vários dedos, por exemplo, para criar um programa finger-paint.

## <a name="sections"></a>Seções

-  [Toque no iOS](touch-in-ios.md)
-  [Passo a passo: usando o toque no iOS](ios-touch-walkthrough.md)
-  [Controle de multitoque](touch-tracking.md)

Este guia serve como introdução ao toque no iOS. Para obter mais informações sobre como usar o 3D Touch e comentários Hápticos no iOS, que foram introduzido no iOS 9 e 10, respectivamente, consulte os guias específicos a seguir:

* [3D Touch](~/ios/platform/3d-touch.md)
* [Fornecer comentários hápticos](~/ios/user-interface/ios-ui/haptic-feedback.md)

## <a name="related-links"></a>Links relacionados

- [iOS Touch iniciar (amostra)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_start)
- [iOS toque Final (amostra)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/Touch_final)
- [Pintura a dedo (amostra)](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)
