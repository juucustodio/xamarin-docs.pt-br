---
title: Subclasses genéricas de NSObject no Xamarin. iOS
description: Este documento descreve como criar criar subclasses genéricas de NSObject. Ele examina o que pode e não pode ser feito, discute o registrador estático e examina o desempenho.
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 531d0b2b6141cc0e1f4014f1d3422af3c6f8643a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291975"
---
# <a name="generic-subclasses-of-nsobject-in-xamarinios"></a>Subclasses genéricas de NSObject no Xamarin. iOS

## <a name="using-generics-with-nsobjects"></a>Usando genéricos com NSObjects

É possível usar genéricos em subclasses de `NSObject`, por exemplo, [UIView](xref:UIKit.UIView):

```csharp
class Foo<T> : UIView {
    public Foo (CGRect x) : base (x) {}
    public override void Draw (CoreGraphics.CGRect rect)
    {
        Console.WriteLine ("T: {0}. Type: {1}", typeof (T), GetType ().Name);
    }
}
```

Como os objetos que a `NSObject` subclasse são registrados com o tempo de execução Objective-C, há algumas limitações quanto ao que é possível com subclasses genéricas de `NSObject` tipos.

## <a name="considerations-for-generic-subclasses-of-nsobject"></a>Considerações para subclasses genéricas de NSObject

Este documento detalha as limitações no suporte limitado para subclasses genéricas de `NSObjects`.

### <a name="generic-type-arguments-in-member-signatures"></a>Argumentos de tipo genérico em assinaturas de membro

Todos os argumentos de tipo genérico em uma assinatura de membro exposta a Objective- `NSObject` C devem ter uma restrição.

**Bom**:

```csharp
class Generic<T> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Motivo**: O parâmetro de tipo genérico é `NSObject`um, portanto, a assinatura `myMethod:` do seletor para pode ser exposta com segurança a Objective-C `NSObject` (sempre será ou uma subclasse dele).

**Inadequado**:

```csharp
class Generic<T> : NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Motivo**: não é possível criar uma assinatura Objective-c para os membros exportados que o código Objective-c pode chamar, pois a assinatura seria diferente dependendo do tipo exato do tipo `T`genérico.

**Bom**:

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

**Motivo**: é possível ter argumentos de tipo genérico irrestrito, desde que eles não façam parte da assinatura de membro exportada.

**Bom**:

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

**Motivo**: o `T` parâmetro no Objective-C exportado `MyMethod` é restrito a ser um `NSObject`, o tipo `U` irrestrito não faz parte da assinatura.

### <a name="instantiations-of-generic-types-from-objective-c"></a>Instanciações de tipos genéricos de Objective-C

A instanciação de tipos genéricos de Objective-C não é permitida. Isso normalmente ocorre quando um tipo gerenciado é usado em um XIB ou um Storyboard.

Considere essa definição de classe, que expõe um construtor que usa `IntPtr` um método (o Xamarin. Ios para construir um C# objeto de uma instância de Objective-C nativa):

```csharp
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

Embora a construção acima esteja bem, em tempo de execução, isso gerará uma exceção em se Objective-C tentar criar uma instância dela.

Isso ocorre porque o Objective-C não tem nenhum conceito de tipos genéricos e não pode especificar o tipo genérico exato a ser criado.

Esse problema pode ser solucionado criando uma subclasse especializada do tipo genérico. Por exemplo:

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

Agora, não há mais ambiguidade, a classe `GenericUIView` pode ser usada em xibs ou storyboards.

## <a name="no-support-for-generic-methods"></a>Não há suporte para métodos genéricos

### <a name="generic-methods-are-not-allowed"></a>Métodos genéricos não são permitidos.

O código a seguir não será compilado:

```csharp
class MyClass : NSObject
{
    [Export ("myMethod")]
    public void MyMethod<T> (T argument)
    {
    }
}
```

**Motivo**: Isso não é permitido porque o Xamarin. Ios não sabe qual tipo usar para o argumento `T` de tipo quando o método é invocado de Objective-C.

Uma alternativa é criar um método especializado e, em vez disso, exportar:

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

### <a name="no-exported-static-members-allowed"></a>Nenhum membro estático exportado é permitido

Você não pode expor um membro estático para Objective-C se ele estiver hospedado dentro de uma subclasse genérica `NSObject`de.

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

**Falha** Assim como os métodos genéricos, o tempo de execução do Xamarin. iOS precisa ser capaz de saber qual tipo usar para o argumento `T`de tipo genérico.

Para membros de instância, a instância em si é usada (já que nunca haverá `Generic<T>`uma instância, ela será `Generic<SomeSpecificClass>`sempre), mas para membros estáticos, essas informações não estão presentes.

Observe que isso se aplica mesmo se o membro em questão não usar o argumento `T` de tipo de forma alguma.

A alternativa nesse caso é criar uma subclasse especializada:

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

O registrador estático não pode resolver um membro exportado em um tipo genérico no momento da compilação, como geralmente faz, ele precisa ser pesquisado no tempo de execução. Isso significa que invocar tal método de Objective-C é um pouco mais lento do que invocar membros de classes não genéricas.

