---
title: 'Xamarin.Essentials: Bloqueio de tela'
description: Este documento descreve a classe de ScreenLock Xamarin.Essentials, que pode solicitar para impedir que a tela que fica no estado de suspensão quando o aplicativo está em execução.
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3c8110b7abc86fe1d12485579f134997718540e6
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38848564"
---
# <a name="xamarinessentials-screen-lock"></a>Xamarin.Essentials: Bloqueio de tela

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **ScreenLock** classe pode solicitar para impedir que a tela que fica no estado de suspensão quando o aplicativo está em execução.

## <a name="using-screenlock"></a>Usando ScreenLock

Adicione uma referência ao Xamarin.Essentials em sua classe:

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

- [Código-fonte de bloqueio de tela](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/ScreenLock)
- [Documentação da API de bloqueio de tela](xref:Xamarin.Essentials.ScreenLock)
