---
title: Marshaling de exceção no Xamarin. iOS
description: Este documento descreve como trabalhar com exceções nativas e gerenciadas em um aplicativo Xamarin. iOS. Ele aborda os problemas que podem ocorrer e uma solução para esses problemas.
ms.prod: xamarin
ms.assetid: BE4EE969-C075-4B9A-8465-E393556D8D90
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/05/2017
ms.openlocfilehash: 07b39f87b6eeb0fc24486be83573a721abc07966
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84572397"
---
# <a name="exception-marshaling-in-xamarinios"></a>Marshaling de exceção no Xamarin. iOS

_O Xamarin. iOS contém novos eventos para ajudar a responder a exceções, especialmente em código nativo._

Tanto o código gerenciado quanto o Objective-C têm suporte para exceções de tempo de execução (cláusulas try/catch/finally).

No entanto, suas implementações são diferentes, o que significa que as bibliotecas de tempo de execução (o tempo de execução mono e as bibliotecas de tempo de execução Objective-C) têm problemas quando precisam lidar com exceções e, em seguida, executar código escrito em outras linguagens.

Este documento explica os problemas que podem ocorrer e as possíveis soluções.

Ele também inclui um projeto de exemplo, [marshaling de exceção](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling), que pode ser usado para testar diferentes cenários e suas soluções.

## <a name="problem"></a>Problema

O problema ocorre quando uma exceção é gerada e, durante o desenrolamento da pilha, um quadro é encontrado, o que não corresponde ao tipo de exceção que foi lançada.

Um exemplo típico disso para Xamarin. iOS ou Xamarin. Mac é quando uma API nativa gera uma exceção Objective-C e, em seguida, essa exceção Objective-C deve ser tratada de alguma forma quando o processo de desenrolamento de pilha atingir um quadro gerenciado.

A ação padrão é não fazer nada. Para o exemplo acima, isso significa deixar os quadros gerenciados desenrolados de tempo de execução Objective-C. Isso é problemático, pois o tempo de execução do Objective-C não sabe como desenrolar quadros gerenciados; por exemplo, ele não executará nenhuma `catch` `finally` cláusula or nesse quadro.

### <a name="broken-code"></a>Código rompido

Considere o seguinte exemplo de código:

``` csharp
var dict = new NSMutableDictionary ();
dict.LowLevelSetObject (IntPtr.Zero, IntPtr.Zero); 
```

Isso gerará um NSInvalidArgumentException Objective-C no código nativo:

```
NSInvalidArgumentException *** setObjectForKey: key cannot be nil
```

E o rastreamento de pilha será algo assim:

```
0   CoreFoundation          __exceptionPreprocess + 194
1   libobjc.A.dylib         objc_exception_throw + 52
2   CoreFoundation          -[__NSDictionaryM setObject:forKey:] + 1015
3   libobjc.A.dylib         objc_msgSend + 102
4   TestApp                 ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
5   TestApp                 Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr)
6   TestApp                 ExceptionMarshaling.Exceptions.ThrowObjectiveCException ()
```

Os quadros 0-3 são quadros nativos, e o desenrolador de pilha no tempo de execução Objective-C _pode_ desenrolar esses quadros. Em particular, ele executará qualquer cláusula Objective-C `@catch` ou `@finally` .

No entanto, o desenrolador Objective-C _não_ é capaz de desenrolar corretamente os quadros gerenciados (quadros 4-6), pois os quadros serão desorganizados, mas a lógica de exceção gerenciada não será executada.

Isso significa que geralmente não é possível capturar essas exceções da seguinte maneira:

```csharp
try {
    var dict = new NSMutableDictionary ();
    dict.LowLevelSetObject (IntPtr.Zero, IntPtr.Zero);
} catch (Exception ex) {
    Console.WriteLine (ex);
} finally {
    Console.WriteLine ("finally");
}
```

Isso ocorre porque o desenrolador de pilha Objective-C não conhece a cláusula gerenciada `catch` e nem a `finally` cláusula será executada.

