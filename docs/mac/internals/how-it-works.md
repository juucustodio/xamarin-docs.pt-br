---
title: Como funciona o Xamarin.Mac
description: Este documento descreve o funcionamento interno do Xamarin.Mac. Em particular, ele examina construtores, gerenciamento de memória, à frente de compilação e o registrador.
ms.prod: xamarin
ms.assetid: C2053ABB-6DBF-4233-AEEA-B72FC6A81FE1
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/25/2017
ms.openlocfilehash: baa60d177a7d7d070a218108b2f6779eeaf94f78
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
ms.locfileid: "30787083"
---
# <a name="how-xamarinmac-works"></a>Como funciona o Xamarin.Mac

Na maioria das vezes, o desenvolvedor nunca precisa se preocupar sobre o interno "magic" de Xamarin.Mac, no entanto, ter uma compreensão aproximada de como as coisas funciona nos bastidores ajudará no interpretar a documentação existente com uma lente c# e depurar problemas quando eles surgem.

Em Xamarin.Mac, um aplicativo une dois mundos: não há tempo de execução com base em Objective-C que contém instâncias de classes nativas (`NSString`, `NSApplication`, etc) e o tempo de execução c# que contém instâncias de classes gerenciadas (`System.String`, `HttpClient`, etc). Entre esses dois mundos, Xamarin.Mac cria uma ponte de duas vias para um aplicativo pode chamar métodos (seletores) em Objective-C (como `NSApplication.Init`) e Objective-C pode chamar métodos do aplicativo c# novamente (como métodos em um representante do aplicativo). Em geral, as chamadas em Objective-C são manipuladas transparentemente **P/Invokes** e algum código de tempo de execução fornece Xamarin.

<a name="exposing-classes" />

## <a name="exposing-c-classes--methods-to-objective-c"></a>Expondo classes c# / métodos para Objective-C

No entanto, para Objective-C para o retorno de chamada em objetos do aplicativo c#, eles precisam ser expostos de forma que possa compreender Objective-C. Isso é feito por meio de `Register` e `Export` atributos. Veja o exemplo a seguir:

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

Neste exemplo, o tempo de execução Objective-C agora saberá sobre uma classe chamada `MyClass` com seletores chamados `init` e `run`.

Na maioria dos casos, isso é um detalhe de implementação que o desenvolvedor pode ignorar, como a maioria dos retornos de chamada de um aplicativo recebe será substituído pelos métodos em `base` classes (como `AppDelegate`, `Delegates`, `DataSources`) ou no  **Ações** passados para APIs. Em todos esses casos, `Export` atributos não são necessários no código do c#.

## <a name="constructor-runthrough"></a>Construtor runthrough

Em muitos casos, o desenvolvedor precisa expor c# classes construção do aplicativo API no tempo de execução Objective-C, assim ele pode ser instanciado de locais, como quando chamado em arquivos de Storyboard ou XIB. Aqui estão os cinco construtores mais comuns usados em aplicativos de Xamarin.Mac:

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

Em geral, o desenvolvedor deve deixar o `IntPtr` e `NSCoder` construtores que são gerados durante a criação de alguns tipos de como personalizar `NSViews` sozinho. Se Xamarin.Mac precisa chamar uma esses construtores em resposta a uma solicitação de tempo de execução Objective-C e removê-lo, o aplicativo falhará em código nativo e pode ser difícil descobrir exatamente o problema.

## <a name="memory-management-and-cycles"></a>Ciclos e gerenciamento de memória

Gerenciamento de memória no Xamarin.Mac é de várias maneiras muito semelhantes ao xamarin. Também é um tópico complexo, um além do escopo deste documento. Leia o [memória e práticas recomendadas de desempenho](~/cross-platform/deploy-test/memory-perf-best-practices.md).

## <a name="ahead-of-time-compilation"></a>Em frente da compilação

Normalmente, os aplicativos .NET não compilar até o código de máquina quando eles são criados, em vez disso, eles são compilados para uma camada intermediária chamada código IL que obtém _Just-In-Time_ (JIT) compilados para código de máquina quando o aplicativo é iniciado.

