---
title: Resumo do capítulo 22. Animação
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 22. Animação'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 47C2B9AB-E688-4412-8AF5-9F633B3DA695
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 935be5bd6696600644463eb4ec26410b546f42a0
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2020
ms.locfileid: "70770992"
---
# <a name="summary-of-chapter-22-animation"></a>Resumo do capítulo 22. Animação

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)

Você viu que pode criar suas próprias animações usando o temporizador Xamarin. Forms ou `Task.Delay`, mas é geralmente mais fácil usar os recursos de animação fornecidos pelo Xamarin. Forms. Três classes implementam essas animações:

- [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions), a abordagem de alto nível
- [`Animation`](xref:Xamarin.Forms.Animation), mais versátil, mas mais difícil
- [`AnimationExtension`](xref:Xamarin.Forms.AnimationExtensions), a abordagem mais versátil e de nível mais baixo

Em geral, as animações direcionar as propriedades que são apoiadas por propriedades associáveis. Isso não é um requisito, mas essas são as únicas propriedades que reagem dinamicamente às alterações.

Não há nenhuma interface XAML para essas animações, mas você pode integrar animações em XAML usando técnicas discutidas no [**capítulo 23. Gatilhos e comportamentos**](chapter23.md).

## <a name="exploring-basic-animations"></a>Explorando animações básicas

As funções básicas de animação são métodos de extensão encontrados na classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) . Esses métodos se aplicam a qualquer objeto derivado de `VisualElement`. As animações mais simples visam as propriedades de transformações discutidas em [`Chapter 21. Transforms`](chapter21.md).

O [**AnimationTryout**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout) demonstra como o manipulador de eventos `Clicked` para um `Button` pode chamar o método de extensão [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) para girar o botão em um círculo.

O método `RotateTo` altera a propriedade `Rotation` da `Button` de 0 para 360 durante o curso de um trimestre (por padrão). No entanto, se o `Button` for tocado novamente, ele não fará nada porque a propriedade `Rotation` já é de 360 graus.

### <a name="setting-the-animation-duration"></a>Definindo a duração da animação

O segundo argumento para `RotateTo` é uma duração em milissegundos. Se for definido como um valor grande, tocar na `Button` durante uma animação iniciará uma nova animação começando no ângulo atual.

### <a name="relative-animations"></a>Animações relativas

O método `RelRotateTo` executa uma rotação relativa adicionando um valor especificado ao valor existente. Esse método permite que a `Button` seja tocada várias vezes e cada vez que a propriedade de `Rotation` seja aumentada por 360 graus.

### <a name="awaiting-animations"></a>Aguardando a animações

Todos os métodos de animação no `ViewExtensions` retornam `Task<bool>` objetos. Isso significa que você pode definir uma série de animações sequenciais usando `ContinueWith` ou `await`. O valor de retorno de `bool` conclusão será `false` se a animação tiver terminado sem interrupção ou `true` se ela foi cancelada pelo método [`CancelAnimation`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) , que cancela todas as animações iniciadas pelo outro método no `ViewExtensions` que são definidas no mesmo elemento.

### <a name="composite-animations"></a>Animações de composição

Você pode misturar aguardadas não aguardado animações e para criar animações de composição. Essas são as animações em `ViewExtensions` que visam as propriedades de transformação `TranslationX`, `TranslationY`e `Scale`:

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))

Observe que `TranslateTo` potencialmente afeta as propriedades `TranslationX` e `TranslationY`.

### <a name="taskwhenall-and-taskwhenany"></a>Task. WhenAll e whenany

Também é possível gerenciar animações simultâneas usando [`Task.WhenAll`](xref:System.Threading.Tasks.Task.WhenAll*), que sinaliza quando várias tarefas foram concluídas e [`Task.WhenAny`](xref:System.Threading.Tasks.Task.WhenAny*), que sinaliza quando a primeira de várias tarefas foi concluída.

### <a name="rotation-and-anchors"></a>Rotação e âncoras

Ao chamar os métodos `ScaleTo`, `RelScaleTo`, `RotateTo`e `RelRotateTo`, você pode definir as propriedades [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) e [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) para indicar o centro do dimensionamento e da rotação.

O [**CircleButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton) demonstra essa técnica girando um `Button` em todo o centro da página.

### <a name="easing-functions"></a>Funções de easing

Em geral, as animações são lineares de um valor de início para um valor de término. Funções de easing podem causar animações aumentar ou reduzir a velocidade ao longo de seu curso. O último argumento opcional para os métodos de animação é do tipo [`Easing`](xref:Xamarin.Forms.Easing), uma classe que define 11 campos somente leitura estáticos do tipo `Easing`:

- [`Linear`](xref:Xamarin.Forms.Easing.Linear), o padrão
- [`SinIn`](xref:Xamarin.Forms.Easing.SinIn), [`SinOut`](xref:Xamarin.Forms.Easing.SinOut)e [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut)
- [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn), [`CubicOut`](xref:Xamarin.Forms.Easing.CubicOut)e [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut)
- [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn) e [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut)
- [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) e [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)

