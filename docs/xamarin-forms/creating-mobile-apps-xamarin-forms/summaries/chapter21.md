---
title: Resumo do capítulo 21. Transformações
description: 'Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 21. Transformações'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3642F112-C7FA-4A74-9000-F9087BA89AD9
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cb822c7ac2a05dc9f0d51f51e9737add4395b84d
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374016"
---
# <a name="summary-of-chapter-21-transforms"></a>Resumo do capítulo 21. Transformações

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)

> [!NOTE]
> Este livro foi publicado na Primavera de 2016 e não foi atualizado desde então. Há muito no livro que permanece valioso, mas alguns materiais estão desatualizados e alguns tópicos não estão mais totalmente corretos ou completos.

Uma Xamarin.Forms exibição aparece na tela em um local e tamanho determinados por seu pai, que geralmente é um `Layout` ou `Layout<View>` derivativo. A *transformação* é um Xamarin.Forms recurso que pode modificar esse local, tamanho ou até mesmo orientação.

Xamarin.Forms dá suporte a três tipos básicos de transformações:

- *Tradução* &mdash; deslocar um elemento horizontalmente ou verticalmente
- *Escala* &mdash; alterar o tamanho de um elemento
- *Rotação* &mdash; transformar um elemento em um ponto ou eixo

No Xamarin.Forms , o dimensionamento é isotropic; ele afeta a largura e a altura uniformemente. Há suporte para a rotação na superfície bidimensional da tela e no espaço 3D. Não há nenhuma transformação de distorção (ou simples) e nenhuma transformação de matriz generalizada.

