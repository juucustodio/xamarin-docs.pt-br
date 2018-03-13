---
title: "Animações simples"
description: "A classe ViewExtensions fornece métodos de extensão que podem ser usados para construir animações simples. Este artigo demonstra como criar e Cancelar animações usando a classe ViewExtensions."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4A6FAE5A-848F-4CE0-BFA1-22A6309B5225
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/27/2017
ms.openlocfilehash: fb7ca216978e4c890349a44b07d5a383e9ca2384
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="simple-animations"></a>Animações simples

_A classe ViewExtensions fornece métodos de extensão que podem ser usados para construir animações simples. Este artigo demonstra como criar e Cancelar animações usando a classe ViewExtensions._


O [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) classe fornece os seguintes métodos de extensão que podem ser usados para criar animações simples:

- [`TranslateTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/) anima a [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/) e [ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/) propriedades de um [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).
- [`ScaleTo`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) anima a [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) propriedade de um [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).
- [`RelScaleTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) aplica-se a um aumento incremental animado ou diminuirá para o [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) propriedade de um [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).
- [`RotateTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) anima a [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) propriedade de um [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).
- [`RelRotateTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelRotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) aplica-se a um aumento incremental animado ou diminuirá para o [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) propriedade de um [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).
- [`RotateXTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateXTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) anima a [ `RotationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationX/) propriedade de um [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).
- [`RotateYTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateYTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) anima a [ `RotationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.RotationY/) propriedade de um [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).
- [`FadeTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) anima a [ `Opacity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Opacity/) propriedade de um [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/).

Por padrão, cada animação levará 250 milissegundos. No entanto, uma duração de cada animação pode ser especificada ao criar a animação.

O [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) classe também inclui um [ `CancelAnimations` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.CancelAnimations/p/Xamarin.Forms.VisualElement/) método que pode ser usado para cancelar qualquer animações.

