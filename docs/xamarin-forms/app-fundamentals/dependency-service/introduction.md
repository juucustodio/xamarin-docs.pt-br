---
title: Introdução ao DependencyService
description: Compreender o funcionamento do DependencyService para acessar os recursos de plataforma nativo
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
ms.openlocfilehash: 88821c5315fc338b5195e42ea4b2bc3e648e6ea1
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2018
---
# <a name="introduction-to-dependencyservice"></a>Introdução ao DependencyService

## <a name="overview"></a>Visão geral

[`DependencyService`](https://developer.xamarin.com/api/type/Xamarin.Forms.DependencyService/) permite que aplicativos chamar a funcionalidade específica de plataforma de código compartilhado. Essa funcionalidade permite que aplicativos xamarin. Forms fazer qualquer coisa que um aplicativo nativo pode fazer.

`DependencyService` é um resolvedor de dependência. Na prática, uma interface é definida e `DependencyService` localiza a implementação correta da interface de vários projetos de plataforma.

## <a name="how-dependencyservice-works"></a>Como funciona o DependencyService

Aplicativos xamarin. Forms precisará três componentes para usar `DependencyService`:

- **Interface** &ndash; a funcionalidade necessária é definida por uma interface no código compartilhado.
- **Implementação por plataforma** &ndash; Classes que implementam a interface devem ser adicionadas a cada plataforma.
- **Registro** &ndash; cada classe de implementação deve ser registrado com `DependencyService` por meio de um atributo de metadados. Permite que o registro `DependencyService` para localizar a classe de implementação e fornecê-lo no lugar da interface em tempo de execução.
- **Chamada para DependencyService** &ndash; compartilhado código precisa chamar explicitamente `DependencyService` perguntar para implementações da interface.

Observe que implementações devem ser fornecidas para cada projeto da plataforma em sua solução. Projetos de plataforma sem implementações falhará em tempo de execução.

A estrutura do aplicativo é explicada pelo seguinte diagrama:

![](introduction-images/overview-diagram.png "Estrutura de aplicativo DependencyService")

### <a name="interface"></a>Interface

A interface que você criar definirá como você interage com a funcionalidade específica de plataforma. Tenha cuidado se você estiver desenvolvendo um componente para ser compartilhado como um componente ou pacote do Nuget. Projeto de API pode fazer ou interromper um pacote. O exemplo a seguir especifica uma interface simples para falar texto que permite flexibilidade de especificar as palavras a ser falado, mas deixa a implementação seja personalizada para cada plataforma:

```csharp
public interface ITextToSpeech {
    void Speak ( string text ); //note that interface members are public by default
}
```

### <a name="implementation-per-platform"></a>Implementação por plataforma

Depois que uma interface adequada foi criada, essa interface deve ser implementada no projeto para cada plataforma de destino. Por exemplo, a seguinte classe implementa o `ITextToSpeech` interface no iOS:

```csharp
namespace UsingDependencyService.iOS
{
    public class TextToSpeech_iOS : ITextToSpeech
    {
        public void Speak (string text)
        {
            var speechSynthesizer = new AVSpeechSynthesizer ();

            var speechUtterance = new AVSpeechUtterance (text) {
                Rate = AVSpeechUtterance.MaximumSpeechRate/4,
                Voice = AVSpeechSynthesisVoice.FromLanguage ("en-US"),
                Volume = 0.5f,
                PitchMultiplier = 1.0f
            };

            speechSynthesizer.SpeakUtterance (speechUtterance);
        }
    }
}
```

### <a name="registration"></a>Registro

Cada implementação da interface precisa ser registrado com `DependencyService` com um atributo de metadados. O código a seguir registra a implementação para iOS:

```csharp
[assembly: Dependency (typeof (TextToSpeech_iOS))]
namespace UsingDependencyService.iOS
{
  ...
}
```

Juntando tudo, a implementação específica de plataforma tem esta aparência:

```csharp
[assembly: Dependency (typeof (TextToSpeech_iOS))]
namespace UsingDependencyService.iOS
{
    public class TextToSpeech_iOS : ITextToSpeech
    {
        public void Speak (string text)
        {
            var speechSynthesizer = new AVSpeechSynthesizer ();

            var speechUtterance = new AVSpeechUtterance (text) {
                Rate = AVSpeechUtterance.MaximumSpeechRate/4,
                Voice = AVSpeechSynthesisVoice.FromLanguage ("en-US"),
                Volume = 0.5f,
                PitchMultiplier = 1.0f
            };

            speechSynthesizer.SpeakUtterance (speechUtterance);
        }
    }
}
```

Observação: o registro executada no nível de namespace, e não no nível de classe.

#### <a name="universal-windows-platform-net-native-compilation"></a>Compilação nativa do Windows universal plataforma .NET

Os projetos UWP que usam a opção de compilação nativa do .NET devem seguir um [configuração ligeiramente diferentes](~/xamarin-forms/platform/windows/installation/index.md#target-invocation-exception) ao inicializar xamarin. Forms. Compilação nativa do .NET também requer registro ligeiramente diferentes para serviços de dependência.

No **App.xaml.cs** de arquivos, registrar manualmente cada serviço de dependência definido no projeto UWP usando o `Register<T>` método, conforme mostrado abaixo:

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// register the dependencies in the same
Xamarin.Forms.DependencyService.Register<TextToSpeechImplementation>();
```

Observação: o registro manual usando `Register<T>` é eficaz somente na versão compilações usando compilação nativa do .NET. Se você omitir essa linha, compilações de depuração ainda funcionarão, mas compilações de versão falhará ao carregar o serviço de dependência.

### <a name="call-to-dependencyservice"></a>Chamada para DependencyService

Depois que o projeto foi configurado com uma interface comum e implementações para cada plataforma, usar `DependencyService` para obter a implementação de direito em tempo de execução:

```csharp
DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
```

`DependencyService.Get<T>` encontra a implementação correta da interface `T`.

### <a name="solution-structure"></a>Estrutura da solução

O [UsingDependencyService solução de exemplo](https://developer.xamarin.com/samples/UsingDependencyService/) é mostrado abaixo para iOS e Android, com as alterações de código descritas acima realçado.

 [![iOS e Android solução](introduction-images/solution-sml.png "estrutura de solução de exemplo DependencyService")](introduction-images/solution.png#lightbox "DependencyService estrutura de solução de exemplo")

> [!NOTE]
> Você **deve** fornecer uma implementação em cada projeto da plataforma. Se nenhuma implementação de Interface é registrada, em seguida, o `DependencyService` não poderá resolver o `Get<T>()` método em tempo de execução.


## <a name="related-links"></a>Links relacionados

- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Amostras do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
