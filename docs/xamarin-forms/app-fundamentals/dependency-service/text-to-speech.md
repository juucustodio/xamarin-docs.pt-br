---
title: Implementação de texto em fala
description: Use DependencyService para chamar a API de texto em fala nativo de cada plataforma
ms.prod: xamarin
ms.assetid: 1D6164F9-4ECE-43A6-B583-1F5D5EFC1DDF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2017
ms.openlocfilehash: 67e392bb3672e54a1e2fe709af9cf5deb3dae5e8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="implementing-text-to-speech"></a>Implementação de texto em fala

Este artigo vai guiá-lo ao criar um aplicativo de plataforma cruzada usa [ `DependencyService` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) para acessar as APIs de fala nativo:

- **[Criar a Interface](#Creating_the_Interface)**  &ndash; entender como a interface é criada no código compartilhado.
- **[iOS implementação](#iOS_Implementation)**  &ndash; saber como implementar a interface em código nativo para iOS.
- **[Implementação do Android](#Android_Implementation)**  &ndash; saber como implementar a interface em código nativo para o Android.
- **[Implementação do Windows](#WindowsImplementation)**  &ndash; saber como implementar a interface em código nativo para Windows Phone e o Windows UWP (plataforma Universal).
- **[Implementando em código compartilhado](#Implementing_in_Shared_Code)**  &ndash; aprender a usar `DependencyService` para chamar a implementação nativa de código compartilhado.

O aplicativo usando `DependencyService` terá a seguinte estrutura:

![](text-to-speech-images/tts-diagram.png "Estrutura de aplicativo DependencyService")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Criando a Interface

Primeiro, crie uma interface no código compartilhado que expressa a funcionalidade que você pretende implementar. Neste exemplo, a interface contém um único método, `Speak`:

```csharp
public interface ITextToSpeech
{
    void Speak (string text);
}
```

Codificando a essa interface no código compartilhado permitirá que o aplicativo xamarin. Forms acessar a APIs de fala em cada plataforma.

> [!NOTE]
> Classes que implementam a interface devem ter um construtor sem parâmetros para trabalhar com o `DependencyService`.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementação do iOS

A interface deve ser implementada em cada projeto de aplicativo específico da plataforma. Observe que a classe tem um construtor sem parâmetros para que o `DependencyService` pode criar novas instâncias.

```csharp
[assembly: Dependency(typeof(TextToSpeechImplementation))]
namespace DependencyServiceSample.iOS
{

    public class TextToSpeechImplementation : ITextToSpeech
    {
        public TextToSpeechImplementation() { }

        public void Speak(string text)
        {
            var speechSynthesizer = new AVSpeechSynthesizer();
            var speechUtterance = new AVSpeechUtterance(text)
            {
                Rate = AVSpeechUtterance.MaximumSpeechRate / 4,
                Voice = AVSpeechSynthesisVoice.FromLanguage("en-US"),
                Volume = 0.5f,
                PitchMultiplier = 1.0f
            };

            speechSynthesizer.SpeakUtterance(speechUtterance);
        }
    }
}
```

O `[assembly]` atributo registra a classe como uma implementação do `ITextToSpeech` interface, o que significa que `DependencyService.Get<ITextToSpeech>()` pode ser usado no código compartilhado para criar uma instância dele.

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementação do Android

O código do Android é mais complexo do que a versão do iOS: requer a classe de implementação para herdar de específico do Android `Java.Lang.Object` e implementar o `IOnInitListener` interface também. Ele também requer acesso ao contexto atual do Android, que é exposto pelo `MainActivity.Instance` propriedade.

```csharp
[assembly: Dependency(typeof(TextToSpeechImplementation))]
namespace DependencyServiceSample.Droid
{
    public class TextToSpeechImplementation : Java.Lang.Object, ITextToSpeech, TextToSpeech.IOnInitListener
    {
        TextToSpeech speaker;
        string toSpeak;

        public void Speak(string text)
        {
            toSpeak = text;
            if (speaker == null)
            {
                speaker = new TextToSpeech(MainActivity.Instance, this);
            }
            else
            {
                speaker.Speak(toSpeak, QueueMode.Flush, null, null);
            }
        }

        public void OnInit(OperationResult status)
        {
            if (status.Equals(OperationResult.Success))
            {
                speaker.Speak(toSpeak, QueueMode.Flush, null, null);
            }
        }
    }
}
```

O `[assembly]` atributo registra a classe como uma implementação do `ITextToSpeech` interface, o que significa que `DependencyService.Get<ITextToSpeech>()` pode ser usado no código compartilhado para criar uma instância dele.

<a name="WindowsImplementation" />

## <a name="windows-phone-and-universal-windows-platform-implementation"></a>Windows Phone e a implementação da plataforma Universal do Windows

Windows Phone e a plataforma Universal do Windows tem uma API de fala no `Windows.Media.SpeechSynthesis` namespace. A única limitação é que se lembrar de escala de **microfone** recurso no manifesto, caso contrário, acesso a fala APIs estão bloqueados.

```csharp
[assembly:Dependency(typeof(TextToSpeechImplementation))]
public class TextToSpeechImplementation : ITextToSpeech
{
    public async void Speak(string text)
    {
        var mediaElement = new MediaElement();
        var synth = new Windows.Media.SpeechSynthesis.SpeechSynthesizer();
        var stream = await synth.SynthesizeTextToStreamAsync(text);

        mediaElement.SetSource(stream, stream.ContentType);
        mediaElement.Play();
    }
}
```

O `[assembly]` atributo registra a classe como uma implementação do `ITextToSpeech` interface, o que significa que `DependencyService.Get<ITextToSpeech>()` pode ser usado no código compartilhado para criar uma instância dele.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementando em código compartilhado

Agora podemos escrever e testar o código compartilhado que acessa a interface de texto em fala. Esta página simple inclui um botão que dispara a funcionalidade de fala. Ele usa o `DependencyService` para obter uma instância do `ITextToSpeech` interface &ndash; em tempo de execução, esta instância será a implementação específica de plataforma que tenha acesso completo para o SDK nativo.

```csharp
public MainPage ()
{
    var speak = new Button {
        Text = "Hello, Forms !",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
    speak.Clicked += (sender, e) => {
        DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
    };
    Content = speak;
}
```

Executar este aplicativo no iOS, Android, ou as plataformas do Windows e pressionando que o botão resultará no aplicativo falar com você, usando a SDK de fala nativo em cada plataforma.

 ![iOS e Android botão de texto em fala](text-to-speech-images/running.png "exemplo de texto em fala")


## <a name="related-links"></a>Links relacionados

- [Usando DependencyService (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample/)
- [Pasta de trabalho de texto em fala](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/text-to-speech/text-to-speech.workbook)
