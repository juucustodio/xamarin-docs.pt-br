---
title: Toque e gestos em xamarin
description: Telas sensíveis ao toque em muitos dos dispositivos atuais permitem que usuários rápida e eficientemente interagir com dispositivos de uma maneira natural e intuitiva. Essa interação não está limitada apenas a detecção de toque simples - é possível usar gestos também. Por exemplo, o gesto de pinçagem para aplicar zoom é um exemplo comum disso por esmagamento uma parte da tela com dois dedos, que o usuário pode ampliar ou reduzir. Este guia examina toque e gestos no Android.
ms.prod: xamarin
ms.assetid: 61874769-978A-4562-9B2A-7FFD45F58B38
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 3700f9c73fb58fefcdba7987c9931e385cd52d38
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
---
# <a name="touch-and-gestures-in-xamarinandroid"></a>Toque e gestos em xamarin

_Telas sensíveis ao toque em muitos dos dispositivos atuais permitem que usuários rápida e eficientemente interagir com dispositivos de uma maneira natural e intuitiva. Essa interação não está limitada apenas a detecção de toque simples - é possível usar gestos também. Por exemplo, o gesto de pinçagem para aplicar zoom é um exemplo comum disso por esmagamento uma parte da tela com dois dedos, que o usuário pode ampliar ou reduzir. Este guia examina toque e gestos no Android._

## <a name="touch-overview"></a>Visão geral de toque

iOS e Android são semelhantes a maneira que elas tratam toque. Ambos podem dar suporte a multitoque - muitos pontos de contato na tela - e gestos complexas. Este guia apresenta algumas semelhanças conceitos, bem como os particularities da implementação de toque e gestos em ambas as plataformas.

Android usa um `MotionEvent` objeto para encapsular dados de toque e métodos no objeto de exibição para escuta de ajustes.

Além de capturar dados de toque, iOS e Android fornecem meios para interpretar padrões de toques para gestos. Esses identificadores de gesto por sua vez podem ser usados para interpretar os comandos específicos do aplicativo, como uma rotação de uma imagem ou uma vez de uma página. Android fornece uma série de gestos com suporte, bem como recursos para fazer adicionando gestos personalizados complexos fácil.

Se você estiver trabalhando no Android ou iOS, a escolha entre ajustes e reconhecedores de gestos pode ser confuso. Este guia recomenda que em geral, preferência deve ser fornecida para reconhecedores de gestos. Identificadores de gesto são implementados como classes discretos, que fornecem maior separação de preocupações e encapsulamento melhor. Isso facilita a compartilhar lógica entre os diferentes modos de exibição, minimizando a quantidade de código escrito.

Este guia segue um formato semelhante para cada sistema operacional: primeiro, toque da plataforma APIs são introduzidas e explicados, como eles são a base na qual toque interações são criadas. Em seguida, nos aprofundarmos no mundo dos reconhecedores de gestos – primeiro, explorar alguns gestos comuns e concluindo com a criação de gestos personalizados para aplicativos. Por fim, você verá como controlar os dedos individuais usando o controle de nível baixo de toque para criar um programa finger-paint.

## <a name="sections"></a>Seções

-  [Toque no Android](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
-  [Passo a passo: Usando toque no Android](~/android/app-fundamentals/touch/android-touch-walkthrough.md)
-  [Controle de multitoque](touch-tracking.md)

## <a name="summary"></a>Resumo

Neste guia, examinamos toque no Android. Para ambos os sistemas operacionais, aprendemos como habilitar toque e como responder a eventos de toque. Em seguida, nós aprendemos sobre gestos e alguns dos reconhecedores de gestos Android e iOS fornecem para lidar com alguns dos cenários mais comuns. Examinamos como criar gestos personalizados e implementá-los em aplicativos. Um passo a passo demonstrou os conceitos e as APIs para cada sistema operacional em ação, e também, você viu como controlar os dedos individuais.



## <a name="related-links"></a>Links relacionados

- [Iniciar Touch Android (exemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android toque Final (exemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
- [Pintura a dedo (exemplo)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)
