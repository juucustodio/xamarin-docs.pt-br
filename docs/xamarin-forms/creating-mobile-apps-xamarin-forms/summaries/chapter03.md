---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 3. Deeper into text''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5423a9f716f384eca107003bdeca69615f8b459f
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136897"
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>Resumo do capítulo 3. Aprofundamento no texto

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)

Este capítulo explora a [`Label`](xref:Xamarin.Forms.Label) exibição mais detalhadamente, incluindo cor, fontes e formatação.

## <a name="wrapping-paragraphs"></a>Encapsulando parágrafos

Quando a [`Text`](xref:Xamarin.Forms.Label.Text) propriedade de `Label` contém texto longo, o `Label` encapsula automaticamente em várias linhas, conforme demonstrado pelo exemplo de [**Baskervilles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles) . Você pode inserir códigos Unicode como ' \u2014 ' para o travessão ou caracteres C#, como ' \r ', para interromper uma nova linha.

Quando as [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) Propriedades e de a `Label` são definidas como `LayoutOptions.Fill` , o tamanho geral do `Label` é regido pelo espaço que seu contêiner disponibiliza. O `Label` é considerado *restrito*. O tamanho do `Label` é o tamanho de seu contêiner.

Quando as `HorizontalOptions` `VerticalOptions` Propriedades e são definidas como valores diferentes de `LayoutOptions.Fill` , o tamanho do `Label` é regido pelo espaço necessário para renderizar o texto, até o tamanho que seu contêiner disponibiliza para o `Label` . O `Label` é considerado *irrestrito* e determina seu próprio tamanho.

(Observação: os termos *restritos* e *irrestrito* podem ser intuitivos, pois uma exibição irrestrita é geralmente menor do que uma exibição restrita. Além disso, esses termos não são usados de forma consistente nos primeiros capítulos do livro.

Uma exibição como uma `Label` pode ser restrita em uma dimensão e não restrita no outro. Um `Label` só quebrará o texto em várias linhas se ele for restrito horizontalmente.

Se um `Label` for restrito, ele poderá ocupar consideravelmente mais espaço do que o necessário para o texto. O texto pode ser posicionado na área geral do `Label` . Defina a [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) propriedade como um membro da [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) Enumeração ( [`Start`](xref:Xamarin.Forms.TextAlignment.Start) , [`Center`](xref:Xamarin.Forms.TextAlignment.Center) ou [`End`](xref:Xamarin.Forms.TextAlignment.Center) ) para controlar o alinhamento de todas as linhas do parágrafo. O padrão é `Start` e alinha o texto à esquerda.

Defina a [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) propriedade como um membro da `TextAlignment` enumeração para posicionar o texto na parte superior, no centro ou na parte inferior da área ocupada pelo `Label` .

Defina a [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) propriedade como um membro da [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) Enumeração (,,,, [`WordWrap`](xref:Xamarin.Forms.LineBreakMode.WordWrap) [`CharacterWrap`](xref:Xamarin.Forms.LineBreakMode.CharacterWrap) [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap) [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode.HeadTruncation) [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode.MiddleTruncation) ou [`TailTruncation`](xref:Xamarin.Forms.LineBreakMode.TailTruncation) ) para controlar como as linhas múltiplas em uma quebra de parágrafo ou estão truncadas.

## <a name="text-and-background-colors"></a>Cores de texto e de plano de fundo

Defina as [`TextColor`](xref:Xamarin.Forms.Label.TextColor) [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) Propriedades e de `Label` como [`Color`](xref:Xamarin.Forms.Color) valores para controlar a cor do texto e do plano de fundo.

O `BackgroundColor` aplica-se ao plano de fundo de toda a área ocupada pelo `Label` . Dependendo das `HorizontalOptions` `VerticalOptions` Propriedades e, esse tamanho pode ser consideravelmente maior do que a área necessária para exibir o texto. Você pode usar cor para experimentar vários valores de `HorizontalOptions` , `VerticalOptions` , `HorizontalExeAlignment` e `VerticalTextAlignment` para ver como eles afetam o tamanho e a posição do `Label` , e o tamanho e a posição do texto dentro do `Label` .

## <a name="the-color-structure"></a>A estrutura de cores

A [`Color`](xref:Xamarin.Forms.Color) estrutura permite que você especifique cores como valores vermelho-verde-azul (RGB) ou valores de HSL (Matiz-saturação-luminosidade) ou com um nome de cor. Um canal alfa também está disponível para indicar transparência.

Use um `Color` construtor para especificar:

- uma [tonalidade cinza](xref:Xamarin.Forms.Color.%23ctor(System.Double))
- um [valor RGB](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))
- um [valor RGB com transparência](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))

