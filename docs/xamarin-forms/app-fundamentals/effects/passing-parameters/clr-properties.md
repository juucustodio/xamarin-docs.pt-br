---
title: Passando parâmetros de efeito como propriedades de Common Language Runtime
description: Propriedades de CLR (Common Language Runtime) podem ser usadas para definir parâmetros de efeito que não respondem a alterações de propriedade de tempo de execução. Este artigo demonstra como usar propriedades de CLR para passar parâmetros para um efeito.
ms.prod: xamarin
ms.assetid: 4B50466C-5DBD-45DD-B1E6-BE9524C92F27
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
ms.openlocfilehash: 04d96dad455cbcf8360f12ee97a0540e7e746d62
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771469"
---
# <a name="passing-effect-parameters-as-common-language-runtime-properties"></a>Passando parâmetros de efeito como propriedades de Common Language Runtime

[![Baixar](~/media/shared/download.png) amostra Baixar a amostra](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)

_As propriedades de TEMPO de execução de linguagem comum (CLR) podem ser usadas para definir parâmetros de efeito que não respondem às alterações de propriedade em tempo de execução. Este artigo demonstra o uso de propriedades CLR para passar parâmetros para um efeito._

O processo para criar parâmetros de efeito que não respondem a alterações de propriedade de runtime é o seguinte:

1. Crie `public` uma classe que [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) subclasses a classe. A classe `RoutingEffect` representa um efeito independente de plataforma que encapsula um efeito interno, que é geralmente é específico da plataforma.
1. Crie um construtor que chama o construtor da classe base, passando uma concatenação do nome do grupo de resolução e a ID exclusiva que foi especificada em cada classe de efeito específica da plataforma.
1. Adicione propriedades à classe para cada parâmetro a ser passado para o efeito.

Em seguida, os parâmetros podem ser passados para o efeito especificando valores para cada propriedade ao instanciar o efeito.

O aplicativo de `ShadowEffect` exemplo demonstra uma sombra ao [`Label`](xref:Xamarin.Forms.Label) texto exibido por um controle. O diagrama a seguir ilustra as responsabilidades de cada projeto no aplicativo de exemplo, bem como as relações entre elas:

![](clr-properties-images/shadow-effect.png "Shadow Effect Project Responsibilities")

Um [`Label`](xref:Xamarin.Forms.Label) controle `HomePage` no é personalizado `LabelShadowEffect` pelo projeto específico da plataforma. Os parâmetros são passados para cada `LabelShadowEffect` por meio das propriedades na classe `ShadowEffect`. Cada classe `LabelShadowEffect` é derivada da classe `PlatformEffect` de cada plataforma. Isso faz com que uma sombra seja adicionada ao texto exibido pelo controle `Label`, conforme mostrado nas capturas de tela seguir:

![](clr-properties-images/screenshots.png "Shadow Effect on each Platform")

## <a name="creating-effect-parameters"></a>Criando parâmetros de efeito

Uma `public` classe que [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) subclasses da classe deve ser criada para representar parâmetros de efeito, conforme demonstrado no exemplo de código a seguir:

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

O `ShadowEffect` contém quatro propriedades que representam os parâmetros a serem passados para cada `LabelShadowEffect` específico da plataforma. O construtor de classe chama o construtor da classe base, passando um parâmetro composto por uma concatenação do nome do grupo de resolução e pela ID exclusiva que foi especificada em cada classe de efeito específica da plataforma. Portanto, uma nova instância `MyCompany.LabelShadowEffect` do será adicionada à [`Effects`](xref:Xamarin.Forms.Element.Effects) coleção `ShadowEffect` de um controle quando um é instanciado.

## <a name="consuming-the-effect"></a>Consumindo o efeito

O exemplo de código XAML a seguir mostra um [`Label`](xref:Xamarin.Forms.Label) controle ao qual o `ShadowEffect` é anexado:

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

O [`Label`](xref:Xamarin.Forms.Label) equivalente em C# é mostrado no seguinte exemplo de código:

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

Em ambos os exemplos de `ShadowEffect` código, uma instância da classe é instanciada com valores [`Effects`](xref:Xamarin.Forms.Element.Effects) sendo especificados para cada propriedade, antes de ser adicionado à coleção do controle. Observe que a propriedade `ShadowEffect.Color` usa valores de cor específicos da plataforma. Para obter mais informações, confira [Classe do dispositivo](~/xamarin-forms/platform/device.md).

## <a name="creating-the-effect-on-each-platform"></a>Criando o efeito em cada plataforma

As seções a seguir abordam a implementação da classe `LabelShadowEffect` específica da plataforma.

### <a name="ios-project"></a>Projeto do iOS

O exemplo de código a seguir mostra a implementação de `LabelShadowEffect` para o projeto do iOS:

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

O método `OnAttached` recupera a instância de `ShadowEffect` e define propriedades de `Control.Layer` como os valores de propriedade especificados para criar a sombra. Essa funcionalidade é encapsulada em um bloco `try`/`catch` caso o controle a que o efeito está anexado não tenha as propriedades de `Control.Layer`. Nenhuma implementação é fornecida pelo método `OnDetached` porque nenhuma limpeza é necessária.

### <a name="android-project"></a>Projeto do Android

O exemplo de código a seguir mostra a implementação de `LabelShadowEffect` para o projeto do Android:

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

O `OnAttached` método recupera `ShadowEffect` a instância [`TextView.SetShadowLayer`](xref:Android.Widget.TextView.SetShadowLayer*) e chama o método para criar uma sombra usando os valores de propriedade especificados. Essa funcionalidade é encapsulada em um bloco `try`/`catch` caso o controle a que o efeito está anexado não tenha as propriedades de `Control.Layer`. Nenhuma implementação é fornecida pelo método `OnDetached` porque nenhuma limpeza é necessária.

### <a name="universal-windows-platform-project"></a>Projeto da Plataforma Universal do Windows

O exemplo de código a seguir mostra a implementação de `LabelShadowEffect` para o projeto da UWP (Plataforma Universal do Windows):

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

A Plataforma Universal Windows não fornece um efeito `LabelShadowEffect` de sombra, e assim a implementação [`Label`](xref:Xamarin.Forms.Label) em `Label`ambas as plataformas simula uma adicionando um segundo deslocamento atrás do principal . O método `OnAttached` recupera a instância de `ShadowEffect`, cria o novo `Label` e define algumas propriedades de layout no `Label`. Em seguida, cria a [`TextColor`](xref:Xamarin.Forms.Label.TextColor) [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX)sombra [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) definindo as propriedades e `Label`propriedades para controlar a cor e a localização do . Em seguida, o `shadowLabel` é inserido deslocado atrás do `Label` principal. Essa funcionalidade é encapsulada em um bloco `try`/`catch` caso o controle a que o efeito está anexado não tenha as propriedades de `Control.Layer`. Nenhuma implementação é fornecida pelo método `OnDetached` porque nenhuma limpeza é necessária.

## <a name="summary"></a>Resumo

Este artigo demonstrou como usar propriedades de CLR para passar parâmetros para um efeito. Propriedades de CLR podem ser usadas para definir parâmetros de efeito que não respondem a alterações de propriedade de runtime.

## <a name="related-links"></a>Links relacionados

- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Efeito](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [Efeito de sombra (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-shadoweffect)
