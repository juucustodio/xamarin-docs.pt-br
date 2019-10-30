---
title: Suporte a Objective-C
description: Este documento fornece uma descrição do suporte para Objective-C na inserção do .NET. Ele aborda a contagem de referência automática, NSString, protocolos, protocolo NSObject, exceções e muito mais.
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: e72f950dc6fcf12e70714e0fbb996ad5ea432548
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029703"
---
# <a name="objective-c-support"></a>Suporte a Objective-C

## <a name="specific-features"></a>Recursos específicos

A geração de Objective-C tem alguns recursos especiais que valem a pena observar.

### <a name="automatic-reference-counting"></a>Contagem de referência automática

O uso da contagem de referência automática (ARC) é **necessário** para chamar as associações geradas. O projeto usando uma biblioteca baseada em incorporação .NET deve ser compilado com `-fobjc-arc`.

### <a name="nsstring-support"></a>Suporte do NSString

As APIs que expõem tipos de `System.String` são convertidas em `NSString`. Isso facilita o gerenciamento de memória do que ao lidar com `char*`.

### <a name="protocols-support"></a>Suporte a protocolos

As interfaces gerenciadas são convertidas em protocolos Objective-C, onde todos os membros são `@required`.

### <a name="nsobject-protocol-support"></a>Suporte do protocolo NSObject

Por padrão, o hash padrão e a igualdade de tempo de execução do .NET e do Objective-C são considerados intercambiáveis, pois compartilham semânticas semelhantes.

Quando um tipo gerenciado substitui `Equals(Object)` ou `GetHashCode`, isso geralmente significa que o comportamento padrão (.NET) não era suficiente; Isso implica que o comportamento padrão de Objective-C provavelmente não é suficiente.

Nesses casos, o gerador substitui o método [`isEqual:`](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc) e [`hash`](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc) propriedade definida no [protocolo`NSObject`](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc). Isso permite que a implementação gerenciada personalizada seja usada do código Objective-C de forma transparente.

### <a name="exceptions-support"></a>Suporte a exceções

A passagem de `--nativeexception` como um argumento para `objcgen` converterá exceções gerenciadas em exceções de Objective-C que podem ser detectadas e processadas. 

### <a name="comparison"></a>Comparação

Os tipos gerenciados que implementam `IComparable` (ou sua versão genérica `IComparable<T>`) produzirão métodos amigáveis de Objective-C que retornam um `NSComparisonResult` e aceitam um argumento de `nil`. Isso torna a API gerada mais amigável para os desenvolvedores de Objective-C. Por exemplo:

```objc
- (NSComparisonResult)compare:(XAMComparableType * _Nullable)other;
```

### <a name="categories"></a>Categorias

Os métodos de extensões gerenciadas são convertidos em categorias. Por exemplo, os seguintes métodos de extensão no `Collection`:

```csharp
public static class SomeExtensions {
    public static int CountNonNull (this Collection collection) { ... }
    public static int CountNull (this Collection collection) { ... }
}
```

criaria uma categoria Objective-C como esta:

```objc
@interface Collection (SomeExtensions)

- (int)countNonNull;
- (int)countNull;

@end
```

Quando um único tipo gerenciado estende vários tipos, várias categorias de Objective-C são geradas.

### <a name="subscripting"></a>Subscrito

As propriedades indexadas gerenciadas são convertidas em assinatura de objeto. Por exemplo:

```csharp
public bool this[int index] {
    get { return c[index]; }
    set { c[index] = value; }
}
```

criaria Objective-C semelhante a:

```objc
- (id)objectAtIndexedSubscript:(int)idx;
- (void)setObject:(id)obj atIndexedSubscript:(int)idx;
```

que pode ser usado por meio da sintaxe de assinatura Objective-C:

```objc
if ([intCollection [0] isEqual:@42])
    intCollection[0] = @13;
```

Dependendo do tipo de indexador, a assinatura indexada ou com chave será gerada quando apropriado.

