---
title: 'Xamarin.Essentials: Discagem telefônica'
description: A classe PhoneDialer no Xamarin.Essentials permite que um aplicativo abra um número de telefone pelo discador
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.author: jamont
ms.date: 07/02/2019
ms.openlocfilehash: 0b00307759d95b8e2efe27bd9a17d2c6efd3d5c0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "67538684"
---
# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials: Discagem telefônica

A classe **PhoneDialer** permite que um aplicativo abra um número de telefone pelo discador.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-phone-dialer"></a>Como usar a Discagem telefônica

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de Discagem telefônica chama o método `Open` com um número de telefone para abrir o discador. Quando `Open` é solicitada, a API automaticamente tenta formatar o número com base no código de país, caso tenha sido especificado.

```csharp
public class PhoneDialerTest
{
    public void PlacePhoneCall(string number)
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

- [Código-fonte de Discagem telefônica](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/PhoneDialer)
- [Documentação da API de Discagem Telefônica](xref:Xamarin.Essentials.PhoneDialer)
