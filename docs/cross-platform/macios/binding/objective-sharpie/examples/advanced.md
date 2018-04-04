---
title: Avançado (manual) exemplo do mundo Real
ms.prod: xamarin
ms.assetid: 044FF669-0B81-4186-97A5-148C8B56EE9C
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 96a8f77124fcd2a011e499e1088650ff7664fa71
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="advanced-manual-real-world-example"></a>Avançado (manual) exemplo do mundo Real


**Este exemplo usa o [biblioteca POP do Facebook](https://github.com/facebook/pop).**


Esta seção aborda uma abordagem mais avançada para associação, onde usaremos da Apple `xcodebuild` ferramenta para criar primeiro um projeto POP e, em seguida, deduzir manualmente a entrada para o objetivo Sharpie. Essencialmente, isso abrange o objetivo Sharpie fazendo nos bastidores na seção anterior.

```csharp
 $ git clone https://github.com/facebook/pop.git
Cloning into 'pop'...
   _(more git clone output)_

$ cd pop
```

Como a biblioteca POP tem um projeto Xcode (`pop.xcodeproj`), podemos usar apenas `xcodebuild` criem POP. Esse processo por sua vez pode gerar arquivos de cabeçalho que talvez seja necessário analisar Sharpie objetivo. É por isso criando antes de associação é importante. Ao criar `xcodebuild` Certifique-se de que você passa o mesmo identificador SDK e arquitetura que você pretende passar para o objetivo Sharpie (e lembre-se de que objetivo Sharpie 3.0 normalmente pode fazer isso para você!):

```csharp
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

Haverá muita saída de informações de compilação no console do como parte do `xcodebuild`. Como a exibida acima, podemos ver que um destino de "CpHeader" foi executado no qual os arquivos de cabeçalho foram copiados para um diretório de saída de compilação. Isso é geralmente o caso e facilita a associação: como parte da compilação da biblioteca nativa, arquivos de cabeçalho geralmente são copiados para um local consumível "público" que pode tornar mais fácil de análise de associação. Nesse caso, sabemos que os arquivos de cabeçalho do POP estão no `build/Headers` directory.

Agora você está pronto para associar POP. Sabemos que queremos desenvolver para o SDK `iphoneos8.1` com o `arm64` arquitetura e que os arquivos de cabeçalho nos preocupamos estão em `build/Headers` sob o check-out de git POP. Se podemos examinar o `build/Headers` diretório, você verá um número de arquivos de cabeçalho:

```csharp
$ ls build/Headers/POP/
POP.h                    POPAnimationTracer.h     POPDefines.h
POPAnimatableProperty.h  POPAnimator.h            POPGeometry.h
POPAnimation.h           POPAnimatorPrivate.h     POPLayerExtras.h
POPAnimationEvent.h      POPBasicAnimation.h      POPPropertyAnimation.h
POPAnimationExtras.h     POPCustomAnimation.h     POPSpringAnimation.h
POPAnimationPrivate.h    POPDecayAnimation.h
```

Se observarmos `POP.h`, podemos ver o arquivo de cabeçalho principal de nível superior da biblioteca é `#import`s outros arquivos. Por isso, basta passar `POP.h` para Sharpie objetivo, e clang fará o restante em segundo plano:

```csharp
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

Você observará que passamos um `-scope build/Headers` argumento Sharpie objetivo. Porque as bibliotecas Objective-C e C devem `#import` ou `#include` outros arquivos de cabeçalho são detalhes de implementação da biblioteca e não a API que deseja associar, o `-scope` argumento informa Sharpie objetivo para ignorar qualquer API que não está definido em um arquivo em algum lugar dentro do `-scope` directory.

Você encontrará o `-scope` argumento geralmente é opcional para bibliotecas implementadas corretamente, embora não haja nenhum problema em fornecê-los explicitamente.

Além disso, especificamos `-c -Ibuild/headers`. Em primeiro lugar, o `-c` argumento informa Sharpie objetivo para parar a interpretar os argumentos de linha de comando e passar argumentos subsequentes _diretamente para o compilador clang_. Portanto, `-Ibuild/Headers` inclui um argumento de compilador clang que instrui o clang para pesquisar em `build/Headers`, que é onde os cabeçalhos POP ao vivo. Sem esse argumento clang não saberia onde localizar os arquivos que `POP.h` é `#import`ndo. _Quase todos os "problemas" com o objetivo de Sharpie resumem para descobrir o que passar para clang_.

### <a name="completing-the-binding"></a>Concluindo a associação

Objetivo Sharpie gerou `Binding/ApiDefinitions.cs` e `Binding/StructsAndEnums.cs` arquivos.

Esses são básica primeira passagem do objetivo Sharpie na ligação, e em alguns casos pode ser tudo o que você precisa. Como mencionado acima, no entanto, o desenvolvedor geralmente será necessário modificar manualmente os arquivos gerados após a conclusão da objetivo Sharpie para [corrigir quaisquer problemas](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) que não pôde ser automaticamente tratada pela ferramenta.

Depois que as atualizações estiverem concluídas, esses dois arquivos agora podem ser adicionados a um projeto de vinculação no Visual Studio para Mac ou ser passados diretamente para o `btouch` ou `bmac` ferramentas para produzir a associação final.

Para obter uma descrição detalhada do processo de associação, consulte nosso [instruções passo a passo completo](~/ios/platform/binding-objective-c/walkthrough.md).

