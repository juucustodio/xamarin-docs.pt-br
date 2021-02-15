---
title: Passando parâmetros de efeito como propriedades anexadas
description: Propriedades anexadas podem ser usadas para definir parâmetros de efeito que respondem a alterações de propriedade de runtime. Este artigo demonstra o uso de propriedades anexadas para passar parâmetros para um efeito e a alteração de um parâmetro em runtime.
ms.prod: xamarin
ms.assetid: DFCDCB9F-17DD-4117-BD53-B4FB206BB387
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/05/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 103839c7016b7e9776e80f3f3459f8dc92b3bcbb
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371645"
---
# <a name="passing-effect-parameters-as-attached-properties"></a>Passando parâmetros de efeito como propriedades anexadas

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/effects-shadoweffectruntimechange)

_As propriedades anexadas podem ser usadas para definir parâmetros de efeito que respondem às alterações de propriedade de tempo de execução. Este artigo demonstra como usar propriedades anexadas para passar parâmetros para um efeito e alterar um parâmetro em tempo de execução._

O processo para criar parâmetros de efeito que respondem a alterações de propriedade de runtime é o seguinte:

1. Crie uma classe `static` que contém uma propriedade anexada para cada parâmetro a ser passado para o efeito.
1. Adicione outra propriedade anexada à classe que será usada para controlar a adição ou remoção do efeito para o controle a que a classe será anexada. Certifique-se de que a propriedade anexada registre um delegado `propertyChanged` que será executado quando o valor da propriedade for alterado.
1. Crie getters e setters `static` para cada propriedade anexada.
1. Implemente a lógica no delegado `propertyChanged` para adicionar e remover o efeito.
1. Implemente uma classe aninhada dentro da classe `static`, com o mesmo nome do efeito, que cria uma subclasse da classe `RoutingEffect`. Para o construtor, chame o construtor da classe base passando uma concatenação do nome do grupo de resolução e a ID exclusiva que foi especificada em cada classe de efeito específica da plataforma.

Em seguida, é possível passar parâmetros para o efeito adicionando as propriedades anexadas e valores de propriedade ao controle apropriado. Além disso, os parâmetros podem ser alterados no runtime especificando um novo valor da propriedade anexada.

> [!NOTE]
> Uma propriedade anexada é um tipo especial de propriedade associável, definida em uma classe, mas anexada a outros objetos e reconhecível no XAML como atributos que contêm uma classe e um nome de propriedade separados por um ponto. Para obter mais informações, confira [Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md).

O aplicativo de exemplo demonstra um `ShadowEffect` que adiciona uma sombra ao texto exibido por um [`Label`](xref:Xamarin.Forms.Label) controle. Além disso, a cor da sombra pode ser alterada em runtime. O diagrama a seguir ilustra as responsabilidades de cada projeto no aplicativo de exemplo, bem como as relações entre elas:

![Responsabilidades do projeto de efeito de sombra](attached-properties-images/shadow-effect.png)

Um [`Label`](xref:Xamarin.Forms.Label) controle no `HomePage` é personalizado pelo `LabelShadowEffect` em cada projeto específico da plataforma. Os parâmetros são passados para cada `LabelShadowEffect` por meio das propriedades anexadas na classe `ShadowEffect`. Cada classe `LabelShadowEffect` é derivada da classe `PlatformEffect` de cada plataforma. Isso faz com que uma sombra seja adicionada ao texto exibido pelo controle `Label`, conforme mostrado nas capturas de tela seguir:

![Efeito de sombra em cada plataforma](attached-properties-images/screenshots.png)

## <a name="creating-effect-parameters"></a>Criando parâmetros de efeito

Uma classe `static` deve ser criada para representar os parâmetros em vigor, conforme demonstrado no exemplo de código a seguir:

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

O `ShadowEffect` contém cinco propriedades anexadas, com getters e setters `static` para cada propriedade anexada. Quatro dessas propriedades representam parâmetros a serem passados para cada `LabelShadowEffect` específico da plataforma. A classe `ShadowEffect` também define uma propriedade anexada `HasShadow` que é usada para controlar a adição ou remoção do efeito para o controle a que a classe `ShadowEffect` é anexada. Essa propriedade anexada registra o método `OnHasShadowChanged` que será executado quando o valor da propriedade for alterado. Esse método adiciona ou remove o efeito com base no valor da propriedade anexada `HasShadow`.

