---
title: NSString no Xamarin. iOS e Xamarin. Mac
description: Este documento descreve como o Xamarin. iOS converte de forma transparente objetos NSString C# em objetos de cadeia de caracteres, quando isso não acontece.
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 589b584e0526ffdc56dd5ec26aa25a0b61e2141a
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69889896"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>NSString no Xamarin. iOS e Xamarin. Mac

O design das chamadas xamarin. Ios e xamarin. Mac para a API de uso para expor o tipo de cadeia de caracteres .NET `string`nativo,, para manipulação C# de cadeia de caracteres no e outras linguagens de programação .net, e para expor cadeia de caracteres como o tipo de dados exposto pela API em vez de o `NSString`tipo de dados.

Isso significa que os desenvolvedores não devem manter as cadeias de caracteres destinadas a serem usadas para chamar o xamarin. Ios & API xamarin. Mac (unificada)`Foundation.NSString`em um tipo especial (), elas `System.String` podem continuar usando o mono para todas as operações e sempre que uma API no Xamarin. iOS ou Xamarin. Mac requer uma cadeia de caracteres, nossa associação de API cuida do marshaling das informações.

Por exemplo, a propriedade Objective-C "text" em `UILabel` um do `NSString`tipo é declarada da seguinte maneira:

```objc
@property(nonatomic, copy) NSString *text
```

Isso é exposto no Xamarin. iOS como:

```csharp
class UILabel {
    public string Text { get; set; }
}
```

Nos bastidores, a implementação dessa propriedade realiza marshaling da C# cadeia de caracteres `NSString` em um e `objc_msgSend` chama o método da mesma forma que o Objective-C.

Há algumas APIs de Objective-C de terceiros que não consomem um `NSString`, mas sim consumir uma cadeia de caracteres C (um "*Char*"). Nesses casos, você ainda pode usar o tipo C# de dados String, mas deve usar o atributo [[plainstring]](~/cross-platform/macios/binding/objective-c-libraries.md) para informar ao gerador de associação que essa cadeia de caracteres não deve ser empacotada `NSString`como um, mas sim como uma cadeia de caracteres C.

 <a name="Exceptions_to_the_Rule" />

## <a name="exceptions-to-the-rule"></a>Exceções à regra

No Xamarin. iOS e no Xamarin. Mac, fizemos uma exceção a essa regra. A decisão entre quando expõemos `string`s e quando criamos um, exceto e expõe `NSString`s, é feita se o `NSString` método pudesse fazer uma comparação de ponteiro em vez de uma comparação de conteúdo.

Isso pode acontecer quando uma API Objective-C usa uma `NSString`constante pública como um token que representa alguma ação, em vez de comparar o conteúdo real da cadeia de caracteres.

Nesses casos, `NSString`  as APIs são expostas e há uma minoria de APIs que têm isso. Você também observará que as propriedades NSString são expostas em algumas classes. Essas `NSString` propriedades são expostas para itens como notificações. Essas são propriedades geralmente semelhantes a:

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

As notificações são chaves que são usadas para `NSNotification` a classe quando você deseja se registrar para um evento específico sendo transmitido pelo tempo de execução.

As chaves geralmente são parecidas com esta:

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

Outro lugar em `NSString`que os s são expostos na API são os tokens usados como parâmetros para determinadas APIs no Ios ou os X que usam `NSDictionary` objetos como parâmetros. O dicionário normalmente contém `NSString` chaves. Xamarin. Ios, por convenção, nomeia essas propriedades `NSString` estáticas adicionando o nome "chave".
