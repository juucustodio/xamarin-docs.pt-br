---
title: Exemplo do mundo Real (manual) avançado
description: Este documento descreve como usar a saída do xcodebuild como entrada para o objetivo Sharpie, que fornece informações sobre o que o objetivo Sharpie faz nos bastidores.
ms.prod: xamarin
ms.assetid: 044FF669-0B81-4186-97A5-148C8B56EE9C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: e820a0c208907a95dda4a50427bb4dac27b88964
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64977899"
---
# <a name="advanced-manual-real-world-example"></a>Exemplo do mundo Real (manual) avançado

**Este exemplo usa o [biblioteca de POP do Facebook](https://github.com/facebook/pop).**

Esta seção aborda uma abordagem mais avançada para associação, onde usaremos da Apple `xcodebuild` ferramenta para primeiro Compile o projeto POP e deduzem a entrada para o objetivo Sharpie manualmente. Essencialmente, isso abrange o que Sharpie objetivo está fazendo nos bastidores na seção anterior.

```
 $ git clone https://github.com/facebook/pop.git
Cloning into 'pop'...
   _(more git clone output)_

$ cd pop
```

Como a biblioteca POP tem um projeto do Xcode (`pop.xcodeproj`), podemos simplesmente usar `xcodebuild` para criar um POP. Esse processo por sua vez pode gerar arquivos de cabeçalho que talvez seja necessário analisar Sharpie objetivo. É por isso criando antes de associação é importante. Quando a criação via `xcodebuild` Verifique se você passar o mesmo identificador do SDK e arquitetura que você pretende passar para o objetivo Sharpie (e lembre-se de que objetivo Sharpie 3.0 geralmente pode fazer isso para você!):

```
$ xcodebuild -sdk iphoneos9.0 -arch arm64

Build settings from command line:
    ARCHS = arm64
    SDKROOT = iphoneos8.1
 
=== BUILD TARGET pop OF PROJECT pop WITH THE DEFAULT CONFIGURATION (Release) ===
 
...
 
CpHeader pop/POPAnimationTracer.h build/Headers/POP/POPAnimationTracer.h
    cd /Users/aaron/src/sharpie/pop
    export PATH="/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/usr/bin:/Applications/Xcode.app/Contents/Developer/usr/bin:/Users/aaron/bin::/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin:/usr/local/git/bin:/Users/aaron/.rvm/bin"
    builtin-copy -exclude .DS_Store -exclude CVS -exclude .svn -exclude .git -exclude .hg -strip-debug-symbols -strip-tool /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/strip -resolve-src-symlinks /Users/aaron/src/sharpie/pop/pop/POPAnimationTracer.h /Users/aaron/src/sharpie/pop/build/Headers/POP
 
...
 
** BUILD SUCCEEDED **
```

Haverá muita saída de informações de compilação no console do como parte do `xcodebuild`. Como exibido acima, podemos ver que um destino de "CpHeader" foi executado no qual os arquivos de cabeçalho foram copiados para um diretório de saída de compilação. Isso é geralmente o caso e facilita a vinculação: como parte da compilação da biblioteca nativa, os arquivos de cabeçalho geralmente são copiados para um local consumível "publicamente" que pode tornar mais fácil de análise de associação. Nesse caso, nós sabemos que os arquivos de cabeçalho do POP estão no `build/Headers` directory.

Agora estamos prontos para associar o POP. Sabemos que queremos criar para o SDK `iphoneos8.1` com o `arm64` arquitetura, e que os arquivos de cabeçalho que nos interessa estejam na `build/Headers` sob o check-out de git POP. Se verificarmos o `build/Headers` diretório, vamos ver um número de arquivos de cabeçalho:

```
$ ls build/Headers/POP/
POP.h                    POPAnimationTracer.h     POPDefines.h
POPAnimatableProperty.h  POPAnimator.h            POPGeometry.h
POPAnimation.h           POPAnimatorPrivate.h     POPLayerExtras.h
POPAnimationEvent.h      POPBasicAnimation.h      POPPropertyAnimation.h
POPAnimationExtras.h     POPCustomAnimation.h     POPSpringAnimation.h
POPAnimationPrivate.h    POPDecayAnimation.h
```

Se observarmos `POP.h`, podemos ver é o arquivo de cabeçalho de nível superior da biblioteca principal `#import`s outros arquivos. Por isso, só precisamos passar `POP.h` para Sharpie objetivo, e clang fará o resto em segundo plano:

```
$ sharpie bind -output Binding -sdk iphoneos8.1 \
    -scope build/Headers build/Headers/POP/POP.h \
    -c -Ibuild/Headers -arch arm64

Parsing Native Code...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Binding Analysis:
  Automated binding is complete, but there are a few APIs which have
  been flagged with [Verify] attributes. While the entire binding
  should be audited for best API design practices, look more closely
  at APIs with the following Verify attribute hints:

  ConstantsInterfaceAssociation (1 instance):
    There's no fool-proof way to determine with which Objective-C
    interface an extern variable declaration may be associated.
    Instances of these are bound as [Field] properties in a partial
    interface into a near-by concrete interface to produce a more
    intuitive API, possibly eliminating the 'Constants' interface
    altogether.

  StronglyTypedNSArray (4 instances):
    A native NSArray* was bound as NSObject[]. It might be possible
    to more strongly type the array in the binding based on
    expectations set through API documentation (e.g. comments in the
    header file) or by examining the array contents through testing.
    For example, an NSArray* containing only NSNumber* instances can
    be bound as NSNumber[] instead of NSObject[].

  MethodToProperty (2 instances):
    An Objective-C method was bound as a C# property due to
    convention such as taking no parameters and returning a value (
    non-void return). Often methods like these should be bound as
    properties to surface a nicer API, but sometimes false-positives
    can occur and the binding should actually be a method.

  Once you have verified a Verify attribute, you should remove it
  from the binding source code. The presence of Verify attributes
  intentionally cause build failures.

  For more information about the Verify attribute hints above,
  consult the Objective Sharpie documentation by running 'sharpie
  docs' or visiting the following URL:

    http://xmn.io/sharpie-docs

Submitting usage data to Xamarin...
  Submitted - thank you for helping to improve Objective Sharpie!

Done.
```

Você observará que passamos um `-scope build/Headers` argumento Sharpie objetivo. Porque as bibliotecas de Objective-C e C devem `#import` ou `#include` outros arquivos de cabeçalho que são os detalhes de implementação da biblioteca e não API que você deseja associar, o `-scope` argumento informa ao objetivo Sharpie para ignorar qualquer API que não está definido em um arquivo em algum lugar dentro do `-scope` directory.

Você encontrará o `-scope` argumento geralmente é opcional para as bibliotecas de implementada corretamente, embora não haja nenhum problema em explicitamente fornecendo a ele.

Além disso, especificamos `-c -Ibuild/headers`. Em primeiro lugar, o `-c` argumento informa ao objetivo Sharpie para parar a interpretar os argumentos de linha de comando e passar argumentos subsequentes _diretamente para o compilador clang_. Portanto, `-Ibuild/Headers` é um argumento de compilador clang que instrui o clang para procurar inclui sob `build/Headers`, que é onde os cabeçalhos do POP ao vivo. Sem esse argumento, clang não saberia onde localizar os arquivos que `POP.h` é `#import`ing. _Quase todos os "problemas" com o uso de objetivo Sharpie se resumem a imaginar o que passar para clang_.

### <a name="completing-the-binding"></a>Concluindo a associação

Objetivo Sharpie gerou `Binding/ApiDefinitions.cs` e `Binding/StructsAndEnums.cs` arquivos.

Esses são básica no primeiro passo do objetivo Sharpie da associação e, em alguns casos pode ser tudo o que você precisa. Como mencionado acima, no entanto, o desenvolvedor geralmente será necessário modificar manualmente os arquivos gerados após a conclusão do objetivo Sharpie para [corrigir quaisquer problemas](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) que não pôde ser automaticamente tratada pela ferramenta.

Depois que as atualizações forem concluídas, esses dois arquivos agora podem ser adicionados a um projeto de associação no Visual Studio para Mac ou ser passados diretamente para o `btouch` ou `bmac` ferramentas para produzir a ligação final.

Para obter uma descrição completa do processo de associação, consulte nosso [instruções passo a passo completo](~/ios/platform/binding-objective-c/walkthrough.md).
