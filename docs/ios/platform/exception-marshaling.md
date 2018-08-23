---
title: Exceção Marshaling no xamarin
description: Este documento descreve como trabalhar com exceções nativas e gerenciadas em um aplicativo xamarin. Ele aborda problemas que podem ocorrer e uma solução para esses problemas.
ms.prod: xamarin
ms.assetid: BE4EE969-C075-4B9A-8465-E393556D8D90
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/05/2017
ms.openlocfilehash: dcf1074aacb6d139d107dac01fa86f459831d5f9
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786737"
---
# <a name="exception-marshaling-in-xamarinios"></a>Exceção Marshaling no xamarin

_Xamarin contém novos eventos para ajudar a responder a exceções, particularmente em código nativo._

O código gerenciado e Objective-C têm suporte para exceções de tempo de execução (try/catch/finally cláusulas).

No entanto, suas implementações forem diferentes, que significa que as bibliotecas de tempo de execução (tempo de execução Mono e as bibliotecas de tempo de execução Objective-C) tenham problemas quando eles precisam lidar com exceções e, em seguida, execute o código escrito em outras linguagens.

Este documento explica os problemas que podem ocorrer e as possíveis soluções.

Ele também inclui um projeto de exemplo, [exceção Marshaling](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling), que pode ser usado para testar suas soluções e cenários diferentes.

## <a name="problem"></a>Problema

O problema ocorre quando uma exceção é gerada e é encontrada durante a liberação de um quadro de pilha que não coincide com o tipo de exceção foi lançada.

Um exemplo típico para xamarin ou Xamarin.Mac é quando uma API nativa lança uma exceção Objective-C e, em seguida, essa exceção Objective-C deve ser tratada alguma forma quando o processo de desenrolamento de pilha atinge um quadro gerenciado.

A ação padrão é não fazer nada. Para o exemplo acima, isso significa permitir que os quadros de desenrolamento gerenciados Objective-C em tempo de execução. Isso é problemático, pois o tempo de execução Objective-C não sabe como desenrolar quadros gerenciados. Por exemplo não executa qualquer `catch` ou `finally` cláusulas nesse quadro.

### <a name="broken-code"></a>Código desfeito

Considere o exemplo de código a seguir:

``` csharp
var dict = new NSMutableDictionary ();
dict.LowLevelSetObject (IntPtr.Zero, IntPtr.Zero); 
```

Isso gerará um NSInvalidArgumentException Objective-C em código nativo:

    NSInvalidArgumentException *** setObjectForKey: key cannot be nil

E o rastreamento de pilha será semelhante a esta:

    0   CoreFoundation          __exceptionPreprocess + 194
    1   libobjc.A.dylib         objc_exception_throw + 52
    2   CoreFoundation          -[__NSDictionaryM setObject:forKey:] + 1015
    3   libobjc.A.dylib         objc_msgSend + 102
    4   TestApp                 ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
    5   TestApp                 Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr)
    6   TestApp                 ExceptionMarshaling.Exceptions.ThrowObjectiveCException ()

Quadros de 0 a 3 são quadros nativos e unwinder a pilha no tempo de execução Objective-C _pode_ os quadros de desenrolamento. Em particular, ele executará qualquer Objective-C `@catch` ou `@finally` cláusulas.

No entanto, o unwinder de pilha Objective-C é _não_ capaz de desenrolamento corretamente os quadros gerenciados (quadros de 4 a 6), em que os quadros será organizados, mas a lógica de exceção gerenciada não será executada.

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

Isso ocorre porque o unwinder de pilha Objective-C não conhece o gerenciado `catch` cláusula e não será o `finally` cláusula ser executada.

Quando o exemplo de código acima _é_ eficaz, é porque Objective-C tem um método de notificação de exceções sem tratamento do Objective-C, [`NSSetUncaughtExceptionHandler`][2], que Xamarin e use Xamarin.Mac e nesse ponto tenta converter todas as exceções Objective-C para exceções gerenciadas.

## <a name="scenarios"></a>Cenários

### <a name="scenario-1---catching-objective-c-exceptions-with-a-managed-catch-handler"></a>Cenário 1: Capturando exceções Objective-C com um manipulador catch gerenciado

No cenário a seguir, é possível capturar exceções Objective-C usando gerenciado `catch` manipuladores:

1. Uma exceção Objective-C é gerada.
2. O tempo de execução Objective-C examina a pilha (mas não os desenrolar), procurando um nativo `@catch` manipulador que pode lidar com a exceção.
3. O tempo de execução Objective-C não encontra qualquer `@catch` manipuladores, chamadas `NSGetUncaughtExceptionHandler`e invoca o manipulador instalado por Xamarin.iOS/Xamarin.Mac.
4. Manipulador Xamarin.iOS/Xamarin.Mac's converterá a exceção Objective-C em uma exceção gerenciada e lançar a ele. Desde o Objective-C runtime não desenrolar a pilha (somente movimentada ele), o quadro atual é o mesmo onde a exceção Objective-C foi lançada.

Outro problema ocorre aqui, porque o tempo de execução Mono não sabe como desenrolar Objective-C quadros corretamente.

Quando é chamado não percebido Objective-C exceção retorno de chamada de xamarin, a pilha é assim:

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

Aqui, somente quadros gerenciados são quadros 8-10, mas a exceção gerenciada é gerada no quadro 0. Isso significa que o tempo de execução Mono deve liberar os quadros nativos 0-7, que faz com que um problema equivalente para o problema discutido acima: Embora o tempo de execução Mono será desenrolar os quadros nativos, ele não irá executar qualquer Objective-C `@catch` ou `@finally` cláusulas .

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

E o `@finally` cláusula não será executada porque o tempo de execução Mono que esvazia este quadro não sabe sobre ele.

Uma variação disso é lançar uma exceção gerenciada em código gerenciado e, em seguida, desenrolamento por meio de quadros nativos para obter o primeiro gerenciados `catch` cláusula:

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

Gerenciado `UIApplication:Main` será chamada do método nativo `UIApplicationMain` método e iOS fará muito da execução do código nativo antes de chamar eventualmente gerenciado `AppDelegate:FinishedLaunching` método ainda muita quadros nativos na pilha quando a exceção gerenciada lançada:

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

Quadros 0-1 e 30 27 são gerenciados, enquanto todos esses aspectos são nativos. Se Mono esvazia por esses quadros, nenhum Objective-C `@catch` ou `@finally` cláusulas serão executadas.

### <a name="scenario-2---not-able-to-catch-objective-c-exceptions"></a>Cenário 2 - não é possível capturar exceções Objective-C

No cenário a seguir, é _não_ possível capturar exceções Objective-C usando gerenciado `catch` manipuladores porque a exceção Objective-C foi tratada de outra maneira:

1. Uma exceção Objective-C é gerada.
2. O tempo de execução Objective-C examina a pilha (mas não os desenrolar), procurando um nativo `@catch` manipulador que pode lidar com a exceção.
3. O tempo de execução Objective-C localiza um `@catch` manipulador, esvazia a pilha e inicia a execução de `@catch` manipulador.

Esse cenário normalmente é encontrado em aplicativos xamarin, porque o thread principal normalmente não há código como este:

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

Isso significa que o thread principal nunca é realmente uma exceção sem tratamento do Objective-C, e, portanto, nosso retorno de chamada que converte as exceções de Objective-C para exceções gerenciadas nunca é chamado.

Isso também é muito comum ao depurar aplicativos Xamarin.Mac em uma versão anterior do macOS que oferece suporte a Xamarin.Mac porque inspecionando a maioria dos objetos de interface do usuário no depurador tentará buscar propriedades que correspondem ao seletores que não existem de execução (de plataforma porque Xamarin.Mac inclui suporte para uma versão mais recente macOS). Chamar esses seletores lançará um `NSInvalidArgumentException` ("seletor não reconhecido enviado para..."), que eventualmente faz com que o processo falhar.

Para resumir, tendo o tempo de execução Objective-C ou os quadros de desenrolamento Mono de tempo de execução que eles não são programados para identificador pode levar a comportamentos indefinidos, como falhas, perdas de memória e outros tipos de comportamentos imprevisíveis (mis).

## <a name="solution"></a>Solução

Em 10 de xamarin e Xamarin.Mac 2.10, adicionamos suporte para capturar exceções gerenciadas e Objective-C em qualquer limite de código gerenciado e nativo e convertendo essa exceção para o outro tipo.

Em pseudocódigo, parece que algo assim:

``` csharp
[DllImport ("libobjc.dylib")]
static extern void objc_msgSend (IntPtr handle, IntPtr selector);

static void DoSomething (NSObject obj)
{
    objc_msgSend (obj.Handle, Selector.GetHandle ("doSomething"));
}
```

P/Invoke para objc_msgSend é interceptado e isso é chamado em vez disso:

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

E algo semelhante é feito para o inverso (marshaling exceções gerenciadas às exceções Objective-C).

Capturando exceções no limite de código gerenciado e nativo não está livre para custo, então ele é nem sempre habilitado por padrão:

