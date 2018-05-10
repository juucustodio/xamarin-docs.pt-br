---
title: Discagem telefônica Xamarin.Essentials
description: A classe PhoneDialer permite que um aplicativo abrir um link da web no navegador preferencial sistema otimizado ou navegador externo.
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 6bea1b26e43459be12272f9dd2ecc6fafba2b8b4
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-phone-dialer"></a>Discagem telefônica Xamarin.Essentials

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **PhoneDialer** classe permite que um aplicativo abrir um link da web no navegador preferencial sistema otimizado ou navegador externo.

## <a name="using-phone-dialer"></a>Usando a discagem telefônica

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de discagem telefônica funciona chamando o `Open` método com um número de telefone para abrir a discagem com. Quando `Open` é solicitado a API tentará automaticamente formatar o número com base no código de país, se especificado.

```csharp
public class PhoneDialerTest
{
    public async Task PlacePhoneCall(string number)
    {
        try
        {
            PhoneDialer.Open(number);
        }
        catch (ArgumentNullException anEx)
        {
            // Number was null or white space
        }
        catch (FeatureNotSupportedException ex)
        {
            // Phone Dialer is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

## <a name="api"></a>API

- [Discagem telefônica código-fonte](https://github.com/xamarin/Essentials/tree/master/Essentials/PhoneDialer)
- [Documentação da API de discagem telefônica](xref:Xamarin.Essentials.PhoneDialer)