O sufixo `In` indica que o efeito está no início da animação, `Out` significa no final e `InOut` significa que está no início e no final da animação.

O exemplo [**BounceButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton) demonstra o uso de funções de atenuação.

### <a name="your-own-easing-functions"></a>Suas próprias funções de easing

Você também pode definir suas próprias funções de atenuação passando um [`Func<double, double>`](xref:System.Func`2) para o [Construtor`Easing`](xref:Xamarin.Forms.Easing.%23ctor(System.Func{System.Double,System.Double})). `Easing` também define uma conversão implícita de `Func<double, double>` para `Easing`. O argumento para a função de easing sempre está no intervalo de 0 a 1 conforme a animação linearmente prossegue do início ao fim. A função *geralmente* retorna um valor no intervalo de 0 a 1, mas pode ser rapidamente negativa ou maior que 1 (como é o caso com as funções `SpringIn` e `SpringOut`) ou pode quebrar as regras se você souber o que está fazendo.

O exemplo [**UneasyScale**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale) demonstra uma função de atenuação personalizada e o [**CustomCubicEase**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase) demonstra outro.

O exemplo [**SwingButton**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton) também demonstra uma função de atenuação personalizada e também uma técnica de alteração das propriedades `AnchorX` e `AnchorY` dentro de uma sequência de animações de rotação.

A biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) tem uma classe [`JiggleButton`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs) que usa uma função de atenuação personalizada para jiggle um botão quando ele é clicado. O exemplo [**JiggleButtonDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo) demonstra isso.

### <a name="entrance-animations"></a>Animações de entrada

Um tipo popular de animação ocorre quando uma página é exibida. Essa animação pode ser iniciada na [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) substituição da página. Para essas animações, o melhor é configurar o XAML para o modo como você deseja que a página apareça *após* a animação e, em seguida, inicializar e animar o layout do código.

O exemplo [**FadingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance) usa o método de extensão [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) para esmaecer o conteúdo da página.

O exemplo [**SlidingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance) usa o método de extensão [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) para deslizar no conteúdo da página dos lados.

O exemplo [**SwingingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance) usa o método de extensão [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) para animar a propriedade `RotationY`. Um método [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) também está disponível.

### <a name="forever-animations"></a>Animações para sempre

Por outro lado, "sempre" animações executado até que o programa é encerrado. Eles geralmente se destina para fins de demonstração.

O exemplo [**FadingTextAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation) usa a animação [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) para esmaecer duas partes do texto para dentro e para fora.

[**PalindromeAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation) exibe um Palindrome e, em seguida, gira em sequência as letras individuais em 180 graus para que elas fiquem todas de cabeça para baixo. Em seguida, a cadeia de caracteres inteira é invertida 180 graus para ler o mesmo que a cadeia de caracteres original.

O exemplo [**CopterAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation) gira uma simples `BoxView` Helicopter enquanto gira em todo o centro da tela.

[**RotatingSpokes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes) gira `BoxView` spoke em todo o centro da tela e, em seguida, gira cada spoke em si para criar padrões interessantes:

[![Captura de tela tripla de spokes giratórias](images/ch22fg21-small.png "Raios de rotação")](images/ch22fg21-large.png#lightbox "Raios de rotação")

No entanto, aumentar progressivamente a propriedade `Rotation` de um elemento pode não funcionar a longo prazo, como demonstra o exemplo de [**RotationBreakdown**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown) .

O exemplo [**SpinningImage**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage) usa [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))e [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) para que pareça que um bitmap está girando no espaço 3D.

### <a name="animating-the-bounds-property"></a>Anime a propriedade de limites

O único método de extensão no `ViewExtensions` ainda não demonstrado é [`LayoutTo`](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)), que efetivamente anima a propriedade [`Bounds`](xref:Xamarin.Forms.VisualElement.Bounds) somente leitura chamando o método [`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) . Esse método é normalmente chamado por `Layout` derivativos, como será discutido no [**capítulo 26. CustomLayouts**](chapter26.md).

O método `LayoutTo` deve ser restrito a finalidades especiais. O programa [**BouncingBox**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox) o usa para compactar e expandir um `BoxView` à medida que ele salta os lados de uma página.

O exemplo [**XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle) usa `LayoutTo` para mover blocos em uma implementação do quebra-cabeça clássico 15-16 que exibe uma imagem embaralhada em vez de blocos numerados:

