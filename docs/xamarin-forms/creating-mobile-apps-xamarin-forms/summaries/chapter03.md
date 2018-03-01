---
title: "Resumo do capítulo 3. Aprofundar no texto"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 9d283a4136a7cdfe39ea0b2da65273332fd47b00
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>Resumo do capítulo 3. Aprofundar no texto

Este capítulo explora o [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) exibição mais detalhadamente, incluindo cores, fontes e a formatação.

## <a name="wrapping-paragraphs"></a>Encapsulamento parágrafos

Quando o [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/) propriedade de `Label` contém texto longo, `Label` automaticamente quebra em várias linhas conforme demonstrado a [ **Baskervilles** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles) exemplo. Você pode inserir códigos Unicode como \u2014 em dash ou c# caracteres como \r interromper para uma nova linha.

Quando o [ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/) e [ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) propriedades de um `Label` são definidos como `LayoutOptions.Fill`, o tamanho geral do `Label` é controlado pelo espaço que seu contêiner disponibiliza. O `Label` será considerada *restrita*. O tamanho do `Label` é o tamanho de seu contêiner.

Quando o `HorizontalOptions` e `VerticalOptions` propriedades são definidas como valores diferentes de `LayoutOptions.Fill`, o tamanho do `Label` é controlado pelo espaço necessário para processar o texto, até o tamanho que seu contêiner disponibiliza para o `Label`. O `Label` será considerada *irrestrita* e determina seu próprio tamanho.

(Observação: os termos *restrita* e *irrestrita* pode ser contrário à intuição, porque uma exibição irrestrita é geralmente menor do que uma exibição restrita. Além disso, esses termos não são usados consistentemente nos capítulos iniciais do livro.)

Um modo de exibição, como um `Label` pode ser restrita em uma dimensão e irrestrita no outro. Um `Label` só será quebrar o texto em várias linhas se ele é restrito horizontalmente.

Se um `Label` é restrito, ele pode ocupar consideravelmente mais espaço do que o necessário para o texto. O texto pode ser posicionado dentro da área geral do `Label`. Definir o [ `HorizontalTextAlignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.HorizontalTextAlignment/) propriedade a um membro do [ `TextAlignment` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextAlignment/) enumeração ([`Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Start/), [ `Center` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Center/), ou [ `End` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Center/)) para controlar o alinhamento de todas as linhas do parágrafo. O padrão é `Start` e alinha o texto à esquerda.

