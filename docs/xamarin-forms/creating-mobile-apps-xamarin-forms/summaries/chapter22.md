---
title: Resumo do capítulo 22. Animação
description: 'Criando aplicativos móveis com Xamarin.Forms : Resumo do capítulo 22. Animação'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 47C2B9AB-E688-4412-8AF5-9F633B3DA695
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f1eca69ac9e5ff802132041f4084a86c3443d745
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373998"
---
# <a name="summary-of-chapter-22-animation"></a>Resumo do capítulo 22. Animação

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)

> [!NOTE]
> Este livro foi publicado na Primavera de 2016 e não foi atualizado desde então. Há muito no livro que permanece valioso, mas alguns materiais estão desatualizados e alguns tópicos não estão mais totalmente corretos ou completos.

Você viu que pode criar suas próprias animações usando o Xamarin.Forms temporizador ou `Task.Delay` , mas é geralmente mais fácil usar os recursos de animação fornecidos pelo Xamarin.Forms . Três classes implementam essas animações:

- [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions), a abordagem de alto nível
- [`Animation`](xref:Xamarin.Forms.Animation), mais versátil, mas mais difícil
- [`AnimationExtension`](xref:Xamarin.Forms.AnimationExtensions), a abordagem mais versátil e de nível mais baixo

Em geral, as animações direcionam as propriedades que são apoiadas pelas propriedades vinculáveis. Isso não é um requisito, mas essas são as únicas propriedades que reagem dinamicamente às alterações.

Não há nenhuma interface XAML para essas animações, mas você pode integrar animações em XAML usando técnicas discutidas no [**capítulo 23. Gatilhos e comportamentos**](chapter23.md).

## <a name="exploring-basic-animations"></a>Explorando animações básicas

As funções básicas de animação são métodos de extensão encontrados na [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe. Esses métodos se aplicam a qualquer objeto derivado de `VisualElement` . As animações mais simples visam as propriedades de transformações discutidas em [`Chapter 21. Transforms`](chapter21.md) .

O [**AnimationTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout) demonstra como o `Clicked` manipulador de eventos para um `Button` pode chamar o [ `RotateTo` ] (xref: Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) o método de extensão para girar o botão em um círculo.

O `RotateTo` método altera a `Rotation` propriedade de `Button` 0 a 360 durante o curso de um trimestre (por padrão). `Button`No entanto, se o for tocado novamente, ele não fará nada porque a `Rotation` Propriedade já é de 360 graus.

### <a name="setting-the-animation-duration"></a>Definindo a duração da animação

O segundo argumento para `RotateTo` é uma duração em milissegundos. Se for definido como um valor grande, tocar no `Button` durante uma animação iniciará uma nova animação começando no ângulo atual.

### <a name="relative-animations"></a>Animações relativas

O `RelRotateTo` método executa uma rotação relativa adicionando um valor especificado ao valor existente. Esse método permite que o seja `Button` tocado várias vezes e cada vez aumenta a `Rotation` propriedade por 360 graus.

### <a name="awaiting-animations"></a>Aguardando animações

Todos os métodos de animação `ViewExtensions` em `Task<bool>` objetos de retorno. Isso significa que você pode definir uma série de animações sequenciais usando o `ContinueWith` ou o `await` . O `bool` valor de retorno de conclusão é `false` se a animação foi concluída sem interrupção ou `true` se foi cancelada pelo [ `CancelAnimation` ] (xref: Xamarin.Forms . ViewExtensions. CancelAnimations ( Xamarin.Forms . Visualelement)), que cancela todas as animações iniciadas pelo outro método no `ViewExtensions` que são definidas no mesmo elemento.

### <a name="composite-animations"></a>Animações compostas

Você pode misturar animações esperadas e não esperadas para criar animações compostas. Essas são as animações nesse `ViewExtensions` destino as `TranslationX` Propriedades de `TranslationY` transformação, e `Scale` :

- [ `TranslateTo` ] (xref: Xamarin.Forms . ViewExtensions. transtardiato ( Xamarin.Forms . Visualelement, System. Double, System. Double, System. UInt32, Xamarin.Forms . Atenuação))
- [ `ScaleTo` ] (xref: Xamarin.Forms . ViewExtensions. ScaleTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Atenuação))
- [ `RelScaleTo` ] (xref: Xamarin.Forms . ViewExtensions. RelScaleTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Atenuação))

Observe que `TranslateTo` , potencialmente, afeta `TranslationX` as `TranslationY` Propriedades e.

### <a name="taskwhenall-and-taskwhenany"></a>Task. WhenAll e Task. WhenAny

