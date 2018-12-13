---
title: Implementando a conversão de texto em fala
description: Este artigo explica como usar a classe DependencyService do Xamarin.Forms para fazer chamadas na API de conversão de texto em fala nativa de cada plataforma.
ms.prod: xamarin
ms.assetid: 1D6164F9-4ECE-43A6-B583-1F5D5EFC1DDF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/18/2017
ms.openlocfilehash: 6d1948214b97a1b536b07b6420c32e4d27124518
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2018
ms.locfileid: "38997537"
---
# <a name="implementing-text-to-speech"></a>Implementando a conversão de texto em fala

Este artigo orientará você na criação de um aplicativo multiplataforma que usa [`DependencyService`](xref:Xamarin.Forms.DependencyService) para acessar APIs de conversão de texto em fala nativas:

- **[Criando a Interface](#Creating_the_Interface)** &ndash; compreenda como a interface é criada em código compartilhado.
- **[Implementação de iOS](#iOS_Implementation)** &ndash; saiba como implementar a interface em código nativo para iOS.
- **[Implementação de Android](#Android_Implementation)** &ndash; saiba como implementar a interface em código nativo para Android.
- **[Implementação de UWP](#WindowsImplementation)** &ndash; saiba como implementar a interface em código nativo para a UWP (Plataforma Universal do Windows).
- **[Implementação em código compartilhado](#Implementing_in_Shared_Code)** &ndash; saiba como usar `DependencyService` para fazer chamadas na implementação nativa usando código compartilhado.

O aplicativo que usa `DependencyService` terá a seguinte estrutura:

![](text-to-speech-images/tts-diagram.png "Estrutura de aplicativo de DependencyService")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Criando a Interface

Primeiro, crie no código compartilhado uma interface que expressa a funcionalidade que você planeja implementar. Neste exemplo, a interface contém um único método, `Speak`:

```csharp
public interface ITextToSpeech
{
    void Speak (string text);
}
```

A codificação nessa interface no código compartilhado permitirá que o aplicativo Xamarin.Forms acesse as APIs de fala em cada plataforma.

> [!NOTE]
> Classes que implementam a interface devem ter um construtor sem parâmetros para trabalhar com `DependencyService`.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implementação de iOS

A interface deve ser implementada em cada projeto de aplicativo específico da plataforma. Observe que a classe tem um construtor sem parâmetros, de modo que `DependencyService` pode criar novas instâncias.

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

O atributo `[assembly]` registra a classe como uma implementação da interface de `ITextToSpeech`, o que significa que `DependencyService.Get<ITextToSpeech>()` pode ser usado no código compartilhado para criar uma instância dele.

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implementação de Android

O código do Android é mais complexo do que a versão do iOS: ele exige a implementação da classe para herdar do `Java.Lang.Object` específico ao Android e implementar a interface de `IOnInitListener`. Ele também precisa ter acesso ao contexto do Android atual, que é exposto pela propriedade `MainActivity.Instance`.

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

O atributo `[assembly]` registra a classe como uma implementação da interface de `ITextToSpeech`, o que significa que `DependencyService.Get<ITextToSpeech>()` pode ser usado no código compartilhado para criar uma instância dele.

<a name="WindowsImplementation" />

## <a name="universal-windows-platform-implementation"></a>Implementação na Plataforma Universal do Windows

A Plataforma Universal do Windows tem uma API de fala no namespace `Windows.Media.SpeechSynthesis`. A única ressalva é que é necessário se lembrar de marcar a capacidade **Microfone** no manifesto; caso contrário, o acesso às APIs de fala será bloqueado.

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

O atributo `[assembly]` registra a classe como uma implementação da interface de `ITextToSpeech`, o que significa que `DependencyService.Get<ITextToSpeech>()` pode ser usado no código compartilhado para criar uma instância dele.

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Implementação em código compartilhado

Agora, podemos escrever e testar o código compartilhado que acessa a interface de conversão de texto em fala. Essa página simples inclui um botão que dispara a funcionalidade de fala. Ela usa `DependencyService` para obter uma instância da interface &ndash; de `ITextToSpeech` em tempo de execução. Essa instância será a implementação específica da plataforma com acesso completo ao SDK nativo.

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

Executar esse aplicativo no iOS, no Android ou na UWP e pressionar o botão fará com que o aplicativo fale com você usando o SDK de fala nativo de cada plataforma.

 ![Botão de conversão de texto em fala do iOS e do Android](text-to-speech-images/running.png "Exemplo de conversão de texto em fala")


## <a name="related-links"></a>Links relacionados

- [Usando DependencyService (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample/)

