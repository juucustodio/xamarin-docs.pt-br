---
title: Como o Xamarin.Mac funciona
description: Este documento descreve o funcionamento interno do xamarin. Mac. Em particular, ele examina construtores, gerenciamento de memória, à frente de compilação de tempo e o registrador.
ms.prod: xamarin
ms.assetid: C2053ABB-6DBF-4233-AEEA-B72FC6A81FE1
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 05/25/2017
ms.openlocfilehash: 0635e110cb2aa7bc00234d3d06df57e0fd6f966e
ms.sourcegitcommit: 6f728aa0c1775224e16c0f3e583cf843d34270f9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59893225"
---
# <a name="how-xamarinmac-works"></a>Como o Xamarin.Mac funciona

Na maioria das vezes, o desenvolvedor nunca precisará se preocupar sobre o interno "mágica" do xamarin. Mac, no entanto, ter uma compreensão aproximada de como as coisas funciona nos bastidores ajudará em ambos os interpretação documentação existente com um C# lente e depuração problemas quando eles surgem.

No xamarin. Mac, um aplicativo preenche dois mundos: Não há tempo de execução com base em Objective-C que contém instâncias de classes nativas (`NSString`, `NSApplication`, etc) e não há o C# classes gerenciadas do tempo de execução que contém instâncias de (`System.String`, `HttpClient`, etc). Entre esses dois mundos, o xamarin. Mac cria uma ponte de duas maneiras para que um aplicativo pode chamar métodos (seletores) em Objective-C (como `NSApplication.Init`) e Objective-C pode chamar o aplicativo C# métodos de volta (como métodos em um representante do aplicativo). Em geral, chamadas em Objective-C são manipuladas de forma transparente por meio **P/Invokes** e algum código de tempo de execução, o Xamarin fornece.

<a name="exposing-classes" />

## <a name="exposing-c-classes--methods-to-objective-c"></a>Expondo C# classes / métodos para Objective-C

No entanto, para Objective-C para o retorno de chamada em um aplicativo C# objetos, eles precisam ser expostos de forma que o Objective-C pode entender. Isso é feito por meio de `Register` e `Export` atributos. Veja o exemplo a seguir:

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

Neste exemplo, o tempo de execução do Objective-C agora saberá sobre uma classe chamada `MyClass` com seletores chamados `init` e `run`.

Na maioria dos casos, isso é um detalhe de implementação que o desenvolvedor pode ignorar, pois a maioria dos retornos de chamada de um aplicativo recebe será por meio de métodos substituídos nos `base` classes (como `AppDelegate`, `Delegates`, `DataSources`) ou no  **Ações** passada para APIs. Em todos esses casos `Export` atributos não são necessários no C# código.

## <a name="constructor-runthrough"></a>Resumo do construtor

Em muitos casos, o desenvolvedor precisa expor o aplicativo C# API de construção de classes para o tempo de execução do Objective-C para que possa ser instanciado de locais, por exemplo, quando chamado no Storyboard ou XIB arquivos. Aqui estão cinco construtores mais comuns usados em aplicativos xamarin. Mac:

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

Em geral, o desenvolvedor deve deixar o `IntPtr` e `NSCoder` construtores que são gerados durante a criação de alguns tipos como personalizado `NSViews` sozinho. Se precisar de xamarin. Mac para chamar um desses construtores em resposta a uma solicitação de tempo de execução do Objective-C e removê-lo, o aplicativo falhará em código nativo e pode ser difícil descobrir exatamente o problema.

## <a name="memory-management-and-cycles"></a>Ciclos e gerenciamento de memória

Gerenciamento de memória no xamarin. MAC é de várias maneiras muito semelhantes ao xamarin. IOS. Também é um tópico complexo, um além do escopo deste documento. Leia as [práticas recomendadas de desempenho e de memória](~/cross-platform/deploy-test/memory-perf-best-practices.md).

## <a name="ahead-of-time-compilation"></a>À frente da compilação de tempo

Normalmente, os aplicativos .NET não são compilados em código de máquina, quando eles são criados, em vez disso, eles são compilados para uma camada intermediária, chamada de código de IL que obtém _Just-In-Time_ (JIT) compilados para código de computador quando o aplicativo é iniciado.

