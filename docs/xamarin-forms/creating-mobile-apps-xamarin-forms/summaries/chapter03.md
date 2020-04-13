---
title: Resumo do Capítulo 3. Aprofundamento no texto
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 3. Aprofundamento no texto'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2E5581A6-4D3E-4BD5-9FDB-ACBA0F0FC734
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 69415b59bbd376330454302981e3216c236a16bb
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "72980929"
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>Resumo do Capítulo 3. Aprofundamento no texto

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)

Este capítulo explora [`Label`](xref:Xamarin.Forms.Label) a exibição com mais profundidade, incluindo cor, fontes e formatação.

## <a name="wrapping-paragraphs"></a>Parágrafos de embrulho

Quando [`Text`](xref:Xamarin.Forms.Label.Text) a `Label` propriedade de `Label` contém texto longo, envolve-o automaticamente em várias linhas, como demonstrado pela amostra [**de Baskervilles.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles) Você pode incorporar códigos Unicode como '\u2014' para os caracteres em-dash ou C# como '\r' para quebrar uma nova linha.

Quando [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) as [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) propriedades `Label` de um `LayoutOptions.Fill`são definidas `Label` para , o tamanho total do é regido pelo espaço que seu recipiente disponibiliza. Dizem `Label` que o é *constrangido.* O tamanho `Label` do é o tamanho do seu recipiente.

Quando `HorizontalOptions` as `VerticalOptions` propriedades são definidas `LayoutOptions.Fill`para valores `Label` diferentes, o tamanho do é regido pelo espaço necessário `Label`para renderizar o texto, até o tamanho que seu recipiente disponibiliza para o . Diz-se `Label` que *não é constrangido* e determina seu próprio tamanho.

(Nota: Os termos *constrangidos* e *sem restrições* podem ser contra-intuitivos, porque uma visão sem restrições é geralmente menor do que uma visão restrita. Além disso, esses termos não são usados consistentemente nos primeiros capítulos do livro.)

Uma visão como `Label` a pode ser constrangida em uma dimensão e sem restrições na outra. A `Label` só envolverá texto em várias linhas se estiver limitado horizontalmente.

Se `Label` um for constrangido, poderá ocupar consideravelmente mais espaço do que o necessário para o texto. O texto pode ser posicionado dentro `Label`da área geral do . Defina [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) a propriedade como [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) membro da[`Start`](xref:Xamarin.Forms.TextAlignment.Start) [`Center`](xref:Xamarin.Forms.TextAlignment.Center)enumeração ( , ou [`End`](xref:Xamarin.Forms.TextAlignment.Center)) para controlar o alinhamento de todas as linhas do parágrafo. O padrão `Start` é e alinha o texto à esquerda.

Defina [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) a propriedade como `TextAlignment` membro da enumeração para posicionar o texto na parte superior, central ou inferior da área ocupada pelo `Label`.

Defina [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) a propriedade como [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) membro da[`WordWrap`](xref:Xamarin.Forms.LineBreakMode.WordWrap) [`CharacterWrap`](xref:Xamarin.Forms.LineBreakMode.CharacterWrap)enumeração ( , , [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap) [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode.HeadTruncation), , [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode.MiddleTruncation)ou [`TailTruncation`](xref:Xamarin.Forms.LineBreakMode.TailTruncation)) para controlar como as múltiplas linhas em uma quebra de parágrafo ou são truncadas.

## <a name="text-and-background-colors"></a>Cores de texto e de fundo

Defina [`TextColor`](xref:Xamarin.Forms.Label.TextColor) [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) as `Label` propriedades [`Color`](xref:Xamarin.Forms.Color) de valores para controlar a cor do texto e do plano de fundo.

Aplica-se `BackgroundColor` ao fundo de toda a `Label`área ocupada pelo . Dependendo das `HorizontalOptions` `VerticalOptions` propriedades, esse tamanho pode ser consideravelmente maior do que a área necessária para exibir o texto. Você pode usar a cor `HorizontalOptions`para `VerticalOptions` `HorizontalExeAlignment`experimentar `VerticalTextAlignment` vários valores de , e `Label`para ver como eles afetam o `Label`tamanho e a posição do , e o tamanho e posição do texto dentro do .

## <a name="the-color-structure"></a>A estrutura de cor

