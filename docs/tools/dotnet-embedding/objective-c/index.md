---
title: Suporte de Objective-C
description: Este documento fornece uma descrição do suporte para Objective-C na inserção do .NET. Ele aborda a contagem de referência automática, NSString, protocolos, NSObject protocolo, exceções e muito mais.
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 95604133293f0fb2fe9b651fd7cb6b18f3994c84
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793844"
---
# <a name="objective-c-support"></a>Suporte de Objective-C

## <a name="specific-features"></a>Recursos específicos

A geração de Objective-C tem alguns recursos especiais que vale a pena observar.

### <a name="automatic-reference-counting"></a>Contagem de referência automática

O uso de automático referência ARC (contagem) é **necessária** para chamar as associações geradas. Usando uma biblioteca com base em .NET incorporação de projeto deve ser compilado com `-fobjc-arc`.

### <a name="nsstring-support"></a>Suporte de NSString

APIs que expõem `System.String` tipos são convertidos em `NSString`. Isso facilita o gerenciamento de memória mais ao lidar com `char*`.

### <a name="protocols-support"></a>Suporte a protocolos

Interfaces gerenciadas são convertidos em protocolos Objective-C em que todos os membros são `@required`.

### <a name="nsobject-protocol-support"></a>Suporte a protocolo NSObject

Por padrão, o padrão de hash e de igualdade do .NET e o tempo de execução Objective-C devem para ser intercambiáveis, pois eles compartilham uma semântica semelhante.

Quando um tipo gerenciado substitui `Equals(Object)` ou `GetHashCode`, isso geralmente significa que o comportamento padrão (.NET) não era suficiente; isso implica que o comportamento de Objective-C padrão é provavelmente não é suficiente ou.

Nesses casos, o gerador de substitui o [ `isEqual:` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc) método e [ `hash` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc) propriedade definida no [ `NSObject` protocolo](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc). Isso permite que a implementação personalizada gerenciada a ser usado no código Objective-C transparente.

### <a name="exceptions-support"></a>Suporte de exceções

Passando `--nativeexception` como um argumento para `objcgen` converterá exceções gerenciadas em exceções Objective-C podem ser capturadas e processadas. 

### <a name="comparison"></a>Comparação

Tipos que implementam gerenciados `IComparable` (ou sua versão genérica `IComparable<T>`) produzirá Objective-C métodos amigáveis que retornam um `NSComparisonResult` e aceitar um `nil` argumento. Isso torna mais fácil de usar para desenvolvedores Objective-C a API gerada. Por exemplo:

```objc
- (NSComparisonResult)compare:(XAMComparableType * _Nullable)other;
```

### <a name="categories"></a>Categorias

Do Managed extensions métodos são convertidos em categorias. Por exemplo, os seguintes métodos de extensão em `Collection`:

```csharp
public static class SomeExtensions {
    public static int CountNonNull (this Collection collection) { ... }
    public static int CountNull (this Collection collection) { ... }
}
```

criaria uma categoria Objective-C como este:

```objc
@interface Collection (SomeExtensions)

- (int)countNonNull;
- (int)countNull;

@end
```

Quando um único tipo gerenciado estende a vários tipos, várias categorias Objective-C são geradas.

### <a name="subscripting"></a>Subscrito

Propriedades indexadas gerenciadas são convertidas em subscrito de objeto. Por exemplo:

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

que pode ser usado por meio da sintaxe subscripting Objective-C:

```objc
if ([intCollection [0] isEqual:@42])
    intCollection[0] = @13;
```

Dependendo do tipo do seu indexador, subscrito indexado ou com chave será gerado quando apropriado.

