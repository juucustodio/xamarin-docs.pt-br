---
title: Resumo do capítulo 21. Transformações
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 21. Transformações'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3642F112-C7FA-4A74-9000-F9087BA89AD9
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 40c091d0c5042d172108709f89774e41e9339d4b
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "70760575"
---
# <a name="summary-of-chapter-21-transforms"></a>Resumo do capítulo 21. Transformações

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)

Uma exibição do Xamarin. Forms aparece na tela em um local e tamanho determinados por seu pai, que geralmente é um `Layout` ou `Layout<View>` derivativo. A *transformação* é um recurso do Xamarin. Forms que pode modificar esse local, tamanho ou até mesmo orientação.

Xamarin. Forms dá suporte a três tipos básicos de transformações:

- A *tradução* &mdash; deslocar um elemento horizontalmente ou verticalmente
- *Dimensionar* &mdash; alterar o tamanho de um elemento
- *Rotação* &mdash; transformar um elemento em um ponto ou eixo

No xamarin. Forms, o dimensionamento é isotropic; ele afeta a largura e altura uniformemente. Rotação dá suporte tanto na superfície bidimensional de tela e no espaço 3D. Não há nenhuma transformação de distorção (ou absoluta) e nenhuma transformação de matriz generalizado.

As transformações têm suporte com oito Propriedades do tipo `double` definidas pela classe `VisualElement`:

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)
- [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)
- [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)
- [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)
- [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)
- [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

Todas essas propriedades são apoiadas por propriedades associáveis. Eles podem ser destinos de vinculação de dados e o estilo. [**Capítulo 22. A animação**](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md) demonstra como essas propriedades podem ser animadas, mas alguns exemplos neste capítulo mostram como você pode animá-las usando o [temporizador](~/xamarin-forms/platform/device.md#devicestarttimer)Xamarin. Forms.

As propriedades de transformação afetam apenas a forma como o elemento é renderizado e *não* afetam como o elemento é percebido no layout.

## <a name="the-translation-transform"></a>A transformação de conversão

Valores diferentes de zero das propriedades [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) e [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) deslocam um elemento horizontalmente ou verticalmente.

O programa [**TranslationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo) permite que você experimente essas propriedades com dois elementos `Slider` que controlam as propriedades `TranslationX` e `TranslationY` de um `Frame`. A transformação também afeta todos os filhos desse `Frame`.

### <a name="text-effects"></a>Efeitos de texto

Um uso comum de propriedades de conversão é Deslocar um pouco a renderização de texto. Isso é demonstrado no exemplo [**Textoffsets**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets) :

[![Captura de tela tripla de deslocamentos de texto](images/ch21fg03-small.png "Deslocamentos de texto")](images/ch21fg03-large.png#lightbox "Deslocamentos de texto")

Outro efeito é para renderizar várias cópias de um `Label` para se assemelhar a um bloco 3D, como demonstrado no exemplo [**BlockText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText) .

### <a name="jumps-and-animations"></a>Saltos e animações

O exemplo [**ButtonJump**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump) usa translação para mover um `Button` sempre que ele é tocado, mas a principal intenção é demonstrar que o `Button` recebe a entrada do usuário no local em que o botão é renderizado.

O exemplo de [**ButtonGlide**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide) é semelhante, mas usa um temporizador para animar o `Button` de um ponto para outro.

## <a name="the-scale-transform"></a>A transformação de escala

A [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) transformação pode aumentar ou diminuir o tamanho renderizado do elemento. O valor padrão é 1. Um valor de 0 faz com que o elemento invisível. Valores negativos fazem com que o elemento seja exibido deve ser girada 180 graus. A propriedade `Scale` não afeta as propriedades `Width` ou `Height` do elemento. Esses valores permanecem os mesmos.

Você pode experimentar a propriedade `Scale` usando o exemplo [**SimpleScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo) .

O exemplo [**ButtonScaler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler) demonstra a diferença entre a animação da propriedade `Scale` de um `Button` e a animação da propriedade `FontSize`. A propriedade `FontSize` afeta como a `Button` é percebida no layout; a propriedade `Scale` não.

O exemplo [**ScaleToSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize) calcula uma propriedade `Scale` que é aplicada a um elemento `Label` para torná-lo o mais grande possível, enquanto ainda se ajusta na página.

### <a name="anchoring-the-scale"></a>Ancoragem de dimensionamento

Os elementos reduzidas horizontalmente em três amostras anteriores todos aumentaram ou diminuíram de tamanho em relação ao centro do elemento. Em outras palavras, o elemento aumenta ou diminui de tamanho igual em todas as direções. Somente o ponto no centro do elemento permanece no mesmo local durante a colocação em escala.

Você pode alterar o centro do dimensionamento definindo as propriedades [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) e [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) . Essas propriedades são em relação ao próprio elemento. Por `AnchorX`, um valor de 0 refere-se ao lado esquerdo do elemento e 1 se refere ao lado direito. Da mesma forma, para `AnchorY`, 0 é a parte superior e 1 é a parte inferior. Ambas as propriedades têm valores padrão de 0,5, que é o centro.

O exemplo [**AnchoredScaleDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo) permite que você experimente as propriedades `AnchorX` e `AnchorY`, bem como a propriedade `Scale`.

No iOS, o uso de valores não padrão de `AnchorX` e `AnchorY` Propriedades é geralmente incompatível com as alterações de orientação do telefone.

## <a name="the-rotation-transform"></a>A transformação de rotação

A propriedade [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) é especificada em graus e indica a rotação horária em um ponto do elemento definido por `AnchorX` e `AnchorY`. O [**PlaneRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo) permite que você experimente essas três propriedades.

### <a name="rotated-text-effects"></a>Efeitos de texto girado

O exemplo [**BoxViewCircle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle) demonstra a matemática necessária para desenhar um círculo usando 64 pequenos elementos de `BoxView` girados.

O exemplo [**RotatedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText) exibe vários elementos de `Label` com a mesma cadeia de texto girada para aparecer como spokes.

O exemplo [**CircularText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText) exibe uma cadeia de texto que parece encapsular um círculo.

### <a name="an-analog-clock"></a>Um relógio analógico

A biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contém uma classe [`AnalogClockViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs) que calcula os ângulos das mãos de um relógio. Para evitar dependências de plataforma no ViewModel, a classe usa `Task.Delay` em vez de um temporizador para localizar um novo valor de `DateTime`.

Também incluído no **Xamarin. FormsBook. Toolkit** é uma classe [`SecondTickConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs) que implementa `IValueConverter` e serve para arredondar um segundo ângulo para o segundo mais próximo.

O [**MinimalBoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock) usa três elementos de rotação `BoxView` para desenhar um relógio analógico.

O [**BoxViewClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock) usa `BoxView` para gráficos mais amplos, incluindo marcas de escala ao longo da face do relógio e as mãos que giram uma pequena distância de suas extremidades:

[![Captura de tela tripla de relógio BoxView](images/ch21fg17-small.png "Face do relógio analógico")](images/ch21fg17-large.png#lightbox "Face do relógio analógico")

Além disso, uma classe [`SecondBackEaseConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs) no **Xamarin. FormsBook. Toolkit** faz com que a segunda mão pareça voltar um pouco antes de passar para a frente e voltar à sua posição correta.

### <a name="vertical-sliders"></a>Controles deslizantes verticais?

O exemplo [**VerticalSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders) demonstra que `Slider` elementos podem ser girados 90 graus e ainda funcionam. No entanto, é difícil posicionar esses elementos de `Slider` girados porque, no layout, eles ainda parecem estar horizontais.

## <a name="3d-ish-rotations"></a>Rotações 3D que

A propriedade [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) parece girar um elemento em volta de um eixo X 3D para que a parte superior e inferior do elemento pareçam mover para frente ou para fora do visualizador. Da mesma forma, a [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) parece girar um elemento em volta do eixo Y para que os lados esquerdo e direito do elemento pareçam ser movidos para frente ou para fora do visualizador.

A propriedade `AnchorX` afeta `RotationY`, mas não `RotationX`. A propriedade `AnchorY` afeta `RotationX`, mas não `RotationY`. Você pode experimentar o exemplo de [**ThreeDeeRotationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo) para explorar as interações dessas propriedades.

O sistema de coordenadas 3D implicado pelo xamarin. Forms é canhoto. Se você apontar o dedo indicador da mão esquerda na direção de X crescente coordena (à direita) e o dedo médio na direção de Y crescente coordena (para baixo), em seguida, seus pontos de thumb na direção de aumentar as coordenadas Z (fora da tela).

Além disso, para qualquer um dos três eixos, se você apontar o polegar esquerdo na direção de aumentar os valores, em seguida, a curva de seus dedos indica a direção de rotação para ângulos de rotação positivas.

## <a name="related-links"></a>Links relacionados

- [CAPÍTULO 21 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [CAPÍTULO 21 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
