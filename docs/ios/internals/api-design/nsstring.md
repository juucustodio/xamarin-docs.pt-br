---
title: NSString no xamarin. IOS e xamarin. Mac
description: Este documento descreve como o xamarin. IOS transparentemente converte objetos NSString para C# objetos, de cadeia de caracteres quando isso não acontece.
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: cc9e3f992642f3cdc3d16fe6f829b6a6c06b50fc
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61277808"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>NSString no xamarin. IOS e xamarin. Mac

O design do xamarin. IOS e xamarin. Mac chama a API de uso a fim de expor do tipo de cadeia de caracteres .NET nativo, `string`, para manipulação de cadeia de caracteres em C# e outras linguagens de programação de .NET e expor a cadeia de caracteres como o tipo de dados exposto pela API em vez de o `NSString` tipo de dados.

Isso significa que os desenvolvedores não devem ter que manter as cadeias de caracteres que se destinam a ser usado para chamar a API de xamarin. MAC (unificado) & de xamarin. IOS em um tipo especial (`Foundation.NSString`), eles podem continuar usando o Mono `System.String` para todas as operações e em qualquer lugar uma API no xamarin. IOS ou xamarin. Mac requer uma cadeia de caracteres, nossa ligação de API se encarrega das informações de marshaling.

Por exemplo, a propriedade de "text" Objective-C em uma `UILabel` do tipo `NSString`, é declarado como este:

```objc
@property(nonatomic, copy) NSString *text
```

Isso é exposto no xamarin. IOS como:

```csharp
class UILabel {
    public string Text { get; set; }
}
```

Nos bastidores, a implementação dessa propriedade realiza marshaling de C# de cadeia de caracteres em uma `NSString` e chama o `objc_msgSend` método da mesma maneira que faria de Objective-C.

Há uma série de APIs do Objective-C de terceiros que não consomem uma `NSString`, mas em vez disso, consumir uma cadeia de caracteres do C (uma "*char*"). Nesses casos, você ainda pode usar o C# tipo de dados de cadeia de caracteres, mas você deve usar o [[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md) atributo para informar o gerador de associação que essa cadeia de caracteres não deve ser empacotada como um `NSString`, mas sim como uma cadeia de caracteres do C.

 <a name="Exceptions_to_the_Rule" />

## <a name="exceptions-to-the-rule"></a>Exceções à regra

No xamarin. IOS e xamarin. Mac, fizemos uma exceção a essa regra. A decisão entre quando expomos `string`s, e quando fazemos um, exceto e expor `NSString`s, será feita se o `NSString` método poderia estar fazendo uma comparação de ponteiro em vez de uma comparação de conteúdo.

Isso pode acontecer quando um APIs do Objective-C usa uma pública `NSString` constante como um token que representa alguma ação, em vez de comparar o conteúdo real da cadeia de caracteres.

Nesses casos, `NSString`  APIs são expostas, e há uma minoria dos APIs com este. Você também observará que NSString propriedades são expostas em algumas classes. Aqueles `NSString` propriedades são expostas para itens, como as notificações. Essas são as propriedades geralmente ter esta aparência:

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```
As notificações são chaves que são usadas para o `NSNotification` classe quando você deseja registrar um evento específico que estão sendo transmitidas pelo tempo de execução.

Chaves geralmente esta aparência:

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

Outro lugar em que `NSString`s são expostas na API é como os tokens que são usados como parâmetros para determinadas APIs no iOS ou OS X que usam `NSDictionary` objetos como parâmetros. O dicionário contém normalmente `NSString` chaves. Xamarin. IOS, por convenção, nomes aqueles estático `NSString` propriedades adicionando o nome de "Chave".
