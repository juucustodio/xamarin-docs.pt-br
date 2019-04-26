---
title: Quais drivers USB são necessários para depurar o Android no Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: 85045967f5c63eb39c45f917b957d2a393a3a068
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60945554"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>Quais drivers USB são necessários para depurar o Android no Windows?

## <a name="finding-usb-drivers"></a>Encontrar Drivers de USB

Para depurar em um dispositivo Android ao desenvolver no Windows; Você precisa instalar um driver USB compatível. O Gerenciador de SDK do Android inclui o "Driver de USB do Google" por padrão, que adiciona suporte para dispositivos Nexus conforme descrito aqui: [https://developer.android.com/sdk/win-usb.html](https://developer.android.com/sdk/win-usb.html)

Outros dispositivos requerem drivers USB especificamente publicados pelo fabricante do dispositivo. Alguns links para os fabricantes mais comuns estão incluídos neste guia: [https://developer.android.com/tools/extras/oem-usb.html](https://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>Alternativas

Dependendo do manfacturer, pode ser difícil de rastrear o driver USB exato necessário. Algumas alternativas para testar aplicativos Android é desenvolvido no Windows, incluindo o uso de um emulador do Android ou usando serviços externos de testes. Algumas dessas incluem:

- [Teste do App Center](https://docs.microsoft.com/appcenter/test-cloud/) - testes executados em centenas de dispositivos Android reais de serviços de nuvem.

- [Emulador do Visual Studio para Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/)

- [Depurando no Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md)

