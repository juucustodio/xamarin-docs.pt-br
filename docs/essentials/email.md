---
title: Email Xamarin.Essentials
description: A classe de Email permite que um aplicativo para abrir o aplicativo de email padrão com informações especificadas incluindo destinatários (para, CC, Cco), assunto e corpo.
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: a23d849131372820a73057b752908886f64b4210
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
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
