---
title: Passando parâmetros de efeito como propriedades anexadas
description: Propriedades anexadas podem ser usadas para definir os parâmetros do efeito que respondem a alterações de propriedade de tempo de execução. Este artigo demonstra como usar propriedades para passar parâmetros para um efeito e alterando um parâmetro em tempo de execução anexadas.
ms.prod: xamarin
ms.assetid: DFCDCB9F-17DD-4117-BD53-B4FB206BB387
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
ms.openlocfilehash: 9483e424a74a88ce3f0eb49624bb5315551f2062
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996445"
---
# <a name="passing-effect-parameters-as-attached-properties"></a>Passando parâmetros de efeito como propriedades anexadas

_Propriedades anexadas podem ser usadas para definir os parâmetros do efeito que respondem a alterações de propriedade de tempo de execução. Este artigo demonstra como usar propriedades para passar parâmetros para um efeito e alterando um parâmetro em tempo de execução anexadas._

O processo para criar parâmetros em vigor que respondem a alterações de propriedade de tempo de execução é da seguinte maneira:

1. Criar um `static` classe que contém uma propriedade anexada para cada parâmetro a ser passado para o efeito.
1. Adicione uma propriedade anexada adicional à classe que será usado para controlar a adição ou remoção do efeito para o controle que será anexado à classe. Certifique-se de que isso anexado propriedade registra um `propertyChanged` delegado que será executado quando o valor da propriedade é alterado.
1. Criar `static` getters e setters para cada propriedade de anexada.
1. Implementar a lógica no `propertyChanged` delegado para adicionar e remover o efeito.
1. Implementar uma classe aninhada dentro de `static` classe, chamada após o efeito, que herda o `RoutingEffect` classe. Para o construtor, chame o construtor de classe base, passando uma concatenação do nome do grupo de resolução e a ID exclusiva que foi especificada em cada classe de efeito específico da plataforma.

Parâmetros, em seguida, podem ser passados para o efeito, adicionando as propriedades anexadas e valores de propriedade, para o controle apropriado. Além disso, os parâmetros podem ser alterados em tempo de execução, especificando um novo valor da propriedade anexada.

> [!NOTE]
> Uma propriedade anexada é um tipo especial de propriedade associável, definido em uma classe, mas anexado a outros objetos e reconhecível no XAML como atributos que contêm uma classe e um nome de propriedade separados por um período. Para obter mais informações, consulte [propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md).

O aplicativo de exemplo demonstra um `ShadowEffect` que adiciona uma sombra para o texto exibido por um [ `Label` ](xref:Xamarin.Forms.Label) controle. Além disso, a cor da sombra pode ser alterada em tempo de execução. O diagrama a seguir ilustra as responsabilidades de cada projeto no aplicativo de exemplo, juntamente com as relações entre eles:

![](attached-properties-images/shadow-effect.png "Responsabilidades do projeto de efeito de sombra")

Um [ `Label` ](xref:Xamarin.Forms.Label) control no `HomePage` é personalizado com o `LabelShadowEffect` em cada projeto específico da plataforma. Os parâmetros são passados para cada `LabelShadowEffect` por meio de propriedades anexadas no `ShadowEffect` classe. Cada `LabelShadowEffect` classe deriva de `PlatformEffect` classe para cada plataforma. Isso resulta em uma sombra que está sendo adicionada ao texto exibido pelo `Label` controle, conforme mostrado nas capturas de tela seguir:

![](attached-properties-images/screenshots.png "Efeito de sombra em cada plataforma")

## <a name="creating-effect-parameters"></a>Criando parâmetros de efeito

Um `static` classe deve ser criada para representar os parâmetros em vigor, conforme demonstrado no exemplo de código a seguir:

