---
title: Namespace XAML recomendado prefixos no xamarin. Forms
description: A classe XmlnsPrefixAttribute pode ser usada por autores de controle para especificar um prefixo recomendado a ser associado a um namespace XAML, para uso do XAML.
ms.prod: xamarin
ms.assetid: 7B315BEC-7A35-48F4-A9C7-EF40255E95FF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2019
ms.openlocfilehash: 33f18b3f9c9ddb6ab31ca92e2f192ffad783ec0c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075083"
---
# <a name="xaml-namespace-recommended-prefixes-in-xamarinforms"></a>Namespace XAML recomendado prefixos no xamarin. Forms

O `XmlnsPrefixAttribute` classe pode ser usada por autores de controle para especificar um prefixo recomendado a ser associado a um namespace XAML, para uso do XAML. O prefixo é útil quando o suporte à serialização de árvore de objeto para XAML, ou ao interagir com um ambiente de design que tenha recursos de edição de XAML. Por exemplo:

- Editores de texto XAML pode usar o `XmlnsPrefixAttribute` como uma dica para um namespace XAML inicial `xmlns` mapeamento.
- Ambientes de design XAML pode usar o `XmlnsPrefixAttribute` para adição de mapeamentos para o XAML ao arrastar objetos para fora de uma caixa de ferramentas e em uma superfície de design visual.

Recomendado prefixos de namespace devem ser definidos no nível de assembly com o `XmlnsPrefixAttribute` construtor, que leva dois argumentos: uma cadeia de caracteres que especifica o identificador de um namespace XAML e uma cadeia de caracteres que especifica um prefixo recomendado:

```csharp
[assembly: XmlnsPrefix("http://xamarin.com/schemas/2014/forms", "xf")]
```

Prefixos devem usar cadeias de caracteres curtas, porque, normalmente, o prefixo é aplicado a todos os elementos serializados que vêm do namespace XAML. Portanto, o comprimento da cadeia de caracteres de prefixo pode ter um efeito notável no tamanho da saída XAML serializado.

> [!NOTE]
> Mais de um `XmlnsPrefixAttribute` pode ser aplicado a um assembly. Por exemplo, se você tiver um assembly que define os tipos para mais de um namespace XAML, você pode definir valores de prefixo diferente para cada namespace XAML.

## <a name="related-links"></a>Links relacionados

- [Esquemas Personalizados de Namespace de XAML](custom-namespace-schemas.md)
- [Namespaces XAML no xamarin. Forms](namespaces.md)
