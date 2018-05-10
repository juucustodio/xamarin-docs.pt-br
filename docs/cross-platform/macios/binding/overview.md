---
title: Visão geral
description: Detalhes de como funciona o processo de ligação
ms.prod: xamarin
ms.assetid: 9EE288C5-8952-C5A9-E542-0BD847300EC6
author: asb3993
ms.author: amburns
ms.openlocfilehash: ce2e45a1985c64b5c479bd41140d7270fae97aee
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2018
---
# <a name="overview"></a>Visão geral

_Detalhes de como funciona o processo de ligação_

Associação de uma biblioteca Objective-C para uso com o Xamarin usa três etapas:

1. Gravar um c# "Definição de API" para descrever como a API nativa é exposta no .NET e como ele mapeia para o objetivo subjacente-C. Isso é feito usando o padrão c# constrói como `interface` e associação de vários **atributos** (consulte este [exemplo simples](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_an_API)).

2. Uma vez você gravou "definição de API" em c#, você compilá-lo para produzir um assembly de "binding". Isso pode ser feito o [ **linha de comando** ](#commandline) ou usando um [ **projeto de vinculação** ](#bindingproject) no Visual Studio para Mac ou o Visual Studio.

3. Assembly "associação" é adicionada ao seu projeto de aplicativo Xamarin, para que possa acessar a funcionalidade nativa usando a API definida por você.
  O projeto de vinculação é separado dos seus projetos de aplicativo.