O tempo que leva o tempo de execução mono para JIT compilar esse código de máquina pode diminuir o lançamento de um aplicativo xamarin. MAC em até 20%, pois demora para o código de máquina necessário a ser gerado.

Devido a limitações impostas pela Apple iOS, compilação JIT do código de IL não está disponível para xamarin. IOS. Como resultado, todos os aplicativos xamarin. IOS estão cheios _Ahead Of Time_ (AOT) compilados para código de computador durante o ciclo de build.

Novo para xamarin. MAC é a capacidade de AOT o código de IL durante o ciclo de build do aplicativo, assim como pode fazer xamarin. IOS. Xamarin. Mac usa um _híbrida_ abordagem AOT que compila a maioria do código de máquina necessário, mas permite que o tempo de execução compilar trampolines necessários e a flexibilidade para continuar a dar suporte a Reflection. Emit (e outros usos casos Atualmente, funciona no xamarin. Mac).

Há duas áreas principais que AOT pode ajudar um aplicativo xamarin. Mac:

- **Melhor os logs de falha "nativo"** - em caso de falha de um aplicativo xamarin. Mac no código nativo, que é a ocorrência comum ao fazer chamadas inválidas em APIs Cocoa (como enviar um `null` em um método que não a aceita) nativo os logs com o JIT de falha os quadros são difíceis de analisar. Uma vez que os quadros JIT não tem informações de depuração, haverá várias linhas com deslocamentos hexadecimais e nenhuma pista, o que estava acontecendo. AOT gera quadros nomeados "real" e os rastreamentos são muito mais fácil de ler. Isso também significa que o aplicativo de xamarin. Mac irá interagir melhor com ferramentas nativas como **lldb** e **instrumentos**.
- **Inicie o melhor desempenho em tempo de** - para aplicativos xamarin. Mac grandes, com um várias segundo tempo de inicialização, todo o código de compilação do JIT podem levar uma quantidade significativa de tempo. AOT faz esse trabalho antecipadamente.

### <a name="enabling-aot-compilation"></a>Habilitando a compilação AOT

AOT é habilitado no xamarin. Mac clicando duas vezes o **nome do projeto** na **Gerenciador de soluções**, a navegação para **Build do Mac** e adicionando `--aot:[options]` para o  **Argumentos mmp adicionais:** campo (onde `[options]` é uma ou mais opções para controlar o tipo AOT, consulte abaixo). Por exemplo:

![Adicionando AOT a argumentos mmp adicionais](how-it-works-images/aot01.png "AOT adicionando para argumentos mmp adicionais")

> [!IMPORTANT]
> Habilitar a AOT compilação aumenta drasticamente o tempo de compilação, às vezes, até vários minutos, mas pode melhorar os tempos de inicialização do aplicativo por uma média de 20%. Como resultado, compilação AOT deve ser habilitada apenas na **versão** compilações de um aplicativo xamarin. Mac.

### <a name="aot-compilation-options"></a>Opções de compilação AOT

Há várias opções diferentes que podem ser ajustadas ao habilitar a compilação de AOT em um aplicativo xamarin. Mac:

- `none` -Sem compilação AOT. Essa é a configuração padrão.
- `all` -AOT compila cada assembly em que o MonoBundle.
- `core` -AOT compila as `Xamarin.Mac`, `System` e `mscorlib` assemblies.
- `sdk` -AOT compila o `Xamarin.Mac` e assemblies de bibliotecas de classe Base (BCL).
- `|hybrid` -Adicionar isso a uma das opções acima habilita o AOT que permite a remoção de IL híbrido, mas será resultam em mais tempos de compilação.
- `+` – Inclui um único arquivo de compilação AOT.
- `-` – Remove um único arquivo de compilação AOT.

Por exemplo, `--aot:all,-MyAssembly.dll` permitiria que a compilação AOT em todos os assemblies no MonoBundle _exceto_ `MyAssembly.dll` e `--aot:core|hybrid,+MyOtherAssembly.dll,-mscorlib.dll` habilitaria híbrido, código AOT incluem o `MyOtherAssembly.dll` e excluindo o `mscorlib.dll`.

## <a name="partial-static-registrar"></a>Registrador estático parcial

