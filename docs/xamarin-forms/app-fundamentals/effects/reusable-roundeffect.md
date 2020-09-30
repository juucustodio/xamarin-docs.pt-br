---
title: Xamarin.Forms RoundEffect reutilizável
description: RoundEffect é um efeito reutilizável que pode ser aplicado a qualquer controle derivado de Visualelement para renderizar o controle como um círculo.
ms.prod: xamarin
ms.assetid: B5DE7507-B565-4EE5-9897-27E5733FD173
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/25/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 47b459945aec4ef8f1c3fa9729efd82461aec0e0
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91561957"
---
# <a name="no-locxamarinforms-reusable-roundeffect"></a>Xamarin.Forms RoundEffect reutilizável

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)

O RoundEffect simplifica a renderização de qualquer controle derivado de Visualelement como um círculo. Esse efeito pode ser usado para criar imagens circulares, botões e outros controles:

[![Capturas de tela do RoundEffect no iOS e no Android](example-roundeffect-images/round-effect-cropped.png)](example-roundeffect-images/round-effect.png#lightbox)

## <a name="create-a-shared-routingeffect"></a>Criar um RoutingEffect compartilhado

Uma classe de efeito deve ser criada no projeto compartilhado para criar um efeito de plataforma cruzada. O aplicativo de exemplo cria uma `RoundEffect` classe vazia que deriva da `RoutingEffect` classe:

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

O projeto da plataforma Android define uma `RoundEffect` classe derivada de `PlatformEffect` . Essa classe é marcada com `assembly` atributos que permitem Xamarin.Forms resolver a classe de efeito:

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

A plataforma Android usa o conceito de um `OutlineProvider` para definir as bordas de um controle. O projeto de exemplo inclui uma `CornerRadiusProvider` classe que deriva da `ViewOutlineProvider` classe:

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

Essa classe usa as `Width` `Height` Propriedades e da Xamarin.Forms `Element` instância do para calcular um raio que é metade da dimensão mais curta.

Depois que um provedor de estrutura de tópicos é definido `RoundEffect` , a classe pode consumi-lo para implementar o efeito:

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

O `OnAttached` método é chamado quando o efeito é anexado a um elemento. O `OutlineProvider` objeto existente é salvo para que possa ser restaurado quando o efeito for desanexado. Uma nova instância do `CornerRadiusOutlineProvider` é usada como `OutlineProvider` e `ClipToOutline` é definida como true para cortar elementos com estouro nas bordas da estrutura de tópicos.

O `OnDetatched` método é chamado quando o efeito é removido de um elemento e restaura o `OutlineProvider` valor original.

> [!NOTE]
> Dependendo do tipo de elemento, a `Control` propriedade pode ou não ser nula. Se a `Control` propriedade não for nula, os cantos arredondados poderão ser aplicados diretamente ao controle. No entanto, se for nulo, os cantos arredondados deverão ser aplicados ao `Container` objeto. O `effectTarget` campo permite que o efeito seja aplicado ao objeto apropriado.

## <a name="implement-the-ios-effect"></a>Implementar o efeito de iOS

O projeto da plataforma iOS define uma `RoundEffect` classe derivada de `PlatformEffect` . Essa classe é marcada com `assembly` atributos que permitem Xamarin.Forms resolver a classe de efeito:

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

No iOS, os controles têm uma `Layer` propriedade, que tem uma `CornerRadius` propriedade. A `RoundEffect` implementação de classe no Ios calcula o raio do canto apropriado e atualiza a propriedade da camada `CornerRadius` :

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

O `CalculateRadius` método calcula um raio com base na dimensão mínima do Xamarin.Forms `Element` . O `OnAttached` método é chamado quando o efeito é anexado a um controle e atualiza a propriedade da camada `CornerRadius` . Ele define a `ClipToBounds` propriedade para `true` que os elementos estouros sejam recortados nas bordas do controle. O `OnDetatched` método é chamado quando o efeito é removido de um controle e reverte essas alterações, restaurando o raio do canto original.

> [!NOTE]
> Dependendo do tipo de elemento, a `Control` propriedade pode ou não ser nula. Se a `Control` propriedade não for nula, os cantos arredondados poderão ser aplicados diretamente ao controle. No entanto, se for nulo, os cantos arredondados deverão ser aplicados ao `Container` objeto. O `effectTarget` campo permite que o efeito seja aplicado ao objeto apropriado.

## <a name="consume-the-effect"></a>Consumir o efeito

Quando o efeito é implementado entre plataformas, ele pode ser consumido pelos Xamarin.Forms controles. Um aplicativo comum do `RoundEffect` está tornando um `Image` objeto circular. O XAML a seguir mostra o efeito que está sendo aplicado a uma `Image` instância:

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

A `RoundEffect` classe pode ser aplicada a qualquer controle derivado de `VisualElement` .

> [!NOTE]
> Para o efeito para calcular o raio correto, o controle ao qual ele é aplicado deve ter um dimensionamento explícito. Portanto, as `HeightRequest` `WidthRequest` Propriedades e devem ser definidas. Se o controle afetado aparecer em um `StackLayout` , sua `HorizontalOptions` propriedade não deverá usar um dos valores de **expansão** como `LayoutOptions.CenterAndExpand` ou não terá dimensões precisas.

## <a name="related-links"></a>Links relacionados

- [Aplicativo de exemplo RoundEffect](/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)
- [Introdução aos efeitos](~/xamarin-forms/app-fundamentals/effects/introduction.md)
- [Criando um efeito](~/xamarin-forms/app-fundamentals/effects/creating.md)