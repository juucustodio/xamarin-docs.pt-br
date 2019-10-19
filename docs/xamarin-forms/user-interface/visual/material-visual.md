---
title: Visual de material do Xamarin. Forms
description: O Visual material xamarin. Forms pode ser usado para criar aplicativos Xamarin. Forms que parecem idênticos ou amplamente idênticos no iOS e no Android.
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2019
ms.openlocfilehash: b735541d51321231775b025745e68c54552697d3
ms.sourcegitcommit: dad4dfcd194b63ec9e903363351b6d9e543d4888
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2019
ms.locfileid: "71198487"
---
# <a name="xamarinforms-material-visual"></a>Visual de material do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

O [design de material](https://material.io) é um sistema de design conceituada criado pelo Google, que prescreve o tamanho, a cor, o espaçamento e outros aspectos de como os modos de exibição e os layouts devem se parecer e se comportar.

O Visual material xamarin. Forms pode ser usado para aplicar regras de design de material a aplicativos Xamarin. Forms, criando aplicativos que parecem idênticos ou amplamente idênticos no iOS e no Android. Quando o Visual material está habilitado, as exibições com suporte adotam a mesma plataforma cruzada de design, criando uma aparência unificada. Isso é obtido com renderizadores materiais, que aplicam as regras de design de material.

O processo para habilitar o Visual material Xamarin. Forms em seu aplicativo é:

1. Adicione o pacote do NuGet [Xamarin. Forms. Visual. material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) aos seus projetos de plataforma iOS e Android. Este pacote NuGet fornece renderizadores de design de material otimizados no iOS e no Android. No iOS, o pacote fornece a dependência transitiva para [Xamarin. Ios. MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents), que é uma C# associação aos [componentes materiais do Google para IOS](https://material.io/develop/ios/). No Android, o pacote fornece destinos de compilação para garantir que seu TargetFramework esteja configurado corretamente.
1. Inicialize os renderizadores de material em cada projeto de plataforma. Para obter mais informações, consulte [inicializar renderizadores de material](#initialize-material-renderers).
1. Consuma os renderizadores de material definindo a propriedade [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) como `Material` em qualquer página que deva adotar as regras de design de material. Para obter mais informações, consulte [consumir renderizadores de materiais](#consume-material-renderers).
1. adicional Personalize os renderizadores de material. Para obter mais informações, consulte [Personalizar renderizadores de material](#customize-material-renderers).

> [!IMPORTANT]
> No Android, os renderizadores de material exigem uma versão mínima de 5,0 (API 21) ou superior e um TargetFramework da versão 9,0 (API 28). Além disso, seu projeto de plataforma requer bibliotecas de suporte do Android 28.0.0 ou superior, e seu tema precisa herdar de um tema de componentes de material ou continuar a herdar de um tema AppCompat. Para obter mais informações, consulte [introdução aos componentes materiais para Android](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md).

Os renderizadores de materiais estão atualmente incluídos no pacote NuGet [Xamarin. Forms. Visual. material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) para as seguintes exibições:

- [`Button`](xref:Xamarin.Forms.Button)
- `CheckBox`
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

Funcionalmente, os renderizadores de material não são diferentes para os renderizadores padrão.

## <a name="initialize-material-renderers"></a>Inicializar renderizadores de materiais

Depois de instalar o pacote NuGet [Xamarin. Forms. Visual. material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) , os renderizadores de material devem ser inicializados em cada projeto de plataforma.

No iOS, isso deve ocorrer em **AppDelegate.cs** invocando o método `Xamarin.Forms.FormsMaterial.Init` *após* o método `Xamarin.Forms.Forms.Init`:

```csharp
global::Xamarin.Forms.Forms.Init();
global::Xamarin.Forms.FormsMaterial.Init();
```

No Android, isso deve ocorrer em **MainActivity.cs** invocando o método `Xamarin.Forms.FormsMaterial.Init` *após* o método `Xamarin.Forms.Forms.Init`:

```csharp
global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
global::Xamarin.Forms.FormsMaterial.Init(this, savedInstanceState);
```

## <a name="consume-material-renderers"></a>Consumir renderizadores de materiais

Os aplicativos podem optar por usar os renderizadores de material definindo a propriedade [`VisualElement.Visual`](xref:Xamarin.Forms.VisualElement.Visual) em uma página, layout ou exibição, para `Material`:

```xaml
<ContentPage Visual="Material"
             ...>
    ...
</ContentPage>
```

Este é o código C# equivalente:

```csharp
ContentPage contentPage = new ContentPage();
contentPage.Visual = VisualMarker.Material;
```

A propriedade [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) pode ser definida como qualquer tipo que implemente `IVisual`, com a classe [`VisualMarker`](xref:Xamarin.Forms.VisualMarker) fornecendo as seguintes propriedades `IVisual`:

- `Default` – indica que a exibição deve renderizar usando o renderizador padrão.
- `MatchParent` – indica que a exibição deve usar o mesmo renderizador que seu pai direto.
- `Material` – indica que a exibição deve renderizar usando um processador de material.

> [!IMPORTANT]
> A propriedade [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) é definida na classe [`VisualElement`](xref:Xamarin.Forms.VisualElement) , com exibições que herdam o valor da propriedade `Visual` de seus pais. Portanto, definir a propriedade `Visual` em uma [`ContentPage`](xref:Xamarin.Forms.ContentPage) garante que todas as exibições com suporte na página usarão esse visual. Além disso, a propriedade `Visual` pode ser substituída em uma exibição.

As capturas de tela a seguir mostram uma interface do usuário renderizada usando os renderizadores padrão:

[![Captura de tela de renderizadores padrão em iOS e Android](material-visual-images/default-renderers.png "Exibições que usam renderizadores padrão")](material-visual-images/default-renderers-large.png#lightbox)

As capturas de tela a seguir mostram a mesma interface de usuário renderizada usando os processadores de material:

[![Captura de tela de renderizadores de material, no iOS e no Android](material-visual-images/material-renderers.png "Exibições usando renderizadores de materiais")](material-visual-images/material-renderers-large.png#lightbox)

As principais diferenças visíveis entre os renderizadores padrão e os renderizadores de materiais, mostrados aqui, são que os renderizadores de material capitalizam [`Button`](xref:Xamarin.Forms.Button) texto e arredondam os cantos de [`Frame`](xref:Xamarin.Forms.Frame) bordas. No entanto, os renderizadores de materiais usam controles nativos e, portanto, ainda pode haver diferenças de interface do usuário entre plataformas para áreas como fontes, sombras, cores e elevação.

> [!NOTE]
> Os componentes de design de material aderem em conjunto às diretrizes do Google. Como resultado, os renderizadores de design de material são tendenciosas em relação ao dimensionamento e ao comportamento. Quando você precisar de maior controle de estilos ou comportamento, ainda poderá criar seu próprio [efeito](~/xamarin-forms/app-fundamentals/effects/index.md), [comportamento](~/xamarin-forms/app-fundamentals/behaviors/index.md)ou [renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) para obter os detalhes necessários.

## <a name="customize-material-renderers"></a>Personalizar renderizadores de materiais

Os renderizadores de materiais podem, opcionalmente, ser personalizados, assim como os renderizadores padrão, por meio das seguintes classes base:

- `MaterialButtonRenderer`
- `MaterialCheckBoxRenderer`
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

O código a seguir mostra um exemplo de personalização da classe `MaterialProgressBarRenderer`:

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

Neste exemplo, o `ExportRendererAttribute` especifica que a classe `CustomMaterialProgressBarRenderer` será usada para renderizar a exibição [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) , com o tipo de `IVisual` registrado como o terceiro argumento.

> [!NOTE]
> Um processador que especifica um tipo de `IVisual`, como parte de seu `ExportRendererAttribute`, será usado para renderizar os modos de exibição, em vez do renderizador padrão. No momento da seleção do processador, a propriedade `Visual` da exibição é inspecionada e incluída no processo de seleção do processador.

Para obter mais informações sobre renderizadores personalizados, consulte [renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="related-links"></a>Links relacionados

- [Visual do material (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)
- [Criar um renderizador Visual Xamarin. Forms](create.md)
- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
