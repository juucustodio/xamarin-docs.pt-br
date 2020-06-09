---
title: Como o Xamarin.Mac funciona
description: Este documento descreve o funcionamento interno do Xamarin. Mac. Em particular, ele analisa os construtores, o gerenciamento de memória, antecipadamente a compilação do tempo e o registrador.
ms.prod: xamarin
ms.assetid: C2053ABB-6DBF-4233-AEEA-B72FC6A81FE1
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 05/25/2017
ms.openlocfilehash: 05bb9a5552022ea1eb5cd92df90659f7ebacb7cc
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571643"
---
# <a name="how-xamarinmac-works"></a>Como o Xamarin.Mac funciona

Na maioria das vezes, o desenvolvedor nunca precisará se preocupar com a "mágica" interna do Xamarin. Mac, no entanto, ter uma compreensão aproximada de como as coisas funcionam nos bastidores ajudará a interpretar a documentação existente com uma lente em C# e problemas de depuração quando surgirem.

No Xamarin. Mac, um aplicativo se une a dois mundos: há o tempo de execução baseado em Objective-C contendo instâncias de classes nativas ( `NSString` , `NSApplication` etc.) e há o tempo de execução do C# contendo instâncias de classes gerenciadas ( `System.String` , `HttpClient` etc.). Entre esses dois mundos, o Xamarin. Mac cria uma ponte de duas vias para que um aplicativo possa chamar métodos (seletores) em Objective-C (como `NSApplication.Init` ) e Objective-C pode chamar os métodos C# do aplicativo de volta (como métodos em um delegado de aplicativo). Em geral, as chamadas para Objective-C são manipuladas de forma transparente por meio de **P/Invokes** e um código de tempo de execução que o Xamarin oferece.

<a name="exposing-classes"></a>

## <a name="exposing-c-classes--methods-to-objective-c"></a>Expondo classes/métodos C# para Objective-C

No entanto, para o Objective-C chamar de volta para os objetos C# de um aplicativo, eles precisam ser expostos de forma que o Objective-C possa entender. Isso é feito por meio `Register` dos `Export` atributos e. Veja o exemplo a seguir:

```csharp
[Register ("MyClass")]
public class MyClass : NSObject
{
   [Export ("init")]
   public MyClass ()
   {
   }

   [Export ("run")]
   public void Run ()
   {
   }
}
```

Neste exemplo, o tempo de execução do Objective-C agora saberá sobre uma classe chamada `MyClass` com seletores chamados `init` e `run` .

Na maioria dos casos, esse é um detalhe de implementação que o desenvolvedor pode ignorar, pois a maioria dos retornos de chamada que um aplicativo recebe será por meio de métodos substituídos em `base` classes (como `AppDelegate` , `Delegates` , `DataSources` ) ou em **ações** passadas para APIs. Em todos esses casos, os `Export` atributos não são necessários no código C#.

## <a name="constructor-runthrough"></a>Resumo do Construtor

Em muitos casos, o desenvolvedor precisará expor a API de construção de classes C# do aplicativo para o tempo de execução Objective-C para que ele possa ser instanciado de locais como quando chamado em arquivos storyboard ou XIB. Aqui estão os cinco construtores mais comuns usados em aplicativos Xamarin. Mac:

```csharp
// Called when created from unmanaged code
public CustomView (IntPtr handle) : base (handle)
{
   Initialize ();
}

// Called when created directly from a XIB file
[Export ("initWithCoder:")]
public CustomView (NSCoder coder) : base (coder)
{
   Initialize ();
}

// Called from C# to instance NSView with a Frame (initWithFrame)
public CustomView (CGRect frame) : base (frame)
{
}

// Called from C# to instance NSView without setting the frame (init)
public CustomView () : base ()
{
}

// This is a special case constructor that you call on a derived class when the derived called has an [Export] constructor.
// For example, if you call init on NSString then you don’t want to call init on NSObject.
public CustomView () : base (NSObjectFlag.Empty)
{
}
```

Em geral, o desenvolvedor deve deixar os `IntPtr` `NSCoder` construtores e que são gerados ao criar alguns tipos, como somente personalizado `NSViews` . Se o Xamarin. Mac precisar chamar um desses construtores em resposta a uma solicitação de tempo de execução Objective-C e você o tiver removido, o aplicativo falhará dentro do código nativo e poderá ser difícil descobrir exatamente o problema.

## <a name="memory-management-and-cycles"></a>Gerenciamento e ciclos de memória

