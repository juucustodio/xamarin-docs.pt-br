---
title: "4000 Embeddinator práticas recomendadas para ObjC"
ms.topic: article
ms.prod: xamarin
ms.assetid: 932C3F0C-D968-42D1-BB14-D97C73361983
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 0000cbfdd690b8478b52cbcb84230ed878746daa
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="embeddinator-4000-best-practices-for-objc"></a>4000 Embeddinator práticas recomendadas para ObjC

Este é um rascunho e pode não ser em sincronia com os recursos atualmente suportada pela ferramenta. Esperamos que este documento será evoluir separadamente e, eventualmente, corresponde a ferramenta final, ou seja, será sugerimos melhores abordagens de longo prazo - soluções alternativas não imediatas.

Uma grande parte deste documento também se aplica a outras linguagens com suporte. No entanto, todos os fornecido são exemplos em c# e objetivo-C.


# <a name="exposing-a-subset-of-the-managed-code"></a>Expor um subconjunto do código gerenciado

O biblioteca/framework em gerado nativo contém código Objective-C para chamar cada as APIs gerenciadas que são expostas. A API mais você superfície (tornar público) maiores, em seguida, nativo _colar_ biblioteca se tornará.

Pode ser uma boa ideia criar um assembly diferente, menor, para expor apenas as APIs necessárias para o desenvolvedor nativo. Esse fachada também permitirá a você mais controle sobre a visibilidade, erro de nomeação,... verificando o código gerado.


# <a name="exposing-a-chunkier-api"></a>Expor uma API chunkier

Há um preço a pagar para fazer a transição de nativo para gerenciado (e voltar). Como tal, é melhor para expor _robusto em vez verborrágica_ APIs para desenvolvedores nativo, por exemplo,

**Chatty**
```
public class Person {
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

```csharp
// this requires 3 calls / transitions to initialize the instance
Person *p = [[Person alloc] init];
p.firstName = @"Sebastien";
p.lastName = @"Pouliot";
```

**Chunky**
```
public class Person {
    public Person (string firstName, string lastName) {}
}
```

```csharp
// a single call / transition will perform better
Person *p = [[Person alloc] initWithFirstName:@"Sebastien" lastName:@"Pouliot"];
```

Como o número de transições é menor, o desempenho será melhor. Ele também requer menos código seja gerado, para que isso produzirá uma menor biblioteca nativa também.


# <a name="naming"></a>Nomenclatura

Nomeando objetos é um dos dois problemas mais difíceis na ciência da computação, os outros sendo invalidação e desativado por 1 erros de cache. Esperamos que .NET inserindo pode proteger você de todos os nomes.

## <a name="types"></a>Tipos

Objective-C não oferece suporte a namespaces. Em geral, seus tipos são prefixados com um 2 (para a Apple) ou 3 (de 3 partes) caracteres de prefixo, como `UIView` do UIKit o modo de exibição, que indica que a estrutura.

Para tipos .NET ignorar o namespace não é possível que ela pode apresentar nomes duplicados ou confusos. Isso faz com que tipos de .NET existentes muito longos, por exemplo

```csharp
namespace Xamarin.Xml.Configuration {
    public class Reader {}
}
```

deve ser usado como:

```csharp
id reader = [[Xamarin_Xml_Configuration_Reader alloc] init];
```

No entanto, você pode expor novamente o tipo como:

```csharp
public class XAMXmlConfigReader : Xamarin.Xml.Configuration.Reader {}
```

tornando mais amigável para Objective-C para usar, por exemplo:

```csharp
id reader = [[XAMXmlConfigReader alloc] init];
```

## <a name="methods"></a>Métodos

Até mesmo os bons nomes de .NET podem não ser ideais para uma API Objective-C.

Convenções de nomenclatura em Objective-C são diferentes de .NET (minúsculas concatenadas em vez de Pascal, o mais detalhado).
Leia o [diretrizes de codificação para Cocoa](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-BCIGIJJF).

Do ponto de vista do desenvolvedor um Objective-C, um método com um `Get` prefixo implica que você não possui a instância, ou seja, o [obter regra](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-SW1).

Essa regra de nomenclatura não tiver correspondência com o mundo .NET GC; um método do .NET com um `Create` prefixo se comportará identicamente em .NET. No entanto, para os desenvolvedores Objective-C, normalmente significa você possui a instância retornada, ou seja, o [criar regra](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029).

# <a name="exceptions"></a>Exceções

É bastante commont no .NET para usar exceções extensivamente para relatar erros. No entanto, eles são lentas e não bastante idêntico em ObjC. Sempre que possível, você deve ocultá-los do desenvolvedor Objective-C.

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

## <a name="exceptions-inside-init"></a>Exceções dentro de `init*`

No .NET um construtor deve o êxito e retornar um (_Esperamos que_) instância válida ou gere uma exceção.

Por outro lado, Objective-C permite `init*` para retornar `nil` quando uma instância não pode ser criada. Este é um padrão comum, mas não em geral, usado em muitas estruturas da Apple. Em alguns casos outros um `assert` pode ocorrer (e elimine o processo atual).

O gerador de seguem o mesmo `return nil` padrão gerado `init*` métodos. Se uma exceção gerenciada é gerada, ela será impressa (usando `NSLog`) e `nil` será retornado ao chamador.

## <a name="operators"></a>Operadores

ObjC não permite que os operadores para ser sobrecarregado como c#, para que eles são convertidos para seletores de classe.

["Amigável"](https://msdn.microsoft.com/en-us/library/ms229032(v=vs.110).aspx) método nomeado são gerados em vez do operador sobrecargas quando encontrado e pode produzir um mais fáceis de consumir API.

Classes que substituem os operadores = = e/ou! = deve substituir o método Equals (Object) padrão.
