---
title: Depurar no dispositivo
description: Este artigo explica como depurar um aplicativo Xamarin.Android em um dispositivo Android físico.
ms.prod: xamarin
ms.assetid: 153D3746-A27F-198B-48FE-D219C0133A79
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 3ca524e451a7a4eb838805c839b33c4b9dd6bddd
ms.sourcegitcommit: 5821c9709bf5e06e6126233932f94f9cf3524577
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75556529"
---
# <a name="debug-on-an-android-device"></a>Depurar em um dispositivo Android

_Este artigo explica como depurar um aplicativo Xamarin.Android em um dispositivo Android físico._

É possível depurar um aplicativo Xamarin.Android em um dispositivo Android usando o Visual Studio para Mac ou o Visual Studio. Antes de depurar um dispositivo, ele deve ser [instalado para desenvolvimento](~/android/get-started/installation/set-up-device-for-development.md) e conectado ao seu PC ou Mac.

## <a name="debug-application"></a>Depurar aplicativo

Quando um dispositivo é conectado ao computador, a depuração de um aplicativo Xamarin.Android é feito da mesma forma como qualquer outro produto Xamarin ou aplicativo .NET. Verifique se a configuração **Depurar** e o dispositivo externo estão selecionados no IDE; isso garantirá que os símbolos de depuração necessários estejam disponíveis e que o IDE possa se conectar ao aplicativo em execução: 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Configuração de depuração selecionada](debug-on-device-images/image1-vs.png)

Em seguida, um ponto de interrupção é definido no código:

![Ponto de interrupção definido na linha de código](debug-on-device-images/image2-vs.png)

Depois que o dispositivo foi selecionado, o Xamarin.Android conectará ao dispositivo, implantará o aplicativo e, em seguida, o executará. Quando o ponto de interrupção é atingido, o depurador para o aplicativo, permitindo que o aplicativo seja depurado de forma semelhante a qualquer outro aplicativo em C#: 

![Ponto de interrupção atingido](debug-on-device-images/image3-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Configuração de depuração selecionada](debug-on-device-images/image1-xs.png)

Em seguida, um ponto de interrupção é definido no código:

![Ponto de interrupção definido na linha de código](debug-on-device-images/image2-xs.png)

Depois que o dispositivo foi selecionado, o Xamarin.Android conectará ao dispositivo, implantará o aplicativo e, em seguida, o executará. Quando o ponto de interrupção é atingido, o depurador para o aplicativo, permitindo que o aplicativo seja depurado de forma semelhante a qualquer outro aplicativo em C#: 

![Ponto de interrupção atingido](debug-on-device-images/image3-xs.png)

-----

## <a name="summary"></a>Resumo

Este documento discutiu como depurar um aplicativo Xamarin.Android definindo um ponto de interrupção e selecionando o dispositivo de destino.

## <a name="related-links"></a>Links Relacionados

- [Configurar o dispositivo para desenvolvimento](~/android/get-started/installation/set-up-device-for-development.md)
- [Configuração do atributo depurável](~/android/deploy-test/debuggable-attribute.md)
