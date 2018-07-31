---
title: 'Xamarin.Essentials: Exibir informações de dispositivo'
description: Este documento descreve a classe de DeviceDisplay Xamarin.Essentials, que fornece métricas de tela do dispositivo no qual o aplicativo está em execução.
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: cb42da4c8c2d0e381a5b00f7e60da6f427d19c66
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353822"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials: Exibir informações de dispositivo

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **DeviceDisplay** classe fornece informações sobre as métricas de tela do dispositivo, o aplicativo está sendo executado.

## <a name="using-devicedisplay"></a>Usando DeviceDisplay

Adicione uma referência ao Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

## <a name="screen-metrics"></a>Métricas de tela

Além das informações básicas do dispositivo a **DeviceDisplay** classe contém informações sobre a tela e a orientação do dispositivo.

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

O **DeviceDisplay** classe também expõe um evento que pode ser assinado que é disparado sempre que quaisquer alterações de métricas de tela:

```csharp
public class ScreenMetricsTest
{
    public ScreenMetricsTest()
    {
        // Subscribe to changes of screen metrics
        DeviceDisplay.ScreenMetricsChanged += OnScreenMetricsChanged;
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
