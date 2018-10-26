---
title: Quais drivers USB precisa depurar Android no Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: 8b996a1cc89acedc47c7169ec579dfb99dae788f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114529"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>Quais drivers USB precisa depurar Android no Windows?

## <a name="finding-usb-drivers"></a>Encontrar Drivers de USB

Para depurar em um dispositivo Android ao desenvolver no Windows; Você precisa instalar um driver USB compatível. O Gerenciador de SDK do Android inclui o "Driver de USB do Google" por padrão, que adiciona suporte para dispositivos Nexus conforme descrito aqui: [http://developer.android.com/sdk/win-usb.html](http://developer.android.com/sdk/win-usb.html)

Outros dispositivos requerem drivers USB especificamente publicados pelo fabricante do dispositivo. Alguns links para os fabricantes mais comuns estão incluídos neste guia: [http://developer.android.com/tools/extras/oem-usb.html](http://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>Alternativas

Dependendo do manfacturer, pode ser difícil de rastrear o driver USB exato necessário. Algumas alternativas para testar aplicativos Android é desenvolvido no Windows, incluindo o uso de um emulador do Android ou usando serviços externos de testes. Algumas dessas incluem:

- [Teste do App Center](https://docs.microsoft.com/appcenter/test-cloud/) - testes executados em centenas de dispositivos Android reais de serviços de nuvem.

- [Emulador do Visual Studio para Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/)

- [Depurando no Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md)

