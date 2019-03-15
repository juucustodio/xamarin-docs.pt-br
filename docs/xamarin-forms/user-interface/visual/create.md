---
title: Criar um renderizador Visual do xamarin. Forms
description: Visual do xamarin. Forms permite que os renderizadores ser aplicadas seletivamente a objetos VisualElement, sem ter de exibições do xamarin. Forms subclasse.
ms.prod: xamarin
ms.assetid: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2019
ms.openlocfilehash: 1bd56d09932c97508dd0a05fbc0eb2bad3af3f0e
ms.sourcegitcommit: 97dca3face7c4ad5555dfaca88f5b45a70ca556d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2019
ms.locfileid: "57972579"
---
# <a name="create-a-xamarinforms-visual-renderer"></a>Criar um renderizador Visual do xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VisualDemos/)

Visual do xamarin. Forms permite renderizadores seja criada e aplicada seletivamente [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) objetos, sem ter de exibições do xamarin. Forms subclasse. Um renderizador que especifica um `IVisual` tipo, como parte da sua `ExportRendererAttribute`, será usado para renderizar aceitado modos de exibição, em vez do renderizador padrão. Em tempo de seleção do renderizador, o `Visual` propriedade do modo de exibição é inspecionada e incluída no processo de seleção do renderizador.

> [!IMPORTANT]
> Atualmente, o [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) propriedade não pode ser alterada depois que o modo de exibição foi renderizado, mas isso será alterado em uma versão futura.

O processo para criar e consumir um renderizador Visual do xamarin. Forms é:

