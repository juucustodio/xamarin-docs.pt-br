---
title: Elementos específicos à plataforma
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir e criar especificações específicas da plataforma.
ms.prod: xamarin
ms.assetid: 4729DB9C-8800-4E29-9D66-3BE13C5F8C94
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/01/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e0c6e305287c97abac3500cba4771479b60381ac
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939796"
---
# <a name="platform-specifics"></a>Elementos específicos à plataforma

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

_As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados._

O processo para consumir uma plataforma específica por meio de XAML ou por meio da API de código Fluent é o seguinte:

1. Adicione uma `xmlns` declaração ou `using` diretiva para o [`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration) namespace.
1. Adicione uma `xmlns` declaração ou `using` diretiva para o namespace que contém a funcionalidade específica da plataforma:
    1. No iOS, esse é o [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace.
    1. No Android, esse é o [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace. Para o Android AppCompat, este é o [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) namespace.
    1. Na Plataforma Universal do Windows, esse é o [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace.
1. Aplique a plataforma específica do XAML ou do código com a `On<T>` API Fluent. O valor de `T` pode ser os [`iOS`](xref:Xamarin.Forms.PlatformConfiguration.iOS) [`Android`](xref:Xamarin.Forms.PlatformConfiguration.Android) tipos, ou [`Windows`](xref:Xamarin.Forms.PlatformConfiguration.Windows) do [`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration) namespace.

> [!NOTE]
> Observe que a tentativa de consumir uma plataforma específica em uma plataforma em que ela está indisponível não resultará em um erro. Em vez disso, o código será executado sem a aplicação específica da plataforma.

