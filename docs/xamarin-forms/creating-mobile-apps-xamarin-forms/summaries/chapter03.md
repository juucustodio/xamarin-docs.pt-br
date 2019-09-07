---
title: Resumo do capítulo 3. Aprofundamento no texto
description: 'Criando aplicativos móveis com Xamarin. Forms: Resumo do capítulo 3. Aprofundamento no texto'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2E5581A6-4D3E-4BD5-9FDB-ACBA0F0FC734
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: e37175240825c0fed350589649469c99f1bbf69a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771221"
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>Resumo do capítulo 3. Aprofundamento no texto

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)

Este capítulo explora a [ `Label` ](xref:Xamarin.Forms.Label) exibir mais detalhes, incluindo cores, fontes e formatação.

## <a name="wrapping-paragraphs"></a>Quebra automática de parágrafos

Quando o [ `Text` ](xref:Xamarin.Forms.Label.Text) propriedade do `Label` contém texto longo, `Label` automaticamente quebra em várias linhas como demonstrado pelo [ **Baskervilles** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles) exemplo. Você pode inserir códigos de Unicode como \u2014 para em-dash, ou C# caracteres, como '\r' quebrar para uma nova linha.

Quando o [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) e [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) propriedades de um `Label` são definidos como `LayoutOptions.Fill`, o tamanho geral do `Label` é regido pelo espaço de que seu contêiner disponibiliza. O `Label` será considerada *restrita*. O tamanho do `Label` é o tamanho de seu contêiner.

Quando o `HorizontalOptions` e `VerticalOptions` propriedades são definidas como valores diferente de `LayoutOptions.Fill`, o tamanho da `Label` é regido pelo espaço necessário para renderizar o texto, até o tamanho que seu contêiner torna disponível para o `Label`. O `Label` será considerada *irrestrita* e determina seu próprio tamanho.

(Observação: Os termos *restritos* e *irrestrito* podem ser intuitivos, uma vez que uma exibição irrestrita é geralmente menor do que uma exibição restrita. Além disso, esses termos não são usados consistentemente nos primeiros capítulos do livro.)

Um modo de exibição, como um `Label` pode ser restrito em uma dimensão e sem restrições no outro. Um `Label` só será quebrar o texto em várias linhas se ele for restrita horizontalmente.

Se um `Label` é restrito, ele pode ocupar espaço consideravelmente mais que o necessário para o texto. O texto pode ser posicionado dentro da área geral do `Label`. Defina as [ `HorizontalTextAlignment` ](xref:Xamarin.Forms.Label.HorizontalTextAlignment) propriedade a um membro da [ `TextAlignment` ](xref:Xamarin.Forms.TextAlignment) enumeração ([`Start`](xref:Xamarin.Forms.TextAlignment.Start), [ `Center` ](xref:Xamarin.Forms.TextAlignment.Center), ou [ `End` ](xref:Xamarin.Forms.TextAlignment.Center)) para controlar o alinhamento de todas as linhas do parágrafo. O padrão é `Start` e alinha o texto à esquerda.

Defina a [ `VerticalTextAlignment` ](xref:Xamarin.Forms.Label.VerticalTextAlignment) propriedade a um membro da `TextAlignment` enumeração para posicionar o texto na parte superior, central ou parte inferior da área ocupada pelo `Label`.

Defina as [ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode) propriedade a um membro da [ `LineBreakMode` ](xref:Xamarin.Forms.LineBreakMode) enumeração ([`WordWrap`](xref:Xamarin.Forms.LineBreakMode.WordWrap), [ `CharacterWrap` ](xref:Xamarin.Forms.LineBreakMode.CharacterWrap), [ `NoWrap` ](xref:Xamarin.Forms.LineBreakMode.NoWrap), [ `HeadTruncation` ](xref:Xamarin.Forms.LineBreakMode.HeadTruncation), [ `MiddleTruncation` ](xref:Xamarin.Forms.LineBreakMode.MiddleTruncation), ou [ `TailTruncation` ](xref:Xamarin.Forms.LineBreakMode.TailTruncation)) para controle como as várias linhas de uma quebra de parágrafo ou são truncados.

## <a name="text-and-background-colors"></a>Cores de plano de fundo e texto

Defina as [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor) e [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor) propriedades do `Label` para [ `Color` ](xref:Xamarin.Forms.Color) valores para controlar a cor do plano de fundo e texto.

O `BackgroundColor` aplica-se ao plano de fundo de toda a área ocupada pelo `Label`. Dependendo de `HorizontalOptions` e `VerticalOptions` propriedades, que o tamanho pode ser consideravelmente maior do que a área necessária para exibir o texto. Você pode usar cor para fazer experiências com vários valores de `HorizontalOptions`, `VerticalOptions`, `HorizontalExeAlignment`, e `VerticalTextAlignment` para ver como elas afetam o tamanho e posição do `Label`e o tamanho e a posição do texto dentro de `Label`.

