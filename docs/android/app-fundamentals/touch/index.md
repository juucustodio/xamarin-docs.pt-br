---
title: Toque e gestos no xamarin. Android
description: Telas sensíveis ao toque em muitos dos dispositivos atuais permitem aos usuários rapidamente e com eficiência interagir com dispositivos de uma maneira natural e intuitiva. Essa interação não se limita apenas a detecção de toque simples - é possível usar gestos bem. Por exemplo, o gesto de pinçar para ampliar é um exemplo muito comum por apertar uma parte da tela com dois dedos, que o usuário pode ampliar ou reduzir. Este guia examina o toque e gestos no Android.
ms.prod: xamarin
ms.assetid: 61874769-978A-4562-9B2A-7FFD45F58B38
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 7f957c9ff5a0e7c3a0821978703860ed2f723a92
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013106"
---
# <a name="touch-and-gestures-in-xamarinandroid"></a>Toque e gestos no xamarin. Android

_Telas sensíveis ao toque em muitos dos dispositivos atuais permitem aos usuários rapidamente e com eficiência interagir com dispositivos de uma maneira natural e intuitiva. Essa interação não se limita apenas a detecção de toque simples - é possível usar gestos bem. Por exemplo, o gesto de pinçar para ampliar é um exemplo muito comum por apertar uma parte da tela com dois dedos, que o usuário pode ampliar ou reduzir. Este guia examina o toque e gestos no Android._

## <a name="touch-overview"></a>Visão geral de toque

iOS e Android são semelhantes as maneiras que tratem de toque. Ambos podem dar suporte a multitoque - muitos pontos de contato na tela - e gestos complexos. Este guia apresenta alguns dos conceitos semelhanças, bem como os particularities da implementação de toque e gestos em ambas as plataformas.

Android usa uma `MotionEvent` objeto para encapsular os dados de toque e métodos no objeto de exibição para ouvir de toques.

Além de capturar dados de toque, iOS e Android fornecem meios para interpretar padrões de toques em gestos. Esses reconhecedores de gestos, por sua vez podem ser usados para interpretar os comandos específicos do aplicativo, como uma rotação de uma imagem ou um turno de uma página. O Android fornece um punhado de gestos com suporte, bem como recursos para tornar adicionando gestos personalizados complexos fácil.

Se você estiver trabalhando no Android ou iOS, a escolha entre toques e reconhecedores de gestos pode ser um confuso. Este guia recomenda que em geral, preferência deve ser fornecida para reconhecedores de gestos. Reconhecedores de gestos são implementados como classes distintas, que fornecem maior separação de preocupações e melhor encapsulamento. Isso torna mais fácil compartilhar a lógica entre os diferentes modos de exibição, minimizando a quantidade de código escrito.

Este guia segue um formato semelhante para cada sistema operacional: primeiro, toque da plataforma, as APIs são introduzidas e explicadas, pois eles são a base na qual toque interações são criadas. Em seguida, nós nos aprofundamos no mundo dos reconhecedores de gestos – primeiro explorando alguns gestos comuns, e finalizando com a criação de gestos personalizados para aplicativos. Por fim, você verá como controlar os dedos individuais usando o controle de toque de nível baixo para criar um programa finger-paint.

## <a name="sections"></a>Seções

-  [Toque no Android](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
-  [Passo a passo: Usar toque no Android](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
-  [Controle multitoque](touch-tracking.md)

## <a name="summary"></a>Resumo

Neste guia, examinamos toque no Android. Para ambos os sistemas operacionais, aprendemos como habilitar o toque e como responder a eventos de toque. Em seguida, aprendemos sobre gestos e alguns dos reconhecedores de gestos Android e iOS fornecem para lidar com alguns dos cenários mais comuns. Examinamos como criar gestos personalizados e implementá-los em aplicativos. Um passo a passo demonstrou os conceitos e as APIs para cada sistema operacional em ação, e você também viu como controlar os dedos individuais.



## <a name="related-links"></a>Links relacionados

- [Android Touch iniciar (amostra)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android toque Final (amostra)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
- [Pintura a dedo (amostra)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)
