---
title: Resumo do Capítulo 22. Animação
description: 'Criando aplicativos móveis com Xamarin.Forms: Resumo do Capítulo 22. Animação'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 47C2B9AB-E688-4412-8AF5-9F633B3DA695
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 935be5bd6696600644463eb4ec26410b546f42a0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70770992"
---
# <a name="summary-of-chapter-22-animation"></a>Resumo do Capítulo 22. Animação

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)

Você já viu que pode criar suas próprias animações usando `Task.Delay`o temporizador Xamarin.Forms ou , mas geralmente é mais fácil usando as instalações de animação fornecidas pelo Xamarin.Forms. Três classes implementam essas animações:

- [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions), a abordagem de alto nível
- [`Animation`](xref:Xamarin.Forms.Animation), mais versátil, mas mais difícil
- [`AnimationExtension`](xref:Xamarin.Forms.AnimationExtensions), a abordagem mais versátil e de menor nível

Geralmente, as animações têm como alvo propriedades que são apoiadas por propriedades vinculáveis. Este não é um requisito, mas estas são as únicas propriedades que reagem dinamicamente às mudanças.

Não há interface XAML para essas animações, mas você pode integrar animações em XAML usando técnicas discutidas no [**Capítulo 23. Gatilhos e Comportamentos**](chapter23.md).

## <a name="exploring-basic-animations"></a>Explorando animações básicas

As funções básicas de animação são [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) métodos de extensão encontrados na classe. Esses métodos se aplicam a `VisualElement`qualquer objeto derivado de . As animações mais simples têm como [`Chapter 21. Transforms`](chapter21.md)alvo as propriedades de transformação discutidas em .

O [**AnimationTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout) demonstra `Clicked` como o `Button` manipulador [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) de eventos para um pode chamar o método de extensão para girar o botão em um círculo.

O `RotateTo` método `Rotation` altera a `Button` propriedade do 0 para 360 ao longo de um segundo (por padrão). Se `Button` o é aproveitado novamente, no `Rotation` entanto, ele não faz nada porque a propriedade já está 360 graus.

### <a name="setting-the-animation-duration"></a>Definindo a duração da animação

O segundo `RotateTo` argumento é uma duração em milissegundos. Se definido para um grande `Button` valor, tocar durante uma animação inicia uma nova animação começando no ângulo atual.

### <a name="relative-animations"></a>Animações relativas

O `RelRotateTo` método realiza uma rotação relativa adicionando um valor especificado ao valor existente. Este método `Button` permite que o ser aproveitado várias vezes, e cada vez aumenta a `Rotation` propriedade em 360 graus.

### <a name="awaiting-animations"></a>Aguardando animações

Todos os métodos `ViewExtensions` `Task<bool>` de animação em objetos de retorno. Isso significa que você pode definir uma série `ContinueWith` de `await`animações seqüenciais usando ou . O `bool` valor de `false` retorno de conclusão `true` é se a animação [`CancelAnimation`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) terminou sem interrupção ou se foi `ViewExtensions` cancelada pelo método, que cancela todas as animações iniciadas pelo outro método em que são definidas no mesmo elemento.

### <a name="composite-animations"></a>Animações compostas

Você pode misturar animações aguardadas e não aguardadas para criar animações compostas. Estas são as `ViewExtensions` animações `TranslationX` `TranslationY`em `Scale` que visam as propriedades e transformar propriedades:

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))

Observe `TranslateTo` que potencialmente `TranslationX` afeta `TranslationY` as propriedades e as propriedades.

### <a name="taskwhenall-and-taskwhenany"></a>Tarefa.Quando Tudo e Tarefa.QuandoQualquer

Também é possível gerenciar animações [`Task.WhenAll`](xref:System.Threading.Tasks.Task.WhenAll*)simultâneas usando , que sinaliza [`Task.WhenAny`](xref:System.Threading.Tasks.Task.WhenAny*)quando várias tarefas foram concluídas, e , que sinaliza quando a primeira de várias tarefas foi concluída.

### <a name="rotation-and-anchors"></a>Rotação e âncoras

Ao chamar `ScaleTo` `RelScaleTo`os `RotateTo`métodos , e `RelRotateTo` métodos, você pode definir as [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) propriedades e [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) para indicar o centro de escala e rotação.

O [**CircleButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton) demonstra essa técnica `Button` girando em torno do centro da página.

### <a name="easing-functions"></a>Funções de easing

Geralmente as animações são lineares de um valor inicial para um valor final. As funções de facilitação podem fazer com que as animações acelerem ou diminuam ao longo de seu curso. O último argumento opcional para os [`Easing`](xref:Xamarin.Forms.Easing)métodos de animação é de tipo , `Easing`uma classe que define 11 campos de leitura estática do tipo :

