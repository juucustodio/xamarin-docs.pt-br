---
title: Animações personalizadas no xamarin. Forms
description: Este artigo demonstra como usar a classe de animação do xamarin. Forms para criar e Cancelar animações, sincronizar várias animações e criar animações personalizadas que animar propriedades que não estão animadas pelos métodos de animação existentes.
ms.prod: xamarin
ms.assetid: 03B2E3FC-E720-4D45-B9A0-711081FC1907
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 86fac9dd0c2f9281a0c828ace68fbf77679dce5b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759834"
---
# <a name="custom-animations-in-xamarinforms"></a>Animações personalizadas no xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)

_A classe de animação é o bloco de construção de todas as animações de xamarin. Forms, com os métodos de extensão na classe ViewExtensions criando um ou mais objetos de animação. Este artigo demonstra como usar a classe de animação para criar e Cancelar animações, sincronizar várias animações e criar animações personalizadas que animar propriedades que não estão animadas pelos métodos de animação existentes._

Um número de parâmetros deve ser especificado durante a criação de um `Animation` objeto, incluindo os valores inicial e final da propriedade sendo animada e um retorno de chamada que altera o valor da propriedade. Um `Animation` objeto também pode manter uma coleção de animações filho que podem ser executados e sincronizadas. Para obter mais informações, consulte [animações filho](#child).

Executar uma animação criada com o [ `Animation` ](xref:Xamarin.Forms.Animation) classe, que pode ou não incluir animações filho, é obtida chamando o [ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) método. Esse método Especifica a duração da animação e, entre outros itens, um retorno de chamada que controla se deve repetir a animação.

## <a name="creating-an-animation"></a>Criação de uma animação

Ao criar uma [ `Animation` ](xref:Xamarin.Forms.Animation) do objeto, normalmente, um mínimo de três parâmetros são necessários, conforme demonstrado no exemplo de código a seguir:

```csharp
var animation = new Animation (v => image.Scale = v, 1, 2);
```

Esse código define uma animação do [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propriedade de uma [ `Image` ](xref:Xamarin.Forms.Image) instância de um valor de 1 para um valor de 2. O valor animado, o que é derivado por xamarin. Forms, é passado ao retorno de chamada especificado como o primeiro argumento, em que ele é usado para alterar o valor da `Scale` propriedade.

A animação é iniciada com uma chamada para o [ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) método, conforme demonstrado no exemplo de código a seguir:

```csharp
animation.Commit (this, "SimpleAnimation", 16, 2000, Easing.Linear, (v, c) => image.Scale = 1, () => true);
```

Observe que o [ `Commit` ](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) método não retorna um `Task` objeto. Em vez disso, as notificações são fornecidas por meio de métodos de retorno de chamada.

Os argumentos a seguir são especificados no `Commit` método:

- O primeiro argumento (*proprietário*) identifica o proprietário da animação. Isso pode ser o elemento visual na qual a animação é aplicada, ou outro elemento visual, como a página.
- O segundo argumento (*nome*) identifica a animação com um nome. O nome é combinado com o proprietário para identificar exclusivamente a animação. Essa identificação exclusiva, em seguida, pode ser usada para determinar se a animação está em execução ([`AnimationIsRunning`](xref:Xamarin.Forms.AnimationExtensions.AnimationIsRunning(Xamarin.Forms.IAnimatable,System.String))), ou para cancelá-la ([`AbortAnimation`](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String))).
- O terceiro argumento (*taxa*) indica o número de milissegundos entre cada chamada para o método de retorno de chamada definido na [ `Animation` ](xref:Xamarin.Forms.Animation) construtor
- O quarto argumento (*comprimento*) indica a duração da animação, em milissegundos.
- O quinto argumento (*atenuação*) define a função de easing a ser usado na animação. Como alternativa, a função de easing pode ser especificada como um argumento para o [ `Animation` ](xref:Xamarin.Forms.Animation) construtor. Para obter mais informações sobre funções de easing, consulte [funções de Easing](~/xamarin-forms/user-interface/animation/easing.md).
- O sexto argumento (*concluído*) é um retorno de chamada que será executado quando a animação for concluída. Esse retorno de chamada usa dois argumentos, com o primeiro argumento que indica um valor final e o segundo argumento que está sendo uma `bool` que é definido como `true` se a animação foi cancelada. Como alternativa, o *concluída* retorno de chamada pode ser especificado como um argumento para o [ `Animation` ](xref:Xamarin.Forms.Animation) construtor. No entanto, com uma única animação, se *concluída* retornos de chamada especificados em ambos o `Animation` construtor e o `Commit` método, apenas o retorno de chamada especificado no `Commit` método será executado.
- O sétimo argumento (*Repita*) é um retorno de chamada que permite que a animação ser repetida. Ele é chamado no final da animação e retornando `true` indica que a animação deve ser repetida.

O efeito geral é criar uma animação que aumenta a [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propriedade de uma [ `Image` ](xref:Xamarin.Forms.Image) de 1 a 2, mais de 2 segundos (2000 milissegundos), usando o [ `Linear` ](xref:Xamarin.Forms.Easing.Linear) função de easing. Cada vez que a animação for concluída, seu `Scale` propriedade é redefinida como 1 e a animação se repete.

> [!NOTE]
> Animações simultâneas, que são executados independentemente uns dos outros podem ser construídas com a criação de um `Animation` do objeto para cada animação e, em seguida, chamar o `Commit` método em cada animação.

<a name="child" />

### <a name="child-animations"></a>Animações filho

O [ `Animation` ](xref:Xamarin.Forms.Animation) classe também dá suporte a animações filho, que envolve a criação de uma `Animation` objeto para os quais outros `Animation` objetos são adicionados. Isso permite que uma série de animações a ser executado e sincronizadas. O exemplo de código a seguir demonstra como criar e executar animações filho:

```csharp
var parentAnimation = new Animation ();
var scaleUpAnimation = new Animation (v => image.Scale = v, 1, 2, Easing.SpringIn);
var rotateAnimation = new Animation (v => image.Rotation = v, 0, 360);
var scaleDownAnimation = new Animation (v => image.Scale = v, 2, 1, Easing.SpringOut);

parentAnimation.Add (0, 0.5, scaleUpAnimation);
parentAnimation.Add (0, 1, rotateAnimation);
parentAnimation.Add (0.5, 1, scaleDownAnimation);

parentAnimation.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

Como alternativa, o exemplo de código pode ser gravado mais concisa, conforme demonstrado no exemplo de código a seguir:

```csharp
new Animation {
    { 0, 0.5, new Animation (v => image.Scale = v, 1, 2) },
    { 0, 1, new Animation (v => image.Rotation = v, 0, 360) },
    { 0.5, 1, new Animation (v => image.Scale = v, 2, 1) }
    }.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

Nos dois exemplos de código, um pai [ `Animation` ](xref:Xamarin.Forms.Animation) objeto é criado, no qual adicionais `Animation` objetos são adicionados. Os dois primeiros argumentos para o [ `Add` ](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)) método especifique quando deve começar e terminar a animação de filho. Os valores de argumento devem ser entre 0 e 1 e representar o período relativo dentro da animação pai que a animação filho especificado estará ativa. Portanto, no exemplo o `scaleUpAnimation` ficará ativo para a primeira metade da animação, o `scaleDownAnimation` ficará ativo para a segunda metade da animação e o `rotateAnimation` estará ativo durante todo o período.

O efeito geral é que a animação ocorre mais de 4 segundos (4000 milissegundos). O `scaleUpAnimation` anima a [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propriedade de 1 a 2, mais de 2 segundos. O `scaleDownAnimation` , em seguida, anima a `Scale` propriedade de 2 para 1, mais de 2 segundos. Embora ambas as animações de escala estão ocorrendo, o `rotateAnimation` anima a [ `Rotation` ](xref:Xamarin.Forms.VisualElement.Rotation) propriedade de 0 a 360, mais de 4 segundos. Observe que as animações de escala também usam funções de easing. O [ `SpringIn` ](xref:Xamarin.Forms.Easing.SpringIn) função de easing faz com que o [ `Image` ](xref:Xamarin.Forms.Image) reduzir inicialmente antes de obter maior e o [ `SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut) função de easing faz com que o `Image` se torne menor do que seu tamanho real até o final da animação completa.

Há várias diferenças entre um [ `Animation` ](xref:Xamarin.Forms.Animation) objeto que usa animações filho e outro que não:

- Ao usar animações filho, o *concluída* retorno de chamada em uma animação de filho indica quando o filho for concluída e o *concluída* retorno de chamada passada para o `Commit` método indica quando o toda animação foi concluída.
- Ao usar animações filho, retornando `true` do *Repita* retorno de chamada a `Commit` método não fará com que a animação ser repetida, mas a animação será continuarão sendo executados sem novos valores.
- Ao incluir uma função de easing a `Commit` método e a função de easing retorna um valor maior que 1, a animação será encerrada. Se a função de easing retorna um valor menor que 0, o valor será fixado como 0. Para usar uma função de easing que retorna um valor menor que 0 ou maior que 1, ele deve ser especificado em uma das animações filho, em vez de no `Commit` método.

O [ `Animation` ](xref:Xamarin.Forms.Animation) classe também inclui [ `WithConcurrent` ](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double)) métodos que podem ser usados para adicionar animações filho a um pai `Animation` objeto. No entanto, suas *começar* e *concluir* valores de argumento não são restritos para 0 para 1, mas apenas essa parte da animação filho que corresponde a um intervalo de 0 a 1 estará ativa. Por exemplo, se um `WithConcurrent` chamada de método define uma animação de filho que tem como alvo um [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propriedade de 1 a 6, mas com *começar* e *concluir* valores de -2 e 3, o *começar* valor de -2 corresponde a um `Scale` valor de 1 e o *concluir* valor 3 corresponde a um `Scale` valor de 6. Porque valores fora do intervalo de 0 e 1 não reproduzir nenhuma parte em uma animação, o `Scale` propriedade apenas será ser animada de 3 a 6.

## <a name="canceling-an-animation"></a>Cancelando uma animação

Um aplicativo pode cancelar uma animação com uma chamada para o [ `AbortAnimation` ](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String)) método de extensão, como demonstrado no exemplo de código a seguir:

```csharp
this.AbortAnimation ("SimpleAnimation");
```

Observe que as animações são identificadas exclusivamente por uma combinação do proprietário de animação e o nome da animação. Portanto, o proprietário e o nome especificado ao executar a animação deve ser especificado para cancelar a animação. Portanto, o exemplo de código imediatamente cancelará a animação chamada `SimpleAnimation` que pertence a página.

## <a name="creating-a-custom-animation"></a>Criar uma animação personalizada

Os exemplos mostrados aqui até agora demonstraram animações igualmente pode ser obtidas com os métodos de [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe. No entanto, a vantagem dos [ `Animation` ](xref:Xamarin.Forms.Animation) classe é que ele tenha acesso para o método de retorno de chamada, que é executado quando o valor animado é alterado. Isso permite que o retorno de chamada implementar qualquer animação desejada. Por exemplo, o exemplo de código a seguir anima a [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor) propriedade de uma página definindo-a como [ `Color` ](xref:Xamarin.Forms.Color) valores criados pelo [ `Color.FromHsla` ](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double))método, com valores de matiz, variando de 0 a 1:

```csharp
new Animation (callback: v => BackgroundColor = Color.FromHsla (v, 1, 0.5),
  start: 0,
  end: 1).Commit (this, "Animation", 16, 4000, Easing.Linear, (v, c) => BackgroundColor = Color.Default);
```

A animação resultante fornece a aparência de aprimorando o plano de fundo da página por meio das cores do arco-íris.

Para obter mais exemplos de criação de animações complexas, incluindo uma animação de curva de Bézier, consulte [capítulo 22](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf) dos [criação de aplicativos móveis com xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md).

## <a name="creating-a-custom-animation-extension-method"></a>Criando um método de extensão de animação personalizada

Os métodos de extensão na [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) classe animar uma propriedade de seu valor atual para um valor especificado. Isso dificulta a criação, por exemplo, um `ColorTo` método de animação que pode ser usado para animar uma cor de um valor para outro, porque:

- As únicas [ `Color` ](xref:Xamarin.Forms.Color) propriedade definida pelo [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) classe é [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor), que nem sempre é o desejado `Color` propriedade para animar.
- Geralmente, o valor atual de um [ `Color` ](xref:Xamarin.Forms.Color) é de propriedade [ `Color.Default` ](xref:Xamarin.Forms.Color.Default), que não é uma cor real, e que não pode ser usado em cálculos de interpolação.

A solução para esse problema é não ter o `ColorTo` método de um determinado destino [ `Color` ](xref:Xamarin.Forms.Color) propriedade. Em vez disso, ele pode ser escrito com um método de retorno de chamada que transmite o interpolada `Color` valor para o chamador. Além disso, o método será levar o início e término `Color` argumentos.

O `ColorTo` método pode ser implementado como um método de extensão que usa o [ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*) método na [ `AnimationExtensions` ](xref:Xamarin.Forms.AnimationExtensions) classe para fornecer sua funcionalidade. Isso ocorre porque o `Animate` método pode ser usado para propriedades de destino que não são do tipo `double`, conforme demonstrado no exemplo de código a seguir:

```csharp
public static class ViewExtensions
{
  public static Task<bool> ColorTo(this VisualElement self, Color fromColor, Color toColor, Action<Color> callback, uint length = 250, Easing easing = null)
  {
    Func<double, Color> transform = (t) =>
      Color.FromRgba(fromColor.R + t * (toColor.R - fromColor.R),
                     fromColor.G + t * (toColor.G - fromColor.G),
                     fromColor.B + t * (toColor.B - fromColor.B),
                     fromColor.A + t * (toColor.A - fromColor.A));
    return ColorAnimation(self, "ColorTo", transform, callback, length, easing);
  }

  public static void CancelAnimation(this VisualElement self)
  {
    self.AbortAnimation("ColorTo");
  }

  static Task<bool> ColorAnimation(VisualElement element, string name, Func<double, Color> transform, Action<Color> callback, uint length, Easing easing)
  {
    easing = easing ?? Easing.Linear;
    var taskCompletionSource = new TaskCompletionSource<bool>();

    element.Animate<Color>(name, transform, callback, 16, length, easing, (v, c) => taskCompletionSource.SetResult(c));
    return taskCompletionSource.Task;
  }
}
```

O [ `Animate` ](xref:Xamarin.Forms.AnimationExtensions.Animate*) método exige um *transformar* argumento, que é um método de retorno de chamada. A entrada para esse retorno de chamada é sempre um `double` variando de 0 a 1. Portanto, o `ColorTo` método define sua própria transformação `Func` que aceita um `double` variando de 0 a 1 e que retorna um [ `Color` ](xref:Xamarin.Forms.Color) valor correspondente a esse valor. O `Color` valor é calculado pela interpolação a [ `R` ](xref:Xamarin.Forms.Color.R), [ `G` ](xref:Xamarin.Forms.Color.G), [ `B` ](xref:Xamarin.Forms.Color.B), e [ `A` ](xref:Xamarin.Forms.Color.A) valores dos dois fornecidos `Color` argumentos. O `Color` valor é então passado para o método de retorno de chamada para o aplicativo para uma determinada propriedade.

Essa abordagem permite que o `ColorTo` método para animar qualquer [ `Color` ](xref:Xamarin.Forms.Color) propriedade, conforme demonstrado no exemplo de código a seguir:

```csharp
await Task.WhenAll(
  label.ColorTo(Color.Red, Color.Blue, c => label.TextColor = c, 5000),
  label.ColorTo(Color.Blue, Color.Red, c => label.BackgroundColor = c, 5000));
await this.ColorTo(Color.FromRgb(0, 0, 0), Color.FromRgb(255, 255, 255), c => BackgroundColor = c, 5000);
await boxView.ColorTo(Color.Blue, Color.Red, c => boxView.Color = c, 4000);
```

Neste exemplo de código, o `ColorTo` método anima a [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor) e [ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor) propriedades de um [ `Label` ](xref:Xamarin.Forms.Label), a `BackgroundColor`propriedade de uma página e o [ `Color` ](xref:Xamarin.Forms.BoxView.Color) propriedade de um [ `BoxView` ](xref:Xamarin.Forms.BoxView).

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar o [ `Animation` ](xref:Xamarin.Forms.Animation) classe para criar e Cancelar animações, sincronizar várias animações e criar animações personalizadas que animar propriedades que não estão animadas pela animação existente métodos. O `Animation` classe é o bloco de construção de todas as animações de xamarin. Forms.

## <a name="related-links"></a>Links relacionados

- [Animações personalizadas (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)
- [Animação](xref:Xamarin.Forms.Animation)
- [AnimationExtensions](xref:Xamarin.Forms.AnimationExtensions)
