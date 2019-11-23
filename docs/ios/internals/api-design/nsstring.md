---
title: NSString no Xamarin. iOS e Xamarin. Mac
description: Este documento descreve como o Xamarin. iOS converte de forma transparente objetos NSString C# em objetos de cadeia de caracteres, quando isso não acontece.
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: f744f4ed5619e4e7f4a9d85897c4451bf7e5b9bc
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022354"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>NSString no Xamarin. iOS e Xamarin. Mac

O design das chamadas Xamarin. iOS e Xamarin. Mac para a API de uso para expor o tipo de cadeia de caracteres .NET nativo, `string`, para manipulação C# de cadeia de caracteres no e outras linguagens de programação .net, e para expor cadeia de caracteres como o tipo de dados exposto pela API em vez do tipo de dados `NSString` .

Isso significa que os desenvolvedores não devem manter as cadeias de caracteres destinadas a serem usadas para chamar o Xamarin. iOS & a API do Xamarin. Mac (unificada) em um tipo especial (`Foundation.NSString`), eles podem continuar usando o `System.String` do mono para todas as operações e sempre que uma API no Xamarin. iOS ou Xamarin. Mac exigir uma cadeia de caracteres, nossa associação de API

Por exemplo, a propriedade Objective-C "text" em um `UILabel` do tipo `NSString`, é declarada da seguinte maneira:

```objc
@property(nonatomic, copy) NSString *text
```

Isso é exposto no Xamarin. iOS como:

```csharp
class UILabel {
    public string Text { get; set; }
}
```

Nos bastidores, a implementação dessa propriedade realiza marshaling da C# cadeia de caracteres em um `NSString` e chama o método `objc_msgSend` da mesma forma que o Objective-C.

Há algumas APIs de Objective-C de terceiros que não consomem um `NSString`, mas sim consumir uma cadeia de caracteres C (um "*Char*"). Nesses casos, você ainda pode usar o tipo C# de dados String, mas deve usar o atributo [[plainstring]](~/cross-platform/macios/binding/objective-c-libraries.md) para informar ao gerador de associação que essa cadeia de caracteres não deve ser empacotada como um `NSString`, mas sim como uma cadeia de caracteres C.

 <a name="Exceptions_to_the_Rule" />

## <a name="exceptions-to-the-rule"></a>Exceções à regra

No Xamarin. iOS e no Xamarin. Mac, fizemos uma exceção a essa regra. A decisão entre o momento em que exponhamos `string`s e quando fizermos uma comparação de `NSString`s será feita se o método de  de `NSString`puder estar fazendo uma diferença de ponteiro em vez de uma comparação de conteúdo.

Isso pode acontecer quando uma API Objective-C usa um `NSString`público  constante como um token que representa alguma ação, em vez de comparar o conteúdo real da cadeia de caracteres.

Nesses casos, `NSString` APIs são expostas e há uma minoria de APIs que têm isso. Você também observará que as propriedades NSString são expostas em algumas classes. Essas propriedades de `NSString` são expostas para itens como notificações. Essas são propriedades geralmente semelhantes a:

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

As notificações são chaves usadas para a classe `NSNotification` quando você deseja se registrar para um evento específico sendo transmitido pelo tempo de execução.

As chaves geralmente são parecidas com esta:

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

Outro local em que `NSString`s são expostos na API são os tokens usados como parâmetros para determinadas APIs no iOS ou OS X que usam `NSDictionary` objetos como parâmetros. O dicionário normalmente contém `NSString` chaves. Xamarin. iOS, por convenção, nomeia essas propriedades de `NSString` estáticas adicionando o nome de "chave".
