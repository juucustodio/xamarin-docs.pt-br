---
title: Criando um efeito
description: Efeitos de simplificam a personalização de um controle. Este artigo demonstra como criar um efeito que muda a cor de plano de fundo do controle de entrada quando o controle obtém foco.
ms.prod: xamarin
ms.assetid: 9E2C8DB0-36A2-4F13-8E3C-A66D7021DB13
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2016
ms.openlocfilehash: d308ea4a9d4055c75ef3a4a1283b5d6a5ab24b0a
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34846799"
---
# <a name="creating-an-effect"></a>Criando um efeito

_Efeitos de simplificam a personalização de um controle. Este artigo demonstra como criar um efeito que muda a cor de plano de fundo do controle de entrada quando o controle obtém foco._

O processo para criar um efeito em cada projeto específico de plataforma é o seguinte:

1. Criar uma subclasse do `PlatformEffect` classe.
1. Substituir o `OnAttached` método e gravação lógica para personalizar o controle.
1. Substituir o `OnDetached` método e gravação lógica para limpar a personalização de controle, se necessário.
1. Adicionar um [ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/) de atributo para a classe do efeito. Esse atributo define um namespace grande empresa efeitos, evitar colisões com outros efeitos com o mesmo nome. Observe que esse atributo só pode ser aplicado uma vez por projeto.
1. Adicionar uma [ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/) de atributo para a classe do efeito. Este atributo registra o efeito com uma ID exclusiva que é usada pelo xamarin. Forms, juntamente com o nome do grupo, para localizar o efeito antes de aplicá-la a um controle. O atributo utiliza dois parâmetros – o nome do tipo do efeito e uma cadeia de caracteres exclusiva que será usada para localizar o efeito antes de aplicá-la a um controle.

O efeito pode ser consumido por anexá-lo para o controle apropriado.

> [!NOTE]
> É opcional fornecer um efeito em cada projeto da plataforma. Tentativa de usar um efeito quando um não estiver registrado retornará um valor não nulo que não faz nada.

O aplicativo de exemplo demonstra um `FocusEffect` que altera a cor de fundo de um controle quando ele recebe o foco. O diagrama a seguir ilustra as responsabilidades de cada projeto de aplicativo de exemplo, juntamente com as relações entre eles:

![](creating-images/focus-effect.png "Responsabilidades de projeto do efeito de foco")

Um [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) control no `HomePage` é personalizado pelo `FocusEffect` classe em cada projeto específico da plataforma. Cada `FocusEffect` classe deriva de `PlatformEffect` classe para cada plataforma. Isso resulta no `Entry` controlar que está sendo processada com uma cor de plano de fundo específica de plataforma, que altera quando o controle obtém foco, conforme mostrado nas capturas de tela seguir:

![](creating-images/screenshots-1.png "Efeito em cada plataforma de foco")
![](creating-images/screenshots-2.png "focar efeito em cada plataforma")

## <a name="creating-the-effect-on-each-platform"></a>Criando o efeito de cada plataforma

As seções a seguir abordam a implementação específica de plataforma do `FocusEffect` classe.

## <a name="ios-project"></a>Projeto do iOS

O seguinte exemplo de código mostra o `FocusEffect` implementação para o projeto iOS:

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(FocusEffect), "FocusEffect")]
namespace EffectsDemo.iOS
{
    public class FocusEffect : PlatformEffect
    {
        UIColor backgroundColor;

