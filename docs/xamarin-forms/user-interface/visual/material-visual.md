---
title: Xamarin.FormsVisual do material
description: Xamarin.FormsO Visual material pode ser usado para criar Xamarin.Forms aplicativos que parecem ser amplamente idênticos no Ios e no Android.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bba7d77d8cf565b1b2db2c1324e171389c5d0280
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84127161"
---
# <a name="xamarinforms-material-visual"></a>Xamarin.FormsVisual do material

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)

O [design de material](https://material.io) é um sistema de design conceituada criado pelo Google, que prescreve o tamanho, a cor, o espaçamento e outros aspectos de como os modos de exibição e os layouts devem se parecer e se comportar.

Xamarin.FormsO Visual material pode ser usado para aplicar regras de design de material a Xamarin.Forms aplicativos, criando aplicativos que parecem amplamente idênticos no Ios e no Android. Quando o Visual material está habilitado, as exibições com suporte adotam a mesma plataforma cruzada de design, criando uma aparência unificada.

[![Instantâneos visuais do material](material-visual-images/material-visual-cropped.png)](material-visual-images/material-visual.png#lightbox)

O processo para habilitar o Xamarin.Forms Visual de material em seu aplicativo é:

1. Adicione o [ Xamarin.Forms . Pacote NuGet Visual. material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) para seus projetos de plataforma iOS e Android. Este pacote NuGet fornece renderizadores de design de material otimizados no iOS e no Android. No iOS, o pacote fornece a dependência transitiva para [Xamarin. Ios. MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents), que é uma ligação C# com os [componentes materiais do Google para IOS](https://material.io/develop/ios/). No Android, o pacote fornece destinos de compilação para garantir que seu TargetFramework esteja configurado corretamente.
1. Inicialize o Visual de material em cada projeto de plataforma. Para obter mais informações, consulte [inicializar o Visual do material](#initialize-material-visual).
1. Crie controles visuais materiais definindo a [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) propriedade como `Material` em todas as páginas que devem adotar as regras de design de material. Para obter mais informações, consulte [consumir renderizadores de materiais](#apply-material-visual).
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

Depois de instalar o [ Xamarin.Forms . Pacote NuGet Visual. material](https://www.nuget.org/packages/Xamarin.Forms.Visual.Material/) , os renderizadores de material devem ser inicializados em cada projeto de plataforma.

No iOS, isso deve ocorrer em **AppDelegate.cs** invocando o `Xamarin.Forms.FormsMaterial.Init` método *após* o `Xamarin.Forms.Forms.Init` método:

```csharp
global::Xamarin.Forms.Forms.Init();
global::Xamarin.Forms.FormsMaterial.Init();
```

No Android, isso deve ocorrer em **MainActivity.cs** invocando o `Xamarin.Forms.FormsMaterial.Init` método *após* o `Xamarin.Forms.Forms.Init` método:

```csharp
global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
global::Xamarin.Forms.FormsMaterial.Init(this, savedInstanceState);
```

## <a name="apply-material-visual"></a>Aplicar visual de material

Os aplicativos podem habilitar o Visual de material definindo a [`VisualElement.Visual`](xref:Xamarin.Forms.VisualElement.Visual) propriedade em uma página, layout ou exibição, para `Material` :

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

Definir a `VisualElement.Visual` propriedade para `Material` direcionar seu aplicativo para usar os renderizadores visuais de material em vez dos renderizadores padrão. A [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) propriedade pode ser definida para qualquer tipo que implemente `IVisual` , com a [`VisualMarker`](xref:Xamarin.Forms.VisualMarker) classe fornecendo as seguintes `IVisual` Propriedades:

- `Default`– indica que a exibição deve renderizar usando o renderizador padrão.
- `MatchParent`– indica que a exibição deve usar o mesmo renderizador que seu pai direto.
- `Material`– indica que a exibição deve renderizar usando um renderizador de material.

> [!IMPORTANT]
> A [`Visual`](xref:Xamarin.Forms.VisualElement.Visual) propriedade é definida na [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe, com exibições que herdam o `Visual` valor da propriedade de seus pais. Portanto, definir a `Visual` propriedade em um [`ContentPage`](xref:Xamarin.Forms.ContentPage) garante que qualquer exibição com suporte na página usará esse visual. Além disso, a `Visual` propriedade pode ser substituída em uma exibição.

As capturas de tela a seguir mostram uma interface do usuário renderizada usando os renderizadores padrão:

[![Captura de tela de renderizadores padrão em iOS e Android](material-visual-images/default-renderers.png "Exibições que usam renderizadores padrão")](material-visual-images/default-renderers-large.png#lightbox)

As capturas de tela a seguir mostram a mesma interface de usuário renderizada usando os processadores de material:

[![Captura de tela de renderizadores de material, no iOS e no Android](material-visual-images/material-renderers.png "Exibições usando renderizadores de materiais")](material-visual-images/material-renderers-large.png#lightbox)

As principais diferenças visíveis entre os renderizadores padrão e os renderizadores de materiais, mostrados aqui, são que os renderizadores de material capitalizam [`Button`](xref:Xamarin.Forms.Button) texto e arredondam os cantos das [`Frame`](xref:Xamarin.Forms.Frame) bordas. No entanto, os renderizadores de materiais usam controles nativos e, portanto, ainda pode haver diferenças de interface do usuário entre plataformas para áreas como fontes, sombras, cores e elevação.

> [!NOTE]
> Os componentes de design de material aderem em conjunto às diretrizes do Google. Como resultado, os renderizadores de design de material são tendenciosas em relação ao dimensionamento e ao comportamento. Quando você precisar de maior controle de estilos ou comportamento, ainda poderá criar seu próprio [efeito](~/xamarin-forms/app-fundamentals/effects/index.md), [comportamento](~/xamarin-forms/app-fundamentals/behaviors/index.md)ou [renderizador personalizado](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) para obter os detalhes necessários.

## <a name="customize-material-visual"></a>Personalizar o Visual de material

O pacote NuGet do Visual do material é uma coleção de renderizadores que percebem os Xamarin.Forms controles. A personalização de controles visuais de material é idêntica à personalização de controles padrão.

Os efeitos são a técnica recomendada quando a meta é personalizar um controle existente. Se houver um processador visual de material, será menos trabalho para personalizar o controle com um efeito do que é a subclasse do renderizador. Para obter mais informações sobre efeitos, consulte [ Xamarin.Forms efeitos](~/xamarin-forms/app-fundamentals/effects/index.md).

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

A subclasse de um processador de material é quase idêntica aos renderizadores que não são materiais. No entanto, ao exportar um renderizador que subtem um processador de material, você deve fornecer um terceiro argumento para o `ExportRenderer` atributo que especifica o `VisualMarker.MaterialVisual` tipo:

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

Neste exemplo, o `ExportRendererAttribute` especifica que a `CustomMaterialProgressBarRenderer` classe será usada para renderizar a [`ProgressBar`](xref:Xamarin.Forms.ProgressBar) exibição, com o `IVisual` tipo registrado como o terceiro argumento.

> [!NOTE]
> Um renderizador que especifica um `IVisual` tipo, como parte de seu `ExportRendererAttribute` , será usado para renderizar os modos de exibição, em vez do renderizador padrão. No momento da seleção do processador, a `Visual` propriedade da exibição é inspecionada e incluída no processo de seleção do processador.

Para obter mais informações sobre renderizadores personalizados, consulte [renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="related-links"></a>Links relacionados

- [Visual do material (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-visualdemos)
- [Criar um Xamarin.Forms renderizador Visual](create.md)
- [Xamarin.FormsEffect](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
