---
title: Resumo do capítulo 22. Animação
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 47C2B9AB-E688-4412-8AF5-9F633B3DA695
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: d18b563c0fc47db8c6d7f8cebb3ec85989064d20
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-22-animation"></a>Resumo do capítulo 22. Animação

Você viu que você pode criar suas próprias animações usando o temporizador xamarin. Forms ou `Task.Delay`, mas é geralmente mais fácil usar os recursos de animação fornecidos pelo xamarin. Forms. Três classes implementam essas animações:

- [`ViewExtensions`](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/), a abordagem de alto nível
- [`Animation`](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/), mais versátil mas mais difícil
- [`AnimationExtension`](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/), abordagem mais versátil, de nível inferior

Em geral, animações destino propriedades que são apoiadas por propriedades vinculáveis. Isso não é um requisito, mas essas são as únicas propriedades que dinamicamente reagem a alterações.

Não há nenhuma interface XAML para essas animações, mas você pode integrar animações em XAML usando técnicas discutidas em [ **capítulo 23. Gatilhos e comportamentos**](chapter23.md).

## <a name="exploring-basic-animations"></a>Explorar animações básicas

As funções básicas de animação são métodos de extensão encontrados no [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) classe. Esses métodos se aplicam a qualquer objeto que deriva de `VisualElement`. As animações mais simples de destino as transformações propriedades discutidas [ `Chapter 21. Transforms` ](chapter21.md).

