---
title: 'Xamarin.Essentials: Text-to-Speech'
description: A classe TextToSpeech no Xamarin.Essentials permite que um aplicativo utilize os mecanismos internos de conversão de texto em fala para repetir o texto do dispositivo e, também, consulte os idiomas disponíveis, compatíveis com o mecanismo.
ms.assetid: AEEF03AE-A047-4DF0-B0E8-CC8D9A7B8351
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: a7f40fc652c6f02d68a9f01dcdd0f4132893a6ca
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898531"
---
# <a name="xamarinessentials-text-to-speech"></a>Xamarin.Essentials: Text-to-Speech

A classe **TextToSpeech** permite que um aplicativo utilize os mecanismos internos de conversão de texto em fala para repetir o texto do dispositivo e, também, consulte os idiomas disponíveis, compatíveis com o mecanismo.

## <a name="get-started"></a>Introdução

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-text-to-speech"></a>Uso da Conversão de Texto em Fala

Adicione uma referência ao Xamarin.Essentials na classe:

```csharp
using Xamarin.Essentials;
```

A Conversão de Texto em Fala funciona chamando o método `SpeakAsync` com texto e parâmetros opcionais e retorna depois que a expressão é concluída.

```csharp
public async Task SpeakNowDefaultSettings()
{
    await TextToSpeech.SpeakAsync("Hello World");

    // This method will block until utterance finishes.
}

public void SpeakNowDefaultSettings2()
{
    TextToSpeech.SpeakAsync("Hello World").ContinueWith((t) =>
    {
        // Logic that will run after utterance finishes.

    }, TaskScheduler.FromCurrentSynchronizationContext());
}
```

Esse método usa um recurso opcional `CancellationToken` para interromper a expressão após ela ser iniciada.

```csharp
CancellationTokenSource cts;
public async Task SpeakNowDefaultSettings()
{
    cts = new CancellationTokenSource();
    await TextToSpeech.SpeakAsync("Hello World", cancelToken: cts.Token);

    // This method will block until utterance finishes.
}

public void CancelSpeech()
{
    if (cts?.IsCancellationRequested ?? false)
        return;

    cts.Cancel();
}
```

A Conversão de Texto em Fala automaticamente enfileirará as solicitações de voz do mesmo thread.

```csharp
bool isBusy = false;
public void SpeakMultiple()
{
    isBusy = true;
    Task.Run(async () =>
    {
        await TextToSpeech.SpeakAsync("Hello World 1");
        await TextToSpeech.SpeakAsync("Hello World 2");
        await TextToSpeech.SpeakAsync("Hello World 3");
        isBusy = false;
    });

    // or you can query multiple without a Task:
    Task.WhenAll(
        TextToSpeech.SpeakAsync("Hello World 1"),
        TextToSpeech.SpeakAsync("Hello World 2"),
        TextToSpeech.SpeakAsync("Hello World 3"))
        .ContinueWith((t) => { isBusy = false; }, TaskScheduler.FromCurrentSynchronizationContext());
}
```

### <a name="speech-settings"></a>Configurações de fala

Para obter mais controle sobre como o áudio é falado com `SpeechOptions`, que permite definir o volume, o tom e a localidade.

```csharp
public async Task SpeakNow()
{
    var settings = new SpeechOptions()
        {
            Volume = .75,
            Pitch = 1.0
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

Veja a seguir os valores com suporte para esses parâmetros:

| Parâmetro | Mínimo | Máximo |
| --- | :---: | :---: |
| Densidade | 0 | 2.0 |
| Volume | 0 | 1.0 |

### <a name="speech-locales"></a>Localidades de fala

Cada plataforma suporta diferentes localidades, para responder em diferentes idiomas e sotaques. As plataformas têm diferentes códigos e formas de especificar a localidade e é por isso que o Xamarin.Essentials fornece uma classe `Locale` de plataforma cruzada e uma maneira de consultá-los com `GetLocalesAsync`.

```csharp
public async Task SpeakNow()
{
    var locales = await TextToSpeech.GetLocalesAsync();

    // Grab the first locale
    var locale = locales.FirstOrDefault();

    var settings = new SpeechOptions()
        {
            Volume = .75,
            Pitch = 1.0,
            Locale = locale
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

## <a name="limitations"></a>Limitações

- A fila de expressão não terá garantia se for chamada através de múltiplos threads.
- Oficialmente, não há suporte para a reprodução de áudio em segundo plano.

## <a name="api"></a>API

- [Código-fonte TextToSpeech](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/TextToSpeech)
- [Documentação da API do TextToSpeech](xref:Xamarin.Essentials.TextToSpeech)
