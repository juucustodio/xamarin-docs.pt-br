---
title: Material do xamarin. Forms Visual
description: Material do xamarin. Forms Visual pode ser usado para criar aplicativos xamarin. Forms que se parecem idênticos ou praticamente idênticas, no iOS e Android.
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2019
ms.openlocfilehash: cf6ab8266b0798ccbf29078313bbc7454125a1af
ms.sourcegitcommit: 97dca3face7c4ad5555dfaca88f5b45a70ca556d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2019
ms.locfileid: "57972618"
---
# <a name="xamarinforms-material-visual"></a>Material do xamarin. Forms Visual

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VisualDemos/)

[Design de material](https://material.io) é um sistema de design "teimosa" criado pelo Google, que determina o tamanho, cor, espaçamento e outros aspectos de como layouts e modos de exibição devem parecer e se comportam.

Material do xamarin. Forms Visual pode ser usado para aplicar regras de Design de Material para aplicativos xamarin. Forms, criação de aplicativos que se parecem idênticos ou praticamente idênticas, no iOS e Android. Quando o Visual de Material é habilitado, modos de exibição com suporte adotam a mesmo design multiplataforma, criando uma aparência unificada. Isso é obtido com processadores de material, que se aplicam as regras de Design de Material.

O processo para habilitar o xamarin. Forms Material Visual em seu aplicativo é:

1. Adicione a [Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) pacote do NuGet para seus projetos de plataforma Android e iOS. Este pacote NuGet entrega otimizados renderizadores de Design de Material em iOS e Android. No iOS, o pacote fornece a dependência transitiva [Xamarin.iOS.MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents), que é um C# associação do Google [componentes do Material para iOS](https://material.io/develop/ios/). No Android, o pacote fornece destinos de compilação para garantir que seu TargetFramework está configurado corretamente.
1. Inicialize os renderizadores de material em cada projeto de plataforma. Para obter mais informações, consulte [inicializar renderizadores materiais](#initialize-material-renderers).
1. Consumir os renderizadores de material, definindo o [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) propriedade `Material` em todas as páginas que devem adotar as regras de Design de Material. Para obter mais informações, consulte [consumir renderizadores materiais](#consume-material-renderers).
1. [opcional] Personalize os renderizadores de material. Para obter mais informações, consulte [personalizar renderizadores materiais](#customize-material-renderers).

> [!IMPORTANT]
> No Android, os renderizadores de material exigem a versão mínima do 5.0 (API 21) ou superior e um TargetFramework da versão 9.0 (28 de API). Além disso, seu projeto de plataforma requer bibliotecas de suporte do Android 28.0.0 ou superior, e seu tema precisa herdar de um tema de Material componentes ou continuar herdar de um tema AppCompat. Para obter mais informações, consulte [Introdução aos componentes de Material para Android](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md).

Renderizadores de material atualmente estão incluídos na [Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) pacote do NuGet para as exibições a seguir:

- [`Button`](xref:Xamarin.Forms.Button)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)

Funcionalmente, os renderizadores de material não são diferentes para os renderizadores de padrão.

## <a name="initialize-material-renderers"></a>Inicializar os renderizadores de material

Depois de instalar o [Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) de pacote do NuGet, o material renderizadores devem ser inicializados em cada projeto de plataforma.

IOS, isso deve ocorrer **AppDelegate.cs** invocando o `FormsMaterial.Init` método *após* o `Xamarin.Forms.Forms.Init` método:

```csharp
global::Xamarin.Forms.Forms.Init();
FormsMaterial.Init();
```

No Android, isso deve ocorrer no **MainActivity.cs** invocando o `FormsMaterial.Init` método *após* o `Xamarin.Forms.Forms.Init` método:

```csharp
global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
FormsMaterial.Init(this, savedInstanceState);
```

## <a name="consume-material-renderers"></a>Consumir os renderizadores de material

Aplicativos podem optarem por usar os renderizadores de material, definindo o [ `VisualElement.Visual` ](xref:Xamarin.Forms.VisualElement.Visual) propriedade em uma página, layout ou modo de exibição, para `Material`:

```xaml
<ContentPage Visual="Material"
             ...>
    ...
</ContentPage>
```

O código c# equivalente é:

```csharp
ContentPage contentPage = new ContentPage();
contentPage.Visual = VisualMarker.Material;
```

O [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) propriedade pode ser definida como qualquer tipo que implemente `IVisual`, com o [ `VisualMarker` ](xref:Xamarin.Forms.VisualMarker) classe que fornece o seguinte `IVisual` propriedades:

- `Default` – indica que o modo de exibição deve ser renderizados usando o renderizador padrão.
- `MatchParent` – indica que o modo de exibição deve usar o renderizador do mesmo como pai direto.
- `Material` – indica que o modo de exibição deve ser renderizados usando um renderizador de material.

> [!IMPORTANT]
> O [ `Visual` ](xref:Xamarin.Forms.VisualElement.Visual) propriedade está definida no [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) classe, com modos de exibição herdando a `Visual` valor da propriedade de seus pais. Portanto, definir a `Visual` propriedade em um [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) garante que todas as exibições com suporte na página usarão esse elemento Visual. Além disso, o `Visual` propriedade pode ser substituída em uma exibição.

As capturas de tela a seguir mostram uma interface do usuário renderizada usando os processadores de padrão:

[![Captura de tela de renderizadores padrão, no iOS e Android](material-visual-images/default-renderers.png "exibições usando renderizadores padrão")](material-visual-images/default-renderers-large.png#lightbox)

As capturas de tela a seguir mostram a mesma interface do usuário renderizado usando os processadores de materiais:

[![Captura de tela de renderizadores de material, no iOS e Android](material-visual-images/material-renderers.png "exibições usando renderizadores de material")](material-visual-images/material-renderers-large.png#lightbox)

As principais diferenças visíveis entre os renderizadores de material, mostrados aqui e o renderizadores padrão são os renderizadores de material em maiuscula [ `Button` ](xref:Xamarin.Forms.Button) texto e arredondar os cantos do [ `Frame` ](xref:Xamarin.Forms.Frame)bordas. No entanto, os renderizadores de material usam controles nativos e, portanto, ainda pode haver diferenças de interface de usuário entre plataformas para áreas como sombras, fontes, cores e elevação.

> [!NOTE]
> Componentes de Design de material risca diretrizes do Google. Como resultado, os renderizadores de Design de Material são mais adequados para dimensionamento e o comportamento. Quando você precisar de maior controle dos estilos ou comportamento, você ainda pode criar seus próprios [efeito](~/xamarin-forms/app-fundamentals/effects/index.md), [comportamento](~/xamarin-forms/app-fundamentals/behaviors/index.md), ou [renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) para alcançar o detalhe que você precisa.

## <a name="customize-material-renderers"></a>Personalizar os renderizadores de material

Os renderizadores de material, opcionalmente, podem ser personalizados, assim como os renderizadores de padrão, as seguintes classes de base por meio de:

- `MaterialButtonRenderer`
- `MaterialEntryRenderer`
- `MaterialFrameRenderer`
- `MaterialProgressBarRenderer`
- `MaterialDatePickerRenderer`
- `MaterialTimePickerRenderer`
- `MaterialPickerRenderer`
- `MaterialActivityIndicatorRenderer`
- `MaterialEditorRenderer`
- `MaterialSliderRenderer`
- `MaterialStepperRenderer`

O código a seguir mostra um exemplo de como personalizar o `MaterialProgressBarRenderer` classe:

```csharp
using Xamarin.Forms.Material.Android;

[assembly: ExportRenderer(typeof(ProgressBar), typeof(CustomMaterialProgressBarRenderer), new[] { typeof(VisualMarker.MaterialVisual) })]
namespace MyApp.Android
{
    public class CustomMaterialProgressBarRenderer : MaterialProgressBarRenderer
    {
        ...
    }
}
```

Neste exemplo, o `ExportRendererAttribute` Especifica que o `CustomMaterialProgressBarRenderer` classe será usada para renderizar o [ `ProgressBar` ](xref:Xamarin.Forms.ProgressBar) exibição, com o `IVisual` tipo registrado como o terceiro argumento.

> [!NOTE]
> Um renderizador que especifica um `IVisual` tipo, como parte da sua `ExportRendererAttribute`, será usado para renderizar aceitado modos de exibição, em vez do renderizador padrão. Em tempo de seleção do renderizador, o `Visual` propriedade do modo de exibição é inspecionada e incluída no processo de seleção do renderizador.

Para obter mais informações sobre renderizadores personalizados, consulte [renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="related-links"></a>Links relacionados

- [Material Visual (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VisualDemos/)
- [Criar um renderizador Visual do xamarin. Forms](create.md)
- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
