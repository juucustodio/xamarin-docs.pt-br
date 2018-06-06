---
title: 'Xamarin.Essentials: Exibir informações de dispositivo'
description: Este documento descreve a classe de DeviceDisplay Xamarin.Essentials, que fornece métricas de tela do dispositivo no qual o aplicativo está sendo executado.
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 830b96bcc21397047cb5aaacb5c568bc2ee863c4
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782366"
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

O **DeviceDisplay** classe também expõe e eventos que podem ser assinados que dispara um evento sempre que qualquer tela métricas alterações:

```csharp
public class ScreenMetricsTest
{
    public ScreenMetricsTest()
    {
        // Subscribe to changes of screen metrics
        DeviceDisplay.ScreenMetricsChanaged += OnScreenMetricsChanged;
    }

    void OnScreenMetricsChanged(ScreenMetricsChanagedEventArgs  e)
    {
        // Process changes
        var metrics = e.Metrics;
    }
}
```

## <a name="api"></a>API

- [Código-fonte DeviceDisplay](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceDisplay)
- [Documentação da API DeviceDisplay](xref:Xamarin.Essentials.DeviceDisplay)