Também é possível gerenciar animações simultâneas usando [`Task.WhenAll`](xref:System.Threading.Tasks.Task.WhenAll*) o, que sinaliza quando várias tarefas foram concluídas e [`Task.WhenAny`](xref:System.Threading.Tasks.Task.WhenAny*) , que sinaliza quando a primeira de várias tarefas foi concluída.

### <a name="rotation-and-anchors"></a>Rotação e âncoras

Ao chamar os `ScaleTo` `RelScaleTo` métodos,, `RotateTo` e `RelRotateTo` , você pode definir as [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) Propriedades e [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) para indicar o centro de dimensionamento e rotação.

O [**CircleButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton) demonstra essa técnica girando um `Button` em volta do centro da página.

### <a name="easing-functions"></a>Funções de easing

Em geral, as animações são lineares de um valor inicial para um valor final. A atenuação de funções pode fazer com que as animações acelerem ou diminuam seu curso. O último argumento opcional para os métodos de animação é do tipo [`Easing`](xref:Xamarin.Forms.Easing) , uma classe que define 11 campos de somente leitura estáticos do tipo `Easing` :

- [`Linear`](xref:Xamarin.Forms.Easing.Linear), o padrão
- [`SinIn`](xref:Xamarin.Forms.Easing.SinIn) , [`SinOut`](xref:Xamarin.Forms.Easing.SinOut) e [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut)
- [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn) , [`CubicOut`](xref:Xamarin.Forms.Easing.CubicOut) e [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut)
- [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn) e [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut)
- [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) e [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)

O `In` sufixo indica que o efeito está no início da animação, `Out` significa no final e `InOut` significa que ele está no início e no final da animação.

O exemplo [**BounceButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton) demonstra o uso de funções de atenuação.

### <a name="your-own-easing-functions"></a>Suas próprias funções de atenuação

Você também pode definir suas próprias funções de atenuação passando um [`Func<double, double>`](xref:System.Func`2) para o [ `Easing` Construtor](xref:Xamarin.Forms.Easing.%23ctor(System.Func{System.Double,System.Double})). `Easing` também define uma conversão implícita de `Func<double, double>` para `Easing` . O argumento para a função de atenuação está sempre no intervalo de 0 a 1, uma vez que a animação prossegue linearmente do início ao fim. A função *geralmente* retorna um valor no intervalo de 0 a 1, mas pode ser rapidamente negativa ou maior que 1 (como é o caso com as `SpringIn` funções e `SpringOut` ) ou pode quebrar as regras se você souber o que está fazendo.

O exemplo [**UneasyScale**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale) demonstra uma função de atenuação personalizada e o [**CustomCubicEase**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase) demonstra outro.

O exemplo [**SwingButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton) também demonstra uma função de atenuação personalizada e também uma técnica de alterar `AnchorX` as `AnchorY` Propriedades e dentro de uma sequência de animações de rotação.

A biblioteca [**Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) tem uma [`JiggleButton`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs) classe que usa uma função de atenuação personalizada para jiggle um botão quando ele é clicado. O exemplo [**JiggleButtonDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo) demonstra isso.

### <a name="entrance-animations"></a>Animações de entrada

Um tipo popular de animação ocorre quando uma página é exibida pela primeira vez. Essa animação pode ser iniciada na [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) substituição da página. Para essas animações, o melhor é configurar o XAML para o modo como você deseja que a página apareça *após* a animação e, em seguida, inicializar e animar o layout do código.

O exemplo [**FadingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance) usa o [ `FadeTo` ] (xref: Xamarin.Forms . ViewExtensions. Fadeto ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) o método de extensão para esmaecer o conteúdo da página.

O exemplo [**SlidingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance) usa o [ `TranslateTo` ] (xref: Xamarin.Forms . ViewExtensions. transtardiato ( Xamarin.Forms . Visualelement, System. Double, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) o método de extensão para deslizar no conteúdo da página dos lados.

O exemplo [**SwingingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance) usa o [ `RotateYTo` ] (xref: Xamarin.Forms . ViewExtensions. RotateYTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) o método de extensão para animar a `RotationY` propriedade. Um [ `RotateXTo` ] (xref: Xamarin.Forms . ViewExtensions. RotateXTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando a disposição)) o método também está disponível.

### <a name="forever-animations"></a>Animações para sempre

No outro extremo, as animações "para sempre" são executadas até que o programa seja encerrado. Geralmente, eles são destinados a fins de demonstração.

O exemplo [**FadingTextAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation) usa [ `FadeTo` ] (xref: Xamarin.Forms . ViewExtensions. Fadeto ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) a animação para esmaecer duas partes do texto para dentro e para fora.

[**PalindromeAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation) exibe um Palindrome e, em seguida, gira em sequência as letras individuais em 180 graus para que elas fiquem todas de cabeça para baixo. Em seguida, a cadeia de caracteres inteira é invertida em 180 graus para ser lido da mesma forma que a cadeia de caracteres original.

O exemplo [**CopterAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation) gira um Helicopter simples `BoxView` enquanto gira em todo o centro da tela.

O [**RotatingSpokes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes) gira `BoxView` em volta do centro da tela e, em seguida, gira cada spoke para criar padrões interessantes:

[![Captura de tela tripla de spokes giratórias](images/ch22fg21-small.png "Raios de rotação")](images/ch22fg21-large.png#lightbox "Raios de rotação")

No entanto, aumentar progressivamente a `Rotation` propriedade de um elemento pode não funcionar a longo prazo, como demonstra o exemplo de [**RotationBreakdown**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown) .

O exemplo [**SpinningImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage) usa [ `RotateTo` ] (xref: Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)), [ `RotateXTo` ] (xref: Xamarin.Forms . ViewExtensions. RotateXTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Atenuação)) e [ `RotateYTo` ] (xref: Xamarin.Forms . ViewExtensions. RotateYTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando a impressão)) para que pareça que um bitmap está girando no espaço 3D.

### <a name="animating-the-bounds-property"></a>Animando a propriedade Bounds

O único método de extensão `ViewExtensions` ainda não demonstrado é [ `LayoutTo` ] (xref: Xamarin.Forms . ViewExtensions. Layoutto ( Xamarin.Forms . Visualelement, Xamarin.Forms . Rectangle, System. UInt32, Xamarin.Forms . Facilitando)), que anima efetivamente a propriedade somente leitura [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds) chamando [ `Layout` ] (xref: Xamarin.Forms . Visualelement. layout ( Xamarin.Forms . Rectangle)). Esse método é normalmente chamado por `Layout` derivativos, como será discutido no [**capítulo 26. CustomLayouts**](chapter26.md).

O `LayoutTo` método deve ser restrito a finalidades especiais. O programa [**BouncingBox**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox) o usa para compactar e expandir um `BoxView` à medida que ele salta os lados de uma página.

O exemplo [**XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle) usa `LayoutTo` para mover blocos em uma implementação do quebra-cabeça clássico 15-16 que exibe uma imagem embaralhada em vez de blocos numerados:

[![Captura de tela tripla do Xamarin Xuzzle](images/ch22fg26-small.png "Jogo de quebra-cabeça Xuzzle")](images/ch22fg26-large.png#lightbox "Jogo de quebra-cabeça Xuzzle")

### <a name="your-own-awaitable-animations"></a>Suas próprias animações awaitable

O exemplo [**TryAwaitableAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation) cria uma animação awaitable. A classe crucial que pode retornar um `Task` objeto do método e do sinal quando a animação é concluída é [`TaskCompletionSource`](xref:System.Threading.Tasks.TaskCompletionSource`1) .

