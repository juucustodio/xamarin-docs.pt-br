---
title: Criando um efeito
description: Efeitos de simplificam a personalização de um controle. Este artigo demonstra como criar um efeito que muda a cor de plano de fundo do controle de entrada quando o controle obtiver foco.
ms.prod: xamarin
ms.assetid: 9E2C8DB0-36A2-4F13-8E3C-A66D7021DB13
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2016
ms.openlocfilehash: b29d83999724a35293882f7b9efc0158171c4fd2
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998154"
---
# <a name="creating-an-effect"></a>Criando um efeito

_Efeitos de simplificam a personalização de um controle. Este artigo demonstra como criar um efeito que muda a cor de plano de fundo do controle de entrada quando o controle obtiver foco._

O processo para criar um efeito em cada projeto específico da plataforma é o seguinte:

1. Criar uma subclasse do `PlatformEffect` classe.
1. Substituir o `OnAttached` lógica de método e gravação para personalizar o controle.
1. Substituir o `OnDetached` lógica de método e gravação para limpar a personalização de controle, se necessário.
1. Adicionar um [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) atributo à classe em vigor. Esse atributo define um namespace de ampla da empresa para efeitos, evitando conflitos com outros efeitos com o mesmo nome. Observe que esse atributo só pode ser aplicado uma vez por projeto.
1. Adicionar um [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) atributo à classe em vigor. Este atributo registra o efeito com uma ID exclusiva que é usada pelo xamarin. Forms, juntamente com o nome do grupo, para localizar o efeito antes de aplicá-la a um controle. O atributo utiliza dois parâmetros – o nome do tipo de uma cadeia de caracteres exclusiva que será usada para localizar o efeito antes de aplicá-la a um controle e o efeito.

O efeito, em seguida, pode ser consumido anexando-o para o controle apropriado.

> [!NOTE]
> É opcional fornecer um efeito em cada projeto de plataforma. Tentativa de usar um efeito quando um não estiver registrado retornará um valor não nulo que não faz nada.

O aplicativo de exemplo demonstra um `FocusEffect` que altera a cor de fundo de um controle quando ele recebe o foco. O diagrama a seguir ilustra as responsabilidades de cada projeto no aplicativo de exemplo, juntamente com as relações entre eles:

![](creating-images/focus-effect.png "Responsabilidades do projeto de efeito de foco")

Uma [ `Entry` ](xref:Xamarin.Forms.Entry) control no `HomePage` é personalizado com o `FocusEffect` classe em cada projeto específico da plataforma. Cada `FocusEffect` classe deriva de `PlatformEffect` classe para cada plataforma. Isso resulta no `Entry` controlar o que está sendo renderizado com uma cor de plano de fundo específica da plataforma, que muda quando o controle obtiver foco, conforme mostrado nas capturas de tela seguir:

![](creating-images/screenshots-1.png "Concentre-se em vigor em cada plataforma")
![](creating-images/screenshots-2.png "se concentrar em vigor em cada plataforma")

## <a name="creating-the-effect-on-each-platform"></a>Criando o efeito de cada plataforma

As seções a seguir discutem a implementação específica da plataforma do `FocusEffect` classe.

## <a name="ios-project"></a>Projeto do iOS

O seguinte exemplo de código mostra o `FocusEffect` implementação para o projeto do iOS:

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

O `OnAttached` método define o `BackgroundColor` propriedade do controle para roxo-claro com o `UIColor.FromRGB` método e também armazena essa cor em um campo. Essa funcionalidade é encapsulada em um `try` / `catch` bloquear no caso de não tem o controle o efeito é anexado a um `BackgroundColor` propriedade. Nenhuma implementação é fornecida pelo `OnDetached` método porque nenhuma limpeza é necessária.

O `OnElementPropertyChanged` substituição responde às alterações de propriedade associável no controle do xamarin. Forms. Quando o [ `IsFocused` ](xref:Xamarin.Forms.VisualElement.IsFocused) alterações de propriedade, o `BackgroundColor` propriedade do controle é alterada para em branco se o controle tem foco, caso contrário, ele é alterado para roxo-claro. Essa funcionalidade é encapsulada em um `try` / `catch` bloquear no caso de não tem o controle o efeito é anexado a um `BackgroundColor` propriedade.

## <a name="android-project"></a>Projeto do Android

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

O `OnAttached` chamadas de método a `SetBackgroundColor` método para definir a cor de plano de fundo do controle à luz verde e também armazena essa cor em um campo. Essa funcionalidade é encapsulada em um `try` / `catch` bloquear no caso de não tem o controle o efeito é anexado a um `SetBackgroundColor` propriedade. Nenhuma implementação é fornecida pelo `OnDetached` método porque nenhuma limpeza é necessária.

O `OnElementPropertyChanged` substituição responde às alterações de propriedade associável no controle do xamarin. Forms. Quando o [ `IsFocused` ](xref:Xamarin.Forms.VisualElement.IsFocused) alterações de propriedade, a cor do plano de fundo do controle é alterada para em branco se o controle tem foco, caso contrário, ele é alterado para verde claro. Essa funcionalidade é encapsulada em um `try` / `catch` bloquear no caso de não tem o controle o efeito é anexado a um `BackgroundColor` propriedade.