Isso [artigo](http://nshipster.com/object-subscripting/) é uma ótima introdução de subscrito.

## <a name="main-differences-with-net"></a>Principais diferenças com o .NET

### <a name="constructors-vs-initializers"></a>Inicializadores de vs construtores

Em Objective-C, você pode chamar qualquer de inicializador de protótipos de qualquer uma das classes pai na cadeia de herança, a menos que ele está marcado como indisponível (`NS_UNAVAILABLE`).

No c#, você deve declarar explicitamente um membro de construtor dentro de uma classe, o que significa que construtores não são herdados.

Para expor a representação à direita da API do c# para Objective-C, `NS_UNAVAILABLE` é adicionado a qualquer inicializador que não está presente na classe filha da classe pai.

API DO C#:

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

Objective-C apresentados API:

```objc
@interface SuperUnique : Unique

- (instancetype)initWithId:(int)id NS_UNAVAILABLE;
- (instancetype)init;

@end
```

Aqui, `initWithId:` foi marcado como indisponível.

### <a name="operator"></a>Operador

Objective-C não suporta o operador de sobrecarga como c#, para os operadores são convertidos para seletores de classe:

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

No entanto, algumas linguagens .NET não dão suporte a sobrecarga de operador, portanto, é comum incluir também um ["amigável"](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads) chamado o método além a sobrecarga de operador.

Se a versão do operador e a versão "amigável" for encontrado, será gerada apenas a versão amigável, como eles gerará o mesmo nome Objective-C.

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

Torna-se:

```objc
+ (instancetype)add:(Overloads_AllOperatorsWithFriendly *)anObjectC1 c2:(Overloads_AllOperatorsWithFriendly *)anObjectC2;
```

### <a name="equality-operator"></a>Operador de igualdade

Em geral operador `==` em c# é tratado como um geral operador conforme observado acima.

No entanto, se o operador de igualdade "amigável" for encontrado, o operador de ambas as `==` e operador `!=` geração serão ignorados.

### <a name="datetime-vs-nsdate"></a>DateTime vs NSDate

Do [ `NSDate` ](https://developer.apple.com/reference/foundation/nsdate?language=objc) documentação:

> `NSDate` objetos encapsulam um único ponto no tempo, independente de qualquer sistema calendrical específica ou o fuso horário. Objetos de data são imutáveis, que representa um intervalo de tempo invariável em relação a uma data de referência absoluta (00: 00:00 UTC em 1 de janeiro de 2001).

Devido a `NSDate` referência data, todas as conversões entre ele e `DateTime` devem ser feitas no UTC.

#### <a name="datetime-to-nsdate"></a>DateTime para NSDate

Ao converter de `DateTime` para `NSDate`, o `Kind` propriedade `DateTime` é levada em conta:

|Tipo|Resultados|
|---|---|
|`Utc`|Conversão é realizada usando fornecido `DateTime` objeto como está.|
|`Local`|O resultado da chamada `ToUniversalTime()` fornecidos `DateTime` objeto é usado para conversão.|
|`Unspecified`|Fornecido `DateTime` objeto é considerado como UTC, até mesmo comportamento quando `Kind` é `Utc`.|

A conversão usa a seguinte fórmula:

```
TimeInterval = DateTimeObjectTicks - NSDateReferenceDateTicks / TicksPerSecond
```

Esta fórmula: 

- `NSDateReferenceDateTicks` é calculado com base no `NSDate` fazem referência a data de 00:00:00 UTC em 1 de janeiro de 2001: 
    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```
- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) é definido em [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

Para criar o `NSDate` objeto, o `TimeInterval` é usado com o `NSDate` [dateWithTimeIntervalSinceReferenceDate:](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc) seletor.

#### <a name="nsdate-to-datetime"></a>NSDate para DateTime

A conversão de `NSDate` para `DateTime` usa a seguinte fórmula:

```
DateTimeTicks = NSDateTimeIntervalSinceReferenceDate * TicksPerSecond + NSDateReferenceDateTicks
```

Esta fórmula: 

- `NSDateReferenceDateTicks` é calculado com base no `NSDate` fazem referência a data de 00:00:00 UTC em 1 de janeiro de 2001: 
    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```
- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) é definido em [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

Depois de calcular `DateTimeTicks`, o `DateTime` [construtor](https://docs.microsoft.com/dotnet/api/system.datetime.-ctor?#System_DateTime__ctor_System_Int64_System_DateTimeKind_) é invocado, definindo seu `kind` para `DateTimeKind.Utc`.

> [!NOTE]
> `NSDate` pode ser `nil`, mas um `DateTime` é um struct no .NET, que, por definição, não pode ser `null`. Se você fornecer um `nil` `NSDate`, ele será convertido para o padrão `DateTime` valor, que é mapeado para `DateTime.MinValue`.

`NSDate` oferece suporte a um máximo maior e um valor mínimo menor do que `DateTime`. Ao converter de `NSDate` para `DateTime`, esses valores superiores e inferiores são alterados para o `DateTime` [MaxValue](https://docs.microsoft.com/dotnet/api/system.datetime.maxvalue) ou [MinValue](https://docs.microsoft.com/dotnet/api/system.datetime.minvalue), respectivamente.
