---
title: Animações personalizadas emXamarin.Forms
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4fb9c94c39823e4ce6d60be6b9dbef1294321a63
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137222"
---
# <a name="custom-animations-in-xamarinforms"></a>Animações personalizadas emXamarin.Forms

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)

_A classe Animation é o bloco de construção de todas as Xamarin.Forms animações, com os métodos de extensão na classe ViewExtensions criando um ou mais objetos de animação. Este artigo demonstra como usar a classe Animation para criar e cancelar animações, sincronizar várias animações e criar animações personalizadas que animam propriedades que não são animadas pelos métodos de animação existentes._

Um número de parâmetros deve ser especificado ao criar um `Animation` objeto, incluindo os valores de início e término da propriedade sendo animada e um retorno de chamada que altera o valor da propriedade. Um `Animation` objeto também pode manter uma coleção de animações filho que podem ser executadas e sincronizadas. Para obter mais informações, consulte [animações filhas](#child).

A execução de uma animação criada com a [`Animation`](xref:Xamarin.Forms.Animation) classe, que pode ou não incluir animações filhas, é obtida chamando [ `Commit` ] (xref: Xamarin.Forms . Animation. Commit ( Xamarin.Forms . IAnimatable, System. String, System. UInt32, System. UInt32, Xamarin.Forms . Método de atenuação, System. Action {System. Double, System. booliano}, System. Func {System. Boolean})). Esse método especifica a duração da animação e entre outros itens, um retorno de chamada que controla se a animação deve ser repetida.

Além disso, a [`Animation`](xref:Xamarin.Forms.Animation) classe tem uma `IsEnabled` propriedade que pode ser examinada para determinar se as animações foram desabilitadas pelo sistema operacional, como quando o modo de economia de energia está ativado.

## <a name="create-an-animation"></a>Criar uma animação

Ao criar um [`Animation`](xref:Xamarin.Forms.Animation) objeto, normalmente, um mínimo de três parâmetros é necessário, conforme demonstrado no exemplo de código a seguir:

```csharp
var animation = new Animation (v => image.Scale = v, 1, 2);
```

Esse código define uma animação da [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propriedade de uma [`Image`](xref:Xamarin.Forms.Image) instância a partir de um valor de 1 até um valor de 2. O valor animado, que é derivado por Xamarin.Forms , é passado para o retorno de chamada especificado como o primeiro argumento, em que é usado para alterar o valor da `Scale` propriedade.

A animação é iniciada com uma chamada para o [ `Commit` ] (xref: Xamarin.Forms . Animation. Commit ( Xamarin.Forms . IAnimatable, System. String, System. UInt32, System. UInt32, Xamarin.Forms . Método de atenuação, System. Action {System. Double, System. booliano}, System. Func {System. Boolean}), conforme demonstrado no exemplo de código a seguir:

```csharp
animation.Commit (this, "SimpleAnimation", 16, 2000, Easing.Linear, (v, c) => image.Scale = 1, () => true);
```

Observe que o [ `Commit` ] (xref: Xamarin.Forms . Animation. Commit ( Xamarin.Forms . IAnimatable, System. String, System. UInt32, System. UInt32, Xamarin.Forms . A atenuação, a função de sistema {System. Double, System. booliano}, System. Func {System. Boolean})) não retorna um `Task` objeto. Em vez disso, são fornecidas notificações por meio de métodos de retorno de chamada.

Os seguintes argumentos são especificados no `Commit` método:

- O primeiro argumento (*proprietário*) identifica o proprietário da animação. Esse pode ser o elemento visual no qual a animação é aplicada ou outro elemento visual, como a página.
- O segundo argumento (*Name*) identifica a animação com um nome. O nome é combinado com o proprietário para identificar exclusivamente a animação. Essa identificação exclusiva pode ser usada para determinar se a animação está em execução ([ `AnimationIsRunning` ] (xref: Xamarin.Forms . AnimationExtensions. AnimationIsRunning ( Xamarin.Forms . IAnimatable, System. String)) ou para cancelá-lo ([ `AbortAnimation` ] (xref: Xamarin.Forms . AnimationExtensions. AbortAnimation ( Xamarin.Forms . IAnimatable, System. String)).
- O terceiro argumento (*Rate*) indica o número de milissegundos entre cada chamada para o método de retorno de chamada definido no [`Animation`](xref:Xamarin.Forms.Animation) Construtor
- O quarto argumento (*comprimento*) indica a duração da animação, em milissegundos.
- O quinto argumento (*facilitando*) define a função de atenuação a ser usada na animação. Como alternativa, a função de atenuação pode ser especificada como um argumento para o [`Animation`](xref:Xamarin.Forms.Animation) Construtor. Para obter mais informações sobre as funções de atenuação, consulte [facilitando funções](~/xamarin-forms/user-interface/animation/easing.md).
- O sexto argumento (*concluído*) é um retorno de chamada que será executado quando a animação for concluída. Esse retorno de chamada usa dois argumentos, com o primeiro argumento que indica um valor final, e o segundo argumento `bool` é definido como `true` se a animação foi cancelada. Como alternativa, o retorno de chamada *concluído* pode ser especificado como um argumento para o [`Animation`](xref:Xamarin.Forms.Animation) Construtor. No entanto, com uma única animação, se as chamadas de retorno *concluídas* forem especificadas tanto no `Animation` Construtor quanto no `Commit` método, somente o retorno de chamada especificado no `Commit` método será executado.
- O sétimo argumento (*REPEAT*) é um retorno de chamada que permite que a animação seja repetida. Ele é chamado no final da animação e `true` o retorno indica que a animação deve ser repetida.

O efeito geral é criar uma animação que aumente a [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propriedade de um [`Image`](xref:Xamarin.Forms.Image) de 1 para 2, mais de 2 segundos (2000 milissegundos), usando a [`Linear`](xref:Xamarin.Forms.Easing.Linear) função de atenuação. Toda vez que a animação é concluída, sua `Scale` propriedade é redefinida como 1 e a animação se repete.

> [!NOTE]
> Animações simultâneas, que são executadas de forma independente umas das outras, podem ser construídas criando-se um `Animation` objeto para cada animação e, em seguida, chamando o `Commit` método em cada animação.

<a name="child" />

### <a name="child-animations"></a>Animações filhas

A [`Animation`](xref:Xamarin.Forms.Animation) classe também dá suporte a animações filhas, que envolve a criação de um `Animation` objeto ao qual outros `Animation` objetos são adicionados. Isso permite que uma série de animações seja executada e sincronizada. O exemplo de código a seguir demonstra como criar e executar animações filhas:

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

Como alternativa, o exemplo de código pode ser escrito de forma mais concisa, como demonstrado no exemplo de código a seguir:

```csharp
new Animation {
    { 0, 0.5, new Animation (v => image.Scale = v, 1, 2) },
    { 0, 1, new Animation (v => image.Rotation = v, 0, 360) },
    { 0.5, 1, new Animation (v => image.Scale = v, 2, 1) }
    }.Commit (this, "ChildAnimations", 16, 4000, null, (v, c) => SetIsEnabledButtonState (true, false));
```

Em ambos os exemplos de código, um [`Animation`](xref:Xamarin.Forms.Animation) objeto pai é criado, ao qual outros `Animation` objetos são adicionados. Os dois primeiros argumentos para [ `Add` ] (xref: Xamarin.Forms . Animation. Add (System. Double, System. Double, Xamarin.Forms . Animation)) do método especifique quando começar e concluir a animação filho. Os valores de argumento devem estar entre 0 e 1 e representar o período relativo dentro da animação pai que a animação filho especificada estará ativa. Portanto, neste exemplo, o `scaleUpAnimation` estará ativo para a primeira metade da animação, o `scaleDownAnimation` estará ativo para a segunda metade da animação e `rotateAnimation` estará ativo durante toda a duração.

O efeito geral é que a animação ocorre em mais de 4 segundos (4000 milissegundos). O `scaleUpAnimation` anima a [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propriedade de 1 a 2, mais de 2 segundos. `scaleDownAnimation`Em seguida, o anima a `Scale` propriedade de 2 para 1, mais de 2 segundos. Enquanto as duas animações de escala estão ocorrendo, o `rotateAnimation` anima a [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propriedade de 0 a 360, mais de 4 segundos. Observe que as animações de dimensionamento também usam funções de atenuação. A [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) função de atenuação faz com que o [`Image`](xref:Xamarin.Forms.Image) seja reduzido inicialmente antes de aumentar, e a [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) função de atenuação faz com que o `Image` fique menor do que seu tamanho real até o fim da animação completa.

Há várias diferenças entre um [`Animation`](xref:Xamarin.Forms.Animation) objeto que usa animações filhas e um que não:

- Ao usar animações filhas, o retorno de chamada *concluído* em uma animação filho indica quando o filho foi concluído e o retorno de chamada *concluído* para o `Commit` método indica quando toda a animação foi concluída.
- Ao usar animações filhas, retornar `true` do retorno de chamada de *repetição* no `Commit` método não fará com que a animação se repita, mas a animação continuará a ser executada sem novos valores.
- Ao incluir uma função de atenuação no `Commit` método, e a função de atenuação retornar um valor maior que 1, a animação será encerrada. Se a função de atenuação retornar um valor menor que 0, o valor será clamped como 0. Para usar uma função de atenuação que retorna um valor menor que 0 ou maior que 1, ela deve ser especificada em uma das animações filhas, e não no `Commit` método.

A [`Animation`](xref:Xamarin.Forms.Animation) classe também inclui [ `WithConcurrent` ] (xref: Xamarin.Forms . Animation. WithConcurrent ( Xamarin.Forms . Os métodos de animação, System. Double, System. Double) que podem ser usados para adicionar animações filhas a um `Animation` objeto pai. No entanto, seus valores de argumento *begin* e *Finish* não são restritos a 0 a 1, mas somente essa parte da animação filho que corresponde a um intervalo de 0 a 1 estará ativa. Por exemplo, se uma `WithConcurrent` chamada de método define uma animação filho que tem [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) como alvo uma propriedade de 1 a 6, mas com valores de *início* e *término* de-2 e 3, o valor *inicial* de-2 corresponde a um `Scale` valor de 1 e o valor de *término* de 3 corresponde a um `Scale` valor de 6. Como os valores fora do intervalo de 0 e 1 não executam nenhuma parte em uma animação, a `Scale` propriedade só será animada de 3 para 6.

## <a name="cancel-an-animation"></a>Cancelar uma animação

Um aplicativo pode cancelar uma animação com uma chamada para o [ `AbortAnimation` ] (xref: Xamarin.Forms . AnimationExtensions. AbortAnimation ( Xamarin.Forms . Método de extensão IAnimatable, System. String), conforme demonstrado no exemplo de código a seguir:

```csharp
this.AbortAnimation ("SimpleAnimation");
```

Observe que as animações são identificadas exclusivamente por uma combinação do proprietário da animação e pelo nome da animação. Portanto, o proprietário e o nome especificados ao executar a animação devem ser especificados para cancelar a animação. Portanto, o exemplo de código cancelará imediatamente a animação denominada de `SimpleAnimation` propriedade da página.

## <a name="create-a-custom-animation"></a>Criar uma animação personalizada

Os exemplos mostrados aqui demonstraram animações que poderiam ser obtidas igualmente com os métodos na [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe. No entanto, a vantagem da [`Animation`](xref:Xamarin.Forms.Animation) classe é que ela tem acesso ao método de retorno de chamada, que é executado quando o valor animado é alterado. Isso permite que o retorno de chamada implemente qualquer animação desejada. Por exemplo, o exemplo de código a seguir anima a [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) propriedade de uma página definindo-a como [`Color`](xref:Xamarin.Forms.Color) valores criados pelo [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) método, com valores de matiz que variam de 0 a 1:

```csharp
new Animation (callback: v => BackgroundColor = Color.FromHsla (v, 1, 0.5),
  start: 0,
  end: 1).Commit (this, "Animation", 16, 4000, Easing.Linear, (v, c) => BackgroundColor = Color.Default);
```

A animação resultante fornece a aparência de avançar o plano de fundo da página por meio das cores do arco-íris.

Para obter mais exemplos de como criar animações complexas, incluindo uma animação de curva de Bézier, consulte o [capítulo 22](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf) da [criação de Xamarin.Forms aplicativos móveis com ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)o.

## <a name="create-a-custom-animation-extension-method"></a>Criar um método de extensão de animação personalizado

Os métodos de extensão na [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe animam uma propriedade de seu valor atual para um valor especificado. Isso dificulta a criação, por exemplo, um método de `ColorTo` animação que pode ser usado para animar uma cor de um valor para outro, porque:

- A única [`Color`](xref:Xamarin.Forms.Color) propriedade definida pela [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe é [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) , que nem sempre é a `Color` propriedade desejada para animar.
- Geralmente, o valor atual de uma [`Color`](xref:Xamarin.Forms.Color) propriedade é [`Color.Default`](xref:Xamarin.Forms.Color.Default) , que não é uma cor real, e que não pode ser usado em cálculos de interpolação.

A solução para esse problema é não ter o `ColorTo` método de destino de uma determinada [`Color`](xref:Xamarin.Forms.Color) propriedade. Em vez disso, ele pode ser escrito com um método de retorno de chamada que passa o valor interpolado de `Color` volta para o chamador. Além disso, o método usará os argumentos Start e End `Color` .

O `ColorTo` método pode ser implementado como um método de extensão que usa o [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) método na [`AnimationExtensions`](xref:Xamarin.Forms.AnimationExtensions) classe para fornecer sua funcionalidade. Isso ocorre porque o `Animate` método pode ser usado para propriedades de destino que não são do tipo `double` , conforme demonstrado no exemplo de código a seguir:

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

O [`Animate`](xref:Xamarin.Forms.AnimationExtensions.Animate*) método requer um argumento *Transform* , que é um método de retorno de chamada. A entrada para esse retorno de chamada é sempre uma `double` variação de 0 a 1. Portanto, o `ColorTo` método define sua própria transformação `Func` que aceita uma `double` variação de 0 a 1, e que retorna um [`Color`](xref:Xamarin.Forms.Color) valor correspondente a esse valor. O `Color` valor é calculado com a interpolação [`R`](xref:Xamarin.Forms.Color.R) dos [`G`](xref:Xamarin.Forms.Color.G) valores,, [`B`](xref:Xamarin.Forms.Color.B) e [`A`](xref:Xamarin.Forms.Color.A) dos dois argumentos fornecidos `Color` . O `Color` valor é passado para o método de retorno de chamada para o aplicativo para uma determinada propriedade.

Essa abordagem permite que o `ColorTo` método anime qualquer [`Color`](xref:Xamarin.Forms.Color) propriedade, conforme demonstrado no exemplo de código a seguir:

```csharp
await Task.WhenAll(
  label.ColorTo(Color.Red, Color.Blue, c => label.TextColor = c, 5000),
  label.ColorTo(Color.Blue, Color.Red, c => label.BackgroundColor = c, 5000));
await this.ColorTo(Color.FromRgb(0, 0, 0), Color.FromRgb(255, 255, 255), c => BackgroundColor = c, 5000);
await boxView.ColorTo(Color.Blue, Color.Red, c => boxView.Color = c, 4000);
```

Neste exemplo de código, o `ColorTo` método anima as [`TextColor`](xref:Xamarin.Forms.Label.TextColor) Propriedades e [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) de a [`Label`](xref:Xamarin.Forms.Label) , a `BackgroundColor` propriedade de uma página e a [`Color`](xref:Xamarin.Forms.BoxView.Color) propriedade de um [`BoxView`](xref:Xamarin.Forms.BoxView) .

## <a name="related-links"></a>Links relacionados

- [Animações personalizadas (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-custom)
- [API de animação](xref:Xamarin.Forms.Animation)
- [API AnimationExtensions](xref:Xamarin.Forms.AnimationExtensions)
