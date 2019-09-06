---
title: Seletores de Objective-C no Xamarin. iOS
description: Este documento discute como interagir com os seletores Objective-C de C#. Ele descreve como invocar seletores e considerações técnicas que devem ser levadas em conta ao fazer isso.
ms.prod: xamarin
ms.assetid: A80904C4-6A89-389B-0487-057AFEB70989
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/12/2017
ms.openlocfilehash: 17b845345175d80237bcfdb171461f2c763c364e
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291848"
---
# <a name="objective-c-selectors-in-xamarinios"></a>Seletores de Objective-C no Xamarin. iOS

A linguagem Objective-C se baseia nos *seletores*. Um seletor é uma mensagem que pode ser enviada a um objeto ou uma *classe*. O [Xamarin. Ios](~/ios/internals/api-design/index.md) mapeia seletores de instância para métodos de instância e seletores de classe para métodos estáticos.

Ao contrário das funções C normais ( C++ e como funções de membro), você não pode invocar diretamente um seletor usando [P/Invoke](https://www.mono-project.com/docs/advanced/pinvoke/) em vez disso, os seletores são enviados para uma classe ou instância Objective-C usando o[`objc_msgSend`](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend)
funcionamento.

Para obter mais informações sobre as mensagens em Objective-C, confira o guia [trabalhando com objetos](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithObjects/WorkingwithObjects.html#//apple_ref/doc/uid/TP40011210-CH4-SW2) da Apple.

## <a name="example"></a>Exemplo

Suponha que você deseja invocar o[`sizeWithFont:forWidth:lineBreakMode:`](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont)
seletor [`NSString`](https://developer.apple.com/documentation/foundation/nsstring)ativado.
A declaração (da documentação da Apple) é:

```objc
- (CGSize)sizeWithFont:(UIFont *)font forWidth:(CGFloat)width lineBreakMode:(UILineBreakMode)lineBreakMode
```

Essa API tem as seguintes características:

- O tipo de retorno `CGSize` é para o API unificada.
- O `font` parâmetro é um [UIFont](xref:UIKit.UIFont) (e um tipo (indiretamente) derivado de [NSObject](xref:Foundation.NSObject)e é mapeado para [System. IntPtr](xref:System.IntPtr).
- O `width` parâmetro `nfloat`, a, é mapeado para. `CGFloat`
- O `lineBreakMode` parâmetro, a [`UILineBreakMode`](https://developer.apple.com/documentation/uikit/uilinebreakmode?language=objc), já foi associado ao Xamarin. Ios como o[`UILineBreakMode`](xref:UIKit.UILineBreakMode)
Enumeração.

Juntando tudo isso, a `objc_msgSend` declaração deve corresponder:

```csharp
CGSize objc_msgSend(
    IntPtr target, 
    IntPtr selector, 
    IntPtr font, 
    nfloat width, 
    UILineBreakMode mode
);
```

Declare-o da seguinte maneira:

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

Para chamar esse método, use um código como o seguinte:

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

Se o valor retornado fosse uma estrutura com menos de 8 bytes de tamanho (como a antiga `SizeF` usada antes de alternar para as APIs unificadas), o código acima teria sido executado no simulador, mas travou no dispositivo. Para chamar um seletor que retorna um valor menor que 8 bits, declare a `objc_msgSend_stret` função:

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

Para chamar esse método, use um código como o seguinte:

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

## <a name="invoking-a-selector"></a>Invocando um seletor

Invocar um seletor tem três etapas:

1. Obter o destino do seletor.
2. Obter o nome do seletor.
3. Chame `objc_msgSend` com os argumentos apropriados.

### <a name="selector-targets"></a>Destinos do seletor

Um destino de seletor é uma instância de objeto ou uma classe Objective-C. Se o destino for uma instância e vier de um tipo Xamarin. Ios associado, use a [`ObjCRuntime.INativeObject.Handle`](xref:ObjCRuntime.INativeObject.Handle) propriedade.

Se o destino for uma classe, use [`ObjCRuntime.Class`](xref:ObjCRuntime.Class) para obter uma referência à instância de classe e, em seguida [`Class.Handle`](xref:ObjCRuntime.Class.Handle) , use a propriedade.

### <a name="selector-names"></a>Nomes de seletor

Os nomes de seletor são listados na documentação da Apple. Por exemplo, [`NSString`](https://developer.apple.com/documentation/foundation/nsstring?language=objc) inclui [`sizeWithFont:`](https://developer.apple.com/documentation/foundation/nsstring/1619917-sizewithfont?language=objc) e [`sizeWithFont:forWidth:lineBreakMode:`](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont?language=objc) seletores. Os dois-pontos inseridos e finais fazem parte do nome do seletor e não podem ser omitidos.

Quando você tiver um nome de seletor, poderá criar [`ObjCRuntime.Selector`](xref:ObjCRuntime.Selector) uma instância para ele.

### <a name="calling-objc_msgsend"></a>Chamando objc_msgSend

`objc_msgSend`envia uma mensagem (seletor) para um objeto. Essa família de funções usa pelo menos dois argumentos obrigatórios: o destino do seletor (uma instância ou um identificador de classe), o próprio seletor e todos os argumentos necessários para o seletor. Os argumentos de instância e seletor `System.IntPtr`devem ser, e todos os argumentos restantes devem corresponder ao tipo que o seletor `nint` espera, por exemplo, `System.IntPtr` um para `NSObject`um `int`ou um para todos os tipos derivados. Use o[`NSObject.Handle`](xref:Foundation.NSObject.Handle)
Propriedade para obter um `IntPtr` para uma instância do tipo Objective-C.

Há mais de uma `objc_msgSend` função:

- Use [`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc) para seletores que retornam uma struct. No ARM, isso inclui todos os tipos de retorno que não são uma enumeração ou qualquer um dos tipos internos de C`char`( `short` `int`, `long` `float`,,, `double`,). No x86 (o simulador), esse método precisa ser usado para todas as estruturas com mais de 8 bytes de`CGSize` tamanho (é de 8 bytes `objc_msgSend_stret` e não é usado no simulador). 
- Use [`objc_msgSend_fpret`](https://developer.apple.com/documentation/objectivec/1456697-objc_msgsend_fpret?language=objc) para seletores que retornam um valor de ponto flutuante somente em x86. Esta função não precisa ser usada no ARM; em vez disso `objc_msgSend`, use. 
- A função principal [objc_msgSend](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend) é usada para todos os outros seletores.

Depois de decidir quais `objc_msgSend` funções você precisa chamar (o simulador e o dispositivo podem exigir um método diferente), você pode usar um método normal [`[DllImport]`](xref:System.Runtime.InteropServices.DllImportAttribute) para declarar a função para invocação posterior.

Um conjunto de `objc_msgSend` declarações predefinidas pode ser encontrado em `ObjCRuntime.Messaging`.

## <a name="different-invocations-on-simulator-and-device"></a>Invocações diferentes no simulador e no dispositivo

Conforme descrito acima, o Objective-C tem três `objc_msgSend` tipos de métodos: um para invocações regulares, um para invocações que retornam valores de ponto flutuante (somente x86) e outro para invocações que retornam valores de struct. O último inclui o sufixo `_stret` em `ObjCRuntime.Messaging`.

Se você estiver invocando um método que retornará determinadas estruturas (regras descritas abaixo), você deverá invocar o método com o valor de retorno como o primeiro parâmetro como `out` um valor:

```csharp
// The following returns a PointF structure:
PointF ret;
Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, this.Handle, selConvertPointFromWindow.Handle, point, window.Handle);
```

A regra para quando usar o `_stret_` método difere no x86 e no ARM.
Se você quiser que suas associações funcionem no simulador e no dispositivo, adicione um código como o seguinte:

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

### <a name="using-the-objc_msgsend_stret-method"></a>Usando o método objc_msgSend_stret

Ao compilar para ARM, use o[`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
para qualquer tipo de valor que não seja uma enumeração ou qualquer um dos tipos base para uma enumeração`int`( `byte`, `short`, `long` `double`,, `float`,).

Ao compilar para x86, use[`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
para qualquer tipo de valor que não seja uma enumeração ou qualquer um dos tipos base para uma enumeração`int`( `byte`, `short`, `long` `double`,, `float`,) e cujo tamanho nativo seja maior que 8 bytes.

### <a name="creating-your-own-signatures"></a>Criando suas próprias assinaturas

O seguinte [deve](https://gist.github.com/rolfbjarne/981b778a99425a6e630c) ser usado para criar suas próprias assinaturas, se necessário.

## <a name="related-links"></a>Links relacionados

- Exemplo [de seletores de Objective-C](https://developer.xamarin.com/samples/mac-ios/Objective-C/)