Definir o [ `VerticalTextAlignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.VerticalTextAlignment/) propriedade a um membro do `TextAlignment` enumeração para posicionar o texto na parte superior, central ou parte inferior da área ocupada pelo `Label`.

Definir o [ `LineBreakMode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.LineBreakMode/) propriedade a um membro do [ `LineBreakMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LineBreakMode/) enumeração ([`WordWrap`](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.WordWrap/), [ `CharacterWrap` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.CharacterWrap/), [ `NoWrap` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.NoWrap/), [ `HeadTruncation` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.HeadTruncation/), [ `MiddleTruncation` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.MiddleTruncation/), ou [ `TailTruncation` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.TailTruncation/)) para controle como as várias linhas em uma quebra de parágrafo ou são truncados.

## <a name="text-and-background-colors"></a>Cores de plano de fundo e texto

Definir o [ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/) e [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/) propriedades de `Label` para [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) valores para controlar a cor do texto e plano de fundo.

O `BackgroundColor` aplica-se ao plano de fundo de toda a área ocupada pelo `Label`. Dependendo do `HorizontalOptions` e `VerticalOptions` propriedades, que o tamanho pode ser consideravelmente maior do que o necessário para exibir o texto de área. Você pode usar cor para fazer experiências com vários valores de `HorizontalOptions`, `VerticalOptions`, `HorizontalExeAlignment`, e `VerticalTextAlignment` para ver como elas afetam o tamanho e a posição do `Label`e o tamanho e a posição do texto dentro de `Label`.

## <a name="the-color-structure"></a>A estrutura de cor

O [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) estrutura lhe permite especificar as cores vermelho-verde-azul valores RGB () ou valores de matiz-saturação-luminosidade (HSL), ou com um nome de cor. Um canal alfa também está disponível para indicar a transparência.

Use um `Color` construtor para especificar:

- um [tonalidade de cinza](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/)
- um [valor RGB](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/System.Double/System.Double/)
- um [valor RGB com transparência](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/System.Double/System.Double/System.Double/)

Os argumentos são `double` valores que variam de 0 a 1.

Você também pode usar vários métodos estáticos para criar `Color` valores:

- [`Color.FromRgb`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgb/p/System.Double/System.Double/System.Double/) para `double` valores RGB de 0 a 1
- [`Color.FromRgb`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgb/p/System.Int32/System.Int32/System.Int32/) para valores RGB de inteiro de 0 a 255
- [`Color.FromRgba`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgba/p/System.Double/System.Double/System.Double/System.Double/) para `double` valores RGB com transparência
- [`Color.FromRgba`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgba/p/System.Int32/System.Int32/System.Int32/System.Int32/) para valores RGB de inteiros com transparência
- [`Color.FromHsla`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHsla/p/System.Double/System.Double/System.Double/System.Double/) para `double` valores HSL com transparência
- [`Color.FromUint`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromUint/p/System.UInt32/) para uma `uint` valor calculado como (B + 256 * (G + 256 * (256 + de R * um)))
- [`Color.FromHex`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHex/p/System.String/) para um `string` formato de dígitos hexadecimais no formato "#AARRGGBB" ou "#RRGGBB" ou "#ARGB" ou "#RGB", onde cada letra corresponde a um dígito hexadecimal para o alfa, vermelho, verdes e azuis canais. Esse método é a principal usada para conversões de cores XAML, conforme discutido em [capítulo 7, XAML e código](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md).

Depois de criado, um `Color` valor é imutável. As características da cor podem ser obtidas das seguintes propriedades:

- [`R`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.R/)
- [`G`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.G/)
- [`B`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.B/)
- [`A`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.A/)
- [`Hue`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Hue/)
- [`Saturation`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Saturation/)
- [`Luminosity`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Luminosity/)

Estas são todas as `double` valores que variam de 0 a 1.

`Color` também define 240 campos públicos estáticos somente leitura para cores comuns. No momento em que o catálogo foi escrito, estavam disponíveis somente 17 cores comuns.

Outro campo estático público somente leitura define uma cor com todos os canais de cores definidos como zero:

- [`Color.Transparent`](https://developer.xamarin.com/api/field/Xamarin.Forms.Color.Transparent/)

Vários métodos de instância permitem modificar uma cor existente para criar uma nova cor:

- [`AddLuminosity`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.AddLuminosity/p/System.Double/)
- [`MultiplyAlpha`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.MultiplyAlpha/p/System.Double/)
- [`WithHue`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.WithHue/p/System.Double/)
- [`WithLuminosity`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.WithLuminosity/p/System.Double/)
- [`WithSaturation`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.WithSaturation/p/System.Double/)

Por fim, duas propriedades somente leitura estáticas definem valor de cor especial:

- [`Color.Default`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Default/), todos os canais definidas como & #x 2013; 1
- [`Color.Accent`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Accent/)

`Color.Default` destina-se de aplicar o esquema de cores da plataforma e, consequentemente, tem um significado diferente em diferentes contextos em diferentes plataformas. Por padrão, os esquemas de cores de plataforma são:

- iOS: texto escuro no plano de fundo claro
- Android: Claro texto em um plano de fundo escuro (no catálogo) ou escuro no plano de fundo claro (para Material de Design por meio de AppCompat no **mestre** ramificação do repositório de código de exemplo)
- UWP: Texto escuro no plano de fundo claro
- Windows 8.1: Claro texto em um plano de fundo escuro
- Windows Phone 8.1: Claro texto em um plano de fundo escuro

O `Color.Accent` valor resulta em uma cor específica de plataforma (e, às vezes, selecionáveis pelo usuário) que é visível em um fundo escuro ou claro.

## <a name="changing-the-application-color-scheme"></a>Alterando o esquema de cores do aplicativo

Várias plataformas têm um esquema de cores padrão, conforme mostrado na lista acima.

Durante o direcionamento do Android, é possível alternar para um esquema escuro em luz especificando um tema claro no arquivo Android.Manifest.xml ou por [adicionando AppCompat e Material de Design](~/xamarin-forms/platform/android/appcompat.md).

Para as plataformas do Windows, o tema de cores normalmente é selecionado pelo usuário, mas você pode adicionar uma `RequestedTheme` atributo definido como `Light` ou `Dark` no arquivo App. XAML da plataforma. Por padrão, o arquivo App. XAML no projeto UWP contém um `RequestedTheme` atributo definido como `Light`.

## <a name="font-sizes-and-attributes"></a>Atributos e tamanhos de fonte

Definir o [ `FontFamily` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FontFamily/) propriedade `Label` para uma cadeia de caracteres como "Times Roman" para selecionar uma família de fontes. No entanto, você precisa especificar uma família de fontes que é compatível com a plataforma específica, e as plataformas são inconsistentes em relação a isso.

Definir o [ `FontSize` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FontSize/) propriedade `Label` para um `double` para especificar altura aproximada da fonte. Consulte [capítulo 5, lidar com tamanhos](chapter05.md), para obter mais detalhes sobre como escolher inteligente tamanhos de fonte.

Como alternativa você pode obter um dos vários tamanhos de fonte de predefinição dependente de plataforma. Estático [ `Device.GetNamedSize` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.GetNamedSize/p/Xamarin.Forms.NamedSize/System.Type/) método e [sobrecarga](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.GetNamedSize/p/Xamarin.Forms.NamedSize/Xamarin.Forms.Element/) retornam um `double` valor de tamanho de fonte apropriado para a plataforma com base nos membros do [ `NamedSize` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NamedSize/)enumeração ([`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Default/), [ `Micro` ](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Micro/), [ `Small` ](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Small/), [ `Medium` ](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Medium/),  e [ `Large` ](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Large/)). O valor retornado do `Medium` membro não é necessariamente o mesmo que `Default`. O [ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) exemplo exibe o texto com estas chamado tamanhos.