1. Crie renderizadores de plataforma para a exibição necessária. Para obter mais informações, consulte [criar renderizadores](#create-platfomr-renderers).
1. Criar um tipo que deriva de `IVisual`. Para obter mais informações, consulte [criar um tipo IVisual](#create-an-ivisual-type).
1. Registre-se a `IVisual` tipo como parte do `ExportRendererAttribute` que decora os renderizadores. Para obter mais informações, consulte [registrar o tipo de IVisual](#register-the-ivisual-type).
1. Consumir o renderizador Visual, definindo o [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) propriedade no modo de exibição para o `IVisual` nome. Para obter mais informações, consulte [consumir o renderizador Visual](#consume-the-visual-renderer).
1. [opcional] Registrar um nome para o `IVisual` tipo. Para obter mais informações, consulte [registrar um nome para o tipo de IVisual](#register-a-name-for-the-ivisual-type).

## <a name="create-platform-renderers"></a>Criar renderizadores de plataforma

Para obter informações sobre como criar uma classe de renderizador, consulte [renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). No entanto, observe que um renderizador Visual do xamarin. Forms é aplicado a um modo de exibição sem precisar subclasse o modo de exibição.

As classes de renderizador descritas aqui implementam um personalizado [ `Button` ](xref:Xamarin.Forms.Button) que exibe seu texto com uma sombra.

### <a name="ios"></a>iOS

O exemplo de código a seguir mostra o renderizador do botão para iOS:

```csharp
public class CustomButtonRenderer : ButtonRenderer
{
    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        base.OnElementChanged(e);

        if (e.OldElement != null)
        {
            // Cleanup
        }

        if (e.NewElement != null)
        {
            Control.TitleShadowOffset = new CoreGraphics.CGSize(1, 1);
            Control.SetTitleShadowColor(Color.Black.ToUIColor(), UIKit.UIControlState.Normal);
        }
    }
}
```

### <a name="android"></a>Android

O exemplo de código a seguir mostra o renderizador do botão para Android:

```csharp
public class CustomButtonRenderer : Xamarin.Forms.Platform.Android.AppCompat.ButtonRenderer
{
    public CustomButtonRenderer(Context context) : base(context)
    {
    }

    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        base.OnElementChanged(e);

        if (e.OldElement != null)
        {
            // Cleanup
        }

        if (e.NewElement != null)
        {
            Control.SetShadowLayer(5, 3, 3, Color.Black.ToAndroid());
        }
    }
}
```

## <a name="create-an-ivisual-type"></a>Criar um tipo de IVisual

Em sua biblioteca de plataforma cruzada, criar um tipo que deriva de `IVisual`:

```csharp
public class CustomVisual : IVisual
{
}
```

O `CustomVisual` tipo, em seguida, pode ser registrado em classes de renderizador, permitindo [ `Button` ](xref:Xamarin.Forms.Button) objetos a serem optarem por usar os renderizadores.

## <a name="register-the-ivisual-type"></a>Registrar o tipo de IVisual

Em projetos da plataforma, Decore as classes de renderizador com o `ExportRendererAttribute`:

```csharp
[assembly: ExportRenderer(typeof(Xamarin.Forms.Button), typeof(CustomButtonRenderer), new[] { typeof(CustomVisual) })]
public class CustomButtonRenderer : ButtonRenderer
{
    protected override void OnElementChanged(ElementChangedEventArgs<Button> e)
    {
        ...
    }
}
```

Neste exemplo, o `ExportRendererAttribute` Especifica que o `CustomButtonRenderer` classe será usada para renderizar consumindo [ `Button` ](xref:Xamarin.Forms.Button) objetos, com o `IVisual` tipo registrado como o terceiro argumento. Um renderizador que especifica um `IVisual` tipo, como parte da sua `ExportRendererAttribute`, será usado para renderizar aceitado modos de exibição, em vez do renderizador padrão.

## <a name="consume-the-visual-renderer"></a>Consumir o renderizador Visual

Um [ `Button` ](xref:Xamarin.Forms.Button) pode aceitar o objeto usando as classes de renderizador definindo seu [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) propriedade `Custom`:

```xaml
<Button Visual="Custom"
        Text="CUSTOM BUTTON"
        BackgroundColor="{StaticResource PrimaryColor}"
        TextColor="{StaticResource SecondaryTextColor}"
        HorizontalOptions="FillAndExpand" />
```

> [!NOTE]
> No XAML, um conversor de tipo remove a necessidade de incluir o sufixo "Visual" na [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) valor da propriedade. No entanto, o nome de tipo completo também pode ser especificado.

O código c# equivalente é:

```csharp
Button button = new Button { Text = "CUSTOM BUTTON", ... };
button.Visual = new CustomVisual();
```

Em tempo de seleção do renderizador, o [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) propriedade o [ `Button` ](xref:Xamarin.Forms.Button) é inspecionado e incluídos no processo de seleção do renderizador. Se um renderizador não for localizado, será usado o renderizador padrão do xamarin. Forms.

As capturas de tela a seguir mostram o renderizado [ `Button` ](xref:Xamarin.Forms.Button), que exibe seu texto com uma sombra:

[![Captura de tela do botão personalizado com o texto de sombra, no iOS e Android](material-visual-images/custom-button.png "botão com texto de sombra")](material-visual-images/custom-button-large.png#lightbox)

## <a name="register-a-name-for-the-ivisual-type"></a>Registrar um nome para o tipo de IVisual

O [ `VisualAttribute` ](xref:Xamarin.Forms.VisualAttribute) pode ser usado para, opcionalmente, registrar um nome diferente para o `IVisual` tipo. Essa abordagem pode ser usada para resolver conflitos de nomes entre diferentes bibliotecas do Visual ou em situações em que você apenas deseja se referir a um elemento Visual por um nome diferente de seu nome de tipo.

O [ `VisualAttribute` ](xref:Xamarin.Forms.VisualAttribute) devem ser definidos no nível de assembly na biblioteca multiplataforma ou no projeto de plataforma:

```csharp
[assembly: Visual("MyVisual", typeof(CustomVisual))]
```

O `IVisual` tipo pode ser consumido por meio de seu nome registrado:

```xaml
<Button Visual="MyVisual"
        ... />
```

> [!NOTE]
> Ao consumir um Visual por meio de seu nome registrado, qualquer sufixo "Visual" deve ser incluído.

## <a name="related-links"></a>Links relacionados

- [Material Visual (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VisualDemos/)
- [Material do xamarin. Forms Visual](material-visual.md)
- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