As transformações têm suporte com oito Propriedades de tipo `double` definidas pela `VisualElement` classe:

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)
- [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)
- [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)
- [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)
- [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)
- [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

Todas essas propriedades têm o apoio das propriedades vinculáveis. Eles podem ser destinos de vinculação de dados e com estilo. [**Capítulo 22. A animação**](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md) demonstra como essas propriedades podem ser animadas, mas alguns exemplos neste capítulo mostram como você pode animá-las usando o Xamarin.Forms [temporizador](~/xamarin-forms/platform/device.md#devicestarttimer).

As propriedades de transformação afetam apenas a forma como o elemento é renderizado e *não* afetam como o elemento é percebido no layout.

## <a name="the-translation-transform"></a>A transformação de tradução

Valores diferentes de zero [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) das [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) Propriedades e deslocam um elemento horizontalmente ou verticalmente.

O programa [**TranslationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo) permite que você experimente essas propriedades com dois `Slider` elementos que controlam as `TranslationX` Propriedades e `TranslationY` de um `Frame` . A transformação também afeta todos os filhos disso `Frame` .

### <a name="text-effects"></a>Efeitos de texto

Um uso comum das propriedades de tradução é compensar ligeiramente a renderização do texto. Isso é demonstrado no exemplo [**Textoffsets**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets) :

[![Captura de tela tripla de deslocamentos de texto](images/ch21fg03-small.png "Deslocamentos de texto")](images/ch21fg03-large.png#lightbox "Deslocamentos de texto")

Outro efeito é para renderizar várias cópias de um `Label` para se assemelhar a um bloco 3D, como demonstrado no exemplo de [**BlockText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText) .

### <a name="jumps-and-animations"></a>Saltos e animações

O exemplo [**ButtonJump**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump) usa translação para mover um `Button` sempre que ele é tocado, mas a principal intenção é demonstrar que o recebe a entrada do `Button` usuário no local onde o botão é renderizado.

O exemplo de [**ButtonGlide**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide) é semelhante, mas usa um temporizador para animar o `Button` de um ponto para outro.

## <a name="the-scale-transform"></a>A transformação de escala

A [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) transformação pode aumentar ou diminuir o tamanho renderizado do elemento. O valor padrão é 1. Um valor de 0 faz com que o elemento fique invisível. Valores negativos fazem com que o elemento pareça ser girado em 180 graus. A `Scale` propriedade não afeta as `Width` Propriedades ou `Height` do elemento. Esses valores permanecem os mesmos.

Você pode experimentar a `Scale` propriedade usando o exemplo [**SimpleScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo) .

O exemplo [**ButtonScaler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler) demonstra a diferença entre animar a `Scale` propriedade de a `Button` e animar a `FontSize` propriedade. A `FontSize` propriedade afeta como o `Button` é percebido no layout; a `Scale` propriedade não.

O exemplo de [**ScaleToSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize) calcula uma `Scale` propriedade que é aplicada a um `Label` elemento para torná-lo o mais grande possível enquanto estiver se ajustando dentro da página.

### <a name="anchoring-the-scale"></a>Ancorando a escala

Os elementos dimensionados nos três exemplos anteriores aumentaram ou diminuíram de tamanho em relação ao centro do elemento. Em outras palavras, o elemento aumenta ou diminui em tamanho igual em todas as direções. Somente o ponto no centro do elemento permanece no mesmo local durante o dimensionamento.

Você pode alterar o centro do dimensionamento definindo as [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) Propriedades e [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) . Essas propriedades são relativas ao próprio elemento. Para `AnchorX` , um valor de 0 refere-se ao lado esquerdo do elemento e 1 se refere ao lado direito. Da mesma forma `AnchorY` , 0 é a parte superior e 1 é a parte inferior. Ambas as propriedades têm valores padrão de 0,5, que é o centro.

O exemplo [**AnchoredScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo) permite que você experimente as `AnchorX` Propriedades e, `AnchorY` bem como a `Scale` propriedade.

No iOS, o uso de valores `AnchorX` e propriedades não padrão `AnchorY` é geralmente incompatível com as alterações de orientação do telefone.

## <a name="the-rotation-transform"></a>A transformação de rotação

A [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propriedade é especificada em graus e indica rotação horária em um ponto do elemento definido por `AnchorX` e `AnchorY` . O [**PlaneRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo) permite que você experimente essas três propriedades.

### <a name="rotated-text-effects"></a>Efeitos de texto girados

O exemplo [**BoxViewCircle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle) demonstra a matemática necessária para desenhar um círculo usando 64 pequenos elementos girados `BoxView` .

O exemplo  [**RotatedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText) exibe vários `Label` elementos com a mesma cadeia de texto girada para aparecer como spokes.

O exemplo [**CircularText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText) exibe uma cadeia de texto que parece encapsular um círculo.

### <a name="an-analog-clock"></a>Um relógio analógico

A biblioteca [**Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contém uma [`AnalogClockViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs) classe que calcula os ângulos das mãos de um relógio. Para evitar dependências de plataforma no ViewModel, a classe usa `Task.Delay` em vez de um temporizador para localizar um novo `DateTime` valor.

Também incluído em **Xamarin.Forms book. Toolkit** é uma [`SecondTickConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs) classe que implementa `IValueConverter` e serve para arredondar um segundo ângulo para o segundo mais próximo.

O [**MinimalBoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock) usa três elementos giratórias `BoxView` para desenhar um relógio analógico.

O [**BoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock) usa `BoxView` para gráficos mais amplos, incluindo marcas de escala ao longo da face do relógio e as mãos que giram uma pequena distância de suas extremidades:

[![Captura de tela tripla de relógio BoxView](images/ch21fg17-small.png "Face do relógio analógico")](images/ch21fg17-large.png#lightbox "Face do relógio analógico")

Além disso, uma [`SecondBackEaseConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs) classe no **Xamarin.Forms book. Toolkit** faz com que a segunda mão pareça voltar um pouco antes de passar para a frente e voltar à sua posição correta.

### <a name="vertical-sliders"></a>Controles deslizantes verticais?

O exemplo [**VerticalSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders) demonstra que os `Slider` elementos podem ser girados 90 graus e ainda funcionam. No entanto, é difícil posicionar esses elementos girados `Slider` porque, no layout, eles ainda parecem estar horizontais.

## <a name="3d-ish-rotations"></a>rotações 3D-ish

A [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) Propriedade parece girar um elemento em volta de um eixo X 3D para que a parte superior e inferior do elemento pareçam mover para longe ou para fora do visualizador. Da mesma forma, o [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) parece girar um elemento em volta do eixo Y para que os lados esquerdo e direito do elemento pareçam ser movidos para frente ou para fora do visualizador.

A `AnchorX` propriedade afeta `RotationY` , mas não `RotationX` . A `AnchorY` propriedade afeta `RotationX` , mas não `RotationY` . Você pode experimentar o exemplo de [**ThreeDeeRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo) para explorar as interações dessas propriedades.

O sistema de coordenadas 3D implícito pelo Xamarin.Forms é canhoto. Se você apontar a dedo indicador do seu lado esquerdo na direção de aumento de coordenadas X (à direita) e o dedo médio na direção de aumento de coordenadas Y (para baixo), o polegar aponta para a direção das coordenadas Z crescentes (fora da tela).

Além disso, para qualquer um dos três eixos, se você apontar o polegar para a esquerda na direção do aumento de valores, a curva dos dedos indicará a direção da rotação para ângulos de rotação positivos.

## <a name="related-links"></a>Links relacionados

- [CAPÍTULO 21 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [CAPÍTULO 21 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
