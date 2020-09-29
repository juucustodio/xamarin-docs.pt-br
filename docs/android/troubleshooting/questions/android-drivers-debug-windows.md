---
title: Quais drivers USB são necessários para depurar o Android no Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 36EC7341-A2A4-409C-BD4F-330BAC505123
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: c07f9099f3b76ed86a235883ce335ce19a426b99
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457881"
---
# <a name="what-usb-drivers-do-i-need-to-debug-android-on-windows"></a>Quais drivers USB são necessários para depurar o Android no Windows?

## <a name="finding-usb-drivers"></a>Localizando drivers USB

Para depurar em um dispositivo Android ao desenvolver no Windows; Você precisa instalar um driver USB compatível. O Gerenciador de SDK do Android inclui o "driver de USB do Google" por padrão, que adiciona suporte para dispositivos Nexus conforme descrito aqui: [https://developer.android.com/sdk/win-usb.html](https://developer.android.com/sdk/win-usb.html)

Outros dispositivos exigem drivers USB especificamente publicados pelo fabricante do dispositivo. Alguns links para os fabricantes mais comuns estão incluídos neste guia: [https://developer.android.com/tools/extras/oem-usb.html](https://developer.android.com/tools/extras/oem-usb.html)

## <a name="alternatives"></a>Alternativas

Dependendo do manfacturer, pode ser difícil rastrear o driver USB exato necessário. Algumas alternativas para testar aplicativos Android desenvolvidos no Windows, incluindo o uso de um emulador do Android ou o uso de serviços de teste externos. Entre eles estão:

- [App Center](/appcenter/test-cloud/) testes – os serviços de teste na nuvem são executados em centenas de dispositivos Android reais.

- [Emulador do Visual Studio para Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/)

- [Depurando no Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md)