> [!NOTE]
> O [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) classe fornece um [ `LayoutTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.LayoutTo/p/Xamarin.Forms.VisualElement/Xamarin.Forms.Rectangle/System.UInt32/Xamarin.Forms.Easing/) método de extensão. No entanto, esse método se destina a ser usado pelo layouts para animar transições entre estados de layout que contêm o tamanho e posição alterações. Portanto, ele só deve ser usado por [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/) subclasses.

Os métodos de extensão de animação no [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) classe são assíncronas e retornar um `Task<bool>` objeto. O valor de retorno é `false` se a animação é concluída, e `true` se a animação é cancelada. Portanto, os métodos de animação normalmente devem ser usados com o `await` operador, que torna possível determinar facilmente quando uma animação estiver concluída. Além disso, em seguida, é possível criar animações sequenciais com métodos subsequentes animação em execução depois que o método anterior foi concluída. Para obter mais informações, consulte [composta animações](#compound).

Se houver um requisito para permitir que uma animação completa em segundo plano, o `await` operador pode ser omitido. Nesse cenário, os métodos de extensão de animação retornará rapidamente após iniciar a animação, com a animação que ocorrem em segundo plano. Esta operação pode ser tomada aproveitar ao criar animações compostas. Para obter mais informações, consulte [animações composto](#composite).

Para obter mais informações sobre o `await` operador, consulte [visão geral de suporte assíncrono](~/cross-platform/platform/async.md).

## <a name="single-animations"></a>Animações único

Cada método de extensão no [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) implementa uma operação única animação progressivamente altera uma propriedade de um valor para outro valor em um período de tempo. Esta seção explora a cada operação de animação.

### <a name="rotation"></a>Rotação

O exemplo de código a seguir demonstra como usar o [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método para animar a [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) propriedade de um [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

Esse código anima a [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) instância girando até 360 graus em 2 segundos (2000 milissegundos). O [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método obtém atual [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) propriedade de valor para o início da animação e, em seguida, gira desse valor para o primeiro argumento (360). Depois que a animação é concluída, a imagem [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) propriedade é redefinida para 0. Isso garante que o `Rotation` propriedade não permanecer com 360 após a conclusão da animação, que o impediria rotações adicionais.

As capturas de tela a seguir mostram a rotação em andamento em cada plataforma:

![](simple-images/rotateto.png "Animação de rotação")

### <a name="relative-rotation"></a>Rotação relativa

O exemplo de código a seguir demonstra como usar o [ `RelRotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelRotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método incrementalmente aumentar ou diminuir o [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) propriedade de um [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.RelRotateTo (360, 2000);
```

Esse código anima a [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) instância girando 360 graus da sua posição inicial mais de 2 segundos (2000 milissegundos). O [ `RelRotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelRotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método obtém atual [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) propriedade de valor para o início da animação e, em seguida, gira desse valor para o valor mais seu primeiro argumento (360). Isso garante que cada animação sempre será uma rotação de 360 graus a partir da posição inicial. Portanto, se uma animação novo é chamada durante uma animação já está em andamento, será iniciado a partir da posição atual e pode terminar em uma posição que não seja um incremento de 360 graus.

As capturas de tela a seguir mostram a rotação relativa em andamento em cada plataforma:

![](simple-images/relrotateto.png "Animação de rotação relativo")

### <a name="scaling"></a>Dimensionamento

O exemplo de código a seguir demonstra como usar o [ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) método para animar a [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) propriedade de um [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.ScaleTo (2, 2000);
```

Esse código anima a [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) instância por até duas vezes o tamanho em 2 segundos (2000 milissegundos). O [ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) método obtém atual [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) o valor de propriedade (valor padrão de 1) para o início da animação e, em seguida, escalas de valor para o primeiro argumento (2). Isso tem o efeito de aumentar o tamanho da imagem para duas vezes o tamanho.

As capturas de tela a seguir mostram o dimensionamento em andamento em cada plataforma:

![](simple-images/scaleto.png "Dimensionamento de animação")

### <a name="relative-scaling"></a>Relativo ao dimensionamento

O exemplo de código a seguir demonstra como usar o [ `RelScaleTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método para animar a [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) propriedade de um [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.RelScaleTo (2, 2000);
```

Esse código anima a [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) instância por até duas vezes o tamanho em 2 segundos (2000 milissegundos). O [ `RelScaleTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RelScaleTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método obtém atual [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) valor de propriedade para o início da animação e, em seguida, escalas de valor para o valor mais seu primeiro argumento (2). Isso garante que cada animação sempre será uma escala de 2 a partir da posição inicial.

### <a name="scaling-and-rotation-with-anchors"></a>Escala e rotação com âncoras

O [ `AnchorX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/) e [ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/) propriedades definidas o Centro de rotação para ou de dimensionamento de [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) e [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) propriedades. Portanto, seus valores também afetam o [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) e [ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) métodos.

Dado um [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) que foi colocado no Centro de um layout, o exemplo de código a seguir demonstra a rotação de imagem em torno do centro do layout definindo seu [ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/) propriedade:

```csharp
image.AnchorY = (Math.Min (absoluteLayout.Width, absoluteLayout.Height) / 2) / image.Height;
await image.RotateTo (360, 2000);
```

Para girar o [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) instância em torno do centro do layout, o [ `AnchorX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorX/) e [ `AnchorY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.AnchorY/) propriedades devem ser definidas com valores que são em relação a largura e altura do `Image`. Neste exemplo, o centro do `Image` é definido como no Centro de layout e, portanto o padrão `AnchorX` valor de 0,5 não requer alterações. No entanto, o `AnchorY` propriedade será redefinida para ser um valor da parte superior do `Image` ao ponto central do layout. Isso garante que o `Image` faz uma rotação completa de 360 graus em torno do ponto central do layout, como mostrado nas capturas de tela seguir:

![](simple-images/rotate-anchors.png "Animação de rotação com âncoras")

### <a name="translation"></a>Conversão

O exemplo de código a seguir demonstra como usar o [ `TranslateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/) método para animar a [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/) e [ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/) propriedades de um [ `Image`](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
await image.TranslateTo (-100, -100, 1000);
```

Esse código anima a [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) instância convertendo-horizontalmente e verticalmente em 1 segundo (1000 milissegundos). O [ `TranslateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.TranslateTo/p/Xamarin.Forms.VisualElement/System.Double/System.Double/System.UInt32/Xamarin.Forms.Easing/) método converte simultaneamente os pixels da imagem 100 para a esquerda e para cima de 100 pixels. Isso ocorre porque o primeiro e segundo argumentos são ambos os números negativos. Fornecer números positivos converterá a imagem para a direita e para baixo.

As capturas de tela a seguir mostram a conversão em andamento em cada plataforma:

![](simple-images/translateto.png "Animação de tradução")

> [!NOTE]
> Se um elemento é inicialmente disposto fora da tela e, em seguida, convertido na tela, após a conversão de layout de entrada do elemento permanece fora da tela e o usuário não pode interagir com ele. Portanto, é recomendável que um modo de exibição deve ser apresentado em sua posição final e, em seguida, as necessárias traduções executadas.

### <a name="fading"></a>Esmaecimento

O exemplo de código a seguir demonstra como usar o [ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método para animar a [ `Opacity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Opacity/) propriedade de um [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/):

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

Esse código anima a [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) instância por cor-lo em mais de 4 segundos (4.000 milissegundos). O [ `FadeTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.FadeTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método obtém atual [ `Opacity` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Opacity/) valor de propriedade para o início da animação e, em seguida, esmaece desse valor para o primeiro argumento (1).

As capturas de tela a seguir mostram a atenuação em andamento em cada plataforma:

![](simple-images/fadeto.png "Animação de esmaecimento")

<a name="compound" />

## <a name="compound-animations"></a>Animações compostas

Uma animação composta é uma combinação sequencial de animações e podem ser criada com o `await` operador, conforme demonstrado no exemplo de código a seguir:

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image up
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

Neste exemplo, o [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) é convertida em 6 segundos (6000 milissegundos). A conversão do `Image` usa animações de cinco, com o `await` operador indicando que cada animação executa sequencialmente. Portanto, os métodos de animação subsequentes executar depois que o método anterior foi concluída.

<a name="composite" />

## <a name="composite-animations"></a>Animações compostas

Uma animação composta é uma combinação de animações onde dois ou mais animações executados simultaneamente. Podem ser criadas animações compostas misturando animações esperadas e não é aguardada, conforme demonstrado no exemplo de código a seguir:

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

Neste exemplo, o [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) é dimensionado e girado simultaneamente em 4 segundos (4.000 milissegundos). O dimensionamento do `Image` usa duas animações sequenciais que ocorrem ao mesmo tempo como a rotação. O [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método é executado sem um `await` operador e retorna imediatamente, com a primeira [ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) , em seguida, a partir de animação. O `await` operador no primeiro `ScaleTo` chamada de método atrasa a segunda `ScaleTo` chamada de método até o primeiro `ScaleTo` concluiu a chamada de método. Neste ponto o `RotateTo` animação é metade concluída de maneira e o `Image` será girados 180 graus. Durante os últimos 2 segundos (2000 milissegundos), o segundo `ScaleTo` animação e a `RotateTo` animação ambos concluir.

### <a name="running-multiple-asynchronous-methods-concurrently"></a>A execução simultânea de vários métodos assíncronos

O `static` `Task.WhenAny` e `Task.WhenAll` métodos são usados para executar vários métodos assíncronos simultaneamente e, portanto, pode ser usados para criar animações compostas. Ambos os métodos retornam um `Task` de objeto e aceitar uma coleção de métodos de retorno que cada um `Task` objeto. O `Task.WhenAny` método é concluído quando qualquer método em sua coleção conclui a execução, conforme demonstrado no exemplo de código a seguir:

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

Neste exemplo, o `Task.WhenAny` chamada de método contém duas tarefas. A primeira tarefa gira a imagem em 4 segundos (4.000 milissegundos), e a segunda tarefa ajusta a imagem de mais de 2 segundos (2000 milissegundos). Quando a segunda tarefa é concluída, o `Task.WhenAny` conclusão da chamada de método. No entanto, embora o [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/) método ainda está em execução, a segunda [ `ScaleTo` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) método pode começar.

O `Task.WhenAll` método é concluído quando todos os métodos em sua coleção tem concluído, conforme demonstrado no exemplo de código a seguir:

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

Neste exemplo, o `Task.WhenAll` chamada de método contém três tarefas, cada uma delas executa em 10 minutos. Cada `Task` faz com que um número diferente de 360 graus rotações – 307 rotações de [ `RotateTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/), 251 rotações de [ `RotateXTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateXTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/)e 199 rotações de [ `RotateYTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.RotateYTo/p/Xamarin.Forms.VisualElement/System.Double/System.UInt32/Xamarin.Forms.Easing/). Esses valores são números primos, portanto, garantindo que as rotações não estão sincronizadas e, portanto, não resultará em padrões repetitivos.

As capturas de tela a seguir mostram as rotações de vários em andamento em cada plataforma:

![](simple-images/multiple-rotations.png "Animação composta")

## <a name="canceling-animations"></a>Cancelando animações

Um aplicativo pode cancelar uma ou mais animações com uma chamada para o `static` [ `ViewExtensions.CancelAnimations` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ViewExtensions.CancelAnimations/p/Xamarin.Forms.VisualElement/) método, conforme demonstrado no exemplo de código a seguir:

```csharp
ViewExtensions.CancelAnimations (image);
```

Isso cancelará todas as animações que estão sendo executados no imediatamente o [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) instância.

## <a name="summary"></a>Resumo

Este artigo demonstrou criando e Cancelando animações usando o [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) classe. Essa classe fornece métodos de extensão que podem ser usados para construir animações simples que girar, redimensionar, traduzam e desaparecerem [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) instâncias.


## <a name="related-links"></a>Links relacionados

- [Visão geral do suporte assíncrono](~/cross-platform/platform/async.md)
- [Animação básica (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/basic/)
- [ViewExtensions](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/)