```csharp
public static class ShadowEffect
{
  public static readonly BindableProperty HasShadowProperty =
    BindableProperty.CreateAttached ("HasShadow", typeof(bool), typeof(ShadowEffect), false, propertyChanged: OnHasShadowChanged);
  public static readonly BindableProperty ColorProperty =
    BindableProperty.CreateAttached ("Color", typeof(Color), typeof(ShadowEffect), Color.Default);
  public static readonly BindableProperty RadiusProperty =
    BindableProperty.CreateAttached ("Radius", typeof(double), typeof(ShadowEffect), 1.0);
  public static readonly BindableProperty DistanceXProperty =
    BindableProperty.CreateAttached ("DistanceX", typeof(double), typeof(ShadowEffect), 0.0);
  public static readonly BindableProperty DistanceYProperty =
    BindableProperty.CreateAttached ("DistanceY", typeof(double), typeof(ShadowEffect), 0.0);

  public static bool GetHasShadow (BindableObject view)
  {
    return (bool)view.GetValue (HasShadowProperty);
  }

  public static void SetHasShadow (BindableObject view, bool value)
  {
    view.SetValue (HasShadowProperty, value);
  }
  ...

  static void OnHasShadowChanged (BindableObject bindable, object oldValue, object newValue)
  {
    var view = bindable as View;
    if (view == null) {
      return;
    }

    bool hasShadow = (bool)newValue;
    if (hasShadow) {
      view.Effects.Add (new LabelShadowEffect ());
    } else {
      var toRemove = view.Effects.FirstOrDefault (e => e is LabelShadowEffect);
      if (toRemove != null) {
        view.Effects.Remove (toRemove);
      }
    }
  }

  class LabelShadowEffect : RoutingEffect
  {
    public LabelShadowEffect () : base ("MyCompany.LabelShadowEffect")
    {
    }
  }
}
```

O `ShadowEffect` contém cinco propriedades anexadas, com `static` getters e setters para cada propriedade de anexada. Quatro dessas propriedades representam os parâmetros a serem passados para cada plataforma específica `LabelShadowEffect`. O `ShadowEffect` classe define também uma `HasShadow` anexados a propriedade que é usada para controlar a adição ou remoção do efeito para o controle que o `ShadowEffect` classe está anexado ao. Isso anexado propriedade registra o `OnHasShadowChanged` método que será executado quando o valor da propriedade é alterado. Esse método adiciona ou remove o efeito com base no valor da `HasShadow` propriedade anexada.

Aninhado `LabelShadowEffect` de classe que herda de [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) classe dá suporte ao efeito adição e remoção. O `RoutingEffect` classe representa um efeito de independente de plataforma que encapsula um efeito interno que é geralmente específico da plataforma. Isso simplifica o processo de remoção de efeito, porque não há nenhum acesso de tempo de compilação para as informações de tipo para um efeito específico da plataforma. O `LabelShadowEffect` construtor chama o construtor de classe base, passando um parâmetro que consiste em uma concatenação do nome do grupo de resolução e a ID exclusiva que foi especificada em cada classe de efeito específico da plataforma. Isso permite que a adição de efeito e remoção no `OnHasShadowChanged` método, da seguinte maneira:

- **Adição de efeito** – uma nova instância dos `LabelShadowEffect` é adicionado ao controle de [ `Effects` ](xref:Xamarin.Forms.Element.Effects) coleção. Isso substitui a usando o [ `Effect.Resolve` ](xref:Xamarin.Forms.Effect.Resolve(System.String)) método para adicionar o efeito.
- **Remoção de efeito** – a primeira instância das `LabelShadowEffect` no controle de [ `Effects` ](xref:Xamarin.Forms.Element.Effects) coleção é recuperada e removida.

## <a name="consuming-the-effect"></a>Consumindo o efeito

Cada específicos da plataforma `LabelShadowEffect` podem ser consumidos, adicionando as propriedades anexadas a uma [ `Label` ](xref:Xamarin.Forms.Label) controlar, conforme demonstrado no exemplo de código XAML a seguir:

```xaml
<Label Text="Label Shadow Effect" ...
       local:ShadowEffect.HasShadow="true" local:ShadowEffect.Radius="5"
       local:ShadowEffect.DistanceX="5" local:ShadowEffect.DistanceY="5">
  <local:ShadowEffect.Color>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="Black" />
        <On Platform="Android" Value="White" />
        <On Platform="UWP" Value="Red" />
    </OnPlatform>
  </local:ShadowEffect.Color>
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

ShadowEffect.SetHasShadow (label, true);
ShadowEffect.SetRadius (label, 5);
ShadowEffect.SetDistanceX (label, 5);
ShadowEffect.SetDistanceY (label, 5);
ShadowEffect.SetColor (label, color));
```

Definindo o `ShadowEffect.HasShadow` anexado à propriedade `true` executa o `ShadowEffect.OnHasShadowChanged` método que adiciona ou remove o `LabelShadowEffect` para o [ `Label` ](xref:Xamarin.Forms.Label) controle. Nos dois exemplos de código, o `ShadowEffect.Color` propriedade anexada fornece valores de cor específico da plataforma. Para obter mais informações, consulte [classe de dispositivo](~/xamarin-forms/platform/device.md).

Além disso, uma [ `Button` ](xref:Xamarin.Forms.Button) permite que a cor da sombra a ser alterado em tempo de execução. Quando o `Button` é clicado, a código a seguir altera a sombra de cor, definindo o `ShadowEffect.Color` propriedade anexada:

```csharp
ShadowEffect.SetColor (label, Color.Teal);
```

### <a name="consuming-the-effect-with-a-style"></a>Consumindo o efeito com um estilo

Os efeitos que podem ser consumidos pela adição de propriedades anexadas a um controle também podem ser consumidos por um estilo. Mostra o exemplo de código XAML abaixo uma *explícita* estilo para o efeito de sombra, que pode ser aplicado a [ `Label` ](xref:Xamarin.Forms.Label) controles:

```xaml
<Style x:Key="ShadowEffectStyle" TargetType="Label">
  <Style.Setters>
    <Setter Property="local:ShadowEffect.HasShadow" Value="True" />
    <Setter Property="local:ShadowEffect.Radius" Value="5" />
    <Setter Property="local:ShadowEffect.DistanceX" Value="5" />
    <Setter Property="local:ShadowEffect.DistanceY" Value="5" />
  </Style.Setters>
</Style>
```

O [ `Style` ](xref:Xamarin.Forms.Style) pode ser aplicado a um [ `Label` ](xref:Xamarin.Forms.Label) definindo seu [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) propriedade para o `Style` instância usando o `StaticResource`extensão de marcação, conforme demonstrado no exemplo de código a seguir:

```xaml
<Label Text="Label Shadow Effect" ... Style="{StaticResource ShadowEffectStyle}" />
```

Para obter mais informações sobre estilos, consulte [estilos](~/xamarin-forms/user-interface/styles/index.md).

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
                UpdateRadius ();
                UpdateColor ();
                UpdateOffset ();
                Control.Layer.ShadowOpacity = 1.0f;
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateRadius ()
        {
            Control.Layer.CornerRadius = (nfloat)ShadowEffect.GetRadius (Element);
        }

        void UpdateColor ()
        {
            Control.Layer.ShadowColor = ShadowEffect.GetColor (Element).ToCGColor ();
        }

        void UpdateOffset ()
        {
            Control.Layer.ShadowOffset = new CGSize (
                (double)ShadowEffect.GetDistanceX (Element),
                (double)ShadowEffect.GetDistanceY (Element));
        }
    }
