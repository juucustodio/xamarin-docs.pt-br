---
title: Suporte de Objective-C
ms.topic: article
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 047f7d7497a114bf4b7c94e50bdf09862b882794
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="objective-c-support"></a>Suporte de Objective-C

## <a name="specific-features"></a>Recursos específicos

A geração de ObjC tem recursos especiais, alguns que vale a pena observar.

### <a name="automatic-reference-counting"></a>Contagem de referência automática

O uso de automático referência ARC (contagem) é **necessária** para chamar as associações geradas. Projeto usando uma biblioteca com base em embeddinator deve ser compilado com `-fobjc-arc`.

### <a name="nsstring-support"></a>Suporte de NSString

APIs que expõem `System.String` tipos são convertidos em `NSString`. Isso facilita o gerenciamento de memória que lidar com `char*`.

### <a name="protocols-support"></a>Suporte a protocolos

Interfaces gerenciadas são convertidos em protocolos ObjC onde todos os membros são `@required`.

### <a name="nsobject-protocol-support"></a>Suporte ao protocolo NSObject

Por padrão, pressupomos que o padrão de hash e igualdade de .net e o tempo de execução ObjC são bem e intercambiáveis conforme eles compartilham semântica muito semelhante.

Quando um tipo gerenciado substitui `Equals(Object)` ou `GetHashCode` isso geralmente significa que o comportamento padrão (.NET) não foi o melhor. Podemos pode assumir que o comportamento de Objective-C padrão não é um.

Nesse caso o gerador substitui o [ `isEqual:` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc) método e [ `hash` ](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc) propriedade definida no [ `NSObject` protocolo](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc). Isso permite que a implementação personalizada gerenciada a ser usado no código ObjC transparente.

### <a name="comparison"></a>Comparação

Tipos que implementam gerenciados `IComparable` ou é a versão genérica `IComparable<T>` produzirá ObjC métodos amigáveis que retorna um `NSComparisonResult` e aceitar um `nil` argumento. Isso faz com que a API gerada mais amigável para desenvolvedores de ObjC, por exemplo

```csharp
- (NSComparisonResult)compare:(XAMComparableType * _Nullable)other;
```

### <a name="categories"></a>Categorias

Do Managed extensions métodos são convertidos em categorias. Por exemplo os seguintes métodos de extensão em `Collection`:

```csharp
    public static class SomeExtensions {

        public static int CountNonNull (this Collection collection) { ... }

        public static int CountNull (this Collection collection) { ... }
    }
```

criaria uma categoria Objective-C como este:

```csharp
@interface Collection (SomeExtensions)

- (int)countNonNull;
- (int)countNull;
@end
```

Quando é gerenciado de um único tipo estende vários tipos e várias categorias Objective-C são geradas.

### <a name="subscripting"></a>Subscrito

Propriedades indexadas gerenciadas são convertidas em subscrito de objeto. Por exemplo:

```csharp
    public bool this[int index] {
        get { return c[index]; }
        set { c[index] = value; }
    }
```

criaria Objective-C semelhante a:

```csharp
- (id)objectAtIndexedSubscript:(int)idx;
- (void)setObject:(id)obj atIndexedSubscript:(int)idx;
```

que pode ser usado por meio da sintaxe subscripting Objective-C:

```csharp
    if ([intCollection [0] isEqual:@42])
        intCollection[0] = @13;
```

Dependendo do tipo do seu indexador, subscrito indexado ou com chave será gerado quando apropriado.

