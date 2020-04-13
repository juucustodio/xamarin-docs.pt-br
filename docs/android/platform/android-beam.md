---
title: Beam do Android
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/06/2017
ms.openlocfilehash: aab121ed5f811baf38eed48cf891ccdf076eaf44
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78293010"
---
# <a name="android-beam"></a>Beam do Android

O Android Beam é uma tecnologia NFC (Near Field Communication) introduzida no Android 4.0 que permite que os aplicativos compartilhem informações via NFC quando estão próximos.

[![Diagrama ilustrando dois dispositivos em informações de compartilhamento de proximidade](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

O Android Beam funciona empurrando mensagens sobre NFC quando dois dispositivos estão ao alcance. Dispositivos a cerca de 4cm uns dos outros podem compartilhar dados usando o Android Beam. Uma atividade em um dispositivo cria uma mensagem e especifica uma atividade (ou atividades) que pode lidar com a sua pressão. Quando a atividade especificada estiver em primeiro plano e os dispositivos estiverem ao alcance, o Android Beam enviará a mensagem para o segundo dispositivo. No dispositivo de recebimento, uma Intenção é invocada contendo os dados da mensagem.

O Android suporta duas maneiras de definir mensagens com o Android Beam:

- `SetNdefPushMessage`- Antes do Android Beam ser iniciado, um aplicativo pode chamar SetNdefPushMessage para especificar um NdefMessage para empurrar o NFC e a Atividade que o está empurrando. Este mecanismo é melhor usado quando uma mensagem não muda enquanto um aplicativo está em uso.

- `SetNdefPushMessageCallback`- Quando o Android Beam é iniciado, um aplicativo pode lidar com um retorno de chamada para criar um NdefMessage. Este mecanismo permite que a criação de mensagens seja adiada até que os dispositivos estejam ao alcance. Ele suporta cenários onde a mensagem pode variar de acordo com o que está acontecendo no aplicativo.

Em ambos os casos, para enviar dados com `NdefMessage`o Android Beam, um aplicativo envia um , embalagem dos dados em vários `NdefRecords`. Vamos dar uma olhada nos pontos-chave que devem ser abordados antes de podermos acionar o Android Beam. Primeiro, trabalharemos com o estilo de `NdefMessage`retorno de chamada de criação de um .

## <a name="creating-a-message"></a>Criando uma mensagem

Podemos registrar retornos de `NfcAdapter` chamada com `OnCreate` um método da Atividade. Por exemplo, `NfcAdapter` assumindo `mNfcAdapter` que um nome seja declarado como uma variável de classe na Atividade, podemos escrever o seguinte código para criar o retorno de chamada que irá construir a mensagem:

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

A Atividade, que `NfcAdapter.ICreateNdefMessageCallback`implementa, `SetNdefPushMessageCallback` é passada para o método acima. Quando o Android Beam for `CreateNdefMessage`iniciado, o sistema chamará , a partir do qual a Atividade pode construir um `NdefMessage` como mostrado abaixo:

```csharp
public NdefMessage CreateNdefMessage (NfcEvent evt)
{
    DateTime time = DateTime.Now;
    var text = ("Beam me up!\n\n" + "Beam Time: " +
        time.ToString ("HH:mm:ss"));
    NdefMessage msg = new NdefMessage (
        new NdefRecord[]{ CreateMimeRecord (
            "application/com.example.android.beam",
            Encoding.UTF8.GetBytes (text)) });
        } };
    return msg;
}

public NdefRecord CreateMimeRecord (String mimeType, byte [] payload)
{
    byte [] mimeBytes = Encoding.UTF8.GetBytes (mimeType);
    NdefRecord mimeRecord = new NdefRecord (
        NdefRecord.TnfMimeMedia, mimeBytes, new byte [0], payload);
    return mimeRecord;
}
```

## <a name="receiving-a-message"></a>Recebendo uma mensagem

No lado receptor, o sistema invoca `ActionNdefDiscovered` uma Intenção com a ação, a partir da qual podemos extrair o NdefMessage da seguinte forma:

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

Para obter um exemplo de código completo que usa o Android Beam, mostrado em execução na captura de tela abaixo, consulte a demonstração do [Android Beam](https://docs.microsoft.com/samples/xamarin/monodroid-samples/androidbeamdemo) na Galeria de Amostras.

[![Exemplos de capturas de tela da demonstração do Android Beam](android-beam-images/24.png)](android-beam-images/24.png#lightbox)

## <a name="related-links"></a>Links relacionados

- [Demonstração do feixe de androides (amostra)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/androidbeamdemo)
