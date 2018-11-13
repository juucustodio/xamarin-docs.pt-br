---
title: Resumo do capítulo 22. Animação
description: 'Criação de aplicativos móveis com xamarin. Forms: Resumo do capítulo 22. Animação'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 47C2B9AB-E688-4412-8AF5-9F633B3DA695
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 82ffecd41115bc91df9732793f5e22e721dbdee4
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563895"
---
# <a name="summary-of-chapter-22-animation"></a>Resumo do capítulo 22. Animação

Você já viu que você pode criar suas próprias animações usando o temporizador de xamarin. Forms ou `Task.Delay`, mas é geralmente mais fácil usando os recursos de animação fornecidos pelo xamarin. Forms. Três classes implementam essas animações:

- [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions), a abordagem de alto nível
- [`Animation`](xref:Xamarin.Forms.Animation), mais versátil mas é mais difícil
- [`AnimationExtension`](xref:Xamarin.Forms.AnimationExtensions), a maioria dos abordagem versátil, de nível inferior

Em geral, as animações direcionar as propriedades que são apoiadas por propriedades associáveis. Isso não é um requisito, mas essas são as únicas propriedades que reagem dinamicamente às alterações.

Não há nenhuma interface XAML para essas animações, mas você pode integrar as animações em XAML usando as técnicas discutidas [ **capítulo 23. Gatilhos e comportamentos**](chapter23.md).

## <a name="exploring-basic-animations"></a>Explorando animações básicas

As funções básicas de animação são métodos de extensão encontrados na [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe. Esses métodos se aplicam a qualquer objeto que deriva de `VisualElement`. As animações mais simples de destino as transformações propriedades discutidas [ `Chapter 21. Transforms` ](chapter21.md).

O [ **AnimationTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout) demonstra como o `Clicked` manipulador de eventos para um `Button` pode chamar o [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método de extensão para girar o botão em um círculo.

O `RotateTo` alterações de método de `Rotation` propriedade do `Button` de 0 a 360 ao longo de um quarto de segundo (por padrão). Se o `Button` é tocado novamente, no entanto, ela não faz nada porque o `Rotation` propriedade já é 360 graus.

### <a name="setting-the-animation-duration"></a>Definindo a duração da animação

O segundo argumento para `RotateTo` é uma duração em milissegundos. Se definido como um valor grande, tocando o `Button` durante uma animação inicia um novo início de animação no ângulo atual.

### <a name="relative-animations"></a>Animações relativas

O `RelRotateTo` método executa uma rotação relativa, adicionando um valor especificado ao valor existente. Esse método permite que o `Button` a ser tocado várias vezes e cada hora aumenta a `Rotation` propriedade por 360 graus.

### <a name="awaiting-animations"></a>Aguardando a animações

Todos os métodos na animação `ViewExtensions` retornar `Task<bool>` objetos. Isso significa que você pode definir uma série de animações sequenciais usando `ContinueWith` ou `await`. O `bool` conclusão valor retornado é `false` se a animação é concluída sem interrupção ou `true` se ela foi cancelada pelo [ `CancelAnimation` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) método, o que cancela todas as animações iniciadas pela outro método em `ViewExtensions` que são definidas no mesmo elemento.

### <a name="composite-animations"></a>Animações de composição

Você pode misturar aguardadas não aguardado animações e para criar animações de composição. Essas são as animações no `ViewExtensions` que se destinam a `TranslationX`, `TranslationY`, e `Scale` propriedades de transformação:

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))

Observe que `TranslateTo` potencialmente afeta ambas as `TranslationX` e `TranslationY` propriedades.

### <a name="taskwhenall-and-taskwhenany"></a>Task. WhenAll e whenany

Também é possível gerenciar animações simultâneas usando [ `Task.WhenAll` ](xref:System.Threading.Tasks.Task.WhenAll*), que sinaliza quando várias tarefas todas concluíram, e [ `Task.WhenAny` ](xref:System.Threading.Tasks.Task.WhenAny*), que sinaliza quando o primeiro de vários tarefas foi concluída.