O [ **AnimationTryout** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/AnimationTryout) demonstra como o `Clicked` manipulador de eventos para um `Button` pode chamar o [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método de extensão para girar o botão em um círculo.

O `RotateTo` alterações de método de `Rotation` propriedade o `Button` de 0 a 360 no decorrer de um quarto de segundo (por padrão). Se o `Button` tocado novamente, no entanto, ele não faz nada porque o `Rotation` propriedade já está 360 graus.

### <a name="setting-the-animation-duration"></a>Definir a duração da animação

O segundo argumento para `RotateTo` for uma duração em milissegundos. Se definido como um valor grande, tocar a `Button` durante uma animação inicia um novo início da animação em ângulo atual.

### <a name="relative-animations"></a>Animações relativas

O `RelRotateTo` método executa uma rotação relativa adicionando um valor especificado para o valor existente. Esse método permite que o `Button` a ser tocado várias vezes e cada vez aumenta o `Rotation` propriedade 360 graus.

### <a name="awaiting-animations"></a>Aguardando animações

Todos os métodos de animação em `ViewExtensions` retornar `Task<bool>` objetos. Isso significa que você pode definir uma série de animações sequenciais usando `ContinueWith` ou `await`. O `bool` conclusão valor de retorno é `false` se a animação é concluída sem interrupções ou `true` se ela foi cancelada pelo [ `CancelAnimation` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.CancelAnimations/p/Xamarin.Forms.VisualElement/) método, o que cancela todas as animações iniciadas pelo outro método em `ViewExtensions` que são definidos no mesmo elemento.

### <a name="composite-animations"></a>Animações compostas

Você pode misturar animações esperadas e não esperada para criar animações compostas. Essas são as animações na `ViewExtensions` que visam o `TranslatonX`, `TranslationY`, e `Scale` propriedades de transformação:

- [`TranslateTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/)
- [`ScaleTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.ScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)
- [`RelScaleTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)

Observe que `TranslateTo` potencialmente afeta tanto o `TranslationX` e `TranslationY` propriedades.

### <a name="taskwhenall-and-taskwhenany"></a>Task. WhenAll e Task.WhenAny

Também é possível gerenciar as animações simultâneas usando [ `Task.WhenAll` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.WhenAll%7BTResult%7D/p/System.Collections.Generic.IEnumerable%7BSystem.Threading.Tasks.Task%7BTResult%7D%7D/), que indica quando várias tarefas todos tem concluído, e [ `Task.WhenAny` ](https://developer.xamarin.com/api/member/System.Threading.Tasks.Task.WhenAny%7BTResult%7D/p/System.Collections.Generic.IEnumerable%7BSystem.Threading.Tasks.Task%7BTResult%7D%7D/), que indica quando o primeiro de vários tarefas foi concluída.

### <a name="rotation-and-anchors"></a>Rotação e âncoras

Ao chamar o `ScaleTo`, `RelScaleTo`, `RotateTo`, e `RelRotateTo` métodos, você pode definir o [ `AnchorX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/) e [ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/) propriedades para indicar o Centro de escala e rotação.

O [ **CircleButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CircleButton) demonstra essa técnica, que são exibidas uma `Button` em torno do centro da página.

### <a name="easing-functions"></a>Funções de atenuação

Geralmente, animações são lineares de um valor de início para um valor de término. Atenuação funções pode causar animações aumentar ou diminuir seu longo. O último argumento opcional para os métodos de animação é do tipo [ `Easing` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Easing/), uma classe que define 11 campos estáticos somente leitura do tipo `Easing`:

- [`Linear`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.Linear/), o padrão
- [`SinIn`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinIn/), [ `SinOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinOut/), e [`SinInOut`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SinInOut/)
- [`CubicIn`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicIn/), [ `CubicOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicOut/), e [`CubicInOut`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.CubicInOut/)
- [`BounceIn`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.BounceIn/) e [`BounceOut`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.BounceOut/)
- [`SpringIn`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringIn/) e [`SpringOut`](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/)

O `In` sufixo indica que o efeito é o início da animação, `Out` significa no final, e `InOut` significa que ele está no início e no final da animação.

O [ **BounceButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BounceButton) exemplo demonstra o uso de funções de atenuação.

### <a name="your-own-easing-functions"></a>Suas próprias funções de atenuação

Você também pode definir a você próprias funções de atenuação, passando um [ `Func<double, double>` ](https://developer.xamarin.com/api/type/System.Func%3CT,TResult%3E/) para o [ `Easing` construtor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Easing.Easing/p/System.Func%7BSystem.Double,System.Double%7D/). `Easing` também define uma conversão implícita de `Func<double, double>` para `Easing`. O argumento para a função de easing é sempre no intervalo de 0 a 1 conforme a animação linearmente prossegue do início ao fim. A função *geralmente* retorna um valor no intervalo de 0 a 1, mas pode ser brevemente negativo ou maior que 1 (como é o caso com o `SpringIn` e `SpringOut` funções) ou pode interromper as regras, se você souber o que está fazendo.

O [ **UneasyScale** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/UneasyScale) demonstra uma função personalizada de atenuação, e [ **CustomCubicEase** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CustomCubicEase) demonstra outra.

O [ **SwingButton** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingButton) exemplo também demonstra uma função personalizada de atenuação e também uma técnica de alterar o `AnchorX` e `AnchorY` propriedades dentro de uma sequência de animações de rotação.

O [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca tem um [ `JiggleButton` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/JiggleButton.cs) classe que usa uma atenuação personalizada a função jiggle um botão quando ele for clicado. O [ **JiggleButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/JiggleButtonDemo) demonstra a ele.

### <a name="entrance-animations"></a>Animações de entrada

Um tipo popular de animação ocorre quando uma página é exibida. Tal uma animação pode ser iniciada a [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing()/) substituição da página. Para essas animações, convém configurar o XAML para definir como deseja que a página seja exibido *depois* a animação e, em seguida, inicializar e animar o layout do código.

O [ **FadingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingEntrance) usa o [ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método de extensão para atenuar o conteúdo da página.

O [ **SlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SlidingEntrance) usa o [ `TranslateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/) método de extensão para deslizar o conteúdo da página de lados.

O [ **SwingingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SwingingEntrance) usa o [ `RotateYTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateYTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método de extensão para animar a `RotationY` propriedade. Um [ `RotateXTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateXTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método também está disponível.

### <a name="forever-animations"></a>Para sempre animações

Por outro lado, "infinito" animações executado até que o programa é encerrado. Esses geralmente são voltadas para fins de demonstração.

O [ **FadingTextAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/FadingTextAnimation) usa [ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) animação em duas partes de texto e desapareçam.

[**PalindromeAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/PalindromeAnimation) exibe um palindrome e, em seguida, em sequência gira as letras individuais por 180 graus para que eles estejam todos cabeça para baixo. Em seguida, a cadeia de caracteres inteira é invertida 180 graus para ler o mesmo que a cadeia de caracteres original.

O [ **CopterAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/CopterAnimation) exemplo gira um simples `BoxView` helicóptero ao mesmo tempo em que são exibidas em torno do centro da tela.

[**RotatingSpokes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotatingSpokes) gira `BoxView` raios em torno do centro da tela e, em seguida, gira cada spoke para criar padrões interessantes:

[![Captura de tela tripla de rotação raios](images/ch22fg21-small.png "girando raios")](images/ch22fg21-large.png#lightbox "girando raios")

No entanto, aumentar progressivamente a `Rotation` propriedade de um elemento pode não funcionar no longo prazo, como o [ **RotationBreakdown** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/RotationBreakdown) demonstra.

O [ **SpinningImage** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpinningImage) usa [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/), [ `RotateXTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateXTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/), e [ `RotateYTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateYTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) para simular como se um bitmap é girando no espaço 3D.

### <a name="animating-the-bounds-property"></a>Animar a propriedade de limites

O único método de extensão no `ViewExtensions` demonstrado ainda não é [ `LayoutTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.LayoutTo/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/System.UInt32/Xamarin.Forms.Easing/), que efetivamente anima somente leitura [ `Bounds` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Bounds/) propriedade chamando o [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) método. Normalmente, esse método é chamado pelo `Layout` derivados como será discutido em [ **Capítulo 26. CustomLayouts**](chapter26.md).

O `LayoutTo` método deve ser restrito para fins especiais. O [ **BouncingBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BouncingBox) programa utiliza para compactar e expandir um `BoxView` como ele refletida os lados de uma página.

O [ **XamagonXuzzle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle) usa `LayoutTo` mover blocos em uma implementação de clássica 15-16 quebra-cabeça que exibe uma imagem embaralhada em vez de blocos numerados:

[![Captura de tela tripla do Xamarin Xuzzle](images/ch22fg26-small.png "Xuzzle quebra-cabeça jogo")](images/ch22fg26-large.png#lightbox "Xuzzle quebra-cabeça jogo")

### <a name="your-own-awaitable-animations"></a>Suas próprias animações awaitable

O [ **TryAwaitableAnimation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/TryAwaitableAnimation) exemplo cria uma animação awaitable. A classe crucial que pode retornar um `Task` objeto do método e, quando a animação é concluída de sinal é [ `TaskCompletionSource` ](https://developer.xamarin.com/api/type/System.Threading.Tasks.TaskCompletionSource%3CTResult%3E/).

## <a name="deeper-into-animations"></a>Profunda animações

O sistema de animação xamarin. Forms pode ser um pouco confuso. Além de `Easing` classe, o sistema de animação inclui o `ViewExtensions`, `Animation`, e `AnimationExtension` classses.

### <a name="viewextensions-class"></a>Classe ViewExtensions

Você já viu [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/). Ele define nove métodos que retornam `Task<bool>` e [ `CancelAnimations` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.CancelAnimations/p/Xamarin.Forms.VisualElement/). Sete do destino nove métodos propriedades de transformação. As outras duas são [ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/), tendo como alvo o [ `Opacity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Opacity/) propriedade e [ `LayoutTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.LayoutTo/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/System.UInt32/Xamarin.Forms.Easing/), que chama o [ `Layout` ](https://developer.xamarin.com/api/member/Xamarin.Forms.VisualElement.Layout/p/Xamarin.Forms.Rectangle/) método.

### <a name="animation-class"></a>Classe de animação

O [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/) classe tiver um [construtor](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Animation.Animation/p/System.Action%7BSystem.Double%7D/System.Double/System.Double/Xamarin.Forms.Easing/System.Action/) com cinco argumentos para definir o retorno de chamada e terminar de métodos e parâmetros da animação.

Animações filho podem ser adicionadas com [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Add/p/System.Double/System.Double/Xamarin.Forms.Animation/), [ `Insert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Insert/p/System.Double/System.Double/Xamarin.Forms.Animation/), [ `WithConcurrent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.WithConcurrent/p/Xamarin.Forms.Animation/System.Double/System.Double/), e e sobrecarga de [ `WithConcurrent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.WithConcurrent/p/System.Action%7BSystem.Double%7D/System.Double/System.Double/Xamarin.Forms.Easing/System.Double/System.Double/).

O objeto de animação, em seguida, é iniciado com uma chamada para o [ `Commit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Commit/p/Xamarin.Forms.IAnimatable/System.String/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action%7BSystem.Double,System.Boolean%7D/System.Func%7BSystem.Boolean%7D/) método.

### <a name="animationextensions-class"></a>Classe AnimationExtensions

O [ `AnimationExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/) classe contém principalmente os métodos de extensão. Existem várias versões de um `Animate` método e genérica [ `Animate` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.Animate%7BT%7D/p/Xamarin.Forms.IAnimatable/System.String/System.Func%7BSystem.Double,T%7D/System.Action%7BT%7D/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action%7BT,System.Boolean%7D/System.Func%7BSystem.Boolean%7D/) método é tão versátil que é realmente a função de animação somente é necessário.

## <a name="working-with-the-animation-class"></a>Trabalhando com a classe de animação

O [ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) demonstra o [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) classe com várias animações diferentes.

### <a name="child-animations"></a>Animações filho

O [ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) exemplo também demonstra como filho animações, que tornam o uso de (muito semelhante) [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Add/p/System.Double/System.Double/Xamarin.Forms.Animation/) e [ `Insert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Insert/p/System.Double/System.Double/Xamarin.Forms.Animation/) métodos.

### <a name="beyond-the-high-level-animation-methods"></a>Além dos métodos de animação de alto nível

O [ **ConcurrentAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ConcurrentAnimations) exemplo também demonstra como executar animações que vão além de propriedades de destino do `ViewExtensions` métodos. Em um exemplo, uma série de períodos de obter maior; em outro exemplo, um `BackgroundColor` propriedade é animada.

### <a name="more-of-your-own-awaitable-methods"></a>Mais de seus próprios métodos awaitable

O [ `TranslateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/) método `ViewExtensions` não funciona com o [ `Easing.SpringOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/) função. É interrompido quando a saída de atenuação obtém acima de 1.

O [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca contém um [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe com [ `TranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L12) e [ `TranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L49) métodos de extensão que não têm esse problema, bem como [ `CancelTranslateXTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L44) e [ `CancelTranslateYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L71) métodos para cancelar as animações.

O [ **SpringSlidingEntrance** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/SpringSlidingEntrance) demonstra o `TranslateXTo` método.

O `MoreExtensions` classe também contém um [ `TranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L76) método de extensão que combina a tradução de X e Y, e um [ `CancelTranslateXYTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L113) método.

### <a name="implementing-a-bezier-animation"></a>Implementando uma animação de Bézier

Também é possível desenvolver uma animação que move um elemento do caminho de uma spline de Bézier. O [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca contém um [ `BezierSpline` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierSpline.cs) estrutura que encapsula uma spline de Bézier e um [ `BezierTangent` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/BezierTangent.cs) enumeração a orientação do controle.

O [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe contém um [ `BezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L118) método de extensão e um [ `CancelBezierPathTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L161) método.

O [ **BezierLoop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/BezierLoop) exemplo demonstra a animação de um elemento em um caminho de Beizer.

## <a name="working-with-animationextensions"></a>Trabalhando com AnimationExtensions

Um tipo de animação ausente da coleção padrão é uma animação de cor. O problema é que não há nenhuma maneira correta para interpolar entre dois `Color` valores. É possível interpolar os valores RGB individuais, mas apenas como válido é interpolação de valores HSL.

Por esse motivo, o [ `MoreViewExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs) classe no [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) biblioteca contém duas `Color` métodos de animação: [ `RgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L166)e [ `HslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L188). (Também há dois métodos de cancelamento: [ `CancelRgbColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L183) e [ `CancelHslColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L206)).

Ambos os métodos fazem uso de [ `ColorAnimation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/MoreViewExtensions.cs#L211), que executa a animação chamando genérica ampla [ `Animate` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.Animate%7BT%7D/p/Xamarin.Forms.IAnimatable/System.String/System.Func%7BSystem.Double,T%7D/System.Action%7BT%7D/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action%7BT,System.Boolean%7D/System.Func%7BSystem.Boolean%7D/) método em [ `AnimationExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/).

O [ **ColorAnimations** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/ColorAnimations) exemplo demonstra como usar esses dois tipos de animações de cores.

## <a name="structuring-your-animations"></a>Estruturar suas animações

Às vezes é útil expressar animações em XAML e usá-los em conjunto com o modelo MVVM. Isso é abordado no próximo capítulo, [ **capítulo 23. Gatilhos e comportamentos**](chapter23.md).



## <a name="related-links"></a>Links relacionados

- [Capítulo 22 de texto completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)
- [Exemplos de capítulo 22](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22)
- [Animação](~/xamarin-forms/user-interface/animation/index.md)
