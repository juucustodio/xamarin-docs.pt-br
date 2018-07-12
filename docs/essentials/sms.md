---
title: 'Xamarin.Essentials: SMS'
description: A classe de Sms em Xamarin.Essentials permite que um aplicativo para abrir o aplicativo de SMS padrão com uma mensagem especificada para enviar para um destinatário.
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: a93a67b83ea8f435a5e3ad5d26e1d6cbbb7092f7
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815591"
---
# <a name="xamarinessentials-sms"></a>Xamarin.Essentials: SMS

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **Sms** classe permite que um aplicativo para abrir o aplicativo de SMS padrão com uma mensagem especificada para enviar para um destinatário.

## <a name="using-sms"></a>Usando o Sms

Adicione uma referência ao Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade SMS funciona chamando o `ComposeAsync` método um `SmsMessage` que contém o destinatário da mensagem e o corpo da mensagem, que são opcionais.

```csharp
public class SmsTest
{
    public async Task SendSms(string messageText, string recipient)
    {
        try
        {
            var message = new SmsMessage(messageText, recipient);
            await Sms.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException ex)
        {
            // Sms is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

## <a name="api"></a>API

- [Código-fonte do SMS](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Sms)
- [Documentação da API de SMS](xref:Xamarin.Essentials.Sms)
