---
title: Implementando o texto em fala
description: Este artigo explica como usar a classe do xamarin. Forms DependencyService para chamar a API de texto em fala nativo de cada plataforma.
ms.prod: xamarin
ms.assetid: 1D6164F9-4ECE-43A6-B583-1F5D5EFC1DDF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2017
ms.openlocfilehash: d39902f2269d3eb241b48831b8eb1b181ff11e7a
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997537"
---
# <a name="implementing-text-to-speech"></a>Implementando o texto em fala

Este artigo vai guiá-lo conforme você cria um aplicativo de plataforma cruzada que usa [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) para acessar APIs nativas do texto em fala:

- **[Criando a Interface](#Creating_the_Interface)**  &ndash; entender como a interface é criada em código compartilhado.
- **[Implementação do iOS](#iOS_Implementation)**  &ndash; Saiba como implementar a interface em código nativo para iOS.
- **[Implementação do Android](#Android_Implementation)**  &ndash; Saiba como implementar a interface em código nativo para Android.
- **[Implementação de UWP](#WindowsImplementation)**  &ndash; Saiba como implementar a interface em código nativo para Universal Windows Platform (UWP).
- **[Implementando em código compartilhado](#Implementing_in_Shared_Code)**  &ndash; Saiba como usar `DependencyService` para chamar a implementação nativa a partir do código compartilhado.

O aplicativo usando `DependencyService` terá a seguinte estrutura:

![](text-to-speech-images/tts-diagram.png "Estrutura de aplicativo do DependencyService")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Criando a Interface

Primeiro, crie uma interface no código compartilhado que expresse a funcionalidade que você planeja implementar. Neste exemplo, a interface contém um único método, `Speak`:

```csharp
public interface ITextToSpeech
{
    void Speak (string text);
}
```

Codificação em relação a essa interface no código compartilhado permitirá que o aplicativo xamarin. Forms acessar a APIs de fala em cada plataforma.

> [!NOTE]
> Classes que implementam a interface devem ter um construtor sem parâmetros para trabalhar com o `DependencyService`.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementação do iOS

A interface deve ser implementada em cada projeto de aplicativo específico da plataforma. Observe que a classe tem um construtor sem parâmetros, de modo que o `DependencyService` pode criar novas instâncias.

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

O código do Android é mais complexo do que a versão do iOS: ele exige a implementação da classe para herdar de específicas do Android `Java.Lang.Object` e implementar o `IOnInitListener` interface também. Ele também exige acesso ao contexto Android atual, que é exposto pelo `MainActivity.Instance` propriedade.

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

## <a name="universal-windows-platform-implementation"></a>Implementação da plataforma universal do Windows

A plataforma Universal do Windows tem uma API de fala no `Windows.Media.SpeechSynthesis` namespace. A única limitação é lembrar-se de marcar a **microfone** recurso no manifesto, caso contrário, acesso ao reconhecimento do fala APIs são bloqueadas.

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

Agora podemos escrever e testar o código compartilhado que acessa a interface do texto em fala. Esta página simple inclui um botão que dispara a funcionalidade de fala. Ele usa o `DependencyService` para obter uma instância das `ITextToSpeech` interface &ndash; em tempo de execução essa instância será a implementação específica da plataforma que tem acesso completo para o SDK nativo.

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

Executando esse aplicativo no iOS, Android ou a UWP e pressionando o botão resultará no aplicativo para você, usando o SDK da fala nativo em cada plataforma de fala.

 ![iOS e Android botão de texto em fala](text-to-speech-images/running.png "exemplo de texto em fala")


## <a name="related-links"></a>Links relacionados

- [Usando o DependencyService (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample/)
- [Pasta de trabalho de texto em fala](https://developer.xamarin.com/workbooks/xamarin-forms/application-fundamentals/text-to-speech/text-to-speech.workbook)
