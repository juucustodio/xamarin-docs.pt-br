---
title: Exceção de Marshaling no xamarin. IOS
description: Este documento descreve como trabalhar com exceções nativas e gerenciadas em um aplicativo xamarin. IOS. Ele aborda problemas que podem ocorrer e uma solução para esses problemas.
ms.prod: xamarin
ms.assetid: BE4EE969-C075-4B9A-8465-E393556D8D90
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/05/2017
ms.openlocfilehash: 167d6ac421bdd2652e7f8474e1ea21bd9040723f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114295"
---
# <a name="exception-marshaling-in-xamarinios"></a>Exceção de Marshaling no xamarin. IOS

_Xamarin. IOS contém novos eventos para ajudar a responder a exceções, especialmente em código nativo._

O código gerenciado e Objective-C têm suporte para exceções de tempo de execução (try/catch/finally cláusulas).

No entanto, suas implementações são diferentes, que significa que as bibliotecas de tempo de execução (tempo de execução Mono e as bibliotecas de tempo de execução do Objective-C) têm problemas quando eles precisam lidar com exceções e, em seguida, execute o código escrito em outras linguagens.

Este documento explica os problemas que podem ocorrer e as possíveis soluções.

Ele também inclui um projeto de exemplo [Marshaling de exceção](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling), que pode ser usado para testar cenários diferentes e suas soluções.

## <a name="problem"></a>Problema

O problema ocorre quando uma exceção é lançada e é encontrada durante o desenrolamento de um quadro de pilha que não coincide com o tipo de exceção foi lançada.

Um exemplo típico para xamarin. IOS ou xamarin. MAC é quando uma API nativa gera uma exceção de Objective-C e, em seguida, essa exceção de Objective-C deve ser manipulada alguma forma durante o processo de desenrolamento de pilha atinge um quadro gerenciado.

A ação padrão é não fazer nada. No exemplo acima, isso significa permitir que os quadros de desenrolamento gerenciado de tempo de execução do Objective-C. Isso é problemático, pois o tempo de execução do Objective-C não sabe como desenrolar quadros gerenciados; Por exemplo não executar alguma `catch` ou `finally` cláusulas no quadro.

### <a name="broken-code"></a>Códigos quebrados

Considere o exemplo de código a seguir:

``` csharp
var dict = new NSMutableDictionary ();
dict.LowLevelSetObject (IntPtr.Zero, IntPtr.Zero); 
```

Isso gerará um NSInvalidArgumentException Objective-C em código nativo:

    NSInvalidArgumentException *** setObjectForKey: key cannot be nil

E o rastreamento de pilha será algo parecido com isto:

    0   CoreFoundation          __exceptionPreprocess + 194
    1   libobjc.A.dylib         objc_exception_throw + 52
    2   CoreFoundation          -[__NSDictionaryM setObject:forKey:] + 1015
    3   libobjc.A.dylib         objc_msgSend + 102
    4   TestApp                 ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
    5   TestApp                 Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr)
    6   TestApp                 ExceptionMarshaling.Exceptions.ThrowObjectiveCException ()

Quadros de 0 a 3 são quadros nativos e o desenrolador de pilha em tempo de execução do Objective-C _pode_ os quadros de desenrolamento. Em particular, ele executará qualquer Objective-C `@catch` ou `@finally` cláusulas.

No entanto, é o unwinder de pilha de Objective-C _não_ capaz de desenrolamento corretamente os quadros gerenciados (quadros de 4 a 6), em que os quadros serão ser organizados, mas a lógica de exceção gerenciada não será executada.

Que significa que geralmente não é possível capturar essas exceções da seguinte maneira:

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

Isso ocorre porque o unwinder de pilha de Objective-C não conhece o gerenciado `catch` cláusula e nem será o `finally` cláusula ser executada.

Quando o exemplo de código acima _é_ eficaz, é porque Objective-C tem um método de notificação de exceções sem tratamento do Objective-C, [`NSSetUncaughtExceptionHandler`][2], que Xamarin e use Xamarin.Mac e nesse ponto tenta converter todas as exceções Objective-C para exceções gerenciadas.

## <a name="scenarios"></a>Cenários

### <a name="scenario-1---catching-objective-c-exceptions-with-a-managed-catch-handler"></a>Cenário 1 - Capturando exceções Objective-C com um manipulador catch gerenciado

No cenário a seguir, é possível capturar exceções Objective-C usando gerenciado `catch` manipuladores:

