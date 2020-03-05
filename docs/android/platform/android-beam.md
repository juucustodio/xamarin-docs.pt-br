---
title: Beam do Android
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/06/2017
ms.openlocfilehash: aab121ed5f811baf38eed48cf891ccdf076eaf44
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78293010"
---
# <a name="android-beam"></a>Beam do Android

O feixe do Android é uma tecnologia NFC (comunicação a curta distância) introduzida no Android 4,0 que permite que os aplicativos compartilhem informações sobre o NFC quando estiverem próximos.

[Diagrama ![ilustrando dois dispositivos em informações de compartilhamento de proximidade próximas](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

O feixe do Android funciona enviando mensagens por meio de NFC quando dois dispositivos estão no intervalo. Os dispositivos sobre 4cm uns dos outros podem compartilhar dados usando o feixe do Android. Uma atividade em um dispositivo cria uma mensagem e especifica uma atividade (ou atividades) que pode lidar com o envio por push. Quando a atividade especificada estiver em primeiro plano e os dispositivos estiverem no intervalo, o feixe do Android enviará por push a mensagem para o segundo dispositivo. No dispositivo receptor, uma tentativa é invocada contendo os dados da mensagem.

O Android dá suporte a duas maneiras de definir mensagens com o feixe do Android:

- `SetNdefPushMessage`-antes de o feixe do Android ser iniciado, um aplicativo pode chamar SetNdefPushMessage para especificar um NdefMessage a ser enviado por Push por meio de NFC e a atividade que o está enviando. Esse mecanismo é melhor usado quando uma mensagem não é alterada enquanto um aplicativo está em uso.

- `SetNdefPushMessageCallback`-quando o feixe do Android é iniciado, um aplicativo pode manipular um retorno de chamada para criar um NdefMessage. Esse mecanismo permite que a criação de mensagens seja atrasada até que os dispositivos estejam no intervalo. Ele dá suporte a cenários em que a mensagem pode variar com base no que está acontecendo no aplicativo.

Em ambos os casos, para enviar dados com o transmissão do Android, um aplicativo envia um `NdefMessage`, empacotando os dados em vários `NdefRecords`. Vamos dar uma olhada nos principais pontos que devem ser resolvidos antes que possamos disparar o feixe do Android. Primeiro, trabalharemos com o estilo de retorno de chamada para criar um `NdefMessage`.

## <a name="creating-a-message"></a>Criando uma mensagem

Podemos registrar retornos de chamada com um `NfcAdapter` no método `OnCreate` da atividade. Por exemplo, supondo que um `NfcAdapter` chamado `mNfcAdapter` seja declarado como uma variável de classe na atividade, podemos escrever o código a seguir para criar o retorno de chamada que construirá a mensagem:

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

A atividade, que implementa `NfcAdapter.ICreateNdefMessageCallback`, é passada para o método `SetNdefPushMessageCallback` acima. Quando o feixe do Android é iniciado, o sistema chama `CreateNdefMessage`, a partir da qual a atividade pode construir um `NdefMessage`, como mostrado abaixo:

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

No lado do recebimento, o sistema invoca uma intenção com a ação `ActionNdefDiscovered`, da qual podemos extrair o NdefMessage da seguinte maneira:

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

Para obter um exemplo de código completo que usa o feixe do Android, mostrado em execução na captura de tela abaixo, consulte a [demonstração do feixe do Android](https://docs.microsoft.com/samples/xamarin/monodroid-samples/androidbeamdemo) na Galeria de exemplos.

[![capturas de tela de exemplo da demonstração do feixe do Android](android-beam-images/24.png)](android-beam-images/24.png#lightbox)

## <a name="related-links"></a>Links relacionados

- [Demonstração do feixe do Android (exemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/androidbeamdemo)
