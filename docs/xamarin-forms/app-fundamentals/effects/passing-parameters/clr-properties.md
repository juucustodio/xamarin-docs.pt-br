---
title: Passando parâmetros de efeito como propriedades de tempo de execução de linguagem comum
description: Propriedades do Common Language Runtime (CLR) podem ser usadas para definir os parâmetros do efeito que não respondem a alterações de propriedade de tempo de execução. Este artigo demonstra como usar propriedades CLR para passar parâmetros para um efeito.
ms.prod: xamarin
ms.assetid: 4B50466C-5DBD-45DD-B1E6-BE9524C92F27
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
ms.openlocfilehash: 1bb357b256a7cc6d52d1d92613f38cbf48400c4c
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995762"
---
# <a name="passing-effect-parameters-as-common-language-runtime-properties"></a>Passando parâmetros de efeito como propriedades de tempo de execução de linguagem comum

_Propriedades do Common Language Runtime (CLR) podem ser usadas para definir os parâmetros do efeito que não respondem a alterações de propriedade de tempo de execução. Este artigo demonstra como usar propriedades CLR para passar parâmetros para um efeito._

O processo para criar parâmetros em vigor que não respondem a alterações de propriedade de tempo de execução é da seguinte maneira:

1. Criar uma `public` que pode efetuar subclasses de classes do [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) classe. O `RoutingEffect` classe representa um efeito de independente de plataforma que encapsula um efeito interno que é geralmente específico da plataforma.
1. Crie um construtor que chama o construtor de classe base, passando uma concatenação do nome do grupo de resolução e a ID exclusiva que foi especificada em cada classe de efeito específico da plataforma.
1. Adicione propriedades à classe para cada parâmetro a ser passado para o efeito.

Parâmetros, em seguida, podem ser passados para o efeito, especificando valores para cada propriedade ao instanciar o efeito.

O aplicativo de exemplo demonstra um `ShadowEffect` que adiciona uma sombra para o texto exibido por um [ `Label` ](xref:Xamarin.Forms.Label) controle. O diagrama a seguir ilustra as responsabilidades de cada projeto no aplicativo de exemplo, juntamente com as relações entre eles:

![](clr-properties-images/shadow-effect.png "Responsabilidades do projeto de efeito de sombra")

Um [ `Label` ](xref:Xamarin.Forms.Label) control no `HomePage` é personalizado com o `LabelShadowEffect` em cada projeto específico da plataforma. Os parâmetros são passados para cada `LabelShadowEffect` por meio das propriedades no `ShadowEffect` classe. Cada `LabelShadowEffect` classe deriva de `PlatformEffect` classe para cada plataforma. Isso resulta em uma sombra que está sendo adicionada ao texto exibido pelo `Label` controle, conforme mostrado nas capturas de tela seguir:

![](clr-properties-images/screenshots.png "Efeito de sombra em cada plataforma")

## <a name="creating-effect-parameters"></a>Criando parâmetros de efeito

Um `public` que pode efetuar subclasses de classes do [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) classe deve ser criada para representar os parâmetros em vigor, conforme demonstrado no exemplo de código a seguir:

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

O `ShadowEffect` contém quatro propriedades que representam os parâmetros a serem passados para cada plataforma específica `LabelShadowEffect`. O construtor da classe chama o construtor de classe base, passando um parâmetro que consiste em uma concatenação do nome do grupo de resolução e a ID exclusiva que foi especificada em cada classe de efeito específico da plataforma. Portanto, uma nova instância dos `MyCompany.LabelShadowEffect` será adicionado a um controle [ `Effects` ](xref:Xamarin.Forms.Element.Effects) coleção quando um `ShadowEffect` é instanciado.

## <a name="consuming-the-effect"></a>Consumindo o efeito

Mostra o exemplo de código XAML abaixo uma [ `Label` ](xref:Xamarin.Forms.Label) controle ao qual o `ShadowEffect` está anexado:

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

O equivalente [ `Label` ](xref:Xamarin.Forms.Label) em c# é mostrado no exemplo de código a seguir:

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

Nos dois exemplos de código, uma instância das `ShadowEffect` classe é instanciada com valores que estão sendo especificados para cada propriedade, antes de serem adicionados ao controle de [ `Effects` ](xref:Xamarin.Forms.Element.Effects) coleção. Observe que o `ShadowEffect.Color` propriedade usa valores de cor específico da plataforma. Para obter mais informações, consulte [classe de dispositivo](~/xamarin-forms/platform/device.md).

## <a name="creating-the-effect-on-each-platform"></a>Criando o efeito de cada plataforma

As seções a seguir discutem a implementação específica da plataforma do `LabelShadowEffect` classe.

### <a name="ios-project"></a>Projeto do iOS

O seguinte exemplo de código mostra o `LabelShadowEffect` implementação para o projeto do iOS:

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

O `OnAttached` método recupera o `ShadowEffect` instância e conjuntos de `Control.Layer` propriedades para valores de propriedade especificado para criar a sombra. Essa funcionalidade é encapsulada em um `try` / `catch` bloquear no caso do controle que o efeito é anexado ao não tem o `Control.Layer` propriedades. Nenhuma implementação é fornecida pelo `OnDetached` método porque nenhuma limpeza é necessária.

### <a name="android-project"></a>Projeto do Android

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

O `OnAttached` método recupera o `ShadowEffect` instância e chama o [ `TextView.SetShadowLayer` ](https://developer.xamarin.com/api/member/Android.Widget.TextView.SetShadowLayer/p/System.Single/System.Single/System.Single/Android.Graphics.Color/) método para criar uma sombra usando os valores de propriedade especificada. Essa funcionalidade é encapsulada em um `try` / `catch` bloquear no caso do controle que o efeito é anexado ao não tem o `Control.Layer` propriedades. Nenhuma implementação é fornecida pelo `OnDetached` método porque nenhuma limpeza é necessária.

### <a name="universal-windows-platform-project"></a>Projeto da plataforma universal do Windows

O seguinte exemplo de código mostra o `LabelShadowEffect` implementação para o projeto da plataforma Universal do Windows (UWP):

```csharp
[assembly: ResolutionGroupName ("Xamarin")]
[assembly: ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.UWP
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

A plataforma Universal do Windows não fornece um efeito de sombra e então o `LabelShadowEffect` implementação nas duas plataformas simula um adicionando um deslocamento segundo [ `Label` ](xref:Xamarin.Forms.Label) atrás primário `Label`. O `OnAttached` método recupera o `ShadowEffect` da instância, cria o novo `Label`e define algumas propriedades de layout no `Label`. Depois, ele cria a sombra definindo a [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor), [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX), e [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) propriedades para controlar a cor e o local do `Label`. O `shadowLabel` é inserido, em seguida, deslocado por trás do primário `Label`. Essa funcionalidade é encapsulada em um `try` / `catch` bloquear no caso do controle que o efeito é anexado ao não tem o `Control.Layer` propriedades. Nenhuma implementação é fornecida pelo `OnDetached` método porque nenhuma limpeza é necessária.

## <a name="summary"></a>Resumo

Este artigo demonstrou usando propriedades de CLR para passar parâmetros para um efeito. Propriedades CLR podem ser usadas para definir os parâmetros do efeito que não respondem a alterações de propriedade de tempo de execução.


## <a name="related-links"></a>Links relacionados

- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Em vigor](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [Efeito de sombra (amostra)](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffect/)
