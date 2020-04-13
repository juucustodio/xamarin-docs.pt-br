---
title: Subclasses genéricas de NSObject em Xamarin.iOS
description: Este documento descreve como criar subclasses genéricas do NSObject. Ele examina o que pode ou não ser feito, discute o registrador estático e dá uma olhada no desempenho.
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 279fcac1611038613bf442e1b766fda45dd5a429
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73022359"
---
# <a name="generic-subclasses-of-nsobject-in-xamarinios"></a>Subclasses genéricas de NSObject em Xamarin.iOS

## <a name="using-generics-with-nsobjects"></a>Usando genéricos com NSObjects

É possível usar genéricos em subclasses de `NSObject`, por [exemplo, UIView](xref:UIKit.UIView):

```csharp
class Foo<T> : UIView {
    public Foo (CGRect x) : base (x) {}
    public override void Draw (CoreGraphics.CGRect rect)
    {
        Console.WriteLine ("T: {0}. Type: {1}", typeof (T), GetType ().Name);
    }
}
```

Uma vez que `NSObject` os objetos que a subclasse são registrados com o tempo de `NSObject` execução Objective-C, existem algumas limitações quanto ao que é possível com subclasses genéricas de tipos.

## <a name="considerations-for-generic-subclasses-of-nsobject"></a>Considerações para subclasses genéricas do NSObject

Este documento detalha as limitações no suporte `NSObjects`limitado para subclasses genéricas de .

### <a name="generic-type-arguments-in-member-signatures"></a>Argumentos de tipo genéricos em assinaturas de membros

Todos os argumentos de tipo genéricos em uma `NSObject` assinatura de membro exposto ao Objective-C devem ter uma restrição.

**Bom:**

```csharp
class Generic<T> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Razão**: O parâmetro de `NSObject`tipo genérico é um `myMethod:` , de modo que a assinatura do `NSObject` seletor para pode ser exposta com segurança ao Objective-C (será sempre ou uma subclasse dele).

**Ruim:**

```csharp
class Generic<T> : NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Motivo**: não é possível criar uma assinatura Objective-C para os membros exportados que o código Objective-C `T`pode chamar, uma vez que a assinatura diferiria dependendo do tipo exato do tipo genérico .

**Bom:**

```csharp
class Generic<T> : NSObject
{
    T storage;

    [Export ("myMethod:")]
    public void MyMethod (NSObject value)
    {
    }
}
```

**Razão**: é possível ter argumentos genéricos sem restrições, desde que não participem da assinatura dos membros exportados.

**Bom:**

```csharp
class Generic<T, U> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
        Console.WriteLine (typeof (U));
    }
}
```

**Razão**: `T` o parâmetro no Objetivo-C exportado `MyMethod` `NSObject`é constrangido a ser um , o tipo `U` sem restrições não faz parte da assinatura.

### <a name="instantiations-of-generic-types-from-objective-c"></a>Instantivas de tipos genéricos de Objetivo-C

A instanciação de tipos genéricos do Objective-C não é permitida. Isso normalmente ocorre quando um tipo gerenciado é usado em um xib ou um storyboard.

Considere essa definição de classe, que expõe `IntPtr` um construtor que toma uma (a maneira Xamarin.iOS de construir um objeto C# a partir de uma instância Objetiva-C nativa):

```csharp
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

Embora a construção acima seja boa, em tempo de execução, isso abrirá uma exceção se o Objective-C tentar criar uma instância dele.

Isso acontece porque o Objective-C não tem conceito de tipos genéricos, e não pode especificar o tipo genérico exato a ser criado.

Esse problema pode ser trabalhado criando uma subclasse especializada do tipo genérico. Por exemplo:

```csharp
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}

class GenericUIView : Generic<UIView>
{
}
```

Agora não há mais ambiguidade, `GenericUIView` a classe pode ser usada em xibs ou storyboards.

## <a name="no-support-for-generic-methods"></a>Sem suporte para métodos genéricos

### <a name="generic-methods-are-not-allowed"></a>Métodos genéricos não são permitidos.

O seguinte código não compilará:

```csharp
class MyClass : NSObject
{
    [Export ("myMethod")]
    public void MyMethod<T> (T argument)
    {
    }
}
```

**Razão**: Isso não é permitido porque xamarin.iOS não sabe `T` qual tipo usar para o argumento do tipo quando o método é invocado a partir de Objective-C .

Uma alternativa é criar um método especializado e exportar que em vez disso:

```csharp
class MyClass : NSObject
{
    [Export ("myMethod")]
    public void MyUIViewMethod (UIView argument)
    {
        MyMethod<UIView> (argument);
    }
    public void MyMethod<T> (T argument)
    {
    }
}
```

### <a name="no-exported-static-members-allowed"></a>Não é permitido membros estáticos exportados

Você não pode expor um membro estático ao Objective-C se `NSObject`ele estiver hospedado dentro de uma subclasse genérica de .

Exemplo de um cenário sem suporte:

```csharp
class Generic<T> : NSObject where T : NSObject
{
    [Export ("myMethod:")]
    public static void MyMethod ()
    {
    }

    [Export ("myProperty")]
    public static T MyProperty { get; set; }
}
```

**Motivo:** Assim como os métodos genéricos, o tempo de execução xamarin.iOS precisa `T`ser capaz de saber que tipo usar para o argumento do tipo genérico .

Por exemplo, membros da própria instância são usados (uma vez que nunca haverá uma instância `Generic<T>`, sempre haverá `Generic<SomeSpecificClass>`), mas para membros estáticos essas informações não estão presentes.

Observe que isso se aplica mesmo se o membro `T` em questão não usar o argumento do tipo de forma alguma.

A alternativa neste caso é criar uma subclasse especializada:

```csharp
class GenericUIView : Generic<UIView>
{
    [Export ("myUIViewMethod")]
    public static void MyUIViewMethod ()
    {
        MyMethod ();
    }

    [Export ("myProperty")]
    public static UIView MyUIViewProperty {
        get { return MyProperty; }
        set { MyProperty = value; }
    }
}

class Generic<T> : NSObject where T : NSObject
{
    public static void MyMethod () {}
    public static T MyProperty { get; set; }
}
```

## <a name="performance"></a>Desempenho

O registrador estático não pode resolver um membro exportado em um tipo genérico no momento da compilação, como normalmente faz, ele tem que ser olhado em tempo de execução. Isso significa que invocar tal método do Objective-C é um pouco mais lento do que invocar membros de classes não genéricas.
