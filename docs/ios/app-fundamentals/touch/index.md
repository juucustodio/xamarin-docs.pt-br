---
title: Lidando com o touch em aplicativos Xamarin. iOS
description: Este documento contém links para guias que descrevem como trabalhar com toque, multitoque, gestos e toque 3D em um aplicativo Xamarin. iOS.
ms.prod: xamarin
ms.assetid: E3904713-6018-4755-A315-EB045DFB3500
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 01/23/2017
ms.openlocfilehash: 60bd87adb56e311407280ba98f48d8c3e2160836
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70121014"
---
# <a name="handling-touch-in-xamarinios-apps"></a>Lidando com o touch em aplicativos Xamarin. iOS

Como outras plataformas móveis, o iOS tem várias maneiras de lidar com o Touch. Ele pode dar suporte a vários pontos de contato na tela – e gestos complexos. Este guia apresenta alguns dos conceitos, bem como as particularidades de implementação de toque e gestos no iOS.

o `UITouch` Ios encapsula dados de toque na classe, que é disponibilizado para aplicativos por meio de uma `UIResponder` série de métodos. Os aplicativos podem substituir esses métodos em subclasses `UIView` de `UIViewController`e, ambos herdam de `UIResponder`.

Além de capturar dados de toque, o iOS fornece meios para interpretar padrões de toques em gestos. Esses reconhecedores de gesto podem, por sua vez, ser usados para interpretar comandos específicos do aplicativo, como uma rotação de uma imagem ou uma rodada de uma página. o iOS fornece uma rica coleção de classes para lidar com gestos comuns com o código mínimo adicionado.

A escolha entre os toques e os reconhecedores de gestos pode ser um tanto confuso. Este guia recomenda que, em geral, a preferência seja dada aos reconhecedores de gestos. Os reconhecedores de gesto são implementados como classes discretas, que fornecem maior separação de preocupações e melhor encapsulamento. Isso torna simples compartilhar a lógica entre diferentes exibições, minimizando a quantidade de código gravada.

No entanto, há ocasiões em que você precisa usar o processamento de toque de baixo nível e até mesmo controlar vários dedos, por exemplo, para criar um programa de pintura a dedo.

## <a name="sections"></a>Seções

- [Toque no iOS](touch-in-ios.md)
- [Passo a passo: usando o toque no iOS](ios-touch-walkthrough.md)
- [Controle de multitoque](touch-tracking.md)

Este guia serve como introdução ao toque no iOS. Para obter mais informações sobre como usar os comentários de toque e Haptic 3D no iOS, que foram introduzidos no iOS 9 e 10, respectivamente, consulte os guias específicos abaixo:

- [3D Touch](~/ios/platform/3d-touch.md)
- [Fornecer comentários hápticos](~/ios/user-interface/ios-ui/haptic-feedback.md)

## <a name="related-links"></a>Links relacionados

- [Início do iOS Touch (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-start)
- [Final do iOS Touch (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-final)
- [FingerPaint (exemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint)