Quando o exemplo de código acima _é_ eficaz, é porque Objective-c tem um método de ser notificado de exceções de Objective-c sem tratamento, [`NSSetUncaughtExceptionHandler`][2] que o xamarin. Ios e o xamarin. Mac usam, e nesse ponto tenta converter quaisquer exceções de Objective-c em exceções gerenciadas.

## <a name="scenarios"></a>Cenários

### <a name="scenario-1---catching-objective-c-exceptions-with-a-managed-catch-handler"></a>Cenário 1-captura de exceções de Objective-C com um manipulador de catch gerenciado

No cenário a seguir, é possível capturar exceções de Objective-C usando `catch` manipuladores gerenciados:

1. Uma exceção Objective-C é lançada.
2. O tempo de execução Objective-C percorre a pilha (mas não a desenrola), procurando um `@catch` manipulador nativo que possa manipular a exceção.
3. O tempo de execução Objective-C não localiza nenhum `@catch` manipulador, chama `NSGetUncaughtExceptionHandler` e invoca o manipulador instalado pelo Xamarin. Ios/Xamarin. Mac.
4. O manipulador do xamarin. iOS/Xamarin. Mac converterá a exceção Objective-C em uma exceção gerenciada e a lançará. Como o tempo de execução Objective-C não desenrolava a pilha (apenas enquadrada), o quadro atual é o mesmo em que a exceção Objective-C foi gerada.

Outro problema ocorre aqui, porque o tempo de execução do mono não sabe como desenrolar os quadros Objective-C corretamente.

Quando o retorno de chamada de exceção Objective-C não capturado do Xamarin. iOS é chamado, a pilha é parecida com esta:

```
 0 libxamarin-debug.dylib   exception_handler(exc=name: "NSInvalidArgumentException" - reason: "*** setObjectForKey: key cannot be nil")
 1 CoreFoundation           __handleUncaughtException + 809
 2 libobjc.A.dylib          _objc_terminate() + 100
 3 libc++abi.dylib          std::__terminate(void (*)()) + 14
 4 libc++abi.dylib          __cxa_throw + 122
 5 libobjc.A.dylib          objc_exception_throw + 337
 6 CoreFoundation           -[__NSDictionaryM setObject:forKey:] + 1015
 7 libxamarin-debug.dylib   xamarin_dyn_objc_msgSend + 102
 8 TestApp                  ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
 9 TestApp                  Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr) [0x00000]
10 TestApp                  ExceptionMarshaling.Exceptions.ThrowObjectiveCException () [0x00013]
```

Aqui, os únicos quadros gerenciados são os quadros 8-10, mas a exceção gerenciada é lançada no quadro 0. Isso significa que o tempo de execução do mono deve desenrolar os quadros nativos 0-7, o que causa um problema equivalente ao problema discutido acima: embora o tempo de execução do mono desenrolar os quadros nativos, ele não executará nenhuma cláusula Objective-C `@catch` ou `@finally` .

Exemplo de código:

```objc
-(id) setObject: (id) object forKey: (id) key
{
    @try {
        if (key == nil)
            [NSException raise: @"NSInvalidArgumentException"];
    } @finally {
        NSLog (@"This will not be executed");
    }
}
```

E a `@finally` cláusula não será executada porque o tempo de execução mono que desenrola este quadro não sabe sobre isso.

Uma variação disso é lançar uma exceção gerenciada em código gerenciado e, em seguida, desenrolar por meio de quadros nativos para chegar à primeira cláusula gerenciada `catch` :

```csharp
class AppDelegate : UIApplicationDelegate {
    public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
    {
        throw new Exception ("An exception");
    }
    static void Main (string [] args)
    {
        try {
            UIApplication.Main (args, null, typeof (AppDelegate));
        } catch (Exception ex) {
            Console.WriteLine ("Managed exception caught.");
        }
    }
}
```

O `UIApplication:Main` método gerenciado chamará o `UIApplicationMain` método nativo e, em seguida, o Ios fará muita execução de código nativo antes de eventualmente chamar o método gerenciado `AppDelegate:FinishedLaunching` , com ainda muitos quadros nativos na pilha quando a exceção gerenciada for gerada:

