---
title: Subclasses genéricas de NSObject em xamarin
description: Este documento descreve como criar criar subclasses genéricas de NSObject. Ele examina o que pode e não pode ser feito, discute o registrador estático e analisa desempenho.
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 9caad9d4990225a0468be8ee4987eaa9fea0c118
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786477"
---
# <a name="generic-subclasses-of-nsobject-in-xamarinios"></a>Subclasses genéricas de NSObject em xamarin

## <a name="using-generics-with-nsobjects"></a>Usar genéricos com NSObjects

Começando com o xamarin 7.2.1, você pode usar genéricos em subclasses de `NSObject` (por exemplo [UIView](https://developer.xamarin.com/api/type/UIKit.UIView/)).

Agora você pode criar classes genéricas, como este:

```csharp
class Foo<T> : UIView {
    public Foo (CGRect x) : base (x) {}
    public override void Draw (CoreGraphics.CGRect rect)
    {
        Console.WriteLine ("T: {0}. Type: {1}", typeof (T), GetType ().Name);
    }
}
```

Como objetos que subclasse `NSObject` são registrados com o tempo de execução Objective-C, existem algumas limitações sobre o que é possível com as subclasses genéricas de `NSObject` tipos.
    
## <a name="considerations-for-generic-subclasses-of-nsobject"></a>Considerações para que as subclasses genéricas de NSObject

Este documento detalha as limitações de suporte limitado para genéricos subclasses de `NSObjects` introduzido com o xamarin 7.2.1.
    
### <a name="generic-type-arguments-in-member-signatures"></a>Argumentos de tipo genéricos em assinaturas de membros

Todos os argumentos de tipo genérico em uma assinatura de membro expostas para Objective-C devem ter um `NSObject` restrição.

**Boa**:

```csharp
class Generic<T> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Motivo**: O parâmetro de tipo genérico é um `NSObject`, portanto a assinatura do seletor para `myMethod:` podem ser expostos com segurança para Objective-C (sempre será `NSObject` ou uma subclasse dela).

**Incorreta**:

```csharp
class Generic<T> : NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Motivo**: não é possível criar uma assinatura Objective-C para os membros exportados chamar código Objective-C, desde que a assinatura será diferente dependendo do tipo exato do tipo genérico `T`.

**Boa**:

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

**Motivo**: é possível ter irrestrita argumentos de tipo genérico, desde que eles não fazem parte da assinatura de membro exportado.

**Boa**:

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

**Motivo**: o `T` exportados do parâmetro no Objective-C `MyMethod` é restrito a não ser um `NSObject`, o tipo irrestrito `U` não faz parte da assinatura.
    
### <a name="instantiations-of-generic-types-from-objective-c"></a>Instâncias de tipos genéricos de Objective-C

Instanciação de tipos genéricos de Objective-C não é permitida. Isso normalmente ocorre quando um tipo gerenciado é usado em um xib.

Considere esta definição de classe, que expõe um construtor que aceita um `IntPtr` (a maneira xamarin de construção de um objeto de uma instância de Objective-C nativo c#):
    
```
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

Durante a construção acima é bom, em tempo de execução, isso lançará uma exceção no se Objective-C tenta criar uma instância dele.

É por isso acontece porque Objective-C não tem nenhum conceito de tipos genéricos, e ele não é possível especificar o tipo exato de genérico para criar.

Esse problema pode ser contornado criando uma subclasse especializada do tipo genérico.   Por exemplo:
    
```
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}

class GenericUIView : Generic<UIView>
{
}
```

Agora, não há nenhuma ambiguidade, a classe `GenericUIView` pode ser usado em xibs.

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

**Motivo**: isso não é permitido porque xamarin não sabe a qual o tipo a ser usado para o argumento de tipo `T` quando o método é chamado de Objective-C.

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

### <a name="no-exported-static-members-allowed"></a>Não há membros estáticos exportados permitidos

Você não pode expor um membros estáticos para Objective-C se ele está hospedado dentro de uma subclasse genérica de `NSObject`.

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

**Motivo:** como métodos genéricos, as necessidades de tempo de execução do xamarin para poder saber que tipo a ser usado para o argumento de tipo genérico T.

Para a instância de membros a própria instância é usada (já que nunca haverá uma instância genérica<T>, sempre será genérico<SomeSpecificClass>), mas essas informações para membros estáticos não estão presentes.

Observe que isso se aplica mesmo se o membro em questão não usa o argumento de tipo T de qualquer forma.

Nesse caso a alternativa é criar uma subclasse especializada:

```csharp
class GenericUIView : Generic<UIView>
{
    [Export ("myUIViewMethod")]
    public static void MyUIViewMethod ()
    {
        MyMethod ();
    }

    [Export ("myProperty")]
    public static UIView MyUIVIewProperty {
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

### <a name="requires-new-static-registrar"></a>Requer novo registrador estático

O suporte a genéricos requer novo [sistema de registro](~/ios/internals/registrar.md).

Se você tentar usar o antigo sistema de registro herdado mostrará avisos quando ele encontra tipos genéricos (além de não gerar código correto, resultando em um comportamento indefinido).
    
## <a name="performance"></a>Desempenho

O registrador estático não é possível resolver um membro exportado em um tipo genérico em tempo de compilação como faz normalmente, ele deve ser pesquisado em tempo de execução. Isso significa que a invocar esse método de Objective-C um é um pouco mais lenta do que chamar membros de classes não genérico.

