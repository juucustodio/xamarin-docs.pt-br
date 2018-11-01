---
title: Introdução ao DependencyService
description: Este artigo explica como funciona o xamarin. Forms DependencyService classe para acessar os recursos de plataforma nativa.
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/15/2018
ms.openlocfilehash: 3c8cc31c21f354b60001cefb919b51bf4d42da9f
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675010"
---
# <a name="introduction-to-dependencyservice"></a>Introdução ao DependencyService

## <a name="overview"></a>Visão geral

[`DependencyService`](xref:Xamarin.Forms.DependencyService) permite que aplicativos chamem a funcionalidade específica da plataforma de código compartilhado. Essa funcionalidade permite que os aplicativos xamarin. Forms fazer qualquer coisa que um aplicativo nativo pode fazer.

`DependencyService` é um localizador de serviço. Na prática, uma interface é definida e `DependencyService` localiza a implementação correta da interface de vários projetos de plataforma.

> [!NOTE]
> Por padrão, o [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) somente resolverá as implementações de plataforma que têm construtores sem parâmetros. No entanto, um método de resolução de dependência pode ser injetado no xamarin. Forms que usa métodos de fábrica ou um contêiner de injeção de dependência para resolver as implementações de plataforma. Essa abordagem pode ser usada para resolver as implementações de plataforma que têm construtores com parâmetros. Para obter mais informações, consulte [resolução de dependência no xamarin. Forms](~/xamarin-forms/internals/dependency-resolution.md).

## <a name="how-dependencyservice-works"></a>Como funciona o DependencyService

Aplicativos xamarin. Forms precisam quatro componentes para usar `DependencyService`:

- **Interface** &ndash; a funcionalidade necessária é definida por uma interface no código compartilhado.
- **Implementação por plataforma** &ndash; Classes que implementam a interface devem ser adicionadas a cada projeto de plataforma.
- **Registro** &ndash; cada classe de implementação deve ser registrado com `DependencyService` por meio de um atributo de metadados. Habilita o registro `DependencyService` para localizar a classe de implementação e fornecê-lo no lugar da interface em tempo de execução.
- **Chamada para DependencyService** &ndash; compartilhado código precisa chamar explicitamente `DependencyService` pedir para implementações da interface.

Observe que as implementações devem ser fornecidas para cada projeto de plataforma em sua solução. Projetos de plataforma sem implementações falhará em tempo de execução.

A estrutura do aplicativo é explicada pelo diagrama a seguir:

![](introduction-images/overview-diagram.png "Estrutura de aplicativo do DependencyService")

### <a name="interface"></a>Interface

A interface de design você vai definir como você interage com a funcionalidade específica da plataforma. Tenha cuidado se você estiver desenvolvendo um componente para ser compartilhado como um componente ou pacote do NuGet. Design de API pode fazer ou interromper um pacote. O exemplo a seguir especifica uma interface simples para falar o texto que permite flexibilidade para especificar as palavras a ser falado, mas deixa a implementação seja personalizada para cada plataforma:

```csharp
public interface ITextToSpeech {
    void Speak ( string text ); //note that interface members are public by default
}
```

### <a name="implementation-per-platform"></a>Implementação por plataforma

Depois que uma interface adequada foi criada, essa interface deve ser implementada no projeto para cada plataforma de destino. Por exemplo, a classe a seguir implementa o `ITextToSpeech` interface no iOS:

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

Juntando as peças, a implementação específica da plataforma terá esta aparência:

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

Observação: que o registro é executado no nível de namespace, não no nível de classe.

#### <a name="universal-windows-platform-net-native-compilation"></a>Compilação .NET nativo da plataforma universal do Windows

Projetos da UWP que usam a opção de compilação nativa do .NET devem seguir uma [configuração ligeiramente diferentes](~/xamarin-forms/platform/windows/installation/index.md#target-invocation-exception) ao inicializar o xamarin. Forms. Compilação nativa do .NET também requer o registro ligeiramente diferentes para serviços de dependência.

No **App.xaml.cs** de arquivos, registrar manualmente cada serviço de dependência definido no projeto UWP usando o `Register<T>` método, conforme mostrado abaixo:

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// register the dependencies in the same
Xamarin.Forms.DependencyService.Register<TextToSpeechImplementation>();
```

Observação: o registro manual usando `Register<T>` é eficaz somente na versão de compilações com compilação nativa do .NET. Se você omitir essa linha, compilações de depuração ainda funcionará, mas builds de versão falhará ao carregar o serviço de dependência.

### <a name="call-to-dependencyservice"></a>Chamada para DependencyService

Depois que o projeto foi configurado com uma interface comum e implementações para cada plataforma, usar `DependencyService` para obter a implementação de direito em tempo de execução:

```csharp
DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
```

`DependencyService.Get<T>` encontrará a implementação correta da interface `T`.

### <a name="solution-structure"></a>Estrutura da solução

O [UsingDependencyService solução de exemplo](https://developer.xamarin.com/samples/UsingDependencyService/) é mostrado abaixo para iOS e Android, com as alterações de código descritas acima realçado.

 [![iOS e Android solução](introduction-images/solution-sml.png "estrutura de solução de exemplo DependencyService")](introduction-images/solution.png#lightbox "DependencyService estrutura da solução de exemplo")

> [!NOTE]
> Você **deve** fornecer uma implementação em cada projeto de plataforma. Se nenhuma implementação da Interface for registrada, o `DependencyService` não será possível resolver o `Get<T>()` método em tempo de execução.

## <a name="related-links"></a>Links relacionados

- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Amostras do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
