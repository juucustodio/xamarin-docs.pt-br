---
title: Atributos de verificação de nitidez do objetivo
description: Este documento descreve o atributo [Verify] gerado pela nitidez objetiva. O atributo [Verify] realça para os desenvolvedores onde eles devem verificar manualmente a saída de nitidez do objetivo.
ms.prod: xamarin
ms.assetid: 107FBCEA-266B-4295-B7AA-40A881B82B7B
author: davidortinau
ms.author: daortin
ms.date: 01/15/2016
ms.openlocfilehash: 6fffad744fa2f60239b0c96f01ff241e2cad9252
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016070"
---
# <a name="objective-sharpie-verify-attributes"></a>Atributos de verificação de nitidez do objetivo

Muitas vezes, você descobrirá que as associações produzidas pela nitidez objetiva serão anotadas com o atributo `[Verify]`. Esses atributos indicam que você deve _verificar_ se a nitidez objetiva fez a coisa correta comparando a associação com a declaração c/Objective-c original (que será fornecida em um comentário acima da declaração associada).

A verificação é recomendada para _todas as_ declarações associadas, mas é provavelmente _necessária_ para declarações anotadas com o atributo `[Verify]`. Isso ocorre porque, em muitas situações, não há metadados suficientes no código-fonte nativo original para inferir como produzir melhor uma associação. Talvez seja necessário fazer referência a documentação ou comentários de código dentro dos arquivos de cabeçalho para tomar a melhor decisão de ligação.

Depois de verificar se a associação está correta ou corrigi-la para estar correta, _remova_ o atributo `[Verify]` da associação.

> [!IMPORTANT]
> os atributos de `[Verify]` causam C# intencionalmente erros de compilação para que você seja forçado a verificar a associação. Você deve remover o atributo `[Verify]` quando tiver revisado (e possivelmente corrigido) o código.

## <a name="verify-hints-reference"></a>Verificar referência de dicas

O argumento de dica fornecido para o atributo pode ser referenciado cruzado com a documentação abaixo. A documentação para todos os atributos de `[Verify]` produzidos será fornecida no console também após a conclusão da associação.

|Dica de `[Verify]`|Descrição|
|---|---|
|InferredFromPreceedingTypedef|O nome dessa declaração foi inferido por uma convenção comum do `typedef` imediatamente anterior no código-fonte nativo original. Verifique se o nome inferido está correto, pois essa Convenção é ambígua.|
|ConstantsInterfaceAssociation|Não há nenhuma maneira de ser possível determinar com qual interface Objective-C uma declaração de variável externa pode ser associada. As instâncias desses são vinculadas como `[Field]` Propriedades em uma interface parcial em uma interface curta por concreto para produzir uma API mais intuitiva, possivelmente eliminando totalmente a interface ' Constants '.|
|MethodToProperty|Um método Objective-C foi associado como C# uma propriedade devido a uma convenção como não usar nenhum parâmetro e retornar um valor (retorno não nulo). Geralmente, métodos como esses devem ser associados como propriedades para trazer uma API mais agradável, mas às vezes os falsos positivos podem ocorrer e a associação deve realmente ser um método.|
|StronglyTypedNSArray|Um `NSArray*` nativo foi associado como `NSObject[]`. Pode ser possível digitar mais fortemente a matriz na associação com base nas expectativas definidas por meio da documentação da API (por exemplo, comentários no arquivo de cabeçalho) ou examinando o conteúdo da matriz por meio de testes. Por exemplo, um NSArray * contendo apenas NSNumber * instancescan ser associado como `NSNumber[]` em vez de `NSObject[]`.|

Você também pode receber rapidamente a documentação para obter uma dica usando a ferramenta `sharpie verify-docs`, por exemplo:

```csharp
sharpie verify-docs InferredFromPreceedingTypedef
```
