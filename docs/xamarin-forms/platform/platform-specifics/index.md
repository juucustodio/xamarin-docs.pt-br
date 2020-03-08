---
title: Elementos específicos à plataforma
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como consumir e criar especificações específicas da plataforma.
ms.prod: xamarin
ms.assetid: 4729DB9C-8800-4E29-9D66-3BE13C5F8C94
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/01/2018
ms.openlocfilehash: f6190b9c0d29d57d6d509bdff25e2ce3572e3a3c
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78910549"
---
# <a name="platform-specifics"></a>Elementos específicos à plataforma

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

_As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados._

O processo para o consumo de um específico da plataforma por meio de XAML, ou por meio do código API fluente é da seguinte maneira:

1. Adicione uma declaração de `xmlns` ou `using` diretiva para o namespace [`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration) .
1. Adicione uma declaração de `xmlns` ou `using` diretiva para o namespace que contém a funcionalidade específica da plataforma:
    1. No iOS, esse é o namespace [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) .
    1. No Android, esse é o namespace [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) . Para o Android AppCompat, esse é o namespace [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) .
    1. Na Plataforma Universal do Windows, esse é o namespace [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) .
1. Aplique a plataforma específica do XAML ou do código com a API Fluent `On<T>`. O valor de `T` pode ser os tipos [`iOS`](xref:Xamarin.Forms.PlatformConfiguration.iOS), [`Android`](xref:Xamarin.Forms.PlatformConfiguration.Android)ou [`Windows`](xref:Xamarin.Forms.PlatformConfiguration.Windows) do namespace [`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration) .

> [!NOTE]
> Observe que a tentativa de consumir um específico da plataforma em uma plataforma em que ele não está disponível não resultará em erro. Em vez disso, o código será executado sem o específicos da plataforma que está sendo aplicado.

