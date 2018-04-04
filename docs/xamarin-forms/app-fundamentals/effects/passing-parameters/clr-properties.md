---
title: Passando parâmetros de efeito como propriedades de tempo de execução de linguagem comum
description: Propriedades do Common Language Runtime (CLR) podem ser usadas para definir os parâmetros do efeito que não respondem a alterações de propriedade de tempo de execução. Este artigo demonstra como usar propriedades CLR para passar parâmetros para um efeito.
ms.prod: xamarin
ms.assetid: 4B50466C-5DBD-45DD-B1E6-BE9524C92F27
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
ms.openlocfilehash: 78d14b9764ab0c7cafb9f09fa1c8acea3f45afde
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2018
---
# <a name="passing-effect-parameters-as-common-language-runtime-properties"></a>Passando parâmetros de efeito como propriedades de tempo de execução de linguagem comum

_Propriedades do Common Language Runtime (CLR) podem ser usadas para definir os parâmetros do efeito que não respondem a alterações de propriedade de tempo de execução. Este artigo demonstra como usar propriedades CLR para passar parâmetros para um efeito._

O processo para criar parâmetros do efeito que não respondem a alterações de propriedade de tempo de execução é da seguinte maneira:

1. Criar um `public` que as subclasses de classes de [ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/) classe. O `RoutingEffect` classe representa um efeito independente de plataforma que encapsula um efeito interno que é geralmente específica da plataforma.
1. Crie um construtor que chama o construtor de classe base, passando uma concatenação do nome do grupo de resolução e a ID exclusiva que foi especificada em cada classe de efeito específico da plataforma.
1. Adicione propriedades à classe para cada parâmetro a ser passado para o efeito.

Parâmetros podem ser passados para o efeito, especificando valores para cada propriedade ao instanciar o efeito.

O aplicativo de exemplo demonstra um `ShadowEffect` que adiciona uma sombra para o texto exibido por um [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) controle. O diagrama a seguir ilustra as responsabilidades de cada projeto de aplicativo de exemplo, juntamente com as relações entre eles:

![](clr-properties-images/shadow-effect.png "Responsabilidades de projeto do efeito de sombra")

Um [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) control no `HomePage` é personalizado pelo `LabelShadowEffect` em cada projeto específico da plataforma. Os parâmetros são passados para cada `LabelShadowEffect` por meio das propriedades no `ShadowEffect` classe. Cada `LabelShadowEffect` classe deriva de `PlatformEffect` classe para cada plataforma. Isso resulta em uma sombra que está sendo adicionada para o texto exibido, o `Label` controlar, como mostrado nas capturas de tela seguir:

![](clr-properties-images/screenshots.png "Efeito de sombra em cada plataforma")

## <a name="creating-effect-parameters"></a>Criar parâmetros de efeito

Um `public` que as subclasses de classes de [ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/) classe deve ser criada para representar parâmetros em vigor, conforme demonstrado no exemplo de código a seguir:

```csharp
public class ShadowEffect : RoutingEffect
{
  public float Radius { get; set; }

  public Color Color { get; set; }

  public float DistanceX { get; set; }

  public float DistanceY { get; set; }

  public ShadowEffect () : base ("MyCompany.LabelShadowEffect")
  {         
  }
}
```

O `ShadowEffect` contém quatro propriedades que representam os parâmetros a serem passados para cada plataforma específica `LabelShadowEffect`. O construtor da classe chama o construtor de classe base, passando um parâmetro que consiste em uma concatenação do nome do grupo de resolução e a ID exclusiva que foi especificada em cada classe de efeito específico da plataforma. Portanto, uma nova instância do `MyCompany.LabelShadowEffect` será adicionado a um controle [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) coleção quando um `ShadowEffect` é instanciado.

## <a name="consuming-the-effect"></a>O efeito de consumo

Mostra o exemplo seguinte do código XAML um [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) controle para o qual o `ShadowEffect` está anexado:

```xaml
<Label Text="Label Shadow Effect" ...>
  <Label.Effects>
    <local:ShadowEffect Radius="5" DistanceX="5" DistanceY="5">
      <local:ShadowEffect.Color>
        <OnPlatform x:TypeArguments="Color">
            <On Platform="iOS" Value="Black" />
            <On Platform="Android" Value="White" />
            <On Platform="UWP" Value="Red" />
        </OnPlatform>
      </local:ShadowEffect.Color>
    </local:ShadowEffect>
  </Label.Effects>
</Label>
```

O equivalente [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) c# é mostrado no exemplo de código a seguir:

```csharp
var label = new Label {
  Text = "Label Shadow Effect",
  ...
};

Color color = Color.Default;
switch (Device.RuntimePlatform)
{
    case Device.iOS:
        color = Color.Black;
        break;
    case Device.Android:
        color = Color.White;
        break;
    case Device.UWP:
        color = Color.Red;
        break;
}

label.Effects.Add (new ShadowEffect {
  Radius = 5,
  Color = color,
  DistanceX = 5,
  DistanceY = 5
});
```

Em ambos os exemplos de código, uma instância do `ShadowEffect` classe é instanciada com valores que estão sendo especificados para cada propriedade, antes de serem adicionados ao controle de [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) coleção. Observe que o `ShadowEffect.Color` propriedade usa valores de cor específico da plataforma. Para obter mais informações, consulte [classe de dispositivo](~/xamarin-forms/platform/device.md).

