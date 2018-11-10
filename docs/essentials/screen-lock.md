---
title: 'Xamarin.Essentials: Bloqueio de tela'
description: Este documento descreve a classe ScreenLock no Xamarin.Essentials, que pode solicitar que a tela não fique no estado de suspensão quando o aplicativo estiver em execução.
ms.assetid: 6B67C114-315E-4199-AA72-3F90E85A4909
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3bf8c949650cf9f039a5a516366a90e717dc944b
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675309"
---
# <a name="xamarinessentials-screen-lock"></a>Xamarin.Essentials: Bloqueio de tela

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

A classe **ScreenLock** pode solicitar que a tela não fique no estado de suspensão quando o aplicativo estiver em execução.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-screenlock"></a>Como usar ScreenLock

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de bloqueio de tela funciona chamando os métodos `RequestActive` e `RequestRelease` para impedir o desligamento da tela.

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

- [Código-fonte de Bloqueio de tela](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/ScreenLock)
- [Documentação da API de Bloqueio de tela](xref:Xamarin.Essentials.ScreenLock)