### <a name="rotation-and-anchors"></a>Rotação e âncoras

Ao chamar o `ScaleTo`, `RelScaleTo`, `RotateTo`, e `RelRotateTo` métodos, você pode definir o [ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX) e [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) propriedades para indicar o Centro de escala e rotação.

O [ **CircleButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton) demonstra essa técnica, revolução um `Button` em torno do centro da página.

### <a name="easing-functions"></a>Funções de easing

Em geral, as animações são lineares de um valor de início para um valor de término. Funções de easing podem causar animações aumentar ou reduzir a velocidade ao longo de seu curso. O último argumento opcional para os métodos de animação é do tipo [ `Easing` ](xref:Xamarin.Forms.Easing), uma classe que define 11 campos estáticos somente leitura do tipo `Easing`:

- [`Linear`](xref:Xamarin.Forms.Easing.Linear), o padrão
- [`SinIn`](xref:Xamarin.Forms.Easing.SinIn), [ `SinOut` ](xref:Xamarin.Forms.Easing.SinOut), e [`SinInOut`](xref:Xamarin.Forms.Easing.SinInOut)
- [`CubicIn`](xref:Xamarin.Forms.Easing.CubicIn), [ `CubicOut` ](xref:Xamarin.Forms.Easing.CubicOut), e [`CubicInOut`](xref:Xamarin.Forms.Easing.CubicInOut)
- [`BounceIn`](xref:Xamarin.Forms.Easing.BounceIn) e [`BounceOut`](xref:Xamarin.Forms.Easing.BounceOut)
- [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) e [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut)

O `In` sufixo indica que o efeito é o início da animação, `Out` significa que no final, e `InOut` significa que ele está no início e no final da animação.

O [ **BounceButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton) exemplo demonstra o uso de funções de easing.

### <a name="your-own-easing-functions"></a>Suas próprias funções de easing