A [`Color`](xref:Xamarin.Forms.Color) estrutura permite especificar cores como valores Vermelho-Verde-Azul (RGB) ou Valores de Hue-Saturação-Luminosidade (HSL) ou com um nome de cor. Um canal Alpha também está disponível para indicar transparência.

Use `Color` um construtor para especificar:

- uma [sombra cinza](xref:Xamarin.Forms.Color.%23ctor(System.Double))
- um [valor RGB](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))
- um [valor RGB com transparência](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))

Argumentos `double` são valores que variam de 0 a 1.

Você também pode usar vários `Color` métodos estáticos para criar valores:

- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double))para `double` valores RGB de 0 a 1
- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Int32,System.Int32,System.Int32))para valores inteiros De RGB de 0 a 255
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Double,System.Double,System.Double,System.Double))para `double` valores RGB com transparência
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32))para valores inteiros RGB com transparência
- [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double))para `double` valores HSL com transparência
- [`Color.FromUint`](xref:Xamarin.Forms.Color.FromUint(System.UInt32))para `uint` um valor calculado como (B + 256 \* (G + 256 \* (R + 256 \* A))
- [`Color.FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String))para `string` um formato de dígitos hexadecimais na forma "#AARRGGBB" ou "#RRGGBB" ou "#ARGB" ou "#RGB", onde cada letra corresponde a um dígito hexadecimal para os canais alfa, vermelho, verde e azul. Este método é usado como principal para conversões de cores XAML, conforme discutido no [Capítulo 7, XAML vs. código](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md).

Uma vez criado, um `Color` valor é imutável. As características da cor podem ser obtidas a partir das seguintes propriedades:

- [`R`](xref:Xamarin.Forms.Color.R)
- [`G`](xref:Xamarin.Forms.Color.G)
- [`B`](xref:Xamarin.Forms.Color.B)
- [`A`](xref:Xamarin.Forms.Color.A)
- [`Hue`](xref:Xamarin.Forms.Color.Hue)
- [`Saturation`](xref:Xamarin.Forms.Color.Saturation)
- [`Luminosity`](xref:Xamarin.Forms.Color.Luminosity)

Estes `double` são todos os valores que variam de 0 a 1.

`Color`também define 240 campos públicos só de leitura estática para cores comuns. Na época em que o livro foi escrito, apenas 17 cores comuns estavam disponíveis.

Outro campo público só de leitura estática define uma cor com todos os canais de cores definidos como zero:

- [`Color.Transparent`](xref:Xamarin.Forms.Color.Transparent)

Vários métodos de instância permitem modificar uma cor existente para criar uma nova cor:

- [`AddLuminosity`](xref:Xamarin.Forms.Color.AddLuminosity(System.Double))
- [`MultiplyAlpha`](xref:Xamarin.Forms.Color.MultiplyAlpha(System.Double))
- [`WithHue`](xref:Xamarin.Forms.Color.WithHue(System.Double))
- [`WithLuminosity`](xref:Xamarin.Forms.Color.WithLuminosity(System.Double))
- [`WithSaturation`](xref:Xamarin.Forms.Color.WithSaturation(System.Double))

Finalmente, duas propriedades estáticas somente de leitura definem um valor de cor especial:

- [`Color.Default`](xref:Xamarin.Forms.Color.Default), todos os &ndash;canais definidos para 1
- [`Color.Accent`](xref:Xamarin.Forms.Color.Accent)

`Color.Default`destina-se a impor o esquema de cores da plataforma e, consequentemente, tem um significado diferente em diferentes contextos em diferentes plataformas. Por padrão, os esquemas de cores da plataforma são:

- iOS: Texto escuro em um fundo claro
- Android: Texto leve em um fundo escuro (no livro) ou texto escuro em um fundo claro (para Design de material via AppCompat no ramo **mestre** do repositório de código de amostra)
- UWP: Texto escuro em um fundo claro

O `Color.Accent` valor resulta em uma cor específica da plataforma (e às vezes selecionável pelo usuário) que é visível em um fundo escuro ou claro.

## <a name="changing-the-application-color-scheme"></a>Alterando o esquema de cores do aplicativo

As várias plataformas têm um esquema de cores padrão, como mostrado na lista acima.

Ao direcionar o Android, é possível mudar para um esquema escuro sobre a luz especificando um tema de luz no arquivo Android.Manifest.xml, ou [adicionando AppCompat e Design de Material](~/xamarin-forms/platform/android/appcompat-material-design.md).

Para as plataformas Windows, o tema de cores é normalmente selecionado pelo usuário, mas você pode adicionar um `RequestedTheme` atributo definido para qualquer um `Light` ou `Dark` no arquivo App.xaml da plataforma. Por padrão, o arquivo App.xaml no `RequestedTheme` projeto UWP contém um atributo definido para `Light`.

## <a name="font-sizes-and-attributes"></a>Tamanhos e atributos da fonte

Defina [`FontFamily`](xref:Xamarin.Forms.Label.FontFamily) a `Label` propriedade de uma string como "Times Roman" para selecionar uma família de fontes. No entanto, você precisa especificar uma família de fontes que é suportada na plataforma específica, e as plataformas são inconsistentes nesse sentido.

Defina [`FontSize`](xref:Xamarin.Forms.Label.FontSize) a `Label` propriedade `double` de a para especificar a altura aproximada da fonte. Consulte [o Capítulo 5, Lidando com Tamanhos,](chapter05.md)para obter mais detalhes sobre a escolha inteligente dos tamanhos das fontes.

Você pode, alternativamente, obter um dos vários tamanhos de fonte pré-definidos dependentes da plataforma. O [`Device.GetNamedSize`](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type)) método estático e `double` a sobrecarga retornam um valor de [`NamedSize`](xref:Xamarin.Forms.NamedSize) tamanho de[`Default`](xref:Xamarin.Forms.NamedSize.Default) [`Micro`](xref:Xamarin.Forms.NamedSize.Micro)fonte apropriado para a plataforma com base nos membros da enumeração ( , [`Small`](xref:Xamarin.Forms.NamedSize.Small), , [`Medium`](xref:Xamarin.Forms.NamedSize.Medium)e [`Large`](xref:Xamarin.Forms.NamedSize.Large)). [overload](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,Xamarin.Forms.Element)) O valor devolvido `Medium` do membro não é `Default`necessariamente o mesmo que . A [**amostra NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) exibe texto com esses tamanhos nomeados.

Defina [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes) a `Label` propriedade de [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) um membro [`Bold`](xref:Xamarin.Forms.FontAttributes.Bold)dessas [`Italic`](xref:Xamarin.Forms.FontAttributes.Italic)enumerações, ou [`None`](xref:Xamarin.Forms.FontAttributes.None). Você pode `Bold` combinar `Italic` os membros com o operador C# bitwise OR.

## <a name="formatted-text"></a>Texto formatado

Em todos os exemplos até agora, todo o `Label` texto exibido pelo foi formatado uniformemente. Para variar a formatação dentro de uma `Text` seqüência de texto, não defina a propriedade de `Label`. Em vez [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) disso, defina [`FormattedString`](xref:Xamarin.Forms.FormattedString)a propriedade como um objeto de tipo .

`FormattedString`tem [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) uma propriedade que [`Span`](xref:Xamarin.Forms.Span) é uma coleção de objetos. Cada `Span` objeto tem [`Text`](xref:Xamarin.Forms.Span.Text) [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily)suas [`FontSize`](xref:Xamarin.Forms.Span.FontSize) [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes)propriedades [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor)próprias [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) e propriedades.

A amostra [**VariableFormattedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText) `FormattedText` demonstra o uso da propriedade para uma única linha de texto, e [**VariableFormattedParagraph**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara) demonstra a técnica de um parágrafo inteiro, conforme mostrado aqui:

[![Captura de tela tripla de parágrafo formatado variável](images/ch03fg06-small.png "Texto de etiqueta formatado variável")](images/ch03fg06-large.png#lightbox "Texto de etiqueta formatado variável")

O [**programa NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) `Label` usa `FormattedString` um único e um objeto para exibir todos os tamanhos de fonte nomeados para cada plataforma.

## <a name="related-links"></a>Links relacionados

- [Capítulo 3 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [Capítulo 3 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [Capítulo 3 F# amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [Rótulo](~/xamarin-forms/user-interface/text/label.md)
- [Trabalhar com cores](~/xamarin-forms/user-interface/colors.md)
