---
title: 'Xamarin.Essentials: Email'
description: A classe Email no Xamarin.Essentials permite que um aplicativo abra o aplicativo de email padrão com uma informação especificada, incluindo assunto, corpo e destinatários (PARA, CC, CCO).
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: c8d4a83caf6832f911193067324915fd6226b380
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674958"
---
# <a name="xamarinessentials-email"></a>Xamarin.Essentials: Email

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

A classe **Email** permite que um aplicativo abra o aplicativo de email padrão com uma informação especificada, incluindo assunto, corpo e destinatários (PARA, CC, CCO).

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-email"></a>Como usar Email

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de Email funciona chamando o método `ComposeAsync` com um `EmailMessage` que contém informações sobre o email:

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
            // Some other exception occurred
        }
    }
}
```

## <a name="api"></a>API

- [Código-fonte de Email](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [Documentação da API de Email](xref:Xamarin.Essentials.Email)
