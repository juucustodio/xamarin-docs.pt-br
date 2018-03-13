---
title: NSString
ms.topic: article
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 87cc1a95f250069310941e051dabe9ea588b4709
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2018
---
# <a name="nsstring"></a>NSString

O design do xamarin e Xamarin.Mac chama a API de uso para expor do tipo de cadeia de caracteres .NET native, `string`, para manipulação de cadeia de caracteres em c# e outras linguagens de programação .NET e para expor a cadeia de caracteres como o tipo de dados exposto pela API em vez de `NSString` tipo de dados.


Isso significa que os desenvolvedores não devem ter que manter as cadeias de caracteres que se destinam a ser usado para chamar xamarin & Xamarin.Mac API (Unified) em um tipo especial (`Foundation.NSString`), eles podem continuar usando do Mono `System.String` para todas as operações e sempre que uma API em xamarin ou Xamarin.Mac requer uma cadeia de caracteres, nossa associação API cuida das informações de marshaling.

Por exemplo, a propriedade de "texto" Objective-C em um `UILabel` do tipo `NSString`, está declarado como este:

```csharp
@property(nonatomic, copy) NSString *text
```

Isso é exposto no xamarin como:

```csharp
class UILabel {
    public string Text { get; set; }
}
```

Nos bastidores, a implementação dessa propriedade controla a cadeia de caracteres c# em um `NSString` e chama o `objc_msgSend` método da mesma maneira que faria Objective-C.

Há uma série de APIs de Objective-C de terceiros que não consomem uma `NSString`, mas em vez disso, consomem uma cadeia de caracteres C (uma "*char*"). Nesses casos, você ainda pode usar o tipo de dados de cadeia de caracteres C#, mas você deve usar o [[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md) atributo para informar o gerador de associação que essa cadeia de caracteres não deve ser empacotada como um `NSString`, mas como uma cadeia de caracteres C.

 <a name="Exceptions_to_the_Rule" />


## <a name="exceptions-to-the-rule"></a>Exceções à regra

No xamarin e Xamarin.Mac, fizemos uma exceção a essa regra. A decisão entre quando expomos `string`s, e quando fazemos um exceto e expor `NSString`s, será feita se a `NSString` método pode fazer uma comparação de ponteiro em vez de uma comparação de conteúdo.


Isso pode acontecer quando um APIs Objective-C usa um público `NSString` constante como um token que representa alguma ação, em vez de comparar o conteúdo real da cadeia de caracteres.


Nesses casos, `NSString` APIs são expostas, e há uma minoria dos APIs com este. Você observará que NSString propriedades são expostas em algumas classes. Os `NSString` propriedades são expostas para itens, como notificações. Essas são propriedades geralmente ter esta aparência:

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

As notificações são chaves que são usadas para o `NSNotification` classe quando você deseja registrar para um determinado evento seja transmitido pelo tempo de execução.

Chaves geralmente algo parecem com isto:

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

Outro lugar onde `NSString`s são expostos na API é como os tokens são usados como parâmetros para determinadas APIs no iOS ou OS X que usam `NSDictionary` objetos como parâmetros. O dicionário contém normalmente `NSString` chaves. Xamarin, por convenção, nomes estáticos aqueles `NSString` propriedades adicionando o nome de "Chave".
