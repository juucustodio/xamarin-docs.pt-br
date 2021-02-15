---
title: Xamarin.Forms sinalizadores experimentais
description: Xamarin.Forms os sinalizadores experimentais permitem que a equipe de engenharia envie novos recursos aos usuários mais rapidamente, enquanto ainda pode alterar as APIs de recursos antes que elas passem para uma versão estável.
ms.prod: xamarin
ms.assetid: AF4BDD27-89F6-48AE-A8CD-D7E4DDA2CCA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d9f6f5e2d27f04dadfa2b830703bb36355072c44
ms.sourcegitcommit: c9f62ed4bab516be18606e87e89e01f57b4ad17e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98163485"
---
# <a name="no-locxamarinforms-experimental-flags"></a>Xamarin.Forms sinalizadores experimentais

Quando um novo Xamarin.Forms recurso é implementado, às vezes ele é colocado atrás de um sinalizador experimental. Isso permite que a equipe de engenharia forneça novos recursos para você com mais rapidez e, ao mesmo tempo, possa alterar as APIs de recursos antes que elas se movam para uma versão estável. O sinalizador experimental é removido quando o recurso passa para uma versão estável.

Xamarin.Forms inclui os seguintes sinalizadores experimentais:

- `Shell_UWP_Experimental`

Usar a funcionalidade que está atrás de um sinalizador experimental exige que você habilite o sinalizador ou os sinalizadores em seu aplicativo. Há duas abordagens para habilitar sinalizadores experimentais:

- Habilite o sinalizador experimental em seus projetos de plataforma.
- Habilite o sinalizador experimental em sua `App` classe.

> [!WARNING]
> O consumo da funcionalidade que está atrás de um sinalizador experimental, sem habilitar o sinalizador resultará em seu aplicativo lançar uma exceção que indica qual sinalizador deve ser habilitado.

## <a name="enable-flags-in-platform-projects"></a>Habilitar sinalizadores em projetos de plataforma

O `Xamarin.Forms.Forms.SetFlags` método pode ser usado para habilitar um sinalizador experimental em seus projetos de plataforma:

```csharp
Xamarin.Forms.Forms.SetFlags("Shell_UWP_Experimental");
```

O `SetFlags` método deve ser invocado em sua `AppDelegate` classe no Ios, em sua `MainActivity` classe no Android e em sua `App` classe no UWP.

> [!IMPORTANT]
> A habilitação de um sinalizador experimental em seus projetos de plataforma deve ocorrer antes que o `Forms.Init` método seja invocado.

O `Xamarin.Forms.Forms.SetFlags` método aceita um `string` argumento de matriz, o que torna possível habilitar vários sinalizadores experimentais em uma única chamada de método:

```csharp
Xamarin.Forms.Forms.SetFlags(new string[] { "Shell_UWP_Experimental", "AnotherFeature_Experimental" });
```

> [!WARNING]
> Nunca chame o `SetFlags` método mais de uma vez, pois as chamadas subsequentes substituirão o resultado de chamadas anteriores.

## <a name="enable-flags-in-your-app-class"></a>Habilitar sinalizadores em sua classe de aplicativo

O `Device.SetFlags` método pode ser usado para habilitar um sinalizador experimental na `App` classe em seu projeto de código compartilhado:

```csharp
Device.SetFlags(new string[]{ "Shell_UWP_Experimental" });
```

O `Device.SetFlags` método aceita um `IReadOnlyList<string>` argumento, o que torna possível habilitar vários sinalizadores experimentais em uma única chamada de método:

```csharp
Device.SetFlags(new string[]{ "Shell_UWP_Experimental", "AnotherFeature_Experimental" });
```

> [!WARNING]
> Nunca chame o `SetFlags` método mais de uma vez, pois as chamadas subsequentes substituirão o resultado de chamadas anteriores.

## <a name="old-experimental-flags"></a>Sinalizadores experimentais antigos

A tabela a seguir lista os sinalizadores experimentais para recursos que agora estão em disponibilidade geral e a Xamarin.Forms versão em que o sinalizador experimental foi removido:

| Sinalizador | Xamarin.Forms Liberar |
| ---- | --------------------- |
| `AppTheme_Experimental` | 4.8 |
| `Brush_Experimental` | 5.0 |
| `CarouselView_Experimental` | 5.0 |
| `CollectionView_Experimental` | 4.3 |
| `DragAndDrop_Experimental` | 5.0 |
| `FastRenderers_Experimental` | 4,0 |
| `IndicatorView_Experimental` | 4.7 |
| `Markup_Experimental` | 5,0 (movido para o Xamarin Community Toolkit) |
| `MediaElement_Experimental` | 5,0 (movido para o Xamarin Community Toolkit) |
| `RadioButton_Experimental` | 5.0 |
| `Shapes_Experimental` | 5.0 |
| `Shell_Experimental` | 4,0  |
| `StateTriggers_Experimental` | 4.7 |
| `SwipeView_Experimental` | 5.0 |
| `Visual_Experimental` | 3,6 |
