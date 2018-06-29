---
title: 'Xamarin.Essentials: Exibir informações de dispositivo'
description: Este documento descreve a classe de DeviceDisplay Xamarin.Essentials, que fornece métricas de tela do dispositivo no qual o aplicativo está sendo executado.
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3060d56e14fb0d3801a96ec0fe6e24c9efda4dac
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080307"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials: Exibir informações de dispositivo

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **DeviceDisplay** classe fornece informações sobre as métricas da tela do dispositivo, o aplicativo está em execução no.

## <a name="using-devicedisplay"></a>Usando DeviceDisplay

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

## <a name="screen-metrics"></a>Métricas de tela

Além das informações básicas de dispositivos de **DeviceDisplay** classe contém informações sobre a tela e a orientação do dispositivo.

```csharp
// Get Metrics
var metrics = DeviceDisplay.ScreenMetrics;

// Orientation (Landscape, Portrait, Square, Unknown)
var orientation = metrics.Orientation;

// Rotation (0, 90, 180, 270)
var rotation = metrics.Rotation;

// Width (in pixels)
var width = metrics.Width;

// Height (in pixels)
var height = metrics.Height;

// Screen density
var density = metrics.Density;
```

O **DeviceDisplay** classe também expõe um evento que pode ser assinado que é disparado sempre que qualquer tela métricas alterações:

```csharp
public class ScreenMetricsTest
{
    public ScreenMetricsTest()
    {
        // Subscribe to changes of screen metrics
        DeviceDisplay.ScreenMetricsChanaged += OnScreenMetricsChanged;
    }

    void OnScreenMetricsChanged(ScreenMetricsChangedEventArgs  e)
    {
        // Process changes
        var metrics = e.Metrics;
    }
}
```

## <a name="api"></a>API

- [Código-fonte DeviceDisplay](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceDisplay)
- [Documentação da API DeviceDisplay](xref:Xamarin.Essentials.DeviceDisplay)
