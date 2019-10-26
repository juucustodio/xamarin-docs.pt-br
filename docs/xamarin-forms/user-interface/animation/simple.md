---
title: Animações simples no Xamarin. Forms
description: A classe ViewExtensions fornece métodos de extensão que podem ser usados para construir animações simples. Este artigo demonstra como criar e cancelar animações usando a classe ViewExtensions.
ms.prod: xamarin
ms.assetid: 4A6FAE5A-848F-4CE0-BFA1-22A6309B5225
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2019
ms.openlocfilehash: 116911787db128b103fb555554076704a0549db5
ms.sourcegitcommit: f8583585c501607fdfa061b95e9a9f385ed1d591
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2019
ms.locfileid: "72959157"
---
# <a name="simple-animations-in-xamarinforms"></a>Animações simples no Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)

_A classe ViewExtensions fornece métodos de extensão que podem ser usados para construir animações simples. Este artigo demonstra como criar e cancelar animações usando a classe ViewExtensions._

A classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) fornece os seguintes métodos de extensão que podem ser usados para criar animações simples:

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) anima as propriedades [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) e [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) de uma [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) anima a propriedade [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) de um [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) aplica um aumento ou diminuição incremental animado à propriedade [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) de uma [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) anima a propriedade [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) de um [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) aplica um aumento ou diminuição incremental animado à propriedade [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) de uma [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) anima a propriedade [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) de um [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) anima a propriedade [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) de um [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) anima a propriedade [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) de um [`VisualElement`](xref:Xamarin.Forms.VisualElement).

Por padrão, cada animação levará 250 milissegundos. No entanto, uma duração para cada animação pode ser especificada ao criar a animação.

A classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) também inclui um método [`CancelAnimations`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) que pode ser usado para cancelar qualquer animação.

> [!NOTE]
> A classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) fornece um método de extensão [`LayoutTo`](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)) . No entanto, esse método destina-se a ser usado por layouts para animar transições entre Estados de layout que contêm alterações de tamanho e posição. Portanto, ele só deve ser usado por [`Layout`](xref:Xamarin.Forms.Layout) subclasses.

