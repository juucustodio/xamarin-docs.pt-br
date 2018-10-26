---
title: .NET incorporação práticas recomendadas para Objective-C
description: Este documento descreve várias práticas recomendadas para usar a incorporação do .NET com o Objective-C. Ele aborda expondo um subconjunto de código gerenciado, expondo uma API chunkier, nomeação e muito mais.
ms.prod: xamarin
ms.assetid: 63C7F5D2-8933-4D4A-8348-E9CBDA45C472
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 33138b7858b8bc04a5be30f9fad1709e916f5575
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105383"
---
# <a name="net-embedding-best-practices-for-objective-c"></a>.NET incorporação práticas recomendadas para Objective-C

Esse é um rascunho e pode não estar em sincronia com os recursos tem suporte pela ferramenta. Esperamos que este documento será evoluam separadamente e, eventualmente, encontre a ferramenta de final, ou seja, iremos sugerir melhores abordagens de longo prazo - soluções alternativas não imediatas.

Uma grande parte deste documento também se aplica a outras linguagens com suporte. No entanto todos fornecidos exemplos estão em C# e Objective-C.

## <a name="exposing-a-subset-of-the-managed-code"></a>Expondo um subconjunto de código gerenciado

A gerado nativa estrutura/biblioteca contém código Objective-C para chamar cada uma das APIs gerenciadas que são expostas. A você de superfície de API mais (tornar público), em seguida, maior nativo _cola_ biblioteca se tornará.

Ele pode ser uma boa ideia criar um assembly diferente, menor, para expor somente as APIs necessárias para o desenvolvedor nativo. Esse fachada também permitirá a você mais controle sobre a visibilidade, nomenclatura, erro ao verificar... o código gerado.

## <a name="exposing-a-chunkier-api"></a>Expor uma API chunkier

Há um preço a pagar para fazer a transição de nativo para gerenciado (e voltar). Como tal, é melhor expor _robusto, em vez de com ruídos_ APIs para desenvolvedores nativos, por exemplo,

**Com ruídos**

```csharp
public class Person {
  public string FirstName { get; set; }
  public string LastName { get; set; }
}
```

```objc
// this requires 3 calls / transitions to initialize the instance
Person *p = [[Person alloc] init];
p.firstName = @"Sebastien";
p.lastName = @"Pouliot";
```

**Chunky**

```csharp
public class Person {
  public Person (string firstName, string lastName) {}
}
```

```objc
// a single call / transition will perform better
Person *p = [[Person alloc] initWithFirstName:@"Sebastien" lastName:@"Pouliot"];
```

Uma vez que o número de transições é menor, o desempenho será melhor. Ele também requer menos código a ser gerado, portanto, isso produzirá uma menor biblioteca nativa também.

## <a name="naming"></a>Nomenclatura

Nomeando itens é um dos dois problemas mais difíceis em ciência da computação, os outros que estão sendo cache erros de invalidação e off-por-1. Espero que incorporação do .NET pode proteger você de tudo, exceto de nomenclatura.

### <a name="types"></a>Tipos

Objective-C não oferece suporte a namespaces. Em geral, seus tipos são prefixados com um 2 (para a Apple) ou 3 (para partes 3ª) caracteres de prefixo, como `UIView` para modo de exibição de UIKit, que denota a estrutura.

Para tipos .NET ignorando o namespace não é possível pois ele pode introduzir nomes duplicados ou confusos. Isso torna a tipos de .NET existentes muito longos, por exemplo

```csharp
namespace Xamarin.Xml.Configuration {
  public class Reader {}
}
```

seria usado como:

```objc
id reader = [[Xamarin_Xml_Configuration_Reader alloc] init];
```

No entanto, você pode expor novamente o tipo como:

```csharp
public class XAMXmlConfigReader : Xamarin.Xml.Configuration.Reader {}
```

tornando mais amigável do Objective-C a ser usado, por exemplo:

```objc
id reader = [[XAMXmlConfigReader alloc] init];
```

### <a name="methods"></a>Métodos

Até mesmo os bons nomes do .NET podem não ser ideais para uma API de Objective-C.

Convenções de nomenclatura em Objective-C são diferentes do .NET (concatenadas em vez do Pascal, o mais detalhado).
Leia as [diretrizes de codificação para Cocoa](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-BCIGIJJF).

Do ponto de vista de um desenvolvedor de Objective-C, um método com um `Get` prefixo implica que você não possui a instância, ou seja, o [obter regra](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-SW1).

Essa regra de nomenclatura não tenha uma correspondência no mundo do .NET GC; um método do .NET com um `Create` prefixo serão têm comportamento idêntico no .NET. No entanto, para os desenvolvedores de Objective-C, ele normalmente significa que você possui a instância retornada, ou seja, o [criar regra](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029).

## <a name="exceptions"></a>Exceções

Ele é bastante comum no .NET para usar exceções extensivamente para relatar erros. No entanto, eles são lentas e não completamente idêntica em Objective-C. Sempre que possível, você deve ocultá-los do desenvolvedor de Objective-C.

Por exemplo, o .NET `Try` padrão será muito mais fácil de consumir do código Objective-C:

```csharp
public int Parse (string number)
{
  return Int32.Parse (number);
}
```

versus

```csharp
public bool TryParse (string number, out int value)
{
  return Int32.TryParse (number, out value);
}
```

### <a name="exceptions-inside-init"></a>Exceções dentro de `init*`

No .NET um construtor deve ser bem-sucedida e retornar um (_espero que_) lançam uma exceção ou instância válida.

Em contraste, o Objective-C permite `init*` para retornar `nil` quando uma instância não pode ser criada. Este é um padrão comum, mas não em geral, usado em muitas estruturas da Apple. Em alguns outros casos um `assert` pode ocorrer (e elimine o processo atual).

O gerador de seguir as mesmas `return nil` padrão gerado `init*` métodos. Se uma exceção gerenciada é gerada, em seguida, ela será impressa (usando `NSLog`) e `nil` será retornado ao chamador.

## <a name="operators"></a>Operadores

Objective-C não permite que operadores seja sobrecarregado como C# funciona, portanto, eles são convertidos em seletores de classe.

["Amigável"](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads) métodos nomeados são gerados em preferência as sobrecargas de operador quando encontrado e pode produzir uma maneira mais fácil consumir API.

Classes que substituem os operadores `==` e/ou `!=` deve substituir o método Equals (Object) padrão.
