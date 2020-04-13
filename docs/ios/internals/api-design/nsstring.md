---
title: NSString em Xamarin.iOS e Xamarin.Mac
description: Este documento descreve como o Xamarin.iOS converte de forma transparente objetos NSString em objetos de seqüência C#, quando isso não acontece.
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: f744f4ed5619e4e7f4a9d85897c4451bf7e5b9bc
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73022354"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>NSString em Xamarin.iOS e Xamarin.Mac

O design de Xamarin.iOS e Xamarin.Mac exige o uso da API `string`para expor o tipo de string nativo .NET, para manipulação de strings em C# `NSString` e outras linguagens de programação .NET, e para expor string como o tipo de dados exposto pela API em vez do tipo de dados.

Isso significa que os desenvolvedores não devem ter que manter strings que se destinam a ser usadas para chamar o`Foundation.NSString`Xamarin.iOS & `System.String` API Xamarin.Mac (Unificado) em um tipo especial (), eles podem continuar usando Mono's para todas as operações, e sempre que uma API em Xamarin.iOS ou Xamarin.Mac exigir uma seqüência, nossa vinculação de API cuida de empacotar as informações.

Por exemplo, a propriedade Objective-C `UILabel` "text" em um tipo, `NSString`é declarada assim:

```objc
@property(nonatomic, copy) NSString *text
```

Isso é exposto em Xamarin.iOS como:

```csharp
class UILabel {
    public string Text { get; set; }
}
```

Nos bastidores, a implementação desta propriedade empacota a seqüência C# em um `NSString` e chama o `objc_msgSend` método da mesma forma que Objective-C faria.

Existem um punhado de APIs Objetivas-C de `NSString`terceiros que não consomem uma , mas em vez disso consomem uma corda C (um "*char*"). Nesses casos, você ainda pode usar o tipo de dados da seqüência c#, mas você deve usar o `NSString`atributo [[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md) para informar o gerador de vinculação que esta seqüência não deve ser empacotada como uma string, mas sim como uma seqüência C.

 <a name="Exceptions_to_the_Rule" />

## <a name="exceptions-to-the-rule"></a>Exceções à Regra

Tanto no Xamarin.iOS quanto no Xamarin.Mac, fizemos uma exceção a essa regra. A decisão entre `string`quando expões s, e `NSString`quando fazemos uma `NSString` exceção e expões s, é tomada se o método poderia estar fazendo uma comparação de ponteiro em vez de uma comparação de conteúdo.

Isso pode acontecer quando uma APIs `NSString` Objective-C usa uma constante pública como um token que representa alguma ação, em vez de comparar o conteúdo real da string.

Nesses casos, `NSString`  as APIs são expostas, e há uma minoria de APIs que têm isso. Você também notará que as propriedades NSString estão expostas em algumas classes. Essas `NSString` propriedades são expostas para itens como notificações. Essas são propriedades geralmente são assim:

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

Notificações são chaves usadas `NSNotification` para a classe quando você deseja se registrar para um evento específico sendo transmitido pelo tempo de execução.

As chaves geralmente se parecem com isso:

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

Outro lugar `NSString`onde s são expostos na API são os tokens que são usados `NSDictionary` como parâmetros para certas APIs no iOS ou OS X que tomam objetos como parâmetros. O dicionário normalmente contém `NSString` chaves. Xamarin.iOS, por convenção, `NSString` nomeia essas propriedades estáticas adicionando o nome "Chave".
