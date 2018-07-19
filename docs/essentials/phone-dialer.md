---
title: 'Xamarin.Essentials: A discagem telefônica'
description: A classe PhoneDialer Xamarin.Essentials permite que um aplicativo abrir um número de telefone no discador
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 34a6c80836d8cb42b1f8fd95718fe248d4701c0f
ms.sourcegitcommit: 7f2e44e6f628753e06a5fe2a3076fc2ec5baa081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39130787"
---
# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials: A discagem telefônica

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **PhoneDialer** classe permite que um aplicativo para abrir um número de telefone no discador.

## <a name="using-phone-dialer"></a>Usando a discagem telefônica

Adicione uma referência ao Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de discagem telefônica funciona chamando o `Open` método com um número de telefone para abrir a discagem com. Quando `Open` é solicitada automaticamente tentará a API formatar o número com base no código de país, se especificado.

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

- [Código-fonte de discagem telefônica](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/PhoneDialer)
- [Documentação da API de discagem de telefone](xref:Xamarin.Essentials.PhoneDialer)