- [`Linear`](xref:Xamarin.Forms.Easing.Linear), o padrão
- [`SinIn`](xref:Xamarin.Forms.Easing.SinIn)E, [`SinOut`](xref:Xamarin.Forms.Easing.SinOut)"E"[`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut)
- [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn)E, [`CubicOut`](xref:Xamarin.Forms.Easing.CubicOut)"E"[`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut)
- [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn)E[`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut)
- [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn)E[`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)

O `In` sufixo indica que o efeito está `Out` no início da `InOut` animação, significa no final, e significa que está no início e no fim da animação.

A amostra [**BounceButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton) demonstra o uso de funções de facilitação.

### <a name="your-own-easing-functions"></a>Suas próprias funções de facilitação

Você também pode definir suas próprias [`Func<double, double>`](xref:System.Func`2) funções de facilitação passando a para o [ `Easing` construtor](xref:Xamarin.Forms.Easing.%23ctor(System.Func{System.Double,System.Double})). `Easing`também define uma conversão `Func<double, double>` `Easing`implícita de . O argumento para a função de facilitação está sempre na faixa de 0 a 1 à medida que a animação prossegue linearmente do início ao fim. A função *geralmente* retorna um valor na faixa de 0 a 1, mas pode ser `SpringIn` brevemente negativa ou maior que 1 (como é o caso com as e `SpringOut` funções) ou pode quebrar as regras se você sabe o que está fazendo.

A amostra [**UneasyScale**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale) demonstra uma função de facilitação personalizada, e [**o CustomCubicEase**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase) demonstra outra.

A amostra [**SwingButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton) também demonstra uma função de facilitação personalizada, e também uma técnica de alterar as `AnchorX` propriedades e `AnchorY` propriedades dentro de uma seqüência de animações de rotação.

A biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) tem uma [`JiggleButton`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs) classe que usa uma função de facilitação personalizada para balançar um botão quando ele é clicado. A amostra [**JiggleButtonDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo) demonstra isso.

### <a name="entrance-animations"></a>Animações de entrada

Um tipo popular de animação ocorre quando uma página aparece pela primeira vez. Tal animação pode ser [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) iniciada na substituição da página. Para essas animações, é melhor configurar o XAML para saber como você deseja que a página apareça após a animação e, *em* seguida, inicialize e anime o layout a partir do código.

A amostra [**FadingEntry**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance) usa o [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método de extensão para desaparecer no conteúdo da página.

A amostra [**SlidingEntry**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance) usa o [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) método de extensão para deslizar o conteúdo da página a partir dos lados.

A amostra [**SwingingEntry**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance) usa o [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método `RotationY` de extensão para animar a propriedade. Um [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método também está disponível.

### <a name="forever-animations"></a>Animações para sempre

No outro extremo, animações "para sempre" são executadas até que o programa seja encerrado. Estes são geralmente destinados para fins de demonstração.

A amostra [**FadingTextAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation) usa [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) animação para desbotar duas peças de texto dentro e fora.

[**PalindromeAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation) exibe um palíndromo, e então sequencialmente gira as letras individuais em 180 graus para que todas estejam de cabeça para baixo. Em seguida, toda a seqüência é invertida 180 graus para ler o mesmo que a seqüência original.

A amostra [**CopterAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation) gira `BoxView` um helicóptero simples enquanto gira em torno do centro da tela.

[**O RotatingSpokes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes) gira `BoxView` em torno do centro da tela e, em seguida, gira cada um falou para criar padrões interessantes:

[![Captura de tela tripla de raios rotativos](images/ch22fg21-small.png "Raios Rotativos")](images/ch22fg21-large.png#lightbox "Raios Rotativos")

No entanto, `Rotation` aumentar progressivamente a propriedade de um elemento pode não funcionar a longo prazo, como demonstra a amostra [**RotationBreakdown.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown)

A amostra [**SpinningImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage) usa [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))e [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) para fazer parecer que um bitmap está girando em espaço 3D.

### <a name="animating-the-bounds-property"></a>Animando a propriedade dos limites

O único método `ViewExtensions` de extensão ainda não demonstrado é, [`LayoutTo`](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)) [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds) que efetivamente [`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) anima a propriedade somente leitura, chamando o método. Este método é `Layout` normalmente chamado por derivativos como será discutido no [**Capítulo 26. Layouts personalizados**](chapter26.md).

O `LayoutTo` método deve ser restrito a propósitos especiais. O programa [**BouncingBox**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox) usa-o para `BoxView` comprimir e expandir à medida que ele rebate os lados de uma página.

A amostra [**XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle) é usa `LayoutTo` para mover telhas em uma implementação do clássico quebra-cabeça 15-16 que exibe uma imagem mexida em vez de telhas numeradas:

