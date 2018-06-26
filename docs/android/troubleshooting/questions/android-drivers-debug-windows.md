---
title: Quais drivers USB é necessário depurar Android no Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/22/2018
ms.openlocfilehash: 8b49d02b9670e66d04060375e59b005905c41bf7
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935250"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>Quais drivers USB é necessário depurar Android no Windows?

## <a name="finding-usb-drivers"></a>Encontrar os Drivers de USB

Para depurar em um dispositivo Android quando desenvolver no Windows. Você precisa instalar o driver compatível. O Gerenciador do Android SDK inclui o Driver de USB"Google" por padrão, o que adiciona suporte para dispositivos do Nexus conforme descrito aqui: [http://developer.android.com/sdk/win-usb.html](http://developer.android.com/sdk/win-usb.html)

Outros dispositivos requerem drivers USB especificamente publicados pelo fabricante do dispositivo. Alguns links para os fabricantes mais comuns estão incluídos neste guia: [http://developer.android.com/tools/extras/oem-usb.html](http://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>Alternativas

Dependendo manfacturer, pode ser difícil de controlar o driver USB exato necessário. Algumas alternativas para testar aplicativos do Android desenvolvidos no Windows, incluindo usando um emulador Android ou serviços externos de teste. Algumas dessas incluem:

- [Teste de aplicativo Center](https://docs.microsoft.com/appcenter/test-cloud/) - teste os serviços são executados em centenas de dispositivos com Android real de nuvem.

- [Emulador do Visual Studio para Android](https://www.visualstudio.com/en-us/features/msft-android-emulator-vs.aspx)

- [Depuração no emulador do Android](~/android/deploy-test/debugging/debug-on-emulator.md)

