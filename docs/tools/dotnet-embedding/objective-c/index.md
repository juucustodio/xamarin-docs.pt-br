---
title: Suporte a Objective-C
description: Este documento fornece uma descrição do suporte para Objective-C na inserção do .NET. Ele aborda a contagem de referência automática, NSString, protocolos, protocolo NSObject, exceções e muito mais.
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 0053bdf4e34f12accc59bde36c04404a19c6d54b
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227808"
---
# <a name="objective-c-support"></a>Suporte a Objective-C

## <a name="specific-features"></a>Recursos específicos

A geração de Objective-C tem alguns recursos especiais que valem a pena observar.

### <a name="automatic-reference-counting"></a>Contagem de referência automática

O uso da contagem de referência automática (ARC) é **necessário** para chamar as associações geradas. O projeto que usa uma biblioteca baseada em incorporação .NET deve ser `-fobjc-arc`compilado com.

### <a name="nsstring-support"></a>Suporte do NSString

As APIs que `System.String` expõem tipos são `NSString`convertidas em. Isso facilita o gerenciamento de memória do que ao `char*`lidar com o.

### <a name="protocols-support"></a>Suporte a protocolos

As interfaces gerenciadas são convertidas em protocolos Objective- `@required`C, onde todos os membros são.

### <a name="nsobject-protocol-support"></a>Suporte do protocolo NSObject

Por padrão, o hash padrão e a igualdade de tempo de execução do .NET e do Objective-C são considerados intercambiáveis, pois compartilham semânticas semelhantes.

Quando um tipo gerenciado é `Equals(Object)` substituído `GetHashCode`ou, em geral, significa que o comportamento padrão (.net) não era suficiente; isso implica que o comportamento padrão de Objective-C provavelmente não é suficiente.

Nesses casos, o gerador substitui o método [`isEqual:`](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc) e [`hash`](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc) a propriedade definidos no [ `NSObject` protocolo](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc). Isso permite que a implementação gerenciada personalizada seja usada do código Objective-C de forma transparente.

### <a name="exceptions-support"></a>Suporte a exceções

Passar `--nativeexception` como um argumento para `objcgen` converterá exceções gerenciadas em exceções de Objective-C que podem ser capturadas e processadas. 

### <a name="comparison"></a>Comparação

Os tipos gerenciados `IComparable` que implementam (ou `IComparable<T>`sua versão genérica) produzirão métodos `NSComparisonResult` amigáveis de Objective-C `nil` que retornam e aceitam um argumento. Isso torna a API gerada mais amigável para os desenvolvedores de Objective-C. Por exemplo:

```objc
- (NSComparisonResult)compare:(XAMComparableType * _Nullable)other;
```

### <a name="categories"></a>Categorias

Os métodos de extensões gerenciadas são convertidos em categorias. Por exemplo, os seguintes métodos de extensão `Collection`em:

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

Este [artigo](http://nshipster.com/object-subscripting/) é uma ótima introdução à assinatura.

## <a name="main-differences-with-net"></a>Principais diferenças com o .NET

### <a name="constructors-vs-initializers"></a>Construtores vs inicializadores

Em Objective-C, você pode chamar qualquer um dos protótipos de inicializador de qualquer uma das classes pai na cadeia de herança, a menos que esteja marcado`NS_UNAVAILABLE`como indisponível ().

Em C# você deve declarar explicitamente um membro de Construtor dentro de uma classe, o que significa que os construtores não são herdados.

Para expor a representação correta da C# API para Objective-C, `NS_UNAVAILABLE` é adicionada a qualquer inicializador que não está presente na classe filho da classe pai.

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

Aqui, `initWithId:` foi marcado como não disponível.

### <a name="operator"></a>Operator

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

No, o `==` operador C# geral no é tratado como um operador geral, conforme observado acima.

No entanto, se o operador "friendly" Equals for encontrado `==` , ambos `!=` operador e operador serão ignorados na geração.

### <a name="datetime-vs-nsdate"></a>DateTime vs NSDate

[`NSDate`](https://developer.apple.com/reference/foundation/nsdate?language=objc) Na documentação:

> `NSDate`os objetos encapsulam um único ponto no tempo, independentemente de qualquer sistema calendrical ou fuso horário específico. Os objetos Date são imutáveis, representando um intervalo de tempo invariável relativo a uma data de referência absoluta (00:00:00 UTC em 1 de janeiro de 2001).

Devido à `NSDate` data de referência, todas as conversões entre ela `DateTime` e devem ser feitas em UTC.

#### <a name="datetime-to-nsdate"></a>DateTime para NSDate

Ao converter de `DateTime` para `NSDate`, a `Kind` propriedade `DateTime` é levada em conta:

|Tipo|Resultados|
|---|---|
|`Utc`|A conversão é executada usando o `DateTime` objeto fornecido como está.|
|`Local`|O resultado da chamada `ToUniversalTime()` no objeto fornecido `DateTime` é usado para conversão.|
|`Unspecified`|O objeto `DateTime` fornecido é considerado como UTC, portanto, mesmo comportamento quando `Kind` é `Utc`.|

A conversão usa a seguinte fórmula:

```
TimeInterval = DateTimeObjectTicks - NSDateReferenceDateTicks / TicksPerSecond
```

Nesta fórmula: 

- `NSDateReferenceDateTicks`é calculado com base na `NSDate` data de referência de 00:00:00 UTC em 1 de janeiro de 2001: 

    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```

- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond)é definido em[`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

Para criar o `NSDate` objeto, o `TimeInterval` é usado com o `NSDate` seletor [dateWithTimeIntervalSinceReferenceDate:](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc) .

#### <a name="nsdate-to-datetime"></a>NSDate para DateTime

A conversão de `NSDate` para `DateTime` usa a seguinte fórmula:

```
DateTimeTicks = NSDateTimeIntervalSinceReferenceDate * TicksPerSecond + NSDateReferenceDateTicks
```

Nesta fórmula: 

- `NSDateReferenceDateTicks`é calculado com base na `NSDate` data de referência de 00:00:00 UTC em 1 de janeiro de 2001: 

    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```

- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond)é definido em[`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

Depois de `DateTimeTicks`calcular, `DateTime` o [Construtor](https://docs.microsoft.com/dotnet/api/system.datetime.-ctor?#System_DateTime__ctor_System_Int64_System_DateTimeKind_) é invocado, `kind` definindo `DateTimeKind.Utc`seu como.

> [!NOTE]
> `NSDate`pode ser `nil`, mas uma `DateTime` é uma struct no .net, que por definição não pode `null`ser. Se você fornecer um `nil` `NSDate`, ele será convertido para o valor padrão `DateTime` , que é mapeado para `DateTime.MinValue`.

`NSDate`dá suporte a um valor máximo mais alto e menor `DateTime`que. Ao converter de `NSDate` para `DateTime`, `DateTime` esses valores superiores e inferiores são alterados para [MaxValue](https://docs.microsoft.com/dotnet/api/system.datetime.maxvalue) ou [MinValue](https://docs.microsoft.com/dotnet/api/system.datetime.minvalue), respectivamente.
