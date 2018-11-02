---
title: 'Xamarin.Essentials: SMS'
description: A classe Sms no Xamarin.Essentials permite que um aplicativo abra o aplicativo de SMS padrão com uma determinada mensagem para enviar para um destinatário.
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 276c1a03b1446c30e7047d454e06598b39bdad6c
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675230"
---
# <a name="xamarinessentials-sms"></a>Xamarin.Essentials: SMS

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

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
