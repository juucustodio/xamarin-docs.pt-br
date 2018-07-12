---
title: 'Xamarin.Essentials: Email'
description: A classe de Email no Xamarin.Essentials permite que um aplicativo para abrir o aplicativo de email padrão com informações incluindo assunto, corpo e os destinatários (para, CC, Cco) especificadas.
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: aea2f429126180ae3d98bc665bed5574f416ea53
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38848540"
---
# <a name="xamarinessentials-email"></a>Xamarin.Essentials: Email

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **Email** classe permite que um aplicativo para abrir o aplicativo de email padrão com informações incluindo assunto, corpo e destinatários (para, CC, Cco) especificadas.

## <a name="using-email"></a>Usando o Email

Adicione uma referência ao Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de Email funciona chamando o `ComposeAsync` método um `EmailMessage` que contém informações sobre o email:

```csharp
public class EmailTest
{
    public async Task SendEmail(string subject, string body, List<string> recipients)
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
            };
            
            await Email.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException fbsEx)
        {
            // Email is not supported on this device
        }
        catch (Exception ex)
        {
            // Some other exception occured
        }
    }
}
```

## <a name="api"></a>API

- [Código de origem do email](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [Documentação da API de email](xref:Xamarin.Essentials.Email)
