---
title: Material do xamarin. Forms Visual
description: Material do xamarin. Forms Visual pode ser usado para criar aplicativos xamarin. Forms que se parecem idênticos ou praticamente idênticas, no iOS e Android.
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2019
ms.openlocfilehash: 46ffe29f898e2f7bd8fcbe759f5a2f54e3dd60e1
ms.sourcegitcommit: 00744f754527e5b55154365f89691caaf1c9d929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57557498"
---
# <a name="xamarinforms-material-visual"></a>Material do xamarin. Forms Visual

Material do xamarin. Forms Visual pode ser usado para criar aplicativos xamarin. Forms que se parecem idênticos ou praticamente idênticas, no iOS e Android. Isso é feito pelo uso de processadores adicionais que implementam uma aparência de material, com aplicativos para aceitar a aparência por meio de `Visual` propriedade:

```xaml
<ContentPage ...
             Visual="Material">
    ...
</ContentPage>
```

O código c# equivalente é:

```csharp
ContentPage contentPage = new ContentPage();
contentPage.Visual = VisualMarker.Material;
```

> [!IMPORTANT]
> O `Visual` propriedade está definida na `VisualElement` classe, com modos de exibição herdando a `Visual` valor da propriedade de seus pais. Portanto, definir a `Visual` propriedade em um `ContentPage` garante que todas as exibições com suporte na página usarão essa aparência visual. Além disso, o `Visual` propriedade pode ser substituída em uma exibição.

Os renderizadores de material estão atualmente incluídos para as seguintes exibições no iOS e Android:

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

No iOS e Android, seu projeto de plataforma deve ter o [Xamarin.Forms.Visual.Material](https://www.nuget.org/packages/ https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) pacote NuGet instalado. Depois de instalar o pacote do NuGet, o código de inicialização é necessária em cada projeto de plataforma *após* o `Xamarin.Forms.Forms.Init` chamada de método. Para iOS, use o seguinte código:

```csharp
global::Xamarin.Forms.Forms.Init();
FormsMaterial.Init();
```

No Android, você deve passar os mesmos parâmetros que são passados para `Forms.Init`:

```csharp
global::Xamarin.Forms.Forms.Init(this, bundle);
FormsMaterial.Init(this, bundle);
```

> [!IMPORTANT]
> No Android, o material só funciona com TargetFramework 9.0 (28 de API). Além disso, seu projeto de plataforma deve usar v28 das bibliotecas de suporte, e seu tema precisa herdar de um tema de Material componentes ou continuar herdar de um tema AppCompat. Para obter mais informações, consulte [Introdução aos componentes de Material para Android](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md).

As capturas de tela a seguir mostram uma interface do usuário que inclui quatro modos de exibição para o qual material renderizadores existem, mas renderizados usando os processadores de padrão:

[![Captura de tela de renderizadores padrão, no iOS e Android](material-visual-images/default-renderers.png "exibições usando renderizadores padrão")](material-visual-images/default-renderers-large.png#lightbox)

As capturas de tela a seguir mostram a mesma interface do usuário renderizado usando os processadores de materiais:

[![Captura de tela de renderizadores de material, no iOS e Android](material-visual-images/material-renderers.png "exibições usando renderizadores de material")](material-visual-images/material-renderers-large.png#lightbox)

> [!NOTE]
> Com processadores de material os controles renderizados permanecem controles nativos e, portanto, ainda haverá diferenças de interface de usuário entre plataformas para áreas como sombras, fontes, cores e elevação.

## <a name="material-renderers"></a>Renderizadores de material

Os renderizadores de material podem ser personalizados, assim como os renderizadores de padrão, usando as seguintes classes base:

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

## <a name="related-links"></a>Links relacionados

- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
