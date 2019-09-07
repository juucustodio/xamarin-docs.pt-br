---
title: Beam do Android
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/06/2017
ms.openlocfilehash: 4d294b7aee9d4a6c2118a5ff12968a7f95fb981c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757865"
---
# <a name="android-beam"></a>Beam do Android

O feixe do Android é uma tecnologia NFC (comunicação a curta distância) introduzida no Android 4,0 que permite que os aplicativos compartilhem informações sobre o NFC quando estiverem próximos.

[![Diagrama ilustrando dois dispositivos em fechar proximidades informações de compartilhamento](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

O feixe do Android funciona enviando mensagens por meio de NFC quando dois dispositivos estão no intervalo. Os dispositivos sobre 4cm uns dos outros podem compartilhar dados usando o feixe do Android. Uma atividade em um dispositivo cria uma mensagem e especifica uma atividade (ou atividades) que pode lidar com o envio por push. Quando a atividade especificada estiver em primeiro plano e os dispositivos estiverem no intervalo, o feixe do Android enviará por push a mensagem para o segundo dispositivo. No dispositivo receptor, uma tentativa é invocada contendo os dados da mensagem.

O Android dá suporte a duas maneiras de definir mensagens com o feixe do Android:

- `SetNdefPushMessage`-Antes de o feixe do Android ser iniciado, um aplicativo pode chamar SetNdefPushMessage para especificar um NdefMessage a ser enviado por Push por meio de NFC e a atividade que o está enviando. Esse mecanismo é melhor usado quando uma mensagem não é alterada enquanto um aplicativo está em uso.

- `SetNdefPushMessageCallback`-Quando o feixe do Android é iniciado, um aplicativo pode manipular um retorno de chamada para criar um NdefMessage. Esse mecanismo permite que a criação de mensagens seja atrasada até que os dispositivos estejam no intervalo. Ele dá suporte a cenários em que a mensagem pode variar com base no que está acontecendo no aplicativo.

Em ambos os casos, para enviar dados com o transmissão do Android, um `NdefMessage`aplicativo envia um, empacotando `NdefRecords`os dados em vários. Vamos dar uma olhada nos principais pontos que devem ser resolvidos antes que possamos disparar o feixe do Android. Primeiro, trabalharemos com o estilo de retorno de chamada para `NdefMessage`criar um.

## <a name="creating-a-message"></a>Criando uma mensagem

Podemos registrar retornos de chamada com `NfcAdapter` um no método da `OnCreate` atividade. Por exemplo, supondo `NfcAdapter` que `mNfcAdapter` um nome seja declarado como uma variável de classe na atividade, podemos escrever o código a seguir para criar o retorno de chamada que construirá a mensagem:

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

A atividade, que implementa `NfcAdapter.ICreateNdefMessageCallback`, é passada para o `SetNdefPushMessageCallback` método acima. Quando o feixe do Android for iniciado, o sistema `CreateNdefMessage`chamará, do qual a atividade pode `NdefMessage` construir um conforme mostrado abaixo:

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

No lado do recebimento, o sistema invoca uma intenção com a `ActionNdefDiscovered` ação, na qual podemos extrair o NdefMessage da seguinte maneira:

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

Para obter um exemplo de código completo que usa o feixe do Android, mostrado em execução na captura de tela abaixo, consulte a [demonstração do feixe do Android](https://docs.microsoft.com/samples/xamarin/monodroid-samples/androidbeamdemo) na Galeria de exemplos.

[![Capturas de tela de exemplo da demonstração do feixe do Android](android-beam-images/24.png)](android-beam-images/24.png#lightbox)

## <a name="related-links"></a>Links relacionados

- [Demonstração do feixe do Android (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/androidbeamdemo)
- [Introdução ao sanduíche de sorvete](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma Android 4,0](https://developer.android.com/sdk/android-4.0.html)
