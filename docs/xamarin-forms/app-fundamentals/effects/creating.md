---
title: Criando um efeito
description: Efeitos simplificam a personalização de um controle. Este artigo demonstra como criar um efeito que altera a cor da tela de fundo do controle Entry quando o controle obtém foco.
ms.prod: xamarin
ms.assetid: 9E2C8DB0-36A2-4F13-8E3C-A66D7021DB13
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2016
ms.openlocfilehash: c07848b808d023439c88117924e69c336984630b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771508"
---
# <a name="creating-an-effect"></a>Criando um efeito

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-focuseffect)

_Os efeitos simplificam a personalização de um controle. Este artigo demonstra como criar um efeito que altera a cor do plano de fundo do controle de entrada quando o controle ganha foco._

O processo para criar um efeito em cada projeto específico da plataforma é o seguinte:

1. Crie uma subclasse da classe `PlatformEffect`.
1. Substitua o método `OnAttached` e escreva a lógica para personalizar o controle.
1. Substitua o método `OnDetached` e escreva a lógica para limpar a personalização do controle se necessário.
1. Adicione um atributo [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) à classe do efeito. Esse atributo define um namespace para os efeitos que abrange toda a empresa, evitando conflitos com outros efeitos de mesmo nome. Observe que esse atributo só pode ser aplicado uma vez por projeto.
1. Adicione um atributo [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) à classe do efeito. Este atributo registra o efeito com uma ID exclusiva que é usada pelo Xamarin.Forms, em conjunto com o nome do grupo, para localizar o efeito antes de aplicá-lo a um controle. O atributo utiliza dois parâmetros – o nome do tipo de efeito e uma cadeia de caracteres exclusiva que será usada para localizar o efeito antes de aplicá-lo a um controle.

O efeito, em seguida, poderá ser consumido sendo anexado ao controle apropriado.

> [!NOTE]
> O fornecimento de um efeito em cada projeto de plataforma é opcional. Tentar usar um efeito quando não há um efeito registrado retornará um valor não nulo que não faz nada.

O aplicativo de exemplo demonstra um `FocusEffect` que altera a cor da tela de fundo de um controle quando ele obtém o foco. O seguinte diagrama ilustra as responsabilidades de cada projeto no aplicativo de exemplo, bem como as relações entre elas:

![](creating-images/focus-effect.png "Responsabilidades do projeto de efeito de foco")

Um controle [`Entry`](xref:Xamarin.Forms.Entry) no `HomePage` é personalizado pela classe `FocusEffect` em cada projeto específico da plataforma. Cada classe `FocusEffect` é derivada da classe `PlatformEffect` de cada plataforma. Isso faz com que o controle `Entry` seja renderizado com uma cor da tela de fundo específica da plataforma, que muda quando o controle obtém foco, conforme mostrado nas capturas de tela seguir:

![](creating-images/screenshots-1.png "Efeito de foco em cada plataforma")
![](creating-images/screenshots-2.png "Efeito de foco em cada plataforma")

## <a name="creating-the-effect-on-each-platform"></a>Criando o efeito em cada plataforma

As seções a seguir abordam a implementação da classe `FocusEffect` específica da plataforma.

## <a name="ios-project"></a>Projeto do iOS

O exemplo de código a seguir mostra a implementação de `FocusEffect` para o projeto do iOS:

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(EffectsDemo.iOS.FocusEffect), nameof(EffectsDemo.iOS.FocusEffect))]
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

O método `OnAttached` define a propriedade `BackgroundColor` do controle como roxo claro com o método `UIColor.FromRGB` e também armazena essa cor em um campo. Essa funcionalidade é encapsulada em um bloco `try`/`catch` caso o controle a que o efeito está anexado não tenha uma propriedade de `BackgroundColor`. Nenhuma implementação é fornecida pelo método `OnDetached` porque nenhuma limpeza é necessária.

A substituição `OnElementPropertyChanged` responde às alterações de propriedade associáveis no controle do Xamarin.Forms. Quando a propriedade [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused) é alterada, a propriedade `BackgroundColor` do controle será alterada para branco se o controle estiver em foco, caso contrário, é alterada para roxo claro. Essa funcionalidade é encapsulada em um bloco `try`/`catch` caso o controle a que o efeito está anexado não tenha uma propriedade de `BackgroundColor`.

## <a name="android-project"></a>Projeto do Android

O exemplo de código a seguir mostra a implementação de `FocusEffect` para o projeto do Android:

```csharp
using System;
using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(EffectsDemo.Droid.FocusEffect), nameof(EffectsDemo.Droid.FocusEffect))]
namespace EffectsDemo.Droid
{
    public class FocusEffect : PlatformEffect
    {
        Android.Graphics.Color originalBackgroundColor = new Android.Graphics.Color(0, 0, 0, 0);
        Android.Graphics.Color backgroundColor;

        protected override void OnAttached()
        {
            try
            {
                backgroundColor = Android.Graphics.Color.LightGreen;
                Control.SetBackgroundColor(backgroundColor);
            }
            catch (Exception ex)
            {
                Console.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached()
        {
        }

        protected override void OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(args);
            try
            {
                if (args.PropertyName == "IsFocused")
                {
                    if (((Android.Graphics.Drawables.ColorDrawable)Control.Background).Color == backgroundColor)
                    {
                        Control.SetBackgroundColor(originalBackgroundColor);
                    }
                    else
                    {
                        Control.SetBackgroundColor(backgroundColor);
                    }
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

O método `OnAttached` chama o método `SetBackgroundColor` para definir a cor da tela de fundo do controle como verde claro e também armazena essa cor em um campo. Essa funcionalidade é encapsulada em um bloco `try`/`catch` caso o controle a que o efeito está anexado não tenha uma propriedade de `SetBackgroundColor`. Nenhuma implementação é fornecida pelo método `OnDetached` porque nenhuma limpeza é necessária.

A substituição `OnElementPropertyChanged` responde às alterações de propriedade associáveis no controle do Xamarin.Forms. Quando a propriedade [`IsFocused`](xref:Xamarin.Forms.VisualElement.IsFocused) é alterada, a cor da tela de fundo do controle será alterada para branco se o controle estiver em foco, caso contrário, é alterada para verde claro. Essa funcionalidade é encapsulada em um bloco `try`/`catch` caso o controle a que o efeito está anexado não tenha uma propriedade de `BackgroundColor`.

## <a name="universal-windows-platform-projects"></a>Projetos da Plataforma Universal do Windows

O exemplo de código a seguir mostra a implementação de `FocusEffect` para projetos da UWP (Plataforma Universal do Windows):

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.UWP;

[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(EffectsDemo.UWP.FocusEffect), nameof(EffectsDemo.UWP.FocusEffect))]
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

O método `OnAttached` define a propriedade `Background` do controle como ciano e define a propriedade `BackgroundFocusBrush` como branco. Essa funcionalidade é encapsulada em um bloco `try`/`catch` caso o controle a que o efeito está anexado não tenha essas propriedades. Nenhuma implementação é fornecida pelo método `OnDetached` porque nenhuma limpeza é necessária.

## <a name="consuming-the-effect"></a>Consumindo o efeito

O processo para consumir um efeito de uma biblioteca .NET Standard do Xamarin.Forms ou de um projeto de Biblioteca compartilhada é o seguinte:

1. Declare um controle que será personalizado pelo efeito.
1. Anexe o efeito ao controle adicionando-o à coleção [`Effects`](xref:Xamarin.Forms.Element.Effects) do controle.

> [!NOTE]
> Uma instância de efeito pode ser anexada somente a um único controle. Portanto, um efeito deve ser resolvido duas vezes para usá-lo em dois controles.

## <a name="consuming-the-effect-in-xaml"></a>Consumindo o efeito em XAML

O exemplo de código XAML abaixo mostra um controle [`Entry`](xref:Xamarin.Forms.Entry) ao qual o `FocusEffect` está anexado:

```xaml
<Entry Text="Effect attached to an Entry" ...>
    <Entry.Effects>
        <local:FocusEffect />
    </Entry.Effects>
    ...
</Entry>
```

A classe `FocusEffect` na biblioteca do .NET Standard dá suporte ao consumo do efeito em XAML e é mostrado no exemplo de código a seguir:

```csharp
public class FocusEffect : RoutingEffect
{
    public FocusEffect () : base ($"MyCompany.{nameof(FocusEffect)}")
    {
    }
}
```

A classe `FocusEffect` cria subclasses da classe [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect), que representa um efeito independente de plataforma que encapsula um efeito interno, que é geralmente é específico da plataforma. A classe `FocusEffect` chama o construtor da classe base, passando um parâmetro composto por uma concatenação do nome do grupo de resolução (especificado usando o atributo [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) na classe do efeito) e pela ID exclusiva que foi especificada usando o atributo [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) na classe do efeito. Portanto, quando o [`Entry`](xref:Xamarin.Forms.Entry) é inicializado em runtime, uma nova instância do `MyCompany.FocusEffect` é adicionada à coleção [`Effects`](xref:Xamarin.Forms.Element.Effects) do controle.

Efeitos também podem ser anexados a controles usando um comportamento ou usando propriedades anexadas. Para obter mais informações sobre como anexar um efeito a um controle usando um comportamento, confira [EffectBehavior reutilizável](~/xamarin-forms/app-fundamentals/behaviors/reusable/effect-behavior.md). Para obter mais informações sobre como anexar um efeito a um controle usando propriedades anexadas, confira [Passar parâmetros para um efeito](~/xamarin-forms/app-fundamentals/effects/passing-parameters/index.md).

## <a name="consuming-the-effect-in-cnum"></a>Consumindo o efeito em C&num;

O [`Entry`](xref:Xamarin.Forms.Entry) equivalente em C# é mostrado no exemplo de código a seguir:

```csharp
var entry = new Entry {
  Text = "Effect attached to an Entry",
  ...
};
```

O `FocusEffect` é anexado à instância de `Entry` adicionando o efeito à coleção [`Effects`](xref:Xamarin.Forms.Element.Effects) do controle, conforme demonstrado no exemplo de código a seguir:

```csharp
public HomePageCS ()
{
  ...
  entry.Effects.Add (Effect.Resolve ($"MyCompany.{nameof(FocusEffect)}"));
  ...
}
```

O [`Effect.Resolve`](xref:Xamarin.Forms.Effect.Resolve(System.String)) retorna um [`Effect`](xref:Xamarin.Forms.Effect) para o nome especificado, que é uma concatenação do nome do grupo de resolução (especificado usando o atributo [`ResolutionGroupName`](xref:Xamarin.Forms.ResolutionGroupNameAttribute) na classe do efeito) e pela ID exclusiva que foi especificada usando o atributo [`ExportEffect`](xref:Xamarin.Forms.ExportEffectAttribute) na classe do efeito. Se uma plataforma não fornecer o efeito, o método `Effect.Resolve` retornará um valor não `null`.

## <a name="summary"></a>Resumo

Este artigo demonstrou como criar um efeito que altera a cor da tela de fundo do controle [`Entry`](xref:Xamarin.Forms.Entry) quando o controle obtém foco.

## <a name="related-links"></a>Links relacionados

- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Effect](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [Efeito de cor da tela de fundo (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-backgroundcoloreffect)
- [Efeito de foco (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-focuseffect)
