---
title: Xamarin.Essentials SMS
description: A classe do Sms permite que um aplicativo abrir o aplicativo de SMS padrão com uma mensagem especificada para enviar para um destinatário.
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 460aea01381934f1862946c6e17e314560e889f2
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="xamarinessentials-sms"></a>Xamarin.Essentials SMS

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **Sms** classe permite que um aplicativo para abrir o aplicativo de SMS padrão com uma mensagem especificada para enviar para um destinatário.

## <a name="using-sms"></a>Usando o Sms

Adicione uma referência a Xamarin.Essentials em sua classe:

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

- [Código de origem do SMS](https://github.com/xamarin/Essentials/tree/master/Essentials/Sms)
- [Documentação da API do SMS](xref:Xamarin.Essentials.Sms)