Você também pode definir a você próprias funções de easing, passando um [ `Func<double, double>` ](xref:System.Func`2) para o [ `Easing` construtor](xref:Xamarin.Forms.Easing.%23ctor(System.Func{System.Double,System.Double})). `Easing` também define uma conversão implícita da `Func<double, double>` para `Easing`. O argumento para a função de easing sempre está no intervalo de 0 a 1 conforme a animação linearmente prossegue do início ao fim. A função *geralmente* retorna um valor no intervalo de 0 a 1, mas pode ser rapidamente negativo ou maior que 1 (como é o caso com o `SpringIn` e `SpringOut` funções) ou poderia quebrar as regras, se você souber que você está fazendo.

O [ **UneasyScale** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale) exemplo demonstra uma função de easing personalizada, e [ **CustomCubicEase** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase) demonstra outra.

O [ **SwingButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton) exemplo também demonstra uma função de easing personalizada e também uma técnica de alterar o `AnchorX` e `AnchorY` propriedades dentro de uma sequência de animações de rotação.

O [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca tem um [ `JiggleButton` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs) classe que usa uma atenuação personalizada a função jiggle um botão quando ele for clicado. O [ **JiggleButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo) exemplo demonstra isso.

### <a name="entrance-animations"></a>Animações de entrada

Um tipo popular de animação ocorre quando uma página é exibida. Uma animação desse tipo pode ser iniciada na [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) substituir da página. Para essas animações, convém configurar o XAML para como você deseja que a página apareça *depois de* a animação e, em seguida, inicializar e animar o layout do código.

O [ **FadingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance) de exemplo usa o [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método de extensão para aplicar fade-in o conteúdo da página.

O [ **SlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance) de exemplo usa o [ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) método de extensão para deslizar o conteúdo da página dos lados.

O [ **SwingingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance) de exemplo usa o [ `RotateYTo` ](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método de extensão para animar o `RotationY` propriedade. Um [ `RotateXTo` ](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método também está disponível.

### <a name="forever-animations"></a>Animações para sempre

Por outro lado, "sempre" animações executado até que o programa é encerrado. Eles geralmente se destina para fins de demonstração.

O [ **FadingTextAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation) de exemplo usa [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) animação de esmaecimento dois pedaços de texto de entrada e saída.

[**PalindromeAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation) exibe um palíndromo e, em seguida, sequencialmente gira as letras individuais 180 graus para que eles sejam cabeça para baixo. Em seguida, a cadeia de caracteres inteira é invertida 180 graus para ler o mesmo que a cadeia de caracteres original.

O [ **CopterAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation) exemplo gira um simples `BoxView` helicóptero enquanto revolução-lo em torno do centro da tela.

[**RotatingSpokes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes) gira `BoxView` raios em torno do centro da tela e, em seguida, gira cada spoke em si para criar padrões interessantes:

[![Captura de tela tripla de rotação de raios](images/ch22fg21-small.png "girando raios")](images/ch22fg21-large.png#lightbox "girando raios")

No entanto, aumentar progressivamente a `Rotation` propriedade de um elemento pode não funcionar em longo prazo, como o [ **RotationBreakdown** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown) demonstra.

O [ **SpinningImage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage) de exemplo usa [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), [ `RotateXTo` ](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), e [ `RotateYTo` ](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) para torná-lo parecer como se um bitmap é girar no espaço 3D.

### <a name="animating-the-bounds-property"></a>Anime a propriedade de limites

O único método de extensão na `ViewExtensions` ainda não foi demonstrado é [ `LayoutTo` ](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)), que efetivamente anima somente leitura [ `Bounds` ](xref:Xamarin.Forms.VisualElement.Bounds) propriedade chamando o [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) método. Normalmente, esse método é chamado pelo `Layout` derivativos conforme será discutido [ **Capítulo 26. CustomLayouts**](chapter26.md).

O `LayoutTo` método deve ser restrito para fins especiais. O [ **BouncingBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox) programa usa para compactar e expandir um `BoxView` conforme ele Rebate os lados de uma página.

O [ **XamagonXuzzle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle) de exemplo usa `LayoutTo` mover os blocos em uma implementação da clássico 15-16 quebra-cabeça que exibe uma imagem embaralhada em vez de blocos numerados:

[![Captura de tela tripla do Xamarin Xuzzle](images/ch22fg26-small.png "jogo de quebra-cabeça Xuzzle")](images/ch22fg26-large.png#lightbox "Xuzzle jogo de quebra-cabeça")

### <a name="your-own-awaitable-animations"></a>Suas próprias animações awaitable

O [ **TryAwaitableAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation) exemplo cria uma animação awaitable. A classe crucial que pode retornar um `Task` é o objeto do método e, quando a animação é concluída de sinal [ `TaskCompletionSource` ](xref:System.Threading.Tasks.TaskCompletionSource`1).

## <a name="deeper-into-animations"></a>Ainda mais a animações

O sistema de animação do xamarin. Forms pode ser um pouco confuso. Além de `Easing` classe, o sistema de animação inclui o `ViewExtensions`, `Animation`, e `AnimationExtension` classes.

### <a name="viewextensions-class"></a>Classe ViewExtensions

Você já viu [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions). Ele define nove métodos que retornam `Task<bool>` e [ `CancelAnimations` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)). Sete de destino nove métodos propriedades de transformação. Os outros dois são [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), quais destinos de [ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity) propriedade, e [ `LayoutTo` ](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)), que chama o [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) método.

### <a name="animation-class"></a>Classe de animação

O [ `Animation` ](xref:Xamarin.Forms.AnimationExtensions) classe tem um [construtor](xref:Xamarin.Forms.Animation.%23ctor(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Action)) com cinco argumentos para definir o retorno de chamada e terminar de métodos e parâmetros da animação.

Animações filho podem ser adicionadas com [ `Add` ](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)), [ `Insert` ](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation)), [ `WithConcurrent` ](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double))e a sobrecarga do [ `WithConcurrent` ](xref:Xamarin.Forms.Animation.WithConcurrent(System.Action{System.Double},System.Double,System.Double,Xamarin.Forms.Easing,System.Double,System.Double)).

