---
title: Seletores de Objective-C no xamarin. IOS
description: Este documento discute como interagir com os seletores de Objective-C do c#. Ele descreve como invocar seletores e considerações técnicas que devem ser levadas em conta ao fazer isso.
ms.prod: xamarin
ms.assetid: A80904C4-6A89-389B-0487-057AFEB70989
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: d8708e518e967083bcfb95c88f8c20fddbb44b53
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998788"
---
# <a name="objective-c-selectors-in-xamarinios"></a>Seletores de Objective-C no xamarin. IOS

A linguagem do Objective-C é baseada no *seletores*. Um seletor é uma mensagem que pode ser enviada a um objeto ou uma *classe*. [Xamarin. IOS](~/ios/internals/api-design/index.md) mapas seletores para métodos de instância da instância e seletores para métodos estáticos de classes.

Ao contrário das funções de C normais (e, como funções de membro de C++), você não pode invocar diretamente usando um seletor [P/Invoke](http://www.mono-project.com/docs/advanced/pinvoke/).
(*Separar*: em teoria, você poderia usar P/Invoke para funções de membro de C++ não virtual, mas você precisa se preocupar sobre per compilador a desconfiguração de nome, que é um mundo de problemáticos melhor ignorado.) Em vez disso, os seletores são enviadas para uma classe de Objective-C ou instância usando o [ `objc_msgSend` função](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend).

Você pode achar [este guia útil em Objective-C mensagens](http://developer.apple.com/iphone/library/documentation/cocoa/conceptual/ObjCRuntimeGuide/Articles/ocrtHowMessagingWorks.html) úteis.

<a name="Example" />

## <a name="example"></a>Exemplo

Suponha que você deseja invocar o [-[NSString sizeWithFont:forWidth:lineBreakMode:]](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:forWidth:lineBreakMode:) seletor.
A declaração (da documentação da Apple) é:

```csharp
- (CGSize)sizeWithFont:(UIFont *)font forWidth:(CGFloat)width lineBreakMode:(UILineBreakMode)lineBreakMode
```

-  É o tipo de retorno *CGSize* para a API unificada.
-  O *font* parâmetro é um [UIFont](https://developer.xamarin.com/api/type/UIKit.UIFont/) (e um tipo derivado (indiretamente) [NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/) ) e, portanto, é mapeada para [System. IntPtr](xref:System.IntPtr) .
-  O *largura* parâmetro, um *CGFloat* , é mapeado para *nfloat*.
-  O *lineBreakMode* parâmetro, um *UILineBreakMode* , já foi associado no xamarin. IOS como o [UILineBreakMode enumeração](https://developer.xamarin.com/api/type/UIKit.UILineBreakMode/) .


Juntar tudo e queremos uma declaração objc_msgSend que corresponde ao:

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

Após a declaração, podemos pode invocá-lo assim que tivermos os parâmetros apropriados:

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

O valor retornado foi uma estrutura que foi menor que 8 bytes de tamanho (como o mais antigo `SizeF` usado antes de alternar para as APIs unificadas) o código acima teria executado no simulador, mas com falha no dispositivo. Portanto, para chamar um seletor que retorna um valor menor que 8 bits de tamanho, consequentemente, estamos *também* precisa declarar o `objc_msgSend_stret()` função:

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

## <a name="invoking-a-selector"></a>Invocar um seletor

Invocar um seletor tem três etapas:

1.  Obtenha o destino do seletor.
1.  Obtenha o nome do seletor.
1.  Chame objc_msgSend() com os argumentos apropriados.


<a name="Selector_Targets" />

### <a name="selector-targets"></a>Destinos do seletor

Um destino do seletor é uma instância do objeto ou uma classe de Objective-C. Se o destino é uma instância e veio de um tipo associado do xamarin. IOS, use o [ObjCRuntime.INativeObject.Handle](https://developer.xamarin.com/api/property/ObjCRuntime.INativeObject.Handle/) propriedade.

Se o destino é uma classe, use [ObjCRuntime.Class](https://developer.xamarin.com/api/type/ObjCRuntime.Class/) para obter uma referência à instância de classe, em seguida, use o [Class.Handle](https://developer.xamarin.com/api/property/ObjCRuntime.Class.Handle/) propriedade.


<a name="Selector_Names" />

### <a name="selector-names"></a>Nomes do seletor

Nomes de seletor são listados na documentação da Apple. Por exemplo, o [métodos de extensão de UIKit NSString](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html) incluem [sizeWithFont:](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:) e [sizeWithFont:forWidth:lineBreakMode:](http://developer.apple.com/iphone/library/documentation/UIKit/Reference/NSString_UIKit_Additions/Reference/Reference.html#//apple_ref/occ/instm/NSString/sizeWithFont:forWidth:lineBreakMode:). Os dois-pontos à direita e inseridos são importantes e fazem parte do nome do seletor.

Quando você tiver um nome de seletor, você pode criar uma [ObjCRuntime.Selector](https://developer.xamarin.com/api/type/ObjCRuntime.Selector/) instância para ele.


<a name="Calling_objc_msgSend()" />

### <a name="calling-objcmsgsend"></a>Chamar objc_msgSend()

 `objc_msgSend()` é usado para enviar uma mensagem (seletor) para um objeto. Essa família de funções leva pelo menos dois argumentos necessários: o destino do seletor (uma instância ou classe manipular), o seletor de si mesmo e, em seguida, nenhum argumento necessário para o seletor de determinado. Os argumentos de instância e o seletor devem ser `System.IntPtr`, e todos os argumentos restantes devem corresponder ao tipo que o seletor de espera, por exemplo, um `nint` para um `int`, ou uma `System.IntPtr` para todos os `NSObject`-tipos derivados. Use o [NSObject.Handle](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/) propriedade para obter um `IntPtr` para uma instância do tipo Objective-C.

Infelizmente, não há mais de um `objc_msgSend()` função.

Use [ `objc_msgSend_stret()` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret) para seletores que retornam uma estrutura.
Para manter as coisas "interessante", no ARM isso inclui todos retornam tipos que são *não* uma enumeração ou qualquer um dos tipos internos C (char, short, int, long, float, double). Em x86 (o simulador), isso precisa ser usado para todas as estruturas maiores que 8 bytes de tamanho. (CGSize – usado no exemplo acima, é exatamente de 8 bytes e, portanto, não use `objc_msgSend_stret()` no simulador.)

Use [ `objc_msgSend_fpret()` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_fpret) para valor de ponto de seletores que retornam flutuante em x86 somente. Essa função não precisa ser usado no ARM; em vez disso, use `objc_msgSend()`.

A principal [objc_msgSend()](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend) função é usada para todos os outros seletores.

Depois de decidir qual `objc_msgSend()` funções você precisa chamar (e pode ser mais de uma, por exemplo, para o dispositivo e simulador), você pode usar um normal [ `[DllImport]` ](xref:System.Runtime.InteropServices.DllImportAttribute) método declarar a função para invocação posterior.

Um conjunto de pré-criado `objc_msgSend()` declarações podem ser encontradas no [ `ObjCRuntime.Messaging` ](https://developer.xamarin.com/api/type/ObjCRuntime.Messaging/).


<a name="ugly" />

## <a name="the-ugly"></a>O feio

Objective-C tem três tipos de `objc_msgSend` métodos: um para invocações regulares, uma para invocações que retornam valores de ponto flutuante (somente x86) e outro para invocações que retornam valores de struct. O último inclui o sufixo `_stret` em `ObjCRuntime.Messaging`.

Se você estiver chamando um método que irá retornar determinadas estruturas (as regras são descritas abaixo), você deve chamar o método com o valor retornado como o primeiro parâmetro como um valor de saída, como este:

```csharp
// The following returns a PointF structure:
PointF ret;
Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, this.Handle, selConvertPointFromWindow.Handle, point, window.Handle);
```

As coisas ficam feia aqui e, como a regra para quando você deve usar _stret_ é diferente em X86 e ARM, se você quiser que suas ligações trabalhe no simulador e o dispositivo, você precisa adicionar o código que tem esta aparência:

```csharp
if (Runtime.Arch == Arch.DEVICE){
    PointF ret;

    Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, myHandle, selector.Handle);

    return ret;
} else
    return Messaging.PointF_objc_msgSend_PointF_IntPtr (myHandle, selector.Handle);
```

### <a name="using-the-objcmsgsendstret-method"></a>Usando o objc\_msgSend\_stret método

A regra para quando usar o [ `objc_msgSend_stret` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret) são como essa para **ARM**:

-  Qualquer valor de tipo que não é uma enumeração ou qualquer um dos tipos de base para uma enumeração (int, byte, short, long, double, float).


A regra para quando usar o [ `objc_msgSend_stret` ](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/objc_msgSend_stret) são como essa para **X86**:

-  Qualquer valor de tipo que não é uma enumeração ou qualquer um dos tipos de base para uma enumeração (int, byte, short, long, double, float) e cujo tamanho nativo é maior que 8 bytes.


### <a name="creating-your-own-signatures"></a>Criando suas próprias assinaturas.

O seguinte [gist](https://gist.github.com/rolfbjarne/981b778a99425a6e630c) pode ser usado para criar suas próprias assinaturas, se necessário.



## <a name="related-links"></a>Links relacionados

- [Exemplo de seletores](https://developer.xamarin.com/samples/mac-ios/Objective-C/Selectors/)
