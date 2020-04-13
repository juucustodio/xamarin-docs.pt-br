---
title: Visão geral das vinculações Objetivas-C
description: Este documento fornece uma visão geral de diferentes maneiras de criar vinculações C# para código Objective-C, incluindo vinculações de linha de comando, projetos de vinculação e Sharpie objetivo. Também discute como funciona a vinculação.
ms.prod: xamarin
ms.assetid: 9EE288C5-8952-C5A9-E542-0BD847300EC6
author: davidortinau
ms.author: daortin
ms.date: 11/25/2015
ms.openlocfilehash: be2f7f555b76d472f7a66d95e661bb2f5884c58f
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292768"
---
# <a name="overview-of-objective-c-bindings"></a>Visão geral das vinculações Objetivas-C

_Detalhes de como funciona o processo de vinculação_

A vinculação de uma biblioteca Objective-C para uso com Xamarin dá três passos:

1. Escreva uma definição de API C# para descrever como a API nativa é exposta em .NET e como ela mapeia para o Objetivo-C subjacente. Isso é feito usando construções `interface` C# padrão como e vários **atributos** de vinculação (veja este [exemplo simples](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_an_API)).

2. Depois de escrever a "definição de API" em C#, compile-a para produzir uma montagem "vinculação". Isso pode ser feito na [**linha de comando**](#commandline) ou usando um projeto de [**vinculação**](#bindingproject) no Visual Studio para Mac ou Visual Studio.

3. Esse conjunto de "vinculação" é então adicionado ao seu projeto de aplicativo Xamarin, para que você possa acessar a funcionalidade nativa usando a API que você definiu.
   O projeto de vinculação é completamente separado de seus projetos de aplicação.

   > [!NOTE]
   > A etapa 1 pode ser automatizada com a assistência da [**Objective Sharpie**](#objectivesharpie). Ele examina a API Objective-C e gera uma proposta c# "definição de API". Você pode personalizar os arquivos criados pelo Objective Sharpie e usá-los em um projeto de vinculação (ou na linha de comando) para criar seu conjunto de vinculação. Sharpie objetivo não cria vinculações por si só, é apenas uma parte opcional do processo maior.

Você também pode ler mais detalhes [técnicos de como ele funciona,](#howitworks)o que vai ajudá-lo a escrever suas vinculações.

<a name="Command_Line_Bindings" /><a name="commandline" />

## <a name="command-line-bindings"></a>Vinculações da linha de comando

Você pode `btouch-native` usar o para Xamarin.iOS (ou `bmac-native` se estiver usando Xamarin.Mac) para construir ligações diretamente. Ele funciona passando as definições de API C# que você criou manualmente (ou usando`btouch-native` Sharpie `bmac-native` Objetivo) para a ferramenta de linha de comando (para iOS ou para Mac).

A sintaxe geral para invocar essas ferramentas é:

```csharp
# Use this for Xamarin.iOS:
bash$ /Developer/MonoTouch/usr/bin/btouch-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

```csharp
# Use this for Xamarin.Mac:
bash$ bmac-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

O comando acima gerará o arquivo `cocos2d.dll` no diretório atual, e ele conterá a biblioteca totalmente vinculada que você pode usar em seu projeto. Esta é a ferramenta que o Visual Studio for Mac usa para criar suas vinculações se você usar um projeto de vinculação (descrito [abaixo](#bindingproject)).

<a name="bindingproject" />

## <a name="binding-project"></a>Projeto de Vinculação

Um projeto de vinculação pode ser criado no Visual Studio para Mac ou Visual Studio (o Visual Studio só suporta ligações iOS) e facilita a edição e a construção de definições de API para vinculação (versus usando a linha de comando).

Siga este [guia de início](~/cross-platform/macios/binding/objective-c-libraries.md#Getting_Started) para ver como criar e usar um projeto de vinculação para produzir uma vinculação.

<a name="objectivesharpie" />

## <a name="objective-sharpie"></a>Objective Sharpie

Objective Sharpie é outra ferramenta de linha de comando separada que ajuda nos estágios iniciais da criação de uma vinculação. Ele não cria uma vinculação por si só, mas automatiza o passo inicial de gerar uma definição de API para a biblioteca nativa alvo.

Leia os [docs sharpie objetivos](~/cross-platform/macios/binding/objective-sharpie/index.md) para aprender como analisar bibliotecas nativas, frameworks nativos e CacauPods em definações de API que podem ser incorporadas em vinculações.

<a name="howitworks" />

## <a name="how-binding-works"></a>Como funciona a vinculação

É possível usar o atributo [[Registro],](xref:Foundation.RegisterAttribute) o atributo [[Exportar]](xref:Foundation.ExportAttribute) e a [invocação manual do seletor Objective-C](~/ios/internals/objective-c-selectors.md) para vincular manualmente novos tipos Objetivo-C (anteriormente desvinculados).

Primeiro, encontre um tipo que você deseja vincular. Para fins de discussão (e simplicidade), vamos vincular o tipo [NSEnumerator](https://developer.apple.com/documentation/foundation/nsenumerator) (que já foi vinculado em [Foundation.NSEnumerator](xref:Foundation.NSEnumerator); a implementação abaixo é apenas para fins de exemplo).

Em segundo lugar, precisamos criar o tipo C#. Nós provavelmente vamos querer colocar isso em um namespace; uma vez que o Objective-C não suporta namespaces, precisaremos usar o atributo `[Register]` para alterar o nome de tipo que o Xamarin.iOS registrará com o tempo de execução Objective-C. O tipo C# também deve herdar de [Foundation.NSObject](xref:Foundation.NSObject):

```csharp
namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        // see steps 3-5
    }
}
```

Em terceiro lugar, revise a documentação Objective-C e crie [oobjCRuntime.Seletor](xref:ObjCRuntime.Selector) para cada seletor que você deseja usar. Coloque-os dentro do corpo da classe:

```csharp
static Selector selInit       = new Selector("init");
static Selector selAllObjects = new Selector("allObjects");
static Selector selNextObject = new Selector("nextObject");
```

Em quarto lugar, seu tipo precisará fornecer construtores. Você *deve acorrentar* sua invocação de construtor para o construtor de classe base. Os `[Export]` atributos permitem que o código Objective-C chame os construtores com o nome do seletor especificado:

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

Quinto, forneça métodos para cada um dos Seletores declarados na Etapa 3. Estes `objc_msgSend()` serão usados para invocar o seletor no objeto nativo. Observe o uso de [Runtime.GetNSObject()](xref:ObjCRuntime.Runtime.GetNSObject*) para converter `IntPtr` `NSObject` um em um tipo (sub)digitado apropriadamente. Se você quiser que o método seja callable a partir do código Objective-C, o membro *deve* ser **virtual**.

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
