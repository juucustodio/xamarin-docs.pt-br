---
title: Seletores de Objective-C em xamarin
description: Este documento aborda como interagir com os seletores de Objective-C do c#. Ele descreve como invocar seletores e considerações técnicas que devem ser levadas em conta ao fazer isso.
ms.prod: xamarin
ms.assetid: A80904C4-6A89-389B-0487-057AFEB70989
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 25276851879ba828361d3236cbf7896cf748588c
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787029"
---
# <a name="objective-c-selectors-in-xamarinios"></a>Seletores de Objective-C em xamarin

O idioma Objective-C é baseado no *seletores*. Um seletor é uma mensagem que pode ser enviada a um objeto ou um *classe*. [Xamarin](~/ios/internals/api-design/index.md) mapas de instância seletores para métodos de instância e classe seletores para métodos estáticos.

Diferente das funções de C normais (e, como funções de membro de C++), você não pode invocar diretamente usando um seletor [P/Invoke](http://www.mono-project.com/docs/advanced/pinvoke/).
(*Lado*: em teoria você pode usar P/Invoke para funções de membro de C++ não virtual, mas você precisa se preocupar sobre per compilador desconfiguração do nome, que é um mundo de problemáticos melhor ignorado.) Em vez disso, os seletores são enviados a uma classe Objective-C ou instância usando o [ `objc_msgSend` função](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend).

Você pode encontrar [este guia útil em Objective-C mensagens](http://developer.apple.com/iphone/library/documentation/cocoa/conceptual/ObjCRuntimeGuide/Articles/ocrtHowMessagingWorks.html) útil.

<a name="Example" />

## <a name="example"></a>Exemplo

Suponha que você deseja invocar o [-[NSString sizeWithFont:forWidth:lineBreakMode:]](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:forWidth:lineBreakMode:) seletor.
A declaração (da documentação da Apple) é:

```csharp
- (CGSize)sizeWithFont:(UIFont *)font forWidth:(CGFloat)width lineBreakMode:(UILineBreakMode)lineBreakMode
```

-  O tipo de retorno é *CGSize* para a API unificada.
-  O *fonte* parâmetro é um [UIFont](https://developer.xamarin.com/api/type/UIKit.UIFont/) (e um tipo derivado (indiretamente) de [NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/) ) e, portanto, é mapeada para [System. IntPtr](https://developer.xamarin.com/api/type/System.IntPtr/) .
-  O *largura* parâmetro, um *CGFloat* , é mapeada para *nfloat*.
-  O *lineBreakMode* parâmetro, um *UILineBreakMode* , já foi associado na xamarin como o [UILineBreakMode enumeração](https://developer.xamarin.com/api/type/UIKit.UILineBreakMode/) .


Reunir todas as e queremos uma declaração objc_msgSend correspondente:

```csharp
CGSize objc_msgSend(IntPtr target, IntPtr selector,
    IntPtr font, nfloat width, UILineBreakMode mode);
```

Precisamos declará-la:

```csharp
[DllImport (Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend")]
static extern CGSize cgsize_objc_msgSend_IntPtr_float_int (
    IntPtr target, IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode);
```

Depois que declarado, podemos pode invocá-lo assim que tivermos os parâmetros apropriados:

```csharp
NSString      target = ...
Selector    selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont          font = ...
nfloat          width = ...
UILineBreakMode mode = ...

CGSize size = cgsize_objc_msgSend_IntPtr_float_int(
    target.Handle, selector.Handle,
    font == null ? IntPtr.Zero : font.Handle,
    width,
    mode);
```

O valor retornado foi uma estrutura que foi menos de 8 bytes (como o antigo `SizeF` usado antes de alternar para as APIs unificado) o código acima executaria no simulador, mas falha no dispositivo. Portanto, para chamar um seletor que retorna um valor menor que 8 bits em tamanho, consequentemente, estamos *também* precisa declarar o `objc_msgSend_stret()` função:

```csharp
[DllImport (MonoTouch.Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend_stret")]
static extern void cgsize_objc_msgSend_stret_IntPtr_float_int (
    out CGSize retval,
    IntPtr target, IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode);
```

Invocação tornaria:

```csharp
NSString      target = ...
Selector    selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont          font = ...
nfloat          width = ...
UILineBreakMode mode = ...

CGSize size;

if (Runtime.Arch == Arch.SIMULATOR)
    size = cgsize_objc_msgSend_IntPtr_float_int(
        target.Handle, selector.Handle,
        font == null ? IntPtr.Zero : font.Handle,
        width,
        mode);
else
    cgsize_objc_msgSend_stret_IntPtr_float_int(
        out size,
        target.Handle, selector.Handle,
        font == null ? IntPtr.Zero: font.Handle,
        width,
        mode);
```


<a name="Invoking_a_Selector" />

## <a name="invoking-a-selector"></a>Invocando um seletor

Invocar um seletor tem três etapas:

1.  Obter o destino do seletor.
1.  Obter o nome de seletor.
1.  Chame objc_msgSend() com os argumentos apropriados.


<a name="Selector_Targets" />

### <a name="selector-targets"></a>Destinos do seletor

Um destino de seletor é uma instância do objeto ou uma classe Objective-C. Se o destino é uma instância e veio de um tipo de xamarin associado, use o [ObjCRuntime.INativeObject.Handle](https://developer.xamarin.com/api/property/ObjCRuntime.INativeObject.Handle/) propriedade.

Se o destino é uma classe, use [ObjCRuntime.Class](https://developer.xamarin.com/api/type/ObjCRuntime.Class/) para obter uma referência para a instância da classe, em seguida, use o [Class.Handle](https://developer.xamarin.com/api/property/ObjCRuntime.Class.Handle/) propriedade.


<a name="Selector_Names" />

### <a name="selector-names"></a>Nomes de seletor

Nomes de seletor são listados na documentação da Apple. Por exemplo, o [métodos de extensão UIKit NSString](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html) incluem [sizeWithFont:](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:) e [sizeWithFont:forWidth:lineBreakMode:](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:forWidth:lineBreakMode:). Os dois pontos inseridos e à direita são importantes e fazem parte do nome do seletor.

Quando você tiver um nome de seletor, você pode criar um [ObjCRuntime.Selector](https://developer.xamarin.com/api/type/ObjCRuntime.Selector/) instância para ele.


<a name="Calling_objc_msgSend()" />

### <a name="calling-objcmsgsend"></a>Chamar objc_msgSend()

 `objc_msgSend()` é usado para enviar uma mensagem (seletor) para um objeto. Essa família de funções tem pelo menos dois argumentos obrigatórios: o destino de seletor (uma instância ou o identificador de classe), o seletor de si mesmo e, em seguida, nenhum argumento necessário para o seletor de específico. Os argumentos de instância e o seletor devem ser `System.IntPtr`, e todos os argumentos restantes devem corresponder ao tipo de seletor de espera, por exemplo, um `nint` para um `int`, ou um `System.IntPtr` para todos os `NSObject`-tipos derivados. Use o [NSObject.Handle](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/) propriedade para obter um `IntPtr` para uma instância de tipo Objective-C.

Infelizmente, não há mais de um `objc_msgSend()` função.

Use [ `objc_msgSend_stret()` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret) para seletores que retornam uma estrutura.
Para manter as coisas "interessante", em ARM isso inclui o retorno de todos os tipos de *não* uma enumeração ou qualquer um dos tipos internos C (char, short, int, long, float, double). X86 (simulador), isso deve ser usado para todas as estruturas de maiores que 8 bytes de tamanho. (CGSize – usado no exemplo acima – é exatamente de 8 bytes e, portanto, não use `objc_msgSend_stret()` no simulador.)

Use [ `objc_msgSend_fpret()` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_fpret) para valor de ponto de seletores que retornam um flutuante x86 somente. Essa função não precisa ser usado em ARM; em vez disso, use `objc_msgSend()`.

O principal [objc_msgSend()](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend) função é usada para todos os outros seletores.

Depois de decidir qual `objc_msgSend()` funções você precisa chamar (e mais de um, por exemplo, para o simulador e dispositivo), você pode usar um normal [ `[DllImport]` ](https://developer.xamarin.com/api/type/System.Runtime.InteropServices.DllImportAttribute/) método para declarar a função de chamada posterior.

Um conjunto de predefinido `objc_msgSend()` declarações podem ser encontradas no [ `ObjCRuntime.Messaging` ](https://developer.xamarin.com/api/type/ObjCRuntime.Messaging/).


<a name="ugly" />

## <a name="the-ugly"></a>O feio

Objective-C tem três tipos de `objc_msgSend` métodos: uma para invocações regulares, uma para invocações que retornam valores de ponto flutuante (somente x86) e outra para invocações que retornam valores de struct. O último inclui o sufixo `_stret` em `ObjCRuntime.Messaging`.

Se você estiver chamando um método que retorna determinadas estruturas (regras estão descritas abaixo), você deve chamar o método com o valor de retorno como o primeiro parâmetro como um valor, como este:

```csharp
// The following returns a PointF structure:
PointF ret;
Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, this.Handle, selConvertPointFromWindow.Handle, point, window.Handle);
```

As coisas ficam feio aqui e porque a regra para quando você deve usar _stret_ é diferente em X86 e ARM, se você quiser que suas associações para trabalhar no simulador e o dispositivo, você precisa adicionar o código que tem esta aparência:

```csharp
if (Runtime.Arch == Arch.DEVICE){
    PointF ret;

    Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, myHandle, selector.Handle);

    return ret;
} else
    return Messaging.PointF_objc_msgSend_PointF_IntPtr (myHandle, selector.Handle);
```

### <a name="using-the-objcmsgsendstret-method"></a>Usando o objc\_msgSend\_stret método

A regra para quando usar o [ `objc_msgSend_stret` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret) são assim para **ARM**:

-  Qualquer valor de tipo que não é uma enumeração ou qualquer um dos tipos de base para uma enumeração (int, byte, short, long, double, float).


A regra para quando usar o [ `objc_msgSend_stret` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret) são assim para **X86**:

-  Qualquer valor de tipo que não é uma enumeração, ou qualquer um dos tipos de base para uma enumeração (int, byte, short, long, double, float) e cujo tamanho nativo é maior que 8 bytes.


### <a name="creating-your-own-signatures"></a>Criando suas próprias assinaturas.

O seguinte [essência](https://gist.github.com/rolfbjarne/981b778a99425a6e630c) pode ser usado para criar suas próprias assinaturas, se necessário.



## <a name="related-links"></a>Links relacionados

- [Exemplo de seletores](https://developer.xamarin.com/samples/mac-ios/Objective-C/Selectors/)
