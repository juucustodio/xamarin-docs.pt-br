---
title: NSString no Xamarin. iOS e Xamarin. Mac
description: Este documento descreve como o Xamarin. iOS converte de forma transparente objetos NSString em objetos de cadeia de caracteres C#, quando isso não acontece.
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 314c94fc9208a63e2f9305511df262327df921a5
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84565064"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>NSString no Xamarin. iOS e Xamarin. Mac

O design das chamadas Xamarin. iOS e Xamarin. Mac para a API de uso para expor o tipo de cadeia de caracteres .NET nativo, `string` , para manipulação de cadeia de caracteres em C# e outras linguagens de programação .net, e para expor cadeia de caracteres como o tipo de dados exposto pela API em vez do  `NSString`   tipo de dados.

Isso significa que os desenvolvedores não devem manter as cadeias de caracteres destinadas a serem usadas para chamar o Xamarin. iOS & a API do Xamarin. Mac (unificada) em um tipo especial ( `Foundation.NSString` ), eles podem continuar usando `System.String` o mono para todas as operações e sempre que uma API no Xamarin. Ios ou Xamarin. Mac exigir uma cadeia de caracteres, nossa associação de API cuida do Marshal

Por exemplo, a propriedade Objective-C "text" em um `UILabel` do tipo `NSString` é declarada da seguinte maneira:

```objc
@property(nonatomic, copy) NSString *text
```

Isso é exposto no Xamarin. iOS como:

```csharp
class UILabel {
    public string Text { get; set; }
}
```

Nos bastidores, a implementação dessa propriedade realiza marshaling da cadeia de caracteres C# em um `NSString` e chama o `objc_msgSend` método da mesma forma que o Objective-C.

Há algumas APIs de Objective-C de terceiros que não consomem um `NSString` , mas sim consumir uma cadeia de caracteres C (um "*Char*"). Nesses casos, você ainda pode usar o tipo de dados de cadeia de caracteres C#, mas deve usar o atributo [[plainstring]](~/cross-platform/macios/binding/objective-c-libraries.md) para informar ao gerador de associação que essa cadeia de caracteres não deve ser empacotada como um `NSString` , mas sim como uma cadeia de caracteres C.

 <a name="Exceptions_to_the_Rule"></a>

## <a name="exceptions-to-the-rule"></a>Exceções à regra

No Xamarin. iOS e no Xamarin. Mac, fizemos uma exceção a essa regra. A decisão entre quando expõemos  `string` s e quando criamos um, exceto e expõe  `NSString` s, é feita se o  `NSString`   método pudesse fazer uma comparação de ponteiro em vez de uma comparação de conteúdo.

Isso pode acontecer quando uma API Objective-C usa uma  `NSString`   constante pública como um token que representa alguma ação, em vez de comparar o conteúdo real da cadeia de caracteres.

Nesses casos, as `NSString`   APIs são expostas e há uma minoria de APIs que têm isso. Você também observará que as propriedades NSString são expostas em algumas classes. Essas `NSString` Propriedades são expostas para itens como notificações. Essas são propriedades geralmente semelhantes a:

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

As notificações são chaves que são usadas para a `NSNotification` classe quando você deseja se registrar para um evento específico sendo transmitido pelo tempo de execução.

As chaves geralmente são parecidas com esta:

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

Outro lugar em que `NSString` os s são expostos na API são os tokens usados como parâmetros para determinadas APIs no Ios ou os X que usam `NSDictionary` objetos como parâmetros. O dicionário normalmente contém `NSString` chaves. Xamarin. iOS, por convenção, nomeia essas propriedades estáticas `NSString` adicionando o nome "chave".
