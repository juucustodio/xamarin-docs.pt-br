---
title: Subclasses genéricas de NSObject no xamarin. IOS
description: Este documento descreve como criar criar subclasses genéricas de NSObject. Ele examina o que pode e não pode ser feito, discute o registrador estático e examina o desempenho.
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 512280e9c298cfbcea6f693b0691236fd1cf5a5f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61036463"
---
# <a name="generic-subclasses-of-nsobject-in-xamarinios"></a>Subclasses genéricas de NSObject no xamarin. IOS

## <a name="using-generics-with-nsobjects"></a>Usando genéricos com NSObjects

Começando com o xamarin. IOS 7.2.1, você pode usar genéricos em subclasses de `NSObject` (por exemplo [UIView](xref:UIKit.UIView).

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

Objetos desde que a subclasse `NSObject` são registrados com o tempo de execução do Objective-C, existem algumas limitações sobre o que é possível com subclasses genéricas de `NSObject` tipos.
    
## <a name="considerations-for-generic-subclasses-of-nsobject"></a>Considerações para subclasses genéricas de NSObject

Este documento fornece detalhes sobre as limitações no suporte limitado para subclasses genéricas de `NSObjects` introduzido com o xamarin. IOS 7.2.1.
    
### <a name="generic-type-arguments-in-member-signatures"></a>Argumentos de tipo genérico em assinaturas de membro

Todos os argumentos de tipo genérico em uma assinatura de membro exposto ao Objective-C devem ter um `NSObject` restrição.

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

**Motivo**: O parâmetro de tipo genérico é um `NSObject`, de modo que a assinatura do seletor para `myMethod:` podem ser expostos com segurança para Objective-C (sempre será `NSObject` ou uma subclasse dela).

**Ruim**:

```csharp
class Generic<T> : NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Motivo**: não é possível criar uma assinatura de Objective-C para os membros exportados que chama código Objective-C, uma vez que a assinatura seria diferente dependendo do tipo exato de tipo genérico `T`.

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

**Motivo**: é possível ter argumentos de tipo genérico sem restrições, desde que eles não fizer parte da assinatura do membro exportado.

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

**Motivo**: o `T` exportados de parâmetro em Objective-C `MyMethod` é restrito a não ser um `NSObject`, o tipo irrestrito `U` não faz parte da assinatura.
    
### <a name="instantiations-of-generic-types-from-objective-c"></a>Instanciações de tipos genéricos de Objective-C

Instanciação de tipos genéricos de Objective-C não é permitida. Isso normalmente ocorre quando um tipo gerenciado é usado em um xib.

Considere esta definição de classe, que expõe um construtor que usa um `IntPtr` (a maneira de xamarin. IOS de construir um C# objeto a partir de uma instância de Objective-C nativo):
    
```
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

Enquanto o constructo acima está tudo bem, em tempo de execução, isso gerará uma exceção em se Objective-C tenta criar uma instância dele.

É por isso acontece porque o Objective-C não tem nenhum conceito de tipos genéricos e ele não é possível especificar o tipo exato de genérico para criar.

Esse problema pode ser contornado, criando uma subclasse especializada do tipo genérico.   Por exemplo:
    
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

**Motivo**: Isso não é permitido porque o xamarin. IOS não sabe qual tipo para usar para o argumento de tipo `T` quando o método é invocado do Objective-C.

Uma alternativa é criar um método especializado e a exportação em vez disso:

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

Você não pode expor um membros estáticos para Objective-C que ele esteja hospedado dentro de uma subclasse genérica de `NSObject`.

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

**Motivo:** Assim como métodos genéricos, o tempo de execução do xamarin. IOS precisa ser capaz de saber que tipo a ser usado para o argumento de tipo genérico T.

Por exemplo os membros de instância em si é usada (já que nunca haverá uma instância genérica<T>, sempre será genérico<SomeSpecificClass>), mas essas informações para membros estáticos não estão presentes.

Observe que isso se aplica mesmo se o membro em questão não usa o argumento de tipo T de forma alguma.

Nesse caso, a alternativa é criar uma subclasse especializada:

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

O suporte a genéricos exige que o novo [sistema de registro](~/ios/internals/registrar.md).

Se você tentar usar o antigo sistema de registro herdado mostrará avisos quando encontra tipos genéricos (Além disso, para não gerar código correto, resultando em um comportamento indefinido).
    
## <a name="performance"></a>Desempenho

O registrador estático não é possível resolver um membro exportado em um tipo genérico em tempo de compilação como geralmente faz, ele deve ser pesquisado em tempo de execução. Isso significa que a invocar o método do Objective-C é um pouco mais lento que invocar os membros de classes não genéricas.

