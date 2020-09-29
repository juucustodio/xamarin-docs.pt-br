---
title: Práticas recomendadas de incorporação .NET para Objective-C
description: Este documento descreve várias práticas recomendadas para usar a inserção .NET com o Objective-C. Ele aborda a exposição de um subconjunto do código gerenciado, expondo uma API chunkier, nomeando e muito mais.
ms.prod: xamarin
ms.assetid: 63C7F5D2-8933-4D4A-8348-E9CBDA45C472
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: 7eccc83a28d0bac7b9ff15a46022942c7238c714
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457387"
---
# <a name="net-embedding-best-practices-for-objective-c"></a>Práticas recomendadas de incorporação .NET para Objective-C

Este é um rascunho e pode não estar em sincronia com os recursos atualmente compatíveis com a ferramenta. Esperamos que este documento se desenvolva separadamente e, eventualmente, corresponda à ferramenta final, ou seja, sugeriremos abordagens de longo prazo, não soluções alternativas imediatas.

Uma grande parte deste documento também se aplica a outros idiomas com suporte. No entanto, todos os exemplos fornecidos estão em C# e Objective-C.

## <a name="exposing-a-subset-of-the-managed-code"></a>Expondo um subconjunto do código gerenciado

A biblioteca/estrutura nativa gerada contém o código Objective-C para chamar cada uma das APIs gerenciadas que é exposta. Quanto mais API você Surface (tornar pública), maior será a biblioteca de _cola_ nativa.

Pode ser uma boa ideia criar um assembly diferente, menor, para expor apenas as APIs necessárias para o desenvolvedor nativo. Essa fachada também lhe permitirá mais controle sobre a visibilidade, a nomenclatura e a verificação de erros... do código gerado.

## <a name="exposing-a-chunkier-api"></a>Expondo uma API chunkier

Há um preço para pagar para fazer a transição de nativo para gerenciado (e para trás). Como tal, é melhor expor _partes em vez de_ APIs informais aos desenvolvedores nativos, por exemplo,

**Informais**

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

**Robusto**

```csharp
public class Person {
  public Person (string firstName, string lastName) {}
}
```

```objc
// a single call / transition will perform better
Person *p = [[Person alloc] initWithFirstName:@"Sebastien" lastName:@"Pouliot"];
```

Como o número de transições é menor, o desempenho será melhor. Ele também requer menos código a ser gerado, portanto, isso produzirá uma biblioteca nativa menor também.

## <a name="naming"></a>Nomenclatura

A nomenclatura de coisas é um dos dois problemas mais difíceis na ciência da computação, as outras que estão armazenando em cache a invalidação e erros fora de 1. Espero que a incorporação do .NET possa protegê-lo de tudo, mas de nomear.

### <a name="types"></a>Tipos

Objective-C não oferece suporte a namespaces. Em geral, seus tipos são prefixados com um prefixo de caractere 2 (para Apple) ou 3 (para terceiros), como `UIView` para a exibição de UIKit, que denota a estrutura.

Para tipos .NET, ignorar o namespace não é possível, pois ele pode introduzir nomes duplicados ou confusos. Isso torna os tipos .NET existentes muito longos, por exemplo,

```csharp
namespace Xamarin.Xml.Configuration {
  public class Reader {}
}
```

seria usado como:

```objc
id reader = [[Xamarin_Xml_Configuration_Reader alloc] init];
```

No entanto, você pode reexpor o tipo como:

```csharp
public class XAMXmlConfigReader : Xamarin.Xml.Configuration.Reader {}
```

tornando mais objetiva-C mais amigável para uso, por exemplo:

```objc
id reader = [[XAMXmlConfigReader alloc] init];
```

### <a name="methods"></a>Métodos

Mesmo bons nomes do .NET podem não ser ideais para uma API Objective-C.

As convenções de nomenclatura em Objective-C são diferentes do .NET (em vez do camel case, mais detalhado).
Leia as [diretrizes de codificação para Cocoa](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-BCIGIJJF).

Do ponto de vista de um desenvolvedor Objective-C, um método com um `Get` prefixo implica que você não possui a instância, ou seja, a [regra Get](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-SW1).

Esta regra de nomenclatura não tem nenhuma correspondência no mundo do .NET GC; um método .NET com um `Create` prefixo se comportará de forma idêntica no .net. No entanto, para desenvolvedores de Objective-C, normalmente significa que você possui a instância retornada, ou seja, a [regra de criação](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029).

## <a name="exceptions"></a>Exceções

É muito comum que o .NET use exceções extensivamente para relatar erros. No entanto, eles são lentos e não são muito idênticos em Objective-C. Sempre que possível, você deve ocultá-los do desenvolvedor Objective-C.

Por exemplo, o `Try` padrão .net será muito mais fácil de consumir do código Objective-C:

```csharp
public int Parse (string number)
{
  return Int32.Parse (number);
}
```

vez

```csharp
public bool TryParse (string number, out int value)
{
  return Int32.TryParse (number, out value);
}
```

### <a name="exceptions-inside-init"></a>Exceções dentro de `init*`

No .NET, um construtor deve ser bem sucedido e retornar uma instância válida (_espero_) ou gerar uma exceção.

Por outro lado, o Objective-C permite `init*` retornar `nil` quando uma instância não pode ser criada. Esse é um padrão comum, mas não geral, usado em muitas das estruturas da Apple. Em alguns outros casos, um `assert` pode acontecer (e eliminar o processo atual).

O gerador segue o mesmo `return nil` padrão para `init*` métodos gerados. Se uma exceção gerenciada for lançada, ela será impressa (usando `NSLog` ) e `nil` será retornada ao chamador.

## <a name="operators"></a>Operadores

O Objective-C não permite que os operadores sejam sobrecarregados, pois o C# faz, portanto, eles são convertidos em seletores de classe.

Os métodos nomeados ["amigáveis"](/dotnet/standard/design-guidelines/operator-overloads) são gerados em preferência às sobrecargas de operador quando encontradas e podem produzir uma API mais fácil de consumir.

As classes que substituem os operadores `==` e/ou `!=` devem substituir o método padrão Equals (Object) também.