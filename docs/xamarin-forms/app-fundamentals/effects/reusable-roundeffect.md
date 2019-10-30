---
title: RoundEffect reutilizáveis do Xamarin. Forms
description: RoundEffect é um efeito reutilizável que pode ser aplicado a qualquer controle derivado de Visualelement para renderizar o controle como um círculo.
ms.prod: xamarin
ms.assetid: B5DE7507-B565-4EE5-9897-27E5733FD173
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/25/2019
ms.openlocfilehash: 851ed7a2ad1c416b4d03d583b9d0aeb7f7774eea
ms.sourcegitcommit: aa7e9d2c370ba9cbc830f10b94b4cd4221fc5467
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73055956"
---
# <a name="xamarinforms-reusable-roundeffect"></a>RoundEffect reutilizáveis do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)

O RoundEffect simplifica a renderização de qualquer controle derivado de Visualelement como um círculo. Esse efeito pode ser usado para criar imagens circulares, botões e outros controles:

[![capturas de tela do RoundEffect no iOS e no Android](example-roundeffect-images/round-effect-cropped.png)](example-roundeffect-images/round-effect.png#lightbox)

## <a name="create-a-shared-routingeffect"></a>Criar um RoutingEffect compartilhado

Uma classe de efeito deve ser criada no projeto compartilhado para criar um efeito de plataforma cruzada. O aplicativo de exemplo cria uma classe `RoundEffect` vazia que deriva da classe `RoutingEffect`:

```csharp
public class RoundEffect : RoutingEffect
{
    public RoundEffect() : base($"Xamarin.{nameof(RoundEffect)}")
    {
    }
}
```

Essa classe permite que o projeto compartilhado resolva as referências ao efeito no código ou XAML, mas não fornece nenhuma funcionalidade. O efeito deve ter implementações para cada plataforma.

## <a name="implement-the-android-effect"></a>Implementar o efeito do Android

O projeto da plataforma Android define uma classe `RoundEffect` que deriva de `PlatformEffect`. Essa classe é marcada com `assembly` atributos que permitem que o Xamarin. Forms resolva a classe Effect:

```csharp
[assembly: ResolutionGroupName("Xamarin")]
[assembly: ExportEffect(typeof(RoundEffectDemo.Droid.RoundEffect), nameof(RoundEffectDemo.Droid.RoundEffect))]
namespace RoundEffectDemo.Droid
{
    public class RoundEffect : PlatformEffect
    {
        // ...
    }
}
```

A plataforma Android usa o conceito de um `OutlineProvider` para definir as bordas de um controle. O projeto de exemplo inclui uma classe `CornerRadiusProvider` que deriva da classe `ViewOutlineProvider`:

```csharp
class CornerRadiusOutlineProvider : ViewOutlineProvider
{
    Element element;

    public CornerRadiusOutlineProvider(Element formsElement)
    {
        element = formsElement;
    }

    public override void GetOutline(Android.Views.View view, Outline outline)
    {
        float scale = view.Resources.DisplayMetrics.Density;
        double width = (double)element.GetValue(VisualElement.WidthProperty) * scale;
        double height = (double)element.GetValue(VisualElement.HeightProperty) * scale;
        float minDimension = (float)Math.Min(height, width);
        float radius = minDimension / 2f;
        Rect rect = new Rect(0, 0, (int)width, (int)height);
        outline.SetRoundRect(rect, radius);
    }
}
```

Essa classe usa as propriedades `Width` e `Height` da instância `Element` Xamarin. Forms para calcular um raio que é metade da dimensão mais curta.

Depois que um provedor de estrutura de tópicos é definido, a classe `RoundEffect` pode consumi-lo para implementar o efeito:

```csharp
public class RoundEffect : PlatformEffect
{
    ViewOutlineProvider originalProvider;
    Android.Views.View effectTarget;

    protected override void OnAttached()
    {
        try
        {
            effectTarget = Control ?? Container;
            originalProvider = effectTarget.OutlineProvider;
            effectTarget.OutlineProvider = new CornerRadiusOutlineProvider(Element);
            effectTarget.ClipToOutline = true;
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Failed to set corner radius: {ex.Message}");
        }
    }

    protected override void OnDetached()
    {
        if(effectTarget != null)
        {
            effectTarget.OutlineProvider = originalProvider;
            effectTarget.ClipToOutline = false;
        }
    }
}
```

O método `OnAttached` é chamado quando o efeito é anexado a um elemento. O objeto `OutlineProvider` existente é salvo para que possa ser restaurado quando o efeito for desanexado. Uma nova instância do `CornerRadiusOutlineProvider` é usada como a `OutlineProvider` e `ClipToOutline` é definida como true para recortar elementos estouros para as bordas da estrutura de tópicos.

O método `OnDetatched` é chamado quando o efeito é removido de um elemento e restaura o valor de `OutlineProvider` original.

> [!NOTE]
> Dependendo do tipo de elemento, a propriedade `Control` pode ou não ser nula. Se a propriedade `Control` não for nula, os cantos arredondados poderão ser aplicados diretamente ao controle. No entanto, se for nulo, os cantos arredondados deverão ser aplicados ao objeto `Container`. O campo `effectTarget` permite que o efeito seja aplicado ao objeto apropriado.

## <a name="implement-the-ios-effect"></a>Implementar o efeito de iOS

O projeto da plataforma iOS define uma classe `RoundEffect` que deriva de `PlatformEffect`. Essa classe é marcada com `assembly` atributos que permitem que o Xamarin. Forms resolva a classe Effect:

```csharp
[assembly: ResolutionGroupName("Xamarin")]
[assembly: ExportEffect(typeof(RoundEffectDemo.iOS.RoundEffect), nameof(RoundEffectDemo.iOS.RoundEffect))]
namespace RoundEffectDemo.iOS
{
    public class RoundEffect : PlatformEffect
    {
        // ...
    }
```

No iOS, os controles têm uma propriedade `Layer`, que tem uma propriedade `CornerRadius`. A implementação da classe `RoundEffect` no iOS calcula o raio do canto apropriado e atualiza a propriedade `CornerRadius` da camada:

```csharp
public class RoundEffect : PlatformEffect
{
    nfloat originalRadius;
    UIKit.UIView effectTarget;

    protected override void OnAttached()
    {
        try
        {
            effectTarget = Control ?? Container;
            originalRadius = effectTarget.Layer.CornerRadius;
            effectTarget.ClipsToBounds = true;
            effectTarget.Layer.CornerRadius = CalculateRadius();
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Failed to set corner radius: {ex.Message}");
        }
    }

    protected override void OnDetached()
    {
        if (effectTarget != null)
        {
            effectTarget.ClipsToBounds = false;
            if (effectTarget.Layer != null)
            {
                effectTarget.Layer.CornerRadius = originalRadius;
            }
        }
    }

    float CalculateRadius()
    {
        double width = (double)Element.GetValue(VisualElement.WidthRequestProperty);
        double height = (double)Element.GetValue(VisualElement.HeightRequestProperty);
        float minDimension = (float)Math.Min(height, width);
        float radius = minDimension / 2f;

        return radius;
    }
}
```

O método `CalculateRadius` calcula um raio com base na dimensão mínima do `Element`Xamarin. Forms. O método `OnAttached` é chamado quando o efeito é anexado a um controle e atualiza a propriedade `CornerRadius` da camada. Ele define a propriedade `ClipToBounds` como `true`, de forma que os elementos estouros sejam recortados nas bordas do controle. O método `OnDetatched` é chamado quando o efeito é removido de um controle e reverte essas alterações, restaurando o raio do canto original.

> [!NOTE]
> Dependendo do tipo de elemento, a propriedade `Control` pode ou não ser nula. Se a propriedade `Control` não for nula, os cantos arredondados poderão ser aplicados diretamente ao controle. No entanto, se for nulo, os cantos arredondados deverão ser aplicados ao objeto `Container`. O campo `effectTarget` permite que o efeito seja aplicado ao objeto apropriado.

## <a name="consume-the-effect"></a>Consumir o efeito

Quando o efeito é implementado entre plataformas, ele pode ser consumido por controles Xamarin. Forms. Um aplicativo comum da `RoundEffect` está tornando um objeto `Image` circular. O XAML a seguir mostra o efeito que está sendo aplicado a uma instância de `Image`:

```xaml
<Image Source=outdoors"
       HeightRequest="100"
       WidthRequest="100">
    <Image.Effects>
        <local:RoundEffect />
    </Image.Effects>
</Image>
```

O efeito também pode ser aplicado no código:

```csharp
var image = new Image
{
    Source = ImageSource.FromFile("outdoors"),
    HeightRequest = 100,
    WidthRequest = 100
};
image.Effects.Add(new RoundEffect());
```

A classe `RoundEffect` pode ser aplicada a qualquer controle derivado de `VisualElement`.

> [!NOTE]
> Para o efeito para calcular o raio correto, o controle ao qual ele é aplicado deve ter um dimensionamento explícito. Portanto, as propriedades `HeightRequest` e `WidthRequest` devem ser definidas. Se o controle afetado aparecer em um `StackLayout`, sua propriedade `HorizontalOptions` não deverá usar um dos valores de **expansão** como `LayoutOptions.CenterAndExpand` ou não terá dimensões precisas.

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo RoundEffect](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)
- [Introdução aos Efeitos](~/xamarin-forms/app-fundamentals/effects/introduction.md)
- [Criar um efeito](~/xamarin-forms/app-fundamentals/effects/creating.md)
