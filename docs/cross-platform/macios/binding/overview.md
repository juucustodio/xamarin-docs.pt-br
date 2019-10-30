---
title: Visão geral das associações de Objective-C
description: Este documento fornece uma visão geral de diferentes maneiras de C# criar associações para código Objective-C, incluindo associações de linha de comando, projetos de associação e nitidez objetiva. Ele também discute como a associação funciona.
ms.prod: xamarin
ms.assetid: 9EE288C5-8952-C5A9-E542-0BD847300EC6
author: davidortinau
ms.author: daortin
ms.date: 11/25/2015
ms.openlocfilehash: cad352466e7661183c5277f60c63c283342c50fb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015879"
---
# <a name="overview-of-objective-c-bindings"></a>Visão geral das associações de Objective-C

_Detalhes de como o processo de ligação funciona_

A associação de uma biblioteca Objective-C para uso com o Xamarin executa três etapas:

1. Escreva uma C# "definição de API" para descrever como a API nativa é exposta no .net e como ela é mapeada para o Objective-C subjacente. Isso é feito usando construções C# padrão como `interface`e vários **atributos** de associação (consulte este [exemplo simples](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_an_API)).

2. Depois de gravar a "definição de API" no C#, você a compila para produzir um assembly de "Associação". Isso pode ser feito na [**linha de comando**](#commandline) ou usando um [**projeto de associação**](#bindingproject) no Visual Studio para Mac ou no Visual Studio.

3. Esse assembly "Binding" é então adicionado ao seu projeto de aplicativo do Xamarin, para que você possa acessar a funcionalidade nativa usando a API que você definiu.
   O projeto de associação é completamente separado dos projetos de aplicativo.

   > [!NOTE]
   > A etapa 1 pode ser automatizada com a ajuda da [**nitidez objetiva**](#objectivesharpie). Ele examina a API Objective-C e gera uma " C# definição de API" proposta. Você pode personalizar os arquivos criados pela nitidez objetiva e usá-los em um projeto de associação (ou na linha de comando) para criar seu assembly de associação. A nitidez do objetivo não cria associações por si só, é meramente uma parte opcional do processo maior.

Você também pode ler mais detalhes técnicos de [como ele funciona](#howitworks), o que o ajudará a escrever suas associações.

<a name="Command_Line_Bindings" /><a name="commandline" />

## <a name="command-line-bindings"></a>Associações de linha de comando

Você pode usar o `btouch-native` para Xamarin. iOS (ou `bmac-native` se você estiver usando o Xamarin. Mac) para criar associações diretamente. Ele funciona passando as definições C# de API que você criou manualmente (ou usando nitidez objetiva) para a ferramenta de linha de comando (`btouch-native`para iOS ou`bmac-native`para Mac).

A sintaxe geral para invocar essas ferramentas é:

```csharp
# Use this for Xamarin.iOS:
bash$ /Developer/MonoTouch/usr/bin/btouch-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

```csharp
# Use this for Xamarin.Mac:
bash$ bmac-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

O comando acima irá gerar o arquivo `cocos2d.dll` no diretório atual e ele conterá a biblioteca totalmente associada que você pode usar em seu projeto. Essa é a ferramenta que o Visual Studio para Mac usa para criar suas associações se você usar um projeto de associação (descrito [abaixo](#bindingproject)).

<a name="bindingproject" />

## <a name="binding-project"></a>Projeto de associação

Um projeto de associação pode ser criado no Visual Studio para Mac ou no Visual Studio (o Visual Studio dá suporte apenas a associações do iOS) e facilita a edição e a criação de definições de API para associação (versus usar a linha de comando).

Siga este [Guia de introdução](~/cross-platform/macios/binding/objective-c-libraries.md#Getting_Started) para ver como criar e usar um projeto de associação para produzir uma associação.

<a name="objectivesharpie" />

## <a name="objective-sharpie"></a>Objective Sharpie

A Sharpde objetiva é outra ferramenta de linha de comando separada que ajuda com os estágios iniciais de criação de uma associação. Ele não cria uma associação por si só, em vez disso, Ele automatiza a etapa inicial de gerar uma definição de API para a biblioteca nativa de destino.

Leia os [documentos de nitidez do objetivo](~/cross-platform/macios/binding/objective-sharpie/index.md) para saber como analisar bibliotecas nativas, estruturas nativas e COCOAPODS na API definições que podem ser incorporadas a associações.

<a name="howitworks" />

## <a name="how-binding-works"></a>Como funciona a associação

É possível usar o atributo [[Register]](xref:Foundation.RegisterAttribute) , o atributo [[Export]](xref:Foundation.ExportAttribute) e a [invocação de seletor de Objective-c manual](~/ios/internals/objective-c-selectors.md) para associar manualmente os novos tipos Objective-c (anteriormente desassociados).

Primeiro, localize um tipo que você deseja associar. Para fins de discussão (e simplicidade), Vincularemos o tipo [NSEnumerator](https://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSEnumerator_Class/Reference/Reference.html) (que já foi associado em [Foundation. NSEnumerator](xref:Foundation.NSEnumerator); a implementação abaixo é apenas para fins de exemplo).

Em segundo lugar, precisamos criar o C# tipo. Provavelmente desejaremos colocá-lo em um namespace; como o Objective-C não dá suporte a namespaces, precisaremos usar o atributo `[Register]` para alterar o nome do tipo que o Xamarin. iOS registrará com o tempo de execução Objective-C. O C# tipo também deve herdar de [Foundation. NSObject](xref:Foundation.NSObject):

```csharp
namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        // see steps 3-5
    }
}
```

Em terceiro lugar, examine a documentação do Objective-C e crie as instâncias de [ObjCRuntime. Selector](xref:ObjCRuntime.Selector) para cada seletor que você deseja usar. Coloque-os dentro do corpo da classe:

```csharp
static Selector selInit       = new Selector("init");
static Selector selAllObjects = new Selector("allObjects");
static Selector selNextObject = new Selector("nextObject");
```

Em quarto lugar, seu tipo precisará fornecer construtores. Você *deve* encadear sua invocação de construtor ao construtor da classe base. Os atributos de `[Export]` permitem que o código Objective-C chame os construtores com o nome do seletor especificado:

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

Quinta, forneça métodos para cada um dos seletores declarados na etapa 3. Eles usarão `objc_msgSend()` para invocar o seletor no objeto nativo. Observe o uso de [Runtime. GetNSObject ()](xref:ObjCRuntime.Runtime.GetNSObject*) para converter um `IntPtr` em um tipo de `NSObject` (subchave) digitado adequadamente. Se você quiser que o método seja chamado do código Objective-C, o membro *deverá* ser **virtual**.

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

Reunindo tudo isso:

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
