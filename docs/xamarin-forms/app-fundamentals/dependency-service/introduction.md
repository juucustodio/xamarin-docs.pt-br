---
title: Introdução a DependencyService
description: Este artigo explica como a classe DependencyService do Xamarin.Forms funciona para acessar recursos da plataforma nativa.
ms.prod: xamarin
ms.assetid: 5d019604-4f6f-4932-9b26-1fce3b4d88f8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/15/2018
ms.openlocfilehash: 3c8cc31c21f354b60001cefb919b51bf4d42da9f
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675010"
---
# <a name="introduction-to-dependencyservice"></a>Introdução a DependencyService

## <a name="overview"></a>Visão geral

O [`DependencyService`](xref:Xamarin.Forms.DependencyService) permite que os aplicativos chamem a funcionalidade específica da plataforma usando código compartilhado. Essa funcionalidade permite que os aplicativos do Xamarin.Forms façam qualquer coisa que um aplicativo nativo pode fazer.

`DependencyService` é um localizador de serviço. Na prática, uma interface é definida e `DependencyService` localiza a implementação correta da interface entre os vários projetos da plataforma.

> [!NOTE]
> Por padrão, o [`DependencyService`](xref:Xamarin.Forms.DependencyService) resolverá somente as implementações de plataforma que têm construtores sem parâmetros. No entanto, é possível injetar no Xamarin.Forms um método de resolução de dependência que um contêiner de injeção de dependências ou métodos de fábrica para resolver implementações da plataforma. Essa abordagem pode ser usada para resolver implementações da plataforma que têm construtores com parâmetros. Para obter mais informações, confira [Resolução de dependências no Xamarin.Forms](~/xamarin-forms/internals/dependency-resolution.md).

## <a name="how-dependencyservice-works"></a>Como DependencyService funciona

Aplicativos do Xamarin.Forms precisam de quatro componentes para usar `DependencyService`:

- **Interface** &ndash; A funcionalidade necessária é definida por uma interface no código compartilhado.
- **Implementação por plataforma** &ndash; Classes que implementam a interface devem ser adicionadas a cada projeto de plataforma.
- **Registro** &ndash; Cada classe de implementação deve ser registrado no `DependencyService` por meio de um atributo de metadados. O registro permite que `DependencyService` localize a classe de implementação e a forneça no lugar da interface em tempo de execução.
- **Chamada para DependencyService** &ndash; O código compartilhado precisa chamar `DependencyService` explicitamente para solicitar implementações da interface.

Observe que é necessário fornecer implementações para cada projeto de plataforma em sua solução. Projetos de plataforma sem implementações falharão em tempo de execução.

A estrutura do aplicativo é explicada pelo diagrama a seguir:

![](introduction-images/overview-diagram.png "Estrutura de aplicativo de DependencyService")

### <a name="interface"></a>Interface

A interface que você elaborar definirá como você interage com a funcionalidade específica da plataforma. Tenha cuidado se você estiver desenvolvendo um componente para ser compartilhado como um componente ou pacote do NuGet. O design da API pode levar ao sucesso ou à falha de um pacote. O exemplo a seguir especifica uma interface simples para falar o texto que permite que haja flexibilidade para especificar as palavras a serem faladas, mas deixa que a implementação seja personalizada para cada plataforma:

```csharp
public interface ITextToSpeech {
    void Speak ( string text ); //note that interface members are public by default
}
```

### <a name="implementation-per-platform"></a>Implementação por plataforma

Após uma interface adequada ser criada, essa interface deverá ser implementada no projeto para cada plataforma de destino. Por exemplo, a classe a seguir implementa a interface `ITextToSpeech` no iOS:

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

Cada implementação da interface precisa ser registrada no `DependencyService` com um atributo de metadados. O código a seguir registra a implementação para o iOS:

```csharp
[assembly: Dependency (typeof (TextToSpeech_iOS))]
namespace UsingDependencyService.iOS
{
  ...
}
```

Juntando tudo, a implementação específica da plataforma terá esta aparência:

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

Observe que o registro é executado no nível de namespace, e não no nível da classe.

#### <a name="universal-windows-platform-net-native-compilation"></a>Compilação .NET Native na Plataforma Universal do Windows

Projetos da UWP que usam a opção de compilação .NET Native devem seguir uma [configuração ligeiramente diferente](~/xamarin-forms/platform/windows/installation/index.md#target-invocation-exception) ao inicializar o Xamarin.Forms. A compilação .NET Native também requer um registro ligeiramente diferente para serviços de dependência.

No arquivo **App.xaml.cs**, registre manualmente cada serviço de dependência definido no projeto da UWP usando o método `Register<T>`, conforme mostrado abaixo:

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// register the dependencies in the same
Xamarin.Forms.DependencyService.Register<TextToSpeechImplementation>();
```

Observação: o registro manual usando `Register<T>` é eficaz somente em builds de Versão que usam a compilação .NET Native. Se você omitir essa linha, builds de Depuração ainda funcionarão, mas builds de Versão falharão ao carregar o serviço de dependência.

### <a name="call-to-dependencyservice"></a>Chamada para DependencyService

Após o projeto ter sido configurado com uma interface comum e com implementações para cada plataforma, use `DependencyService` para obter a implementação correta em tempo de execução:

```csharp
DependencyService.Get<ITextToSpeech>().Speak("Hello from Xamarin Forms");
```

`DependencyService.Get<T>` encontrará a implementação correta da interface `T`.

### <a name="solution-structure"></a>Estrutura da solução

A [solução de exemplo UsingDependencyService](https://developer.xamarin.com/samples/UsingDependencyService/) é mostrada abaixo para iOS e Android, com as alterações de código descritas acima realçadas.

 [![Solução para iOS e Android](introduction-images/solution-sml.png "Estrutura da solução de exemplo DependencyService")](introduction-images/solution.png#lightbox "Estrutura da solução de exemplo DependencyService")

> [!NOTE]
> Você **precisa** fornecer uma implementação no projeto de cada plataforma. Se nenhuma implementação da Interface estiver registrada, o `DependencyService` não poderá resolver o método `Get<T>()` em tempo de execução.

## <a name="related-links"></a>Links relacionados

- [DependencyServiceSample](https://developer.xamarin.com/samples/xamarin-forms/UsingDependencyService/)
- [Amostras do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
