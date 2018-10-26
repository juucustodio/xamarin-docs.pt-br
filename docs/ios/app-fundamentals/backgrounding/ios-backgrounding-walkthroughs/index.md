---
title: instruções passo a passo de Backgrounding do iOS
description: Este documento leva a instruções passo a passo que demonstram como usar informações de localização em um aplicativo backgrounded e como usar NSURLSession e o serviço de transferência em segundo plano.
ms.prod: xamarin
ms.assetid: D4937080-7865-48C0-8FE5-310E90229BC3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.openlocfilehash: e0da7dcdaa41dec244ff81a6ee6a9a9ed99c02e6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116869"
---
# <a name="ios-backgrounding-walkthroughs"></a>instruções passo a passo de Backgrounding do iOS

##  <a name="walkthrough---using-background-locationiosapp-fundamentalsbackgroundingios-backgrounding-walkthroughslocation-walkthroughmd"></a>[Passo a passo: usar o local em segundo plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/location-walkthrough.md)

Neste exemplo, criamos um iOS aplicativo local que imprime informações sobre nosso local atual - latitude, longitude e outros parâmetros - na tela. Este aplicativo será demonstram como executar corretamente as atualizações de local enquanto o aplicativo está ativo ou Backgrounded.

##  <a name="walkthrough---using-background-transfer-service-and-nsurlsessioniosapp-fundamentalsbackgroundingios-backgrounding-walkthroughsbackground-transfer-walkthroughmd"></a>[Passo a passo:usar NSURLSession e Serviço de Transferência em Segundo Plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/background-transfer-walkthrough.md)

Neste passo a passo, podemos usar o serviço de transferência em segundo plano e `NSURLSession` API para iniciar o download de uma imagem grande que continua a baixar quando o aplicativo está em segundo plano.
