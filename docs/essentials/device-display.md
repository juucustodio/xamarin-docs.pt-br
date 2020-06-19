---
title: 'Xamarin.Essentials: Informações de exibição do dispositivo'
description: Este documento descreve a classe DeviceDisplay no Xamarin.Essentials , que fornece métricas de tela para o dispositivo no qual o aplicativo está sendo executado.
ms.assetid: 2821C908-C613-490D-8E8C-1BD3269FCEEA
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 11/04/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 867e6bd1828d4158f70226dbaad678f9d6037bb0
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802406"
---
# <a name="xamarinessentials-device-display-information"></a>Xamarin.Essentials: Informações de exibição do dispositivo

A classe **DeviceDisplay** fornece informações sobre as métricas de tela do dispositivo no qual o aplicativo está sendo executado e pode solicitar para evitar que a tela fique suspensa quando o aplicativo está em execução.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-devicedisplay"></a>Como usar DeviceDisplay

Adicione uma referência a Xamarin.Essentials em sua classe:

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

    void OnMainDisplayInfoChanged(object sender, DisplayInfoChangedEventArgs  e)
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

# <a name="android"></a>[Android](#tab/android)

Sem diferenças.

# <a name="ios"></a>[iOS](#tab/ios)

- O acesso a `DeviceDisplay` deve ser no thread da interface do usuário, caso contrário uma exceção será lançada. Você pode usar o [`MainThread.BeginInvokeOnMainThread`](~/essentials/main-thread.md) método para executar esse código no thread da interface do usuário.

# <a name="uwp"></a>[UWP](#tab/uwp)

Sem diferenças.

--------------

## <a name="api"></a>API

- [Código-fonte de DeviceDisplay](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/DeviceDisplay)
- [Documentação da API de DeviceDisplay](xref:Xamarin.Essentials.DeviceDisplay)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Device-Display-Information-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
