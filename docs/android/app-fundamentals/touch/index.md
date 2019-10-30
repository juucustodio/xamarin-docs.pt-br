---
title: Toque e gestos no Xamarin. Android
description: As telas de toque em muitos dos dispositivos atuais permitem que os usuários interajam de forma rápida e eficiente com os dispositivos de maneira natural e intuitiva. Essa interação não é limitada apenas à detecção de toque simples – também é possível usar gestos. Por exemplo, o gesto de pinçar para zoom é um exemplo muito comum disso ao pinçar uma parte da tela com dois dedos nos quais o usuário pode ampliar ou reduzir. Este guia examina toque e gestos no Android.
ms.prod: xamarin
ms.assetid: 61874769-978A-4562-9B2A-7FFD45F58B38
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 43637d8592631b2732e5922544f52d91947dd3bd
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024291"
---
# <a name="touch-and-gestures-in-xamarinandroid"></a>Toque e gestos no Xamarin. Android

_As telas de toque em muitos dos dispositivos atuais permitem que os usuários interajam de forma rápida e eficiente com os dispositivos de maneira natural e intuitiva. Essa interação não é limitada apenas à detecção de toque simples – também é possível usar gestos. Por exemplo, o gesto de pinçar para zoom é um exemplo muito comum disso ao pinçar uma parte da tela com dois dedos nos quais o usuário pode ampliar ou reduzir. Este guia examina toque e gestos no Android._

## <a name="touch-overview"></a>Visão geral de toque

o iOS e o Android são semelhantes de como lidam com o Touch. Ambos podem dar suporte a vários pontos de contato diversos na tela e em gestos complexos. Este guia apresenta algumas das semelhanças em conceitos, bem como as particularidades de implementação de toque e gestos em ambas as plataformas.

O Android usa um objeto `MotionEvent` para encapsular dados de toque e métodos no objeto de exibição para ouvir os toques.

Além de capturar dados de toque, o iOS e o Android fornecem meios para interpretar padrões de toques em gestos. Esses reconhecedores de gesto podem, por sua vez, ser usados para interpretar comandos específicos do aplicativo, como uma rotação de uma imagem ou uma rodada de uma página. O Android fornece alguns gestos com suporte, bem como recursos para facilitar a adição de gestos personalizados complexos.

Se você estiver trabalhando no Android ou no iOS, a escolha entre toques e reconhecedores de gesto pode ser um tanto confuso. Este guia recomenda que, em geral, a preferência seja dada aos reconhecedores de gestos. Os reconhecedores de gesto são implementados como classes discretas, que fornecem maior separação de preocupações e melhor encapsulamento. Isso facilita o compartilhamento da lógica entre diferentes exibições, minimizando a quantidade de código gravada.

Este guia segue um formato semelhante para cada sistema operacional: primeiro, as APIs de toque da plataforma são introduzidas e explicadas, pois são a base sobre a qual as interações de toque são criadas. Em seguida, nos aprofundamos no mundo dos reconhecedores de gestos – primeiro, explorando alguns gestos comuns e concluindo com a criação de gestos personalizados para aplicativos. Por fim, você verá como controlar os dedos individuais usando o controle de toque de baixo nível para criar um programa de pintura a dedo.

## <a name="sections"></a>Seções

- [Toque no Android](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
- [Walkthrough: usando o touch no Android](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
- [Controle multitoque](touch-tracking.md)

## <a name="summary"></a>Resumo

Neste guia, examinamos o toque no Android. Para ambos os sistemas operacionais, aprendemos como habilitar o toque e como responder aos eventos de toque. Em seguida, aprendemos sobre gestos e alguns dos reconhecedores de gestos que o Android e o iOS fornecem para lidar com alguns dos cenários mais comuns. Examinamos como criar gestos personalizados e implementá-los em aplicativos. Uma explicação demonstra os conceitos e as APIs para cada sistema operacional em ação, e você também viu como controlar os dedos individuais.

## <a name="related-links"></a>Links relacionados

- [Android Touch Start (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-start)
- [Final do Android Touch (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-final)
- [FingerPaint (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)
