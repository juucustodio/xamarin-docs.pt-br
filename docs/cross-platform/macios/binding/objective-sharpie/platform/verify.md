---
title: Atributos de verificação de nitidez do objetivo
description: Este documento descreve o atributo [Verify] gerado pela nitidez objetiva. O atributo [Verify] realça para os desenvolvedores onde eles devem verificar manualmente a saída de nitidez do objetivo.
ms.prod: xamarin
ms.assetid: 107FBCEA-266B-4295-B7AA-40A881B82B7B
author: conceptdev
ms.author: crdun
ms.date: 01/15/2016
ms.openlocfilehash: eb4a992255fa26be1e83f27f93755e0fd8bda0a4
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765682"
---
# <a name="objective-sharpie-verify-attributes"></a>Atributos de verificação de nitidez do objetivo

Muitas vezes, você descobrirá que as associações produzidas pela nitidez objetiva serão anotadas com `[Verify]` o atributo. Esses atributos indicam que você deve _verificar_ se a nitidez objetiva fez a coisa correta comparando a associação com a declaração c/Objective-c original (que será fornecida em um comentário acima da declaração associada).

A verificação é recomendada para _todas as_ declarações associadas, mas é provavelmente _necessária_ para declarações anotadas com `[Verify]` o atributo. Isso ocorre porque, em muitas situações, não há metadados suficientes no código-fonte nativo original para inferir como produzir melhor uma associação. Talvez seja necessário fazer referência a documentação ou comentários de código dentro dos arquivos de cabeçalho para tomar a melhor decisão de ligação.

Depois de verificar se a associação está correta ou corrigi-la para estar correta, _remova_ o `[Verify]` atributo da associação.

> [!IMPORTANT]
> `[Verify]`os atributos causam C# intencionalmente erros de compilação para que você seja forçado a verificar a associação. Você deve remover o `[Verify]` atributo quando tiver revisado (e, possivelmente, corrigido) o código.

## <a name="verify-hints-reference"></a>Verificar referência de dicas

O argumento de dica fornecido para o atributo pode ser referenciado cruzado com a documentação abaixo. A documentação para todos `[Verify]` os atributos produzidos será fornecida no console também após a conclusão da associação.

|`[Verify]`Hint|Descrição|
|---|---|
|InferredFromPreceedingTypedef|O nome dessa declaração foi inferido por convenção comum do anterior `typedef` imediatamente no código-fonte nativo original. Verifique se o nome inferido está correto, pois essa Convenção é ambígua.|
|ConstantsInterfaceAssociation|Não há nenhuma maneira de ser possível determinar com qual interface Objective-C uma declaração de variável externa pode ser associada. As instâncias desses são associadas como `[Field]` Propriedades em uma interface parcial em uma interface de proximidade e concreta para produzir uma API mais intuitiva, possivelmente eliminando totalmente a interface ' Constants '.|
|MethodToProperty|Um método Objective-C foi associado como C# uma propriedade devido a uma convenção como não usar nenhum parâmetro e retornar um valor (retorno não nulo). Geralmente, métodos como esses devem ser associados como propriedades para trazer uma API mais agradável, mas às vezes os falsos positivos podem ocorrer e a associação deve realmente ser um método.|
|StronglyTypedNSArray|Um nativo `NSArray*` foi associado como `NSObject[]`. Pode ser possível digitar mais fortemente a matriz na associação com base nas expectativas definidas por meio da documentação da API (por exemplo, comentários no arquivo de cabeçalho) ou examinando o conteúdo da matriz por meio de testes. Por exemplo, um NSArray * contendo apenas NSNumber * instancescan ser associado como `NSNumber[]` em vez `NSObject[]`de.|

Você também pode receber rapidamente a documentação para obter uma dica `sharpie verify-docs` usando a ferramenta, por exemplo:

```csharp
sharpie verify-docs InferredFromPreceedingTypedef
```