O tempo que leva o tempo de execução mono para compilação JIT esse código de máquina pode reduzir a inicialização de um aplicativo Xamarin.Mac por até 20%, conforme leva tempo para o código de máquina necessário será gerado.

Devido a limitações impostas pela Apple iOS, a compilação JIT do código IL não está disponível para xamarin. Como resultado, todos os aplicativos do xamarin estão cheios _Ahead de tempo_ (AOT) compilados para código de máquina durante o ciclo de compilação.

Novo Xamarin.Mac é a capacidade de AOT o código IL durante o ciclo de compilação do aplicativo, assim como pode xamarin. Xamarin.Mac usa um _híbrida_ abordagem AOT que compila a maioria do código de máquina necessário, mas permite que o tempo de execução compilar trampolines necessários e a flexibilidade para continuar a oferecer suporte Reflection. Emit (e outros use casos atualmente funciona em Xamarin.Mac).

Há duas áreas principais que AOT pode ajudar um aplicativo Xamarin.Mac:

- **Melhor logs de falha "nativo"** - se um aplicativo de Xamarin.Mac falhar em código nativo, que é a ocorrência comum ao fazer chamadas inválidas em APIs Cocoa (como o envio de um `null` em um método que não a aceita) nativo logs com JIT de falha quadros são difíceis de analisar. Como os quadros JIT não possuem informações de depuração, haverá várias linhas com deslocamentos hexadecimais e nenhuma dica o que estava acontecendo. AOT gera quadros nomeados "real" e os rastreamentos sejam mais fáceis de ler. Isso também significa Xamarin.Mac aplicativo irá interagir melhor com ferramentas nativas como **lldb** e **instrumentos**.
- **Inicie o melhor desempenho de tempo** - para aplicativos Xamarin.Mac grandes, com vários inicialização novamente, JIT compilar todo o código podem levar uma quantidade significativa de tempo. AOT que funciona com antecedência.

### <a name="enabling-aot-compilation"></a>Habilitar compilação AOT

AOT está habilitado no Xamarin.Mac clicando duas vezes o **nome do projeto** no **Solution Explorer**, navegando para **Mac criar** e adicionando `--aot:[options]` para o  **Argumentos adicionais mmp:** campo (onde `[options]` é uma ou mais opções para controlar o tipo AOT, veja abaixo). Por exemplo:

![Adicionando AOT a argumentos adicionais mmp](how-it-works-images/aot01.png "adicionando AOT para argumentos adicionais mmp")

> [!IMPORTANT]
> Habilitando AOT compilação aumenta consideravelmente o tempo de compilação, às vezes até vários minutos, mas pode melhorar os tempos de inicialização de aplicativo por uma média de 20%. Como resultado, compilação AOT deve ser habilitada apenas na **versão** compilações de um aplicativo Xamarin.Mac.

### <a name="aot-compilation-options"></a>Opções de compilação de AOT

Há várias opções diferentes que podem ser ajustadas ao habilitar a compilação de AOT em um aplicativo de Xamarin.Mac:

- `none` -Nenhuma compilação AOT. Essa é a configuração padrão.
- `all` -AOT compila cada assembly no MonoBundle.
- `core` -AOT compila o `Xamarin.Mac`, `System` e `mscorlib` assemblies.
- `sdk` -AOT compila o `Xamarin.Mac` e assemblies de bibliotecas BCL (Base Class).
- `|hybrid` -Adicionar como uma das opções acima permite híbrida AOT que permite a remoção de IL, mas resultará em mais compilará vezes.
- `+` -Inclui uma única para a compilação de AOT.
- `-` -Remove um único arquivo de compilação de AOT.

Por exemplo, `--aot:all,-MyAssembly.dll` deve habilitar a compilação de AOT em todos os assemblies no MonoBundle _exceto_ `MyAssembly.dll` e `--aot:core|hybrid,+MyOtherAssembly.dll,-mscorlib.dll` permitiria híbrido, código AOT incluem o `MyOtherAssembly.dll` e excluindo o `mscorlib.dll`.

## <a name="partial-static-registrar"></a>Registrador estático parcial

