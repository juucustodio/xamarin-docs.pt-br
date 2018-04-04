---
title: Extensões de marcação XAML
description: Estenda o intervalo de fontes do qual XAML atributos são definidos
ms.prod: xamarin
ms.assetid: EB06C8B7-3FD5-47B7-A09C-A13063BD110F
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/05/2018
ms.openlocfilehash: b81bc4b31edd1d8b8f5f43f97885c38e889dd32c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="xaml-markup-extensions"></a>Extensões de marcação XAML

Extensões de marcação XAML ajudam a estender o poder e flexibilidade de XAML, permitindo que os atributos do elemento a ser definido de fontes diferentes cadeias de caracteres de texto literal.

Por exemplo, normalmente você definir o `Color` propriedade `BoxView` esta aparência:

```xaml
<BoxView Color="Blue" />
```

Ou, você pode configurá-lo para um valor de cor RGB hexadecimal:

```xaml
<BoxView Color="#FF0080" />
```

Em ambos os casos, a cadeia de caracteres de texto é definido como o `Color` atributo é convertido em um `Color` valor pelo [ `ColorTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ColorTypeConverter/) classe.

Talvez você prefira em vez disso definir o `Color` de um valor armazenado em um dicionário de recurso, ou o valor de uma propriedade estática de uma classe que você criou ou uma propriedade de tipo de atributo `Color` de outro elemento na página, ou construído a partir de Separe os valores de matiz, saturação e luminosidade.

Todas essas opções são possíveis usando extensões de marcação XAML. Mas não deixe a frase "extensões de marcação" assustar você: extensões de marcação XAML são *não* extensões para XML. Mesmo com extensões de marcação XAML, XAML é sempre XML válido. 

Uma extensão de marcação é apenas uma maneira diferente de expressar um atributo de um elemento. Extensões de marcação XAML são normalmente identificadas por uma configuração de atributo que é colocada entre chaves:

```xaml
<BoxView Color="{StaticResource themeColor}" />
```

Qualquer configuração de atributo entre chaves é *sempre* uma extensão de marcação XAML. No entanto, como você verá, extensões de marcação XAML podem ser referenciadas sem o uso de chaves.

Este artigo é dividido em duas partes:

## <a name="consuming-xaml-markup-extensionsconsumingmd"></a>[Consumo de extensões de marcação XAML](consuming.md)  

Use as extensões de marcação XAML definidas em xamarin. Forms.

## <a name="creating-xaml-markup-extensionscreatingmd"></a>[Criação de extensões de marcação XAML](creating.md) 

Escreva suas próprias extensões de marcação XAML personalizados.



## <a name="related-links"></a>Links relacionados

- [Extensões de marcação (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [Capítulo de extensões de marcação XAML do catálogo xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Dicionários de recurso](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Estilos dinâmicos](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Associação de dados](~/xamarin-forms/app-fundamentals/data-binding/index.md)
