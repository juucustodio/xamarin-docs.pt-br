---
title: Iniciador do Xamarin.Essentials
description: A classe de inicializador Xamarin.Essentials permite que um aplicativo para abrir um URI pelo sistema.
ms.assetid: BABF40CC-8BEE-43FD-BE12-6301DF27DD33
author: jamesmontemagno
ms.author: jamont
ms.date: 07/25/2018
ms.openlocfilehash: 252bb873c1494265aafb2285057490ca29ce7419
ms.sourcegitcommit: bf51592be39b2ae3d63d029be1d7745ee63b0ce1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39573627"
---
# <a name="xamarinessentials-launcher"></a>Xamarin.Essentials: inicializador

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **iniciador** classe permite que um aplicativo para abrir um URI pelo sistema. Isso geralmente é usado quando vinculação em esquemas URI personalizadas do outro aplicativo. Se você estiver procurando para abrir o navegador em um site, você deve consultar a **[navegador](open-browser.md)** API.

## <a name="using-launcher"></a>Usando o iniciador

Adicione uma referência ao Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

Para usar a iniciador funcionalidade chamada a `OpenAsync` método e passar em um `string` ou `Uri` para abrir. Opcionalmente, o `CanOpenAsync` método pode ser usado para verificar se o esquema URI pode ser tratado por um aplicativo no dispositivo.

```csharp
public class LauncherTest
{
    public async Task OpenRideShareAsync()
    {
        var supportsUri = await Launcher.CanOpenAsync("lyft://");
        if (supportsUri)
            await Launcher.OpenAsync("lyft://ridetype?id=lyft_line");
    }
}
```

## <a name="api"></a>API

- [Código-fonte do iniciador](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Launcher)
- [Documentação da API do iniciador](xref:Xamarin.Essentials.Launcher)