Definir o [ `FontAttributes` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FontAttributes/) propriedade `Label` a um membro desses [ `FontAttributes` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FontAttributes/) enumeração, [ `Bold` ](https://developer.xamarin.com/api/field/Xamarin.Forms.FontAttributes.Bold/), [ `Italic` ](https://developer.xamarin.com/api/field/Xamarin.Forms.FontAttributes.Italic/), ou [ `None` ](https://developer.xamarin.com/api/field/Xamarin.Forms.FontAttributes.None/). Você pode combinar o `Bold` e `Italic` membros com o c# operador bit a bit OR.

## <a name="formatted-text"></a>Texto formatado

Em todos os exemplos até agora, todo o texto exibido pelo `Label` tiverem sido formatados de maneira uniforme. Para variar a formatação em uma cadeia de caracteres de texto, não defina o `Text` propriedade `Label`. Em vez disso, defina o [ `FormattedText` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FormattedText/) propriedade para um objeto do tipo [ `FormattedString` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FormattedString/).

`FormattedString` tem um [ `Spans` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FormattedString.Spans/) propriedade que é uma coleção de [ `Span` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Span/) objetos. Cada `Span` objeto tem seu próprio [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.Text/), [ `FontFamily` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.FontFamily/), [ `FontSize` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.FontSize/), [ `FontAttributes` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.FontAttributes/), [ `ForegroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.ForegroundColor/), e [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.BackgroundColor/) propriedades.

O [ **VariableFormattedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText) exemplo demonstra como usar o `FormattedText` propriedade para uma única linha de texto, e [ **VariableFormattedParagraph** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara) demonstra a técnica para todo o parágrafo, como mostrado aqui:

[![Tripla captura de tela da variável formatado parágrafo](images/ch03fg06-small.png "texto de rótulo formatado da variável")](images/ch03fg06-large.png "variável formatado texto de rótulo")

O [ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes) programa usa uma única `Label` e um `FormattedString` objeto para exibir todos os tamanhos de fonte nomeados para cada plataforma.



## <a name="related-links"></a>Links relacionados

- [Texto completo do capítulo 3 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [Exemplos de capítulo 3](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [Exemplos de capítulo 3 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [Rótulo](~/xamarin-forms/user-interface/text/label.md)
- [Trabalhar com cores](~/xamarin-forms/user-interface/colors.md)
