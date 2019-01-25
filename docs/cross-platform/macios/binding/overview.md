---
title: Visão geral de associações do Objective-C
description: Este documento fornece uma visão geral das diferentes maneiras de criar C# associações para o código Objective-C, incluindo associações de linha de comando, projetos de associação e objetivo Sharpie. Ele também discute como funciona a associação.
ms.prod: xamarin
ms.assetid: 9EE288C5-8952-C5A9-E542-0BD847300EC6
author: asb3993
ms.author: amburns
ms.date: 11/25/2015
ms.openlocfilehash: 3f15eaf9171ac44b870239fb5ffa14edd6210360
ms.sourcegitcommit: ee626f215de02707b7a94ba1d0fa1d75b22ab84f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54879297"
---
# <a name="overview-of-objective-c-bindings"></a>Visão geral de associações do Objective-C

_Detalhes de como funciona o processo de associação_

Associação de uma biblioteca Objective-C para uso com o Xamarin usa três etapas:

1. Escrever um C# "Definição de API" para descrever como a API nativa é exposta em .NET e como ele mapeia subjacente Objective-C. Isso é feito usando o padrão C# construções `interface` e a associação de vários **atributos** (consulte este [exemplo simples](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_an_API)).

2. Depois de você ter escrito a definição de API"" em C#, você compilá-lo para produzir um assembly de "binding". Isso pode ser feito na [ **linha de comando** ](#commandline) ou usando um [ **projeto associação** ](#bindingproject) no Visual Studio para Mac ou Visual Studio.

3. Esse assembly "binding", em seguida, é adicionado ao seu projeto de aplicativo do Xamarin, para que você possa acessar a funcionalidade nativa usando a API que você definiu.
  O projeto de associação é completamente separado do seus projetos de aplicativo.

**OBSERVAÇÃO:** Etapa 1 pode ser automatizada com o auxílio de [ **objetivo Sharpie**](#objectivesharpie). Ele examina a API de Objective-C e gera uma proposta C# "Definição de API". Você pode personalizar os arquivos criados por objetivo Sharpie e usá-los em um projeto de associação (ou na linha de comando) para criar o seu assembly de associação. Objetivo Sharpie não cria associações por si só, é simplesmente uma parte opcional do processo maior.

Você também pode ler mais detalhes técnicos da [como ele funciona](#howitworks), que ajudará você a escrever suas associações.

<a name="Command_Line_Bindings" /><a name="commandline" />

## <a name="command-line-bindings"></a>Associações de linha de comando

Você pode usar o `btouch-native` para xamarin. IOS (ou `bmac-native` se você estiver usando o xamarin. Mac) para criar associações diretamente. Ele funciona, passando o C# definições que você criou manualmente API (ou usando o objetivo Sharpie) para a ferramenta de linha de comando (`btouch-native` para iOS ou `bmac-native` para Mac).


A sintaxe geral para invocar essas ferramentas é:

```csharp
# Use this for Xamarin.iOS:
bash$ /Developer/MonoTouch/usr/bin/btouch-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

```csharp
# Use this for Xamarin.Mac:
bash$ bmac-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

O comando acima gerará o arquivo `cocos2d.dll` no diretório atual, e ele conterá a biblioteca totalmente associada que você pode usar em seu projeto. Esta é a ferramenta que o Visual Studio para Mac usa para criar as associações, se você usar um projeto de associação (descrito [abaixo](#bindingproject)).


<a name="bindingproject" />

## <a name="binding-project"></a>Projeto de associação

Um projeto de associação pode ser criado no Visual Studio para Mac ou Visual Studio (Visual Studio só dá suporte a associações de iOS) e torna mais fácil de editar e criar as definições da API de associação (em vez de usar a linha de comando).

Siga esse [guia de Introdução](~/cross-platform/macios/binding/objective-c-libraries.md#Getting_Started) para ver como criar e usar um projeto de associação para produzir uma associação.

<a name="objectivesharpie" />

## <a name="objective-sharpie"></a>Objective Sharpie

Sharpie objetivo é a ferramenta de linha de comando do outro, separado que ajuda com os estágios iniciais de criação de uma associação. Não cria uma associação por si só, em vez disso, ele automatiza a etapa inicial de geração de uma definição de API para a biblioteca nativa de destino.

Ler o [docs objetivo Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md) para aprender como analisar bibliotecas nativas, estruturas nativas e CocoaPods em definições de API que podem ser compiladas em associações.

<a name="howitworks" />

## <a name="how-binding-works"></a>Como funciona a associação

É possível usar o [[registrar]](https://developer.xamarin.com/api/type/Foundation.RegisterAttribute/) atributo [[exportar]](https://developer.xamarin.com/api/type/Foundation.ExportAttribute/) atributo, e [invocação manual de seletor de Objective-C](~/ios/internals/objective-c-selectors.md) juntos para associar manualmente novos (anteriormente tipos de Objective-C não associados).

Primeiro, encontre um tipo que você deseja associar. Para discussão fins (e manter a simplicidade) faremos o vínculo a [NSEnumerator](http://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSEnumerator_Class/Reference/Reference.html) tipo (que já foi associado na [Foundation.NSEnumerator](https://developer.xamarin.com/api/type/Foundation.NSEnumerator/); assim, por exemplo, a implementação abaixo é fins).

Em segundo lugar, precisamos criar o C# tipo. Podemos provavelmente desejará colocar isso em um namespace; uma vez que o Objective-C não oferece suporte a namespaces, será necessário usar o `[Register]` atributo para alterar o nome do tipo que o xamarin. IOS serão registrados com o tempo de execução do Objective-C. O C# tipo também deve herdar de [Foundation.NSObject](https://developer.xamarin.com/api/type/Foundation.NSObject/):

```csharp
namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        // see steps 3-5
    }
}
```

Em terceiro lugar, leia a documentação do Objective-C e crie [ObjCRuntime.Selector](https://developer.xamarin.com/api/type/ObjCRuntime.Selector/) instâncias para cada seletor que deseja usar. Colocá-los dentro do corpo da classe:

```csharp
static Selector selInit       = new Selector("init");
static Selector selAllObjects = new Selector("allObjects");
static Selector selNextObject = new Selector("nextObject");
```

Quarto, seu tipo será necessário fornecer construtores. Você *deve* encadear sua invocação do construtor para o construtor de classe base. O `[Export]` atributos permitir que o código de Objective-C para chamar os construtores com o nome do seletor especificado:

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

Quinto, fornecem métodos para cada um dos seletores declarado na etapa 3. Eles usarão `objc_msgSend()` para invocar o seletor de no objeto nativo. Observe o uso de [Runtime.GetNSObject()](https://developer.xamarin.com/api/member/ObjCRuntime.Runtime.GetNSObject/(System.IntPtr)) para converter um `IntPtr` em adequadamente tipado `NSObject` (sub) tipo. Se você quiser que o método a ser chamado do código Objective-C, o membro *devem* ser **virtual**.

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

Juntando as peças:

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

## <a name="related-links"></a>Links relacionados

- [Xamarin University curso: Compilando uma biblioteca de associações do Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin University curso: Criar uma biblioteca de associações do Objective-C com objetivo Sharpie](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)