---
title: 'Xamarin.Essentials: Email'
description: A classe Email no Xamarin.Essentials permite que um aplicativo abra o aplicativo de email padrão com uma informação especificada, incluindo assunto, corpo e destinatários (PARA, CC, CCO).
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3c2958cc4572c2f87c46c9edc5fc194284658f24
ms.sourcegitcommit: 704d4cfd418c17b0e85a20c33a16d2419db0be71
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51691744"
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


## <a name="platform-differences"></a>Diferenças entre plataformas

# <a name="androidtabandroid"></a>[Android](#tab/android)

Sem diferenças entre plataformas.

# <a name="iostabios"></a>[iOS](#tab/ios)

Sem diferenças entre plataformas.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Só há suporte para `PlainText`, pois se `BodyFormat` tentar enviar `Html` lançará `FeatureNotSupportedException`.

-----

## <a name="api"></a>API

- [Código-fonte de Email](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [Documentação da API de Email](xref:Xamarin.Essentials.Email)
