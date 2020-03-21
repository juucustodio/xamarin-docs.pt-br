---
title: Sinalizadores experimentais do Xamarin. Forms
description: Os sinalizadores experimentais do Xamarin. Forms permitem que a equipe de engenharia envie novos recursos aos usuários mais rapidamente, enquanto ainda pode alterar as APIs de recursos antes que elas passem para uma versão estável.
ms.prod: xamarin
ms.assetid: AF4BDD27-89F6-48AE-A8CD-D7E4DDA2CCA2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/20/2020
ms.openlocfilehash: cebb996da992058616f9cf96ef3212c9ce27022a
ms.sourcegitcommit: 6c60914b380ff679bbffd7790edd4d5e18005d0a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2020
ms.locfileid: "80112585"
---
# <a name="xamarinforms-experimental-flags"></a>Sinalizadores experimentais do Xamarin. Forms

Quando um novo recurso Xamarin. Forms é implementado, às vezes ele é colocado atrás de um sinalizador experimental. Isso permite que a equipe de engenharia forneça novos recursos para você com mais rapidez e, ao mesmo tempo, possa alterar as APIs de recursos antes que elas se movam para uma versão estável. O sinalizador experimental é removido quando o recurso passa para uma versão estável.

O Xamarin. Forms inclui os seguintes sinalizadores experimentais:

- `CarouselView_Experimental`
- `IndicatorView_Experimental`
- `Markup_Experimental`
- `MediaElement_Experimental`
- `Shell_UWP_Experimental`
- `StateTriggers_Experimental`
- `SwipeView_Experimental`

Usar a funcionalidade que está atrás de um sinalizador experimental exige que você habilite o sinalizador ou os sinalizadores em seu aplicativo. Há duas abordagens para habilitar sinalizadores experimentais:

- Habilite o sinalizador experimental, ou sinalizadores, em seus projetos de plataforma.
- Habilite o sinalizador experimental, ou sinalizadores, na sua classe de `App`.

> [!WARNING]
> O consumo da funcionalidade que está atrás de um sinalizador experimental, sem habilitar o sinalizador resultará em seu aplicativo lançar uma exceção que indica qual sinalizador deve ser habilitado.

## <a name="enable-flags-in-platform-projects"></a>Habilitar sinalizadores em projetos de plataforma

O método `Xamarin.Forms.Forms.SetFlags` pode ser usado para habilitar um sinalizador experimental em seus projetos de plataforma:

```csharp
Xamarin.Forms.Forms.SetFlags("CarouselView_Experimental");
```

O método `SetFlags` deve ser invocado em sua classe `AppDelegate` no iOS, na sua classe `MainActivity` no Android e na sua classe `App` no UWP.

> [!IMPORTANT]
> A habilitação de um sinalizador experimental em seus projetos de plataforma deve ocorrer antes que o método `Forms.Init` seja invocado.

O método `Xamarin.Forms.Forms.SetFlags` aceita um argumento de matriz `string`, o que torna possível habilitar vários sinalizadores experimentais em uma única chamada de método:

```csharp
Xamarin.Forms.Forms.SetFlags(new string[] { "CarouselView_Experimental", "IndicatorView_Experimental", "SwipeView_Experimental" });
```

> [!WARNING]
> Nunca chame o método `SetFlags` mais de uma vez, pois as chamadas subsequentes substituirão o resultado de chamadas anteriores.

## <a name="enable-flags-in-your-app-class"></a>Habilitar sinalizadores em sua classe de aplicativo

O método `Device.SetFlags` pode ser usado para habilitar um sinalizador experimental na classe `App` em seu projeto de código compartilhado:

```csharp
Device.SetFlags(new string[]{ "MediaElement_Experimental" });
```

O método `Device.SetFlags` aceita um argumento `IReadOnlyList<string>`, o que torna possível habilitar vários sinalizadores experimentais em uma única chamada de método:

```csharp
Device.SetFlags(new string[]{ "CarouselView_Experimental", "MediaElement_Experimental", "SwipeView_Experimental" });
```

> [!WARNING]
> Nunca chame o método `SetFlags` mais de uma vez, pois as chamadas subsequentes substituirão o resultado de chamadas anteriores.
