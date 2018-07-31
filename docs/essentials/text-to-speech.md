---
title: 'Xamarin.Essentials: texto em fala'
description: A classe TextToSpeech no habilita Xamarin.Essentials um aplicativo utilizar internos nos mecanismos de texto em fala para falar texto back do dispositivo e também a linguagens de consulta disponíveis que o mecanismo pode dar suporte.
ms.assetid: AEEF03AE-A047-4DF0-B0E8-CC8D9A7B8351
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: ba822870edafce44140caa66b01f4da242fb7779
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353607"
---
# <a name="xamarinessentials-text-to-speech"></a>Xamarin.Essentials: texto em fala

![Pré-lançamento NuGet](~/media/shared/pre-release.png)

O **TextToSpeech** classe permite que um aplicativo utilizar internos nos mecanismos de texto em fala para falar texto back do dispositivo e também a linguagens de consulta disponíveis que o mecanismo pode dar suporte.

## <a name="using-text-to-speech"></a>Usando texto em fala

Adicione uma referência ao Xamarin.Essentials em sua classe:

```csharp
using Xamarin.Essentials;
```

A funcionalidade de texto em fala funciona chamando o `SpeakAsync` método com texto e parâmetros opcionais e retorna depois que a expressão foi concluída. 

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

Esse método usa um recurso opcional `CancellationToken` para interromper a expressão depois que ele for iniciado.

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

Texto em fala colocará em fila automaticamente solicitações de fala do mesmo thread.

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

Para obter mais controle sobre como o áudio é falado novamente com `SpeakSettings` que permite definir o Volume, a densidade e a localidade.

```csharp
public async Task SpeakNow()
{
    var settings = new SpeakSettings()
        {
            Volume = .75,
            Pitch = 1.0
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

Os seguintes valores com suporte para esses parâmetros são:

| Parâmetro | Mínimo | Máximo |
| --- | :---: | :---: |
| Densidade | 0 | 2.0 |
| Volume | 0 | 1.0 |

### <a name="speech-locales"></a>Localidades de fala

Cada plataforma oferece localidades para falar texto voltar em vários idiomas e acentos. Cada plataforma tem diferentes códigos e maneiras de especificar isso, razão pela qual Essentials fornece uma plataforma cruzada `Locale` classe e uma maneira para consultá-los com `GetLocalesAsync`.

```csharp
public async Task SpeakNow()
{
    var locales = await TextToSpeech.GetLocalesAsync();

    // Grab the first locale
    var locale = locales.FirstOrDefault();

    var settings = new SpeakSettings()
        {
            Volume = .75,
            Pitch = 1.0,
            Locale = locale
        };

    await TextToSpeech.SpeakAsync("Hello World", settings);
}
```

## <a name="limitations"></a>Limitações

- Fila de expressão não é garantida se chamados por vários threads.
- Oficialmente, não há suporte para reprodução de áudio em segundo plano.

## <a name="api"></a>API

- [Código-fonte TextToSpeech](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/TextToSpeech)
- [Documentação da API TextToSpeech](xref:Xamarin.Essentials.TextToSpeech)
