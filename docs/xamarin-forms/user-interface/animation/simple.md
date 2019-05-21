---
title: Animações simples no xamarin. Forms
description: A classe ViewExtensions fornece métodos de extensão que podem ser usados para construir animações simples. Este artigo demonstra como criar e cancelar as animações usando a classe ViewExtensions.
ms.prod: xamarin
ms.assetid: 4A6FAE5A-848F-4CE0-BFA1-22A6309B5225
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/27/2017
ms.openlocfilehash: 46c2280717b39160a4ce1b44d0a63382ed0b283f
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926469"
---
# <a name="simple-animations-in-xamarinforms"></a>Animações simples no xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Animation/Basic/)

_A classe ViewExtensions fornece métodos de extensão que podem ser usados para construir animações simples. Este artigo demonstra como criar e cancelar as animações usando a classe ViewExtensions._


O [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe fornece os seguintes métodos de extensão que podem ser usados para criar animações simples:

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) Anima o [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) e [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) propriedades de um [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`ScaleTo`](xref:Xamarin.Forms.VisualElement.Scale) Anima o [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propriedade de uma [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) aplica-se um aumento incremental animado ou diminuirá para o [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propriedade de uma [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) Anima o [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propriedade de uma [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) aplica-se um aumento incremental animado ou diminuirá para o [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propriedade de uma [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) Anima o [ `RotationX` ](xref:Xamarin.Forms.VisualElement.RotationX) propriedade de uma [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) Anima o [ `RotationY` ](xref:Xamarin.Forms.VisualElement.RotationY) propriedade de uma [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).
- [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) Anima o [ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity) propriedade de uma [ `VisualElement` ](xref:Xamarin.Forms.VisualElement).

Por padrão, cada animação levará 250 milissegundos. No entanto, uma duração para cada animação pode ser especificada ao criar a animação.

O [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe também inclui um [ `CancelAnimations` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) método que pode ser usado para cancelar todas as animações.

> [!NOTE]
> O [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe fornece um [ `LayoutTo` ](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)) método de extensão. No entanto, esse método se destina a ser usado pelo layouts para animar as transições entre estados de layout que contêm o tamanho e posição alterações. Portanto, ele só deve ser usado pelo [ `Layout` ](xref:Xamarin.Forms.Layout) subclasses.

Os métodos de extensão de animação na [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe são assíncronas e retorno um `Task<bool>` objeto. O valor retornado será `false` se a animação for concluída, e `true` se a animação for cancelada. Portanto, os métodos de animação normalmente devem ser usados com o `await` operador, que torna possível determinar facilmente quando uma animação for concluída. Além disso, em seguida, torna possível criar animações sequenciais com métodos de animação subsequente executados depois que o método anterior foi concluída. Para obter mais informações, consulte [composta animações](#compound).

Se houver um requisito para permitir que uma animação completa em segundo plano, em seguida, a `await` operador pode ser omitido. Nesse cenário, os métodos de extensão de animação retornará rapidamente após iniciar a animação, com a animação que ocorrem em segundo plano. Essa operação pode ser aproveitada durante a criação de animações de composição. Para obter mais informações, consulte [animações de composição](#composite).

Para obter mais informações sobre o `await` operador, consulte [visão geral do suporte assíncrono](~/cross-platform/platform/async.md).

## <a name="single-animations"></a>Animações únicas

Cada método de extensão na [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) implementa uma operação única animação progressivamente altera uma propriedade de um valor para outro valor por um período de tempo. Essa seção explora cada operação de animação.

### <a name="rotation"></a>Rotação

O exemplo de código a seguir demonstra como usar o [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método animar o [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propriedade de um [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

Esse código é animado a [ `Image` ](xref:Xamarin.Forms.Image) instância, girando até 360 graus em 2 segundos (2000 milissegundos). O [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método obtém atual [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propriedade de valor para o início da animação e, em seguida, gira desse valor para o seu primeiro argumento (360). Depois que a animação for concluída, a imagem [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propriedade é redefinida como 0. Isso garante que o `Rotation` propriedade não permaneça em 360 depois que a animação termina, o que impediria rotações adicionais.

As capturas de tela a seguir mostram a rotação em andamento em cada plataforma:

![](simple-images/rotateto.png "Animação de rotação")

### <a name="relative-rotation"></a>Rotação relativa

O exemplo de código a seguir demonstra como usar o [ `RelRotateTo` ](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método para aumentar ou diminuir de forma incremental o [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propriedade de um [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.RelRotateTo (360, 2000);
```

Esse código é animado a [ `Image` ](xref:Xamarin.Forms.Image) instância por mais de 2 segundos (2000 milissegundos) de rotação de 360 graus de sua posição inicial. O [ `RelRotateTo` ](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método obtém atual [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propriedade de valor para o início da animação e, em seguida, gira desse valor para o valor mais seu primeiro argumento (360). Isso garante que cada animação sempre será uma rotação de 360 graus a partir da posição inicial. Portanto, se uma nova animação será invocada, enquanto uma animação já está em andamento, ele será iniciada a partir da posição atual e pode terminar em uma posição que não é um incremento de 360 graus.

As capturas de tela a seguir mostram a rotação relativa em andamento em cada plataforma:

![](simple-images/relrotateto.png "Animação de rotação relativo")

### <a name="scaling"></a>Dimensionamento

O exemplo de código a seguir demonstra como usar o [ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale) método animar o [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propriedade de um [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.ScaleTo (2, 2000);
```

Esse código é animado a [ `Image` ](xref:Xamarin.Forms.Image) instância pelo escalar verticalmente para duas vezes o tamanho mais de 2 segundos (2000 milissegundos). O [ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale) método obtém atual [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) valor da propriedade (valor padrão de 1) para o início da animação e, em seguida, escalas desse valor para o seu primeiro argumento (2). Isso tem o efeito de expansão do tamanho da imagem para duas vezes seu tamanho.

As capturas de tela a seguir mostram o dimensionamento em andamento em cada plataforma:

![](simple-images/scaleto.png "Animação de colocação em escala")

> [!NOTE]
> O [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) classe define também [ `ScaleX` ](xref:Xamarin.Forms.VisualElement.ScaleX) e [ `ScaleY` ](xref:Xamarin.Forms.VisualElement.ScaleY) propriedades, que podem dimensionar o `VisualElement` forma diferente no direções horizontal e vertical. Essas propriedades podem ser animadas com o [ `Animation` ](xref:Xamarin.Forms.Animation) classe. Para obter mais informações, consulte [animações personalizadas no xamarin. Forms](custom.md).

### <a name="relative-scaling"></a>Relativo ao dimensionamento

O exemplo de código a seguir demonstra como usar o [ `RelScaleTo` ](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método animar o [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propriedade de um [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
await image.RelScaleTo (2, 2000);
```

Esse código é animado a [ `Image` ](xref:Xamarin.Forms.Image) instância pelo escalar verticalmente para duas vezes o tamanho mais de 2 segundos (2000 milissegundos). O [ `RelScaleTo` ](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método obtém atual [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) valor da propriedade para o início da animação e, em seguida, escalas desse valor para o valor mais seu primeiro argumento (2). Isso garante que cada animação sempre será uma escala de 2 a partir da posição inicial.

### <a name="scaling-and-rotation-with-anchors"></a>Escala e rotação com âncoras

O [ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX) e [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) propriedades define o centro da escala ou rotação para a [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) e [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propriedades. Portanto, seus valores também afetam a [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) e [ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale) métodos.

Considerando um [ `Image` ](xref:Xamarin.Forms.Image) que foi colocado no Centro de um layout, o exemplo de código a seguir demonstra a rotação a imagem em torno do centro do layout, definindo seu [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) propriedade:

```csharp
image.AnchorY = (Math.Min (absoluteLayout.Width, absoluteLayout.Height) / 2) / image.Height;
await image.RotateTo (360, 2000);
```

Para girar o [ `Image` ](xref:Xamarin.Forms.Image) instância em torno do centro do layout, o [ `AnchorX` ](xref:Xamarin.Forms.VisualElement.AnchorX) e [ `AnchorY` ](xref:Xamarin.Forms.VisualElement.AnchorY) propriedades devem ser definidas com valores que são relativa à largura e altura do `Image`. Neste exemplo, o centro do `Image` é definido para ser no Centro de layout e, portanto, o padrão `AnchorX` valor de 0,5 não precisará ser alterada. No entanto, o `AnchorY` propriedade será redefinida para ser um valor da parte superior do `Image` ao ponto central do layout. Isso garante que o `Image` faz uma rotação completa de 360 graus em torno do ponto central do layout, conforme mostrado nas capturas de tela seguir:

![](simple-images/rotate-anchors.png "Animação de rotação com âncoras")

### <a name="translation"></a>Conversão

O exemplo de código a seguir demonstra como usar o [ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) método animar o [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX) e [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) propriedades de um [ `Image`](xref:Xamarin.Forms.Image):

```csharp
await image.TranslateTo (-100, -100, 1000);
```

Esse código é animado a [ `Image` ](xref:Xamarin.Forms.Image) instância, convertendo-lo horizontalmente e verticalmente mais de 1 segundo (1000 milissegundos). O [ `TranslateTo` ](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) método simultaneamente traduz os pixels da imagem 100 para a esquerda e para cima de 100 pixels. Isso ocorre porque o primeiro e segundo argumentos são os dois números negativos. Fornecer números positivos convertem a imagem à direita e para baixo.

As capturas de tela a seguir mostram a tradução em andamento em cada plataforma:

![](simple-images/translateto.png "Animação de tradução")

> [!NOTE]
> Se um elemento é inicialmente disposto fora da tela e, em seguida, convertido para a tela, após a tradução de entrada o layout do elemento permanece fora da tela e o usuário não pode interagir com ele. Portanto, é recomendável que um modo de exibição deve ser disposto em sua posição final e, em seguida, todas as exigidas traduções executadas.

### <a name="fading"></a>Esmaecimento

O exemplo de código a seguir demonstra como usar o [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método animar o [ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity) propriedade de um [ `Image` ](xref:Xamarin.Forms.Image):

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

Esse código é animado a [ `Image` ](xref:Xamarin.Forms.Image) instância pelo esmaecimento-lo em mais de 4 segundos (4000 milissegundos). O [ `FadeTo` ](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método obtém atual [ `Opacity` ](xref:Xamarin.Forms.VisualElement.Opacity) valor da propriedade para o início da animação e, em seguida, esmaece desse valor para o seu primeiro argumento (1).

As capturas de tela a seguir mostram o esmaecimento em andamento em cada plataforma:

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

Neste exemplo, o [ `Image` ](xref:Xamarin.Forms.Image) é convertido em 6 segundos (6000 milissegundos). A conversão do `Image` usa cinco animações, com o `await` operador que indica que cada animação é executada sequencialmente. Portanto, métodos de animação subsequente executadas depois que o método anterior foi concluída.

<a name="composite" />

## <a name="composite-animations"></a>Animações de composição

Uma animação de composição é uma combinação de animações em que dois ou mais animações executados simultaneamente. Animações de composição podem ser criadas pela mistura animações esperadas e não é aguardada, conforme demonstrado no exemplo de código a seguir:

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

Neste exemplo, o [ `Image` ](xref:Xamarin.Forms.Image) é dimensionado e girado simultaneamente mais de 4 segundos (4000 milissegundos). O dimensionamento do `Image` usa duas animações sequenciais que ocorrem ao mesmo tempo que a rotação. O [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método é executado sem um `await` operador e retorna imediatamente, com o primeiro [ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale) , em seguida, a partir de animação. O `await` operador na primeira `ScaleTo` chamada de método atrasa a segunda `ScaleTo` chamada de método até que a primeira `ScaleTo` chamada de método foi concluída. Neste momento a `RotateTo` animação é metade forma concluída e o `Image` será girada 180 graus. Durante os últimos 2 segundos (2000 milissegundos), a segunda `ScaleTo` animação e o `RotateTo` animação ambos concluir.

### <a name="running-multiple-asynchronous-methods-concurrently"></a>A execução simultânea de vários métodos assíncronos

O `static` `Task.WhenAny` e `Task.WhenAll` métodos são usados para executar vários métodos assíncronos simultaneamente e, portanto, pode ser usados para criar animações de composição. Ambos os métodos retornam um `Task` do objeto e aceitar uma coleção de métodos de retorno que cada um `Task` objeto. O `Task.WhenAny` método é concluído quando qualquer método em sua coleção conclui a execução, conforme demonstrado no exemplo de código a seguir:

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

Neste exemplo, o `Task.WhenAny` chamada de método contém duas tarefas. A primeira tarefa gira a imagem mais de 4 segundos (4000 milissegundos), e a segunda tarefa redimensiona a imagem mais de 2 segundos (2000 milissegundos). Quando a segunda tarefa é concluída, o `Task.WhenAny` chamada de método é concluída. No entanto, mesmo que o [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) método ainda está em execução, a segunda [ `ScaleTo` ](xref:Xamarin.Forms.VisualElement.Scale) método pode começar.

O `Task.WhenAll` método é concluído quando tem concluído todos os métodos em sua coleção, conforme demonstrado no exemplo de código a seguir:

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

Neste exemplo, o `Task.WhenAll` chamada de método contém três tarefas, cada uma delas executa mais de 10 minutos. Cada `Task` faz com que um número diferente de rotações de 360 graus – 307 rotações para [ `RotateTo` ](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), 251 rotações para [ `RotateXTo` ](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))e as 199 rotações para [ `RotateYTo` ](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)). Esses valores são números primos, portanto, garantindo que as rotações não estão sincronizadas e, portanto, não resultará em padrões repetitivos.

As capturas de tela a seguir mostram as rotações vários em andamento em cada plataforma:

![](simple-images/multiple-rotations.png "Animação de composição")

## <a name="canceling-animations"></a>Cancelando a animações

Um aplicativo pode cancelar uma ou mais animações com uma chamada para o `static` [ `ViewExtensions.CancelAnimations` ](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) método, conforme demonstrado no exemplo de código a seguir:

```csharp
ViewExtensions.CancelAnimations (image);
```

Isso cancelará todas as animações que estão sendo executados no imediatamente a [ `Image` ](xref:Xamarin.Forms.Image) instância.

## <a name="summary"></a>Resumo

Este artigo demonstrou criando e Cancelando animações usando o [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe. Essa classe fornece métodos de extensão que podem ser usados para construir animações simples que giram, dimensionarem, traduzam e fade [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) instâncias.


## <a name="related-links"></a>Links relacionados

- [Visão geral do suporte assíncrono](~/cross-platform/platform/async.md)
- [Animação básica (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Animation/Basic/)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