## <a name="the-color-structure"></a>A estrutura de cor

O [ `Color` ](xref:Xamarin.Forms.Color) estrutura permite que você especificar cores como valores de vermelho-verde-azul (RGB) ou valores de matiz-saturação-luminosidade HSL (), ou com um nome de cor. Um canal alfa também está disponível para indicar a transparência.

Use um `Color` construtor para especificar:

- um [tonalidade de cinza](xref:Xamarin.Forms.Color.%23ctor(System.Double))
- um [valor RGB](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))
- um [valor RGB com transparência](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))

Os argumentos são `double` valores que variam de 0 a 1.

Você também pode usar vários métodos estáticos para criar `Color` valores:

- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) para `double` valores RGB de 0 a 1
- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Int32,System.Int32,System.Int32)) para valores RGB de inteiro de 0 a 255
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Double,System.Double,System.Double,System.Double)) para `double` valores RGB com transparência
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) para valores RGB de inteiros com transparência
- [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) para `double` valores HSL com transparência
- [`Color.FromUint`](xref:Xamarin.Forms.Color.FromUint(System.UInt32)) para um `uint` valor calculado como (B + 256 * (G + 256 * (R + 256 * um)))
- [`Color.FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String)) para um `string` formato de dígitos hexadecimais no formulário "#AARRGGBB" ou "#RRGGBB" ou "#ARGB" ou "#RGB", em que cada letra corresponde a um dígito hexadecimal para alfa, vermelho, verdes e azuis de canais. Esse método é primário usado para conversões de cor do XAML, conforme discutido em [capítulo 7, o XAML versus código](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md).

Depois de criada, um `Color` valor é imutável. As características da cor podem ser obtidas as seguintes propriedades:

- [`R`](xref:Xamarin.Forms.Color.R)
- [`G`](xref:Xamarin.Forms.Color.G)
- [`B`](xref:Xamarin.Forms.Color.B)
- [`A`](xref:Xamarin.Forms.Color.A)
- [`Hue`](xref:Xamarin.Forms.Color.Hue)
- [`Saturation`](xref:Xamarin.Forms.Color.Saturation)
- [`Luminosity`](xref:Xamarin.Forms.Color.Luminosity)

Estas são todas as `double` valores que variam de 0 a 1.

`Color` também define 240 campos estáticos públicos somente leitura para cores comuns. No momento em que o livro foi escrito, estavam disponíveis somente 17 cores comuns.

Outro campo estático público somente leitura define uma cor com todos os canais de cores definidos como zero:

- [`Color.Transparent`](xref:Xamarin.Forms.Color.Transparent)

Vários métodos de instância permitem a modificação de uma cor existente para criar uma nova cor:

- [`AddLuminosity`](xref:Xamarin.Forms.Color.AddLuminosity(System.Double))
- [`MultiplyAlpha`](xref:Xamarin.Forms.Color.MultiplyAlpha(System.Double))
- [`WithHue`](xref:Xamarin.Forms.Color.WithHue(System.Double))
- [`WithLuminosity`](xref:Xamarin.Forms.Color.WithLuminosity(System.Double))
- [`WithSaturation`](xref:Xamarin.Forms.Color.WithSaturation(System.Double))

Por fim, duas propriedades somente leitura estáticas definem valor de cor especial:

- [`Color.Default`](xref:Xamarin.Forms.Color.Default), todos os canais definido como &ndash;1
- [`Color.Accent`](xref:Xamarin.Forms.Color.Accent)

`Color.Default` destina-se para impor o esquema de cores da plataforma e, consequentemente, tem um significado diferente em contextos diferentes em diferentes plataformas. Por padrão, os esquemas de cores de plataforma são:

- iOS: Texto escuro em um plano de fundo claro
- Android: Texto claro em um plano de fundo escuro (no livro) ou texto escuro em um plano de fundo claro (para o design de material por meio de AppCompat no Branch **mestre** do repositório de código de exemplo)
- UWP: Texto escuro em um plano de fundo claro

O `Color.Accent` valor resulta em uma cor específica da plataforma (e, às vezes, selecionável pelo usuário) que é visível em um fundo claro ou escuro.

## <a name="changing-the-application-color-scheme"></a>Alterando o esquema de cores do aplicativo

Várias plataformas têm um esquema de cores padrão, conforme mostrado na lista acima.

Ao direcionar o Android, é possível alternar para um esquema de escuro na luz, especificando um tema claro no arquivo Android.Manifest.xml ou pelo [adicionando AppCompat e Design de Material](~/xamarin-forms/platform/android/appcompat-material-design.md).

Para as plataformas Windows, o tema de cores é normalmente selecionado pelo usuário, mas você pode adicionar um `RequestedTheme` atributo definido como `Light` ou `Dark` no arquivo App. XAML da plataforma. Por padrão, o arquivo App. XAML no projeto UWP contém um `RequestedTheme` atributo definido como `Light`.

## <a name="font-sizes-and-attributes"></a>Atributos e tamanhos de fonte

Defina as [ `FontFamily` ](xref:Xamarin.Forms.Label.FontFamily) propriedade do `Label` para uma cadeia de caracteres como "Times Roman" para selecionar uma família de fontes. No entanto, você precisa especificar uma família de fontes com suporte na plataforma específica e as plataformas são inconsistentes em relação a isso.

Defina as [ `FontSize` ](xref:Xamarin.Forms.Label.FontSize) propriedade do `Label` para um `double` para especificar a altura aproximada da fonte. Ver [capítulo 5, lidar com tamanhos](chapter05.md), para obter mais detalhes sobre como escolher com inteligência de tamanhos de fonte.

Como alternativa, você pode obter um dos vários tamanhos de fonte de predefinição dependente de plataforma. Estático [ `Device.GetNamedSize` ](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type)) método e [sobrecarregar](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,Xamarin.Forms.Element)) retornam uma `double` valor de tamanho de fonte apropriado para a plataforma com base nos membros do [ `NamedSize` ](xref:Xamarin.Forms.NamedSize)enumeração ([`Default`](xref:Xamarin.Forms.NamedSize.Default), [ `Micro` ](xref:Xamarin.Forms.NamedSize.Micro), [ `Small` ](xref:Xamarin.Forms.NamedSize.Small), [ `Medium` ](xref:Xamarin.Forms.NamedSize.Medium),  e [ `Large` ](xref:Xamarin.Forms.NamedSize.Large)). O valor retornado de `Medium` membro não é necessariamente o mesmo que `Default`. O [ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) exemplo exibe texto com esses tamanhos de nomeados.

Defina as [ `FontAttributes` ](xref:Xamarin.Forms.Label.FontAttributes) propriedade do `Label` a um membro desses [ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes) enumeração, [ `Bold` ](xref:Xamarin.Forms.FontAttributes.Bold), [ `Italic` ](xref:Xamarin.Forms.FontAttributes.Italic), ou [ `None` ](xref:Xamarin.Forms.FontAttributes.None). Você pode combinar as `Bold` e `Italic` membros com o C# operador OR de bit a bit.

## <a name="formatted-text"></a>Texto formatado

Em todos os exemplos até agora, todo o texto exibido pelo `Label` tiverem sido formatados de maneira uniforme. Para variar a formatação dentro de uma cadeia de caracteres de texto, não defina a `Text` propriedade de `Label`. Em vez disso, defina as [ `FormattedText` ](xref:Xamarin.Forms.Label.FormattedText) propriedade para um objeto do tipo [ `FormattedString` ](xref:Xamarin.Forms.FormattedString).

`FormattedString` tem um [ `Spans` ](xref:Xamarin.Forms.FormattedString.Spans) propriedade que é uma coleção de [ `Span` ](xref:Xamarin.Forms.Span) objetos. Cada `Span` objeto tem seu próprio [ `Text` ](xref:Xamarin.Forms.Span.Text), [ `FontFamily` ](xref:Xamarin.Forms.Span.FontFamily), [ `FontSize` ](xref:Xamarin.Forms.Span.FontSize), [ `FontAttributes` ](xref:Xamarin.Forms.Span.FontAttributes), [ `ForegroundColor` ](xref:Xamarin.Forms.Span.ForegroundColor), e [ `BackgroundColor` ](xref:Xamarin.Forms.Span.BackgroundColor) propriedades.

O [ **VariableFormattedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText) exemplo demonstra como usar o `FormattedText` propriedade para uma única linha de texto, e [ **VariableFormattedParagraph** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara) demonstra a técnica para um parágrafo inteiro, como mostrado aqui:

[![Tripla captura de tela da variável formatado parágrafo](images/ch03fg06-small.png "variável de texto de rótulo formatado")](images/ch03fg06-large.png#lightbox "texto de rótulo formatado da variável")

O [ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) programa usa um único `Label` e um `FormattedString` objeto para exibir todos os tamanhos de fonte nomeada para cada plataforma.

## <a name="related-links"></a>Links relacionados

- [Capítulo 3 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [Exemplos do capítulo 3](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [Capítulo 3 F# amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [Rótulo](~/xamarin-forms/user-interface/text/label.md)
- [Trabalhar com cores](~/xamarin-forms/user-interface/colors.md)