```
 0: TestApp                 ExceptionMarshaling.IOS.AppDelegate:FinishedLaunching (UIKit.UIApplication,Foundation.NSDictionary)
 1: TestApp                 (wrapper runtime-invoke) <Module>:runtime_invoke_bool__this___object_object (object,intptr,intptr,intptr) 
 2: libmonosgen-2.0.dylib   mono_jit_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>, error=<unavailable>)
 3: libmonosgen-2.0.dylib   do_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>, error=<unavailable>)
 4: libmonosgen-2.0.dylib   mono_runtime_invoke [inlined] mono_runtime_invoke_checked(method=<unavailable>, obj=<unavailable>, params=<unavailable>, error=0xbff45758)
 5: libmonosgen-2.0.dylib   mono_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>)
 6: libxamarin-debug.dylib  xamarin_invoke_trampoline(type=<unavailable>, self=<unavailable>, sel="application:didFinishLaunchingWithOptions:", iterator=<unavailable>), context=<unavailable>)
 7: libxamarin-debug.dylib  xamarin_arch_trampoline(state=0xbff45ad4)
 8: libxamarin-debug.dylib  xamarin_i386_common_trampoline
 9: UIKit                   -[UIApplication _handleDelegateCallbacksWithOptions:isSuspended:restoreState:]
10: UIKit                   -[UIApplication _callInitializationDelegatesForMainScene:transitionContext:]
11: UIKit                   -[UIApplication _runWithMainScene:transitionContext:completion:]
12: UIKit                   __84-[UIApplication _handleApplicationActivationWithScene:transitionContext:completion:]_block_invoke.3124
13: UIKit                   -[UIApplication workspaceDidEndTransaction:]
14: FrontBoardServices      __37-[FBSWorkspace clientEndTransaction:]_block_invoke_2
15: FrontBoardServices      __40-[FBSWorkspace _performDelegateCallOut:]_block_invoke
16: FrontBoardServices      __FBSSERIALQUEUE_IS_CALLING_OUT_TO_A_BLOCK__
17: FrontBoardServices      -[FBSSerialQueue _performNext]
18: FrontBoardServices      -[FBSSerialQueue _performNextFromRunLoopSource]
19: FrontBoardServices      FBSSerialQueueRunLoopSourceHandler
20: CoreFoundation          __CFRUNLOOP_IS_CALLING_OUT_TO_A_SOURCE0_PERFORM_FUNCTION__
21: CoreFoundation          __CFRunLoopDoSources0
22: CoreFoundation          __CFRunLoopRun
23: CoreFoundation          CFRunLoopRunSpecific
24: CoreFoundation          CFRunLoopRunInMode
25: UIKit                   -[UIApplication _run]
26: UIKit                   UIApplicationMain
27: TestApp                 (wrapper managed-to-native) UIKit.UIApplication:UIApplicationMain (int,string[],intptr,intptr)
28: TestApp                 UIKit.UIApplication:Main (string[],intptr,intptr)
29: TestApp                 UIKit.UIApplication:Main (string[],string,string)
30: TestApp                 ExceptionMarshaling.IOS.Application:Main (string[])
```

Os quadros 0-1 e 27-30 são gerenciados, enquanto todos os entre eles são nativos. Se o mono desenrolar por meio desses quadros, nenhuma cláusula Objective-C `@catch` ou `@finally` será executada.

### <a name="scenario-2---not-able-to-catch-objective-c-exceptions"></a>Cenário 2-não é possível capturar exceções de Objective-C

No cenário a seguir, _não_ é possível capturar exceções de Objective-c usando `catch` manipuladores gerenciados porque a exceção Objective-c foi tratada de outra maneira:

1. Uma exceção Objective-C é lançada.
2. O tempo de execução Objective-C percorre a pilha (mas não a desenrola), procurando um `@catch` manipulador nativo que possa manipular a exceção.
3. O tempo de execução Objective-C localiza um `@catch` manipulador, desenrola a pilha e começa a executar o `@catch` manipulador.

Esse cenário normalmente é encontrado em aplicativos Xamarin. iOS, pois, no thread principal, geralmente há um código como este:

``` objective-c
void UIApplicationMain ()
{
    @try {
        while (true) {
            ExecuteRunLoop ();
        }
    } @catch (NSException *ex) {
        NSLog (@"An unhandled exception occured: %@", exc);
        abort ();
    }
}

```

Isso significa que, no thread principal, nunca há uma exceção de Objective-C sem tratamento e, portanto, nosso retorno de chamada que converte exceções de Objective-C para exceções gerenciadas nunca é chamado.

Isso também é bastante comum ao depurar aplicativos Xamarin. Mac em uma versão anterior do macOS do que o Xamarin. Mac dá suporte, pois inspecionar a maioria dos objetos da interface do usuário no depurador tentará buscar propriedades que correspondam aos seletores que não existem na plataforma em execução (porque o Xamarin. Mac inclui suporte para uma versão superior do macOS). Chamar esses seletores lançará um `NSInvalidArgumentException` ("seletor não reconhecido enviado para..."), que eventualmente faz com que o processo falhe.

Para resumir, ter o tempo de execução Objective-C ou os quadros de liberação de tempo de execução mono que não estão programados para lidar pode levar a comportamentos indefinidos, como falhas, vazamentos de memória e outros tipos de comportamentos imprevisíveis (inadequados).

## <a name="solution"></a>Solução

No Xamarin. iOS 10 e no Xamarin. Mac 2,10, adicionamos suporte para capturar exceções gerenciadas e objetivas-C em qualquer limite nativo gerenciado e para converter essa exceção para outro tipo.

No pseudocódigo, ele se parece com o seguinte:

``` csharp
[DllImport ("libobjc.dylib")]
static extern void objc_msgSend (IntPtr handle, IntPtr selector);

static void DoSomething (NSObject obj)
{
    objc_msgSend (obj.Handle, Selector.GetHandle ("doSomething"));
}
```

O P/Invoke para objc_msgSend é interceptado, e isso é chamado em vez disso:

``` objective-c
void
xamarin_dyn_objc_msgSend (id obj, SEL sel)
{
    @try {
        objc_msgSend (obj, sel);
    } @catch (NSException *ex) {
        convert_to_and_throw_managed_exception (ex);
    }
}
```

E algo semelhante é feito para o caso inverso (marshaling de exceções gerenciadas para exceções Objective-C).

A captura de exceções no limite nativo gerenciado não é sem custos, portanto, ela nem sempre está habilitada por padrão:

- Xamarin. iOS/tvOS: a interceptação de exceções Objective-C está habilitada no simulador.
- Xamarin. watchOS: a interceptação é imposta em todos os casos, porque permitir que os quadros gerenciados desenrolados de tempo de execução Objective-C confunda o coletor de lixo e o destrave ou falhe.
- Xamarin. Mac: a interceptação de exceções de Objective-C está habilitada para compilações de depuração.