O objeto de animação é então iniciado com uma chamada para o [ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) método.

### <a name="animationextensions-class"></a>Classe AnimationExtensions

O [ `AnimationExtensions` ](xref:Xamarin.Forms.AnimationExtensions) classe contém principalmente os métodos de extensão. Há várias versões de um `Animate` método e genérica [ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*) método é tão versátil que realmente é a função de animação somente é necessário.

## <a name="working-with-the-animation-class"></a>Trabalhando com a classe de animação

O [ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) exemplo demonstra o [ `Animation` ](xref:Xamarin.Forms.Animation) classe com várias animações diferentes.

### <a name="child-animations"></a>Animações filho

O [ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) exemplo também demonstra filho animações que façam usam da (muito semelhante) [ `Add` ](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)) e [ `Insert` ](xref:Xamarin.Forms.Animation.Insert(System.Double,System.Double,Xamarin.Forms.Animation)) métodos.

### <a name="beyond-the-high-level-animation-methods"></a>Além dos métodos de animação de alto nível

O [ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) exemplo também demonstra como executar animações que vão além de propriedades direcionadas pelo `ViewExtensions` métodos. Em um exemplo, uma série de períodos de obter maior; em outro exemplo, um `BackgroundColor` propriedade é animada.

### <a name="more-of-your-own-awaitable-methods"></a>Mais de seus próprios métodos awaitable

O [ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) método `ViewExtensions` não funciona com o [ `Easing.SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut) função. Ele é interrompido quando a saída de easing obtém acima de 1.

O [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca contém um [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe com [ `TranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12) e [ `TranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49) métodos de extensão que não têm esse problema, bem como [ `CancelTranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44) e [ `CancelTranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71) métodos para cancelar os animações.

O [ **SpringSlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance) demonstra o `TranslateXTo` método.

O `MoreExtensions` classe também contém um [ `TranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76) método de extensão que combina a translação de X e Y, e uma [ `CancelTranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113) método.

### <a name="implementing-a-bezier-animation"></a>Implementando uma animação de Bézier

Também é possível desenvolver uma animação que move um elemento ao longo do caminho de uma spline de Bézier. O [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca contém um [ `BezierSpline` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs) estrutura que encapsula uma spline de Bézier e uma [ `BezierTangent` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs) enumeração a orientação do controle.

O [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe contém um [ `BezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118) o método de extensão e um [ `CancelBezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161) método.

O [ **BezierLoop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop) exemplo demonstra a animação de um elemento ao longo de um caminho Beizer.

## <a name="working-with-animationextensions"></a>Trabalhando com AnimationExtensions

Um tipo de animação ausente da coleção padrão é uma animação de cor. O problema é que não há nenhuma maneira certa para interpolar entre dois `Color` valores. É possível interpolar os valores RGB individuais, mas apenas como válido é interpolação os valores HSL.

Por esse motivo, o [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe os [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca contém duas `Color` métodos de animação: [ `RgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166)e [ `HslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188). (Também há dois métodos de cancelamento: [ `CancelRgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183) e [ `CancelHslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206)).

Ambos os métodos fazem uso de [ `ColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211), que executa a animação chamando o extenso genérico [ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*) método na [ `AnimationExtensions` ](xref:Xamarin.Forms.AnimationExtensions).

O [ **ColorAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations) exemplo demonstra como usar esses dois tipos de animações de cores.

## <a name="structuring-your-animations"></a>Estruturar suas animações

Às vezes é útil expressar animações no XAML e usá-los em conjunto com o MVVM. Isso é abordado no próximo capítulo [ **capítulo 23. Gatilhos e comportamentos**](chapter23.md).



## <a name="related-links"></a>Links relacionados

- [Capítulo 22 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [Exemplos do capítulo 22](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [Animação](~/xamarin-forms/user-interface/animation/index.md)