        protected override void OnAttached ()
        {
            try {
                Control.BackgroundColor = backgroundColor = UIColor.FromRGB (204, 153, 255);
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }

        protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged (args);

            try {
                if (args.PropertyName == "IsFocused") {
                    if (Control.BackgroundColor == backgroundColor) {
                        Control.BackgroundColor = UIColor.White;
                    } else {
                        Control.BackgroundColor = backgroundColor;
                    }
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

O `OnAttached` método define o `BackgroundColor` propriedade do controle para lilás com o `UIColor.FromRGB` método e também armazena essa cor em um campo. Essa funcionalidade é encapsulada em um `try` / `catch` bloquear caso não tem o controle o efeito é anexado a um `BackgroundColor` propriedade. Nenhuma implementação é fornecida pelo `OnDetached` método porque nenhuma limpeza é necessária.

O `OnElementPropertyChanged` substituição responde às alterações de propriedade ligável no controle xamarin. Forms. Quando o [ `IsFocused` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsFocused/) alterações de propriedade, o `BackgroundColor` propriedade do controle é alterada em branco se o controle tem foco, caso contrário, ele é alterado para roxo claro. Essa funcionalidade é encapsulada em um `try` / `catch` bloquear caso não tem o controle o efeito é anexado a um `BackgroundColor` propriedade.

## <a name="android-project"></a>Projeto Android

O seguinte exemplo de código mostra o `FocusEffect` implementação para o projeto Android:

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(FocusEffect), "FocusEffect")]
namespace EffectsDemo.Droid
{
    public class FocusEffect : PlatformEffect
    {
        Android.Graphics.Color backgroundColor;

        protected override void OnAttached ()
        {
            try {
                backgroundColor = Android.Graphics.Color.LightGreen;
                Control.SetBackgroundColor (backgroundColor);

            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }

        protected override void OnElementPropertyChanged (System.ComponentModel.PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged (args);
            try {
                if (args.PropertyName == "IsFocused") {
                    if (((Android.Graphics.Drawables.ColorDrawable)Control.Background).Color == backgroundColor) {
                        Control.SetBackgroundColor (Android.Graphics.Color.Black);
                    } else {
                        Control.SetBackgroundColor (backgroundColor);
                    }
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

O `OnAttached` chamadas de método de `SetBackgroundColor` método para definir a cor de plano de fundo do controle à luz verde e também armazena essa cor em um campo. Essa funcionalidade é encapsulada em um `try` / `catch` bloquear caso não tem o controle o efeito é anexado a um `SetBackgroundColor` propriedade. Nenhuma implementação é fornecida pelo `OnDetached` método porque nenhuma limpeza é necessária.

O `OnElementPropertyChanged` substituição responde às alterações de propriedade ligável no controle xamarin. Forms. Quando o [ `IsFocused` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsFocused/) alterações de propriedade, a cor de plano de fundo do controle é alterada em branco se o controle tem foco, caso contrário, ele é alterado para verde-claro. Essa funcionalidade é encapsulada em um `try` / `catch` bloquear caso não tem o controle o efeito é anexado a um `BackgroundColor` propriedade.

## <a name="universal-windows-platform-projects"></a>Projetos de plataforma universal do Windows

O seguinte exemplo de código mostra o `FocusEffect` implementação para projetos do Windows UWP (plataforma Universal):

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.UWP;

[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(FocusEffect), "FocusEffect")]
namespace EffectsDemo.UWP
{
    public class FocusEffect : PlatformEffect
    {
        protected override void OnAttached()
        {
            try
            {
                (Control as Windows.UI.Xaml.Controls.Control).Background = new SolidColorBrush(Colors.Cyan);
                (Control as FormsTextBox).BackgroundFocusBrush = new SolidColorBrush(Colors.White);
            }
            catch (Exception ex)
            {
                Debug.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached()
        {
        }
    }
}
```

O `OnAttached` método define o `Background` propriedade do controle como ciano e define o `BackgroundFocusBrush` propriedade em branco. Essa funcionalidade é encapsulada em um `try` / `catch` bloquear caso o efeito é anexado ao controle não tem essas propriedades. Nenhuma implementação é fornecida pelo `OnDetached` método porque nenhuma limpeza é necessária.

## <a name="consuming-the-effect"></a>O efeito de consumo

O processo para consumir um efeito de uma biblioteca xamarin. Forms .NET padrão ou um projeto de biblioteca compartilhada é da seguinte maneira:

1. Declare um controle que será personalizado pelo efeito.
1. Anexar o efeito para o controle adicionando-o para o controle [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) coleção.

> [!NOTE]
> Uma instância de efeito somente pode ser conectada a um único controle. Portanto, um efeito deve ser resolvido duas vezes para usá-lo em dois controles.

## <a name="consuming-the-effect-in-xaml"></a>Consumindo o efeito em XAML

Mostra o exemplo seguinte do código XAML um [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controle para o qual o `FocusEffect` está anexado:

```xaml
<Entry Text="Effect attached to an Entry" ...>
    <Entry.Effects>
        <local:FocusEffect />
    </Entry.Effects>
    ...
</Entry>
```

O `FocusEffect` classe na biblioteca do .NET padrão oferece suporte ao consumo de efeito em XAML e é mostrado no exemplo de código a seguir:

```csharp
public class FocusEffect : RoutingEffect
{
    public FocusEffect () : base ("MyCompany.FocusEffect")
    {
    }
}
```

O `FocusEffect` classe subclasses de [ `RoutingEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RoutingEffect/) classe que representa um efeito independente de plataforma que encapsula um efeito interno que é geralmente específica da plataforma. O `FocusEffect` classe chama o construtor de classe base, passando um parâmetro que consiste em uma concatenação do nome do grupo de resolução (especificada usando o [ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/) atributo da classe efeito), e a ID única que foi especificado usando o [ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/) atributo da classe do efeito. Portanto, quando o [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) é inicializado no tempo de execução, uma nova instância do `MyCompany.FocusEffect` é adicionado ao controle de [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) coleção.

Efeitos também podem ser anexados a controles usando um comportamento ou por meio de propriedades anexadas. Para obter mais informações sobre como anexar um efeito a um controle usando um comportamento, consulte [EffectBehavior reutilizável](~/xamarin-forms/app-fundamentals/behaviors/reusable/effect-behavior.md). Para obter mais informações sobre como anexar um efeito a um controle usando propriedades anexadas, consulte [passar parâmetros para um efeito](~/xamarin-forms/app-fundamentals/effects/passing-parameters/index.md).

## <a name="consuming-the-effect-in-cnum"></a>Consumindo o efeito em C&num;

O equivalente [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) c# é mostrado no exemplo de código a seguir:

```csharp
var entry = new Entry {
  Text = "Effect attached to an Entry",
  ...
};
```

O `FocusEffect` está associada a `Entry` instância adicionando o efeito para o controle [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) coleção, conforme demonstrado no exemplo de código a seguir:

```csharp
public HomePageCS ()
{
  ...
  entry.Effects.Add (Effect.Resolve ("MyCompany.FocusEffect"));
  ...
}
```

O [ `Effect.Resolve` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Effect.Resolve/p/System.String/) retorna um [ `Effect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/) para o nome especificado, que é uma concatenação do nome do grupo de resolução (especificada usando o [ `ResolutionGroupName` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResolutionGroupNameAttribute/) atributo da classe efeito) e a ID exclusiva que é especificada com o [ `ExportEffect` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ExportEffectAttribute/) atributo da classe do efeito. Se uma plataforma não fornece o efeito de `Effect.Resolve` método retornará não`null` valor.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar um efeito que altera a cor de fundo de [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controlar quando o controle obtém foco.


## <a name="related-links"></a>Links relacionados

- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Efeito](https://developer.xamarin.com/api/type/Xamarin.Forms.Effect/)
- [PlatformEffect](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformEffect%3CTContainer,TControl%3E/)
- [Efeito de cor de plano de fundo (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/effects/backgroundcoloreffect/)
- [Efeito de foco (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/effects/focuseffect/)