**Observação:** etapa 1 pode ser automatizada com o auxílio do [ **objetivo Sharpie**](#objectivesharpie). Ele examina a API Objective-C e gera uma proposta c# "Definição de API". Você pode personalizar os arquivos criados pelo Sharpie objetivo e usá-los em um projeto de vinculação (ou na linha de comando) para criar o assembly de associação. Objetivo Sharpie não cria associações por si só, é simplesmente uma parte opcional do processo maior.

Você também pode ler mais detalhes técnicos de [como ele funciona](#howitworks), que ajudará você a escrever suas associações.

<a name="Command_Line_Bindings" /><a name="commandline" />

## <a name="command-line-bindings"></a>Associações de linha de comando

Você pode usar o `btouch-native` para xamarin (ou `bmac-native` se você estiver usando Xamarin.Mac) para criar associações diretamente. Ele funciona, passando as definições de API do c# que você criou manualmente (ou usando o objetivo Sharpie) para a ferramenta de linha de comando (`btouch-native` para iOS ou `bmac-native` para Mac).


A sintaxe geral para chamar essas ferramentas é:

```csharp
# Use this for Xamarin.iOS:
bash$ /Developer/MonoTouch/usr/bin/btouch-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

```csharp
# Use this for Xamarin.Mac:
bash$ bmac-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

O comando acima irá gerar o arquivo `cocos2d.dll` no diretório atual, e ele conterá a biblioteca totalmente associada que você pode usar em seu projeto. Esta é a ferramenta que o Visual Studio para Mac usa para criar suas associações se você usar um projeto de vinculação (descrito [abaixo](#bindingproject)).


<a name="bindingproject" />

## <a name="binding-project"></a>Projeto de vinculação

Um projeto de associação pode ser criado no Visual Studio para Mac ou o Visual Studio (Visual Studio só dá suporte a associações de iOS) e torna mais fácil editar e criar definições de API para associação (em vez de usar a linha de comando).

Siga este [guia de Introdução](~/cross-platform/macios/binding/objective-c-libraries.md#Getting_Started) para saber como criar e usar um projeto de vinculação para produzir uma associação.

<a name="objectivesharpie" />

## <a name="objective-sharpie"></a>Objetivo Sharpie

Objetivo Sharpie é ferramenta de linha de comando de outra, separado que ajuda com os estágios iniciais de criação de uma associação. Ele não cria uma associação por si só, em vez disso, ele automatiza a etapa inicial de geração de uma definição de API para a biblioteca nativa de destino.

Leitura de [documentos Sharpie objetivo](~/cross-platform/macios/binding/objective-sharpie/index.md) para saber como analisar CocoaPods, nativo estruturas e bibliotecas nativas em definições de API que podem ser construídas em associações.

<a name="howitworks" />

## <a name="how-binding-works"></a>Como funciona a associação

É possível usar o [[registrar]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) atributo, [[exportar]](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/) atributo, e [manual invocação de seletor Objective-C](~/ios/internals/objective-c-selectors.md) juntos para associar manualmente new (anteriormente tipos de Objective-C não vinculados).

Primeiro, encontre um tipo que você deseja vincular. Para discussão fins (e simplicidade) é associará o [NSEnumerator](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSEnumerator_Class/Reference/Reference.html) tipo (que já foi associado na [Foundation.NSEnumerator](https://developer.xamarin.com/api/type/Foundation.NSEnumerator/); a implementação abaixo é apenas por exemplo fins).

Em segundo lugar, é preciso criar o tipo c#. É provável que queira colocar isso em um namespace; como Objective-C não dá suporte a namespaces, será necessário usar o `[Register]` atributo para alterar o nome de tipo xamarin será registrado com o tempo de execução Objective-C. O tipo c# também deve herdar de [Foundation.NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/):

```csharp
namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        // see steps 3-5
    }
}
```

Em terceiro lugar, examine a documentação Objective-C e criar [ObjCRuntime.Selector](https://developer.xamarin.com/api/type/ObjCRuntime.Selector/) instâncias de cada seletor que você deseja usar. Colocá-los dentro do corpo de classe:

```csharp
static Selector selInit       = new Selector("init");
static Selector selAllObjects = new Selector("allObjects");
static Selector selNextObject = new Selector("nextObject");
```

Quarto, seu tipo será necessário fornecer construtores. Você *deve* sua chamada de construtor para o construtor de classe base da cadeia. O `[Export]` atributos permitem que o código Objective-C para chamar os construtores com o nome de seletor especificado:

```csharp
[Export("init")]
public NSEnumerator()
    : base(NSObjectFlag.Empty)
{
    Handle = Messaging.IntPtr_objc_msgSend(this.Handle, selInit.Handle);
}
```

```csharp
// This constructor must be present so that Xamarin.iOS
// can create instances of your type from Objective-C code.
public NSEnumerator(IntPtr handle)
    : base(handle)
{
}
```

Quinto, fornecem métodos para cada um dos seletores de declarado na etapa 3. Eles usarão `objc_msgSend()` para invocar o seletor do objeto nativo. Observe o uso de [Runtime.GetNSObject()](https://developer.xamarin.com/api/member/ObjCRuntime.Runtime.GetNSObject/(System.IntPtr)) para converter um `IntPtr` em adequadamente com um tipo `NSObject` (sub) tipo. Se você quiser que o método a ser chamado de código Objective-C, o membro *deve* ser **virtual**.

```csharp
[Export("nextObject")]
public virtual NSObject NextObject()
{
    return Runtime.GetNSObject(
        Messaging.IntPtr_objc_msgSend(this.Handle, selNextObject.Handle));
}
```

```csharp
// Note that for properties, [Export] goes on the get/set method:
public virtual NSArray AllObjects {
    [Export("allObjects")]
    get {
        return (NSArray) Runtime.GetNSObject(
            Messaging.IntPtr_objc_msgSend(this.Handle, selAllObjects.Handle));
    }
}
```

Juntando tudo:

```csharp
using System;
using Foundation;
using ObjCRuntime;

namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        static Selector selInit       = new Selector("init");
        static Selector selAllObjects = new Selector("allObjects");
        static Selector selNextObject = new Selector("nextObject");

        [Export("init")]
        public NSEnumerator()
            : base(NSObjectFlag.Empty)
        {
            Handle = Messaging.IntPtr_objc_msgSend(this.Handle,
                selInit.Handle);
        }

        public NSEnumerator(IntPtr handle)
            : base(handle)
        {
        }

        [Export("nextObject")]
        public virtual NSObject NextObject()
        {
            return Runtime.GetNSObject(
                Messaging.IntPtr_objc_msgSend(this.Handle,
                    selNextObject.Handle));
        }

        // Note that for properties, [Export] goes on the get/set method:
        public virtual NSArray AllObjects {
            [Export("allObjects")]
            get {
                return (NSArray) Runtime.GetNSObject(
                    Messaging.IntPtr_objc_msgSend(this.Handle,
                        selAllObjects.Handle));
            }
        }
    }
}
```

