---
title: Resumo do capítulo 21. Transformações
description: 'Criando aplicativos móveis com xamarin. Forms: Resumo do capítulo 21. Transformações'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 3642F112-C7FA-4A74-9000-F9087BA89AD9
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 313348952b87d94db63d1682f8e1b9413d56714d
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67513022"
---
# <a name="summary-of-chapter-21-transforms"></a>Resumo do capítulo 21. Transformações

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)

Uma exibição do xamarin. Forms é exibida na tela em um local e o tamanho determinado pelo pai, que geralmente é um `Layout` ou `Layout<View>` derivativo. O *transformar* é um recurso do xamarin. Forms que pode modificar esse local, tamanho ou orientação até mesma.

Xamarin. Forms dá suporte a três tipos básicos de transformações:

- *Tradução* &mdash; Deslocar um elemento horizontal ou verticalmente
- *Escala* &mdash; alterar o tamanho de um elemento
- *Rotação* &mdash; ativar um elemento em torno de um ponto ou de eixo

No xamarin. Forms, o dimensionamento é isotropic; ele afeta a largura e altura uniformemente. Rotação dá suporte tanto na superfície bidimensional de tela e no espaço 3D. Não há nenhuma transformação de distorção (ou absoluta) e nenhuma transformação de matriz generalizado.

Transformações são compatíveis com oito propriedades do tipo `double` definido pelo `VisualElement` classe:

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)
- [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation)
- [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX)
- [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)
- [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX)
- [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY)