- Xamarin.iOS/tvOS: interceptação de exceções Objective-C está habilitada no simulador.
- Xamarin.watchOS: interceptação é aplicada em todos os casos, porque, permitindo que a liberação de tempo de execução Objective-C gerenciadas quadros serão confundir o coletor de lixo e torne-falha ou trava.
- Xamarin.Mac: interceptação de exceções Objective-C está habilitada para compilações de depuração.

O [sinalizadores de tempo de compilação](#build_time_flags) seção explica como habilitar a intercepção quando ele não está habilitado por padrão.

## <a name="events"></a>Eventos

Há dois novos eventos que são gerados quando uma exceção for interceptada: `Runtime.MarshalManagedException` e `Runtime.MarshalObjectiveCException`.

Ambos os eventos são passados um `EventArgs` objeto que contém a exceção original que foi gerada (o `Exception` propriedade) e um `ExceptionMode` propriedade para definir como a exceção deve ser lido.

O `ExceptionMode` propriedade pode ser alterada no evento manipulador para alterar o comportamento de acordo com qualquer processamento personalizado feito no manipulador. Um exemplo seria anular o processo se ocorrer uma exceção de determinados.

Alterando o `ExceptionMode` propriedade se aplica a único evento, ele não afeta todas as exceções interceptadas no futuro.

Os modos a seguir estão disponíveis:

- `Default`: O padrão varia de acordo com a plataforma. É `ThrowObjectiveCException` se o GC está no modo cooperativo (watchOS), e `UnwindNativeCode` caso contrário (iOS / watchOS / macOS). O padrão pode ser alterado no futuro.
- `UnwindNativeCode`: Esse é o comportamento de (indefinido) anterior. Isso não está disponível ao usar o GC no modo cooperativo (que é a única opção em watchOS; portanto, isso não é uma opção válida na watchOS), mas é a opção padrão para todas as outras plataformas.
- `ThrowObjectiveCException`: Converter da exceção gerenciada em uma exceção Objective-C e lançar a exceção Objective-C. Esse é o padrão em watchOS.
- `Abort`: O processo anulado.
- `Disable`: Desabilita a interceptação de exceção, portanto, não faz sentido para definir esse valor no manipulador de eventos, mas depois que o evento é gerado é muito tarde para desabilitá-lo. Em qualquer caso, se definido, ele se comportará como `UnwindNativeCode`.

Para realizar marshaling exceções Objective-C para código gerenciado, estão disponíveis os seguintes modos:

- `Default`: O padrão varia de acordo com a plataforma. É `ThrowManagedException` se o GC está no modo cooperativo (watchOS), e `UnwindManagedCode` caso contrário (iOS / tvOS / macOS). O padrão pode ser alterado no futuro.
- `UnwindManagedCode`: Esse é o comportamento de (indefinido) anterior. Isso não está disponível ao usar o GC no modo cooperativo (que é o modo de GC só é válido em watchOS; portanto, isso não é uma opção válida em watchOS), mas ele é o padrão para todas as outras plataformas.
- `ThrowManagedException`: Converter a exceção Objective-C em uma exceção gerenciada e lançar a exceção gerenciada. Esse é o padrão em watchOS.
- `Abort`: O processo anulado.
- `Disable`: Desabilita a interceptação de exceção, portanto não faz sentido para definir este valor no evento manipulador, mas uma vez o evento é gerado, é muito tarde para desabilitá-lo. Em qualquer caso se definido, ele cancelará o processo.

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

É possível passar as seguintes opções para **mtouch** (para aplicativos xamarin) e **mmp** (para aplicativos de Xamarin.Mac), que determinará se a interceptação de exceção é ativada e definir a ação padrão que deve ocorrer:

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

O `disable` opção será _principalmente_ desabilitar intercepção, exceto que ainda será interceptar exceções quando ele não adicione qualquer sobrecarga de execução. Os eventos de marshaling ainda são gerados para essas exceções, com o modo padrão que está sendo o modo padrão para a plataforma em execução.

## <a name="limitations"></a>Limitações

Estamos apenas interceptar P/Invokes para a `objc_msgSend` família de funções ao tentar capturar exceções Objective-C. Isso significa que um P/Invoke para outra função de C, que, em seguida, gera exceções Objective-C, ainda será executado para o antigo comportamento indefinido (Isso pode ser melhorado no futuro).

[2]: https://developer.apple.com/reference/foundation/1409609-nssetuncaughtexceptionhandler?language=objc


## <a name="related-links"></a>Links relacionados

- [Marshaling de exceção (exemplo)](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling)