## <a name="creating-the-effect-on-each-platform"></a>Criando o efeito de cada plataforma

As seções a seguir abordam a implementação específica de plataforma do `LabelShadowEffect` classe.

### <a name="ios-project"></a>iOS Project

O seguinte exemplo de código mostra o `LabelShadowEffect` implementação para o projeto iOS:

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.iOS
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached ()
        {
            try {
                var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                if (effect != null) {
                    Control.Layer.CornerRadius = effect.Radius;
                    Control.Layer.ShadowColor = effect.Color.ToCGColor ();
                    Control.Layer.ShadowOffset = new CGSize (effect.DistanceX, effect.DistanceY);
                    Control.Layer.ShadowOpacity = 1.0f;
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

O `OnAttached` método recupera o `ShadowEffect` instância e conjuntos de `Control.Layer` propriedades para valores de propriedade especificado para criar a sombra. Essa funcionalidade é encapsulada em um `try` / `catch` bloquear caso o controle que o efeito é anexado ao não tem o `Control.Layer` propriedades. Nenhuma implementação é fornecida pelo `OnDetached` método porque nenhuma limpeza é necessária.

### <a name="android-project"></a>Android Project

O seguinte exemplo de código mostra o `LabelShadowEffect` implementação para o projeto Android:

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.Droid
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached ()
        {
            try {
                var control = Control as Android.Widget.TextView;
                var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                if (effect != null) {
                    float radius = effect.Radius;
                    float distanceX = effect.DistanceX;
                    float distanceY = effect.DistanceY;
                    Android.Graphics.Color color = effect.Color.ToAndroid ();
                    control.SetShadowLayer (radius, distanceX, distanceY, color);
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

O `OnAttached` método recupera o `ShadowEffect` instância e chama o [ `TextView.SetShadowLayer` ](https://developer.xamarin.com/api/member/Android.Widget.TextView.SetShadowLayer/p/System.Single/System.Single/System.Single/Android.Graphics.Color/) método para criar uma sombra usando os valores de propriedade especificada. Essa funcionalidade é encapsulada em um `try` / `catch` bloquear caso o controle que o efeito é anexado ao não tem o `Control.Layer` propriedades. Nenhuma implementação é fornecida pelo `OnDetached` método porque nenhuma limpeza é necessária.

### <a name="windows-phone--universal-windows-platform-projects"></a>Windows Phone e projetos de plataforma Universal do Windows

O seguinte exemplo de código mostra o `LabelShadowEffect` implementação para os projetos do Windows Phone e Windows UWP (plataforma Universal):

```csharp
[assembly: ResolutionGroupName ("Xamarin")]
[assembly: ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.WinPhone81
{
    public class LabelShadowEffect : PlatformEffect
    {
        bool shadowAdded = false;

        protected override void OnAttached ()
        {
            try {
                if (!shadowAdded) {
                    var effect = (ShadowEffect)Element.Effects.FirstOrDefault (e => e is ShadowEffect);
                    if (effect != null) {
                        var textBlock = Control as Windows.UI.Xaml.Controls.TextBlock;
                        var shadowLabel = new Label ();
                        shadowLabel.Text = textBlock.Text;
                        shadowLabel.FontAttributes = FontAttributes.Bold;
                        shadowLabel.HorizontalOptions = LayoutOptions.Center;
                        shadowLabel.VerticalOptions = LayoutOptions.CenterAndExpand;
                        shadowLabel.TextColor = effect.Color;
                        shadowLabel.TranslationX = effect.DistanceX;
                        shadowLabel.TranslationY = effect.DistanceY;

                        ((Grid)Element.Parent).Children.Insert (0, shadowLabel);
                        shadowAdded = true;
                    }
                }
            } catch (Exception ex) {
                Debug.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
    }
}
```

O tempo de execução do Windows e a plataforma Universal do Windows não fornecem um efeito de sombra e portanto o `LabelShadowEffect` implementação em ambas as plataformas simula um adicionando um deslocamento segundo [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) atrás primário `Label`. O `OnAttached` método recupera o `ShadowEffect` da instância, cria o novo `Label`e define algumas propriedades de layout na `Label`. Ele cria a sombra definindo o [ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/), [ `TranslationX` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/), e [ `TranslationY` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/) propriedades para controlar a cor e o local do `Label`. O `shadowLabel` é inserido deslocamento atrás primário `Label`. Essa funcionalidade é encapsulada em um `try` / `catch` bloquear caso o controle que o efeito é anexado ao não tem o `Control.Layer` propriedades. Nenhuma implementação é fornecida pelo `OnDetached` método porque nenhuma limpeza é necessária.

## <a name="summary"></a>Resumo

Este artigo demonstrou usando as propriedades do CLR para passar parâmetros para um efeito. Propriedades do CLR podem ser usadas para definir os parâmetros do efeito que não respondem a alterações de propriedade de tempo de execução.


## <a name="related-links"></a>Links relacionados

- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effect](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/)
- [PlatformEffect](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformEffect%3CTContainer,TControl%3E/)
- [RoutingEffect](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/)
- [Efeito de sombra (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffect/)
