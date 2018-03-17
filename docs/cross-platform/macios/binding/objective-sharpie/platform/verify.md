---
title: Verifique se os atributos
ms.topic: article
ms.prod: xamarin
ms.assetid: 107FBCEA-266B-4295-B7AA-40A881B82B7B
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 01/15/2016
ms.openlocfilehash: 10fb2e2824a05954e19f9b483884061b217be683
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="verify-attributes"></a>Verifique se os atributos


Você descobrirá com frequência que associações produzidas por objetivo Sharpie serão anotadas com a `[Verify]` atributo. Esses atributos indicam que você deve _verificar_ que Sharpie objetivo foi a ação correta, comparando a associação com a declaração original do C/Objective-C (que será fornecida em um comentário acima da declaração associada).

Verificação é recomendada para _todos os_ associado declarações, mas é mais provável _necessária_ para declarações anotados com o `[Verify]` atributo. Isso ocorre porque, em muitas situações, não há metadados suficientes no código-fonte nativo original para inferir como melhor produzir uma associação. Talvez seja necessário fazer referência a documentação ou comentários de código em arquivos de cabeçalho para tomar a melhor decisão de associação.

Depois de verificar que a associação é corrigir ou corrigido-o para correto, _remover_ o `[Verify]` atributo da associação.

> [!IMPORTANT]
> `[Verify]` atributos intencionalmente causam erros de compilação do c# para que você deve verificar a associação. Você deve remover o `[Verify]` quando você tiver revisado (e possivelmente corrigido) o código de atributo.

## <a name="verify-hints-reference"></a>Verificar a referência de dicas

O argumento de dica fornecido para o atributo pode ser cruzado referenciada com a documentação a seguir. Documentação para qualquer produzido `[Verify]` atributos serão fornecidos no console também após a associação.

|Verifique se a dica|Descrição|
|---|---|
|InferredFromPreceedingTypedef|O nome dessa declaração foi inferido pela convenção comum dos imediatamente precedentes `typedef` no código nativo de origem original. Verifique se o nome deduzido correto, pois essa convenção é ambígua.|
|ConstantsInterfaceAssociation|Não há nenhuma maneira de prova para determinar com qual interface Objective-C, uma declaração de variável externa pode ser associada. Essas instâncias são associadas como `[Field]` propriedades em uma interface parcial em uma interface perto por, concreto, para produzir uma API mais intuitiva, possivelmente, eliminando as constantes interface completamente.|
|MethodToProperty|Um método Objective-C foram associado como uma propriedade c# devido a convenção de como não fazer nenhum parâmetro e retornar um valor (retorno de void não). Geralmente métodos, como eles devem ser associados como propriedades apresentar uma API melhor, mas, às vezes, os falsos positivos podem ocorrer e a associação, na verdade, deve ser um método.|
|StronglyTypedNSArray|Um nativo `NSArray*` foram associados como `NSObject[]`. Talvez seja possível digitar mais fortemente a matriz na associação com base em expectativas definido por meio da documentação da API (por exemplo, os comentários no arquivo de cabeçalho) ou examinando o conteúdo da matriz por meio de testes. Por exemplo, um NSArray * contendo apenas NSNumber * instancescan ser associado como `NSNumber[]` em vez de `NSObject[]`.|

Você também pode receber documentação para usar uma dica de `sharpie verify-docs` ferramenta, por exemplo:

```csharp
sharpie verify-docs InferredFromPreceedingTypedef
```

