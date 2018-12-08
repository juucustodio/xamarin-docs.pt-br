---
title: Extensões de marcação XAML
description: O artigo explica como usar extensões de marcação de XAML de xamarin. Forms para estender o poder e flexibilidade do XAML, permitindo que os atributos do elemento a ser definido de fontes diferentes cadeias de caracteres de texto literal.
ms.prod: xamarin
ms.assetid: EB06C8B7-3FD5-47B7-A09C-A13063BD110F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: cfdd639672f7fa624c7c8e30f17fbfc9dad403af
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53060155"
---
# <a name="xaml-markup-extensions"></a>Extensões de marcação XAML

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)

Extensões de marcação XAML ajudam a estender o poder e flexibilidade do XAML, permitindo que os atributos do elemento a ser definido de fontes diferentes cadeias de caracteres de texto literal.

Por exemplo, normalmente você defina as `Color` propriedade de `BoxView` semelhante a esta:

```xaml
<BoxView Color="Blue" />
```

Ou, você pode defini-lo como um valor hexadecimal de cor RGB:

```xaml
<BoxView Color="#FF0080" />
```

Em ambos os casos, a cadeia de caracteres de texto é definida como o `Color` atributo será convertido em um `Color` valor pela [ `ColorTypeConverter` ](xref:Xamarin.Forms.ColorTypeConverter) classe.

Talvez você prefira em vez disso, defina as `Color` de um valor armazenado em um dicionário de recurso, ou o valor de uma propriedade estática de uma classe que você criou ou uma propriedade de tipo de atributo `Color` de outro elemento na página, ou construídos a partir de Separe os valores de matiz, saturação e luminosidade.

Todas essas opções são possíveis usando extensões de marcação XAML. Mas não deixe que a frase "extensões de marcação" assustam você: extensões de marcação XAML são *não* extensões para XML. Mesmo com extensões de marcação XAML, o XAML é sempre XML legal.

Uma extensão de marcação é realmente apenas uma maneira diferente para expressar um atributo de um elemento. Extensões de marcação XAML são geralmente identificadas por uma configuração de atributo que é colocada entre chaves:

```xaml
<BoxView Color="{StaticResource themeColor}" />
```

Qualquer configuração de atributo entre chaves é *sempre* uma extensão de marcação XAML. No entanto, como você verá, extensões de marcação XAML também podem ser referenciadas sem o uso de chaves.

Este artigo está dividido em duas partes:

## <a name="consuming-xaml-markup-extensionsconsumingmd"></a>[Consumo de extensões de marcação XAML](consuming.md)  

Use as extensões de marcação XAML definidas no xamarin. Forms.

## <a name="creating-xaml-markup-extensionscreatingmd"></a>[Criação de extensões de marcação XAML](creating.md)

Escreva suas próprias extensões de marcação XAML personalizados.



## <a name="related-links"></a>Links relacionados

- [Extensões de marcação (amostra)](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [Capítulo de extensões de marcação XAML do xamarin. Forms book](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Dicionários de recurso](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilos dinâmicos](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md)
