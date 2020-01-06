---
title: Modo de transição de toque SwipeView no iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como consumir a plataforma do iOS específica que controla a transição usada ao abrir um SwipeView.
ms.prod: xamarin
ms.assetid: C667F24C-BAD8-47E0-9285-D3546BEF703B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
ms.openlocfilehash: d5ba92d008cf3431bce2c197aca45c894eb3d5c7
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490321"
---
# <a name="swipeview-swipe-transition-mode-on-ios"></a>Modo de transição de toque SwipeView no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do iOS específica controla a transição que é usada ao abrir um `SwipeView`. Ele é consumido em XAML definindo a propriedade `SwipeView.SwipeTransitionMode` vinculável como um valor da enumeração `SwipeTransitionMode`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SwipeView ios:SwipeView.SwipeTransitionMode="Drag">
            <SwipeView.LeftItems>
                <SwipeItems>
                    <SwipeItem Text="Delete"
                               IconImageSource="delete.png"
                               BackgroundColor="LightPink"
                               Invoked="OnDeleteSwipeItemInvoked" />
                </SwipeItems>
            </SwipeView.LeftItems>
            <!-- Content -->
        </SwipeView>
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

SwipeView swipeView = new Xamarin.Forms.SwipeView();
swipeView.On<iOS>().SetSwipeTransitionMode(SwipeTransitionMode.Drag);
// ...
```

O `SwipeView.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O método `SwipeView.SetSwipeTransitionMode`, no namespace [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , é usado para controlar a transição usada ao abrir uma `SwipeView`. A enumeração `SwipeTransitionMode` fornece dois valores possíveis:

- `Reveal` indica que os itens do dedo serão revelados, pois o conteúdo do `SwipeView` é transdedodo e é o valor padrão da propriedade `SwipeView.SwipeTransitionMode`.
- `Drag` indica que os itens do dedo serão arrastados para a exibição à medida que o conteúdo do `SwipeView` é transdedodo.

Além disso, o método `SwipeView.GetSwipeTransitionMode` pode ser usado para retornar o `SwipeTransitionMode` que é aplicado ao `SwipeView`.

O resultado é que um valor de `SwipeTransitionMode` especificado é aplicado ao `SwipeView`, que controla a transição usada ao abrir o `SwipeView`:

[![Captura de tela de SwipeView SwipeTransitionModes, no iOS](swipeview-swipetransitionmode-images/swipetransitionmode.png "SwipeTransitionModes no iOS")](swipeview-swipetransitionmode-images/swipetransitionmode-large.png#lightbox "SwipeTransitionModes no iOS")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