Isso [artigo](http://nshipster.com/object-subscripting/) é uma ótima introdução de subscrito.

## <a name="main-differences-with-net"></a>Principais diferenças com o .NET

### <a name="constructors-vs-initializers"></a>Construtores vs inicializadores

Em Objective-C, você pode chamar qualquer de inicializador de protótipos de qualquer uma das classes pai na cadeia de herança, a menos que ele está marcado como indisponível (NS_UNAVAILABLE).

No c#, você deve declarar explicitamente um membro de construtor dentro de uma classe, isso significa que construtores não são herdados.

Para expor a representação à direita da API do c# para Objective-C, adicionamos `NS_UNAVAILABLE` para qualquer inicializador que não está presente na classe filha da classe pai.

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

```objectivec
@interface SuperUnique : Unique

- (instancetype)initWithId:(int)id NS_UNAVAILABLE;
- (instancetype)init;

@end
```

Aqui, podemos ver que `initWithId:` foi marcado como indisponível.

### <a name="operator"></a>Operador

ObjC não dá suporte para operador sobrecarga como c#, para os operadores são convertidos para seletores de classe:

```csharp
    public static AllOperators operator + (AllOperators c1, AllOperators c2)
    {
        return new AllOperators (c1.Value + c2.Value);
    }
```

para

```csharp
+ (instancetype)add:(Overloads_AllOperators *)anObjectC1 c2:(Overloads_AllOperators *)anObjectC2;
```

No entanto, algumas linguagens .NET não dão suporte a sobrecarga de operador, portanto, é comum incluir também um ["amigável"](https://msdn.microsoft.com/en-us/library/ms229032(v=vs.110).aspx) chamado o método além a sobrecarga de operador.

Se a versão do operador e a versão "amigável" for encontrado, será gerada apenas a versão amigável, como eles gerará o mesmo nome do objetivo c.

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

```csharp
+ (instancetype)add:(Overloads_AllOperatorsWithFriendly *)anObjectC1 c2:(Overloads_AllOperatorsWithFriendly *)anObjectC2;
```

### <a name="equality-operator"></a>Operador de igualdade

Em geral operador = = em c# é tratado como um operador geral como mencionado acima.

No entanto, se o operador de igualdade "amigável" for encontrado, ambos os operador = = e o operador! = será ignorada na geração.

### <a name="datetime-vs-nsdate"></a>DateTime vs NSDate

De [do NSDate](https://developer.apple.com/reference/foundation/nsdate?language=objc) documentação:

> Objetos NSDate encapsulam um único ponto no tempo, independente de qualquer sistema calendrical específica ou o fuso horário. Objetos de data são imutáveis, que representa um intervalo de tempo invariável em relação a uma data de referência absoluta (00: 00:00 UTC em 1 de janeiro de 2001).

Devido a `NSDate` referência data, todas as conversões entre ele e `DateTime` devem ser feitas no UTC.

#### <a name="datetime-to-nsdate"></a>DateTime para NSDate

Ao converter de `DateTime` para `NSDate` o DateTime `Kind` propriedade é levada em conta.

|Tipo|Resultados                                                                                            |
|---|---|
|Utc|Conversão é realizada usando fornecido `DateTime` objeto como está.|
|Local|O resultado da chamada `ToUniversalTime()` fornecidos `DateTime` objeto é usado para conversão.|
|Não especificado|Fornecido `DateTime` objeto é considerado como UTC, até mesmo comportamento como tipo = = Utc.|

A conversão é feita usando a fórmula a seguir:

> [!NOTE]
> **TimeInterval** = DateTimeObjectTicks - NSDateReferenceDateTicks[dt] / [TicksPerSecond](https://msdn.microsoft.com/en-us/library/system.timespan.tickspersecond(v=vs.110).aspx)

Assim que tivermos o TimeInterval usamos do NSDate [dateWithTimeIntervalSinceReferenceDate:](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc) seletor para criá-lo.

#### <a name="nsdate-to-datetime"></a>NSDate para DateTime

Indo de NSDate para DateTime, vamos supor que estamos obtendo um NSDate instância que é a data de referência é **00:00:00 UTC em 1 de janeiro de 2001** e use a seguinte fórmula:

> [!NOTE]
> **DateTimeTicks** = NSDateTimeIntervalSinceReferenceDate * [TicksPerSecond](https://msdn.microsoft.com/en-us/library/system.timespan.tickspersecond(v=vs.110).aspx) + NSDateReferenceDateTicks [dt]

Uma vez que é calculada a **DateTimeTicks** usamos o seguinte DateTime [construtor](https://msdn.microsoft.com/en-us/library/w0d47c9c(v=vs.110).aspx) configuração seu `kind` para `DateTimeKind.Utc`.

Há algumas considerações que você deve estar atento, pode ser NSDate `nil` , mas uma data e hora é um struct no .NET e por definição, ele não pode ser `null`. Se você fornecer um `nil` NSDate nós traduzirá com o valor de data e hora padrão que mapeia para `DateTime.MinValue`.

MinValue e MaxValue também são diferentes, NSDate pode dar suporte a limites superiores e inferiores da data e hora forma sempre que você fornecer um valor maior ou menor será definido para DateTime [MaxValue](https://msdn.microsoft.com/en-us/library/system.datetime.maxvalue(v=vs.110).aspx) ou [MinValue](https://msdn.microsoft.com/en-us/library/system.datetime.minvalue(v=vs.110).aspx) respectivamente.

**DT**: data de referência NSDate **00:00:00 UTC em 1 de janeiro de 2001** => `new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;`
