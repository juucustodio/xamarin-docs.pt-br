---
title: "Resumo do capítulo 21. Transformações"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3642F112-C7FA-4A74-9000-F9087BA89AD9
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: a40b4f00fd2a0dd3f61c4882a2ef25b8eb68a3a5
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/15/2018
---
# <a name="summary-of-chapter-21-transforms"></a>Resumo do capítulo 21. Transformações

Um modo de exibição do xamarin. Forms aparece na tela em um local e um tamanho determinado pelo pai, que é geralmente um `Layout` ou `Layout<View>` derivado. O *transformação* é um recurso de xamarin. Forms que pode modificar esse local, tamanho ou até mesmo orientação.

Xamarin. Forms oferece suporte a três tipos básicos de transformações:

- *Tradução* &mdash; Deslocar um elemento horizontal ou verticalmente
- *Escala* &mdash; alterar o tamanho de um elemento
- *Rotação* &mdash; ativar um elemento em torno de um ponto ou um eixo

Xamarin. Forms, a escala é isotropic; ele afeta a largura e altura uniformemente. Rotação tem suporte tanto na superfície bidimensional da tela e no espaço 3D. Não há nenhuma transformação de distorção (ou absoluta) e nenhuma transformação de matriz generalizado.

Transformações têm suporte com oito propriedades do tipo `double` definido pelo `VisualElement` classe:

- [`TranslationX`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/)
- [`TranslationY`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/)
- [`Scale`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/)
- [`Rotation`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/)
- [`RotationX`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationX/)
- [`RotationY`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationY/)
- [`AnchorX`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/)
- [`AnchorY`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/)

