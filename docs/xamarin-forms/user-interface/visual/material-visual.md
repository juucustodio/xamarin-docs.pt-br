---
title: Visual de material do Xamarin. Forms
description: O Visual material xamarin. Forms pode ser usado para criar aplicativos Xamarin. Forms que parecem amplamente idênticos no iOS e no Android.
ms.prod: xamarin
ms.assetid: B774F68C-EF9E-49E1-B738-CDC64879ADA2
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 11/25/2019
ms.openlocfilehash: 81ef3c44d6eb8aaf4dd0ec467e11ef04adb02a76
ms.sourcegitcommit: 5a6124271a679b8961fa9430bd738fcb18544e92
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2020
ms.locfileid: "78291873"
---
# <a name="xamarinforms-material-visual"></a>Visual de material do Xamarin. Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

O [design de material](https://material.io) é um sistema de design conceituada criado pelo Google, que prescreve o tamanho, a cor, o espaçamento e outros aspectos de como os modos de exibição e os layouts devem se parecer e se comportar.

O Visual material xamarin. Forms pode ser usado para aplicar regras de design de material a aplicativos Xamarin. Forms, criando aplicativos que parecem ser amplamente idênticos no iOS e no Android. Quando o Visual material está habilitado, as exibições com suporte adotam a mesma plataforma cruzada de design, criando uma aparência unificada.

[capturas de tela de materiais visuais de ![](material-visual-images/material-visual-cropped.png)](material-visual-images/material-visual.png#lightbox)

O processo para habilitar o Visual material Xamarin. Forms em seu aplicativo é:

1. Adicione o pacote do NuGet [Xamarin. Forms. Visual. material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) aos seus projetos de plataforma iOS e Android. Este pacote NuGet fornece renderizadores de design de material otimizados no iOS e no Android. No iOS, o pacote fornece a dependência transitiva para [Xamarin. Ios. MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents), que é uma C# associação aos [componentes materiais do Google para IOS](https://material.io/develop/ios/). No Android, o pacote fornece destinos de compilação para garantir que seu TargetFramework esteja configurado corretamente.
1. Inicialize o Visual de material em cada projeto de plataforma. Para obter mais informações, consulte [inicializar o Visual do material](#initialize-material-visual).
1. Crie controles visuais materiais definindo a propriedade [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) como `Material` em todas as páginas que devem adotar as regras de design de material. Para obter mais informações, consulte [consumir renderizadores de materiais](#apply-material-visual).
1. adicional Personalize os controles materiais. Para obter mais informações, consulte [Personalizar controles materiais](#customize-material-visual).

> [!IMPORTANT]
> No Android, o Visual do material requer uma versão mínima de 5,0 (API 21) ou superior e um TargetFramework da versão 9,0 (API 28). Além disso, seu projeto de plataforma requer bibliotecas de suporte do Android 28.0.0 ou superior, e seu tema precisa herdar de um tema de componentes de material ou continuar a herdar de um tema AppCompat. Para obter mais informações, consulte [introdução aos componentes materiais para Android](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md).

O Visual de material atualmente dá suporte aos seguintes controles:

- [`ActivityIndicator`](xref:Xamarin.Forms.ActivityIndicator)
- [`Button`](xref:Xamarin.Forms.Button)
- [`CheckBox`](xref:Xamarin.Forms.CheckBox)
- [`DatePicker`](xref:Xamarin.Forms.DatePicker)
- [`Editor`](xref:Xamarin.Forms.Editor)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`Picker`](xref:Xamarin.Forms.Picker)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)
- [`Slider`](xref:Xamarin.Forms.Slider)
- [`Stepper`](xref:Xamarin.Forms.Stepper)
- [`TimePicker`](xref:Xamarin.Forms.TimePicker)

Os controles materiais são obtidos por renderizadores materiais, que aplicam as regras de design de material. Funcionalmente, os renderizadores de materiais não são diferentes para os renderizadores padrão. Para obter mais informações, consulte [Personalizar material Visual](#customize-material-visual).

## <a name="initialize-material-visual"></a>Inicializar o Visual do material

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

## <a name="apply-material-visual"></a>Aplicar visual de material

Os aplicativos podem habilitar o Visual de material definindo a propriedade [`VisualElement.Visual`](xref:Xamarin.Forms.VisualElement.Visual) em uma página, um layout ou uma exibição, para `Material`:

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

Definir a propriedade `VisualElement.Visual` como `Material` direciona seu aplicativo para usar os renderizadores visuais de material em vez dos renderizadores padrão. A propriedade [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) pode ser definida como qualquer tipo que implemente `IVisual`, com a classe [`VisualMarker`](xref:Xamarin.Forms.VisualMarker) fornecendo as seguintes propriedades `IVisual`:

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

## <a name="customize-material-visual"></a>Personalizar o Visual de material

O pacote NuGet do Visual do material é uma coleção de renderizadores que percebem os controles Xamarin. Forms. A personalização de controles visuais de material é idêntica à personalização de controles padrão.

Os efeitos são a técnica recomendada quando a meta é personalizar um controle existente. Se houver um processador visual de material, será menos trabalho para personalizar o controle com um efeito do que é a subclasse do renderizador. Para obter mais informações sobre efeitos, consulte os [efeitos do Xamarin. Forms](~/xamarin-forms/app-fundamentals/effects/index.md).

Renderizadores personalizados são a técnica recomendada quando um processador de materiais não existe. As seguintes classes de processador estão incluídas com o Visual material:

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

A subclasse de um processador de material é quase idêntica aos renderizadores que não são materiais. No entanto, ao exportar um renderizador que subtem um processador de material, você deve fornecer um terceiro argumento para o atributo `ExportRenderer` que especifica o tipo de `VisualMarker.MaterialVisual`:

```csharp
using Xamarin.Forms.Material.Android;

[assembly: ExportRenderer(typeof(ProgressBar), typeof(CustomMaterialProgressBarRenderer), new[] { typeof(VisualMarker.MaterialVisual) })]
namespace MyApp.Android
{
    public class CustomMaterialProgressBarRenderer : MaterialProgressBarRenderer
    {
        //...
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
- [Efeitos do Xamarin. Forms](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
