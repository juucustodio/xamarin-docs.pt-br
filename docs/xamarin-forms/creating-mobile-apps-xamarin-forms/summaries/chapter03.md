---
title: Resumo do capítulo 3. Aprofundamento no texto
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 3. Aprofundamento no texto'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2E5581A6-4D3E-4BD5-9FDB-ACBA0F0FC734
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 69415b59bbd376330454302981e3216c236a16bb
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "72980929"
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>Resumo do capítulo 3. Aprofundamento no texto

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)

Este capítulo explora a exibição [`Label`](xref:Xamarin.Forms.Label) mais detalhadamente, incluindo cor, fontes e formatação.

## <a name="wrapping-paragraphs"></a>Quebra automática de parágrafos

Quando a propriedade [`Text`](xref:Xamarin.Forms.Label.Text) de `Label` contém texto longo, `Label` automaticamente o quebra em várias linhas, conforme demonstrado pelo exemplo de [**Baskervilles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles) . Você pode inserir códigos de Unicode como \u2014 para em-dash, ou C# caracteres, como '\r' quebrar para uma nova linha.

Quando as propriedades [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) e [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) de um `Label` são definidas como `LayoutOptions.Fill`, o tamanho geral do `Label` é regido pelo espaço que seu contêiner disponibiliza. O `Label` é considerado *restrito*. O tamanho do `Label` é o tamanho de seu contêiner.

Quando as propriedades `HorizontalOptions` e `VerticalOptions` são definidas como valores diferentes de `LayoutOptions.Fill`, o tamanho do `Label` é regido pelo espaço necessário para processar o texto, até o tamanho que seu contêiner disponibiliza para o `Label`. O `Label` é considerado *irrestrito* e determina seu próprio tamanho.

(Observação: os termos *restritos* e *irrestrito* podem ser intuitivos, pois uma exibição irrestrita é geralmente menor do que uma exibição restrita. Além disso, esses termos não são usados consistentemente nos primeiros capítulos do livro.)

Uma exibição como uma `Label` pode ser restrita em uma dimensão e não restrita no outro. Uma `Label` quebrará apenas o texto em várias linhas se ela for restrita horizontalmente.

Se um `Label` for restrito, ele poderá ocupar um espaço consideravelmente maior do que o necessário para o texto. O texto pode ser posicionado na área geral da `Label`. Defina a propriedade [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) como um membro da enumeração [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) ([`Start`](xref:Xamarin.Forms.TextAlignment.Start), [`Center`](xref:Xamarin.Forms.TextAlignment.Center)ou [`End`](xref:Xamarin.Forms.TextAlignment.Center)) para controlar o alinhamento de todas as linhas do parágrafo. O padrão é `Start` e alinha o texto à esquerda.

Defina a propriedade [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) como um membro da enumeração `TextAlignment` para posicionar o texto na parte superior, central ou inferior da área ocupada pelo `Label`.

Defina a propriedade [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) como um membro da enumeração [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) ([`WordWrap`](xref:Xamarin.Forms.LineBreakMode.WordWrap), [`CharacterWrap`](xref:Xamarin.Forms.LineBreakMode.CharacterWrap), [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap), [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode.HeadTruncation), [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode.MiddleTruncation)ou [`TailTruncation`](xref:Xamarin.Forms.LineBreakMode.TailTruncation)) para controlar como as linhas múltiplas em uma quebra de parágrafo ou são truncadas.

## <a name="text-and-background-colors"></a>Cores de plano de fundo e texto

Defina as propriedades [`TextColor`](xref:Xamarin.Forms.Label.TextColor) e [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) de `Label` para [`Color`](xref:Xamarin.Forms.Color) valores para controlar a cor do texto e do plano de fundo.

O `BackgroundColor` aplica-se ao plano de fundo de toda a área ocupada pelo `Label`. Dependendo das propriedades `HorizontalOptions` e `VerticalOptions`, esse tamanho pode ser consideravelmente maior do que a área necessária para exibir o texto. Você pode usar Color para experimentar vários valores de `HorizontalOptions`, `VerticalOptions`, `HorizontalExeAlignment`e `VerticalTextAlignment` para ver como eles afetam o tamanho e a posição do `Label`, e o tamanho e a posição do texto dentro do `Label`.

