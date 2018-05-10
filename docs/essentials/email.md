---
title: Email Xamarin.Essentials
description: A classe de Email permite que um aplicativo para abrir o aplicativo de email padrão com informações especificadas incluindo destinatários (para, CC, Cco), assunto e corpo.
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 80503c5e887538bca40a19c7b26b981850d2bd92
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-email"></a>Email Xamarin.Essentials

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **Email** classe permite que um aplicativo para abrir o aplicativo de email padrão com informações especificadas incluindo destinatários (para, CC, Cco), assunto e corpo.

## <a name="using-email"></a>Usando o Email

Adicione uma referência a Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de Email funciona chamando o `ComposeAsync` método um `EmailMessage` que contém informações sobre o email:

```csharp
public class EmailTest
{
    public async Task SendEmail(string subject, string, body, List<string> recipients)
    {
        try
        {
            var message = new EmailMessage
            {
                Subject = subject,
                Body = body,
                To = recipients,
                //Cc = ccRecipients,
                //Bcc = bccRecipients
            }
            await Email.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException fbsEx)
        {
            // Sms is not supported on this device
        }
        catch (Exception ex)
        {
            // Some other exception occured
        }
    }
}
```

## <a name="api"></a>API

- [Código de origem do email](https://github.com/xamarin/Essentials/tree/master/Essentials/Email)
- [Documentação da API de email](xref:Xamarin.Essentials.Email)
