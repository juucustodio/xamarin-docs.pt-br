---
title: Seletores de Objective-C no xamarin. IOS
description: Este documento discute como interagir com os seletores de Objective-C do c#. Ele descreve como invocar seletores e considerações técnicas que devem ser levadas em conta ao fazer isso.
ms.prod: xamarin
ms.assetid: A80904C4-6A89-389B-0487-057AFEB70989
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/12/2017
ms.openlocfilehash: b51ee6b547cc53761f23379e7233bb710090a61b
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "39351724"
---
# <a name="objective-c-selectors-in-xamarinios"></a>Seletores de Objective-C no xamarin. IOS

A linguagem do Objective-C é baseada no *seletores*. Um seletor é uma mensagem que pode ser enviada a um objeto ou uma *classe*. [Xamarin. IOS](~/ios/internals/api-design/index.md) mapas seletores para métodos de instância da instância e seletores para métodos estáticos de classes.

Ao contrário das funções de C normais (e, como funções de membro de C++), você não pode invocar diretamente usando um seletor [P/Invoke](http://www.mono-project.com/docs/advanced/pinvoke/) em vez disso, os seletores são enviadas para uma classe de Objective-C ou instância usando o [`objc_msgSend`](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend)
função.

Para obter mais informações sobre mensagens em Objective-C, dê uma olhada da Apple [trabalhando com objetos](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithObjects/WorkingwithObjects.html#//apple_ref/doc/uid/TP40011210-CH4-SW2) guia.

## <a name="example"></a>Exemplo

Suponha que você deseja invocar o [`sizeWithFont:forWidth:lineBreakMode:`](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont)
seletor de [ `NSString` ](https://developer.apple.com/documentation/foundation/nsstring).
A declaração (da documentação da Apple) é:

```objc
- (CGSize)sizeWithFont:(UIFont *)font forWidth:(CGFloat)width lineBreakMode:(UILineBreakMode)lineBreakMode
```

Essa API tem as seguintes características:

- O tipo de retorno é `CGSize` para a API unificada.
- O `font` parâmetro é um [UIFont](https://developer.xamarin.com/api/type/UIKit.UIFont/) (e um tipo derivado (indiretamente) de [NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/)) e é mapeado para [System. IntPtr](xref:System.IntPtr).
- O `width` parâmetro, uma `CGFloat`, é mapeado para `nfloat`.
- O `lineBreakMode` parâmetro, uma [ `UILineBreakMode` ](https://developer.apple.com/documentation/uikit/uilinebreakmode?language=objc), já foi associado no xamarin. IOS como o [`UILineBreakMode`](https://developer.xamarin.com/api/type/UIKit.UILineBreakMode/)
Enumeração.

Juntando as peças, a `objc_msgSend` deve corresponder a declaração:

```csharp
CGSize objc_msgSend(
    IntPtr target, 
    IntPtr selector, 
    IntPtr font, 
    nfloat width, 
    UILineBreakMode mode
);
```

Declará-la da seguinte maneira:

```csharp
[DllImport (Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend")]
static extern CGSize cgsize_objc_msgSend_IntPtr_float_int (
    IntPtr target, 
    IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode
);
```

Para chamar esse método, use o código como o seguinte:

```csharp
NSString target = ...
Selector selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont font = ...
nfloat width = ...
UILineBreakMode mode = ...

CGSize size = cgsize_objc_msgSend_IntPtr_float_int(
    target.Handle, 
    selector.Handle,
    font == null ? IntPtr.Zero : font.Handle,
    width,
    mode
);
```

O valor retornado foi uma estrutura que foi menor que 8 bytes de tamanho (como o mais antigo `SizeF` usado antes de alternar para as APIs unificadas), o código acima teria executado no simulador, mas com falha no dispositivo. Para chamar um seletor que retorna um valor menor que 8 bits de tamanho, declare o `objc_msgSend_stret` função:

```csharp
[DllImport (MonoTouch.Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend_stret")]
static extern void cgsize_objc_msgSend_stret_IntPtr_float_int (
    out CGSize retval,
    IntPtr target, 
    IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode
);
```

Para chamar esse método, use o código como o seguinte:

```csharp
NSString      target = ...
Selector    selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont          font = ...
nfloat          width = ...
UILineBreakMode mode = ...

CGSize size;

if (Runtime.Arch == Arch.SIMULATOR)
    size = cgsize_objc_msgSend_IntPtr_float_int(
        target.Handle, 
        selector.Handle,
        font == null ? IntPtr.Zero : font.Handle,
        width,
        mode
    );
else
    cgsize_objc_msgSend_stret_IntPtr_float_int(
        out size,
        target.Handle, selector.Handle,
        font == null ? IntPtr.Zero: font.Handle,
        width,
        mode
    );
```

## <a name="invoking-a-selector"></a>Invocar um seletor

Invocar um seletor tem três etapas:

1. Obtenha o destino do seletor.
2. Obtenha o nome do seletor.
3. Chamar `objc_msgSend` com os argumentos apropriados.

### <a name="selector-targets"></a>Destinos do seletor

Um destino do seletor é uma instância do objeto ou uma classe de Objective-C. Se o destino é uma instância e veio de um tipo associado do xamarin. IOS, use o [ `ObjCRuntime.INativeObject.Handle` ](https://developer.xamarin.com/api/property/ObjCRuntime.INativeObject.Handle/) propriedade.

Se o destino é uma classe, use [ `ObjCRuntime.Class` ](https://developer.xamarin.com/api/type/ObjCRuntime.Class/) para obter uma referência à instância de classe, em seguida, use o [ `Class.Handle` ](https://developer.xamarin.com/api/property/ObjCRuntime.Class.Handle/) propriedade.

### <a name="selector-names"></a>Nomes do seletor

Nomes de seletor são listados na documentação da Apple. Por exemplo, [ `NSString` ](https://developer.apple.com/documentation/foundation/nsstring?language=objc) inclui [ `sizeWithFont:` ](https://developer.apple.com/documentation/foundation/nsstring/1619917-sizewithfont?language=objc) e [ `sizeWithFont:forWidth:lineBreakMode:` ](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont?language=objc) seletores. Os dois-pontos à direita e inseridos são parte do nome do seletor e não podem ser omitidos.

Quando você tiver um nome de seletor, você pode criar uma [ `ObjCRuntime.Selector` ](https://developer.xamarin.com/api/type/ObjCRuntime.Selector/) instância para ele.

### <a name="calling-objcmsgsend"></a>Chamar objc_msgSend

`objc_msgSend` envia uma mensagem (seletor) para um objeto. Essa família de funções leva pelo menos dois argumentos necessários: o destino do seletor (uma instância ou classe manipular), o seletor de si mesmo e quaisquer argumentos necessários para o seletor. Os argumentos de instância e o seletor devem ser `System.IntPtr`, e todos os argumentos restantes devem corresponder ao tipo que o seletor de espera, por exemplo uma `nint` para um `int`, ou uma `System.IntPtr` para todos os `NSObject`-tipos derivados. Use o [`NSObject.Handle`](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/)
propriedade para obter um `IntPtr` para uma instância do tipo Objective-C.

Há mais de um `objc_msgSend` função:

- Use [ `objc_msgSend_stret` ](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc) para seletores que retornam um struct. No ARM, isso inclui o retorno de todos os tipos que não é uma enumeração ou qualquer um dos tipos internos C (`char`, `short`, `int`, `long`, `float`, `double`). Em x86 (o simulador), esse método precisa ser usado para todas as estruturas maiores que 8 bytes de tamanho (`CGSize` é de 8 bytes e não usar `objc_msgSend_stret` no simulador). 
- Use [ `objc_msgSend_fpret` ](https://developer.apple.com/documentation/objectivec/1456697-objc_msgsend_fpret?language=objc) para valor de ponto de seletores que retornam flutuante em x86 somente. Essa função não precisa ser usado no ARM; em vez disso, use `objc_msgSend`. 
- A principal [objc_msgSend](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend) função é usada para todos os outros seletores.

Depois de decidir qual `objc_msgSend` funções, você precisará chamar (dispositivo e simulador podem exigir um método diferente), você pode usar um normal [ `[DllImport]` ](xref:System.Runtime.InteropServices.DllImportAttribute) método declarar a função para invocação posterior.

Um conjunto de pré-criado `objc_msgSend` declarações podem ser encontradas no `ObjCRuntime.Messaging`.

## <a name="different-invocations-on-simulator-and-device"></a>Invocações diferentes no simulador e dispositivo

Conforme descrito acima, Objective-C tem três tipos de `objc_msgSend` métodos: um para invocações regulares, uma para invocações que retornam valores de ponto flutuante (somente x86) e outro para invocações que retornam valores de struct. O último inclui o sufixo `_stret` em `ObjCRuntime.Messaging`.

Se você estiver chamando um método que irá retornar determinadas estruturas (regras descritas abaixo), você deve chamar o método com o valor retornado como o primeiro parâmetro como um `out` valor:

```csharp
// The following returns a PointF structure:
PointF ret;
Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, this.Handle, selConvertPointFromWindow.Handle, point, window.Handle);
```

A regra para quando usar o `_stret_` método difere em x86 e ARM.
Se você quiser que suas ligações para trabalhar no simulador e no dispositivo, adicione o código como o seguinte:

```csharp
if (Runtime.Arch == Arch.DEVICE)
{
    PointF ret;
    Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, myHandle, selector.Handle);
    return ret;
} 
else
{
    return Messaging.PointF_objc_msgSend_PointF_IntPtr (myHandle, selector.Handle);
}
```

### <a name="using-the-objcmsgsendstret-method"></a>Usando o método objc_msgSend_stret

Ao compilar para ARM, use o [`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
para qualquer tipo de valor que não é uma enumeração ou qualquer um dos tipos de base para uma enumeração (`int`, `byte`, `short`, `long`, `double`, `float`).

Ao compilar para x86, use [`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
para qualquer tipo de valor que não é uma enumeração ou qualquer um dos tipos de base para uma enumeração (`int`, `byte`, `short`, `long`, `double`, `float`) e cujo tamanho nativo é maior que 8 bytes.

### <a name="creating-your-own-signatures"></a>Criando suas próprias assinaturas

O seguinte [gist](https://gist.github.com/rolfbjarne/981b778a99425a6e630c) pode ser usado para criar suas próprias assinaturas, se necessário.

## <a name="related-links"></a>Links relacionados

- [Seletores de Objective-C](https://developer.xamarin.com/samples/mac-ios/Objective-C/) exemplo
