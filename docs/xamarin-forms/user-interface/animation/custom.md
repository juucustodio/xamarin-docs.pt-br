---
title: Animações personalizadas no xamarin. Forms
description: Este artigo demonstra como usar a classe de animação do xamarin. Forms para criar e Cancelar animações, sincronizar várias animações e criar animações personalizadas que animar propriedades que não estão animadas pelos métodos de animação existentes.
ms.prod: xamarin
ms.assetid: 03B2E3FC-E720-4D45-B9A0-711081FC1907
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/10/2019
ms.openlocfilehash: 405d7990b622b890aa3d66bd632662f086441666
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2020
ms.locfileid: "78292251"
---
# <a name="custom-animations-in-xamarinforms"></a>Animações personalizadas no xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)

_A classe Animation é o bloco de construção de todas as animações do Xamarin. Forms, com os métodos de extensão na classe ViewExtensions, criando um ou mais objetos de animação. Este artigo demonstra como usar a classe Animation para criar e cancelar animações, sincronizar várias animações e criar animações personalizadas que animam propriedades que não são animadas pelos métodos de animação existentes._

Um número de parâmetros deve ser especificado ao criar um objeto de `Animation`, incluindo os valores de início e término da propriedade sendo animada e um retorno de chamada que altera o valor da propriedade. Um objeto `Animation` também pode manter uma coleção de animações filho que podem ser executadas e sincronizadas. Para obter mais informações, consulte [animações filhas](#child).

A execução de uma animação criada com a classe [`Animation`](xref:Xamarin.Forms.Animation) , que pode ou não incluir animações filhas, é obtida chamando o método [`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) . Esse método Especifica a duração da animação e, entre outros itens, um retorno de chamada que controla se deve repetir a animação.

Além disso, a classe [`Animation`](xref:Xamarin.Forms.Animation) tem uma propriedade `IsEnabled` que pode ser examinada para determinar se as animações foram desabilitadas pelo sistema operacional, como quando o modo de economia de energia está ativado.

## <a name="create-an-animation"></a>Criar uma animação

Ao criar um objeto de [`Animation`](xref:Xamarin.Forms.Animation) , normalmente, um mínimo de três parâmetros é necessário, conforme demonstrado no exemplo de código a seguir:

```csharp
var animation = new Animation (v => image.Scale = v, 1, 2);
```

Esse código define uma animação da propriedade [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) de uma instância de [`Image`](xref:Xamarin.Forms.Image) de um valor de 1 a um valor de 2. O valor animado, que é derivado de Xamarin. Forms, é passado para o retorno de chamada especificado como o primeiro argumento, em que é usado para alterar o valor da propriedade `Scale`.

A animação é iniciada com uma chamada para o método [`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) , conforme demonstrado no exemplo de código a seguir:

```csharp
animation.Commit (this, "SimpleAnimation", 16, 2000, Easing.Linear, (v, c) => image.Scale = 1, () => true);
```

Observe que o método [`Commit`](xref:Xamarin.Forms.Animation.Commit(Xamarin.Forms.IAnimatable,System.String,System.UInt32,System.UInt32,Xamarin.Forms.Easing,System.Action{System.Double,System.Boolean},System.Func{System.Boolean})) não retorna um objeto `Task`. Em vez disso, as notificações são fornecidas por meio de métodos de retorno de chamada.

Os seguintes argumentos são especificados no método `Commit`:

- O primeiro argumento (*proprietário*) identifica o proprietário da animação. Isso pode ser o elemento visual na qual a animação é aplicada, ou outro elemento visual, como a página.
- O segundo argumento (*Name*) identifica a animação com um nome. O nome é combinado com o proprietário para identificar exclusivamente a animação. Essa identificação exclusiva pode ser usada para determinar se a animação está em execução ([`AnimationIsRunning`](xref:Xamarin.Forms.AnimationExtensions.AnimationIsRunning(Xamarin.Forms.IAnimatable,System.String))) ou cancelá-la ([`AbortAnimation`](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String))).
- O terceiro argumento (*Rate*) indica o número de milissegundos entre cada chamada para o método de retorno de chamada definido no construtor de [`Animation`](xref:Xamarin.Forms.Animation)
- O quarto argumento (*comprimento*) indica a duração da animação, em milissegundos.
- O quinto argumento (*facilitando*) define a função de atenuação a ser usada na animação. Como alternativa, a função de atenuação pode ser especificada como um argumento para o construtor de [`Animation`](xref:Xamarin.Forms.Animation) . Para obter mais informações sobre as funções de atenuação, consulte [facilitando funções](~/xamarin-forms/user-interface/animation/easing.md).
- O sexto argumento (*concluído*) é um retorno de chamada que será executado quando a animação for concluída. Esse retorno de chamada usa dois argumentos, com o primeiro argumento indicando um valor final e o segundo argumento sendo um `bool` definido como `true` se a animação foi cancelada. Como alternativa, o retorno de chamada *concluído* pode ser especificado como um argumento para o construtor de [`Animation`](xref:Xamarin.Forms.Animation) . No entanto, com uma única animação, se as chamadas de retorno *concluídas* forem especificadas tanto no Construtor `Animation` quanto no método `Commit`, somente o retorno de chamada especificado no método `Commit` será executado.
- O sétimo argumento (*REPEAT*) é um retorno de chamada que permite que a animação seja repetida. Ele é chamado no final da animação e retornar `true` indica que a animação deve ser repetida.

