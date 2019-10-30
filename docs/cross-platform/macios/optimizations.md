---
title: Otimizações de compilação
description: Este documento explica as várias otimizações que são aplicadas no momento da compilação para os aplicativos Xamarin. iOS e Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 84B67E31-B217-443D-89E5-CFE1923CB14E
author: davidortinau
ms.author: daortin
ms.date: 04/16/2018
ms.openlocfilehash: 22028743742a618bd7347d5e49153defecd4e3bb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015177"
---
# <a name="build-optimizations"></a>Otimizações de compilação

Este documento explica as várias otimizações que são aplicadas no momento da compilação para os aplicativos Xamarin. iOS e Xamarin. Mac.

## <a name="remove-uiapplicationensureuithread--nsapplicationensureuithread"></a>Remover UIApplication. EnsureUIThread/NSApplication. EnsureUIThread

Remove chamadas para [UIApplication. EnsureUIThread][1] (para Xamarin. Ios) ou `NSApplication.EnsureUIThread` (para Xamarin. Mac).

Essa otimização irá alterar o seguinte tipo de código:

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    global::UIKit.UIApplication.EnsureUIThread ();
    // ...
}
```

para o seguinte:

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    // ...
}
```

Essa otimização requer que o vinculador esteja habilitado e seja aplicado somente a métodos com o atributo `[BindingImpl (BindingImplOptions.Optimizable)]`.

Por padrão, ele é habilitado para compilações de versão.

O comportamento padrão pode ser substituído passando `--optimize=[+|-]remove-uithread-checks` para mTouch/MMP.

[1]: https://docs.microsoft.com/dotnet/api/UIKit.UIApplication.EnsureUIThread

## <a name="inline-intptrsize"></a>IntPtr embutido. tamanho

Embuti o valor constante de `IntPtr.Size` de acordo com a plataforma de destino.

Essa otimização irá alterar o seguinte tipo de código:

```csharp
if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

para o seguinte (ao compilar para uma plataforma de 64 bits):

```csharp
if (8 == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

Essa otimização requer que o vinculador esteja habilitado e seja aplicado somente a métodos com o atributo `[BindingImpl (BindingImplOptions.Optimizable)]`.

Por padrão, ele é habilitado se estiver direcionando a uma única arquitetura ou para o assembly da plataforma (**xamarin. Ios. dll**, **xamarin. TVOS. dll**, **xamarin. WatchOS. dll** ou **xamarin. Mac. dll**).

Se estiver direcionando várias arquiteturas, essa otimização criará diferentes assemblies para a versão de 32 bits e a versão de 64 bits do aplicativo, e ambas as versões precisarão ser incluídas no aplicativo, aumentando efetivamente o tamanho final do aplicativo em vez de diminuir fosse.

O comportamento padrão pode ser substituído passando `--optimize=[+|-]inline-intptr-size` para mTouch/MMP.

## <a name="inline-nsobjectisdirectbinding"></a>NSObject embutido. isdirectbinding

`NSObject.IsDirectBinding` é uma propriedade de instância que determina se uma determinada instância é de um tipo de wrapper ou não (um tipo de wrapper é um tipo gerenciado que é mapeado para um tipo nativo; por exemplo, o tipo de `UIKit.UIView` gerenciado é mapeado para o tipo de `UIView` nativo-o oposto é um tipo de usuário , nesse caso `class MyUIView : UIKit.UIView` seria um tipo de usuário).

É necessário saber o valor de `IsDirectBinding` ao chamar o Objective-C, pois o valor determina qual versão do `objc_msgSend` usar.

Dado apenas o código a seguir:

```csharp
class UIView : NSObject {
    public virtual string SomeProperty {
        get {
            if (IsDirectBinding) {
                return "true";
            } else {
                return "false"
            }
        }
    }
}

class NSUrl : NSObject {
    public virtual string SomeOtherProperty {
        get {
            if (IsDirectBinding) {
                return "true";
            } else {
                return "false"
            }
        }
    }
}

class MyUIView : UIView {
}
```

Podemos determinar que, em `UIView.SomeProperty` o valor de `IsDirectBinding` não é uma constante e não pode ser embutido:

```csharp
void uiView = new UIView ();
Console.WriteLine (uiView.SomeProperty); /* prints 'true' */
void myView = new MyUIView ();
Console.WriteLine (myView.SomeProperty); // prints 'false'
```

No entanto, é possível examinar todos os tipos no aplicativo e determinar que não há tipos que herdam de `NSUrl`e, portanto, é seguro embutir o valor de `IsDirectBinding` em uma constante `true`:

```csharp
void myURL = new NSUrl ();
Console.WriteLine (myURL.SomeOtherProperty); // prints 'true'
// There's no way to make SomeOtherProperty print anything but 'true', since there are no NSUrl subclasses.
```

Em particular, essa otimização alterará o seguinte tipo de código (esse é o código de associação para `NSUrl.AbsoluteUrl`):

```csharp
if (IsDirectBinding) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

no seguinte (quando pode ser determinado que não há subclasses de `NSUrl` no aplicativo):

```csharp
if (true) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

Essa otimização requer que o vinculador esteja habilitado e seja aplicado somente a métodos com o atributo `[BindingImpl (BindingImplOptions.Optimizable)]`.

Ele é sempre habilitado por padrão para Xamarin. iOS e sempre desabilitado por padrão para o Xamarin. Mac (porque é possível carregar dinamicamente os assemblies no Xamarin. Mac, não é possível determinar que uma classe específica nunca seja subclasseda).

O comportamento padrão pode ser substituído passando `--optimize=[+|-]inline-isdirectbinding` para mTouch/MMP.

## <a name="inline-runtimearch"></a>Tempo de execução embutido. Arch

Essa otimização irá alterar o seguinte tipo de código:

```csharp
if (Runtime.Arch == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

para o seguinte (ao compilar para o dispositivo):

```csharp
if (Arch.DEVICE == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

Essa otimização requer que o vinculador esteja habilitado e seja aplicado somente a métodos com o atributo `[BindingImpl (BindingImplOptions.Optimizable)]`.

Ele está sempre habilitado por padrão para o Xamarin. iOS (ele não está disponível para o Xamarin. Mac).

O comportamento padrão pode ser substituído passando `--optimize=[+|-]inline-runtime-arch` para mTouch.

## <a name="dead-code-elimination"></a>Eliminação de código inativo

Essa otimização irá alterar o seguinte tipo de código:

```csharp
if (true) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

porta

```csharp
Console.WriteLine ("Doing this");
```

Ele também irá avaliar as comparações constantes, como esta:

```csharp
if (8 == 8) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

e determinar se a expressão `8 == 8` é sempre verdadeira e reduzi-la para:

```csharp
Console.WriteLine ("Doing this");
```

Essa é uma otimização poderosa quando usada junto com as otimizações de inalinhamento, pois ela pode transformar o seguinte tipo de código (esse é o código de associação para `NFCIso15693ReadMultipleBlocksConfiguration.Range`):

```csharp
NSRange ret;
if (IsDirectBinding) {
    if (Runtime.Arch == Arch.DEVICE) {
        if (IntPtr.Size == 8) {
            ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
        } else {
            global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret (out ret, this.Handle, Selector.GetHandle ("range"));
        }
    } else if (IntPtr.Size == 8) {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
    } else {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
    }
} else {
    if (Runtime.Arch == Arch.DEVICE) {
        if (IntPtr.Size == 8) {
            ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
        } else {
            global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret (out ret, this.SuperHandle, Selector.GetHandle ("range"));
        }
    } else if (IntPtr.Size == 8) {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
    } else {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
    }
}
return ret;
```

para isso (ao compilar para um dispositivo de 64 bits e quando também é possível garantir que não haja nenhuma subclasse `NFCIso15693ReadMultipleBlocksConfiguration` no aplicativo):

```csharp
NSRange ret;
ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
return ret;
```

O compilador AOT já é capaz de eliminar códigos mortos como esse, mas essa otimização é feita dentro do vinculador, o que significa que o vinculador consegue ver que há vários métodos que não são mais usados e, portanto, podem ser removidos (a menos que sejam usados em outro lugar) :

* `global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret`

Essa otimização requer que o vinculador esteja habilitado e seja aplicado somente a métodos com o atributo `[BindingImpl (BindingImplOptions.Optimizable)]`.

Ele sempre é habilitado por padrão (quando o vinculador está habilitado).

O comportamento padrão pode ser substituído passando `--optimize=[+|-]dead-code-elimination` para mTouch/MMP.

## <a name="optimize-calls-to-blockliteralsetupblock"></a>Otimizar chamadas para BlockLiteral. SetupBlock

O tempo de execução do Xamarin. iOS/Mac precisa saber a assinatura de bloco ao criar um bloco Objective-C para um delegado gerenciado. Essa pode ser uma operação bastante cara. Essa otimização calculará a assinatura de bloco no momento da compilação e modificará o IL para chamar um método de `SetupBlock` que usa a assinatura como um argumento em vez disso. Isso evita a necessidade de calcular a assinatura em tempo de execução.

Os parâmetros de comparação mostram que isso acelera a chamada de um bloco por um fator de 10 a 15.

Ele irá transformar o seguinte [código](https://github.com/xamarin/xamarin-macios/blob/018f7153441d9d7e0f58e2046f39eeb46f1ff480/src/UIKit/UIAccessibility.cs#L198-L211):

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlock (callback, completionHandler);
    // ...
}
```

porta

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlockImpl (callback, completionHandler, true, "v@?B");
    // ...
}
```

Essa otimização requer que o vinculador esteja habilitado e seja aplicado somente a métodos com o atributo `[BindingImpl (BindingImplOptions.Optimizable)]`.

Ele é habilitado por padrão ao usar o registrador estático (no Xamarin. iOS, o registrador estático é habilitado por padrão para compilações de dispositivo e, no Xamarin. Mac, o registrador estático é habilitado por padrão para compilações de versão).

O comportamento padrão pode ser substituído passando `--optimize=[+|-]blockliteral-setupblock` para mTouch/MMP.

## <a name="optimize-support-for-protocols"></a>Otimizar o suporte para protocolos

O tempo de execução do Xamarin. iOS/Mac precisa de informações sobre como os tipos gerenciados implementam os protocolos Objective-C. Essas informações são armazenadas em interfaces (e atributos nessas interfaces), o que não é um formato muito eficiente, nem é amigável para vinculador.

Um exemplo é que essas interfaces armazenam informações sobre todos os membros de protocolo em um `[ProtocolMember]` atributo, que entre outras coisas contêm referências aos tipos de parâmetro desses membros. Isso significa que simplesmente implementar tal interface fará com que o vinculador preserve todos os tipos usados nessa interface, mesmo para membros opcionais que o aplicativo nunca chama ou implementa.

Essa otimização fará com que o registrador estático armazene todas as informações necessárias em um formato eficiente que use pouca memória que seja fácil e rápida de encontrar no tempo de execução.

Ele também ensinará o vinculador de que ele não precisa necessariamente preservar essas interfaces nem qualquer um dos atributos relacionados.

Essa otimização exige que o vinculador e o registrador estático sejam habilitados.

No Xamarin. iOS, essa otimização é habilitada por padrão quando o vinculador e o registrador estático estão habilitados.

No Xamarin. Mac, essa otimização nunca é habilitada por padrão, pois o Xamarin. Mac dá suporte ao carregamento de assemblies dinamicamente, e esses assemblies podem não ter sido conhecidos no momento da compilação (e, portanto, não são otimizados).

O comportamento padrão pode ser substituído passando `--optimize=-register-protocols` para mTouch/MMP.

## <a name="remove-the-dynamic-registrar"></a>Remover o registrador dinâmico

O Xamarin. iOS e o tempo de execução do Xamarin. Mac incluem suporte para o [registro de tipos gerenciados](~/ios/internals/registrar.md) com o tempo de execução Objective-C. Ele pode ser feito no momento da compilação ou em tempo de execução (ou parcialmente no tempo de compilação e no restante em tempo de execução), mas se for completamente feito no momento da compilação, significa que o código de suporte para fazer isso em tempo de execução pode ser removido. Isso resulta em uma diminuição significativa no tamanho do aplicativo, especialmente para aplicativos menores, como extensões ou aplicativos watchOS.

Essa otimização exige que o registrador estático e o vinculador sejam habilitados.

O vinculador tentará determinar se é seguro remover o registrador dinâmico e, se for, tentará removê-lo.

Como o Xamarin. Mac dá suporte ao carregamento dinâmico de assemblies em tempo de execução (que não eram conhecidos no momento da compilação), é impossível determinar no momento da compilação se essa é uma otimização segura. Isso significa que essa otimização nunca é habilitada por padrão para aplicativos Xamarin. Mac.

O comportamento padrão pode ser substituído passando `--optimize=[+|-]remove-dynamic-registrar` para mTouch/MMP.

Se o padrão for substituído para remover o registrador dinâmico, o vinculador emitirá avisos se detectar que ele não é seguro (mas o registrador dinâmico ainda será removido).

## <a name="inline-runtimedynamicregistrationsupported"></a>Tempo de execução embutido. DynamicRegistrationSupported

Embuti o valor de `Runtime.DynamicRegistrationSupported` conforme determinado no momento da compilação.

Se o registrador dinâmico for removido (consulte [remover a otimização do registrador dinâmico](#remove-the-dynamic-registrar) ), essa é uma constante `false` valor, caso contrário, é uma constante `true` valor.

Essa otimização irá alterar o seguinte tipo de código:

```csharp
if (Runtime.DynamicRegistrationSupported) {
    Console.WriteLine ("do something");
} else {
    throw new Exception ("dynamic registration is not supported");
}
```

para o seguinte quando o registrador dinâmico for removido:

```csharp
throw new Exception ("dynamic registration is not supported");
```

para o seguinte quando o registrador dinâmico não for removido:

```csharp
Console.WriteLine ("do something");
```

Essa otimização requer que o vinculador esteja habilitado e seja aplicado somente a métodos com o atributo `[BindingImpl (BindingImplOptions.Optimizable)]`.

Ele sempre é habilitado por padrão (quando o vinculador está habilitado).

O comportamento padrão pode ser substituído passando `--optimize=[+|-]inline-dynamic-registration-supported` para mTouch/MMP.

## <a name="precompute-methods-to-create-managed-delegates-for-objective-c-blocks"></a>Métodos pré-calcular para criar delegados gerenciados para blocos Objective-C

Quando Objective-C chama um seletor que usa um bloco como parâmetro e, em seguida, o código gerenciado tem substituído esse método, o tempo de execução do Xamarin. iOS/Xamarin. Mac precisa criar um delegado para esse bloco.

O código de associação gerado pelo gerador de associação incluirá um atributo `[BlockProxy]`, que especifica o tipo com um método `Create` que pode fazer isso.

Dado o seguinte código Objective-C:

```objc
@interface ObjCBlockTester : NSObject {
}
-(void) classCallback: (void (^)())completionHandler;
-(void) callClassCallback;
@end

@implementation ObjCBlockTester
-(void) classCallback: (void (^)())completionHandler
{
}

-(void) callClassCallback
{
    [self classCallback: ^()
    {
        NSLog (@"called!");
    }];
}
@end
```

e o seguinte código de ligação:

```csharp
[BaseType (typeof (NSObject))]
interface ObjCBlockTester
{
    [Export ("classCallback:")]
    void ClassCallback (Action completionHandler);
}
```

o gerador produzirá:

```csharp
[Register("ObjCBlockTester", true)]
public unsafe partial class ObjCBlockTester : NSObject {
    // unrelated code...

    [Export ("callClassCallback")]
    [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
    public virtual void CallClassCallback ()
    {
        if (IsDirectBinding) {
            ApiDefinition.Messaging.void_objc_msgSend (this.Handle, Selector.GetHandle ("callClassCallback"));
        } else {
            ApiDefinition.Messaging.void_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("callClassCallback"));
        }
    }

    [Export ("classCallback:")]
    [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
    public unsafe virtual void ClassCallback ([BlockProxy (typeof (Trampolines.NIDActionArity1V0))] System.Action completionHandler)
    {
        // ...

    }
}

static class Trampolines
{
    [UnmanagedFunctionPointerAttribute (CallingConvention.Cdecl)]
    [UserDelegateType (typeof (System.Action))]
    internal delegate void DActionArity1V0 (IntPtr block);

    static internal class SDActionArity1V0 {
        static internal readonly DActionArity1V0 Handler = Invoke;

        [MonoPInvokeCallback (typeof (DActionArity1V0))]
        static unsafe void Invoke (IntPtr block) {
            var descriptor = (BlockLiteral *) block;
            var del = (System.Action) (descriptor->Target);
            if (del != null)
                del (obj);
        }
    }

    internal class NIDActionArity1V0 {
        IntPtr blockPtr;
        DActionArity1V0 invoker;

        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        public unsafe NIDActionArity1V0 (BlockLiteral *block)
        {
            blockPtr = _Block_copy ((IntPtr) block);
            invoker = block->GetDelegateForBlock<DActionArity1V0> ();
        }

        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        ~NIDActionArity1V0 ()
        {
            _Block_release (blockPtr);
        }

        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        public unsafe static System.Action Create (IntPtr block)
        {
            if (block == IntPtr.Zero)
                return null;
            if (BlockLiteral.IsManagedBlock (block)) {
                var existing_delegate = ((BlockLiteral *) block)->Target as System.Action;
                if (existing_delegate != null)
                    return existing_delegate;
            }
            return new NIDActionArity1V0 ((BlockLiteral *) block).Invoke;
        }

        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        unsafe void Invoke ()
        {
            invoker (blockPtr);
        }
    }
}
```

Quando Objective-C chama `[ObjCBlockTester callClassCallback]`, o tempo de execução do Xamarin. iOS/Xamarin. Mac examinará o atributo `[BlockProxy (typeof (Trampolines.NIDActionArity1V0))]` no parâmetro. Em seguida, ele pesquisará o método `Create` nesse tipo e chamará esse método para criar o delegado.

Essa otimização encontrará o método `Create` no momento da compilação, e o registrador estático gerará código que pesquisa o método em tempo de execução usando os tokens de metadados usando o atributo e a reflexão (isso é muito mais rápido e também permite que o vinculador Remova o código de tempo de execução correspondente, tornando o aplicativo menor).

Se MMP/mTouch não conseguir localizar o método `Create`, um aviso de MT4174/MM4174 será mostrado e a pesquisa será executada em tempo de execução.
A causa mais provável é o código de associação gravado manualmente sem os atributos de `[BlockProxy]` necessários.

Essa otimização requer que o registrador estático esteja habilitado.

Ele sempre é habilitado por padrão (desde que o registrador estático esteja habilitado).

O comportamento padrão pode ser substituído passando `--optimize=[+|-]static-delegate-to-block-lookup` para mTouch/MMP.