[![Captura tripla de Xamarin Xuzzle](images/ch22fg26-small.png "Jogo de quebra-cabeça de Xuzzle")](images/ch22fg26-large.png#lightbox "Jogo de quebra-cabeça de Xuzzle")

### <a name="your-own-awaitable-animations"></a>Suas próprias animações aguardadas

A amostra [**TryAwaitableAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation) cria uma animação aguardável. A classe crucial que `Task` pode retornar um objeto do método [`TaskCompletionSource`](xref:System.Threading.Tasks.TaskCompletionSource`1)e sinalizar quando a animação estiver concluída é .

## <a name="deeper-into-animations"></a>Mais profundo em animações

O sistema de animação Xamarin.Forms pode ser um pouco confuso. Além `Easing` da classe, o sistema de `ViewExtensions` `Animation`animação `AnimationExtension` é composto pelas classes e classes.

### <a name="viewextensions-class"></a>Exibir classe Extensões

Você já viu. [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) Ele define nove métodos `Task<bool>` [`CancelAnimations`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement))que retornam e . Sete dos nove métodos visam transformar propriedades. Os outros [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))dois são , [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) que [`LayoutTo`](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing))tem como [`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) alvo a propriedade, e , que chama o método.

### <a name="animation-class"></a>Aula de animação

A [`Animation`](xref:Xamarin.Forms.AnimationExtensions) classe tem um [construtor](xref:Xamarin.Forms.Animation.%23ctor(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Action)) com cinco argumentos para definir métodos de retorno e acabamento, e parâmetros da animação.

As animações infantis [`Add`](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation))podem [`Insert`](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation)) [`WithConcurrent`](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double))ser adicionadas [`WithConcurrent`](xref:Xamarin.Forms.Animation.WithConcurrent(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Double,System.Double))com , e sobrecarga de .

O objeto de animação é então [`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) iniciado com uma chamada para o método.

### <a name="animationextensions-class"></a>Classe AnimationExtensions

A [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) classe contém principalmente métodos de extensão. Existem várias `Animate` versões [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) de um método e o método genérico é tão versátil que é realmente a única função de animação que você precisa.

## <a name="working-with-the-animation-class"></a>Trabalhando com a classe de animação

A amostra [**SimultâneaAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) [`Animation`](xref:Xamarin.Forms.Animation) demonstra a classe com várias animações diferentes.

### <a name="child-animations"></a>Animações infantis

A amostra [**SimultâneaAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) também demonstra animações infantis, que fazem [`Add`](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)) [`Insert`](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation)) uso dos métodos (muito semelhantes).

### <a name="beyond-the-high-level-animation-methods"></a>Além dos métodos de animação de alto nível

A amostra [**SimultâneaAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) também demonstra como executar animações que vão `ViewExtensions` além das propriedades visadas pelos métodos. Em um exemplo, uma série de períodos fica mais longo; em outro exemplo, uma `BackgroundColor` propriedade é animada.

### <a name="more-of-your-own-awaitable-methods"></a>Mais de seus próprios métodos aguardados

O [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) método `ViewExtensions` de não funciona [`Easing.SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) com a função. Ele pára quando a saída de facilitação fica acima de 1.

A biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contém [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) uma classe [`TranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12) com métodos de extensão [`CancelTranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44) e [`CancelTranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71) [`TranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49) classe que não têm esse problema, bem como métodos para cancelar essas animações.

O [**SpringSlidingEntry**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance) `TranslateXTo` demonstra o método.

A `MoreExtensions` classe também [`TranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76) contém um método de extensão [`CancelTranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113) que combina tradução x e y, e um método.

### <a name="implementing-a-bezier-animation"></a>Implementando uma animação bezier

Também é possível desenvolver uma animação que mova um elemento ao longo do caminho de um spline bezier. A biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contém uma [`BezierSpline`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs) estrutura que encapsula uma [`BezierTangent`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs) linha de bezier e uma enumeração para controlar a orientação.

A [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe [`BezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118) contém um [`CancelBezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161) método de extensão e um método.

A amostra [**bezierLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop) demonstra animar um elemento ao longo de um caminho beizer.

## <a name="working-with-animationextensions"></a>Trabalhando com extensões de animação

Um tipo de animação ausente da coleção padrão é uma animação colorida. O problema é que não há uma maneira `Color` certa de interpolar entre dois valores. É possível interpolar os valores individuais de RGB, mas igualmente válido é interpolar os valores do HSL.

Por [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) essa razão, a classe na biblioteca [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contém dois `Color` métodos de animação: [`RgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166) e [`HslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188). (Há também dois métodos [`CancelRgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183) [`CancelHslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206)de cancelamento: e ).

Ambos os métodos [`ColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211)fazem uso de , que [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) executa a animação chamando o método genérico extensivo em [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions).

A amostra [**ColorAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations) demonstra o uso desses dois tipos de animações coloridas.

## <a name="structuring-your-animations"></a>Estruturando suas animações

Às vezes é útil expressar animações em XAML e usá-las em conjunto com a MVVM. Isso é abordado no próximo capítulo, [**Capítulo 23. Gatilhos e Comportamentos**](chapter23.md).

## <a name="related-links"></a>Links relacionados

- [Capítulo 22 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [Capítulo 22 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [Animação](~/xamarin-forms/user-interface/animation/index.md)