O efeito geral é criar uma animação que aumente a propriedade [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) de um [`Image`](xref:Xamarin.Forms.Image) de 1 para 2, mais de 2 segundos (2000 milissegundos), usando a função de atenuação [`Linear`](xref:Xamarin.Forms.Easing.Linear) . Toda vez que a animação é concluída, sua propriedade `Scale` é redefinida como 1 e a animação se repete.

> [!NOTE]
> Animações simultâneas, que são executadas de forma independente umas das outras, podem ser construídas criando um objeto de `Animation` para cada animação e, em seguida, chamando o método `Commit` em cada animação.

<a name="child" />

### <a name="child-animations"></a>Animações filho

A classe [`Animation`](xref:Xamarin.Forms.Animation) também dá suporte a animações filhas, que envolve a criação de um objeto `Animation` ao qual outros objetos `Animation` são adicionados. Isso permite que uma série de animações a ser executado e sincronizadas. O exemplo de código a seguir demonstra como criar e executar animações filho:

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

Em ambos os exemplos de código, um objeto de [`Animation`](xref:Xamarin.Forms.Animation) pai é criado, ao qual outros objetos `Animation` são adicionados. Os dois primeiros argumentos para o método [`Add`](xref:Xamarin.Forms.Animation.Add(System.Double,System.Double,Xamarin.Forms.Animation)) especificam quando iniciar e concluir a animação filho. Os valores de argumento devem ser entre 0 e 1 e representar o período relativo dentro da animação pai que a animação filho especificado estará ativa. Portanto, neste exemplo, o `scaleUpAnimation` estará ativo para a primeira metade da animação, o `scaleDownAnimation` estará ativo na segunda metade da animação e o `rotateAnimation` estará ativo por toda a duração.

O efeito geral é que a animação ocorre mais de 4 segundos (4000 milissegundos). O `scaleUpAnimation` anima a propriedade [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) de 1 a 2, mais de 2 segundos. Em seguida, a `scaleDownAnimation` anima a propriedade `Scale` de 2 para 1, mais de 2 segundos. Enquanto as duas animações de escala estão ocorrendo, o `rotateAnimation` anima a propriedade [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) de 0 a 360, mais de 4 segundos. Observe que as animações de escala também usam funções de easing. A função de atenuação de [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) faz com que o [`Image`](xref:Xamarin.Forms.Image) seja reduzido inicialmente antes de aumentar, e a função de atenuação [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) faz com que o `Image` se torne menor do que seu tamanho real até o fim da animação completa.

Há várias diferenças entre um objeto de [`Animation`](xref:Xamarin.Forms.Animation) que usa animações filhas e um que não:

- Ao usar animações filhas, o retorno de chamada *concluído* em uma animação filho indica quando o filho foi concluído e o retorno de chamada *concluído* passado para o método `Commit` indica quando toda a animação foi concluída.
- Ao usar animações filhas, retornar `true` do retorno de chamada de *repetição* no método `Commit` não fará com que a animação se repita, mas a animação continuará a ser executada sem novos valores.
- Ao incluir uma função de atenuação no método `Commit` e a função de atenuação retornar um valor maior que 1, a animação será encerrada. Se a função de easing retorna um valor menor que 0, o valor será fixado como 0. Para usar uma função de atenuação que retorna um valor menor que 0 ou maior que 1, ela deve ser especificada em uma das animações filhas, em vez de no método `Commit`.

A classe [`Animation`](xref:Xamarin.Forms.Animation) também inclui [`WithConcurrent`](xref:Xamarin.Forms.Animation.WithConcurrent(Xamarin.Forms.Animation,System.Double,System.Double)) métodos que podem ser usados para adicionar animações filhas a um objeto pai `Animation`. No entanto, seus valores de argumento *begin* e *Finish* não são restritos a 0 a 1, mas somente essa parte da animação filho que corresponde a um intervalo de 0 a 1 estará ativa. Por exemplo, se uma chamada de método `WithConcurrent` define uma animação filho que tem como alvo uma propriedade [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) de 1 a 6, mas com valores de *início* e *término* de-2 e 3, o valor *inicial* de-2 corresponde a um valor `Scale` de 1 e o valor de *término* de 3 corresponde a um valor `Scale` de 6. Como os valores fora do intervalo de 0 e 1 não executam nenhuma parte em uma animação, a propriedade `Scale` só será animada de 3 para 6.

## <a name="cancel-an-animation"></a>Cancelar uma animação

Um aplicativo pode cancelar uma animação com uma chamada para o método de extensão [`AbortAnimation`](xref:Xamarin.Forms.AnimationExtensions.AbortAnimation(Xamarin.Forms.IAnimatable,System.String)) , conforme demonstrado no exemplo de código a seguir:

```csharp
this.AbortAnimation ("SimpleAnimation");
```

Observe que as animações são identificadas exclusivamente por uma combinação do proprietário de animação e o nome da animação. Portanto, o proprietário e o nome especificado ao executar a animação deve ser especificado para cancelar a animação. Portanto, o exemplo de código cancelará imediatamente a animação chamada `SimpleAnimation` pertencente à página.

## <a name="create-a-custom-animation"></a>Criar uma animação personalizada

Os exemplos mostrados aqui demonstraram animações que poderiam ser obtidas igualmente com os métodos na classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) . No entanto, a vantagem da classe [`Animation`](xref:Xamarin.Forms.Animation) é que ela tem acesso ao método de retorno de chamada, que é executado quando o valor animado é alterado. Isso permite que o retorno de chamada implementar qualquer animação desejada. Por exemplo, o exemplo de código a seguir anima a propriedade [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) de uma página definindo-a como [`Color`](xref:Xamarin.Forms.Color) valores criados pelo método [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) , com valores de matiz que variam de 0 a 1:

```csharp
new Animation (callback: v => BackgroundColor = Color.FromHsla (v, 1, 0.5),
  start: 0,
  end: 1).Commit (this, "Animation", 16, 4000, Easing.Linear, (v, c) => BackgroundColor = Color.Default);
```

A animação resultante fornece a aparência de aprimorando o plano de fundo da página por meio das cores do arco-íris.

Para obter mais exemplos de como criar animações complexas, incluindo uma animação de curva de Bézier, consulte o [capítulo 22](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf) da [criação de aplicativos móveis com Xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md).

## <a name="create-a-custom-animation-extension-method"></a>Criar um método de extensão de animação personalizado

Os métodos de extensão na classe [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) animam uma propriedade de seu valor atual para um valor especificado. Isso dificulta a criação, por exemplo, um `ColorTo` método de animação que pode ser usado para animar uma cor de um valor para outro, porque:

- A única propriedade [`Color`](xref:Xamarin.Forms.Color) definida pela classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) é [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor), que nem sempre é a propriedade `Color` desejada para animar.
- Geralmente, o valor atual de uma propriedade [`Color`](xref:Xamarin.Forms.Color) é [`Color.Default`](xref:Xamarin.Forms.Color.Default), que não é uma cor real e que não pode ser usada em cálculos de interpolação.

A solução para esse problema é não ter o método de `ColorTo` de destino de uma determinada propriedade [`Color`](xref:Xamarin.Forms.Color) . Em vez disso, ele pode ser escrito com um método de retorno de chamada que passa o valor de `Color` interpolado de volta para o chamador. Além disso, o método usará os argumentos Start e End `Color`.

O método `ColorTo` pode ser implementado como um método de extensão que usa o método [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) na classe [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) para fornecer sua funcionalidade. Isso ocorre porque o método `Animate` pode ser usado para propriedades de destino que não são do tipo `double`, conforme demonstrado no exemplo de código a seguir:

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

O método [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) requer um argumento *Transform* , que é um método de retorno de chamada. A entrada para esse retorno de chamada é sempre um `double` variando de 0 a 1. Portanto, o método `ColorTo` define seu próprio `Func` de transformação que aceita uma `double` variando de 0 a 1, e que retorna um valor de [`Color`](xref:Xamarin.Forms.Color) correspondente a esse valor. O valor `Color` é calculado com a interpolação dos valores [`R`](xref:Xamarin.Forms.Color.R), [`G`](xref:Xamarin.Forms.Color.G), [`B`](xref:Xamarin.Forms.Color.B)e [`A`](xref:Xamarin.Forms.Color.A) dos dois argumentos de `Color` fornecidos. O valor de `Color` é passado para o método de retorno de chamada para o aplicativo para uma determinada propriedade.

Essa abordagem permite que o método `ColorTo` anime qualquer propriedade [`Color`](xref:Xamarin.Forms.Color) , conforme demonstrado no exemplo de código a seguir:

```csharp
await Task.WhenAll(
  label.ColorTo(Color.Red, Color.Blue, c => label.TextColor = c, 5000),
  label.ColorTo(Color.Blue, Color.Red, c => label.BackgroundColor = c, 5000));
await this.ColorTo(Color.FromRgb(0, 0, 0), Color.FromRgb(255, 255, 255), c => BackgroundColor = c, 5000);
await boxView.ColorTo(Color.Blue, Color.Red, c => boxView.Color = c, 4000);
```

Neste exemplo de código, o método `ColorTo` anima as propriedades [`TextColor`](xref:Xamarin.Forms.Label.TextColor) e [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) de uma [`Label`](xref:Xamarin.Forms.Label), a propriedade `BackgroundColor` de uma página e a propriedade [`Color`](xref:Xamarin.Forms.BoxView.Color) de um [`BoxView`](xref:Xamarin.Forms.BoxView).

## <a name="related-links"></a>Links relacionados

- [Animações personalizadas (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)
- [API de animação](xref:Xamarin.Forms.Animation)
- [API AnimationExtensions](xref:Xamarin.Forms.AnimationExtensions)
