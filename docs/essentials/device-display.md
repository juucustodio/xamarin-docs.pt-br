---
title: 'Xamarin.Essentials: Informações da tela do dispositivo'
description: Este documento descreve a classe DeviceDisplay no Xamarin.Essentials, que fornece métricas da tela do dispositivo no qual o aplicativo está em execução.
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: d3102f0a4ed5f16c77c4a4768feb4a1565f2dd1a
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898884"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials: Informações da tela do dispositivo

A classe **DeviceDisplay** fornece informações sobre as métricas de tela do dispositivo no qual o aplicativo está sendo executado e pode solicitar para evitar que a tela fique suspensa quando o aplicativo está em execução.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-devicedisplay"></a>Como usar DeviceDisplay

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

## <a name="main-display-info"></a>Informações principais de Tela

Além das informações básicas do dispositivo, a classe **DeviceDisplay** contém informações sobre a tela e a orientação do dispositivo.

```csharp
// Get Metrics
var mainDisplayInfo = DeviceDisplay.MainDisplayInfo;

// Orientation (Landscape, Portrait, Square, Unknown)
var orientation = mainDisplayInfo.Orientation;

// Rotation (0, 90, 180, 270)
var rotation = mainDisplayInfo.Rotation;

// Width (in pixels)
var width = mainDisplayInfo.Width;

// Height (in pixels)
var height = mainDisplayInfo.Height;

// Screen density
var density = mainDisplayInfo.Density;
```

A classe **DeviceDisplay** também expõe um evento que pode ser assinado e que é disparado sempre que ocorre alterações nas métricas da tela:

```csharp
public class DisplayInfoTest
{
    public DisplayInfoTest()
    {
        // Subscribe to changes of screen metrics
        DeviceDisplay.MainDisplayInfoChanged += OnMainDisplayInfoChanged;
    }

    void OnMainDisplayInfoChanged(DisplayInfoChangedEventArgs  e)
    {
        // Process changes
        var displayInfo = e.DisplayInfo;
    }
}
```

A classe **DeviceDisplay** expõe uma `bool` propriedade chamada `KeepScreenOn` que pode ser definida para tentar evitar o bloqueio ou desligamento da tela do dispositivo.

```csharp
public class KeepScreenOnTest
{
    public void ToggleScreenLock()
    {
        DeviceDisplay.KeepScreenOn = !DeviceDisplay.KeepScreenOn;
    }
}
```

## <a name="platform-differences"></a>Diferenças entre plataformas

# <a name="androidtabandroid"></a>[Android](#tab/android)

Sem diferenças.

# <a name="iostabios"></a>[iOS](#tab/ios)

* O acesso a `DeviceDisplay` deve ser no thread da interface do usuário, caso contrário uma exceção será lançada. Você pode usar o método [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) para executar esse código no thread da interface do usuário.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Sem diferenças.

--------------


## <a name="api"></a>API

- [Código-fonte de DeviceDisplay](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/DeviceDisplay)
- [Documentação da API de DeviceDisplay](xref:Xamarin.Essentials.DeviceDisplay)