Especificações de plataforma consumidas por meio do `On<T>` API de código Fluent [`IPlatformElementConfiguration`](xref:Xamarin.Forms.IPlatformElementConfiguration`2) objetos de retorno. Isso permite que várias especificações de plataforma a ser invocado no mesmo objeto com método em cascata.

Para obter mais informações sobre as especificações de plataforma fornecidas pelo Xamarin. Forms, consulte [especificações da plataforma iOS](~/xamarin-forms/platform/ios/index.md), [especificações da plataforma Android](~/xamarin-forms/platform/android/index.md)e [especificações da plataforma Windows](~/xamarin-forms/platform/windows/index.md).

## <a name="creating-platform-specifics"></a>Criando especificações específicas da plataforma

Os fornecedores podem criar suas próprias especificações de plataforma com efeitos. Um efeito fornece a funcionalidade específica, que, em seguida, é exposta por meio de uma plataforma específica. O resultado é um efeito que pode ser mais facilmente consumido por meio de XAML e um API fluente de código.

O processo para a criação de uma plataforma específica é da seguinte maneira:

1. Implemente a funcionalidade específica como um efeito. Para obter mais informações, consulte [criando um efeito](~/xamarin-forms/app-fundamentals/effects/creating.md).
1. Crie uma classe específica da plataforma que irá expor o efeito. Para obter mais informações, consulte [criando uma classe específica da plataforma](#creating-a-platform-specific-class).
1. Na classe específica da plataforma, implemente uma propriedade anexada para permitir que o específicos da plataforma ser consumida por meio de XAML. Para obter mais informações, consulte [adicionando uma propriedade anexada](#adding-an-attached-property).
1. Na classe específica da plataforma, implemente métodos de extensão para permitir que o específicos da plataforma ser consumida por meio de um API fluente de código. Para obter mais informações, consulte [adicionando métodos de extensão](#adding-extension-methods).
1. Modifique a implementação em vigor para que o efeito é aplicado somente se a plataforma específica foi invocada na mesma plataforma como o efeito. Para obter mais informações, consulte [criando o efeito](#creating-the-effect).

O resultado de expor um efeito como uma plataforma específica é que o efeito pode ser consumido com mais facilidade por meio de XAML e um API fluente de código.

> [!NOTE]
> É previu que fornecedores usará essa técnica para criar seus próprios-especificidades da plataforma, para facilitar o consumo por usuários. Embora os usuários podem optar por criar seus próprios especificidades da plataforma, deve-se observar que ela requer mais código que criar e consumir um efeito.

O [aplicativo de exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific) demonstra um `Shadow` específico da plataforma que adiciona uma sombra ao texto exibido por um controle de [`Label`](xref:Xamarin.Forms.Label) :

![](images/screenshots.png "Shadow Platform-Specific")

O [aplicativo de exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific) implementa a `Shadow` específica da plataforma em cada plataforma, para facilitar a compreensão. No entanto, além de cada implementação do efeito específico da plataforma, a implementação da classe sombra é praticamente idêntica para cada plataforma. Portanto, este guia concentra-se na implementação da classe de sombra e efeito associado em uma única plataforma.

Para obter mais informações sobre efeitos, consulte [Personalizando controles com efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).

### <a name="creating-a-platform-specific-class"></a>Criando uma classe específica da plataforma

Uma plataforma específica é criada como uma classe de `public static`:

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
  public static Shadow
  {
    ...
  }
}
```

As seções a seguir discutem a implementação do `Shadow` efeito associado e específico da plataforma.

#### <a name="adding-an-attached-property"></a>Adicionando uma propriedade anexada

Uma propriedade anexada deve ser adicionada à `Shadow` específica da plataforma para permitir o consumo por meio de XAML:

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

A propriedade anexada `IsShadowed` é usada para adicionar o efeito de `MyCompany.LabelShadowEffect` ao e removê-lo do, o controle ao qual a classe `Shadow` está anexada. Essa propriedade anexada registra o método `OnIsShadowedPropertyChanged` que será executado quando o valor da propriedade for alterado. Por sua vez, esse método chama o `AttachEffect` ou o método `DetachEffect` para adicionar ou remover o efeito com base no valor da propriedade `IsShadowed` anexada. O efeito é adicionado ou removido do controle modificando a coleção de [`Effects`](xref:Xamarin.Forms.Element.Effects) do controle.

> [!NOTE]
> Observe que o efeito é resolvido, especificando um valor que é uma concatenação do nome do grupo de resolução e identificador exclusivo que é especificado na implementação do efeito. Para obter mais informações, consulte [criando um efeito](~/xamarin-forms/app-fundamentals/effects/creating.md).

Para obter mais informações sobre as propriedades anexadas, confira [Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md).

#### <a name="adding-extension-methods"></a>Adicionando métodos de extensão

Os métodos de extensão devem ser adicionados à `Shadow` específica da plataforma para permitir o consumo por meio de uma API de código fluente:

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

Os métodos de extensão `IsShadowed` e `SetIsShadowed` invocam os acessadores get e Set para a propriedade `IsShadowed` anexada, respectivamente. Cada método de extensão opera no tipo de `IPlatformElementConfiguration<iOS, FormsElement>`, que especifica que a plataforma específica pode ser invocada em instâncias de [`Label`](xref:Xamarin.Forms.Label) do Ios.

#### <a name="creating-the-effect"></a>Criando o efeito

A `Shadow` específica da plataforma adiciona a `MyCompany.LabelShadowEffect` a uma [`Label`](xref:Xamarin.Forms.Label)e a remove. O exemplo de código a seguir mostra a implementação de `LabelShadowEffect` para o projeto do iOS:

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

O método `UpdateShadow` define `Control.Layer` Propriedades para criar a sombra, desde que a propriedade `IsShadowed` anexada esteja definida como `true`e desde que a `Shadow` específica da plataforma tenha sido invocada na mesma plataforma para a qual o efeito é implementado. Essa verificação é executada com o método `OnThisPlatform`.

Se o valor da propriedade anexada `Shadow.IsShadowed` for alterado em tempo de execução, o efeito precisará responder removendo a sombra. Portanto, uma versão substituída do método `OnElementPropertyChanged` é usada para responder à alteração da propriedade vinculável chamando o método `UpdateShadow`.

Para obter mais informações sobre como criar um efeito, consulte [criando um efeito](~/xamarin-forms/app-fundamentals/effects/creating.md) e [passando parâmetros de efeito como propriedades anexadas](~/xamarin-forms/app-fundamentals/effects/passing-parameters/attached-properties.md).

### <a name="consuming-the-platform-specific"></a>Consumindo a plataforma específica

A `Shadow` específica da plataforma é consumida em XAML definindo a propriedade `Shadow.IsShadowed` anexada como um valor `boolean`:

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

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [ShadowPlatformSpecific (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific)
- [Especificações da plataforma iOS](~/xamarin-forms/platform/ios/index.md)
- [Especificações da plataforma Android](~/xamarin-forms/platform/android/index.md)
- [Especificações da plataforma Windows](~/xamarin-forms/platform/windows/index.md)
- [Personalizando controles com efeitos](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md)
- [API PlatformConfiguration](xref:Xamarin.Forms.PlatformConfiguration)
