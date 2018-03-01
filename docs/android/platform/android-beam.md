---
title: Emitir Android
ms.topic: article
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/06/2017
ms.openlocfilehash: bea8480c66a2ecf499375636c98511ca55ce7693
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="android-beam"></a>Emitir Android

Emitir Android é uma nova tecnologia de comunicação NFC (curta distância) no Android 4 que permite que aplicativos compartilhe informações via NFC quando nas proximidades.

[![Diagrama que ilustra dois dispositivos nas proximidades compartilhando informações](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png)

Emitir Android funciona enviando mensagens sobre NFC quando dois dispositivos estão no intervalo. Dispositivos aproximadamente 4cm uns dos outros podem compartilhar dados usando emitir Android. Cria uma mensagem de uma atividade em um dispositivo e especifica uma atividade (ou atividades) que pode lidar com envio por push-lo. Quando a atividade especificada está em primeiro plano e os dispositivos estão no intervalo, emitir Android enviará a mensagem para o segundo dispositivo. No dispositivo receptor, uma tentativa é invocada que contém os dados da mensagem.

Android oferece suporte a dois modos de mensagens de configuração com transmissão Android:

-   `SetNdefPushMessage` -Antes de emitir Android é iniciada, um aplicativo pode chamar SetNdefPushMessage para especificar um NdefMessage para enviar por push sobre NFC e a atividade que é enviar por push. Esse mecanismo é melhor usado quando uma mensagem não é alterado enquanto um aplicativo está em uso.

-   `SetNdefPushMessageCallback` -Ao emitir Android é iniciada, um aplicativo pode lidar com um retorno de chamada para criar um NdefMessage. Esse mecanismo permite a criação de mensagem deve ser atrasada até que os dispositivos estão no intervalo. Ele dá suporte a cenários em que a mensagem pode variar com base no que está acontecendo no aplicativo.


Em ambos os casos, para enviar dados de transmissão Android, um aplicativo envia uma `NdefMessage`, empacotamento de dados em vários `NdefRecords`. Vamos dar uma olhada em pontos importantes que devem ser resolvidos antes de nós pode disparar emitir Android. Primeiro, trabalharemos com o estilo de retorno de chamada de criação de um `NdefMessage`.

<a name="Creating_a_Message" />

## <a name="creating-a-message"></a>Criar uma mensagem

É possível registrar retornos de chamada com um `NfcAdapter` na atividade de `OnCreate` método. Por exemplo, supondo que uma `NfcAdapter` chamado `mNfcAdapter` é declarado como uma variável de classe na atividade, podemos escrever o código a seguir para criar o retorno de chamada que criará a mensagem:

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

A atividade, que implementa `NfcAdapter.ICreateNdefMessageCallback`, é passada para o `SetNdefPushMessageCallback` método acima. Ao emitir Android é iniciado, o sistema chamará `CreateNdefMessage`, do qual a atividade pode construir um `NdefMessage` conforme mostrado abaixo:

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

<a name="Receiving_a_Message" />

## <a name="receiving-a-message"></a>Recebendo uma mensagem

No lado de recepção, o sistema invoca uma tentativa com o `ActionNdefDiscovered` ação, do qual é possível extrair o NdefMessage da seguinte maneira:

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

Para obter um exemplo de código completo que usa emitir Android, mostrado em execução na captura de tela abaixo, consulte o [demonstração emitir Android](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/) na Galeria de exemplo.

[![Capturas de tela de exemplo de demonstração emitir Android](android-beam-images/24.png)](android-beam-images/24.png)



## <a name="related-links"></a>Links relacionados

- [Demonstração de transmissão Android (exemplo)](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/)
- [Introdução ao sabor de sorvete Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
