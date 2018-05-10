---
title: Bloqueio de tela de Xamarin.Essentials
description: A classe ScreenLock pode solicitar para impedir que a tela fica no estado de suspensão quando o aplicativo está em execução.
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 7175362dcb7f85746ea85447936d7fe3e2fd026b
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-screen-lock"></a>Bloqueio de tela de Xamarin.Essentials

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **ScreenLock** classe poderá solicitar para impedir que a tela fica no estado de suspensão quando o aplicativo está em execução.

## <a name="using-screenlock"></a>Usando ScreenLock

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de bloqueio de tela funciona chamando o `RequestActive` e `RequestRelease` métodos para solicitar a tela de desligar.

```csharp
public class ScreenLockTest
{
    public void ToggleScreenLock()
    {
        if (ScreenLock.IsMonitoring)
            ScreenLock.RequestActive();
        else
            ScreenLock.RequestRelease;
    }
}
```

## <a name="api"></a>API

- [Código de origem de bloqueio de tela](https://github.com/xamarin/Essentials/tree/master/Essentials/ScreenLock)
- [Documentação da API de bloqueio de tela](xref:Xamarin.Essentials.ScreenLock)
