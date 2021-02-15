---
title: 'Xamarin.Essentials: Captura de tela'
description: Este documento descreve a classe de captura de Xamarin.Essentials tela no, que permite que você faça uma captura do ecrã exibido atual do aplicativo.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 01/04/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e907cf06814a5b14678e4584f9aabc56f39bb164
ms.sourcegitcommit: 995ee23d93e08dceb8754cc6c682cd2f4594345b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972260"
---
# <a name="no-locxamarinessentials-screenshot"></a>Xamarin.Essentials: Captura de tela

A classe de **captura** de tela permite que você faça uma captura da tela atual exibida do aplicativo.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-screenshot"></a>Usando captura de tela

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Em seguida, chame `CaptureAsync` para tirar uma captura de tela da tela atual do aplicativo em execução. Isso retornará um `ScreenshotResult` que pode ser usado para obter o, o `Width` `Height` e uma `Stream` das capturas de tela.


```csharp
async Task CaptureScreenshot()
{
    var screenshot = await Screenshot.CaptureAsync();
    var stream = await screenshot.OpenReadAsync();

    Image = ImageSource.FromStream(() => stream);
}
```


## <a name="api"></a>API

- [Código-fonte da captura de tela](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Screenshot)
- [Documentação da API de captura de tela](xref:Xamarin.Essentials.Screenshot)
