---
title: Criar um renderizador Visual do xamarin. Forms
description: Visual do xamarin. Forms permite que os renderizadores ser aplicadas seletivamente a objetos VisualElement, sem ter de controles do xamarin. Forms subclasse.
ms.prod: xamarin
ms.assetid: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2019
ms.openlocfilehash: 3c614bcd0044a0aa4a698c830d2f2af5aba6c65a
ms.sourcegitcommit: 00744f754527e5b55154365f89691caaf1c9d929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57557508"
---
# <a name="xamarinforms-visual"></a>Visual do xamarin. Forms

Visual do xamarin. Forms permite que os renderizadores ser aplicadas seletivamente a `VisualElement` objetos, sem ter de controles do xamarin. Forms subclasse. Quaisquer renderizadores que especificam uma `VisualType` como parte do `ExportRendererAttribute` será usado para exibições de processador, em vez dos renderizadores de padrão. Em tempo de seleção do renderizador, o `Visual` propriedade do modo de exibição é inspecionada e incluída no processo de seleção do renderizador.

> [!IMPORTANT]
> Atualmente, o `Visual` não pode ser alterado depois que o controle tiver sido processado, mas isso será alterado em uma versão futura.

Xamarin. Forms inclui uma aparência visual com base no design de material. Para obter mais informações, consulte [xamarin. Forms Material Visual](material-visual.md).

## <a name="create-a-visual"></a>Criar um Visual

Em sua biblioteca de plataforma cruzada, criar um tipo que deriva de `IVisual`:

```csharp
public class CustomVisual : IVisual
{
}
```

## <a name="register-the-visual-type"></a>Registrar o tipo de Visual

Criar um renderizador para a exibição necessária e registrar o tipo de elemento Visual como parte das plataformas `ExportRenderAttribute`:

```csharp
using Xamarin.Forms.Material.Android;

[assembly: ExportRenderer(typeof(ContentPage), typeof(CustomPageRenderer), new[] { typeof(CustomVisual) })]
namespace MyApp.Android
{
    public class CustomPageRenderer : PageRenderer
    {
        ...
    }
}
```

## <a name="consume-the-visual"></a>Consumir o Visual

Aplicativos podem aceitar usando o Visual personalizado por meio de `Visual` propriedade:

```xaml
<ContentPage ...
             Visual="Custom">
    ...
</ContentPage>
```

O código c# equivalente é:

```csharp
ContentPage contentPage = new ContentPage();
contentPage.Visual = new CustomVisual();
```

## <a name="register-a-name-for-a-visual"></a>Registrar um nome para um Visual

Pode haver conflitos entre diferentes bibliotecas do Visual ou talvez você queira apenas fazer referência a um Visual por um nome diferente. Nesse cenário, você pode usar um atributo de assembly para registrar um nome diferente para um determinado elemento Visual:

```csharp
[assembly: Visual("MyMaterialName", typeof(VisualMarker.MaterialVisual))]
```

O Visual personalizado, em seguida, pode ser consumido por meio de seu nome registrado:

```xaml
<ContentPage ...
             Visual="MyMaterialName">
    ...
</ContentPage>
````

## <a name="related-links"></a>Links relacionados

- [Material do xamarin. Forms Visual](material-visual.md)
- [Renderizadores personalizados](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
