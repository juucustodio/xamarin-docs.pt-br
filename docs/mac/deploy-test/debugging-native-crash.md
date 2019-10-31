---
title: Depuração de uma falha nativa em um aplicativo Xamarin.Mac
description: Este documento descreve como depurar exceções originadas no runtime do Objective-C. Ele aborda as falhas de declaração, problemas com retornos de chamada, propagação de exceção e muito mais.
ms.prod: xamarin
ms.assetid: B0C0CE31-2737-4969-8EA5-D39D3333E9C2
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 10/19/2016
ms.openlocfilehash: bc5a151323414e867b919035b0c5705234faebf9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021663"
---
# <a name="debugging-a-native-crash-in-a-xamarinmac-app"></a>Depuração de uma falha nativa em um aplicativo Xamarin.Mac

## <a name="overview"></a>Visão Geral

Erros de programação, às vezes, podem gerar falhas no runtime nativo Objective-C. Diferentemente de exceções C#, eles não apontam para uma linha específica em seu código, que você pode buscar corrigir. Às vezes, eles podem ser triviais para localizar e corrigir, enquanto em outras ocasiões eles podem ser extremamente difíceis de rastrear. 

Passaremos por alguns exemplos reais de falhas nativas e daremos uma olhada.

## <a name="example-1-assertion-failure"></a>Exemplo 1: falha de asserção

Eis aqui as primeiras linhas de uma falha em um aplicativo de teste simples (essas informações estarão no painel **Saída do Aplicativo**):

```csharp
2014-10-15 16:18:02.364 NSOutlineViewHottness[79111:1304993] *** Assertion failure in -[NSTableView _uncachedRectHeightOfRow:], /SourceCache/AppKit/AppKit-1343.13/TableView.subproj/NSTableView.m:1855
2014-10-15 16:18:02.364 NSOutlineViewHottness[79111:1304993] NSTableView variable rowHeight error: The value must be > 0 for row 0, but the delegate <NSOutlineViewHottness_HotnessViewDelegate: 0xaa01860> gave -1.000.
2014-10-15 16:18:02.378 NSOutlineViewHottness[79111:1304993] *** Assertion failure in -[NSTableView _uncachedRectHeightOfRow:], /SourceCache/AppKit/AppKit-1343.13/TableView.subproj/NSTableView.m:1855
2014-10-15 16:18:02.378 NSOutlineViewHottness[79111:1304993] NSTableView variable rowHeight error: The value must be > 0 for row 0, but the delegate <NSOutlineViewHottness_HotnessViewDelegate: 0xaa01860> gave -1.000.
2014-10-15 16:18:02.381 NSOutlineViewHottness[79111:1304993] (
  0   CoreFoundation                      0x91888343 __raiseError + 195
  1   libobjc.A.dylib                     0x9a5e6a2a objc_exception_throw + 276
  2   CoreFoundation                      0x918881ca +[NSException raise:format:arguments:] + 138
  3   Foundation                          0x950742b1 -[NSAssertionHandler handleFailureInMethod:object:file:lineNumber:description:] + 118
  4   AppKit                              0x975db476 -[NSTableView _uncachedRectHeightOfRow:] + 373
  5   AppKit                              0x975db2f8 -[_NSTableRowHeightStorage _uncachedRectHeightOfRow:] + 143
  6   AppKit                              0x975db206 -[_NSTableRowHeightStorage _cacheRowHeights] + 167
  7   AppKit                              0x975db130 -[_NSTableRowHeightStorage _createRowHeightsArray] + 226
  8   AppKit                              0x975b5851 -[_NSTableRowHeightStorage _ensureRowHeights] + 73
  9   AppKit                              0x975b5790 -[_NSTableRowHeightStorage computeTableHeightForNumberOfRows:] + 89
  10  AppKit                              0x975b4c38 -[NSTableView _totalHeightOfTableView] + 220
```

As linhas prefixadas com números são o rastreamento de pilha nativo. Com base nisso, você pode ver se a falha ocorreu em algum lugar dentro do `NSTableView` que manipula as alturas das linhas. Em seguida, `NSAssertionHandler` dispara um `NSException (objc_exception_throw)` e podemos ver a falha de asserção:

```csharp
rowHeight error: The value must be > 0 for row 0, but the delegate
<NSOutlineView_ViewDelegate: 0xaa01860> gave -1.000
```

Quando você vê isso, fica bem claro que algum método `NSOutlineViewDelegate` está retornando um número negativo. Este era o problema:

```csharp
public override nfloat GetRowHeight (NSTableView tableView, nint row)
{
    return -1;
}
```

## <a name="example-2-callback-jumped-into-middle-of-nowhere"></a>Exemplo 2: o retorno de chamada foi para o meio do nada

