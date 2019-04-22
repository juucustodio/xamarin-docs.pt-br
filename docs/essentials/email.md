---
title: 'Xamarin.Essentials: Email'
description: A classe Email no Xamarin.Essentials permite que um aplicativo abra o aplicativo de email padrão com uma informação especificada, incluindo assunto, corpo e destinatários (PARA, CC, CCO).
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.openlocfilehash: 06b4f4b612d0cb44e467a9da6dbee3194338027d
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58869956"
---
# <a name="xamarinessentials-email"></a>Xamarin.Essentials: Email

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

Nem todos os clientes de email para Android dão suporte para `Html`. Como não há nenhuma maneira de detectar isso, é recomendável usar `PlainText` ao enviar emails.

# <a name="iostabios"></a>[iOS](#tab/ios)

Sem diferenças entre plataformas.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Só há suporte para `PlainText`, pois se `BodyFormat` tentar enviar `Html` lançará `FeatureNotSupportedException`.

-----

## <a name="file-attachments"></a>Anexos de Arquivo

![Versão prévia do recurso](~/media/shared/preview.png)

O envio de arquivos por email está disponível como uma versão prévia experimental no Xamarin.Essentials versão 1.1.0. Esse recurso permite que um aplicativo envie arquivos por email em clientes de email no dispositivo. Para habilitar esse recurso, defina a propriedade a seguir no código de inicialização do aplicativo:

```csharp
ExperimentalFeatures.Enable(ExperimentalFeatures.EmailAttachments);
```

Depois de habilitar o recurso, qualquer arquivo poderá ser enviado por email. O Xamarin.Essentials detectará automaticamente o tipo de arquivo (MIME) e solicitará que o arquivo seja adicionado como um anexo. Cada cliente de email é diferente poderá ser compatível somente com extensões de arquivo específicas ou até mesmo não ser com nenhuma.

Aqui está um exemplo de como gravar texto no disco e adicioná-lo como um anexo de email:

```csharp
var message = new EmailMessage
{
    Subject = "Hello",
    Body = "World",
};

var fn = "Attachment.txt";
var file = Path.Combine(FileSystem.CacheDirectory, fn);
File.WriteAllText(file, "Hello World");

message.Attachments.Add(new EmailAttachment(file));

await Email.ComposeAsync(message);
```

## <a name="api"></a>API

- [Código-fonte de Email](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [Documentação da API de Email](xref:Xamarin.Essentials.Email)