O gerenciamento de memória no Xamarin. Mac é de muitas maneiras muito semelhante ao Xamarin. iOS. Ele também é um tópico complexo, um além do escopo deste documento. Leia as [práticas recomendadas de memória e desempenho](~/cross-platform/deploy-test/memory-perf-best-practices.md).

## <a name="ahead-of-time-compilation"></a>Compilação antecipada de tempo

Normalmente, os aplicativos .NET não se compilam no código do computador quando são criados, em vez de serem compilados em uma camada intermediária chamada código de IL que obtém JIT ( _just-in-time_ ) compilado para código do computador quando o aplicativo é iniciado.

O tempo que demora o tempo de execução do mono para compilar JIT esse código de computador pode retardar a inicialização de um aplicativo Xamarin. Mac em até 20%, pois leva tempo para que o código de computador necessário seja gerado.

Devido às limitações impostas pela Apple no iOS, a compilação JIT do código IL não está disponível para o Xamarin. iOS. Como resultado, todo o aplicativo Xamarin. iOS é compilado na AOT (vida _útil_ completa) para o código do computador durante o ciclo de compilação.

Novo no Xamarin. Mac é a capacidade de AOT o código de IL durante o ciclo de compilação do aplicativo, assim como o Xamarin. iOS pode. O Xamarin. Mac usa uma abordagem AOT _híbrida_ que compila a maioria do código de máquina necessário, mas permite que o tempo de execução compile o trampolines necessário e a flexibilidade para continuar a dar suporte a Reflection. Emit (e outros casos de uso que funcionam atualmente no Xamarin. Mac).

Há duas áreas principais em que a AOT pode ajudar um aplicativo Xamarin. Mac:

- **Melhores logs de falhas "nativos"** – se um aplicativo Xamarin. Mac falhar no código nativo, que é ocorrência comum ao fazer chamadas inválidas em APIs Cocoa (como enviar um `null` para um método que não o aceita), os logs de falha nativos com quadros JIT são difíceis de analisar. Como os quadros JIT não têm informações de depuração, haverá várias linhas com deslocamentos hexadecimais e nenhuma pista sobre o que estava acontecendo. A AOT gera quadros nomeados "reais" e os rastreamentos são muito mais fáceis de ler. Isso também significa que o aplicativo Xamarin. Mac irá interagir melhor com ferramentas nativas como **lldb** e **instrumentos**.
- **Melhor desempenho do tempo de inicialização** -para aplicativos Xamarin. Mac grandes, com um tempo de inicialização de vários segundos, a compilação JIT de todo o código pode levar um tempo significativo. A AOT faz isso funcionar antecipadamente.

### <a name="enabling-aot-compilation"></a>Habilitando a compilação AOT

A AOT está habilitada no Xamarin. Mac clicando duas vezes no **nome do projeto** no **Gerenciador de soluções**, navegando para o **Build do Mac** e adicionando `--aot:[options]` ao campo **MMP Arguments adicionais:** (em que `[options]` é uma ou mais opções para controlar o tipo de AOT, veja abaixo). Por exemplo:

![Adicionando a AOT a argumentos MMP adicionais](how-it-works-images/aot01.png "Adicionando a AOT a argumentos MMP adicionais")

> [!IMPORTANT]
> A habilitação da compilação AOT aumenta drasticamente o tempo de compilação, às vezes, até vários minutos, mas pode melhorar os tempos de inicialização do aplicativo em uma média de 20%. Como resultado, a compilação AOT só deve ser habilitada em compilações de **versão** de um aplicativo Xamarin. Mac.

### <a name="aot-compilation-options"></a>Opções de compilação AOT

Há várias opções diferentes que podem ser ajustadas ao habilitar a compilação AOT em um aplicativo Xamarin. Mac:

- `none`-Sem compilação AOT. Essa é a configuração padrão.
- `all`-AOT compila cada assembly no grupo de monoconjunto.
- `core`-AOT compila os `Xamarin.Mac` `System` `mscorlib` assemblies e.
- `sdk`-AOT compila o e os `Xamarin.Mac` assemblies da BCL (biblioteca de classes base).
- `|hybrid`-Adicionar isso a uma das opções acima habilita a AOT híbrida, que permite a remoção de IL, mas resultará em tempos de compilação mais longos.
- `+`-Inclui um único arquivo para compilação AOT.
- `-`-Remove um único arquivo da compilação AOT.

Por exemplo, `--aot:all,-MyAssembly.dll` habilitaria a compilação AOT em todos os assemblies no grupo de monopacote _, exceto_ `MyAssembly.dll` e `--aot:core|hybrid,+MyOtherAssembly.dll,-mscorlib.dll` permitiria híbrido, o código AOT incluiria `MyOtherAssembly.dll` e excluindo o `mscorlib.dll` .

