---
title: Suporte a recursos e a plataformas do Xamarin.Essentials
description: O Xamarin.Essentials oferece uma API única entre plataformas que funciona com qualquer aplicativo iOS, Android ou UWP e que pode ser acessado no código compartilhado, independentemente da forma como a interface do usuário é criada.
ms.assetid: 63FA28A5-6F52-4CB7-AF39-8DF7B436B5A4
author: jamesmontemagno
ms.author: jamont
ms.date: 08/20/2019
ms.openlocfilehash: b12606317fd6cbc9a6a0f59364270595f1250abf
ms.sourcegitcommit: ab51d32f4ea0e0d4701f0bf2f1465c9323cd070b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70887348"
---
# <a name="platform-support"></a>Suporte de plataforma

O Xamarin.Essentials é compatível com os seguintes sistemas operacionais e plataformas:

| Plataforma | Versão |
| --- | --- |
| Android | 4.4 (API 19) ou superior |
| iOS |10.0 ou superior |
| Tizen | 4.0 ou superior |
| tvOS | 10.0 ou superior |
| watchOS | 4.0 ou superior |
| UWP | 10.0.16299.0 ou superior |

> [!NOTE]
> * A Tizen tem o apoio oficial da equipe de desenvolvedores da Samsung.
> * tvOS e watchOS têm cobertura de API limitada, confira o guia de recursos para obter mais informações.

## <a name="feature-support"></a>Suporte a recursos

O Xamarin.Essentials sempre tenta trazer recursos novos para todas as plataformas, mas, às vezes, há limitações relacionadas ao dispositivo. Veja abaixo um guia de quais recursos têm suporte em cada plataforma.

Guia de ícones:

* ✔– Suporte total
* ⚠ – Suporte limitado
* ❌ – Sem suporte

| Recurso | Android | iOS | UWP | watchOS | tvOS | Tizen |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| [Acelerômetro](accelerometer.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ❌ | ✔ |
| [Informações do aplicativo](app-information.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [Barômetro](barometer.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ❌ | ✔ |
| [Bateria](battery.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ⚠ | ⚠ |
| [Área de transferência](clipboard.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ❌ |
| [Conversores de cor](color-converters.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [Bússola](compass.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ✔ |
| [Conectividade](connectivity.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [Detectar movimento](detect-shake.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [Informações sobre DeviceDisplay](device-display.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ❌ |
| [Informações do dispositivo](device-information.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [Email](email.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ✔ |
| [Auxiliares do sistema de arquivos](file-system-helpers.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [Lanterna](flashlight.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ✔ |
| [Geocódigo](geocoding.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [Geolocalização](geolocation.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ✔ |
| [Giroscópio](gyroscope.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ❌ | ✔ |
| [Inicializador](launcher.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ✔ |
| [Magnetômetro](magnetometer.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ❌ | ✔ |
| [MainThread](main-thread.md?content=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [Mapas](maps.md?content=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ❌ | ✔ |
| [Abrir Navegador](open-browser.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ✔ |
| [Sensor de orientação](orientation-sensor.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ❌ | ✔ |
| [Discagem telefônica](phone-dialer.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ✔ |
| [Extensões de plataforma](platform-extensions.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [Preferências](preferences.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [Armazenamento seguro](secure-storage.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [Compartilhe](share.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ✔ |
| [SMS](sms.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ✔ |
| [Conversão de Texto em Fala](text-to-speech.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [Conversores de unidade](unit-converters.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [Monitoramento de versões](version-tracking.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ |
| [Vibrar](vibrate.md?context=xamarin/xamarin-forms) | ✔ | ✔ | ✔ | ❌ | ❌ | ✔ |
