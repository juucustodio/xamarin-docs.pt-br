---
title: Extensões de marcação do XAML
description: O artigo explica como usar Xamarin.Forms extensões de marcação XAML para estender a potência e a flexibilidade do XAML, permitindo que os atributos do elemento sejam definidos de fontes diferentes de cadeias de caracteres de texto literal.
ms.prod: xamarin
ms.assetid: EB06C8B7-3FD5-47B7-A09C-A13063BD110F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: da88337778f31378d257894c3428ad89889673d3
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368941"
---
# <a name="xaml-markup-extensions"></a>Extensões de marcação do XAML

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

As extensões de marcação XAML ajudam a ampliar o poder e a flexibilidade do XAML, permitindo que os atributos de elemento sejam definidos de fontes diferentes de cadeias de caracteres de texto literal.

Por exemplo, normalmente você define a `Color` propriedade de `BoxView` como esta:

```xaml
<BoxView Color="Blue" />
```

Ou você pode defini-lo como um valor de cor RGB hexadecimal:

```xaml
<BoxView Color="#FF0080" />
```

Em ambos os casos, a cadeia de caracteres de texto definida como o `Color` atributo é convertida em um `Color` valor pela [`ColorTypeConverter`](xref:Xamarin.Forms.ColorTypeConverter) classe.

Em vez disso, você pode preferir definir o `Color` atributo de um valor armazenado em um dicionário de recursos ou do valor de uma propriedade estática de uma classe que você criou, ou de uma propriedade do tipo `Color` de outro elemento na página, ou construídos a partir de valores separados de matiz, saturação e luminosidade.

Todas essas opções são possíveis usando extensões de marcação XAML. Mas não deixe a frase "extensões de marcação" assustam você: as extensões de marcação XAML *não* são extensões para XML. Mesmo com extensões de marcação XAML, o XAML é sempre XML válido.

Uma extensão de marcação é, na verdade, apenas uma maneira diferente de expressar um atributo de um elemento. Extensões de marcação XAML geralmente são identificáveis por uma configuração de atributo que está entre chaves:

```xaml
<BoxView Color="{StaticResource themeColor}" />
```

Qualquer configuração de atributo entre chaves é *sempre* uma extensão de marcação XAML. No entanto, como você verá, as extensões de marcação XAML também podem ser referenciadas sem o uso de chaves.

Este artigo é dividido em duas partes:

## <a name="consuming-xaml-markup-extensions"></a>[Consumo de extensões de marcação do XAML](consuming.md)  

Use as extensões de marcação XAML definidas em Xamarin.Forms .

## <a name="creating-xaml-markup-extensions"></a>[Criação de extensões de marcação do XAML](creating.md)

Escreva suas próprias extensões de marcação XAML personalizadas.

## <a name="related-links"></a>Links Relacionados

- [Extensões de marcação (exemplo)](/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Capítulo de extensões de marcação XAML do Xamarin.Forms livro](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Dicionários de recurso](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilos dinâmicos](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md)