## <a name="deeper-into-animations"></a>Mais profundo em animações

O Xamarin.Forms sistema de animação pode ser um pouco confuso. Além da `Easing` classe, o sistema de animação consiste nas `ViewExtensions` `Animation` classes, e `AnimationExtension` .

### <a name="viewextensions-class"></a>Classe ViewExtensions

Você já viu [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) . Ele define nove métodos que retornam `Task<bool>` e [ `CancelAnimations` ] (xref: Xamarin.Forms . ViewExtensions. CancelAnimations ( Xamarin.Forms . Visualelement)). Sete dos nove métodos são propriedades de transformação de destino. Os outros dois são [ `FadeTo` ] (xref: Xamarin.Forms . ViewExtensions. Fadeto ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)), que tem como alvo a [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) propriedade e [ `LayoutTo` ] (xref: Xamarin.Forms . ViewExtensions. Layoutto ( Xamarin.Forms . Visualelement, Xamarin.Forms . Rectangle, System. UInt32, Xamarin.Forms . Facilitando)), que chama o [ `Layout` ] (xref: Xamarin.Forms . Visualelement. layout ( Xamarin.Forms . Rectangle)).

### <a name="animation-class"></a>Classe de animação

A [`Animation`](xref:Xamarin.Forms.AnimationExtensions) classe tem um [constructor] (xref: Xamarin.Forms . Animação .% 23ctor (System. Action {System. Double}, System. Double, System. Double, Xamarin.Forms . Facilitando, System. Action) com cinco argumentos para definir o retorno de chamada e os métodos concluídos e os parâmetros da animação.

Animações filhas podem ser adicionadas com [ `Add` ] (xref: Xamarin.Forms . Animation. Add (System. Double, System. Double, Xamarin.Forms . Animation)), [ `Insert` ] (xref: Xamarin.Forms . Animation. Insert (System. Double, System. Double, Xamarin.Forms . Animation)), [ `WithConcurrent` ] (xref: Xamarin.Forms . Animation. WithConcurrent ( Xamarin.Forms . Animação, System. Double, System. Double) e sobrecarga de [ `WithConcurrent` ] (xref: Xamarin.Forms . Animation. WithConcurrent (System. Action {System. Double}, System. Double, System. Double, Xamarin.Forms . Facilitando, sistema. duplo, sistema. duplo)).

O objeto de animação é iniciado com uma chamada para o [ `Commit` ] (xref: Xamarin.Forms . Animation. Commit ( Xamarin.Forms . IAnimatable, System. String, System. UInt32, System. UInt32, Xamarin.Forms . Método de atenuação, System. Action {System. Double, System. booliano}, System. Func {System. Boolean})).

### <a name="animationextensions-class"></a>Classe AnimationExtensions

A [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) classe contém principalmente métodos de extensão. Há várias versões de um `Animate` método e o método genérico [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) é tão versátil que é realmente a única função de animação de que você precisa.

## <a name="working-with-the-animation-class"></a>Trabalhando com a classe Animation

O exemplo [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) demonstra a [`Animation`](xref:Xamarin.Forms.Animation) classe com várias animações diferentes.

### <a name="child-animations"></a>Animações filhas

O exemplo [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) também demonstra animações filhas, que fazem uso do (muito semelhante) [ `Add` ] (xref: Xamarin.Forms . Animation. Add (System. Double, System. Double, Xamarin.Forms . Animation)) e [ `Insert` ] (xref: Xamarin.Forms . Animation. Insert (System. Double, System. Double, Xamarin.Forms . Animation)).

### <a name="beyond-the-high-level-animation-methods"></a>Além dos métodos de animação de alto nível

O exemplo [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) também demonstra como executar animações que vão além das propriedades direcionadas pelos `ViewExtensions` métodos. Em um exemplo, uma série de períodos fica mais longa; em outro exemplo, uma `BackgroundColor` propriedade é animada.

### <a name="more-of-your-own-awaitable-methods"></a>Mais de seus próprios métodos que são awaitable

O [ `TranslateTo` ] (xref: Xamarin.Forms . ViewExtensions. transtardiato ( Xamarin.Forms . Visualelement, System. Double, System. Double, System. UInt32, Xamarin.Forms . Facilitando a tarefa)) o método de `ViewExtensions` não funciona com a [`Easing.SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) função. Ele para quando a saída de atenuação é acima de 1.

A biblioteca [**Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contém uma [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe com [`TranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12) e [`TranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49) métodos de extensão que não têm esse problema, bem como [`CancelTranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44) os [`CancelTranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71) métodos para cancelar essas animações.

O [**SpringSlidingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance) demonstra o `TranslateXTo` método.

A `MoreExtensions` classe também contém um  [`TranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76) método de extensão que combina a translação X e Y e um [`CancelTranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113) método.

### <a name="implementing-a-bezier-animation"></a>Implementando uma animação de Bézier

Também é possível desenvolver uma animação que move um elemento ao longo do caminho de um spline de Bézier. A biblioteca [**Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contém uma [`BezierSpline`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs) estrutura que encapsula uma spline de Bézier e uma [`BezierTangent`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs) enumeração para controlar a orientação.

A [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe contém um [`BezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118) método de extensão e um [`CancelBezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161) método.

O exemplo [**BezierLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop) demonstra a animação de um elemento ao longo de um caminho beizer.

## <a name="working-with-animationextensions"></a>Trabalhando com AnimationExtensions

Um tipo de animação ausente na coleção Standard é uma animação colorida. O problema é que não há nenhuma maneira correta de interpolar entre dois `Color` valores. É possível interpolar os valores RGB individuais, mas assim como válido está interpolando os valores HSL.

Por esse motivo, a [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe na biblioteca [**Xamarin.Forms book. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contém dois `Color` métodos de animação: [`RgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166) e [`HslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188) . (Também há dois métodos de cancelamento: [`CancelRgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183) e [`CancelHslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206) ).

Ambos os métodos usam [`ColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211) o, que executa a animação chamando o amplo método genérico [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) no [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) .

O exemplo [**coloranimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations) demonstra o uso desses dois tipos de animações coloridas.

## <a name="structuring-your-animations"></a>Estruturando suas animações

Às vezes, é útil expressar animações em XAML e usá-las em conjunto com o MVVM. Isso é abordado no próximo capítulo, [**capítulo 23. Gatilhos e comportamentos**](chapter23.md).

## <a name="related-links"></a>Links relacionados

- [Capítulo 22 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [Capítulo 22 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [Animação](~/xamarin-forms/user-interface/animation/index.md)
