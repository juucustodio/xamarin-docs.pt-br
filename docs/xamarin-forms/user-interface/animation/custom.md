---
title: Animações personalizadas
description: A classe de animação é o bloco de construção de todas as animações xamarin. Forms, com os métodos de extensão da classe ViewExtensions criando um ou mais objetos de animação. Este artigo demonstra como usar a classe de animação para criar e Cancelar animações, sincronizar várias animações e criar animações personalizadas que animar propriedades que não são de animação pelos métodos de animação existente.
ms.prod: xamarin
ms.assetid: 03B2E3FC-E720-4D45-B9A0-711081FC1907
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: 238268a3ad2b82494f1d096d0cbeba97edb90366
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34847778"
---
# <a name="custom-animations"></a>Animações personalizadas

_A classe de animação é o bloco de construção de todas as animações xamarin. Forms, com os métodos de extensão da classe ViewExtensions criando um ou mais objetos de animação. Este artigo demonstra como usar a classe de animação para criar e Cancelar animações, sincronizar várias animações e criar animações personalizadas que animar propriedades que não são de animação pelos métodos de animação existente._


Um número de parâmetros deve ser especificado ao criar um `Animation` objeto, incluindo os valores inicial e final da propriedade que está sendo animada e um retorno de chamada que altera o valor da propriedade. Um `Animation` objeto também pode manter uma coleção de animações filho que pode ser executada e sincronizadas. Para obter mais informações, consulte [filho animações](#child).

Executando uma animação criada com o [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) classe, que pode ou não incluir animações filho, que é obtida chamando o [ `Commit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Commit/p/Xamarin.Forms.IAnimatable/System.String/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{System.Double,System.Boolean}/System.Func{System.Boolean}/) método. Esse método Especifica a duração da animação e entre outros itens, um retorno de chamada que controla se é preciso repetir a animação.

## <a name="creating-an-animation"></a>Criar uma animação

Ao criar um [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) do objeto, normalmente, um mínimo de três parâmetros são necessários, conforme demonstrado no exemplo de código a seguir:

```csharp
var animation = new Animation (v => image.Scale = v, 1, 2);
```

Esse código define uma animação do [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) propriedade de um [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) instância de um valor de 1 a um valor de 2. O valor animado, que é derivado, xamarin. Forms, é passado para o retorno de chamada especificado como o primeiro argumento, onde ele é usado para alterar o valor da `Scale` propriedade.

A animação é iniciada com uma chamada para o [ `Commit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Commit/p/Xamarin.Forms.IAnimatable/System.String/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{System.Double,System.Boolean}/System.Func{System.Boolean}/) método, conforme demonstrado no exemplo de código a seguir:

```csharp
animation.Commit (this, "SimpleAnimation", 16, 2000, Easing.Linear, (v, c) => image.Scale = 1, () => true);
```

Observe que o [ `Commit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Commit/p/Xamarin.Forms.IAnimatable/System.String/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{System.Double,System.Boolean}/System.Func{System.Boolean}/) método não retorna um `Task` objeto. Em vez disso, notificações são fornecidas por meio de métodos de retorno de chamada.

Os argumentos a seguir são especificados no `Commit` método:

- O primeiro argumento (*proprietário*) identifica o proprietário da animação. Isso pode ser o elemento visual que a animação é aplicada, ou outro elemento visual, como a página.
- O segundo argumento (*nome*) identifica a animação com um nome. O nome é combinado com o proprietário para identificar exclusivamente a animação. Essa identificação exclusiva, em seguida, pode ser usada para determinar se a animação está executando ([`AnimationIsRunning`](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.AnimationIsRunning/p/Xamarin.Forms.IAnimatable/System.String/)), ou para cancelá-la ([`AbortAnimation`](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.AbortAnimation/p/Xamarin.Forms.IAnimatable/System.String/)).
- O terceiro argumento (*taxa*) indica o número de milissegundos entre cada chamada para o método de retorno de chamada definido no [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) construtor
- O quarto argumento (*comprimento*) indica a duração da animação, em milissegundos.
- O quinto argumento (*atenuação*) define a função atenuação a ser usada na animação. Como alternativa, a função de easing pode ser especificada como um argumento para o [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) construtor. Para obter mais informações sobre funções de atenuação, consulte [atenuação funções](~/xamarin-forms/user-interface/animation/easing.md).
- O sexto argumento (*concluída*) é um retorno de chamada que será executado quando a animação é concluída. Esse retorno de chamada leva dois argumentos, com o primeiro argumento, que indica um valor final e o segundo argumento seja uma `bool` está definido como `true` se a animação foi cancelada. Como alternativa, o *concluída* retorno de chamada pode ser especificado como um argumento para o [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) construtor. No entanto, com uma única animação, se *concluída* retornos de chamada são especificados em ambos o `Animation` construtor e o `Commit` método, somente o retorno de chamada especificado no `Commit` método será executado.
- O sétimo argumento (*Repita*) é um retorno de chamada que permite que a animação deve ser repetido. Ele é chamado no final da animação e retornar `true` indica que a animação deve ser repetida.

O efeito geral é criar uma animação que aumenta a [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) propriedade de um [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) de 1 a 2, mais 2 segundos (2000 milissegundos), usando o [ `Linear` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.Linear/) facilitando a função. Cada vez que a animação é concluída, seu `Scale` propriedade é redefinida para 1 e repete a animação.

> [!NOTE]
> Podem ser construídas animações simultâneas, que são executados independentemente um do outro, criando um `Animation` objeto para cada animação e, em seguida, chamar o `Commit` método em cada animação.

<a name="child" />

### <a name="child-animations"></a>Animações filho

O [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) classe também oferece suporte a animações filho, que envolve a criação de um `Animation` objeto para os quais outros `Animation` objetos são adicionados. Isso permite que uma série de animações a ser executado e sincronizadas. O exemplo de código a seguir demonstra Criando e executando animações de filho:

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

Como alternativa, o exemplo de código pode ser escrito de forma mais concisa, do como demonstrado no exemplo de código a seguir:

```csharp
new Animation {
    { 0, 0.5, new Animation (v => image.Scale = v, 1, 2) },
    { 0, 1, new Animation (v => image.Rotation = v, 0, 360) },
    { 0.5, 1, new Animation (v => image.Scale = v, 2, 1) }
    }.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

Em ambos os exemplos de código, um pai [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) objeto é criado, no qual adicionais `Animation` objetos são adicionados. Os dois primeiros argumentos para o [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.Add/p/System.Double/System.Double/Xamarin.Forms.Animation/) método Especifica que quando começar e terminar a animação filho. Os valores de argumento devem estar entre 0 e 1 e representam o período relativo dentro a animação pai que a animação filho especificado estará ativa. Portanto, no exemplo de `scaleUpAnimation` ficará ativo para a primeira metade da animação, a `scaleDownAnimation` ficará ativo para a segunda metade da animação e o `rotateAnimation` estará ativa por todo o período.

O efeito geral é que a animação ocorre em 4 segundos (4.000 milissegundos). O `scaleUpAnimation` anima a [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) propriedade de 1 a 2, mais 2 segundos. O `scaleDownAnimation` , em seguida, anima a `Scale` propriedade de 2 para 1, mais 2 segundos. Embora ambas as animações de escala estão ocorrendo, o `rotateAnimation` anima a [ `Rotation` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Rotation/) propriedade de 0 a 360, mais de 4 segundos. Observe que as animações escala também usam funções de atenuação. O [ `SpringIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringIn/) facilitando a função faz com que o [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) inicialmente reduzir antes de obter maior e o [ `SpringOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/) facilitando a função faz com que o `Image` seja menor do que o tamanho real em direção ao final da animação concluída.

Há várias diferenças entre um [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) objeto que usa animações filho e que não:

- Ao usar animações filho, o *concluída* retorno de chamada em uma animação filho indica quando estiver concluída, o filho e o *concluída* retorno de chamada transmitido para o `Commit` método indica quando o animação inteira foi concluída.
- Ao usar animações filho, retornando `true` do *Repita* retorno de chamada no `Commit` método não fará com que a animação ser repetida, mas a animação continuará a ser executado sem novos valores.
- Ao incluir uma função de atenuação no `Commit` método e a função de easing retorna um valor maior que 1, a animação será encerrada. Se a função de easing retorna um valor menor que 0, o valor é fixado como 0. Para usar uma função de atenuação que retorna um valor menor que 0 ou maior que 1, ele deve ser especificado em uma das animações filho, em vez do `Commit` método.

O [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) também inclui uma classe [ `WithConcurrent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Animation.WithConcurrent/p/Xamarin.Forms.Animation/System.Double/System.Double/) métodos que podem ser usados para adicionar animações filho a um pai `Animation` objeto. No entanto, seus *começar* e *concluir* valores de argumento não são restritos para 0 para 1, mas apenas essa parte da animação filho que corresponde a um intervalo de 0 a 1 estará ativa. Por exemplo, se um `WithConcurrent` chamada de método define uma animação filho que tem como alvo um [ `Scale` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Scale/) propriedade de 1 a 6, mas com *começar* e *concluir* valores -2 e 3, o *começar* valor de -2 corresponde a um `Scale` valor de 1 e o *concluir* valor 3 corresponde a uma `Scale` valor de 6. Porque valores fora do intervalo de 0 e 1 não executar nenhuma parte de uma animação, a `Scale` propriedade será somente animada de 3 a 6.

## <a name="canceling-an-animation"></a>Cancelando uma animação

Um aplicativo pode cancelar uma animação com uma chamada para o [ `AbortAnimation` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.AbortAnimation/p/Xamarin.Forms.IAnimatable/System.String/) método de extensão, como demonstrado no exemplo de código a seguir:

```csharp
this.AbortAnimation ("SimpleAnimation");
```

Observe que as animações são identificadas exclusivamente por uma combinação do proprietário animação e o nome da animação. Portanto, o proprietário e o nome especificado ao executar a animação deve ser especificado para cancelar a animação. Portanto, o exemplo de código imediatamente cancelará a animação denominada `SimpleAnimation` que pertence a página.

## <a name="creating-a-custom-animation"></a>Criar uma animação personalizada

Os exemplos mostrados aqui até o momento demonstraram animações igualmente podem ser obtidas com os métodos de [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) classe. No entanto, a vantagem do [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) classe é que ele tem acesso para o método de retorno de chamada, que é executado quando o valor animado é alterado. Isso permite que o retorno de chamada implementar qualquer animação desejada. Por exemplo, o exemplo de código a seguir anima a [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/) propriedade de uma página definindo-a como [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) valores criados pelo [ `Color.FromHsla` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHsla/p/System.Double/System.Double/System.Double/System.Double/)método com valores de matiz, variando de 0 a 1:

```csharp
new Animation (callback: v => BackgroundColor = Color.FromHsla (v, 1, 0.5),
  start: 0,
  end: 1).Commit (this, "Animation", 16, 4000, Easing.Linear, (v, c) => BackgroundColor = Color.Default);
```

A animação resultante fornece a aparência de adiantamento de segundo plano da página, as cores do arco-íris.

Para obter mais exemplos de criação de animações complexas, incluindo uma animação de curva de Bézier, consulte [capítulo 22](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf) de [criação de aplicativos móveis com o xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md).

## <a name="creating-a-custom-animation-extension-method"></a>Criando um método de extensão da animação personalizada

Os métodos de extensão no [ `ViewExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewExtensions/) classe animar uma propriedade do seu valor atual para um valor especificado. Isso dificulta a criação, por exemplo, um `ColorTo` método animação que pode ser usado para animar uma cor de um valor para outro, porque:

- A única [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) propriedade definida pelo [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/) classe é [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/), que nem sempre é o desejado `Color` propriedade animar.
- Geralmente, o valor atual de um [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) é de propriedade [ `Color.Default` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Default/), que não é uma cor real, e que não pode ser usado em cálculos de interpolação.

A solução para esse problema é que nem o `ColorTo` método de um determinado destino [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) propriedade. Em vez disso, ele pode ser escrito com um método de retorno de chamada que passa o interpolada `Color` valor de volta para o chamador. Além disso, o método se iniciar e terminar `Color` argumentos.

O `ColorTo` método pode ser implementado como um método de extensão que usa o [ `Animate` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.Animate{T}/p/Xamarin.Forms.IAnimatable/System.String/System.Func{System.Double,T}/System.Action{T}/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{T,System.Boolean}/System.Func{System.Boolean}/) método o [ `AnimationExtensions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/) classe para fornecer sua funcionalidade. Isso ocorre porque o `Animate` método pode ser usado para propriedades de destino que não são do tipo `double`, conforme demonstrado no exemplo de código a seguir:

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

O [ `Animate` ](https://developer.xamarin.com/api/member/Xamarin.Forms.AnimationExtensions.Animate{T}/p/Xamarin.Forms.IAnimatable/System.String/System.Func{System.Double,T}/System.Action{T}/System.UInt32/System.UInt32/Xamarin.Forms.Easing/System.Action{T,System.Boolean}/System.Func{System.Boolean}/) método exige um *transformação* argumento, que é um método de retorno de chamada. A entrada para esse retorno de chamada é sempre um `double` variando de 0 a 1. Portanto, o `ColorTo` método define sua própria transformação `Func` que aceita um `double` variando de 0 a 1 e que retorna um [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) valor correspondente a esse valor. O `Color` valor é calculado pela interpolação de [ `R` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.R/), [ `G` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.G/), [ `B` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.B/), e [ `A` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.A/) valores dos dois fornecidos `Color` argumentos. O `Color` valor é então passado para o método de retorno de chamada para uma propriedade específica do aplicativo.

Essa abordagem permite que o `ColorTo` método animar quaisquer [ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/) propriedade, conforme demonstrado no exemplo de código a seguir:

```csharp
await Task.WhenAll(
  label.ColorTo(Color.Red, Color.Blue, c => label.TextColor = c, 5000),
  label.ColorTo(Color.Blue, Color.Red, c => label.BackgroundColor = c, 5000));
await this.ColorTo(Color.FromRgb(0, 0, 0), Color.FromRgb(255, 255, 255), c => BackgroundColor = c, 5000);
await boxView.ColorTo(Color.Blue, Color.Red, c => boxView.Color = c, 4000);
```

Neste exemplo de código, o `ColorTo` método anima a [ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/) e [ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/) propriedades de um [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/), o `BackgroundColor`propriedade de uma página e o [ `Color` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/) propriedade de um [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/).

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar o [ `Animation` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/) classe para criar e Cancelar animações, sincronizar várias animações e criar animações personalizadas que animar propriedades que não são de animação pela animação existente métodos. O `Animation` classe é o bloco de construção de todas as animações xamarin. Forms.


## <a name="related-links"></a>Links relacionados

- [Animações personalizadas (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/animation/custom/)
- [Animação](https://developer.xamarin.com/api/type/Xamarin.Forms.Animation/)
- [AnimationExtensions](https://developer.xamarin.com/api/type/Xamarin.Forms.AnimationExtensions/)