A classe aninhada `LabelShadowEffect` , que subclasse a [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) classe, dá suporte à adição e remoção de efeito. A classe `RoutingEffect` representa um efeito independente de plataforma que encapsula um efeito interno, que é geralmente é específico da plataforma. Isso simplifica o processo de remoção do efeito, porque não há nenhum acesso de tempo de compilação às informações de tipo para um efeito específico da plataforma. O construtor `LabelShadowEffect` chama o construtor da classe base, passando um parâmetro composto por uma concatenação do nome do grupo de resolução e pela ID exclusiva que foi especificada em cada classe de efeito específica da plataforma. Isso habilita a adição e a remoção do efeito no método `OnHasShadowChanged`, da seguinte maneira:

- **Adição de efeito** – uma nova instância do `LabelShadowEffect` é adicionada à coleção [`Effects`](xref:Xamarin.Forms.Element.Effects) do controle. Isso substitui o uso do [`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) método para adicionar o efeito.
- **Remoção de efeito** – a primeira instância do `LabelShadowEffect` na coleção [`Effects`](xref:Xamarin.Forms.Element.Effects) do controle é recuperada e removida.

## <a name="consuming-the-effect"></a>Consumindo o efeito

Cada plataforma específica `LabelShadowEffect` pode ser consumida adicionando as propriedades anexadas a um [`Label`](xref:Xamarin.Forms.Label) controle, conforme demonstrado no exemplo de código XAML a seguir:

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

O equivalente [`Label`](xref:Xamarin.Forms.Label) em C# é mostrado no seguinte exemplo de código:

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

Definir a `ShadowEffect.HasShadow` Propriedade anexada para `true` executar o `ShadowEffect.OnHasShadowChanged` método que adiciona ou remove o `LabelShadowEffect` para o [`Label`](xref:Xamarin.Forms.Label) controle. Nos dois exemplos de código, a propriedade anexada `ShadowEffect.Color` fornece valores de cor específicos da plataforma. Para obter mais informações, confira [Classe do dispositivo](~/xamarin-forms/platform/device.md).

Além disso, um [`Button`](xref:Xamarin.Forms.Button) permite que a cor da sombra seja alterada em tempo de execução. Quando o usuário clica no `Button`, a código a seguir altera a cor da sombra definindo a propriedade anexada `ShadowEffect.Color`:

```csharp
ShadowEffect.SetColor (label, Color.Teal);
```

### <a name="consuming-the-effect-with-a-style"></a>Consumindo o efeito com um estilo

Os efeitos que podem ser consumidos adicionando propriedades anexadas a um controle também podem ser consumidos por um estilo. O exemplo de código XAML abaixo mostra um estilo *explícito* para o efeito de sombra, que pode ser aplicado a controles [`Label`](xref:Xamarin.Forms.Label):

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

O [`Style`](xref:Xamarin.Forms.Style) pode ser aplicado a um [`Label`](xref:Xamarin.Forms.Label) definindo sua [`Style`](xref:Xamarin.Forms.NavigableElement.Style) propriedade para a `Style` instância usando a `StaticResource` extensão de marcação, conforme demonstrado no exemplo de código a seguir:

```xaml
<Label Text="Label Shadow Effect" ... Style="{StaticResource ShadowEffectStyle}" />
```

Para mais informações sobre estilos, confira [Estilos](~/xamarin-forms/user-interface/styles/index.md).

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
            Control.Layer.ShadowRadius = (nfloat)ShadowEffect.GetRadius (Element);
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

O método `OnAttached` chama métodos que recuperam os valores de propriedade anexada usando os getters `ShadowEffect` e que definem as propriedades de `Control.Layer` com os valores da propriedade para criar a sombra. Essa funcionalidade é encapsulada em um bloco `try`/`catch` caso o controle a que o efeito está anexado não tenha as propriedades de `Control.Layer`. Nenhuma implementação é fornecida pelo método `OnDetached` porque nenhuma limpeza é necessária.

#### <a name="responding-to-property-changes"></a>Respondendo a alterações de propriedade

Se qualquer um dos valores da propriedade anexada `ShadowEffect` for alterado em runtime, o efeito precisará responder exibindo as alterações. Uma versão de substituição do método `OnElementPropertyChanged`, na classe de efeito específica da plataforma, é o lugar para responder a alterações de propriedade vinculáveis, conforme demonstrado no código de exemplo a seguir:

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

O método `OnElementPropertyChanged` atualiza o raio, a cor ou o deslocamento da sombra, desde que o valor da propriedade anexada `ShadowEffect` tenha sido alterado. Uma verificação da propriedade alterada sempre deve ser feita, pois essa substituição pode ser chamada várias vezes.

### <a name="android-project"></a>Projeto do Android

O exemplo de código a seguir mostra a implementação de `LabelShadowEffect` para o projeto do Android:

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

O `OnAttached` método chama métodos que recuperam os valores de propriedade anexados usando os `ShadowEffect` getters e chama um método que chama o [`TextView.SetShadowLayer`](xref:Android.Widget.TextView.SetShadowLayer*) método para criar uma sombra usando os valores de propriedade. Essa funcionalidade é encapsulada em um bloco `try`/`catch` caso o controle a que o efeito está anexado não tenha as propriedades de `Control.Layer`. Nenhuma implementação é fornecida pelo método `OnDetached` porque nenhuma limpeza é necessária.

#### <a name="responding-to-property-changes"></a>Respondendo a alterações de propriedade

Se qualquer um dos valores da propriedade anexada `ShadowEffect` for alterado em runtime, o efeito precisará responder exibindo as alterações. Uma versão de substituição do método `OnElementPropertyChanged`, na classe de efeito específica da plataforma, é o lugar para responder a alterações de propriedade vinculáveis, conforme demonstrado no código de exemplo a seguir:

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

O método `OnElementPropertyChanged` atualiza o raio, a cor ou o deslocamento da sombra, desde que o valor da propriedade anexada `ShadowEffect` tenha sido alterado. Uma verificação da propriedade alterada sempre deve ser feita, pois essa substituição pode ser chamada várias vezes.

### <a name="universal-windows-platform-project"></a>Projeto da Plataforma Universal do Windows

O exemplo de código a seguir mostra a implementação de `LabelShadowEffect` para o projeto da UWP (Plataforma Universal do Windows):

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

O Plataforma Universal do Windows não fornece um efeito de sombra e, portanto, a `LabelShadowEffect` implementação em ambas as plataformas simula uma adicionando um segundo deslocamento [`Label`](xref:Xamarin.Forms.Label) por trás do primário `Label` . O método `OnAttached` cria o novo `Label` e define algumas propriedades de layout no `Label`. Em seguida, ele chama métodos que recuperam os valores de propriedade anexados usando os `ShadowEffect` getters e cria a sombra definindo as [`TextColor`](xref:Xamarin.Forms.Label.TextColor) [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) Propriedades, e [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) para controlar a cor e o local do `Label` . Em seguida, o `shadowLabel` é inserido deslocado atrás do `Label` principal. Essa funcionalidade é encapsulada em um bloco `try`/`catch` caso o controle a que o efeito está anexado não tenha as propriedades de `Control.Layer`. Nenhuma implementação é fornecida pelo método `OnDetached` porque nenhuma limpeza é necessária.

#### <a name="responding-to-property-changes"></a>Respondendo a alterações de propriedade

Se qualquer um dos valores da propriedade anexada `ShadowEffect` for alterado em runtime, o efeito precisará responder exibindo as alterações. Uma versão de substituição do método `OnElementPropertyChanged`, na classe de efeito específica da plataforma, é o lugar para responder a alterações de propriedade vinculáveis, conforme demonstrado no código de exemplo a seguir:

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

O método `OnElementPropertyChanged` atualiza a cor ou o deslocamento da sombra, desde que o valor da propriedade anexada `ShadowEffect` tenha sido alterado. Uma verificação da propriedade alterada sempre deve ser feita, pois essa substituição pode ser chamada várias vezes.

## <a name="summary"></a>Resumo

Este artigo demonstrou o uso de propriedades anexadas para passar parâmetros para um efeito e a alteração de um parâmetro em runtime. Propriedades anexadas podem ser usadas para definir parâmetros de efeito que respondem a alterações de propriedade de runtime.

## <a name="related-links"></a>Links Relacionados

- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Efeito](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [RoutingEffect](xref:Xamarin.Forms.RoutingEffect)
- [Efeito de sombra (amostra)](/samples/xamarin/xamarin-forms-samples/effects-shadoweffectruntimechange)