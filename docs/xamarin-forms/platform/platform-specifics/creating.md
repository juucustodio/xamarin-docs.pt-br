---
title: Criando as especificidades da plataforma
description: Este artigo demonstra como expor um efeito por meio de uma plataforma específica.
ms.prod: xamarin
ms.assetid: 0D0E6274-6EF2-4D40-BB77-3D8E53BCD24B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/23/2016
ms.openlocfilehash: 3f68b7c1ef9603d4a6d21e0c00a4cc8f4f7d818a
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53058514"
---
# <a name="creating-platform-specifics"></a>Criando as especificidades da plataforma

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/shadowplatformspecific/)

_Fornecedores podem criar seus próprios itens específicos à plataforma com efeitos. Um efeito fornece a funcionalidade específica, que, em seguida, é exposta por meio de uma plataforma específica. O resultado é um efeito que pode ser mais facilmente consumido por meio de XAML e um API fluente de código. Este artigo demonstra como expor um efeito por meio de uma plataforma específica._

## <a name="overview"></a>Visão geral

O processo para a criação de uma plataforma específica é da seguinte maneira:

1. Implemente a funcionalidade específica como um efeito. Para obter mais informações, consulte [criando um efeito](~/xamarin-forms/app-fundamentals/effects/creating.md).
1. Crie uma classe específica da plataforma que irá expor o efeito. Para obter mais informações, consulte [criando uma classe específica da plataforma](#creating).
1. Na classe específica da plataforma, implemente uma propriedade anexada para permitir que o específicos da plataforma ser consumida por meio de XAML. Para obter mais informações, consulte [adicionando uma propriedade anexada](#attached_property).
1. Na classe específica da plataforma, implemente métodos de extensão para permitir que o específicos da plataforma ser consumida por meio de um API fluente de código. Para obter mais informações, consulte [adicionando métodos de extensão](#extension_methods).
1. Modifique a implementação em vigor para que o efeito é aplicado somente se a plataforma específica foi invocada na mesma plataforma como o efeito. Para obter mais informações, consulte [criando o efeito de](#creating_the_effect).

O resultado de expor um efeito como uma plataforma específica é que o efeito pode ser consumido com mais facilidade por meio de XAML e um API fluente de código.

> [!NOTE]
> É previu que fornecedores usará essa técnica para criar seus próprios-especificidades da plataforma, para facilitar o consumo por usuários. Embora os usuários podem optar por criar seus próprios especificidades da plataforma, deve-se observar que ela requer mais código que criar e consumir um efeito.

O aplicativo de exemplo demonstra um `Shadow` específico da plataforma que adiciona uma sombra para o texto exibido por um [ `Label` ](xref:Xamarin.Forms.Label) controle:

![](creating-images/screenshots.png "Específico da plataforma de sombra")

O aplicativo de exemplo implementa o `Shadow` específico da plataforma em cada plataforma, para facilitar a compreensão. No entanto, além de cada implementação do efeito específico da plataforma, a implementação da classe sombra é praticamente idêntica para cada plataforma. Portanto, este guia concentra-se na implementação da classe de sombra e efeito associado em uma única plataforma.

Para obter mais informações sobre os efeitos, consulte [personalizar controles com efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).

<a name="creating" />

## <a name="creating-a-platform-specific-class"></a>Criando uma classe específica da plataforma

Uma plataforma específica é criada como um `public static` classe:

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
  public static Shadow
  {
    ...
  }
}
```

As seções a seguir discutem a implementação do `Shadow` efeito específico da plataforma e associado.

<a name="attached_property" />

### <a name="adding-an-attached-property"></a>Adicionando uma propriedade anexada

Uma propriedade anexada deve ser adicionada para o `Shadow` específicos da plataforma para permitir o consumo por meio de XAML:

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
    using System.Linq;
    using Xamarin.Forms;
    using Xamarin.Forms.PlatformConfiguration;
    using FormsElement = Xamarin.Forms.Label;

    public static class Shadow
    {
        const string EffectName = "MyCompany.LabelShadowEffect";

        public static readonly BindableProperty IsShadowedProperty =
            BindableProperty.CreateAttached("IsShadowed",
                                            typeof(bool),
                                            typeof(Shadow),
                                            false,
                                            propertyChanged: OnIsShadowedPropertyChanged);

        public static bool GetIsShadowed(BindableObject element)
        {
            return (bool)element.GetValue(IsShadowedProperty);
        }

        public static void SetIsShadowed(BindableObject element, bool value)
        {
            element.SetValue(IsShadowedProperty, value);
        }

        ...

        static void OnIsShadowedPropertyChanged(BindableObject element, object oldValue, object newValue)
        {
            if ((bool)newValue)
            {
                AttachEffect(element as FormsElement);
            }
            else
            {
                DetachEffect(element as FormsElement);
            }
        }

        static void AttachEffect(FormsElement element)
        {
            IElementController controller = element;
            if (controller == null || controller.EffectIsAttached(EffectName))
            {
                return;
            }
            element.Effects.Add(Effect.Resolve(EffectName));
        }

        static void DetachEffect(FormsElement element)
        {
            IElementController controller = element;
            if (controller == null || !controller.EffectIsAttached(EffectName))
            {
                return;
            }

            var toRemove = element.Effects.FirstOrDefault(e => e.ResolveId == Effect.Resolve(EffectName).ResolveId);
            if (toRemove != null)
            {
                element.Effects.Remove(toRemove);
            }
        }
    }
}
```

O `IsShadowed` propriedade anexada é usada para adicionar o `MyCompany.LabelShadowEffect` efeito e removê-lo do controle que o `Shadow` classe está anexado ao. Isso anexado propriedade registra o `OnIsShadowedPropertyChanged` método que será executado quando o valor da propriedade é alterado. Por sua vez, esse método chama o `AttachEffect` ou `DetachEffect` método para adicionar ou remover o efeito com base no valor da `IsShadowed` propriedade anexada. O efeito é adicionado ou removido do controle, modificando o controle [ `Effects` ](xref:Xamarin.Forms.Element.Effects) coleção.

> [!NOTE]
> Observe que o efeito é resolvido, especificando um valor que é uma concatenação do nome do grupo de resolução e identificador exclusivo que é especificado na implementação do efeito. Para obter mais informações, consulte [criando um efeito](~/xamarin-forms/app-fundamentals/effects/creating.md).

Para obter mais informações sobre propriedades anexadas, consulte [propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md).

<a name="extension_methods" />

### <a name="adding-extension-methods"></a>Adicionando métodos de extensão

Métodos de extensão devem ser adicionados para o `Shadow` específicos da plataforma para permitir o consumo por meio de um API fluente de código:

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
    using System.Linq;
    using Xamarin.Forms;
    using Xamarin.Forms.PlatformConfiguration;
    using FormsElement = Xamarin.Forms.Label;

    public static class Shadow
    {
        ...
        public static bool IsShadowed(this IPlatformElementConfiguration<iOS, FormsElement> config)
        {
            return GetIsShadowed(config.Element);
        }

        public static IPlatformElementConfiguration<iOS, FormsElement> SetIsShadowed(this IPlatformElementConfiguration<iOS, FormsElement> config, bool value)
        {
            SetIsShadowed(config.Element, value);
            return config;
        }
        ...
    }
}
```

O `IsShadowed` e `SetIsShadowed` chamar get de métodos de extensão e definir acessadores para o `IsShadowed` anexado a propriedade, respectivamente. Cada método de extensão funciona com o `IPlatformElementConfiguration<iOS, FormsElement>` tipo, que especifica que a plataforma específica pode ser invocada sob [ `Label` ](xref:Xamarin.Forms.Label) instâncias do iOS.

<a name="creating_the_effect" />

### <a name="creating-the-effect"></a>Criando o efeito

O `Shadow` específico da plataforma adiciona a `MyCompany.LabelShadowEffect` para um [ `Label` ](xref:Xamarin.Forms.Label)e a remove. O seguinte exemplo de código mostra o `LabelShadowEffect` implementação para o projeto do iOS:

```csharp
[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace ShadowPlatformSpecific.iOS
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached()
        {
            UpdateShadow();
        }

        protected override void OnDetached()
        {
        }

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(args);

            if (args.PropertyName == Shadow.IsShadowedProperty.PropertyName)
            {
                UpdateShadow();
            }
        }

        void UpdateShadow()
        {
            try
            {
                if (((Label)Element).OnThisPlatform().IsShadowed())
                {
                    Control.Layer.CornerRadius = 5;
                    Control.Layer.ShadowColor = UIColor.Black.CGColor;
                    Control.Layer.ShadowOffset = new CGSize(5, 5);
                    Control.Layer.ShadowOpacity = 1.0f;
                }
                else if (!((Label)Element).OnThisPlatform().IsShadowed())
                {
                    Control.Layer.ShadowOpacity = 0;
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

O `UpdateShadow` método define `Control.Layer` as propriedades para criar a sombra, desde que o `IsShadowed` propriedade anexada é definida como `true`e desde que o `Shadow` específicos da plataforma foi invocado na mesma plataforma que o Efeito é implementado para. Essa verificação é realizada com o `OnThisPlatform` método.

Se o `Shadow.IsShadowed` anexado alterações de valor de propriedade em tempo de execução, o efeito precisa responder, removendo a sombra. Portanto, uma versão de substituição de `OnElementPropertyChanged` método é usado para responder à alteração de propriedade associável chamando o `UpdateShadow` método.

Para obter mais informações sobre como criar um efeito, consulte [criando um efeito](~/xamarin-forms/app-fundamentals/effects/creating.md) e [passando os parâmetros de efeito como propriedades anexadas](~/xamarin-forms/app-fundamentals/effects/passing-parameters/attached-properties.md).

## <a name="consuming-a-platform-specific"></a>Consumindo um específico da plataforma

O `Shadow` específico da plataforma é consumido em XAML, definindo o `Shadow.IsShadowed` anexado à propriedade um `boolean` valor:

```xaml
<ContentPage xmlns:ios="clr-namespace:MyCompany.Forms.PlatformConfiguration.iOS" ...>
  ...
  <Label Text="Label Shadow Effect" ios:Shadow.IsShadowed="true" ... />
  ...
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using MyCompany.Forms.PlatformConfiguration.iOS;

...

shadowLabel.On<iOS>().SetIsShadowed(true);
```

Para obter mais informações sobre como consumir especificidades da plataforma, consulte [consumindo-especificidades da plataforma](~/xamarin-forms/platform/platform-specifics/consuming/index.md).

## <a name="summary"></a>Resumo

Este artigo demonstrou como expor um efeito por meio de uma plataforma específica. O resultado é um efeito que pode ser mais facilmente consumido por meio de XAML e um API fluente de código.


## <a name="related-links"></a>Links relacionados

- [ShadowPlatformSpecific (amostra)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/shadowplatformspecific/)
- [Personalizar controles com efeitos](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md)