```csharp
Stacktrace:

 at <unknown> <0xffffffff>
 at (wrapper managed-to-native) MonoMac.AppKit.NSApplication.NSApplicationMain (int,string[]) <IL 0x000a4, 0xffffffff>
 at MonoMac.AppKit.NSApplication.Main (string[]) [0x00041] in /Users/donblas/Programming/xamcore-master/src/AppKit/NSApplication.cs:107
 at NSOutlineViewHottness.MainClass.Main (string[]) [0x00007] in /Users/donblas/Programming/Local/NSOutlineViewHottness/NSOutlineViewHottness/Main.cs:14
 at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr) <IL 0x00050, 0xffffffff>

Native stacktrace:

Debug info from gdb:

(lldb) command source -s 0 '/tmp/mono-gdb-commands.qrHllW'
Executing commands in '/private/tmp/mono-gdb-commands.qrHllW'.
(lldb) process attach --pid 79229
Process 79229 stopped
Executable module set to "/Users/donblas/Programming/Local/NSOutlineViewHottness/NSOutlineViewHottness/bin/Debug/NSOutlineViewHottness.app/Contents/MacOS/NSOutlineViewHottness".
Architecture set to: i386-apple-macosx.
(lldb) thread list
Process 79229 stopped
* thread #1: tid = 0x142776, 0x9af75e1a libsystem_kernel.dylib`__wait4 + 10, queue = 'com.apple.main-thread', stop reason = signal SIGSTOP
 thread #2: tid = 0x142790, 0x9af768d2 libsystem_kernel.dylib`kevent64 + 10, queue = 'com.apple.libdispatch-manager'
 thread #3: tid = 0x142792, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #4: tid = 0x142794, 0x9af6fa6a libsystem_kernel.dylib`semaphore_wait_trap + 10
 thread #5: tid = 0x142795, 0x9af75772 libsystem_kernel.dylib`__recvfrom + 10
 thread #6: tid = 0x142799, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #7: tid = 0x14279a, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #8: tid = 0x14279b, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #9: tid = 0x1427f8, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #10: tid = 0x1427fe, 0x9af6fa2e libsystem_kernel.dylib`mach_msg_trap + 10
(lldb) thread backtrace all
* thread #1: tid = 0x142776, 0x9af75e1a libsystem_kernel.dylib`__wait4 + 10, queue = 'com.apple.main-thread', stop reason = signal SIGSTOP
 * frame #0: 0x9af75e1a libsystem_kernel.dylib`__wait4 + 10
   frame #1: 0x986bfb25 libsystem_c.dylib`waitpid$UNIX2003 + 48
   frame #2: 0x028ba36d libmono-2.0.dylib`mono_handle_native_sigsegv(signal=11, ctx=0x03115fe0) + 541 at mini-exceptions.c:2323
   frame #3: 0x0290a8bb libmono-2.0.dylib`mono_arch_handle_altstack_exception(sigctx=<unavailable>, fault_addr=<unavailable>, stack_ovf=0) + 155 at exceptions-x86.c:1159
   frame #4: 0x0280b4fd libmono-2.0.dylib`mono_sigsegv_signal_handler(_dummy=<unavailable>, info=<unavailable>, context=<unavailable>) + 445 at mini.c:6861
   frame #5: 0x91ef403b libsystem_platform.dylib`_sigtramp + 43
   frame #6: 0x9a5dd0bd libobjc.A.dylib`objc_msgSend + 45
   frame #7: 0x96bcec03 libsystem_trace.dylib`_os_activity_initiate + 89
   frame #8: 0x9773ba91 AppKit`-[NSApplication sendAction:to:from:] + 548
   frame #9: 0x9773b82d AppKit`-[NSControl sendAction:to:] + 102
   frame #10: 0x97934d36 AppKit`__26-[NSCell _sendActionFrom:]_block_invoke + 176
   frame #11: 0x96bcec03 libsystem_trace.dylib`_os_activity_initiate + 89
   frame #12: 0x97787975 AppKit`-[NSCell _sendActionFrom:] + 161
   frame #13: 0x979188ea AppKit`-[NSButtonCell _sendActionFrom:] + 55
   frame #14: 0x979366e6 AppKit`__48-[NSCell trackMouse:inRect:ofView:untilMouseUp:]_block_invoke965 + 43
   frame #15: 0x96bcec03 libsystem_trace.dylib`_os_activity_initiate + 89
   frame #16: 0x977a3d00 AppKit`-[NSCell trackMouse:inRect:ofView:untilMouseUp:] + 2815
   frame #17: 0x977a2df4 AppKit`-[NSButtonCell trackMouse:inRect:ofView:untilMouseUp:] + 524
   frame #18: 0x977a233b AppKit`-[NSControl mouseDown:] + 762
   frame #19: 0x97cbc112 AppKit`-[_NSThemeWidget mouseDown:] + 378
   frame #20: 0x97d36d74 AppKit`-[NSWindow _reallySendEvent:] + 12353
   frame #21: 0x977201f9 AppKit`-[NSWindow sendEvent:] + 409
   frame #22: 0x976cdc67 AppKit`-[NSApplication sendEvent:] + 4679
   frame #23: 0x9754807c AppKit`-[NSApplication run] + 1003
