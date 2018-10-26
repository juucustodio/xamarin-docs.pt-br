---
title: Beam do Android
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/06/2017
ms.openlocfilehash: 13a0a0d9c6a9d1d5f49020b1a8096f5e054d415c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114919"
---
# <a name="android-beam"></a>Beam do Android

Beam do Android é uma tecnologia de (NFC comunicação) introduzida no Android 4.0 que permite que os aplicativos compartilhem informações por NFC quando nas proximidades.

[![Diagrama que ilustra dois dispositivos nas proximidades, compartilhamento de informações](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

Beam do Android funciona enviando mensagens ao longo de NFC quando dois dispositivos estiverem no intervalo. Dispositivos cerca de 4cm uns dos outros podem compartilhar dados usando Beam do Android. Cria uma mensagem de uma atividade em um dispositivo e especifica uma atividade (ou atividades) que pode lidar com enviá-la. Quando a atividade especificada está em primeiro plano e os dispositivos estão no intervalo, Beam do Android enviará a mensagem para o segundo dispositivo. No dispositivo de recebimento, uma intenção é invocada que contém os dados da mensagem.

Android dá suporte a duas maneiras de mensagens de configuração com Beam do Android:

-   `SetNdefPushMessage` -Antes Beam do Android é iniciado, um aplicativo pode chamar SetNdefPushMessage para especificar um NdefMessage para enviar por push por NFC e a atividade que está enviando por push. Esse mecanismo é melhor usado quando uma mensagem não é alterado enquanto um aplicativo está em uso.

-   `SetNdefPushMessageCallback` -Quando Beam do Android é iniciado, um aplicativo pode manipular um retorno de chamada para criar um NdefMessage. Esse mecanismo permite a criação de mensagem deve ser atrasada até que os dispositivos estão no intervalo. Ele dá suporte a cenários em que a mensagem pode variar com base no que está acontecendo no aplicativo.


Em ambos os casos, para enviar os dados com Beam do Android, um aplicativo envia uma `NdefMessage`, Empacotando os dados em vários `NdefRecords`. Vamos dar uma olhada nos pontos principais que devem ser abordadas antes que podemos disparar Beam do Android. Primeiro, trabalharemos com o estilo de retorno de chamada de criação de um `NdefMessage`.


## <a name="creating-a-message"></a>Criando uma mensagem

Podemos pode registrar retornos de chamada com um `NfcAdapter` na atividade de `OnCreate` método. Por exemplo, supondo que um `NfcAdapter` chamado `mNfcAdapter` é declarado como uma variável de classe na atividade, podemos escrever o código a seguir para criar o retorno de chamada que construirão a mensagem:

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

A atividade, que implementa `NfcAdapter.ICreateNdefMessageCallback`, é passado para o `SetNdefPushMessageCallback` método acima. Quando Beam do Android é iniciado, o sistema chamará `CreateNdefMessage`, na qual a atividade pode construir um `NdefMessage` conforme mostrado abaixo:

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


## <a name="receiving-a-message"></a>Receber uma mensagem

No lado de recepção, o sistema invoca uma intenção com a `ActionNdefDiscovered` ação, do qual podemos pode extrair o NdefMessage da seguinte maneira:

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

Para obter um exemplo de código completo que usa Beam do Android, mostrado em execução na captura de tela abaixo, consulte a [demonstração Beam do Android](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/) na Galeria de exemplo.

[![Capturas de tela de exemplo da demonstração Beam do Android](android-beam-images/24.png)](android-beam-images/24.png#lightbox)



## <a name="related-links"></a>Links relacionados

- [Demonstração do Beam do Android (amostra)](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/)
- [Apresentando o Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Plataforma 4.0 Android](http://developer.android.com/sdk/android-4.0.html)