A seção de [sinalizadores de tempo de compilação](#build_time_flags) explica como habilitar a interceptação quando ela não está habilitada por padrão.

## <a name="events"></a>Eventos

Há dois novos eventos que são gerados quando uma exceção é interceptada: `Runtime.MarshalManagedException` e `Runtime.MarshalObjectiveCException` .

Os dois eventos são passados `EventArgs` para um objeto que contém a exceção original que foi lançada (a `Exception` Propriedade) e uma `ExceptionMode` propriedade para definir como a exceção deve ser empacotada.

A `ExceptionMode` propriedade pode ser alterada no manipulador de eventos para alterar o comportamento de acordo com qualquer processamento personalizado feito no manipulador. Um exemplo seria anular o processo se ocorrer uma determinada exceção.

A alteração da `ExceptionMode` propriedade aplica-se ao evento único; ela não afeta nenhuma exceção interceptada no futuro.

Os seguintes modos estão disponíveis:

- `Default`: O padrão varia por plataforma. `ThrowObjectiveCException`Se o GC estiver no modo cooperativo (watchOS) e de `UnwindNativeCode` outra forma (Ios/WatchOS/MacOS). O padrão pode ser alterado no futuro.
- `UnwindNativeCode`: Esse é o comportamento anterior (indefinido). Isso não está disponível ao usar o GC no modo cooperativo (que é a única opção em watchOS; portanto, essa não é uma opção válida em watchOS), mas é a opção padrão para todas as outras plataformas.
- `ThrowObjectiveCException`: Converta a exceção gerenciada em uma exceção Objective-C e gere a exceção Objective-C. Esse é o padrão em watchOS.
- `Abort`: Anular o processo.
- `Disable`: Desabilita a interceptação de exceção, portanto, não faz sentido definir esse valor no manipulador de eventos, mas depois que o evento é gerado, ele é muito tarde para desabilitá-lo. Em qualquer caso, se for definido, ele se comportará como `UnwindNativeCode` .

Para realizar marshaling de exceções de Objective-C para código gerenciado, os seguintes modos estão disponíveis:

- `Default`: O padrão varia por plataforma. `ThrowManagedException`Se o GC estiver no modo cooperativo (watchOS) e de `UnwindManagedCode` outra forma (Ios/TvOS/MacOS). O padrão pode ser alterado no futuro.
- `UnwindManagedCode`: Esse é o comportamento anterior (indefinido). Isso não está disponível ao usar o GC no modo cooperativo (que é o único modo GC válido em watchOS; portanto, essa não é uma opção válida em watchOS), mas é o padrão para todas as outras plataformas.
- `ThrowManagedException`: Converta a exceção Objective-C em uma exceção gerenciada e gere a exceção gerenciada. Esse é o padrão em watchOS.
- `Abort`: Anular o processo.
- `Disable`:D isaptos a interceptação de exceção, portanto, não faz sentido definir esse valor no manipulador de eventos, mas depois que o evento é gerado, ele é muito tarde para desabilitá-lo. Em qualquer caso, se definido, ele anulará o processo.

Portanto, para ver toda vez que uma exceção é empacotada, você pode fazer isso:

``` csharp
Runtime.MarshalManagedException += (object sender, MarshalManagedExceptionEventArgs args) =>
{
    Console.WriteLine ("Marshaling managed exception");
    Console.WriteLine ("    Exception: {0}", args.Exception);
    Console.WriteLine ("    Mode: {0}", args.ExceptionMode);
    
};
Runtime.MarshalObjectiveCException += (object sender, MarshalObjectiveCExceptionEventArgs args) =>
{
    Console.WriteLine ("Marshaling Objective-C exception");
    Console.WriteLine ("    Exception: {0}", args.Exception);
    Console.WriteLine ("    Mode: {0}", args.ExceptionMode);
};
```

<a name="build_time_flags"></a>

## <a name="build-time-flags"></a>Sinalizadores de tempo de compilação

É possível passar as seguintes opções para **mTouch** (para aplicativos Xamarin. Ios) e **MMP** (para aplicativos xamarin. Mac), que determinará se a interceptação de exceção está habilitada e definirá a ação padrão que deve ocorrer:

- `--marshal-managed-exceptions=`
  - `default`
  - `unwindnativecode`
  - `throwobjectivecexception`
  - `abort`
  - `disable`

- `--marshal-objectivec-exceptions=`
  - `default`
  - `unwindmanagedcode`
  - `throwmanagedexception`
  - `abort`
  - `disable`

Exceto para `disable` , esses valores são idênticos aos `ExceptionMode` valores que são passados para os `MarshalManagedException` `MarshalObjectiveCException` eventos e.

A `disable` opção desabilitará _principalmente_ a interceptação, exceto que ainda iremos interceptar exceções quando não adicionar nenhuma sobrecarga de execução. Os eventos de marshaling ainda são gerados para essas exceções, com o modo padrão sendo o modo padrão para a plataforma em execução.

## <a name="limitations"></a>Limitações

Interceptamos apenas P/Invokes para a `objc_msgSend` família de funções ao tentar capturar exceções de Objective-C. Isso significa que um P/Invoke para outra função C, que, em seguida, gera quaisquer exceções Objective-C, ainda será executado no comportamento antigo e indefinido (isso pode ser melhorado no futuro).

[2]: https://developer.apple.com/reference/foundation/1409609-nssetuncaughtexceptionhandler?language=objc

## <a name="related-links"></a>Links relacionados

- [Marshaling de exceção (exemplo)](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling)