```

O `OnAttached` método chama métodos que recuperam os valores de propriedade anexada usando o `ShadowEffect` getters e quais definir `Control.Layer` propriedades para valores de propriedade para criar a sombra. Essa funcionalidade é encapsulada em um `try` / `catch` bloquear no caso do controle que o efeito é anexado ao não tem o `Control.Layer` propriedades. Nenhuma implementação é fornecida pelo `OnDetached` método porque nenhuma limpeza é necessária.

#### <a name="responding-to-property-changes"></a>Responder às alterações de propriedade

Se qualquer uma da `ShadowEffect` anexado a alteração de valores de propriedade em tempo de execução, o efeito precisa responder exibindo as alterações. Uma versão de substituição a `OnElementPropertyChanged` método na classe específica da plataforma efeito, é o lugar para responder a alterações de propriedade associável, conforme demonstrado no exemplo de código a seguir:

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.RadiusProperty.PropertyName) {
      UpdateRadius ();
    } else if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
               args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
    }
  }
  ...
}
```

O `OnElementPropertyChanged` método atualiza o radius, a cor ou o deslocamento da sombra, desde que o apropriada `ShadowEffect` alterou o valor da propriedade anexada. Uma verificação para a propriedade que é alterada sempre deve ser feita conforme essa substituição pode ser chamada várias vezes.

### <a name="android-project"></a>Projeto do Android

O seguinte exemplo de código mostra o `LabelShadowEffect` implementação para o projeto Android:

```csharp
[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.Droid
{
    public class LabelShadowEffect : PlatformEffect
    {
        Android.Widget.TextView control;
        Android.Graphics.Color color;
        float radius, distanceX, distanceY;

        protected override void OnAttached ()
        {
            try {
                control = Control as Android.Widget.TextView;
                UpdateRadius ();
                UpdateColor ();
                UpdateOffset ();
                UpdateControl ();
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateControl ()
        {
            if (control != null) {
                control.SetShadowLayer (radius, distanceX, distanceY, color);
            }
        }

        void UpdateRadius ()
        {
            radius = (float)ShadowEffect.GetRadius (Element);
        }

        void UpdateColor ()
        {
            color = ShadowEffect.GetColor (Element).ToAndroid ();
        }

        void UpdateOffset ()
        {
            distanceX = (float)ShadowEffect.GetDistanceX (Element);
            distanceY = (float)ShadowEffect.GetDistanceY (Element);
        }
    }
```

