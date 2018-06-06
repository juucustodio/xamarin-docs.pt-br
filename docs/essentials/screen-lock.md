---
title: 'Xamarin.Essentials: Bloqueio de tela'
description: Este documento descreve a classe de ScreenLock Xamarin.Essentials, que pode pedir para impedir que a tela fica no estado de suspensão quando o aplicativo está em execução.
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3c8110b7abc86fe1d12485579f134997718540e6
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782902"
---
# <a name="xamarinessentials-screen-lock"></a>Xamarin.Essentials: Bloqueio de tela

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
        if (!ScreenLock.IsActive)
            ScreenLock.RequestActive();
        else
            ScreenLock.RequestRelease();
    }
}
```

## <a name="api"></a>API

- [Código de origem de bloqueio de tela](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/ScreenLock)
- [Documentação da API de bloqueio de tela](xref:Xamarin.Essentials.ScreenLock)