Ao desenvolver um aplicativo Xamarin.Mac, minimizar o tempo entre a conclusão de uma alteração e testá-lo pode se tornar importante cumprimento de prazos de desenvolvimento. Estratégias, como modularização de bases de código e testes de unidade podem ajudar a diminuir o tempo de compilação, pois elas reduzem o número de vezes que um aplicativo exigirá uma recriação completa cara.

Além disso e novo para Xamarin.Mac, _parcial registrador estático_ (como pioneira por xamarin) pode reduzir drasticamente os tempos de inicialização de um aplicativo Xamarin.Mac no **depurar** configuração. Noções básicas sobre como usar o registrador estático parcial pode compactado um quase uma melhoria de 5 vezes na inicialização de depuração levará um pouco de informações básicas sobre o que é o registrador, qual é a diferença entre estático e dinâmico e o que esta versão de "static parcial" não.

### <a name="about-the-registrar"></a>Sobre o registrador

O código subjacente de qualquer Xamarin.Mac aplicativo encontra-se a estrutura de Cocoa da Apple e o tempo de execução Objective-C. Criar uma ponte entre "mundo nativo" e "mundo gerenciado" do c# é responsabilidade principal Xamarin.Mac. Parte dessa tarefa é tratado pelo registrador, que é executado dentro de `NSApplication.Init ()` método. Esse é um motivo que requer o uso das APIs de Cocoa em Xamarin.Mac `NSApplication.Init` para ser chamado primeiro.

Trabalho do registrador é informar o tempo de execução Objective-C da existência de c# classes do aplicativo que derivam de classes como `NSApplicationDelegate`, `NSView`, `NSWindow`, e `NSObject`. Isso requer uma verificação de todos os tipos no aplicativo para determinar o que precisa registrar e quais elementos em cada tipo de relatório.

Essa verificação pode ser feita **dinamicamente**, na inicialização do aplicativo com reflexão, ou **estaticamente**, como uma etapa de tempo de compilação. Ao escolher um tipo de registro, o desenvolvedor deve estar ciente das seguintes opções:

- Registro estático pode reduzir drasticamente os tempos de inicialização, mas pode reduzir tempos de compilações significativamente (double normalmente mais de tempo de compilação de depuração). Esse será o padrão para **versão** compilações de configuração.
- O registro dinâmico atrasa a esse trabalho até que o aplicativo Iniciar e ignora a geração de código, mas esse trabalho adicional pode criar uma pausa notável (pelo menos dois segundos) na inicialização do aplicativo. Isso é especialmente perceptível em compilações de configuração de depuração, que assume como padrão o registro dinâmico e cujo reflexão é mais lenta.

Registro estático parcial, introduzida no xamarin 8.13, oferece ao desenvolvedor o melhor de ambas as opções. Calculando previamente com as informações de registro de cada elemento em `Xamarin.Mac.dll` e envio essas informações com Xamarin.Mac em uma biblioteca estática (que só precisa ser vinculado no momento da compilação), a Microsoft removeu a maioria do tempo de reflexão de dinâmico registrador enquanto não afetar o tempo de compilação.

### <a name="enabling-the-partial-static-registrar"></a>Habilitando o registrador estático parcial

O registrador estático parcial é habilitado no Xamarin.Mac clicando duas vezes o **nome do projeto** no **Solution Explorer**, navegando para **Mac criar** e adicionando `--registrar:static` para o **argumentos adicionais mmp:** campo. Por exemplo:

![Adicionando o registrador estático parcial a argumentos adicionais mmp](how-it-works-images/psr01.png "adicionando o registrador estático parcial a argumentos adicionais mmp")

## <a name="additional-resources"></a>Recursos adicionais

Aqui estão algumas explicações mais detalhadas sobre como as coisas funcionam internamente:

- [Seletores de Objective-C](~/ios/internals/objective-c-selectors.md)
- [Registrar](~/ios/internals/registrar.md)
- [Xamarin Unified API para iOS e OS X](~/cross-platform/macios/unified/index.md)
- [Conceitos básicos de theading](~/ios/app-fundamentals/threading.md)
- [Delegados, protocolos e eventos](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Sobre `newrefcount`](~/ios/internals/newrefcount.md)

