---
title: Animações simples em Xamarin.Forms
description: A classe ViewExtensions fornece métodos de extensão que podem ser usados para construir animações simples. Este artigo demonstra como criar e cancelar animações usando a classe ViewExtensions.
ms.prod: xamarin
ms.assetid: 4A6FAE5A-848F-4CE0-BFA1-22A6309B5225
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a2259d9784d9b55be636e92452562a13f0750b14
ms.sourcegitcommit: c9f62ed4bab516be18606e87e89e01f57b4ad17e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98163498"
---
# <a name="simple-animations-in-no-locxamarinforms"></a>Animações simples em Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)

_A classe ViewExtensions fornece métodos de extensão que podem ser usados para construir animações simples. Este artigo demonstra como criar e cancelar animações usando a classe ViewExtensions._

A [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe fornece os seguintes métodos de extensão que podem ser usados para criar animações simples:

- [ `CancelAnimations` ] (xref: Xamarin.Forms . ViewExtensions. CancelAnimations ( Xamarin.Forms . Visualelement) cancela todas as animações.
- [ `FadeTo` ] (xref: Xamarin.Forms . ViewExtensions. Fadeto ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) anima a [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) propriedade de um [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `RelScaleTo` ] (xref: Xamarin.Forms . ViewExtensions. RelScaleTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando a tentativa)) aplica um aumento ou diminuição incremental animado à [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propriedade de um [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `RotateTo` ] (xref: Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) anima a [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propriedade de um [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `RelRotateTo` ] (xref: Xamarin.Forms . ViewExtensions. RelRotateTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando a tentativa)) aplica um aumento ou diminuição incremental animado à [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propriedade de um [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `RotateXTo` ] (xref: Xamarin.Forms . ViewExtensions. RotateXTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) anima a [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) propriedade de um [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `RotateYTo` ] (xref: Xamarin.Forms . ViewExtensions. RotateYTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) anima a [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) propriedade de um [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) Anima a [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propriedade de um [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- `ScaleXTo` Anima a [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) propriedade de um [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- `ScaleYTo` Anima a [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) propriedade de um [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `TranslateTo` ] (xref: Xamarin.Forms . ViewExtensions. transtardiato ( Xamarin.Forms . Visualelement, System. Double, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) anima as [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) Propriedades e de um [`VisualElement`](xref:Xamarin.Forms.VisualElement) .

Por padrão, cada animação levará 250 milissegundos. No entanto, uma duração para cada animação pode ser especificada ao criar a animação.

> [!NOTE]
> A [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe fornece um [ `LayoutTo` ] (xref: Xamarin.Forms . ViewExtensions. Layoutto ( Xamarin.Forms . Visualelement, Xamarin.Forms . Rectangle, System. UInt32, Xamarin.Forms . Facilitando)) método de extensão. No entanto, esse método destina-se a ser usado por layouts para animar transições entre Estados de layout que contêm alterações de tamanho e posição. Portanto, ele só deve ser usado por [`Layout`](xref:Xamarin.Forms.Layout) subclasses.

Os métodos de extensão de animação na [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe são todos assíncronos e retornam um `Task<bool>` objeto. O valor de retorno será `false` se a animação for concluída e `true` se a animação for cancelada. Portanto, os métodos de animação normalmente devem ser usados com o `await` operador, o que torna possível determinar facilmente quando uma animação é concluída. Além disso, torna-se possível criar animações sequenciais com métodos de animação subsequentes em execução após a conclusão do método anterior. Para obter mais informações, consulte [animações compostas](#compound-animations).

Se houver um requisito para permitir que uma animação seja concluída em segundo plano, o `await` operador poderá ser omitido. Nesse cenário, os métodos de extensão de animação retornarão rapidamente depois de iniciar a animação, com a animação ocorrendo em segundo plano. Essa operação pode ser aproveitada ao criar animações compostas. Para obter mais informações, consulte [animações compostas](#composite-animations).

Para obter mais informações sobre o `await` operador, consulte [visão geral do suporte assíncrono](~/cross-platform/platform/async.md).

## <a name="single-animations"></a>Animações únicas

Cada método de extensão no [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) implementa uma única operação de animação que altera progressivamente uma propriedade de um valor para outro, ao longo de um período de tempo. Esta seção explora cada operação de animação.

### <a name="rotation"></a>Rotação

O exemplo de código a seguir demonstra como usar o [ `RotateTo` ] (xref: Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) o método para animar a [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propriedade de um [`Image`](xref:Xamarin.Forms.Image) :

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

Esse código anima a [`Image`](xref:Xamarin.Forms.Image) instância girando até 360 graus em 2 segundos (2000 milissegundos). O [ `RotateTo` ] (xref: Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) o método obtém o [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) valor da propriedade atual para o início da animação e, em seguida, gira desse valor para seu primeiro argumento (360). Quando a animação for concluída, a propriedade da imagem [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) será redefinida como 0. Isso garante que a `Rotation` propriedade não permaneça em 360 após a conclusão da animação, o que impediria rotações adicionais.

As capturas de tela a seguir mostram a rotação em andamento em cada plataforma:

![Animação de rotação](simple-images/rotateto.png)

> [!NOTE]
> Além do [ `RotateTo` ] (xref: Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) o método, também há [ `RotateXTo` ] (xref: Xamarin.Forms . ViewExtensions. RotateXTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) e [ `RotateYTo` ] (xref: Xamarin.Forms . ViewExtensions. RotateYTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) métodos que animam as [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) Propriedades e, respectivamente.

### <a name="relative-rotation"></a>Rotação relativa

O exemplo de código a seguir demonstra como usar o [ `RelRotateTo` ] (xref: Xamarin.Forms . ViewExtensions. RelRotateTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) o método para aumentar ou diminuir incrementalmente a [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propriedade de um [`Image`](xref:Xamarin.Forms.Image) :

```csharp
await image.RelRotateTo (360, 2000);
```

Esse código anima a [`Image`](xref:Xamarin.Forms.Image) instância girando 360 graus de sua posição inicial em mais de 2 segundos (2000 milissegundos). O [ `RelRotateTo` ] (xref: Xamarin.Forms . ViewExtensions. RelRotateTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) o método obtém o [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) valor da propriedade atual para o início da animação e, em seguida, gira desse valor para o valor mais seu primeiro argumento (360). Isso garante que cada animação sempre será uma rotação de 360 graus da posição inicial. Portanto, se uma nova animação for invocada enquanto uma animação já estiver em andamento, ela será iniciada a partir da posição atual e poderá terminar em uma posição que não seja um incremento de 360 graus.

As capturas de tela a seguir mostram a rotação relativa em andamento em cada plataforma:

![Animação de rotação relativa](simple-images/relrotateto.png)

### <a name="scaling"></a>Dimensionamento

O exemplo de código a seguir demonstra [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) como usar o método para animar a [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propriedade de um [`Image`](xref:Xamarin.Forms.Image) :

```csharp
await image.ScaleTo (2, 2000);
```

Esse código anima a [`Image`](xref:Xamarin.Forms.Image) instância ajustando verticalmente até duas vezes seu tamanho em mais de 2 segundos (2000 milissegundos). O [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) método obtém o valor da [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propriedade atual (valor padrão de 1) para o início da animação e, em seguida, dimensiona a partir desse valor para seu primeiro argumento (2). Isso tem o efeito de expandir o tamanho da imagem para duas vezes seu tamanho.

As capturas de tela a seguir mostram o dimensionamento em andamento em cada plataforma:

![Animação em escala](simple-images/scaleto.png)

> [!NOTE]
> Além do [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) método, também existem `ScaleXTo` e `ScaleYTo` métodos que animam as [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) Propriedades e, respectivamente.

### <a name="relative-scaling"></a>Dimensionamento relativo

O exemplo de código a seguir demonstra como usar o [ `RelScaleTo` ] (xref: Xamarin.Forms . ViewExtensions. RelScaleTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) o método para animar a [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propriedade de um [`Image`](xref:Xamarin.Forms.Image) :

```csharp
await image.RelScaleTo (2, 2000);
```

Esse código anima a [`Image`](xref:Xamarin.Forms.Image) instância ajustando verticalmente até duas vezes seu tamanho em mais de 2 segundos (2000 milissegundos). O [ `RelScaleTo` ] (xref: Xamarin.Forms . ViewExtensions. RelScaleTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) o método obtém o [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) valor da propriedade atual para o início da animação e, em seguida, é dimensionado desse valor para o valor mais seu primeiro argumento (2). Isso garante que cada animação sempre será um dimensionamento de 2 a partir da posição inicial.

### <a name="scaling-and-rotation-with-anchors"></a>Dimensionamento e rotação com âncoras

As [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) Propriedades e definem o centro de dimensionamento ou rotação para as [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) Propriedades e. Portanto, seus valores também afetam o [ `RotateTo` ] (xref: Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) e [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) métodos.

Dado um [`Image`](xref:Xamarin.Forms.Image) que foi colocado no centro de um layout, o exemplo de código a seguir demonstra a rotação da imagem ao lado do centro do layout definindo sua [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) Propriedade:

```csharp
double radius = Math.Min(absoluteLayout.Width, absoluteLayout.Height) / 2;
image.AnchorY = radius / image.Height;
await image.RotateTo(360, 2000);
```

Para girar a [`Image`](xref:Xamarin.Forms.Image) instância em todo o centro do layout, as [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) Propriedades e devem ser definidas como valores relativos à largura e à altura do `Image` . Neste exemplo, o centro do `Image` é definido para estar no centro do layout e, portanto, o `AnchorX` valor padrão de 0,5 não requer alteração. No entanto, a `AnchorY` propriedade é redefinida para ser um valor da parte superior do `Image` até o ponto central do layout. Isso garante que o `Image` faça uma rotação completa de 360 graus em volta do ponto central do layout, conforme mostrado nas capturas de tela a seguir:

![Animação de rotação com âncoras](simple-images/rotate-anchors.png)

### <a name="translation"></a>Tradução

O exemplo de código a seguir demonstra como usar o [ `TranslateTo` ] (xref: Xamarin.Forms . ViewExtensions. transtardiato ( Xamarin.Forms . Visualelement, System. Double, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) o método para animar as [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) Propriedades e de um [`Image`](xref:Xamarin.Forms.Image) :

```csharp
await image.TranslateTo (-100, -100, 1000);
```

Esse código anima a [`Image`](xref:Xamarin.Forms.Image) instância traduzindo-a horizontalmente e verticalmente em 1 segundo (1000 milissegundos). O [ `TranslateTo` ] (xref: Xamarin.Forms . ViewExtensions. transtardiato ( Xamarin.Forms . Visualelement, System. Double, System. Double, System. UInt32, Xamarin.Forms . Facilitando a ocorrência)) o método simultaneamente converte a imagem 100 pixels à esquerda e 100 pixels para cima. Isso ocorre porque o primeiro e o segundo argumentos são números negativos. Fornecer números positivos converteria a imagem à direita e abaixo.

As capturas de tela a seguir mostram a tradução em andamento em cada plataforma:

![Animação de tradução](simple-images/translateto.png)

> [!NOTE]
> Se um elemento é inicialmente disposto fora da tela e, em seguida, convertido na tela, após a tradução, o layout de entrada do elemento permanece fora da tela e o usuário não pode interagir com ele. Portanto, é recomendável que uma exibição seja disposta em sua posição final e todas as traduções necessárias sejam executadas.

### <a name="fading"></a>Esmaecimento

O exemplo de código a seguir demonstra como usar o [ `FadeTo` ] (xref: Xamarin.Forms . ViewExtensions. Fadeto ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) o método para animar a [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) propriedade de um [`Image`](xref:Xamarin.Forms.Image) :

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

Esse código anima a [`Image`](xref:Xamarin.Forms.Image) instância esmaecido em mais de 4 segundos (4000 milissegundos). O [ `FadeTo` ] (xref: Xamarin.Forms . ViewExtensions. Fadeto ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) o método obtém o [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) valor da propriedade atual para o início da animação e, em seguida, esmaece a partir desse valor para seu primeiro argumento (1).

As capturas de tela a seguir mostram o esmaecimento em andamento em cada plataforma:

![Animação esmaecida](simple-images/fadeto.png)

## <a name="compound-animations"></a>Animações compostas

Uma animação composta é uma combinação sequencial de animações e pode ser criada com o `await` operador, conforme demonstrado no exemplo de código a seguir:

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image up
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

Neste exemplo, o [`Image`](xref:Xamarin.Forms.Image) é convertido em mais de 6 segundos (6000 milissegundos). A conversão do `Image` usa cinco animações, com o `await` operador indicando que cada animação é executada sequencialmente. Portanto, os métodos de animação subsequentes são executados após a conclusão do método anterior.

## <a name="composite-animations"></a>Animações compostas

Uma animação composta é uma combinação de animações em que duas ou mais animações são executadas simultaneamente. Animações compostas podem ser criadas com a combinação de animações aguardadas e não esperadas, conforme demonstrado no exemplo de código a seguir:

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

Neste exemplo, o [`Image`](xref:Xamarin.Forms.Image) é dimensionado e girado simultaneamente em 4 segundos (4000 milissegundos). O dimensionamento do `Image` usa duas animações sequenciais que ocorrem ao mesmo tempo que a rotação. O [ `RotateTo` ] (xref: Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando a tentativa)) o método é executado sem um `await` operador e retorna imediatamente, com a primeira [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) animação, começando. O `await` operador na primeira `ScaleTo` chamada de método atrasa a segunda `ScaleTo` chamada de método até que a primeira `ScaleTo` chamada de método seja concluída. Neste ponto, a `RotateTo` animação é concluída pela metade e o `Image` será girado 180 graus. Durante os 2 segundos finais (2000 milissegundos), a segunda `ScaleTo` animação e a `RotateTo` animação são concluídas.

### <a name="running-multiple-asynchronous-methods-concurrently"></a>Executando vários métodos assíncronos simultaneamente

Os `static` `Task.WhenAny` `Task.WhenAll` métodos e são usados para executar vários métodos assíncronos simultaneamente e, portanto, podem ser usados para criar animações compostas. Os dois métodos retornam um `Task` objeto e aceitam uma coleção de métodos que cada um retorna um `Task` objeto. O `Task.WhenAny` método é concluído quando qualquer método em sua coleção conclui a execução, conforme demonstrado no exemplo de código a seguir:

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

Neste exemplo, a `Task.WhenAny` chamada de método contém duas tarefas. A primeira tarefa gira a imagem em 4 segundos (4000 milissegundos) e a segunda tarefa escala a imagem em 2 segundos (2000 milissegundos). Quando a segunda tarefa é concluída, a `Task.WhenAny` chamada do método é concluída. No entanto, mesmo que o [ `RotateTo` ] (xref: Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando a operação)) o método ainda está em execução, o segundo [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) método pode começar.

O `Task.WhenAll` método é concluído quando todos os métodos em sua coleção são concluídos, conforme demonstrado no exemplo de código a seguir:

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

Neste exemplo, a `Task.WhenAll` chamada de método contém três tarefas, cada uma delas é executada em mais de 10 minutos. Cada `Task` uma delas faz um número diferente de rotações de 360 graus – 307 rotações para [ `RotateTo` ] (xref: Xamarin.Forms . ViewExtensions. RotateTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)), 251 rotações para [ `RotateXTo` ] (xref: Xamarin.Forms . ViewExtensions. RotateXTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Facilitando)) e 199 rotações para [ `RotateYTo` ] (xref: Xamarin.Forms . ViewExtensions. RotateYTo ( Xamarin.Forms . Visualelement, System. Double, System. UInt32, Xamarin.Forms . Atenuação)). Esses valores são números primos, portanto, garantir que as rotações não sejam sincronizadas e, portanto, não resultará em padrões repetitivos.

As capturas de tela a seguir mostram as várias rotações em andamento em cada plataforma:

![Animação composta](simple-images/multiple-rotations.png)

## <a name="canceling-animations"></a>Cancelando animações

Um aplicativo pode cancelar uma ou mais animações com uma chamada para o [CancelAnimations '] (xref: Xamarin.Forms . ViewExtensions. CancelAnimations ( Xamarin.Forms . Visualelement)), conforme demonstrado no exemplo de código a seguir:

```csharp
image.CancelAnimations();
```

Isso cancelará imediatamente todas as animações atualmente em execução na [`Image`](xref:Xamarin.Forms.Image) instância.

## <a name="summary"></a>Resumo

Este artigo demonstrou a criação e o cancelamento de animações usando a [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe. Essa classe fornece métodos de extensão que podem ser usados para construir animações simples que giram, dimensionam, traduzem e esmaecem as [`VisualElement`](xref:Xamarin.Forms.VisualElement) instâncias.

## <a name="related-links"></a>Links Relacionados

- [Visão geral do suporte assíncrono](~/cross-platform/platform/async.md)
- [Animação básica (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