## <a name="universal-windows-platform-projects"></a>Projetos de plataforma universal do Windows

O seguinte exemplo de código mostra o `FocusEffect` implementação para projetos de plataforma Universal do Windows (UWP):

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

O `OnAttached` método define o `Background` propriedade do controle como ciano e define o `BackgroundFocusBrush` propriedade em branco. Essa funcionalidade é encapsulada em um `try` / `catch` bloquear no caso do efeito é anexado ao controle não tem essas propriedades. Nenhuma implementação é fornecida pelo `OnDetached` método porque nenhuma limpeza é necessária.

## <a name="consuming-the-effect"></a>Consumindo o efeito

O processo para o consumo de um efeito de um projeto de biblioteca compartilhada ou a biblioteca do xamarin. Forms .NET Standard é da seguinte maneira:

1. Declare um controle que será personalizado pelo efeito.
1. Anexar o efeito para o controle ao adicioná-lo para o controle [ `Effects` ](xref:Xamarin.Forms.Element.Effects) coleção.

> [!NOTE]
> Uma instância de efeito somente pode ser anexada a um único controle. Portanto, um efeito deve ser resolvido duas vezes para usá-lo em dois controles.

## <a name="consuming-the-effect-in-xaml"></a>Consumindo o efeito em XAML

Mostra o exemplo de código XAML abaixo uma [ `Entry` ](xref:Xamarin.Forms.Entry) controle ao qual o `FocusEffect` está anexado:

```xaml
<Entry Text="Effect attached to an Entry" ...>
    <Entry.Effects>
        <local:FocusEffect />
    </Entry.Effects>
    ...
</Entry>
```

O `FocusEffect` classe na biblioteca do .NET Standard dá suporte ao consumo em vigor no XAML e é mostrado no exemplo de código a seguir:

```csharp
public class FocusEffect : RoutingEffect
{
    public FocusEffect () : base ("MyCompany.FocusEffect")
    {
    }
}
```

O `FocusEffect` subclasses de classes do [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) classe, que representa um efeito de independente de plataforma que encapsula um efeito interno que é geralmente específico da plataforma. O `FocusEffect` classe chama o construtor de classe base, passando um parâmetro que consiste em uma concatenação do nome do grupo de resolução (especificados usando o [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) atributo na classe efeito), e a ID única que foi especificado usando o [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) atributo na classe em vigor. Portanto, quando o [ `Entry` ](xref:Xamarin.Forms.Entry) é inicializado em tempo de execução, uma nova instância da `MyCompany.FocusEffect` é adicionado ao controle de [ `Effects` ](xref:Xamarin.Forms.Element.Effects) coleção.

Efeitos também podem ser anexados aos controles usando um comportamento ou por meio de propriedades anexadas. Para obter mais informações sobre como anexar um efeito a um controle usando um comportamento, consulte [reutilizável EffectBehavior](~/xamarin-forms/app-fundamentals/behaviors/reusable/effect-behavior.md). Para obter mais informações sobre como anexar um efeito a um controle usando as propriedades anexadas, consulte [passando parâmetros para um efeito](~/xamarin-forms/app-fundamentals/effects/passing-parameters/index.md).

## <a name="consuming-the-effect-in-cnum"></a>Consumindo o efeito em C&num;

O equivalente [ `Entry` ](xref:Xamarin.Forms.Entry) em c# é mostrado no exemplo de código a seguir:

```csharp
var entry = new Entry {
  Text = "Effect attached to an Entry",
  ...
};
```

O `FocusEffect` está associada a `Entry` instância adicionando o efeito para o controle [ `Effects` ](xref:Xamarin.Forms.Element.Effects) coleção, conforme demonstrado no exemplo de código a seguir:

```csharp
public HomePageCS ()
{
  ...
  entry.Effects.Add (Effect.Resolve ("MyCompany.FocusEffect"));
  ...
}
```

O [ `Effect.Resolve` ](xref:Xamarin.Forms.Effect.Resolve(System.String)) retorna um [ `Effect` ](xref:Xamarin.Forms.Effect) para o nome especificado, o que é uma concatenação do nome do grupo de resolução (especificados usando o [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) atributo na classe efeito) e a ID exclusiva que foi especificada usando o [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) atributo na classe em vigor. Se uma plataforma não fornecer o efeito, a `Effect.Resolve` método será retornado um não -`null` valor.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar um efeito que altera a cor de fundo a [ `Entry` ](xref:Xamarin.Forms.Entry) controlar quando o controle obtiver foco.


## <a name="related-links"></a>Links relacionados

- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Em vigor](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [Efeito de cor de plano de fundo (amostra)](https://developer.xamarin.com/samples/xamarin-forms/effects/backgroundcoloreffect/)
- [Efeito de foco (amostra)](https://developer.xamarin.com/samples/xamarin-forms/effects/focuseffect/)