Especificações de plataforma consumidas por meio dos `On<T>` objetos de retorno da API de código Fluent [`IPlatformElementConfiguration`](xref:Xamarin.Forms.IPlatformElementConfiguration`2) . Isso permite que várias especificações específicas da plataforma sejam invocadas no mesmo objeto com o método em cascata.

Para obter mais informações sobre as especificações de plataforma fornecidas pelo Xamarin.Forms , consulte [especificações da plataforma iOS](~/xamarin-forms/platform/ios/index.md), [especificações da plataforma Android](~/xamarin-forms/platform/android/index.md)e [especificações da plataforma Windows](~/xamarin-forms/platform/windows/index.md).

## <a name="creating-platform-specifics"></a>Criando especificações específicas da plataforma

Os fornecedores podem criar suas próprias especificações de plataforma com efeitos. Um efeito fornece a funcionalidade específica, que é exposta por meio de uma plataforma específica. O resultado é um efeito que pode ser consumido com mais facilidade por meio de XAML e por meio de uma API de código fluente.

O processo de criação de uma plataforma específica é o seguinte:

1. Implemente a funcionalidade específica como um efeito. Para obter mais informações, consulte [criando um efeito](~/xamarin-forms/app-fundamentals/effects/creating.md).
1. Crie uma classe específica da plataforma que exporá o efeito. Para obter mais informações, consulte [criando uma classe específica da plataforma](#creating-a-platform-specific-class).
1. Na classe específica da plataforma, implemente uma propriedade anexada para permitir que a plataforma específica seja consumida por meio de XAML. Para obter mais informações, consulte [adicionando uma propriedade anexada](#adding-an-attached-property).
1. Na classe específica da plataforma, implemente métodos de extensão para permitir que a plataforma específica seja consumida por meio de uma API de código fluente. Para obter mais informações, consulte [adicionando métodos de extensão](#adding-extension-methods).
1. Modifique a implementação do efeito para que o efeito seja aplicado somente se a plataforma específica tiver sido invocada na mesma plataforma que o efeito. Para obter mais informações, consulte [criando o efeito](#creating-the-effect).

O resultado da exposição de um efeito como uma plataforma específica é que o efeito pode ser consumido com mais facilidade por meio de XAML e por meio de uma API de código fluente.

> [!NOTE]
> É previu que os fornecedores usarão essa técnica para criar suas próprias especificações de plataforma, para facilitar o consumo pelos usuários. Embora os usuários possam optar por criar suas próprias especificações específicas da plataforma, deve-se observar que ele requer mais código do que criar e consumir um efeito.

O [aplicativo de exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific) demonstra uma `Shadow` plataforma específica que adiciona uma sombra ao texto exibido por um [`Label`](xref:Xamarin.Forms.Label) controle:

![Específico da plataforma de sombra](images/screenshots.png)

O [aplicativo de exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific) implementa a `Shadow` plataforma específica em cada plataforma, para facilitar a compreensão. No entanto, além de cada implementação de efeito específica da plataforma, a implementação da classe Shadow é amplamente idêntica para cada plataforma. Portanto, este guia aborda na implementação da classe Shadow e o efeito associado em uma única plataforma.

Para obter mais informações sobre efeitos, consulte [Personalizando controles com efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).

### <a name="creating-a-platform-specific-class"></a>Criando uma classe específica da plataforma

Uma plataforma específica é criada como uma `public static` classe:

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
  public static Shadow
  {
    ...
  }
}
```

As seções a seguir discutem a implementação do `Shadow` efeito associado e específico à plataforma.

#### <a name="adding-an-attached-property"></a>Adicionando uma propriedade anexada

Uma propriedade anexada deve ser adicionada à `Shadow` plataforma específica para permitir o consumo por meio de XAML:

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

A `IsShadowed` Propriedade anexada é usada para adicionar o `MyCompany.LabelShadowEffect` efeito a e removê-lo de, o controle ao qual a `Shadow` classe está anexada. Essa propriedade anexada registra o método `OnIsShadowedPropertyChanged` que será executado quando o valor da propriedade for alterado. Por sua vez, esse método chama `AttachEffect` o `DetachEffect` método ou para adicionar ou remover o efeito com base no valor da `IsShadowed` Propriedade anexada. O efeito é adicionado ou removido do controle modificando a coleção do controle [`Effects`](xref:Xamarin.Forms.Element.Effects) .

> [!NOTE]
> Observe que o efeito é resolvido especificando um valor que é uma concatenação do nome do grupo de resolução e do identificador exclusivo especificado na implementação do efeito. Para obter mais informações, consulte [criando um efeito](~/xamarin-forms/app-fundamentals/effects/creating.md).

Para obter mais informações sobre as propriedades anexadas, confira [Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md).

#### <a name="adding-extension-methods"></a>Adicionando métodos de extensão

Os métodos de extensão devem ser adicionados à `Shadow` plataforma específica para permitir o consumo por meio de uma API de código fluente:

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

Os `IsShadowed` `SetIsShadowed` métodos de extensão e invocam os acessadores get e Set para a `IsShadowed` Propriedade anexada, respectivamente. Cada método de extensão opera no `IPlatformElementConfiguration<iOS, FormsElement>` tipo, que especifica que o específico da plataforma pode ser invocado em [`Label`](xref:Xamarin.Forms.Label) instâncias do Ios.

#### <a name="creating-the-effect"></a>Criando o efeito

A `Shadow` plataforma específica adiciona o `MyCompany.LabelShadowEffect` a a [`Label`](xref:Xamarin.Forms.Label) e remove-o. O exemplo de código a seguir mostra a implementação de `LabelShadowEffect` para o projeto do iOS:

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

O `UpdateShadow` método define `Control.Layer` Propriedades para criar a sombra, desde que a `IsShadowed` Propriedade anexada esteja definida como `true` e desde que a `Shadow` plataforma específica tenha sido invocada na mesma plataforma para a qual o efeito é implementado. Essa verificação é executada com o `OnThisPlatform` método.

Se o `Shadow.IsShadowed` valor da propriedade anexada for alterado em tempo de execução, o efeito precisará responder removendo a sombra. Portanto, uma versão substituída do `OnElementPropertyChanged` método é usada para responder à alteração da propriedade vinculável chamando o `UpdateShadow` método.

Para obter mais informações sobre como criar um efeito, consulte [criando um efeito](~/xamarin-forms/app-fundamentals/effects/creating.md) e [passando parâmetros de efeito como propriedades anexadas](~/xamarin-forms/app-fundamentals/effects/passing-parameters/attached-properties.md).

### <a name="consuming-the-platform-specific"></a>Consumindo a plataforma específica

A `Shadow` plataforma específica é consumida em XAML definindo a `Shadow.IsShadowed` Propriedade anexada como um `boolean` valor:

```xaml
<ContentPage xmlns:ios="clr-namespace:MyCompany.Forms.PlatformConfiguration.iOS" ...>
  ...
  <Label Text="Label Shadow Effect" ios:Shadow.IsShadowed="true" ... />
  ...
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using MyCompany.Forms.PlatformConfiguration.iOS;

...

shadowLabel.On<iOS>().SetIsShadowed(true);
```

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [ShadowPlatformSpecific (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific)
- [Elementos específicos à plataforma iOS](~/xamarin-forms/platform/ios/index.md)
- [Elementos específicos à plataforma Android](~/xamarin-forms/platform/android/index.md)
- [Especificações da plataforma Windows](~/xamarin-forms/platform/windows/index.md)
- [Personalizando controles com efeitos](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Propriedades Anexadas](~/xamarin-forms/xaml/attached-properties.md)
- [API PlatformConfiguration](xref:Xamarin.Forms.PlatformConfiguration)
