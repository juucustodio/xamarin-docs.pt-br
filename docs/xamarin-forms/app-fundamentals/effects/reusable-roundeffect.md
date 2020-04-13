---
title: Xamarin.Forms Reutilizável RoundEffect
description: RoundEffect é um efeito reutilizável que pode ser aplicado a qualquer controle derivado do VisualElement para tornar o controle como um círculo.
ms.prod: xamarin
ms.assetid: B5DE7507-B565-4EE5-9897-27E5733FD173
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/25/2019
ms.openlocfilehash: 851ed7a2ad1c416b4d03d583b9d0aeb7f7774eea
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73055956"
---
# <a name="xamarinforms-reusable-roundeffect"></a>Xamarin.Forms Reutilizável RoundEffect

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)

O RoundEffect simplifica a renderização de qualquer controle que deriva do VisualElement como um círculo. Esse efeito pode ser usado para criar imagens circulares, botões e outros controles:

[![Screenshots roundEffect no iOS e Android](example-roundeffect-images/round-effect-cropped.png)](example-roundeffect-images/round-effect.png#lightbox)

## <a name="create-a-shared-routingeffect"></a>Crie um RoutingEffect compartilhado

Uma classe de efeito deve ser criada no projeto compartilhado para criar um efeito multiplataforma. O aplicativo de `RoundEffect` exemplo cria uma `RoutingEffect` classe vazia que deriva da classe:

```csharp
public class RoundEffect : RoutingEffect
{
    public RoundEffect() : base($"Xamarin.{nameof(RoundEffect)}")
    {
    }
}
```

Esta classe permite que o projeto compartilhado resolva as referências ao efeito em código ou XAML, mas não fornece nenhuma funcionalidade. O efeito deve ter implementações para cada plataforma.

## <a name="implement-the-android-effect"></a>Implementar o efeito Android

O projeto da plataforma `RoundEffect` Android define `PlatformEffect`uma classe que deriva de . Esta classe é `assembly` marcada com atributos que permitem que xamarin.Forms resolva a classe de efeito:

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

A plataforma Android usa `OutlineProvider` o conceito de um para definir as bordas de um controle. O projeto amostral `CornerRadiusProvider` inclui uma classe `ViewOutlineProvider` que deriva da classe:

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

Esta classe `Width` usa `Height` as propriedades da ocorrência `Element` Xamarin.Forms para calcular um raio que é metade da dimensão mais curta.

Uma vez definido um `RoundEffect` provedor de contorno, a classe pode consumi-lo para implementar o efeito:

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

O `OnAttached` método é chamado quando o efeito é anexado a um elemento. O objeto `OutlineProvider` existente é salvo para que possa ser restaurado quando o efeito for separado. Uma nova instância `CornerRadiusOutlineProvider` do é `OutlineProvider` `ClipToOutline` usado como o e é definido como verdadeiro para cortar elementos transbordantes para as bordas do contorno.

O `OnDetatched` método é chamado quando o efeito é removido `OutlineProvider` de um elemento e restaura o valor original.

> [!NOTE]
> Dependendo do tipo de `Control` elemento, a propriedade pode ou não ser nula. Se `Control` a propriedade não for nula, os cantos arredondados podem ser aplicados diretamente ao controle. No entanto, se for nulo, os `Container` cantos arredondados devem ser aplicados ao objeto. O `effectTarget` campo permite que o efeito seja aplicado ao objeto apropriado.

## <a name="implement-the-ios-effect"></a>Implementar o efeito iOS

O projeto da plataforma `RoundEffect` iOS define `PlatformEffect`uma classe que deriva de . Esta classe é `assembly` marcada com atributos que permitem que xamarin.Forms resolva a classe de efeito:

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

No iOS, os `Layer` controles têm uma `CornerRadius` propriedade, que tem uma propriedade. A `RoundEffect` implementação de classe no iOS calcula o `CornerRadius` raio de canto apropriado e atualiza a propriedade da camada:

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

O `CalculateRadius` método calcula um raio com base na dimensão mínima das formas `Element`de Xamarin. O `OnAttached` método é chamado quando o efeito é anexado a `CornerRadius` um controle e atualiza a propriedade da camada. Ele define `ClipToBounds` a `true` propriedade para que elementos transbordantes sejam cortados para as bordas do controle. O `OnDetatched` método é chamado quando o efeito é removido de um controle e inverte essas alterações, restaurando o raio de canto original.

> [!NOTE]
> Dependendo do tipo de `Control` elemento, a propriedade pode ou não ser nula. Se `Control` a propriedade não for nula, os cantos arredondados podem ser aplicados diretamente ao controle. No entanto, se for nulo, os `Container` cantos arredondados devem ser aplicados ao objeto. O `effectTarget` campo permite que o efeito seja aplicado ao objeto apropriado.

## <a name="consume-the-effect"></a>Consumir o efeito

Uma vez que o efeito é implementado em todas as plataformas, ele pode ser consumido pelos controles Xamarin.Forms. Uma aplicação comum `RoundEffect` do `Image` é fazer uma circular de objeto. O XAML a seguir mostra o `Image` efeito sendo aplicado a uma instância:

```xaml
<Image Source=outdoors"
       HeightRequest="100"
       WidthRequest="100">
    <Image.Effects>
        <local:RoundEffect />
    </Image.Effects>
</Image>
```

O efeito também pode ser aplicado em código:

```csharp
var image = new Image
{
    Source = ImageSource.FromFile("outdoors"),
    HeightRequest = 100,
    WidthRequest = 100
};
image.Effects.Add(new RoundEffect());
```

A `RoundEffect` classe pode ser aplicada a qualquer `VisualElement`controle que deriva de .

> [!NOTE]
> Para o efeito para calcular o raio correto, o controle a que é aplicado deve ter dimensionamento explícito. Portanto, as `HeightRequest` `WidthRequest` propriedades e propriedades devem ser definidas. Se o controle afetado `StackLayout`aparecer `HorizontalOptions` em um , sua propriedade `LayoutOptions.CenterAndExpand` não deve usar um dos valores **expandir,** como ou não terá dimensões precisas.

## <a name="related-links"></a>Links relacionados

- [Aplicativo de amostra RoundEffect](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-roundeffect/)
- [Introdução aos efeitos](~/xamarin-forms/app-fundamentals/effects/introduction.md)
- [Criando um efeito](~/xamarin-forms/app-fundamentals/effects/creating.md)
