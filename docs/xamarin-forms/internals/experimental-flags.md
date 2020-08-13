---
title: Xamarin.Formssinalizadores experimentais
description: Xamarin.Formsos sinalizadores experimentais permitem que a equipe de engenharia envie novos recursos aos usuários mais rapidamente, enquanto ainda pode alterar as APIs de recursos antes que elas passem para uma versão estável.
ms.prod: xamarin
ms.assetid: AF4BDD27-89F6-48AE-A8CD-D7E4DDA2CCA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/13/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 94fed78d7cf67ec5b6d783b1ced25a81266242d6
ms.sourcegitcommit: f7fe46c0236a7130b63a33d9d1670d5111582dd2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88186103"
---
# <a name="no-locxamarinforms-experimental-flags"></a>Xamarin.Formssinalizadores experimentais

Quando um novo Xamarin.Forms recurso é implementado, às vezes ele é colocado atrás de um sinalizador experimental. Isso permite que a equipe de engenharia forneça novos recursos para você com mais rapidez e, ao mesmo tempo, possa alterar as APIs de recursos antes que elas se movam para uma versão estável. O sinalizador experimental é removido quando o recurso passa para uma versão estável.

Xamarin.Formsinclui os seguintes sinalizadores experimentais:

- `Brush_Experimental`
- `CarouselView_Experimental`
- `DragAndDrop_Experimental`
- `Expander_Experimental`
- `Markup_Experimental`
- `MediaElement_Experimental`
- `RadioButton_Experimental`
- `Shapes_Experimental`
- `Shell_UWP_Experimental`
- `SwipeView_Experimental`

Usar a funcionalidade que está atrás de um sinalizador experimental exige que você habilite o sinalizador ou os sinalizadores em seu aplicativo. Há duas abordagens para habilitar sinalizadores experimentais:

- Habilite o sinalizador experimental, ou sinalizadores, em seus projetos de plataforma.
- Habilite o sinalizador experimental, ou sinalizadores, em sua `App` classe.

> [!WARNING]
> O consumo da funcionalidade que está atrás de um sinalizador experimental, sem habilitar o sinalizador resultará em seu aplicativo lançar uma exceção que indica qual sinalizador deve ser habilitado.

## <a name="enable-flags-in-platform-projects"></a>Habilitar sinalizadores em projetos de plataforma

O `Xamarin.Forms.Forms.SetFlags` método pode ser usado para habilitar um sinalizador experimental em seus projetos de plataforma:

```csharp
Xamarin.Forms.Forms.SetFlags("CarouselView_Experimental");
```

O `SetFlags` método deve ser invocado em sua `AppDelegate` classe no Ios, em sua `MainActivity` classe no Android e em sua `App` classe no UWP.

> [!IMPORTANT]
> A habilitação de um sinalizador experimental em seus projetos de plataforma deve ocorrer antes que o `Forms.Init` método seja invocado.

O `Xamarin.Forms.Forms.SetFlags` método aceita um `string` argumento de matriz, o que torna possível habilitar vários sinalizadores experimentais em uma única chamada de método:

```csharp
Xamarin.Forms.Forms.SetFlags(new string[] { "CarouselView_Experimental", "MediaElement_Experimental", "SwipeView_Experimental" });
```

> [!WARNING]
> Nunca chame o `SetFlags` método mais de uma vez, pois as chamadas subsequentes substituirão o resultado de chamadas anteriores.

## <a name="enable-flags-in-your-app-class"></a>Habilitar sinalizadores em sua classe de aplicativo

O `Device.SetFlags` método pode ser usado para habilitar um sinalizador experimental na `App` classe em seu projeto de código compartilhado:

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

O `Device.SetFlags` método aceita um `IReadOnlyList<string>` argumento, o que torna possível habilitar vários sinalizadores experimentais em uma única chamada de método:

```csharp
Device.SetFlags(new string[]{ "CarouselView_Experimental", "MediaElement_Experimental", "SwipeView_Experimental" });
```

> [!WARNING]
> Nunca chame o `SetFlags` método mais de uma vez, pois as chamadas subsequentes substituirão o resultado de chamadas anteriores.

## <a name="old-experimental-flags"></a>Sinalizadores experimentais antigos

A tabela a seguir lista os sinalizadores experimentais para recursos que agora estão em disponibilidade geral e a Xamarin.Forms versão em que o sinalizador experimental foi removido:

| Sinalizador | Xamarin.FormsLiberar |
| ---- | --------------------- |
| `AppTheme_Experimental` | 4.8 |
| `CollectionView_Experimental` | 4.3 |
| `FastRenderers_Experimental` | 4,0 |
| `IndicatorView_Experimental` | 4.7 |
| `Shell_Experimental` | 4,0  |
| `StateTriggers_Experimental` | 4.7 |
| `Visual_Experimental` | 3.6 |
