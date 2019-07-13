---
title: instruções passo a passo de Backgrounding do iOS
description: Este documento leva a instruções passo a passo que demonstram como usar informações de localização em um aplicativo backgrounded e como usar NSURLSession e o serviço de transferência em segundo plano.
ms.prod: xamarin
ms.assetid: D4937080-7865-48C0-8FE5-310E90229BC3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: b739b5a23653fdcc2b9324aefde03e48446bdc76
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865538"
---
# <a name="ios-backgrounding-walkthroughs"></a>instruções passo a passo de Backgrounding do iOS

## <a name="walkthrough---using-background-locationiosapp-fundamentalsbackgroundingios-backgrounding-walkthroughslocation-walkthroughmd"></a>[Passo a passo: usar o local em segundo plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/location-walkthrough.md)

Neste exemplo, criamos um iOS aplicativo local que imprime informações sobre nosso local atual - latitude, longitude e outros parâmetros - na tela. Este aplicativo será demonstram como executar corretamente as atualizações de local enquanto o aplicativo está ativo ou Backgrounded.

## <a name="walkthrough---using-background-transfer-service-and-nsurlsessioniosapp-fundamentalsbackgroundingios-backgrounding-walkthroughsbackground-transfer-walkthroughmd"></a>[Passo a passo:usar NSURLSession e Serviço de Transferência em Segundo Plano](~/ios/app-fundamentals/backgrounding/ios-backgrounding-walkthroughs/background-transfer-walkthrough.md)

Neste passo a passo, podemos usar o serviço de transferência em segundo plano e `NSURLSession` API para iniciar o download de uma imagem grande que continua a baixar quando o aplicativo está em segundo plano.