1. Uma exceção de Objective-C é gerada.
2. O tempo de execução do Objective-C percorre a pilha (mas não os desenrolamento), procurando um nativo `@catch` manipulador que pode lidar com a exceção.
3. O tempo de execução do Objective-C não encontra qualquer `@catch` manipuladores, chamadas `NSGetUncaughtExceptionHandler`e invoca o manipulador instalado pelo Xamarin.iOS/Xamarin.Mac.
4. Manipulador de Xamarin.iOS/Xamarin.Mac's converter a exceção de Objective-C em uma exceção gerenciada e lançá-la. Desde o Objective-C runtime não desenrolar a pilha (somente percorreu a ele), o quadro atual é o mesmo em que a exceção de Objective-C foi lançada.

Outro problema ocorre aqui, porque o tempo de execução Mono não sabe como desenrolar de quadros de Objective-C corretamente.

Quando é chamado não identificado Objective-C exceção retorno de chamada de xamarin. IOS, a pilha é como este:

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

Aqui, os quadros gerenciados somente são quadros de 8 a 10, mas a exceção gerenciada é gerada no quadro 0. Isso significa que o tempo de execução Mono deve desenrolar quadros nativos 0 a 7, que faz com que um problema equivalente para o problema discutido acima: Embora o tempo de execução Mono desenrolará quadros nativos, ele não irá executar nenhum Objective-C `@catch` ou `@finally` cláusulas .

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

E o `@finally` cláusula não será executada porque o tempo de execução Mono que desenrola este quadro não souber sobre ele.

Uma variação disso é lançar uma exceção gerenciada no código gerenciado e, em seguida, desenrolar por meio de quadros nativos para obter o primeiro gerenciado `catch` cláusula:

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

Gerenciado `UIApplication:Main` será chamada do método nativo `UIApplicationMain` método e, em seguida, o iOS fará muito da execução de código nativo antes de chamar, eventualmente, gerenciado `AppDelegate:FinishedLaunching` método com ainda uma grande quantidade de quadros nativos na pilha quando a exceção gerenciada é lançada:

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

Quadros de 0 a 1 e 27 a 30 são gerenciados, enquanto aqueles entre são nativas. Se o Mono é desenrolado por esses quadros, nenhum Objective-C `@catch` ou `@finally` cláusulas serão executadas.

### <a name="scenario-2---not-able-to-catch-objective-c-exceptions"></a>Cenário 2 - não é possível capturar exceções Objective-C

No cenário a seguir, é _não_ possíveis capturar exceções Objective-C usando gerenciado `catch` manipuladores porque a exceção de Objective-C foi tratada de outra forma:

1. Uma exceção de Objective-C é gerada.
2. O tempo de execução do Objective-C percorre a pilha (mas não os desenrolamento), procurando um nativo `@catch` manipulador que pode lidar com a exceção.
3. O tempo de execução do Objective-C localiza uma `@catch` manipulador, desenrola a pilha e inicia a execução a `@catch` manipulador.

Esse cenário normalmente é encontrado em aplicativos xamarin. IOS, porque no thread principal geralmente há código como este:

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

Isso significa que no thread principal nunca há realmente uma exceção sem tratamento do Objective-C, e, portanto, nossa retorno de chamada que converte as exceções de Objective-C para exceções gerenciadas nunca é chamado.

Isso também é bastante comum ao depurar aplicativos xamarin. MAC em uma versão anterior do macOS que oferece suporte a xamarin. Mac pois inspecionando a maioria dos objetos de interface do usuário no depurador tentará buscar as propriedades que correspondem ao seletores que não existem no (da plataforma em execução porque o xamarin. Mac inclui suporte para uma versão posterior do macOS). Chamar esses seletores lançará um `NSInvalidArgumentException` ("não reconhecido seletor enviado para..."), que, eventualmente, faz com que o processo falhe.

Para resumir, tendo o tempo de execução do Objective-C ou os quadros de desenrolamento de tempo de execução Mono que eles não sejam programados para identificador pode levar a comportamentos indefinidos, como falhas, vazamentos de memória e outros tipos de comportamentos imprevisíveis (mis).

## <a name="solution"></a>Solução

No xamarin. IOS 10 e no xamarin. MAC 2.10, adicionamos suporte para capturar exceções gerenciadas e Objective-C em qualquer limite de código gerenciado e nativo e para converter essa exceção para o outro tipo.

Em pseudocódigo, parece que algo parecido com isto:

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

E algo semelhante é feito para o inverso (empacotamento de exceções gerenciadas às exceções Objective-C).

Capturando exceções no limite de código gerenciado e nativo não está livre para custo, portanto, ele é nem sempre habilitado por padrão:

- Xamarin.iOS/tvOS: interceptação de exceções Objective-C está habilitada no simulador.
- Xamarin. watchos: intercepção é imposta em todos os casos, porque deixar o desenrolamento de tempo de execução do Objective-C gerenciado quadros confundirá o coletor de lixo e torne ele travamento ou falha.
- Xamarin. Mac: interceptação de exceções Objective-C está habilitada para compilações de depuração.

