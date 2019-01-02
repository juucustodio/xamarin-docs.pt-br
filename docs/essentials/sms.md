---
title: 'Xamarin.Essentials: SMS'
description: A classe Sms no Xamarin.Essentials permite que um aplicativo abra o aplicativo de SMS padrão com uma determinada mensagem para enviar para um destinatário.
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: a7b52bac0e9e2061cf9ff277db044ab232b1e9e5
ms.sourcegitcommit: 6e84adf7358dc05f4d888ab2674de70d88214090
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2018
ms.locfileid: "53815185"
---
# <a name="xamarinessentials-sms"></a>Xamarin.Essentials: SMS

A classe **Sms** permite que um aplicativo abra o aplicativo de SMS padrão com uma determinada mensagem para enviar para um destinatário.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-sms"></a>Uso do SMS

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade do SMS funciona chamando o método `ComposeAsync` de uma `SmsMessage` que contém o destinatário da mensagem e o corpo da mensagem, sendo ambos opcionais.

```csharp
public class SmsTest
{
    public async Task SendSms(string messageText, string recipient)
    {
        try
        {
            var message = new SmsMessage(messageText, new []{ recipient });
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

Além disso, você pode passar vários destinatários para uma `SmsMessage`:

```csharp
public class SmsTest
{
    public async Task SendSms(string messageText, string[] recipients)
    {
        try
        {
            var message = new SmsMessage(messageText, recipients);
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
- [Documentação da API do SMS](xref:Xamarin.Essentials.Sms)