```

Esse é um problema que foi muito mais difícil de rastrear. Quando você vê `at <unknown> <0xffffffff>` ou `MonoMac.ObjCRuntime.Runtime.GetNSObject (IntPtr ptr)` na parte superior do rastreamento de pilha gerenciado, parece que estamos tentando executar um código gerenciado com um objeto que foi coletado como lixo. O rastreamento de pilha nativo mostra `trackMouse:inRect:ofView:untilMouseUp` em `NSCell _sendActionFrom`, de modo que ficamos em algum lugar manipulando um evento de clique, tentando realizar o retorno de chamada para C# e terminando em falha.

Em geral, erros como esse são difíceis de rastrear. Adicionei `GC.Collect(2)` a um manipulador de botões para ajudar a rastrear o problema (forçar uma coleta de lixo) para que o problema possa ser reproduzido. Depois de dividir o exemplo em dois por um tempo e remover seções de código até o desaparecimento do problema, foi descoberto que o responsável era [este bug](https://bugzilla.xamarin.com/show_bug.cgi?id=23378):

```csharp
mainWindowController.Window.StandardWindowButton (NSWindowButton.CloseButton).Activated += HandleActivated;
```

O `NSButton` retornado por `StandardWindowButton()` estava sendo coletado, embora um evento estivesse registrado para ele (esse é o bug). Quando se tenta chamar esse evento com clique, ocorre uma falha se o botão é coletado como lixo.

Embora essa não fosse a causa raiz desse problema específico, rastreamentos de pilha como esse também podem ser causados por assinaturas de método incorretas em funções `[Export]`das para Objective-C. Por exemplo, se um método espera que um parâmetro seja um `out string` e você o digita como `string`, podemos ter uma falha da mesma maneira.

## <a name="example-3-callbacks-and-managed-objects"></a>Exemplo 3: retornos de chamada e objetos gerenciados

Muitas APIs Cocoa sofrem um "retorno de chamada" pela biblioteca quando ocorre algum evento, dando a você uma oportunidade de responder, ou quando alguns dados são necessários para executar uma tarefa. Embora você possa considerar principalmente os padrões **Delegado** e **DataSource**, há uma variedade de APIs que trabalham desse modo. Por exemplo, quando você substitui os métodos de um `NSView` e, em seguida, insere-o na árvore visual, você espera que o AppKit retorne a chamada para você quando determinados eventos ocorrem.

Em quase todos os casos, o Xamarin.Mac impedirá corretamente que o objeto gerenciado que for o destino desses retornos de chamada seja coletado como lixo enquanto ainda existir possibilidade de ele ser chamado novamente. No entanto, raramente, bugs na associação podem interromper esse comportamento. Quando isso acontece, você pode ver falhas desagradáveis semelhantes a esta:

```csharp
Thread 0 Crashed:: Dispatch queue: com.apple.main-thread

0   libsystem_kernel.dylib              0x98c2f69a __pthread_kill + 10
1   libsystem_pthread.dylib             0x90341f19 pthread_kill + 101
2   libsystem_c.dylib                   0x9453feee abort + 156
3   libmonosgen-2.0.dylib               0x020bfba5 mono_handle_native_sigsegv + 757
4   libmonosgen-2.0.dylib               0x0210b812 mono_arch_handle_altstack_exception + 162
5   libmonosgen-2.0.dylib               0x0200c55e mono_sigsegv_signal_handler + 446
6   libsystem_platform.dylib            0x9513003b _sigtramp + 43
7   ???                                 0xffffffff 0 + 4294967295
8   libmonosgen-2.0.dylib               0x0200c3a0 mono_sigill_signal_handler + 48
9   com.apple.AppKit                    0x99f76041 -[NSView setFrame:] + 448
10  com.apple.AppKit                    0x9a1fd4ea -[NSToolbarView adjustToWindow:attachedToEdge:] + 198
11  com.apple.AppKit                    0x9a1fd414 -[NSToolbar _adjustViewToWindow] + 68
12  com.apple.AppKit                    0x9a01eb0d -[NSToolbar _windowWillShowToolbar] + 79