## <a name="the-color-structure"></a>A estrutura de cor

A estrutura de [`Color`](xref:Xamarin.Forms.Color) permite especificar cores como valores de vermelho-verde-azul (RGB) ou valores de matiz-saturação-luminosidade (HSL) ou com um nome de cor. Um canal alfa também está disponível para indicar a transparência.

Use um construtor de `Color` para especificar:

- uma [tonalidade cinza](xref:Xamarin.Forms.Color.%23ctor(System.Double))
- um [valor RGB](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))
- um [valor RGB com transparência](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))

Os argumentos são `double` valores variando de 0 a 1.

Você também pode usar vários métodos estáticos para criar `Color` valores:

- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) para `double` valores RGB de 0 a 1
- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Int32,System.Int32,System.Int32)) para valores RGB inteiros de 0 a 255
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Double,System.Double,System.Double,System.Double)) para `double` valores RGB com transparência
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) para valores RGB inteiros com transparência
- [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) para `double` valores HSL com transparência
- [`Color.FromUint`](xref:Xamarin.Forms.Color.FromUint(System.UInt32)) para um valor de `uint` calculado como (B + 256 \* (G + 256 \* (R + 256 \* a)))
- [`Color.FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String)) para um formato de `string` de dígitos hexadecimais no formato "#AARRGGBB" ou "#RRGGBB" ou "#ARGB" ou "#RGB", onde cada letra corresponde a um dígito hexadecimal para os canais alfa, vermelho, verde e azul. Esse método é primário usado para conversões de cores XAML, conforme discutido no [capítulo 7, XAML vs. Code](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md).

Depois de criada, um valor de `Color` é imutável. As características da cor podem ser obtidas as seguintes propriedades:

- [`R`](xref:Xamarin.Forms.Color.R)
- [`G`](xref:Xamarin.Forms.Color.G)
- [`B`](xref:Xamarin.Forms.Color.B)
- [`A`](xref:Xamarin.Forms.Color.A)
- [`Hue`](xref:Xamarin.Forms.Color.Hue)
- [`Saturation`](xref:Xamarin.Forms.Color.Saturation)
- [`Luminosity`](xref:Xamarin.Forms.Color.Luminosity)

Esses são todos `double` valores que variam de 0 a 1.

`Color` também define 240 campos somente leitura estáticos públicos para cores comuns. No momento em que o livro foi escrito, estavam disponíveis somente 17 cores comuns.

Outro campo estático público somente leitura define uma cor com todos os canais de cores definidos como zero:

- [`Color.Transparent`](xref:Xamarin.Forms.Color.Transparent)

Vários métodos de instância permitem a modificação de uma cor existente para criar uma nova cor:

- [`AddLuminosity`](xref:Xamarin.Forms.Color.AddLuminosity(System.Double))
- [`MultiplyAlpha`](xref:Xamarin.Forms.Color.MultiplyAlpha(System.Double))
- [`WithHue`](xref:Xamarin.Forms.Color.WithHue(System.Double))
- [`WithLuminosity`](xref:Xamarin.Forms.Color.WithLuminosity(System.Double))
- [`WithSaturation`](xref:Xamarin.Forms.Color.WithSaturation(System.Double))

Por fim, duas propriedades somente leitura estáticas definem valor de cor especial:

- [`Color.Default`](xref:Xamarin.Forms.Color.Default), todos os canais definidos como &ndash;1
- [`Color.Accent`](xref:Xamarin.Forms.Color.Accent)

`Color.Default` destina-se a impor o esquema de cores da plataforma e, consequentemente, tem um significado diferente em contextos diferentes em diferentes plataformas. Por padrão, os esquemas de cores de plataforma são:

- iOS: texto escuro no plano de fundo claro
- Android: texto claro em um plano de fundo escuro (no livro) ou texto escuro em um plano de fundo claro (para o design de material por meio de AppCompat no Branch **mestre** do repositório de código de exemplo)
- UWP: Texto escuro no plano de fundo claro

O valor de `Color.Accent` resulta em uma cor específica da plataforma (e, às vezes, selecionável pelo usuário) que é visível em um plano de fundo escuro ou claro.

## <a name="changing-the-application-color-scheme"></a>Alterando o esquema de cores do aplicativo

Várias plataformas têm um esquema de cores padrão, conforme mostrado na lista acima.

Ao direcionar o Android, é possível alternar para um esquema escuro na luz especificando um tema claro no arquivo Android. manifest. xml ou [adicionando AppCompat e design de material](~/xamarin-forms/platform/android/appcompat-material-design.md).

Para as plataformas do Windows, o tema de cores normalmente é selecionado pelo usuário, mas você pode adicionar um conjunto de atributos `RequestedTheme` para `Light` ou `Dark` no arquivo app. XAML da plataforma. Por padrão, o arquivo app. XAML no projeto UWP contém um atributo `RequestedTheme` definido como `Light`.

## <a name="font-sizes-and-attributes"></a>Atributos e tamanhos de fonte

Defina a propriedade [`FontFamily`](xref:Xamarin.Forms.Label.FontFamily) de `Label` como uma cadeia de caracteres como "Times Roman" para selecionar uma família de fontes. No entanto, você precisa especificar uma família de fontes com suporte na plataforma específica e as plataformas são inconsistentes em relação a isso.

Defina a propriedade [`FontSize`](xref:Xamarin.Forms.Label.FontSize) de `Label` como um `double` para especificar a altura aproximada da fonte. Consulte o [capítulo 5, lidando com tamanhos](chapter05.md), para obter mais detalhes sobre como escolher os tamanhos de fonte de forma inteligente.

Como alternativa, você pode obter um dos vários tamanhos de fonte de predefinição dependente de plataforma. O método [estático`Device.GetNamedSize`](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type)) e a [sobrecarga](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,Xamarin.Forms.Element)) retornam um valor de tamanho de fonte `double` apropriado para a plataforma com base em membros da enumeração [`NamedSize`](xref:Xamarin.Forms.NamedSize) ([`Default`](xref:Xamarin.Forms.NamedSize.Default), [`Micro`](xref:Xamarin.Forms.NamedSize.Micro), [`Small`](xref:Xamarin.Forms.NamedSize.Small), [`Medium`](xref:Xamarin.Forms.NamedSize.Medium)e [`Large`](xref:Xamarin.Forms.NamedSize.Large)). O valor retornado do membro `Medium` não é necessariamente o mesmo que `Default`. O exemplo de [**NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) exibe texto com esses tamanhos nomeados.

Defina a propriedade [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes) de `Label` como um membro desses [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) enumeração, [`Bold`](xref:Xamarin.Forms.FontAttributes.Bold), [`Italic`](xref:Xamarin.Forms.FontAttributes.Italic)ou [`None`](xref:Xamarin.Forms.FontAttributes.None). Você pode combinar os membros de `Bold` e de `Italic` C# com o operador OR.

## <a name="formatted-text"></a>Texto formatado

Em todos os exemplos até agora, todo o texto exibido pelo `Label` foi formatado uniformemente. Para variar a formatação dentro de uma cadeia de texto, não defina a propriedade `Text` de `Label`. Em vez disso, defina a propriedade [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) como um objeto do tipo [`FormattedString`](xref:Xamarin.Forms.FormattedString).

`FormattedString` tem uma propriedade [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) que é uma coleção de objetos [`Span`](xref:Xamarin.Forms.Span) . Cada objeto de `Span` tem suas próprias propriedades [`Text`](xref:Xamarin.Forms.Span.Text), [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily), [`FontSize`](xref:Xamarin.Forms.Span.FontSize), [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes), [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor)e [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) .

O exemplo [**VariableFormattedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText) demonstra como usar a propriedade `FormattedText` para uma única linha de texto, e [**VariableFormattedParagraph**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara) demonstra a técnica para um parágrafo inteiro, como mostrado aqui:

[![Captura de tela tripla de parágrafo formatado por variável](images/ch03fg06-small.png "Texto do rótulo formatado variável")](images/ch03fg06-large.png#lightbox "Texto do rótulo formatado variável")

O programa [**NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) usa um único `Label` e um objeto `FormattedString` para exibir todos os tamanhos de fonte nomeados para cada plataforma.

## <a name="related-links"></a>Links relacionados

- [Capítulo 3 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [Exemplos do capítulo 3](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [Exemplos do F# capítulo 3](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [Rótulo](~/xamarin-forms/user-interface/text/label.md)
- [Trabalhando com cores](~/xamarin-forms/user-interface/colors.md)