Todas essas propriedades são apoiadas por propriedades associáveis. Eles podem ser destinos de vinculação de dados e o estilo. [**Capítulo 22. Animação** ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter22.md) demonstra como essas propriedades podem ser animadas, mas alguns exemplos neste capítulo mostram como é possível animá-los usando o xamarin. Forms [temporizador](~/xamarin-forms/platform/device.md#devicestarttimer).

Transformar propriedades afetam apenas o modo como o elemento é renderizado e fazer *não* afetam como o elemento é percebido no layout.

## <a name="the-translation-transform"></a>A transformação de conversão

Valores diferentes de zero do [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) e [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) propriedades Deslocar um elemento horizontalmente ou verticalmente.

O [ **TranslationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TranslationDemo) programa permite que você pode experimentar com essas propriedades com dois `Slider` elementos que controlam o `TranslationX` e `TranslationY` propriedades de um `Frame`. A transformação também afeta todos os filhos do `Frame`.

### <a name="text-effects"></a>Efeitos de texto

Um uso comum de propriedades de conversão é Deslocar um pouco a renderização de texto. Isso é demonstrado na [ **TextOffsets** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/TextOffsets) exemplo:

[![Captura de tela tripla de deslocamentos de texto](images/ch21fg03-small.png "deslocamentos de texto")](images/ch21fg03-large.png#lightbox "deslocamentos de texto")

Outro efeito é processar várias cópias de um `Label` se pareça com um bloco 3D, como demonstrado a [ **BlockText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BlockText) exemplo.

### <a name="jumps-and-animations"></a>Saltos e animações

O [ **ButtonJump** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonJump) exemplo usa a conversão para mover um `Button` sempre que ele é tocado, mas a intenção principal é demonstrar que o `Button` recebe entrada do usuário no local em que o botão é renderizado.

O [ **ButtonGlide** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonGlide) exemplo é semelhante, mas usa um temporizador para animar o `Button` de um ponto para outro.

## <a name="the-scale-transform"></a>A transformação de escala

O [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) transformação pode aumentar ou diminuir o tamanho do elemento renderizado. O valor padrão é 1. Um valor de 0 faz com que o elemento invisível. Valores negativos fazem com que o elemento seja exibido deve ser girada 180 graus. O `Scale` propriedade não afeta a `Width` ou `Height` propriedades do elemento. Esses valores permanecem os mesmos.

Você pode fazer experiências com o `Scale` propriedade usando o [ **SimpleScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/SimpleScaleDemo) exemplo.

O [ **ButtonScaler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ButtonScaler) exemplo demonstra a diferença entre Animando o `Scale` propriedade de um `Button` e animação a `FontSize` propriedade. O `FontSize` propriedade afeta como o `Button` é percebida no layout; o `Scale` propriedade não faz isso.

O [ **ScaleToSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ScaleToSize) exemplo calcula uma `Scale` propriedade que é aplicada a um `Label` elemento para torná-lo tão grandes quanto possível e que se encaixe dentro da página.

### <a name="anchoring-the-scale"></a>Ancoragem de dimensionamento

Os elementos reduzidas horizontalmente em três amostras anteriores todos aumentaram ou diminuíram de tamanho em relação ao centro do elemento. Em outras palavras, o elemento aumenta ou diminui de tamanho igual em todas as direções. Somente o ponto no centro do elemento permanece no mesmo local durante a colocação em escala.

Você pode alterar o centro do dimensionamento de definindo a [ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX) e [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) propriedades. Essas propriedades são em relação ao próprio elemento. Para `AnchorX`, um valor de 0 refere-se para o lado esquerdo do elemento e 1 refere-se para o lado direito. Da mesma forma para `AnchorY`, 0 é a parte superior e 1 é a parte inferior. Ambas as propriedades têm valores padrão de 0,5, que é o centro.

O [ **AnchoredScaleDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/AnchoredScaleDemo) amostra permite que você experimentar o `AnchorX` e `AnchorY` propriedades, bem como a `Scale` propriedade.

No iOS, com os valores não padrão de `AnchorX` e `AnchorY` propriedades são geralmente incompatíveis com as alterações de orientação do telefone.

## <a name="the-rotation-transform"></a>A transformação de rotação

O [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propriedade é especificada em graus e indica a rotação horária em torno de um ponto do elemento definido pelo `AnchorX` e `AnchorY`. O [ **PlaneRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/PlaneRotationDemo) lhe permite fazer experiências com essas três propriedades.

### <a name="rotated-text-effects"></a>Efeitos de texto girado

O [ **BoxViewCircle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewCircle) exemplo demonstra a matemática necessária para desenhar um círculo usando 64 pequena girado `BoxView` elementos.

O [ **RotatedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/RotatedText) exemplo exibirá vários `Label` elementos com a mesma cadeia de caracteres de texto girado apareçam como spokes.

O [ **CircularText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/CircularText) exemplo exibe uma cadeia de caracteres de texto que aparece para encapsular em um círculo.

### <a name="an-analog-clock"></a>Um relógio analógico

O [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca contém um [ `AnalogClockViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AnalogClockViewModel.cs) classe que calcula os ângulos para mãos de um relógio. Para evitar dependências de plataforma no ViewModel, usa a classe `Task.Delay` em vez de um temporizador para localizar um novo `DateTime` valor.

Também é incluído no **Xamarin.FormsBook.Toolkit** é um [ `SecondTickConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondTickConverter.cs) classe que implementa `IValueConverter` e serve para arredondar um ângulo de segundo para o segundo mais próximo.

O [ **MinimalBoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/MinimalBoxViewClock) usa três girando `BoxView` elementos para desenhar um relógio analógico.

O [ **BoxViewClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/BoxViewClock) usa `BoxView` para gráficos mais amplos, incluindo escala marca em torno a face do relógio e passa ou girar uma pequena distância de suas extremidades:

[![Captura de tela tripla do relógio BoxView](images/ch21fg17-small.png "Face do relógio analógico")](images/ch21fg17-large.png#lightbox "Face do relógio analógico")

Além de uma [ `SecondBackEaseConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/SecondBackEaseConverter.cs) classe **Xamarin.FormsBook.Toolkit** faz com que a mão segundo apareçam retroceder um pouco antes de Avançar e, em seguida, para mover de volta na posição correta.

### <a name="vertical-sliders"></a>Controles deslizantes verticais?

O [ **VerticalSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/VerticalSliders) exemplo demonstra que `Slider` elementos pode ser girados 90 graus e continuam a funcionar. No entanto, é difícil posicionar esses girado `Slider` elementos porque no layout que elas ainda parecem ser horizontal.

## <a name="3d-ish-rotations"></a>Rotações 3D que

O [ `RotationX` ](xref:Xamarin.Forms.VisualElement.RotationX) propriedade aparece para girar um elemento em torno de um eixo x 3D para que a parte superior e inferior do elemento parecem mover na direção ou distante do visualizador. Da mesma forma, o [ `RotationY` ](xref:Xamarin.Forms.VisualElement.RotationY) parece girar um elemento em torno do eixo y para tornar os lados esquerdo e direito do elemento parecer mover na direção ou distante do visualizador.

O `AnchorX` propriedade afeta `RotationY` , mas não `RotationX`. O `AnchorY` propriedade afeta `RotationX` , mas não `RotationY`. Você pode fazer experiências com o [ **ThreeDeeRotationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21/ThreeDeeRotationDemo) exemplo para explorar as interações dessas propriedades.

O sistema de coordenadas 3D implicado pelo xamarin. Forms é canhoto. Se você apontar o dedo indicador da mão esquerda na direção de X crescente coordena (à direita) e o dedo médio na direção de Y crescente coordena (para baixo), em seguida, seus pontos de thumb na direção de aumentar as coordenadas Z (fora da tela).

Além disso, para qualquer um dos três eixos, se você apontar o polegar esquerdo na direção de aumentar os valores, em seguida, a curva de seus dedos indica a direção de rotação para ângulos de rotação positivas.



## <a name="related-links"></a>Links relacionados

- [Capítulo 21 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch21-Apr2016.pdf)
- [Exemplos do capítulo 21](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter21)
