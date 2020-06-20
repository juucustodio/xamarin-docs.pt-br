---
title: Prefixos recomendados do namespace XAML emXamarin.Forms
description: A classe XmlnsPrefixAttribute pode ser usada por autores de controle para especificar um prefixo recomendado para associar a um namespace XAML, para uso XAML.
ms.prod: xamarin
ms.assetid: 7B315BEC-7A35-48F4-A9C7-EF40255E95FF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 71ae523f40f3f7529c12f853778404e224fbae30
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138132"
---
# <a name="xaml-namespace-recommended-prefixes-in-xamarinforms"></a>Prefixos recomendados do namespace XAML emXamarin.Forms

A `XmlnsPrefixAttribute` classe pode ser usada por autores de controle para especificar um prefixo recomendado para associar a um namespace XAML, para uso XAML. O prefixo é útil ao dar suporte à serialização da árvore de objetos para XAML ou ao interagir com um ambiente de design que tem recursos de edição XAML. Por exemplo:

- Os editores de texto XAML poderiam usar o `XmlnsPrefixAttribute` como uma dica para um mapeamento de namespace XAML inicial `xmlns` .
- Os ambientes de design XAML poderiam usar o `XmlnsPrefixAttribute` para adicionar mapeamentos ao XAML ao arrastar objetos para fora de uma caixa de ferramentas e para uma superfície de Design Visual.

Os prefixos de namespace recomendados devem ser definidos no nível de assembly com o `XmlnsPrefixAttribute` Construtor, que usa dois argumentos: uma cadeia de caracteres que especifica o identificador de um namespace XAML e uma cadeia de caracteres que especifica um prefixo recomendado:

```csharp
[assembly: XmlnsPrefix("http://xamarin.com/schemas/2014/forms", "xf")]
```

Os prefixos devem usar cadeias de caracteres curtas, porque o prefixo é normalmente aplicado a todos os elementos serializados provenientes do namespace XAML. Portanto, o comprimento da cadeia de caracteres de prefixo pode ter um efeito perceptível no tamanho da saída XAML serializada.

> [!NOTE]
> Mais de um `XmlnsPrefixAttribute` pode ser aplicado a um assembly. Por exemplo, se você tiver um assembly que define tipos para mais de um namespace XAML, poderá definir valores de prefixo diferentes para cada namespace XAML.

## <a name="related-links"></a>Links relacionados

- [Esquemas personalizados de namespace do XAML](custom-namespace-schemas.md)
- [Namespaces XAML emXamarin.Forms](namespaces.md)
