---
title: Criando específicos de plataforma
description: Fornecedores podem criar suas próprias especificações de plataforma com efeitos. Um efeito fornece a funcionalidade específica, em seguida, é exposta por meio de uma plataforma específica. O resultado é um efeito que pode ser mais facilmente consumido por meio de XAML e uma API fluente de código. Este artigo demonstra como expor um efeito por meio de uma plataforma específica.
ms.prod: xamarin
ms.assetid: 0D0E6274-6EF2-4D40-BB77-3D8E53BCD24B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/23/2016
ms.openlocfilehash: dcd22dd0d4e281245a1f6598d9a58d24a97b1f20
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848142"
---
# <a name="creating-platform-specifics"></a>Criando específicos de plataforma

_Fornecedores podem criar suas próprias especificações de plataforma com efeitos. Um efeito fornece a funcionalidade específica, em seguida, é exposta por meio de uma plataforma específica. O resultado é um efeito que pode ser mais facilmente consumido por meio de XAML e uma API fluente de código. Este artigo demonstra como expor um efeito por meio de uma plataforma específica._

## <a name="overview"></a>Visão geral

O processo de criação de uma plataforma específica é o seguinte:

1. Implemente a funcionalidade específica como um efeito. Para obter mais informações, consulte [criando um efeito](~/xamarin-forms/app-fundamentals/effects/creating.md).
1. Crie uma classe específica de plataforma que irá expor o efeito. Para obter mais informações, consulte [criando uma classe específica de plataforma](#creating).
1. Na classe específica da plataforma, implemente uma propriedade anexada para permitir que o específico da plataforma ser consumida por meio de XAML. Para obter mais informações, consulte [adicionando uma propriedade anexada](#attached_property).
1. Na classe específica da plataforma, implemente métodos de extensão para permitir que o específico da plataforma ser consumida por meio de uma API fluente de código. Para obter mais informações, consulte [adicionando métodos de extensão](#extension_methods).
1. Modifique a implementação do efeito de forma que o efeito será aplicado somente se a plataforma específica foi chamada na mesma plataforma como o efeito. Para obter mais informações, consulte [criando o efeito de](#creating_the_effect).

O resultado de expor um efeito como uma plataforma específica é que o efeito pode ser consumido mais facilmente por meio de XAML e uma API fluente de código.

> [!NOTE]
> É envisaged que fornecedores usará essa técnica para criar suas próprias especificações-plataforma, para facilitar o consumo por usuários. Enquanto os usuários podem optar por criar seus próprios específicos de plataforma, deve-se observar que requer mais código que a criação e consumo de um efeito.

O aplicativo de exemplo demonstra um `Shadow` específico da plataforma que adiciona uma sombra para o texto exibido por um [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) controle:

![](creating-images/screenshots.png "Específico da plataforma de sombra")

Implementados pelo aplicativo de exemplo do `Shadow` específico da plataforma em cada plataforma, para facilitar a compreensão. No entanto, além de cada implementação específica de plataforma do efeito, a implementação da classe de sombra é praticamente idêntica para cada plataforma. Portanto, este guia aborda a implementação da classe de sombra e efeito associado em uma única plataforma.

Para obter mais informações sobre os efeitos, consulte [Personalizando controles com efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).

<a name="creating" />

## <a name="creating-a-platform-specific-class"></a>Criando uma classe específica de plataforma

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

As seções a seguir abordam a implementação do `Shadow` efeito específico da plataforma e associado.

<a name="attached_property" />

### <a name="adding-an-attached-property"></a>Adicionando uma propriedade anexada

Uma propriedade anexada deve ser adicionada para o `Shadow` específico da plataforma para permitir o consumo por meio de XAML:

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

O `IsShadowed` propriedade anexada é usada para adicionar o `MyCompany.LabelShadowEffect` em vigor e removê-lo do controle que o `Shadow` classe está associada. Isso anexado propriedade registra o `OnIsShadowedPropertyChanged` método que será executado quando o valor da propriedade é alterado. Por sua vez, este método chama o `AttachEffect` ou `DetachEffect` método para adicionar ou remover o efeito com base no valor da `IsShadowed` propriedade anexada. O efeito é adicionado ou removido do controle modificando o controle [ `Effects` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Effects/) coleção.

> [!NOTE]
> Observe que o efeito é resolvido, especificando um valor que é uma concatenação do nome do grupo de resolução e identificador exclusivo que é especificado na implementação do efeito. Para obter mais informações, consulte [criando um efeito](~/xamarin-forms/app-fundamentals/effects/creating.md).

Para obter mais informações sobre propriedades anexadas, consulte [propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md).

<a name="extension_methods" />

### <a name="adding-extension-methods"></a>Adicionando métodos de extensão

Métodos de extensão devem ser adicionados para o `Shadow` específico da plataforma para permitir o consumo por meio de uma API fluente de código:

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

O `IsShadowed` e `SetIsShadowed` métodos de extensão invocar get e definir acessadores para o `IsShadowed` anexado a propriedade, respectivamente. Cada método de extensão funciona com o `IPlatformElementConfiguration<iOS, FormsElement>` tipo, que especifica que a plataforma específica pode ser chamada em [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) instâncias do iOS.

<a name="creating_the_effect" />

### <a name="creating-the-effect"></a>Criando o efeito

O `Shadow` específico da plataforma adiciona o `MyCompany.LabelShadowEffect` para um [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)e remove-o. O seguinte exemplo de código mostra o `LabelShadowEffect` implementação para o projeto iOS:

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

O `UpdateShadow` método conjuntos `Control.Layer` propriedades para criar a sombra, contanto que o `IsShadowed` propriedade anexada é definida como `true`e desde que o `Shadow` específico da plataforma foi chamado na mesma plataforma que o Efeito é implementado para. Essa verificação é executada com o `OnThisPlatform` método.

Se o `Shadow.IsShadowed` anexado alterações de valor de propriedade em tempo de execução, o efeito precisa responder, removendo a sombra. Portanto, uma versão de substituição de `OnElementPropertyChanged` método é usado para responder à alteração de propriedade associável chamando o `UpdateShadow` método.

Para obter mais informações sobre como criar um efeito, consulte [criando um efeito](~/xamarin-forms/app-fundamentals/effects/creating.md) e [passando parâmetros de efeito como propriedades anexadas](~/xamarin-forms/app-fundamentals/effects/passing-parameters/attached-properties.md).

## <a name="consuming-a-platform-specific"></a>Consumindo uma plataforma específica

O `Shadow` específica de plataforma é consumida em XAML, definindo o `Shadow.IsShadowed` anexado a propriedade para um `boolean` valor:

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

Para obter mais informações sobre consumo específicos de plataforma, consulte [específicos de plataforma consumindo](~/xamarin-forms/platform/platform-specifics/consuming/index.md).

## <a name="summary"></a>Resumo

Este artigo demonstrou como expor um efeito por meio de uma plataforma específica. O resultado é um efeito que pode ser mais facilmente consumido por meio de XAML e uma API fluente de código.


## <a name="related-links"></a>Links relacionados

- [ShadowPlatformSpecific (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/shadowplatformspecific/)
- [Personalização de controles com efeitos](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md)