Todas essas propriedades são apoiadas por propriedades vinculáveis. Eles podem ser destinos de vinculação de dados e o estilo. [**Capítulo 22. Animação** ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md) demonstra como essas propriedades podem ser animadas, mas alguns exemplos neste capítulo mostram como é possível animá-los usando o xamarin. Forms [timer](~/xamarin-forms/platform/device.md#Device_StartTimer).

Transformar propriedades afetam apenas o modo como o elemento é processado e fazer *não* afetam como o elemento é percebido no layout.

## <a name="the-translation-transform"></a>A transformação de conversão

Valores diferentes de zero do [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/) e [ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/) propriedades Deslocar um elemento horizontal ou verticalmente.

O [ **TranslationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo) programa permite fazer experiências com essas propriedades com dois `Slider` elementos que controlam o `TranslationX` e `TranslationY` propriedades de um `Frame`. A transformação também afeta todos os filhos do que `Frame`.

### <a name="text-effects"></a>Efeitos do texto

Um uso comum de propriedades de conversão é Deslocar um pouco a renderização de texto. Isso é demonstrado no [ **TextOffsets** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets) exemplo:

[![Captura de tela tripla de deslocamentos de texto](images/ch21fg03-small.png "texto desloca")](images/ch21fg03-large.png#lightbox "deslocamentos de texto")

Outro efeito é processar várias cópias de um `Label` se pareça com um bloco 3D, como demonstrado no [ **BlockText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText) exemplo.

### <a name="jumps-and-animations"></a>Saltos e animações

O [ **ButtonJump** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump) exemplo usa a conversão para mover um `Button` sempre que ele é tocado, mas o principal objetivo é demonstrar que a `Button` recebe entrada do usuário no local onde o botão é processado.

O [ **ButtonGlide** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide) exemplo é semelhante, mas usa um temporizador para animar a `Button` de um ponto para outro.

## <a name="the-scale-transform"></a>A transformação de escala

O [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) transformação pode aumentar ou diminuir o tamanho do elemento renderizado. O valor padrão é 1. Um valor de 0 faz com que o elemento seja invisível. Valores negativos fazer com que o elemento apareça ser girados 180 graus. O `Scale` propriedade não afeta a `Width` ou `Height` propriedades do elemento. Esses valores permanecem os mesmos.

Você pode experimentar o `Scale` propriedade usando o [ **SimpleScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo) exemplo.

O [ **ButtonScaler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler) demonstra a diferença entre a animação o `Scale` propriedade de um `Button` e animar a `FontSize` propriedade. O `FontSize` propriedade afeta como o `Button` é percebida no layout; o `Scale` propriedade não.

O [ **ScaleToSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize) exemplo calcula um `Scale` propriedade que é aplicada a um `Label` elemento para torná-lo tão grandes quanto possível e que se encaixe dentro da página.

### <a name="anchoring-the-scale"></a>Ancoragem de escala

Os elementos de escala nos três exemplos anteriores todos aumentaram ou diminuíram de tamanho em relação ao centro do elemento. Em outras palavras, o elemento aumenta ou diminui de tamanho igual em todas as direções. Somente o ponto no centro do elemento permanece no mesmo local durante o dimensionamento.

Você pode alterar o centro do dimensionamento, definindo o [ `AnchorX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/) e [ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/) propriedades. Essas propriedades são relativas ao elemento em si. Para `AnchorX`, um valor de 0 se refere à esquerda do elemento e 1 refere-se à direita. Da mesma forma para `AnchorY`, 0 é a parte superior e 1 é a parte inferior. Ambas as propriedades têm valores padrão de 0,5, que é o centro.

O [ **AnchoredScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo) amostra permite que você experimente o `AnchorX` e `AnchorY` propriedades, bem como a `Scale` propriedade.

No iOS, com os valores não padrão de `AnchorX` e `AnchorY` propriedades é geralmente é incompatível com as alterações de orientação do telefone.

## <a name="the-rotation-transform"></a>A transformação de rotação

O [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) propriedade é especificada em graus e indica uma Rotação horária ao redor de um ponto do elemento definido pelo `AnchorX` e `AnchorY`. O [ **PlaneRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo) permite fazer experiências com essas três propriedades.

### <a name="rotated-text-effects"></a>Efeitos do texto girado

O [ **BoxViewCircle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle) demonstra a matemática necessária desenhar um círculo usando 64 pequena girado `BoxView` elementos.

O [ **RotatedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText) exemplo exibe vários `Label` elementos com a mesma cadeia de caracteres de texto girado apareça como spokes.

O [ **CircularText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText) exemplo exibe uma cadeia de caracteres de texto que aparece para incluir em um círculo.

### <a name="an-analog-clock"></a>Um relógio analógico

O [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca contém um [ `AnalogClockViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs) classe que calcula ângulos para ponteiros de um relógio. Para evitar dependências de plataforma no ViewModel, usa a classe `Task.Delay` em vez de um timer para localizar um novo `DateTime` valor.

Também é incluído no **Xamarin.FormsBook.Toolkit** é um [ `SecondTickConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs) classe que implementa `IValueConverter` e serve para arredondar um ângulo de segundo para o segundo mais próximo.

O [ **MinimalBoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock) usa três girando `BoxView` elementos para desenhar um relógio analógico.

O [ **BoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock) usa `BoxView` para gráficos mais amplo, incluindo escala marca ao redor de face do relógio e encaminha ou girar uma pequena distância de suas extremidades:

[![Captura de tela tripla do relógio BoxView](images/ch21fg17-small.png "Face do relógio analógico")](images/ch21fg17-large.png#lightbox "Face do relógio analógico")

Além de um [ `SecondBackEaseConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs) classe em **Xamarin.FormsBook.Toolkit** faz com que o segundo mão apareça retroceder um pouco antes de Avançar e, em seguida, Voltar na posição correta.

### <a name="vertical-sliders"></a>Controles deslizantes verticais?

O [ **VerticalSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders) exemplo demonstra que `Slider` elementos pode ser girados 90 graus e ainda funcionam. No entanto, é difícil posicionar esses girado `Slider` elementos como no layout, eles ainda parecem ser horizontal.

## <a name="3d-ish-rotations"></a>Rotações de problema associado a 3D

O [ `RotationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationX/) propriedade aparece para girar um elemento em torno de um eixo x 3D para que a parte superior e inferior do elemento parecem mover em direção ou longe do visualizador. Da mesma forma, o [ `RotationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationY/) parece girar um elemento em torno do eixo y para tornar à esquerda e à direita do elemento parece mover em direção ou longe do visualizador.

O `AnchorX` propriedade afeta `RotationY` mas não `RotationX`. O `AnchorY` propriedade afeta `RotationX` mas não `RotationY`. Você pode experimentar o [ **ThreeDeeRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo) exemplo para explorar as interações dessas propriedades.

O sistema de coordenadas 3D implicado xamarin. Forms é canhoto. Se você apontar o dedo indicador da mão esquerda na direção de X crescente coordena (à direita) e coordena o dedo intermediária na direção y crescente (baixo), em seguida, os pontos de thumb a direção de aumentar as coordenadas Z (fora da tela).

Além disso, para qualquer um dos três eixos, se você apontar o polegar esquerdo na direção crescente de valores, em seguida, a curva de seus dedos indica a direção de rotação positivos ângulos de rotação.



## <a name="related-links"></a>Links relacionados

- [Capítulo 21 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [Exemplos de capítulo 21](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