[![Captura de tela tripla do Xamarin Xuzzle](images/ch22fg26-small.png "Jogo de quebra-cabeça Xuzzle")](images/ch22fg26-large.png#lightbox "Jogo de quebra-cabeça Xuzzle")

### <a name="your-own-awaitable-animations"></a>Suas próprias animações awaitable

O exemplo [**TryAwaitableAnimation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation) cria uma animação awaitable. A classe crucial que pode retornar um objeto `Task` do método e do sinal quando a animação é concluída é [`TaskCompletionSource`](xref:System.Threading.Tasks.TaskCompletionSource`1).

## <a name="deeper-into-animations"></a>Ainda mais a animações

O sistema de animação do xamarin. Forms pode ser um pouco confuso. Além da classe `Easing`, o sistema de animação consiste nas classes `ViewExtensions`, `Animation`e `AnimationExtension`.

### <a name="viewextensions-class"></a>Classe ViewExtensions

Você já viu [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions). Ele define nove métodos que retornam `Task<bool>` e [`CancelAnimations`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)). Sete de destino nove métodos propriedades de transformação. Os outros dois são [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), que tem como alvo a propriedade [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) e [`LayoutTo`](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)), que chama o método [`Layout`](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) .

### <a name="animation-class"></a>Classe de animação

A classe [`Animation`](xref:Xamarin.Forms.AnimationExtensions) tem um [Construtor](xref:Xamarin.Forms.Animation.%23ctor(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Action)) com cinco argumentos para definir o retorno de chamada e os métodos concluídos e os parâmetros da animação.

Animações filhas podem ser adicionadas com [`Add`](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)), [`Insert`](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation)), [`WithConcurrent`](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double))e sobrecarga de [`WithConcurrent`](xref:Xamarin.Forms.Animation.WithConcurrent(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Double,System.Double)).

O objeto de animação é iniciado com uma chamada para o método [`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) .

### <a name="animationextensions-class"></a>Classe AnimationExtensions

A classe [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) contém principalmente métodos de extensão. Há várias versões de um método `Animate` e o método [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) genérico é tão versátil que é, na verdade, a única função de animação de que você precisa.

## <a name="working-with-the-animation-class"></a>Trabalhando com a classe de animação

O exemplo [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) demonstra a classe [`Animation`](xref:Xamarin.Forms.Animation) com várias animações diferentes.

### <a name="child-animations"></a>Animações filho

O exemplo [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) também demonstra animações filhas, que usam os métodos (muito semelhantes) [`Add`](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)) e [`Insert`](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation)) .

### <a name="beyond-the-high-level-animation-methods"></a>Além dos métodos de animação de alto nível

O exemplo de [**ConcurrentAnimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) também demonstra como executar animações que vão além das propriedades direcionadas pelos métodos de `ViewExtensions`. Em um exemplo, uma série de períodos fica mais longa; em outro exemplo, uma propriedade `BackgroundColor` é animada.

### <a name="more-of-your-own-awaitable-methods"></a>Mais de seus próprios métodos awaitable

O método [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) de `ViewExtensions` não funciona com a função [`Easing.SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) . Ele é interrompido quando a saída de easing obtém acima de 1.

A biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contém uma classe [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) com [`TranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12) e [`TranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49) métodos de extensão que não têm esse problema, bem como os métodos [`CancelTranslateXTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44) e [`CancelTranslateYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71) para cancelar essas animações.

O [**SpringSlidingEntrance**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance) demonstra o método `TranslateXTo`.

A classe `MoreExtensions` também contém um método de extensão [`TranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76) que combina a translação X e Y e um método [`CancelTranslateXYTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113) .

### <a name="implementing-a-bezier-animation"></a>Implementando uma animação de Bézier

Também é possível desenvolver uma animação que move um elemento ao longo do caminho de uma spline de Bézier. A biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contém uma estrutura [`BezierSpline`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs) que encapsula uma spline de bézier e uma enumeração [`BezierTangent`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs) para controlar a orientação.

A classe [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) contém um método de extensão [`BezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118) e um método [`CancelBezierPathTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161) .

O exemplo [**BezierLoop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop) demonstra a animação de um elemento ao longo de um caminho beizer.

## <a name="working-with-animationextensions"></a>Trabalhando com AnimationExtensions

Um tipo de animação ausente da coleção padrão é uma animação de cor. O problema é que não há nenhuma maneira correta de interpolar entre dois valores de `Color`. É possível interpolar os valores RGB individuais, mas apenas como válido é interpolação os valores HSL.

Por esse motivo, a classe [`MoreViewExtensions`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) na biblioteca [**Xamarin. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) contém dois métodos de animação `Color`: [`RgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166) e [`HslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188). (Há também dois métodos de cancelamento: [`CancelRgbColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183) e [`CancelHslColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206)).

Ambos os métodos fazem uso de [`ColorAnimation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211), que executa a animação chamando o amplo método [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) genérico em [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions).

O exemplo [**coloranimations**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations) demonstra o uso desses dois tipos de animações coloridas.

## <a name="structuring-your-animations"></a>Estruturar suas animações

Às vezes é útil expressar animações no XAML e usá-los em conjunto com o MVVM. Isso é abordado no próximo capítulo, [**capítulo 23. Gatilhos e comportamentos**](chapter23.md).

## <a name="related-links"></a>Links relacionados

- [Capítulo 22 texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [Capítulo 22 amostras](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [Animação](~/xamarin-forms/user-interface/animation/index.md)
