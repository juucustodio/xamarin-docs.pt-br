---
title: 'Xamarin.Essentials: Informações da tela do dispositivo'
description: Este documento descreve a classe DeviceDisplay no Xamarin.Essentials, que fornece métricas da tela do dispositivo no qual o aplicativo está em execução.
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: ebe97cf7fbb78bff17196110e835bd35ff76b826
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674880"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials: Informações da tela do dispositivo

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

A classe **DeviceDisplay** fornece informações sobre as métricas de tela do dispositivo no qual o aplicativo está em execução.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-devicedisplay"></a>Como usar DeviceDisplay

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

## <a name="screen-metrics"></a>Métricas da tela

Além das informações básicas do dispositivo, a classe **DeviceDisplay** contém informações sobre a tela e a orientação do dispositivo.

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

A classe **DeviceDisplay** também expõe um evento que pode ser assinado e que é disparado sempre que ocorre alterações nas métricas da tela:

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

## <a name="platform-differences"></a>Diferenças entre plataformas

# <a name="androidtabandroid"></a>[Android](#tab/android)

Sem diferenças.

# <a name="iostabios"></a>[iOS](#tab/ios)

* O acesso a `ScreenMetrics` deve ser no thread da interface do usuário, caso contrário uma exceção será lançada. Você pode usar o método [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) para executar esse código no thread da interface do usuário.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Sem diferenças.

--------------


## <a name="api"></a>API

- [Código-fonte de DeviceDisplay](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceDisplay)
- [Documentação da API de DeviceDisplay](xref:Xamarin.Essentials.DeviceDisplay)