Este [artigo](https://nshipster.com/object-subscripting/) é uma ótima introdução à assinatura.

## <a name="main-differences-with-net"></a>Principais diferenças com o .NET

### <a name="constructors-vs-initializers"></a>Construtores vs inicializadores

Em Objective-C, você pode chamar qualquer um dos protótipos do inicializador de qualquer uma das classes pai na cadeia de herança, a menos que esteja marcado como indisponível (`NS_UNAVAILABLE`).

Em C# você deve declarar explicitamente um membro de Construtor dentro de uma classe, o que significa que os construtores não são herdados.

Para expor a representação correta da C# API para Objective-C,`NS_UNAVAILABLE`é adicionada a qualquer inicializador que não está presente na classe filho da classe pai.

C#API

```csharp
public class Unique {
    public Unique () : this (1)
    {
    }

    public Unique (int id)
    {
    }
}

public class SuperUnique : Unique {
    public SuperUnique () : base (911)
    {
    }
}
```

API Surface-C de Objective:

```objc
@interface SuperUnique : Unique

- (instancetype)initWithId:(int)id NS_UNAVAILABLE;
- (instancetype)init;

@end
```

Aqui, `initWithId:` foi marcado como indisponível.

### <a name="operator"></a>Operador

O Objective-C não dá suporte à sobrecarga de C# operador como faz, portanto os operadores são convertidos em seletores de classe:

```csharp
public static AllOperators operator + (AllOperators c1, AllOperators c2)
{
    return new AllOperators (c1.Value + c2.Value);
}
```

para

```objc
+ (instancetype)add:(Overloads_AllOperators *)anObjectC1 c2:(Overloads_AllOperators *)anObjectC2;
```

No entanto, algumas linguagens .NET não oferecem suporte à sobrecarga de operador, portanto, é comum também incluir um método nomeado ["amigável"](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads) além da sobrecarga de operador.

Se a versão do operador e a versão "amigável" forem encontradas, somente a versão amigável será gerada, pois elas serão geradas para o mesmo nome de Objective-C.

```csharp
public static AllOperatorsWithFriendly operator + (AllOperatorsWithFriendly c1, AllOperatorsWithFriendly c2)
{
    return new AllOperatorsWithFriendly (c1.Value + c2.Value);
}

public static AllOperatorsWithFriendly Add (AllOperatorsWithFriendly c1, AllOperatorsWithFriendly c2)
{
    return new AllOperatorsWithFriendly (c1.Value + c2.Value);
}
```

ficará

```objc
+ (instancetype)add:(Overloads_AllOperatorsWithFriendly *)anObjectC1 c2:(Overloads_AllOperatorsWithFriendly *)anObjectC2;
```

### <a name="equality-operator"></a>Operador de igualdade

No operador geral `==` no C# é tratado como um operador geral, conforme observado acima.

No entanto, se o operador "friendly" Equals for encontrado, tanto o operador `==` quanto o operador `!=` serão ignorados na geração.

### <a name="datetime-vs-nsdate"></a>DateTime vs NSDate

Na documentação do [`NSDate`](https://developer.apple.com/reference/foundation/nsdate?language=objc) :

> `NSDate` objetos encapsulam um único ponto no tempo, independentemente de qualquer sistema calendrical ou fuso horário específico. Os objetos Date são imutáveis, representando um intervalo de tempo invariável relativo a uma data de referência absoluta (00:00:00 UTC em 1 de janeiro de 2001).

Devido à data de referência `NSDate`, todas as conversões entre ela e `DateTime` devem ser feitas em UTC.

#### <a name="datetime-to-nsdate"></a>DateTime para NSDate

Ao converter de `DateTime` para `NSDate`, a propriedade `Kind` em `DateTime` é levada em conta:

|Tipo|Resultados|
|---|---|
|`Utc`|A conversão é executada usando o objeto de `DateTime` fornecido como está.|
|`Local`|O resultado da chamada de `ToUniversalTime()` no objeto `DateTime` fornecido é usado para conversão.|
|`Unspecified`|O objeto `DateTime` fornecido é considerado como UTC, portanto, o mesmo comportamento quando `Kind` é `Utc`.|

A conversão usa a seguinte fórmula:

```
TimeInterval = DateTimeObjectTicks - NSDateReferenceDateTicks / TicksPerSecond
```

Nesta fórmula: 

- `NSDateReferenceDateTicks` é calculado com base na data de referência `NSDate` de 00:00:00 UTC em 1 de janeiro de 2001: 

    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```

- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) é definido em [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

Para criar o objeto `NSDate`, o `TimeInterval` é usado com o seletor `NSDate` [dateWithTimeIntervalSinceReferenceDate:](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc) .

#### <a name="nsdate-to-datetime"></a>NSDate para DateTime

A conversão de `NSDate` para `DateTime` usa a seguinte fórmula:

```
DateTimeTicks = NSDateTimeIntervalSinceReferenceDate * TicksPerSecond + NSDateReferenceDateTicks
```

Nesta fórmula: 

- `NSDateReferenceDateTicks` é calculado com base na data de referência `NSDate` de 00:00:00 UTC em 1 de janeiro de 2001: 

    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```

- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) é definido em [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

Depois de calcular `DateTimeTicks`, o [Construtor](https://docs.microsoft.com/dotnet/api/system.datetime.-ctor?#System_DateTime__ctor_System_Int64_System_DateTimeKind_) de `DateTime` é invocado, definindo seu `kind` como `DateTimeKind.Utc`.

> [!NOTE]
> `NSDate` pode ser `nil`, mas um `DateTime` é um struct no .NET, que por definição não pode ser `null`. Se você fornecer um `nil` `NSDate`, ele será convertido para o valor de `DateTime` padrão, que é mapeado para `DateTime.MinValue`.

`NSDate` dá suporte a um valor máximo mais alto e menor que `DateTime`. Ao converter de `NSDate` para `DateTime`, esses valores superiores e inferiores são alterados para o `DateTime` [MaxValue](https://docs.microsoft.com/dotnet/api/system.datetime.maxvalue) ou [MinValue](https://docs.microsoft.com/dotnet/api/system.datetime.minvalue), respectivamente.
