---
title: 'Xamarin.Essentials: Email'
description: A classe email no Xamarin.Essentials permite que um aplicativo Abra o aplicativo de email padrão com uma informação especificada, incluindo assunto, corpo e destinatários (para, CC, Cco).
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 059405d4e3219162022b3f8c0208ee5cc4ac2d38
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434537"
---
# <a name="no-locxamarinessentials-email"></a>Xamarin.Essentials: Email

A classe **Email** permite que um aplicativo abra o aplicativo de email padrão com uma informação especificada, incluindo assunto, corpo e destinatários (PARA, CC, CCO).

Para acessar a funcionalidade de **email** , a configuração específica de plataforma a seguir é necessária.

# <a name="android"></a>[Android](#tab/android)

Se a versão do Android de destino do seu projeto estiver definida como **Android 11 (API R 30)** , você deverá atualizar seu manifesto do Android com consultas que são usadas com os novos [requisitos de visibilidade de pacote](https://developer.android.com/preview/privacy/package-visibility).

Abra o arquivo **AndroidManifest.xml** na pasta **Propriedades** e adicione o seguinte dentro do nó do **manifesto**:

```xml
<queries>
  <intent>
    <action android:name="android.intent.action.SENDTO" />
    <data android:scheme="mailto" />
  </intent>
</queries>
```

# <a name="ios"></a>[iOS](#tab/ios)

Não exige mais configurações.

# <a name="uwp"></a>[UWP](#tab/uwp)

Sem diferenças entre plataformas.

-----

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

> [!TIP]
> Para usar a API de Email no iOS, é preciso executá-la em um dispositivo físico; caso contrário, uma exceção será lançada.

## <a name="using-email"></a>Como usar Email

Adicione uma referência a Xamarin.Essentials em sua classe:

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

## <a name="file-attachments"></a>Anexos de Arquivo

Esse recurso permite que um aplicativo envie arquivos por email em clientes de email no dispositivo. Xamarin.Essentials o detectará automaticamente o tipo de arquivo (MIME) e solicitará que o arquivo seja adicionado como um anexo. Cada cliente de email é diferente e só pode dar suporte a extensões de arquivo específicas ou nenhum.

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

## <a name="platform-differences"></a>Diferenças entre plataformas

# <a name="android"></a>[Android](#tab/android)

Nem todos os clientes de email para Android dão suporte para `Html`. Como não há nenhuma maneira de detectar isso, é recomendável usar `PlainText` ao enviar emails.

# <a name="ios"></a>[iOS](#tab/ios)

Sem diferenças entre plataformas.

# <a name="uwp"></a>[UWP](#tab/uwp)

Só há suporte para `PlainText`, pois se `BodyFormat` tentar enviar `Html` lançará `FeatureNotSupportedException`.

Nem todos os clientes de email dão suporte ao envio de anexos. Para obter mais informações, confira a [documentação](/windows/uwp/contacts-and-calendar/sending-email).

-----

## <a name="api"></a>API

- [Código-fonte de email](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Email)
- [Documentação da API de email](xref:Xamarin.Essentials.Email)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Email-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]