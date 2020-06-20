---
title: 'Xamarin.Essentials: Discagem telefônica'
description: A classe PhoneDialer no Xamarin.Essentials permite que um aplicativo Abra um número de telefone na discagem
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 07/02/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d642005e9aed663570c251e955c6a3af4704ed5c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802207"
---
# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials: Discagem telefônica

A classe **PhoneDialer** permite que um aplicativo abra um número de telefone pelo discador.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-phone-dialer"></a>Como usar a Discagem telefônica

Adicione uma referência a Xamarin.Essentials em sua classe:

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

- [Código-fonte de Discagem telefônica](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/PhoneDialer)
- [Documentação da API de Discagem Telefônica](xref:Xamarin.Essentials.PhoneDialer)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Phone-Dialer-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
