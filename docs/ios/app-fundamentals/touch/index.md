---
title: Lidando com o touch em aplicativos Xamarin. iOS
description: Este documento contém links para guias que descrevem como trabalhar com toque, multitoque, gestos e toque 3D em um aplicativo Xamarin. iOS.
ms.prod: xamarin
ms.assetid: E3904713-6018-4755-A315-EB045DFB3500
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 01/23/2017
ms.openlocfilehash: db3e66920beb355e0b05df2118cd2645c602f0d5
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433922"
---
# <a name="handling-touch-in-xamarinios-apps"></a>Lidando com o touch em aplicativos Xamarin. iOS

Como outras plataformas móveis, o iOS tem várias maneiras de lidar com o Touch. Ele pode dar suporte a vários pontos de contato na tela – e gestos complexos. Este guia apresenta alguns dos conceitos, bem como as particularidades de implementação de toque e gestos no iOS.

o iOS encapsula dados de toque na `UITouch` classe, que é disponibilizado para aplicativos por meio de uma série de `UIResponder` métodos. Os aplicativos podem substituir esses métodos em subclasses de `UIView` e `UIViewController` , ambos herdam de `UIResponder` .

Além de capturar dados de toque, o iOS fornece meios para interpretar padrões de toques em gestos. Esses reconhecedores de gesto podem, por sua vez, ser usados para interpretar comandos específicos do aplicativo, como uma rotação de uma imagem ou uma rodada de uma página. o iOS fornece uma rica coleção de classes para lidar com gestos comuns com o código mínimo adicionado.

A escolha entre os toques e os reconhecedores de gestos pode ser um tanto confuso. Este guia recomenda que, em geral, a preferência seja dada aos reconhecedores de gestos. Os reconhecedores de gesto são implementados como classes discretas, que fornecem maior separação de preocupações e melhor encapsulamento. Isso torna simples compartilhar a lógica entre diferentes exibições, minimizando a quantidade de código gravada.

No entanto, há ocasiões em que você precisa usar o processamento de toque de baixo nível e até mesmo controlar vários dedos, por exemplo, para criar um programa de pintura a dedo.

## <a name="sections"></a>Seções

- [Toque no iOS](touch-in-ios.md)
- [Passo a passo: como usar o Touch no iOS](ios-touch-walkthrough.md)
- [Controle de multitoque](touch-tracking.md)

Este guia serve como introdução ao toque no iOS. Para obter mais informações sobre como usar os comentários de toque e Haptic 3D no iOS, que foram introduzidos no iOS 9 e 10, respectivamente, consulte os guias específicos abaixo:

- [3D Touch](~/ios/platform/3d-touch.md)
- [Como fornecer comentários hápticos](~/ios/user-interface/ios-ui/haptic-feedback.md)

## <a name="related-links"></a>Links Relacionados

- [Final do iOS Touch (exemplo)](/samples/xamarin/ios-samples/applicationfundamentals-touch-final)
- [FingerPaint (exemplo)](/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint)