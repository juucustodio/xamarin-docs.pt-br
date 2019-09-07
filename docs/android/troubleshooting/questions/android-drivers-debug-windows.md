---
title: Quais drivers USB são necessários para depurar o Android no Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: 8be3f1b8803aa7e052ebc89af51dad3b659f95f5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757324"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>Quais drivers USB são necessários para depurar o Android no Windows?

## <a name="finding-usb-drivers"></a>Localizando drivers USB

Para depurar em um dispositivo Android ao desenvolver no Windows; Você precisa instalar um driver USB compatível. O Gerenciador de SDK do Android inclui o "driver de USB do Google" por padrão, que adiciona suporte para dispositivos Nexus conforme descrito aqui:[https://developer.android.com/sdk/win-usb.html](https://developer.android.com/sdk/win-usb.html)

Outros dispositivos exigem drivers USB especificamente publicados pelo fabricante do dispositivo. Alguns links para os fabricantes mais comuns estão incluídos neste guia:[https://developer.android.com/tools/extras/oem-usb.html](https://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>Alternativas

Dependendo do manfacturer, pode ser difícil rastrear o driver USB exato necessário. Algumas alternativas para testar aplicativos Android desenvolvidos no Windows, incluindo o uso de um emulador do Android ou o uso de serviços de teste externos. Algumas dessas incluem:

- [App Center](https://docs.microsoft.com/appcenter/test-cloud/) testes – os serviços de teste na nuvem são executados em centenas de dispositivos Android reais.

- [Emulador do Visual Studio para Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/)

- [Depurando no Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md)