O `OnAttached` método chama métodos que recuperam os valores de propriedade anexada usando o `ShadowEffect` getters e chama um método que chama o [ `TextView.SetShadowLayer` ](https://developer.xamarin.com/api/member/Android.Widget.TextView.SetShadowLayer/p/System.Single/System.Single/System.Single/Android.Graphics.Color/) método para criar uma sombra usando os valores de propriedade. Essa funcionalidade é encapsulada em um `try` / `catch` bloquear no caso do controle que o efeito é anexado ao não tem o `Control.Layer` propriedades. Nenhuma implementação é fornecida pelo `OnDetached` método porque nenhuma limpeza é necessária.

#### <a name="responding-to-property-changes"></a>Responder às alterações de propriedade

Se qualquer uma da `ShadowEffect` anexado a alteração de valores de propriedade em tempo de execução, o efeito precisa responder exibindo as alterações. Uma versão de substituição a `OnElementPropertyChanged` método na classe específica da plataforma efeito, é o lugar para responder a alterações de propriedade associável, conforme demonstrado no exemplo de código a seguir:

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.RadiusProperty.PropertyName) {
      UpdateRadius ();
      UpdateControl ();
    } else if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
      UpdateControl ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
               args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
      UpdateControl ();
    }
  }
  ...
}
```

O `OnElementPropertyChanged` método atualiza o radius, a cor ou o deslocamento da sombra, desde que o apropriada `ShadowEffect` alterou o valor da propriedade anexada. Uma verificação para a propriedade que é alterada sempre deve ser feita conforme essa substituição pode ser chamada várias vezes.

### <a name="universal-windows-platform-project"></a>Projeto da plataforma universal do Windows

O seguinte exemplo de código mostra o `LabelShadowEffect` implementação para o projeto da plataforma Universal do Windows (UWP):

```csharp
[assembly: ResolutionGroupName ("MyCompany")]
[assembly: ExportEffect (typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace EffectsDemo.UWP
{
    public class LabelShadowEffect : PlatformEffect
    {
        Label shadowLabel;
        bool shadowAdded = false;

        protected override void OnAttached ()
        {
            try {
                if (!shadowAdded) {
                    var textBlock = Control as Windows.UI.Xaml.Controls.TextBlock;

                    shadowLabel = new Label ();
                    shadowLabel.Text = textBlock.Text;
                    shadowLabel.FontAttributes = FontAttributes.Bold;
                    shadowLabel.HorizontalOptions = LayoutOptions.Center;
                    shadowLabel.VerticalOptions = LayoutOptions.CenterAndExpand;

                    UpdateColor ();
                    UpdateOffset ();

                    ((Grid)Element.Parent).Children.Insert (0, shadowLabel);
                    shadowAdded = true;
                }
            } catch (Exception ex) {
                Debug.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }
        ...

        void UpdateColor ()
        {
            shadowLabel.TextColor = ShadowEffect.GetColor (Element);
        }

        void UpdateOffset ()
        {
            shadowLabel.TranslationX = ShadowEffect.GetDistanceX (Element);
            shadowLabel.TranslationY = ShadowEffect.GetDistanceY (Element);
        }
    }
}
```

A plataforma Universal do Windows não fornece um efeito de sombra e então o `LabelShadowEffect` implementação nas duas plataformas simula um adicionando um deslocamento segundo [ `Label` ](xref:Xamarin.Forms.Label) atrás primário `Label`. O `OnAttached` método cria o novo `Label` e define algumas propriedades de layout no `Label`. Depois, ele chama os métodos que recuperam os valores de propriedade anexada usando o `ShadowEffect` getters e cria a sombra definindo a [ `TextColor` ](xref:Xamarin.Forms.Label.TextColor), [ `TranslationX` ](xref:Xamarin.Forms.VisualElement.TranslationX)e [ `TranslationY` ](xref:Xamarin.Forms.VisualElement.TranslationY) propriedades para controlar a cor e o local do `Label`. O `shadowLabel` é inserido, em seguida, deslocado por trás do primário `Label`. Essa funcionalidade é encapsulada em um `try` / `catch` bloquear no caso do controle que o efeito é anexado ao não tem o `Control.Layer` propriedades. Nenhuma implementação é fornecida pelo `OnDetached` método porque nenhuma limpeza é necessária.

#### <a name="responding-to-property-changes"></a>Responder às alterações de propriedade

Se qualquer uma da `ShadowEffect` anexado a alteração de valores de propriedade em tempo de execução, o efeito precisa responder exibindo as alterações. Uma versão de substituição a `OnElementPropertyChanged` método na classe específica da plataforma efeito, é o lugar para responder a alterações de propriedade associável, conforme demonstrado no exemplo de código a seguir:

```csharp
public class LabelShadowEffect : PlatformEffect
{
  ...
  protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
  {
    if (args.PropertyName == ShadowEffect.ColorProperty.PropertyName) {
      UpdateColor ();
    } else if (args.PropertyName == ShadowEffect.DistanceXProperty.PropertyName ||
                      args.PropertyName == ShadowEffect.DistanceYProperty.PropertyName) {
      UpdateOffset ();
    }
  }
  ...
}
```

O `OnElementPropertyChanged` método atualiza a cor ou o deslocamento da sombra, desde que o apropriada `ShadowEffect` alterou o valor da propriedade anexada. Uma verificação para a propriedade que é alterada sempre deve ser feita conforme essa substituição pode ser chamada várias vezes.

## <a name="summary"></a>Resumo

Este artigo demonstrou Use nas propriedades para passar parâmetros para um efeito e alterando um parâmetro em tempo de execução. Propriedades anexadas podem ser usadas para definir os parâmetros do efeito que respondem a alterações de propriedade de tempo de execução.


## <a name="related-links"></a>Links relacionados

- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Em vigor](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [Efeito de sombra (amostra)](https://developer.xamarin.com/samples/xamarin-forms/effects/shadoweffectruntimechange/)