```

Este guia ajudará a rastrear bugs dessa natureza se eles aparecerem, relatá-los corretamente para que eles possam ser corrigidos e trabalhar no código para evitá-los até que isso aconteça.

### <a name="locating"></a>Localizar

Em quase todos os casos com bugs dessa natureza, o sintoma primário é de falhas nativas, normalmente com algo semelhante a `mono_sigsegv_signal_handler`, ou `_sigtrap` nos quadros superiores da pilha. O Cocoa está tentando chamar de volta em seu código C#, atingindo um objeto que foi coletado como lixo e falhando. No entanto, nem todas as falhas com esses símbolos são causadas por um problema de associação como esse. É necessário fazer pesquisa adicional para confirmar que esse é o problema. 

O que torna esses bugs difíceis de rastrear é que eles ocorrem apenas **depois** que uma coleta de lixo descartou o objeto em questão. Se você acredita que encontrou um desses bugs, adicione o seguinte código em algum lugar na sua sequência de inicialização:

```csharp
new System.Threading.Thread (() => 
{
    while (true) {
         System.Threading.Thread.Sleep (1000);
         GC.Collect ();
    }
}).Start (); 
```

Isso forçará o aplicativo a executar o coletor de lixo a cada segundo. Execute novamente o seu aplicativo e tente reproduzir o bug. Se você falha imediatamente ou consistentemente em vez de aleatoriamente, isso significa que você está no caminho certo.

### <a name="reporting"></a>Relatórios

A próxima etapa é relatar o problema para o Xamarin para que a associação possa ser corrigida para versões futuras. Se você for o proprietário da licença Enterprise ou comercial, abra um tíquete em 

[visualstudio.microsoft.com/vs/support/](https://visualstudio.microsoft.com/vs/support/)

Caso contrário, procure um problema existente:

- Verifique os [Fóruns do Xamarin.Mac](https://forums.xamarin.com/categories/mac)
- Pesquise o [repositório de problemas](https://github.com/xamarin/xamarin-macios/issues)
- Antes de mudar para problemas do GitHub, os problemas do Xamarin eram rastreados no [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi). Procure lá por problemas correspondentes.
- Se você não encontrar um problema correspondente, envie um novo problema no [repositório de problemas do GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

Os problemas do GitHub são todos públicos. Não é possível ocultar comentários ou anexos. 

Inclua tanto do seguinte quanto possível:

- Um exemplo simples reproduzindo o problema. Isso é **inestimável**, quando possível. 
- O rastreamento de pilha completo da falha.
- O código C# ao redor da falha.   

### <a name="working-around"></a>Solução alternativa

Depois que tiver rastreado o problema, aplicar um patch no problema com uma solução alternativa até que a associação possa ser corrigida pode ser um procedimento simples. O objetivo é evitar que o objeto (**Exibição**, **Delegado**, **DataSource**) que está sendo descartado incorretamente saia da memória, mantendo uma referência em aberto.

Para casos simples em que há apenas uma única instância do objeto, altere o código disto:

```csharp
void AddObject ()
{
    item.View = new MyView ();
    ...
}
```

Para o uso de uma variável estática, deste modo:

```csharp
static NSObject view;
...

void AddObject ()
{
    view = new MyView ();
    item.View = view;
    ...
}
```

Nos casos em que várias instâncias podem ser criadas, um `HashSet` estático pode ser empregado:

```csharp
static HashSet<NSObject> collection = new HashSet<NSObject> ();
...

void AddObject ()
{
    item.View = new MyView ();
    collection.Add (item.View );
    ...
}
```

Depois que a associação foi corrigida e que você atualizou para a versão do Xamarin.Mac que inclui a correção, o código ao redor do trabalho pode ser removido.

## <a name="exception-bubbling-and-objective-c"></a>Propagação de exceção e Objective-C

Você nunca deve permitir que uma exceção do C# "vaze" código gerenciado para o método Objective-C chamador. Se você fizer isso, os resultados serão indefinidos, mas geralmente envolverão falha. Em geral, fazemos todo o possível para propagar informações úteis tanto para falhas nativas quanto para as gerenciadas, a fim de ajudar você a solucionar seus problemas rapidamente.

Sem perder muito tempo explicando os motivos técnicos disto, configurar a infraestrutura para capturar exceções gerenciadas em cada limite gerenciado/nativo é algo especialmente caro e há _muitas_ transições que acontecem em várias operações comuns. Várias operações, especialmente aquelas envolvendo o thread de interface do usuário, deverão ser concluídas rapidamente, ou o seu aplicativo falhará e terá características de desempenho inaceitáveis. Muitos desses retornos de chamada fazem coisas muito simples, que raramente têm a possibilidade de gerar exceções; portanto, essa sobrecarga seria cara e também desnecessária nesses casos.

Assim, nós não configuraríamos esses blocos try/catch para você. Para os locais em que o código faz coisas não triviais (digamos, além de retornar boolianos ou matemática simples), você pode definir os blocos try/catch por conta própria. 
