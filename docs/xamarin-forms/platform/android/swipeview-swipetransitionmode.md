---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c420fe65b020067169230dd06dbcd5ce65c036ab
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128616"
---
# <a name="swipeview-swipe-transition-mode-on-android"></a>Modo de transição de toque SwipeView no Android

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma Android específica controla a transição usada ao abrir um `SwipeView` . Ele é consumido em XAML definindo a `SwipeView.SwipeTransitionMode` propriedade vinculável como um valor da `SwipeTransitionMode` enumeração:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core" >
    <StackLayout>
        <SwipeView android:SwipeView.SwipeTransitionMode="Drag">
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

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

SwipeView swipeView = new Xamarin.Forms.SwipeView();
swipeView.On<Android>().SetSwipeTransitionMode(SwipeTransitionMode.Drag);
// ...
```

O `SwipeView.On<Android>` método especifica que essa plataforma específica será executada somente no Android. O `SwipeView.SetSwipeTransitionMode` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para controlar a transição usada ao abrir um `SwipeView` . A `SwipeTransitionMode` enumeração fornece dois valores possíveis:

- `Reveal`indica que os itens do dedo serão revelados à medida que o `SwipeView` conteúdo é transdedodo e é o valor padrão da `SwipeView.SwipeTransitionMode` propriedade.
- `Drag`indica que os itens do dedo serão arrastados para a exibição à medida que o `SwipeView` conteúdo é transdedodo.

Além disso, o `SwipeView.GetSwipeTransitionMode` método pode ser usado para retornar o `SwipeTransitionMode` que é aplicado ao `SwipeView` .

O resultado é que um `SwipeTransitionMode` valor especificado é aplicado ao `SwipeView` , que controla a transição usada ao abrir o `SwipeView` :

[![Captura de tela de SwipeView SwipeTransitionModes, no Android](swipeview-swipetransitionmode-images/swipetransitionmode.png "SwipeTransitionModes no Android")](swipeview-swipetransitionmode-images/swipetransitionmode-large.png#lightbox "SwipeTransitionModes no Android")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
