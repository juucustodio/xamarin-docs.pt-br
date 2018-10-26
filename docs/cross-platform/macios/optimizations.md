---
title: Criar otimizações
description: Este documento explica as várias otimizações que são aplicadas em tempo de compilação para aplicativos xamarin. IOS e xamarin. Mac.
ms.prod: xamarin
ms.assetid: 84B67E31-B217-443D-89E5-CFE1923CB14E
author: conceptdev
ms.author: crdun
ms.date: 04/16/2018
ms.openlocfilehash: 7d67b924253dfea66781f16b5f83007811de5909
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119027"
---
# <a name="build-optimizations"></a>Criar otimizações

Este documento explica as várias otimizações que são aplicadas em tempo de compilação para aplicativos xamarin. IOS e xamarin. Mac.

## <a name="remove-uiapplicationensureuithread--nsapplicationensureuithread"></a>Remover UIApplication.EnsureUIThread / NSApplication.EnsureUIThread

Remove as chamadas para [UIApplication.EnsureUIThread] [ 1] (para xamarin. IOS) ou `NSApplication.EnsureUIThread` (para xamarin. Mac).

Essa otimização será alterado o seguinte tipo de código:

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    global::UIKit.UIApplication.EnsureUIThread ();
    // ...
}
```

da seguinte maneira:

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    // ...
}
```

Essa otimização requer o vinculador a ser habilitado e só é aplicado aos métodos com o `[BindingImpl (BindingImplOptions.Optimizable)]` atributo.

Por padrão, que ele é habilitado para a versão se baseia.

O comportamento padrão pode ser substituído passando `--optimize=[+|-]remove-uithread-checks` para mtouch/mmp.

[1]: https://developer.xamarin.com/api/member/UIKit.UIApplication.EnsureUIThread/

## <a name="inline-intptrsize"></a>IntPtr.Size embutido

O valor da constante de linhas internas de `IntPtr.Size` acordo com a plataforma de destino.

Essa otimização será alterado o seguinte tipo de código:

```csharp
if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

da seguinte maneira (durante a criação de uma plataforma de 64 bits):

```csharp
if (8 == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

Essa otimização requer o vinculador a ser habilitado e só é aplicado aos métodos com o `[BindingImpl (BindingImplOptions.Optimizable)]` atributo.

Por padrão, ele é habilitado se direcionando para uma única arquitetura de ou para o assembly de plataforma (**xamarin**, **Xamarin.TVOS.dll**, **Xamarin.WatchOS.dll** ou  **Xamarin**).

Se estiver direcionando para várias arquiteturas, essa otimização criará assemblies diferentes para a versão de 32 bits e a versão de 64 bits do aplicativo, e ambas as versões terão a serem incluídos no aplicativo, efetivamente, aumentando o tamanho final do aplicativo em vez de reduzi -lo.

O comportamento padrão pode ser substituído passando `--optimize=[+|-]inline-intptr-size` para mtouch/mmp.

## <a name="inline-nsobjectisdirectbinding"></a>NSObject.IsDirectBinding embutido

`NSObject.IsDirectBinding` é uma propriedade de instância que determina se é uma instância específica de um tipo de wrapper ou não (um tipo de wrapper é um tipo gerenciado que é mapeado para um tipo nativo; para instância gerenciada `UIKit.UIView` tipo é mapeado para o nativo `UIView` tipo - o oposto é um tipo de usuário Nesse caso, `class MyUIView : UIKit.UIView` seria um tipo de usuário).

É necessário saber o valor de `IsDirectBinding` ao chamar em Objective-C, pois o valor determina qual versão do `objc_msgSend` usar.

Considerando apenas o código a seguir:

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

Podemos determinar que, na `UIView.SomeProperty` o valor de `IsDirectBinding` não é uma constante e não pode ser embutida:

```csharp
void uiView = new UIView ();
Console.WriteLine (uiView.SomeProperty); /* prints 'true' */
void myView = new MyUIView ();
Console.WriteLine (myView.SomeProperty); // prints 'false'
```

No entanto, é possível examinar todos os tipos no aplicativo e determinar que não existem tipos que herdam `NSUrl`, e, portanto, é seguro para embutir o `IsDirectBinding` valor a uma constante `true`:

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

da seguinte maneira (quando ele pode ser determinado que não há nenhum subclasses de `NSUrl` no aplicativo):

```csharp
if (true) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

Essa otimização requer o vinculador a ser habilitado e só é aplicado aos métodos com o `[BindingImpl (BindingImplOptions.Optimizable)]` atributo.

Ele está sempre habilitado por padrão para o xamarin. IOS e sempre desabilitado por padrão para xamarin. MAC (porque é possível carregar dinamicamente os assemblies no xamarin. Mac, não é possível determinar que uma determinada classe nunca é uma subclasse).

O comportamento padrão pode ser substituído passando `--optimize=[+|-]inline-isdirectbinding` para mtouch/mmp.

## <a name="inline-runtimearch"></a>Runtime.Arch embutido

Essa otimização será alterado o seguinte tipo de código:

```csharp
if (Runtime.Arch == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

da seguinte maneira (durante a criação do dispositivo):

```csharp
if (Arch.DEVICE == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

Essa otimização requer o vinculador a ser habilitado e só é aplicado aos métodos com o `[BindingImpl (BindingImplOptions.Optimizable)]` atributo.

Ele está sempre habilitado por padrão para xamarin. IOS (ele não está disponível para xamarin. Mac).

O comportamento padrão pode ser substituído passando `--optimize=[+|-]inline-runtime-arch` para mtouch.

## <a name="dead-code-elimination"></a>Eliminação de código inativo

Essa otimização será alterado o seguinte tipo de código:

```csharp
if (true) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

em:

```csharp
Console.WriteLine ("Doing this");
```

Ela também será avaliada constante comparações, como este:

```csharp
if (8 == 8) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

e determinar que a expressão `8 == 8` é um sempre verdadeiro e reduzi-lo para:

```csharp
Console.WriteLine ("Doing this");
```

Isso é uma otimização de poderosa quando usado junto com as otimizações de inlining, porque ele pode transformar o seguinte tipo de código (esse é o código de associação para `NFCIso15693ReadMultipleBlocksConfiguration.Range`):

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

Para isso (durante a criação de um dispositivo de 64 bits e quando também capaz de garantir que há nenhum `NFCIso15693ReadMultipleBlocksConfiguration` subclasses no aplicativo):

```csharp
NSRange ret;
ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
return ret;
```

O compilador AOT já é capaz de eliminar o código morto como este, mas essa otimização é feita no vinculador, o que significa que o vinculador pode ver que há vários métodos que não são mais usados e, portanto, podem ser removidos (a menos que usado em outro lugar) :

* `global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret`

Essa otimização requer o vinculador a ser habilitado e só é aplicado aos métodos com o `[BindingImpl (BindingImplOptions.Optimizable)]` atributo.

Ele está sempre habilitado por padrão (quando o vinculador está habilitado).

O comportamento padrão pode ser substituído passando `--optimize=[+|-]dead-code-elimination` para mtouch/mmp.

## <a name="optimize-calls-to-blockliteralsetupblock"></a>Otimizar a chamadas para BlockLiteral.SetupBlock

O tempo de execução Xamarin.iOS/Mac precisa saber a assinatura do bloco durante a criação de um bloco de Objective-C para um delegado. Isso pode ser uma operação relativamente cara. Essa otimização calculam a assinatura de bloco em tempo de compilação e modificar o IL para chamar um `SetupBlock` método que usa a assinatura como um argumento em vez disso. Isso evita a necessidade para calcular a assinatura em tempo de execução.

Parâmetros de comparação mostram que isso acelera um bloco de chamada por um fator de 10 a 15.

Ele transformará o seguinte [código](https://github.com/xamarin/xamarin-macios/blob/018f7153441d9d7e0f58e2046f39eeb46f1ff480/src/UIKit/UIAccessibility.cs#L198-L211):

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlock (callback, completionHandler);
    // ...
}
```

em:

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlockImpl (callback, completionHandler, true, "v@?B");
    // ...
}
```

Essa otimização requer o vinculador a ser habilitado e só é aplicado aos métodos com o `[BindingImpl (BindingImplOptions.Optimizable)]` atributo.

Ele é habilitado por padrão ao usar o registrador estático (em xamarin. IOS o registrador estático está habilitado por padrão para compilações de dispositivo e no registrador de estática é habilitado por padrão para a versão de xamarin. Mac se baseia).

O comportamento padrão pode ser substituído passando `--optimize=[+|-]blockliteral-setupblock` para mtouch/mmp.

## <a name="optimize-support-for-protocols"></a>Otimizar o suporte para protocolos

O tempo de execução Xamarin.iOS/Mac precisa obter informações sobre protocolos de implementa Objective-C de tipos gerenciados. Essas informações são armazenadas em interfaces (e os atributos nessas interfaces), que não é um formato muito eficiente, nem é amigável para o vinculador.

Um exemplo é que essas interfaces armazenam informações sobre todos os membros de protocolo em um `[ProtocolMember]` atributo, que, entre outras coisas, contêm referências para os tipos de parâmetro desses membros. Isso significa que basta implementar uma interface desse tipo fará com que o vinculador preservar todos os tipos usados nessa interface, mesmo para membros opcionais o nunca chama ou o aplicativo implementa.

Essa otimização fará com que o registrador estático armazenar todas as informações necessárias em um formato eficiente que utiliza pouca memória que é fácil e rápido encontrar no tempo de execução.

Ele também ensina o vinculador que ele não necessariamente precisa preservar essas interfaces, nem qualquer um dos atributos relacionados.

Essa otimização requer que o vinculador e o registrador estático ser habilitado.

No xamarin. IOS essa otimização é habilitada por padrão quando o vinculador e o registrador estático estão habilitados.

No xamarin. Mac essa otimização nunca é habilitada por padrão, porque dá suporte de xamarin. Mac carregar assemblies dinamicamente e os assemblies podem não foram conhecidas em tempo de compilação (e, portanto, não otimizada).

O comportamento padrão pode ser substituído passando `--optimize=-register-protocols` para mtouch/mmp.

## <a name="remove-the-dynamic-registrar"></a>Remover o registrador dinâmico

O xamarin. IOS e o tempo de execução do xamarin. Mac incluem suporte para [registrando tipos gerenciados](https://developer.xamarin.com/guides/ios/advanced_topics/registrar/) com o tempo de execução do Objective-C. Ele pode ser feito no momento da compilação ou em tempo de execução (ou parcialmente em tempo de compilação e o restante em tempo de execução), mas se estiver totalmente pronto no momento da compilação, isso significa que o código de suporte para fazê-lo em tempo de execução pode ser removido. Isso resulta em uma redução significativa no tamanho do aplicativo, em particular para aplicativos menores, como extensões ou aplicativos watchOS.

Essa otimização requer o registrador estático e o vinculador a ser habilitado.

O vinculador tenta determinar se é seguro remover o registrador dinâmico e se, portanto, tentar removê-lo.

Uma vez que o xamarin. Mac dá suporte a dinamicamente ao carregar assemblies em tempo de execução (que não eram conhecidas no momento da compilação), é impossível determinar no tempo de compilação se esta é uma otimização de segurança. Isso significa que essa otimização nunca é habilitada por padrão para aplicativos xamarin. Mac.

O comportamento padrão pode ser substituído passando `--optimize=[+|-]remove-dynamic-registrar` para mtouch/mmp.

Se o padrão é substituído para remover o registrador dinâmico, o vinculador emitirá avisos se detectar que não é seguro (mas o registrador dinâmico ainda será removido).

## <a name="inline-runtimedynamicregistrationsupported"></a>Runtime.DynamicRegistrationSupported embutido

O valor de linhas internas de `Runtime.DynamicRegistrationSupported` conforme determinado no momento da compilação.

Se o registrador dinâmico for removido (consulte a [remover o registrador dinâmico](#remove-the-dynamic-registrar) otimização), isso é uma constante `false` o valor, caso contrário, ela é uma constante `true` valor.

Essa otimização será alterado o seguinte tipo de código:

```csharp
if (Runtime.DynamicRegistrationSupported) {
    Console.WriteLine ("do something");
} else {
    throw new Exception ("dynamic registration is not supported");
}
```

para o seguinte quando o registrador dinâmico é removido:

```csharp
throw new Exception ("dynamic registration is not supported");
```

para o seguinte quando o registrador dinâmico não é removido:

```csharp
Console.WriteLine ("do something");
```

Essa otimização requer o vinculador a ser habilitado e só é aplicado aos métodos com o `[BindingImpl (BindingImplOptions.Optimizable)]` atributo.

Ele está sempre habilitado por padrão (quando o vinculador está habilitado).

O comportamento padrão pode ser substituído passando `--optimize=[+|-]inline-dynamic-registration-supported` para mtouch/mmp.

## <a name="precompute-methods-to-create-managed-delegates-for-objective-c-blocks"></a>Pré-calcular métodos para criar delegados gerenciados para blocos de Objective-C

Quando Objective-C chama um seletor que usa um bloco como um parâmetro e, em seguida, o código gerenciado tenha substituído esse método, o xamarin. IOS / precisa criar um delegado para esse bloco de tempo de execução do xamarin. Mac.

O código de associação gerado pelo gerador de associação incluirá uma `[BlockProxy]` atributo, que especifica o tipo com um `Create` método que pode fazer isso.

Considerando o seguinte código de Objective-C:

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

e o código de associação a seguir:

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

Quando chama Objective-C `[ObjCBlockTester callClassCallback]`, o xamarin. IOS / xamarin. MAC de tempo de execução examinará a `[BlockProxy (typeof (Trampolines.NIDActionArity1V0))]` atributo no parâmetro. Ele irá pesquisar, em seguida, o `Create` método nesse tipo e chamar esse método para criar o delegado.

Essa otimização encontrará o `Create` método em tempo de compilação e o registrador estático irá gerar o código que pesquisa o método em tempo de execução usando os tokens de metadados em vez de usar o atributo e reflexão (Isso é muito mais rápido e também permite que o vinculador Para remover o código de tempo de execução correspondentes, tornando o aplicativo menor).

Se o mmp/mtouch não consegue encontrar o `Create` método, em seguida, será mostrado um aviso MT4174/MM4174 e a pesquisa será executada em tempo de execução em vez disso.
A causa mais provável manualmente é escrita o código de associação sem necessários `[BlockProxy]` atributos.

Essa otimização requer o registrador estático ser habilitado.

Ele está sempre habilitado por padrão (desde que o registrador estático está habilitado).

O comportamento padrão pode ser substituído passando `--optimize=[+|-]static-delegate-to-block-lookup` para mtouch/mmp.