Ao desenvolver um aplicativo xamarin. Mac, minimizando o tempo entre a conclusão de uma alteração e testá-lo pode se tornar importante para o cumprimento de prazos de desenvolvimento. Estratégias, como a modularização de bases de código e testes de unidade podem ajudar a diminuir o tempo de compilação, pois eles reduzem o número de vezes que um aplicativo exigirá uma recompilação completa cara.

Além disso, novos para xamarin. Mac e _registrador estático parcial_ (como foi a pioneira em xamarin. IOS) pode reduzir drasticamente os tempos de inicialização de um aplicativo xamarin. MAC na **depurar** configuração. Noções básicas sobre como usar o registrador estático parcial pode ser compactado horizontalmente um quase uma melhoria de 5 vezes na inicialização de depuração levará um pouco de Conhecimento sobre o que é o registrador, qual é a diferença entre estático e dinâmico e o que faz essa versão de "static parcial".

### <a name="about-the-registrar"></a>Sobre o registrador

Nos bastidores do qualquer xamarin. Mac aplicativo encontra-se a estrutura do Cocoa da Apple e o tempo de execução do Objective-C. Criar uma ponte entre esse "mundo nativo" e "mundo gerenciado" do C# é a principal responsabilidade do xamarin. Mac. Parte dessa tarefa é tratada pelo registrador, que é executado dentro de `NSApplication.Init ()` método. Esse é um motivo requer que qualquer uso de APIs Cocoa no xamarin. Mac `NSApplication.Init` a ser chamado pela primeira vez.

Trabalho do registrador é informar o tempo de execução do Objective-C da existência do aplicativo C# classes que derivam de classes, como `NSApplicationDelegate`, `NSView`, `NSWindow`, e `NSObject`. Isso requer uma verificação de todos os tipos no aplicativo para determinar o que precisa registrar e quais elementos em cada tipo de relatório.

Essa verificação pode ser feita tanto **dinamicamente**, na inicialização do aplicativo com a reflexão, ou **estaticamente**, como uma etapa de tempo de compilação. Ao escolher um tipo de registro, o desenvolvedor deve estar ciente das seguintes opções:

- Registro estático pode reduzir drasticamente os tempos de inicialização, mas pode significativamente mais lento tempos de compilações (double normalmente mais de tempo de compilação de depuração). Esse será o padrão para **versão** compilações de configuração.
- Registro dinâmico atrasa a esse trabalho até que o aplicativo Iniciar e ignora a geração de código, mas esse trabalho adicional pode criar uma pausa perceptível (pelo menos dois segundos) na inicialização do aplicativo. Isso é particularmente perceptível em compilações de configuração de depuração, que assume como padrão para o registro dinâmico e cujo reflexão é mais lento.

Registro estático parcial, introduzido pela primeira vez no xamarin. IOS 8.13, oferece ao desenvolvedor o melhor de ambas as opções. Computando previamente as informações de registro de cada elemento em `Xamarin.Mac.dll` e enviar essas informações com o xamarin. MAC em uma biblioteca estática (que só precisa ser vinculado ao tempo de compilação), a Microsoft removeu a maioria do tempo reflexão da dinâmica registrador sem afetar o tempo de compilação.

### <a name="enabling-the-partial-static-registrar"></a>Habilitando o registrador estático parcial

O registrador estático parcial está habilitado no xamarin. Mac clicando duas vezes o **nome do projeto** na **Gerenciador de soluções**, a navegação para **Build do Mac** e adicionando `--registrar:static` para o **argumentos mmp adicionais:** campo. Por exemplo:

![Adicionando o registrador estático parcial a argumentos mmp adicionais](how-it-works-images/psr01.png "adicionando o registrador estático parcial a argumentos mmp adicionais")

## <a name="additional-resources"></a>Recursos adicionais

Aqui estão algumas explicações mais detalhadas sobre como as coisas funcionam internamente:

- [Seletores de Objective-C](~/ios/internals/objective-c-selectors.md)
- [Registrar](~/ios/internals/registrar.md)
- [API unificada do Xamarin para iOS e OS X](~/cross-platform/macios/unified/index.md)
- [Conceitos básicos de theading](~/ios/app-fundamentals/threading.md)
- [Eventos, protocolos e delegados](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Sobre `newrefcount`](~/ios/internals/newrefcount.md)