Os métodos de extensão de animação na classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) são todos assíncronos e retornam um objeto `Task<bool>`. O valor de retorno será `false` se a animação for concluída e `true` se a animação for cancelada. Portanto, os métodos de animação normalmente devem ser usados com o operador de `await`, o que torna possível determinar facilmente quando uma animação é concluída. Além disso, torna-se possível criar animações sequenciais com métodos de animação subsequentes em execução após a conclusão do método anterior. Para obter mais informações, consulte [animações compostas](#compound).

Se houver um requisito para permitir que uma animação seja concluída em segundo plano, o operador de `await` poderá ser omitido. Nesse cenário, os métodos de extensão de animação retornarão rapidamente depois de iniciar a animação, com a animação ocorrendo em segundo plano. Essa operação pode ser aproveitada ao criar animações compostas. Para obter mais informações, consulte [animações compostas](#composite).

Para obter mais informações sobre o operador de `await`, consulte [visão geral do suporte assíncrono](~/cross-platform/platform/async.md).

## <a name="single-animations"></a>Animações únicas

Cada método de extensão no [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) implementa uma única operação de animação que altera progressivamente uma propriedade de um valor para outro ao longo de um período de tempo. Esta seção explora cada operação de animação.

### <a name="rotation"></a>Gira

O exemplo de código a seguir demonstra como usar o método [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) para animar a propriedade [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) de um [`Image`](xref:Xamarin.Forms.Image):

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

Esse código anima a instância de [`Image`](xref:Xamarin.Forms.Image) girando até 360 graus em 2 segundos (2000 milissegundos). O método [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) Obtém o valor da propriedade [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) atual para o início da animação e, em seguida, gira desse valor para seu primeiro argumento (360). Quando a animação for concluída, a propriedade de [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) da imagem será redefinida como 0. Isso garante que a propriedade `Rotation` não permaneça às 360 após a conclusão da animação, o que impediria rotações adicionais.

As capturas de tela a seguir mostram a rotação em andamento em cada plataforma:

![](simple-images/rotateto.png "Rotation Animation")

### <a name="relative-rotation"></a>Rotação relativa

O exemplo de código a seguir demonstra o uso do método [`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) para aumentar ou diminuir incrementalmente a propriedade [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) de um [`Image`](xref:Xamarin.Forms.Image):

```csharp
await image.RelRotateTo (360, 2000);
```

Esse código anima a instância de [`Image`](xref:Xamarin.Forms.Image) girando 360 graus de sua posição inicial em mais de 2 segundos (2000 milissegundos). O método [`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) Obtém o valor da propriedade [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) atual para o início da animação e, em seguida, gira desse valor para o valor mais seu primeiro argumento (360). Isso garante que cada animação sempre será uma rotação de 360 graus da posição inicial. Portanto, se uma nova animação for invocada enquanto uma animação já estiver em andamento, ela será iniciada a partir da posição atual e poderá terminar em uma posição que não seja um incremento de 360 graus.

As capturas de tela a seguir mostram a rotação relativa em andamento em cada plataforma:

![](simple-images/relrotateto.png "Relative Rotation Animation")

### <a name="scaling"></a>Dimensionamento

O exemplo de código a seguir demonstra como usar o método [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) para animar a propriedade [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) de um [`Image`](xref:Xamarin.Forms.Image):

```csharp
await image.ScaleTo (2, 2000);
```

Esse código anima a instância de [`Image`](xref:Xamarin.Forms.Image) ao escalar verticalmente para duas vezes seu tamanho em mais de 2 segundos (2000 milissegundos). O método [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) Obtém o valor da propriedade [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) atual (valor padrão de 1) para o início da animação e, em seguida, dimensiona a partir desse valor para seu primeiro argumento (2). Isso tem o efeito de expandir o tamanho da imagem para duas vezes seu tamanho.

As capturas de tela a seguir mostram o dimensionamento em andamento em cada plataforma:

![](simple-images/scaleto.png "Scaling Animation")

> [!NOTE]
> A classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) define também as propriedades [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) e [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY), que podem dimensionar o `VisualElement` de forma diferente nos sentidos horizontal e vertical. Essas propriedades podem ser animadas com a classe [`Animation`](xref:Xamarin.Forms.Animation) . Para obter mais informações, consulte [animações personalizadas no Xamarin. Forms](custom.md).

### <a name="relative-scaling"></a>Dimensionamento relativo

O exemplo de código a seguir demonstra como usar o método [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) para animar a propriedade [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) de um [`Image`](xref:Xamarin.Forms.Image):

```csharp
await image.RelScaleTo (2, 2000);
```

Esse código anima a instância de [`Image`](xref:Xamarin.Forms.Image) ao escalar verticalmente para duas vezes seu tamanho em mais de 2 segundos (2000 milissegundos). O método [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) Obtém o valor da propriedade [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) atual para o início da animação e, em seguida, é dimensionado desse valor para o valor mais seu primeiro argumento (2). Isso garante que cada animação sempre será um dimensionamento de 2 a partir da posição inicial.

### <a name="scaling-and-rotation-with-anchors"></a>Dimensionamento e rotação com âncoras

As propriedades [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) e [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) definem o centro de dimensionamento ou rotação para as propriedades [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) e [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) . Portanto, seus valores também afetam os métodos [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) e [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) .

Dado um [`Image`](xref:Xamarin.Forms.Image) que foi colocado no centro de um layout, o exemplo de código a seguir demonstra a rotação da imagem ao lado do centro do layout definindo sua propriedade [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) :

```csharp
double radius = Math.Min(absoluteLayout.Width, absoluteLayout.Height) / 2;
image.AnchorY = radius / image.Height;
await image.RotateTo(360, 2000);
```

Para girar a instância de [`Image`](xref:Xamarin.Forms.Image) ao todo o centro do layout, as propriedades [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) e [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) devem ser definidas como valores relativos à largura e à altura do `Image`. Neste exemplo, o centro do `Image` é definido para estar no centro do layout e, portanto, o valor de `AnchorX` padrão de 0,5 não requer alteração. No entanto, a propriedade `AnchorY` é redefinida para ser um valor da parte superior do `Image` para o ponto central do layout. Isso garante que o `Image` faça uma rotação completa de 360 graus em volta do ponto central do layout, conforme mostrado nas capturas de tela a seguir:

![](simple-images/rotate-anchors.png "Rotation Animation with Anchors")

### <a name="translation"></a>Conversão

O exemplo de código a seguir demonstra o uso do método [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) para animar as propriedades [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) e [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) de uma [`Image`](xref:Xamarin.Forms.Image):

```csharp
await image.TranslateTo (-100, -100, 1000);
```

Esse código anima a instância de [`Image`](xref:Xamarin.Forms.Image) convertendo-a horizontalmente e verticalmente em 1 segundo (1000 milissegundos). O método [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) simultaneamente converte a imagem 100 pixels à esquerda e 100 pixels em diante. Isso ocorre porque o primeiro e o segundo argumentos são números negativos. Fornecer números positivos converteria a imagem à direita e abaixo.

As capturas de tela a seguir mostram a tradução em andamento em cada plataforma:

![](simple-images/translateto.png "Translation Animation")

> [!NOTE]
> Se um elemento é inicialmente disposto fora da tela e, em seguida, convertido na tela, após a tradução, o layout de entrada do elemento permanece fora da tela e o usuário não pode interagir com ele. Portanto, é recomendável que uma exibição seja disposta em sua posição final e todas as traduções necessárias sejam executadas.

### <a name="fading"></a>Esmaecimento

O exemplo de código a seguir demonstra como usar o método [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) para animar a propriedade [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) de um [`Image`](xref:Xamarin.Forms.Image):

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

Esse código anima a instância de [`Image`](xref:Xamarin.Forms.Image) ao esmaecimento em mais de 4 segundos (4000 milissegundos). O método [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) Obtém o valor da propriedade [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) atual para o início da animação e, em seguida, esmaece a partir desse valor para seu primeiro argumento (1).

As capturas de tela a seguir mostram o esmaecimento em andamento em cada plataforma:

![](simple-images/fadeto.png "Fading Animation")

<a name="compound" />

## <a name="compound-animations"></a>Animações compostas

Uma animação composta é uma combinação sequencial de animações e pode ser criada com o operador de `await`, conforme demonstrado no exemplo de código a seguir:

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image up
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

Neste exemplo, o [`Image`](xref:Xamarin.Forms.Image) é convertido em mais de 6 segundos (6000 milissegundos). A conversão do `Image` usa cinco animações, com o operador `await` indicando que cada animação é executada sequencialmente. Portanto, os métodos de animação subsequentes são executados após a conclusão do método anterior.

<a name="composite" />

## <a name="composite-animations"></a>Animações compostas

Uma animação composta é uma combinação de animações em que duas ou mais animações são executadas simultaneamente. Animações compostas podem ser criadas com a combinação de animações aguardadas e não esperadas, conforme demonstrado no exemplo de código a seguir:

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

Neste exemplo, a [`Image`](xref:Xamarin.Forms.Image) é dimensionada e girada simultaneamente em 4 segundos (4000 milissegundos). O dimensionamento da `Image` usa duas animações sequenciais que ocorrem ao mesmo tempo que a rotação. O método [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) é executado sem um operador de `await` e retorna imediatamente, com a primeira animação [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) , começando. O operador de `await` na primeira chamada de método `ScaleTo` atrasa a segunda chamada de método `ScaleTo` até que a primeira chamada de método `ScaleTo` tenha sido concluída. Neste ponto, a animação de `RotateTo` é concluída pela metade e o `Image` será girado 180 graus. Durante os 2 segundos finais (2000 milissegundos), a segunda `ScaleTo` animação e a animação de `RotateTo` são concluídas.

### <a name="running-multiple-asynchronous-methods-concurrently"></a>Executando vários métodos assíncronos simultaneamente

Os métodos `static` `Task.WhenAny` e `Task.WhenAll` são usados para executar vários métodos assíncronos simultaneamente e, portanto, podem ser usados para criar animações compostas. Os dois métodos retornam um objeto `Task` e aceitam uma coleção de métodos que cada um retorna um objeto `Task`. O método `Task.WhenAny` é concluído quando qualquer método em sua coleção conclui a execução, conforme demonstrado no exemplo de código a seguir:

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

Neste exemplo, a chamada do método `Task.WhenAny` contém duas tarefas. A primeira tarefa gira a imagem em 4 segundos (4000 milissegundos) e a segunda tarefa escala a imagem em 2 segundos (2000 milissegundos). Quando a segunda tarefa é concluída, a chamada do método `Task.WhenAny` é concluída. No entanto, embora o método de [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) ainda esteja em execução, o segundo método de [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) pode começar.

O método `Task.WhenAll` é concluído quando todos os métodos em sua coleção são concluídos, conforme demonstrado no exemplo de código a seguir:

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

Neste exemplo, a chamada do método `Task.WhenAll` contém três tarefas, cada uma delas é executada em mais de 10 minutos. Cada `Task` faz um número diferente de rotações de 360 graus – 307 rotações para [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), 251 rotações para [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))e as rotações de 199 para [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)). Esses valores são números primos, portanto, garantir que as rotações não sejam sincronizadas e, portanto, não resultará em padrões repetitivos.

As capturas de tela a seguir mostram as várias rotações em andamento em cada plataforma:

![](simple-images/multiple-rotations.png "Composite Animation")

## <a name="canceling-animations"></a>Cancelando animações

Um aplicativo pode cancelar uma ou mais animações com uma chamada para o método `static` [`ViewExtensions.CancelAnimations`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) , conforme demonstrado no exemplo de código a seguir:

```csharp
ViewExtensions.CancelAnimations (image);
```

Isso cancelará imediatamente todas as animações atualmente em execução na instância de [`Image`](xref:Xamarin.Forms.Image) .

## <a name="summary"></a>Resumo

Este artigo demonstrou a criação e o cancelamento de animações usando a classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) . Essa classe fornece métodos de extensão que podem ser usados para construir animações simples que giram, dimensionam, traduzemm e esmaecem [`VisualElement`](xref:Xamarin.Forms.VisualElement) instâncias.

## <a name="related-links"></a>Links relacionados

- [Visão geral do suporte assíncrono](~/cross-platform/platform/async.md)
- [Animação básica (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
