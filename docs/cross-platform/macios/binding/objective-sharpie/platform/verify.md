---
title: Objetivo Sharpie verificar atributos
description: Este documento descreve o atributo [verificar] gerado pelo Sharpie objetivo. O atributo [verificar] destaca aos desenvolvedores onde eles devem verificar saída do objetivo Sharpie manualmente.
ms.prod: xamarin
ms.assetid: 107FBCEA-266B-4295-B7AA-40A881B82B7B
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: 96e5bafc14c2d3aba03ccc137151a83ee8afeef9
ms.sourcegitcommit: bf18425f97b48661ab6b775195eac76b356eeba0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64977858"
---
# <a name="objective-sharpie-verify-attributes"></a>Objetivo Sharpie verificar atributos

Você descobrirá com frequência que associações produzidas pelo Sharpie objetivo serão ser anotadas com o `[Verify]` atributo. Esses atributos indicam que você deva _verificar_ que o objetivo Sharpie fez a coisa correta, comparando a ligação com a declaração do C/Objective-C original (que será fornecida em um comentário acima da declaração associada).

Verificação é recomendada para _todos os_ associado declarações, mas é mais provável _necessária_ para declarações anotados com o `[Verify]` atributo. Isso ocorre porque, em muitas situações, não há metadados suficientes no código nativo de origem original para inferir como melhor produzir uma associação. Você talvez precise fazer referência a documentação ou comentários de código dentro dos arquivos de cabeçalho para tomar a melhor decisão de associação.

Uma vez que você verificou que a associação é corrigir ou corrigiram-o para correto, _remova_ o `[Verify]` atributo da associação.

> [!IMPORTANT]
> `[Verify]` atributos causam intencionalmente C# erros de compilação para que você é forçado para verificar a associação. Você deve remover o `[Verify]` quando você tiver revisado (e possivelmente corrigido) o código do atributo.

## <a name="verify-hints-reference"></a>Verificar a referência de dicas

O argumento de dica fornecido para o atributo pode ser cruzado referenciada com a documentação a seguir. Documentação para qualquer produzido `[Verify]` atributos serão fornecidos no console também após a associação de dados.

|`[Verify]` Dica|Descrição|
|---|---|
|InferredFromPreceedingTypedef|O nome dessa declaração foi inferido pela convenção comum dos imediatamente anterior `typedef` no código nativo de origem original. Verifique se o nome deduzido está correto, como essa convenção é ambígua.|
|ConstantsInterfaceAssociation|Não há nenhuma maneira de prova para determinar com qual interface Objective-C, uma declaração de variável externa pode ser associada. Essas instâncias são associadas como `[Field]` propriedades em uma interface parcial em uma interface quase-by, concreto, para produzir uma API mais intuitiva, possivelmente eliminando as constantes de interface completamente.|
|MethodToProperty|Um método de Objective-C foram associado como uma C# propriedade devido a convenção como pegar sem parâmetros e retornar um valor (retorno não nulo). Geralmente os métodos como esses devem ser associados como propriedades à tona uma API mais agradável, mas, às vezes, os falsos positivos podem ocorrer e a associação, na verdade, deve ser um método.|
|StronglyTypedNSArray|Um nativo `NSArray*` foram associados como `NSObject[]`. Pode ser possível para o tipo mais forte a matriz na associação com base nas expectativas definidas por meio de documentação da API (por exemplo, os comentários no arquivo de cabeçalho) ou examinando o conteúdo da matriz por meio de testes. Por exemplo, um NSArray * contendo apenas NSNumber * instancescan ser associado como `NSNumber[]` em vez de `NSObject[]`.|

Você pode receber também rapidamente a documentação para usar uma dica de `sharpie verify-docs` ferramenta, por exemplo:

```csharp
sharpie verify-docs InferredFromPreceedingTypedef
```

