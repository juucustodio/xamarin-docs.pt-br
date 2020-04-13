---
title: Resumo do Capítulo 21. Transformações
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 21. Transformações'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3642F112-C7FA-4A74-9000-F9087BA89AD9
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 40c091d0c5042d172108709f89774e41e9339d4b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760575"
---
# <a name="summary-of-chapter-21-transforms"></a>Resumo do Capítulo 21. Transformações

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)

Uma exibição Xamarin.Forms aparece na tela em um local e tamanho `Layout` `Layout<View>` determinados por seu pai, que geralmente é um ou derivado. A *transformação* é um recurso Xamarin.Forms que pode modificar esse local, tamanho ou até mesmo orientação.

Xamarin.Forms suporta três tipos básicos de transformações:

- *A tradução* &mdash; muda um elemento horizontal ou verticalmente
- *Alterar* &mdash; a escala do tamanho de um elemento
- *Rotação* &mdash; gira um elemento em torno de um ponto ou eixo

Em Xamarin.Forms, o dimensionamento é isotrópico; afeta a largura e a altura uniformemente. A rotação é suportada tanto na superfície bidimensional da tela quanto no espaço 3D. Não há nenhuma distorção (ou pura) transformação, e nenhuma transformação de matriz generalizada.

As transformações são suportadas `double` com oito `VisualElement` propriedades de tipo definidas pela classe:

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)
- [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)
- [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)
- [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)
- [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)
- [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

Todas essas propriedades são apoiadas por propriedades vinculáveis. Eles podem ser alvos de vinculação de dados e estilo. [**Capítulo 22. A animação**](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md) demonstra como essas propriedades podem ser animadas, mas algumas amostras neste capítulo mostram como você pode animá-las usando o [temporizador](~/xamarin-forms/platform/device.md#devicestarttimer)Xamarin.Forms .

As propriedades de transformar afetam apenas a forma como o elemento é renderizado e *não* afetam a forma como o elemento é percebido no layout.

## <a name="the-translation-transform"></a>A tradução transforma

Valores não [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) zero [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) das propriedades e mudança suscetia um elemento horizontal ou verticalmente.

O programa [**TranslationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo) permite que você `Slider` experimente essas `TranslationX` propriedades `TranslationY` com `Frame`dois elementos que controlam as propriedades de um . A transformação também afeta `Frame`todas as crianças disso.

### <a name="text-effects"></a>Efeitos de texto

Um uso comum das propriedades de tradução é compensar ligeiramente a renderização do texto. Isso é demonstrado na amostra [**TextOffsets:**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets)

[![Captura de tela tripla de deslocamentos de texto](images/ch21fg03-small.png "Deslocamentos de texto")](images/ch21fg03-large.png#lightbox "Deslocamentos de texto")

Outro efeito é renderizar `Label` várias cópias de um para se assemelhar a um bloco 3D, como demonstrado na amostra [**BlockText.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText)

### <a name="jumps-and-animations"></a>Saltos e animações

A amostra [**ButtonJump**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump) usa `Button` a tradução para mover um sempre que é `Button` grampeado, mas a intenção principal é demonstrar que a entrada do usuário recebe no local onde o botão é renderizado.

A amostra [**ButtonGlide**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide) é semelhante, mas usa `Button` um temporizador para animar o de um ponto a outro.

## <a name="the-scale-transform"></a>A transformação da escala

A [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) transformação pode aumentar ou diminuir o tamanho renderizado do elemento. O valor padrão é 1. Um valor de 0 faz com que o elemento seja invisível. Valores negativos fazem com que o elemento pareça estar girando 180 graus. A `Scale` propriedade não `Width` afeta `Height` as propriedades ou propriedades do elemento. Esses valores permanecem os mesmos.

Você pode experimentar `Scale` a propriedade usando a amostra [**SimpleScaleDemo.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo)

A amostra [**ButtonScaler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler) demonstra a `Scale` diferença entre `Button` animar a `FontSize` propriedade de um e animar a propriedade. A `FontSize` propriedade afeta `Button` a forma como o é percebido no layout; a `Scale` propriedade não.

A amostra [**ScaleToSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize) `Scale` calcula uma propriedade `Label` que é aplicada a um elemento para torná-la o maior possível enquanto ainda se encaixa dentro da página.

### <a name="anchoring-the-scale"></a>Ancorando a escala

Os elementos dimensionados nas três amostras anteriores aumentaram ou diminuíram de tamanho em relação ao centro do elemento. Em outras palavras, o elemento aumenta ou diminui de tamanho o mesmo em todas as direções. Apenas o ponto no centro do elemento permanece no mesmo local durante o dimensionamento.

Você pode alterar o centro do [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) dimensionamento definindo as propriedades e. Essas propriedades são relativas ao elemento em si. Para `AnchorX`, um valor de 0 refere-se ao lado esquerdo do elemento e 1 refere-se ao lado direito. Da mesma `AnchorY`forma para , 0 é o topo e 1 é o inferior. Ambas as propriedades têm valores padrão de 0,5, que é o centro.

A amostra [**AnchoredScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo) permite que `AnchorX` `AnchorY` você experimente com `Scale` as propriedades e propriedades, bem como com a propriedade.

No iOS, o uso `AnchorX` de `AnchorY` valores e propriedades não padrão é geralmente incompatível com as alterações de orientação do telefone.

## <a name="the-rotation-transform"></a>A rotação se transforma

A [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propriedade é especificada em graus e indica rotação no `AnchorX` sentido `AnchorY`horário em torno de um ponto do elemento definido por e . O [**PlaneRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo) permite que você experimente com essas três propriedades.

### <a name="rotated-text-effects"></a>Efeitos de texto girados

A amostra [**BoxViewCircle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle) demonstra a matemática necessária para desenhar `BoxView` um círculo usando 64 pequenos elementos giratórios.

A amostra [**RotatedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText) exibe vários `Label` elementos com a mesma seqüência de texto girada para parecer em raios.

A amostra [**CircularText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText) exibe uma seqüência de texto que parece envolver em um círculo.

### <a name="an-analog-clock"></a>Um relógio analógico

A biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contém uma [`AnalogClockViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs) classe que calcula ângulos para as mãos de um relógio. Para evitar dependências de plataforma no `Task.Delay` ViewModel, a classe usa `DateTime` em vez de um temporizador para encontrar um novo valor.

Também incluído em **Xamarin.FormsBook.Toolkit** é [`SecondTickConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs) `IValueConverter` uma classe que implementa e serve para arredondar um segundo ângulo até o segundo mais próximo.

O [**MinimalBoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock) usa `BoxView` três elementos rotativos para desenhar um relógio analógico.

O [**BoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock) usa `BoxView` gráficos mais extensos, incluindo marcas de tique-taque ao redor da face do relógio, e mãos que giram um pouco longe de suas extremidades:

[![Captura de tela tripla do Relógio BoxView](images/ch21fg17-small.png "Face do relógio analógico")](images/ch21fg17-large.png#lightbox "Face do relógio analógico")

Além disso, uma [`SecondBackEaseConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs) classe em **Xamarin.FormsBook.Toolkit** faz com que a segunda mão pareça recuar um pouco antes de saltar à frente e, em seguida, voltar para sua posição correta.

### <a name="vertical-sliders"></a>Controles deslizantes verticais?

A amostra [**VerticalSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders) demonstra que `Slider` os elementos podem ser girados 90 graus e ainda funcionar. No entanto, é difícil posicionar `Slider` esses elementos girados porque no layout eles ainda parecem ser horizontais.

## <a name="3d-ish-rotations"></a>Rotações 3D-ish

A [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) propriedade parece girar um elemento em torno de um eixo X 3D de modo que a parte superior e inferior do elemento parece mover-se para ou para longe do espectador. Da mesma [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) forma, parece girar um elemento em torno do eixo Y para fazer com que os lados esquerdo e direito do elemento pareçam mover-se para ou para longe do espectador.

A `AnchorX` propriedade `RotationY` afeta, `RotationX`mas não. A `AnchorY` propriedade `RotationX` afeta, `RotationY`mas não. Você pode experimentar com a amostra [**ThreeDeeRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo) para explorar as interações dessas propriedades.

O sistema de coordenadas 3D implícito por Xamarin.Forms é canhoto. Se você apontar o indicador da mão esquerda na direção de aumentar as coordenadas X (para a direita) e o dedo médio na direção de aumentar as coordenadas Y (para baixo), então o polegar aponta na direção de aumentar as coordenadas Z (fora da tela).

Além disso, para qualquer um dos três eixos, se você apontar o polegar esquerdo na direção de valores crescentes, então a curva dos dedos indica a direção da rotação para ângulos de rotação positivos.

## <a name="related-links"></a>Links relacionados

- [Capítulo 21 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [Capítulo 21 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
