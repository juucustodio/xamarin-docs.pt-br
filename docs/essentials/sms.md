---
title: 'Xamarin.Essentials: SMS'
description: A classe SMS no Xamarin.Essentials permite que um aplicativo Abra o aplicativo SMS padrão com uma mensagem especificada para enviar a um destinatário.
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d981a7ed2bffbbff12cf69ee4d0cda27ce319040
ms.sourcegitcommit: 3a15d9b29d65139b18dcf0871fe00cffb2a56357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91353376"
---
# <a name="no-locxamarinessentials-sms"></a>Xamarin.Essentials: SMS

A classe **Sms** permite que um aplicativo abra o aplicativo de SMS padrão com uma determinada mensagem para enviar para um destinatário.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

Para acessar a funcionalidade do **SMS** , é necessária a configuração específica da plataforma a seguir.

# <a name="android"></a>[Android](#tab/android)

Se a versão do Android de destino do seu projeto estiver definida como **Android 11 (API R 30)** , você deverá atualizar seu manifesto do Android com consultas que são usadas com os novos [requisitos de visibilidade de pacote](https://developer.android.com/preview/privacy/package-visibility).

Abra o arquivo **AndroidManifest.xml** na pasta **Propriedades** e adicione o seguinte dentro do nó do **manifesto**:

```xml
<queries>
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <data android:scheme="smsto"/>
  </intent>
</queries>
```

# <a name="ios"></a>[iOS](#tab/ios)

Não exige mais configurações.

# <a name="uwp"></a>[UWP](#tab/uwp)

Sem diferenças entre plataformas.

-----

## <a name="using-sms"></a>Uso do SMS

Adicione uma referência a Xamarin.Essentials em sua classe:

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

- [Código-fonte do SMS](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Sms)
- [Documentação da API do SMS](xref:Xamarin.Essentials.Sms)

## <a name="related-video"></a>Vídeo relacionados

> [!Video https://channel9.msdn.com/Shows/XamarinShow/SMS-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