Argumentos são `double` valores variando de 0 a 1.

Você também pode usar vários métodos estáticos para criar `Color` valores:

- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double))para `double` valores RGB de 0 a 1
- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Int32,System.Int32,System.Int32))para valores RGB inteiros de 0 a 255
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Double,System.Double,System.Double,System.Double))para `double` valores RGB com transparência
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32))para valores RGB inteiros com transparência
- [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double))para `double` valores HSL com transparência
- [`Color.FromUint`](xref:Xamarin.Forms.Color.FromUint(System.UInt32))para um `uint` valor calculado como (B + 256 \* (G + 256 \* (R + 256 \* a)))
- [`Color.FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String))para um `string` formato de dígitos hexadecimais no formato "#AARRGGBB" ou "#RRGGBB" ou "#ARGB" ou "#RGB", em que cada letra corresponde a um dígito hexadecimal para os canais alfa, vermelho, verde e azul. Esse método é primário usado para conversões de cores XAML, conforme discutido no [capítulo 7, XAML vs. Code](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md).

Depois de criado, um `Color` valor é imutável. As características da cor podem ser obtidas nas seguintes propriedades:

- [`R`](xref:Xamarin.Forms.Color.R)
- [`G`](xref:Xamarin.Forms.Color.G)
- [`B`](xref:Xamarin.Forms.Color.B)
- [`A`](xref:Xamarin.Forms.Color.A)
- [`Hue`](xref:Xamarin.Forms.Color.Hue)
- [`Saturation`](xref:Xamarin.Forms.Color.Saturation)
- [`Luminosity`](xref:Xamarin.Forms.Color.Luminosity)

Esses são todos os `double` valores que variam de 0 a 1.

`Color`também define campos somente leitura estáticos públicos 240 para cores comuns. No momento em que o livro foi escrito, apenas 17 cores comuns estavam disponíveis.

Outro campo somente leitura estático público define uma cor com todos os canais de cor definidos como zero:

- [`Color.Transparent`](xref:Xamarin.Forms.Color.Transparent)

Vários métodos de instância permitem modificar uma cor existente para criar uma nova cor:

- [`AddLuminosity`](xref:Xamarin.Forms.Color.AddLuminosity(System.Double))
- [`MultiplyAlpha`](xref:Xamarin.Forms.Color.MultiplyAlpha(System.Double))
- [`WithHue`](xref:Xamarin.Forms.Color.WithHue(System.Double))
- [`WithLuminosity`](xref:Xamarin.Forms.Color.WithLuminosity(System.Double))
- [`WithSaturation`](xref:Xamarin.Forms.Color.WithSaturation(System.Double))

Finalmente, duas propriedades somente leitura estáticas definem o valor de cor especial:

- [`Color.Default`](xref:Xamarin.Forms.Color.Default), todos os canais são definidos como &ndash; 1
- [`Color.Accent`](xref:Xamarin.Forms.Color.Accent)

`Color.Default`o destina-se a impor o esquema de cores da plataforma e, consequentemente, tem um significado diferente em contextos diferentes em diferentes plataformas. Por padrão, os esquemas de cores da plataforma são:

- iOS: texto escuro em um plano de fundo claro
- Android: texto claro em um plano de fundo escuro (no livro) ou texto escuro em um plano de fundo claro (para o design de material por meio de AppCompat no Branch **mestre** do repositório de código de exemplo)
- UWP: texto escuro em um plano de fundo claro

O `Color.Accent` valor resulta em uma cor específica da plataforma (e, às vezes, selecionável pelo usuário) que é visível em um plano de fundo escuro ou claro.

## <a name="changing-the-application-color-scheme"></a>Alterando o esquema de cores do aplicativo

As várias plataformas têm um esquema de cores padrão, conforme mostrado na lista acima.

Ao direcionar o Android, é possível alternar para um esquema escuro na luz especificando um tema claro no arquivo Android. manifest. xml ou [adicionando AppCompat e design de material](~/xamarin-forms/platform/android/appcompat-material-design.md).

Para as plataformas Windows, o tema de cores normalmente é selecionado pelo usuário, mas você pode adicionar um `RequestedTheme` conjunto de atributos a `Light` ou `Dark` no arquivo app. XAML da plataforma. Por padrão, o arquivo app. XAML no projeto UWP contém um `RequestedTheme` atributo definido como `Light` .

## <a name="font-sizes-and-attributes"></a>Tamanhos e atributos de fonte

Defina a [`FontFamily`](xref:Xamarin.Forms.Label.FontFamily) propriedade de como `Label` uma cadeia de caracteres como "Times Roman" para selecionar uma família de fontes. No entanto, você precisa especificar uma família de fontes com suporte na plataforma específica e as plataformas são inconsistentes nesse aspecto.

Defina a [`FontSize`](xref:Xamarin.Forms.Label.FontSize) propriedade de `Label` como um `double` para especificar a altura aproximada da fonte. Consulte o [capítulo 5, lidando com tamanhos](chapter05.md), para obter mais detalhes sobre como escolher os tamanhos de fonte de forma inteligente.

Como alternativa, você pode obter um dos vários tamanhos de fonte predefinidos dependentes da plataforma. O static [ `Device.GetNamedSize` ] (xref: Xamarin.Forms . Device. GetNamedSize ( Xamarin.Forms . Método NamedSize, System. Type) e [Overload] (xref: Xamarin.Forms . Device. GetNamedSize ( Xamarin.Forms . NamedSize, Xamarin.Forms . )) Retornam um `double` valor de tamanho de fonte apropriado para a plataforma com base em membros da [`NamedSize`](xref:Xamarin.Forms.NamedSize) Enumeração (,,, [`Default`](xref:Xamarin.Forms.NamedSize.Default) [`Micro`](xref:Xamarin.Forms.NamedSize.Micro) [`Small`](xref:Xamarin.Forms.NamedSize.Small) [`Medium`](xref:Xamarin.Forms.NamedSize.Medium) e [`Large`](xref:Xamarin.Forms.NamedSize.Large) ). O valor retornado do `Medium` membro não é necessariamente o mesmo que `Default` . O exemplo de [**NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) exibe texto com esses tamanhos nomeados.

Defina a [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes) propriedade de `Label` como um membro dessa [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) enumeração,, [`Bold`](xref:Xamarin.Forms.FontAttributes.Bold) [`Italic`](xref:Xamarin.Forms.FontAttributes.Italic) ou [`None`](xref:Xamarin.Forms.FontAttributes.None) . Você pode combinar os `Bold` `Italic` Membros e com o operador OR ou bit C#.

## <a name="formatted-text"></a>Texto formatado

Em todos os exemplos até agora, todo o texto exibido pelo `Label` foi formatado uniformemente. Para variar a formatação dentro de uma cadeia de texto, não defina a `Text` propriedade de `Label` . Em vez disso, defina a [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) propriedade como um objeto do tipo [`FormattedString`](xref:Xamarin.Forms.FormattedString) .

`FormattedString`tem uma [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) propriedade que é uma coleção de [`Span`](xref:Xamarin.Forms.Span) objetos. Cada `Span` objeto tem suas próprias [`Text`](xref:Xamarin.Forms.Span.Text) [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily) Propriedades,, [`FontSize`](xref:Xamarin.Forms.Span.FontSize) ,, [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes) [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor) e [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) .

O exemplo [**VariableFormattedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText) demonstra como usar a `FormattedText` propriedade para uma única linha de texto e [**VariableFormattedParagraph**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara) demonstra a técnica de um parágrafo inteiro, como mostrado aqui:

[![Captura de tela tripla de parágrafo formatado por variável](images/ch03fg06-small.png "Texto do rótulo formatado variável")](images/ch03fg06-large.png#lightbox "Texto do rótulo formatado variável")

O programa [**NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) usa um único `Label` e um `FormattedString` objeto para exibir todos os tamanhos de fonte nomeados para cada plataforma.

## <a name="related-links"></a>Links relacionados

- [Capítulo 3 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [Exemplos do capítulo 3](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [Capítulo 3 exemplos de F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [Chamada](~/xamarin-forms/user-interface/text/label.md)
- [Trabalhando com cores](~/xamarin-forms/user-interface/colors.md)
