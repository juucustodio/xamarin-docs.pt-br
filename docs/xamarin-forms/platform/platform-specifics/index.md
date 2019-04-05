---
title: Especificidades da plataforma
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como consumir e criar as especificidades da plataforma.
ms.prod: xamarin
ms.assetid: 4729DB9C-8800-4E29-9D66-3BE13C5F8C94
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/01/2018
ms.openlocfilehash: 69c730462d6b277f8ec5a4eb7390aaeb50575a39
ms.sourcegitcommit: a7170494e1975f0f1be547a45444752fd8e57819
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58507130"
---
# <a name="platform-specifics"></a>Especificidades da plataforma

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

_Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos._

O processo para o consumo de um específico da plataforma por meio de XAML, ou por meio do código API fluente é da seguinte maneira:

1. Adicionar um `xmlns` declaração ou `using` diretiva para o [ `Xamarin.Forms.PlatformConfiguration` ](xref:Xamarin.Forms.PlatformConfiguration) namespace.
1. Adicionar um `xmlns` declaração ou `using` diretiva para o namespace que contém a funcionalidade específica da plataforma:
    1. No iOS, essa é a [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace.
    1. No Android, essa é a [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace. Para Android AppCompat, esse é o [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) namespace.
    1. Na plataforma Universal do Windows, isso é o [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace.
1. Aplicar o específicos da plataforma de XAML ou de código com o `On<T>` API fluente. O valor de `T` pode ser o [ `iOS` ](xref:Xamarin.Forms.PlatformConfiguration.iOS), [ `Android` ](xref:Xamarin.Forms.PlatformConfiguration.Android), ou [ `Windows` ](xref:Xamarin.Forms.PlatformConfiguration.Windows) tipos do [ `Xamarin.Forms.PlatformConfiguration` ](xref:Xamarin.Forms.PlatformConfiguration) namespace.

> [!NOTE]
> Observe que a tentativa de consumir um específico da plataforma em uma plataforma em que ele não está disponível não resultará em erro. Em vez disso, o código será executado sem o específicos da plataforma que está sendo aplicado.

Especificidades da plataforma consumidos por meio de `On<T>` retorno da API de código fluente [ `IPlatformElementConfiguration` ](xref:Xamarin.Forms.IPlatformElementConfiguration`2) objetos. Isso permite que várias especificações de plataforma a ser invocado no mesmo objeto com método em cascata.

Para obter mais informações sobre as especificidades de plataforma fornecido pelo xamarin. Forms, consulte [especificidades da plataforma do iOS](~/xamarin-forms/platform/ios/index.md), [especificidades da plataforma Android](~/xamarin-forms/platform/android/index.md), e [Windows-especificidades da plataforma](~/xamarin-forms/platform/windows/index.md).

## <a name="creating-platform-specifics"></a>Criando as especificidades da plataforma

Fornecedores podem criar seus próprios itens específicos à plataforma com efeitos. Um efeito fornece a funcionalidade específica, que, em seguida, é exposta por meio de uma plataforma específica. O resultado é um efeito que pode ser mais facilmente consumido por meio de XAML e um API fluente de código.

O processo para a criação de uma plataforma específica é da seguinte maneira:

1. Implemente a funcionalidade específica como um efeito. Para obter mais informações, consulte [criando um efeito](~/xamarin-forms/app-fundamentals/effects/creating.md).
1. Crie uma classe específica da plataforma que irá expor o efeito. Para obter mais informações, consulte [criando uma classe específica da plataforma](#creating-a-platform-specific-class).
1. Na classe específica da plataforma, implemente uma propriedade anexada para permitir que o específicos da plataforma ser consumida por meio de XAML. Para obter mais informações, consulte [adicionando uma propriedade anexada](#adding-an-attached-property).
1. Na classe específica da plataforma, implemente métodos de extensão para permitir que o específicos da plataforma ser consumida por meio de um API fluente de código. Para obter mais informações, consulte [adicionando métodos de extensão](#adding-extension-methods).
1. Modifique a implementação em vigor para que o efeito é aplicado somente se a plataforma específica foi invocada na mesma plataforma como o efeito. Para obter mais informações, consulte [criando o efeito de](#creating-the-effect).

O resultado de expor um efeito como uma plataforma específica é que o efeito pode ser consumido com mais facilidade por meio de XAML e um API fluente de código.

> [!NOTE]
> É previu que fornecedores usará essa técnica para criar seus próprios-especificidades da plataforma, para facilitar o consumo por usuários. Embora os usuários podem optar por criar seus próprios especificidades da plataforma, deve-se observar que ela requer mais código que criar e consumir um efeito.

O [aplicativo de exemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/shadowplatformspecific/) demonstra uma `Shadow` específicos da plataforma que adiciona uma sombra para o texto exibido por um [ `Label` ](xref:Xamarin.Forms.Label) controle:

![](images/screenshots.png "Específico da plataforma de sombra")

O [aplicativo de exemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/shadowplatformspecific/) implementa o `Shadow` específico da plataforma em cada plataforma, para facilitar a compreensão. No entanto, além de cada implementação do efeito específico da plataforma, a implementação da classe sombra é praticamente idêntica para cada plataforma. Portanto, este guia concentra-se na implementação da classe de sombra e efeito associado em uma única plataforma.

Para obter mais informações sobre os efeitos, consulte [personalizar controles com efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).

### <a name="creating-a-platform-specific-class"></a>Criando uma classe específica da plataforma

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

#### <a name="adding-an-attached-property"></a>Adicionando uma propriedade anexada

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

#### <a name="adding-extension-methods"></a>Adicionando métodos de extensão

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

#### <a name="creating-the-effect"></a>Criando o efeito

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

### <a name="consuming-the-platform-specific"></a>Consumindo o específicos da plataforma

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

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [ShadowPlatformSpecific (amostra)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/shadowplatformspecific/)
- [Especificidades da plataforma do iOS](~/xamarin-forms/platform/ios/index.md)
- [Especificidades da plataforma Android](~/xamarin-forms/platform/android/index.md)
- [Especificidades da plataforma Windows](~/xamarin-forms/platform/windows/index.md)
- [Personalizar controles com efeitos](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md)
- [PlatformConfiguration API](xref:Xamarin.Forms.PlatformConfiguration)