O [sinalizadores de tempo de compilação](#build_time_flags) seção explica como habilitar a intercepção quando ele não está habilitado por padrão.

## <a name="events"></a>Eventos

Há dois novos eventos que são gerados depois que uma exceção é interceptada: `Runtime.MarshalManagedException` e `Runtime.MarshalObjectiveCException`.

Ambos os eventos são passados uma `EventArgs` objeto que contém a exceção original que foi gerada (o `Exception` propriedade) e um `ExceptionMode` propriedade para definir como a exceção deve ser empacotada.

O `ExceptionMode` propriedade pode ser alterada no evento manipulador para alterar o comportamento de acordo com qualquer processamento personalizado feito no manipulador. Um exemplo seria anular o processo se ocorrer uma exceção de determinados.

Alterando o `ExceptionMode` propriedade se aplica a único evento, ele não afeta todas as exceções interceptadas no futuro.

Os modos a seguir estão disponíveis:

- `Default`: O padrão varia de acordo com a plataforma. Vale `ThrowObjectiveCException` se o GC está no modo cooperativo (watchOS), e `UnwindNativeCode` caso contrário (iOS / watchOS / macOS). O padrão pode mudar no futuro.
- `UnwindNativeCode`: Esse é o comportamento (indefinido) anterior. Isso não está disponível ao usar o GC no modo cooperativo (que é a única opção na watchOS; portanto, isso não é uma opção válida no watchOS), mas é a opção padrão para todas as outras plataformas.
- `ThrowObjectiveCException`: Converter a exceção gerenciada em uma exceção de Objective-C e lançar a exceção de Objective-C. Esse é o padrão no watchOS.
- `Abort`: Anule o processo.
- `Disable`: Desativa a interceptação de exceção, portanto, não faz sentido definir esse valor no manipulador de eventos, mas depois que o evento é gerado que é tarde demais para desabilitá-lo. Em qualquer caso, se definido, ele se comportará como `UnwindNativeCode`.

Para realizar marshaling exceções Objective-C para código gerenciado, os modos a seguir estão disponíveis:

- `Default`: O padrão varia de acordo com a plataforma. Vale `ThrowManagedException` se o GC está no modo cooperativo (watchOS), e `UnwindManagedCode` caso contrário (iOS / tvOS / macOS). O padrão pode mudar no futuro.
- `UnwindManagedCode`: Esse é o comportamento (indefinido) anterior. Isso não está disponível ao usar o GC no modo cooperativo (que é o modo de GC só é válido no watchOS; portanto, isso não é uma opção válida no watchOS), mas é o padrão para todas as outras plataformas.
- `ThrowManagedException`: Converter a exceção de Objective-C em uma exceção gerenciada e lançar a exceção gerenciada. Esse é o padrão no watchOS.
- `Abort`: Anule o processo.
- `Disable`: Desativa a interceptação de exceção, portanto, não faz sentido definir este valor no caso de manipulador, mas uma vez o evento é gerado, é tarde demais para desabilitá-lo. Em qualquer caso se definido, ele cancelará o processo.

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

<a name="build_time_flags" />

## <a name="build-time-flags"></a>Sinalizadores de tempo de compilação

É possível passar as opções a seguir para **mtouch** (para aplicativos xamarin. IOS) e **mmp** (para aplicativos xamarin. Mac), que determinará se a interceptação de exceção estiver habilitada e definir a ação padrão que deve ocorrer:

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

Exceto para `disable`, esses valores são idênticos do `ExceptionMode` valores que são passados para o `MarshalManagedException` e `MarshalObjectiveCException` eventos.

O `disable` opção will _principalmente_ desabilitar interceptação, exceto que estamos ainda irá interceptar exceções quando ele não adicione qualquer sobrecarga de execução. Os eventos de marshaling ainda são gerados para essas exceções, com o modo padrão que está sendo o modo padrão para a plataforma em execução.

## <a name="limitations"></a>Limitações

Estamos apenas interceptar P/Invokes para a `objc_msgSend` família de funções durante a tentativa capturar exceções Objective-C. Isso significa que um P/Invoke para outra função de C, que, em seguida, emite as exceções de Objective-C, ainda será executado para o antigo comportamento indefinido (Isso pode ser melhorado no futuro).

[2]: https://developer.apple.com/reference/foundation/1409609-nssetuncaughtexceptionhandler?language=objc


## <a name="related-links"></a>Links relacionados

- [Marshaling de exceção (amostra)](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling)