## <a name="partial-static-registrar"></a>Registrador parcialmente estático

Ao desenvolver um aplicativo Xamarin. Mac, minimizar o tempo entre a conclusão de uma alteração e o teste pode se tornar importante para atender aos prazos de desenvolvimento. Estratégias como a modularidade de bases de código e testes de unidade podem ajudar a diminuir os tempos de compilação, pois reduzem o número de vezes que um aplicativo exigirá uma recompilação completa cara.

Além disso, e novo no Xamarin. Mac, o _registrador parcial_ (como pioneiro pelo Xamarin. Ios) pode reduzir drasticamente os tempos de inicialização de um aplicativo Xamarin. Mac na configuração de **depuração** . Entender como usar o registrador estático parcial pode ter um aperfeiçoamento quase uma melhoria de 5x na inicialização de depuração levará um pouco de fundo sobre o que o registrador é, qual é a diferença entre estático e dinâmico e o que essa versão "estática parcial" faz.

### <a name="about-the-registrar"></a>Sobre o registrador

Nos bastidores de qualquer aplicativo Xamarin. Mac está a estrutura Cocoa da Apple e do tempo de execução do Objective-C. Criar uma ponte entre esse "mundo nativo" e o "mundo gerenciado" do C# é a principal responsabilidade do Xamarin. Mac. Parte dessa tarefa é tratada pelo registrador, que é executado dentro do `NSApplication.Init ()` método. Essa é uma razão pela qual qualquer uso de APIs Cocoa no Xamarin. Mac precisa `NSApplication.Init` ser chamado primeiro.

O trabalho do registrador é informar o tempo de execução do Objective-C da existência das classes C# do aplicativo que derivam de classes como `NSApplicationDelegate` , `NSView` , `NSWindow` e `NSObject` . Isso requer uma verificação de todos os tipos no aplicativo para determinar o que precisa de registro e quais elementos em cada tipo relatar.

Essa verificação pode ser feita **dinamicamente**, na inicialização do aplicativo com reflexão ou **estaticamente**, como uma etapa de tempo de compilação. Ao escolher um tipo de registro, o desenvolvedor deve estar ciente do seguinte:

- O registro estático pode reduzir drasticamente os tempos de inicialização, mas pode retardar as compilações de forma significativa (normalmente mais do que o tempo de compilação de depuração dupla). Esse será o padrão para compilações de configuração de **versão** .
- O registro dinâmico atrasa esse trabalho até que o aplicativo seja iniciado e ignore a geração de código, mas esse trabalho adicional pode criar uma pausa perceptível (pelo menos dois segundos) na inicialização do aplicativo. Isso é especialmente perceptível em compilações de configuração de depuração, que assume o padrão de registro dinâmico e cuja reflexão é mais lenta.

O registro estático parcial, introduzido pela primeira vez no Xamarin. iOS 8,13, oferece ao desenvolvedor o melhor das duas opções. Ao pré-testar previamente as informações de registro de cada elemento no `Xamarin.Mac.dll` e no envio dessas informações com o Xamarin. Mac em uma biblioteca estática (que só precisa ser vinculada no momento da compilação), a Microsoft removeu a maior parte do tempo de reflexão do registrador dinâmico, sem afetar o tempo de compilação.

### <a name="enabling-the-partial-static-registrar"></a>Habilitando o registrador estático parcial

O registrador de estática parcial está habilitado no Xamarin. Mac clicando duas vezes no **nome do projeto** no **Gerenciador de soluções**, navegando para o Build do **Mac** e adicionando `--registrar:static` ao campo **argumentos adicionais do MMP:** . Por exemplo:

![Adicionando o registrador estático parcial a argumentos MMP adicionais](how-it-works-images/psr01.png "Adicionando o registrador estático parcial a argumentos MMP adicionais")

## <a name="additional-resources"></a>Recursos adicionais

Aqui estão algumas explicações mais detalhadas de como as coisas funcionam internamente:

- [Seletores de Objective-C](~/ios/internals/objective-c-selectors.md)
- [Registrador](~/ios/internals/registrar.md)
- [API Unificada Xamarin para iOS e OS X](~/cross-platform/macios/unified/index.md)
- [Conceitos básicos do theading](~/ios/app-fundamentals/threading.md)
- [Delegados, protocolos e eventos](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Pensar`newrefcount`](~/ios/internals/newrefcount.md)
