---
title: Bloqueio de tela de Xamarin.Essentials
description: A classe ScreenLock pode solicitar para impedir que a tela fica no estado de suspensão quando o aplicativo está em execução.
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 0bdf75825d9c6dc594749fe7aa1e133207cfa0fa
ms.sourcegitcommit: 4db5f5c93f79f273d8fc462de2f405458b62fc02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2018
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
        if (ScreenLock.IsActive)
            ScreenLock.RequestActive();
        else
            ScreenLock.RequestRelease();
    }
}
```

## <a name="api"></a>API

- [Código de origem de bloqueio de tela](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/ScreenLock)
- [Documentação da API de bloqueio de tela](xref:Xamarin.Essentials.ScreenLock)
