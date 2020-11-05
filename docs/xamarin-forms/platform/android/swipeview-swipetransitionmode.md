---
title: Modo de transição de toque SwipeView no Android
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Android específica que controla a transição usada ao abrir um SwipeView.
ms.prod: xamarin
ms.assetid: 6B1F8213-9D62-4C40-9F04-881F1371B5AA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a848b9b58961a643ed674f5a61ac38b7b2dc8334
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366913"
---
# <a name="swipeview-swipe-transition-mode-on-android"></a>Modo de transição de toque SwipeView no Android

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

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

- `Reveal` indica que os itens do dedo serão revelados à medida que o `SwipeView` conteúdo é transdedodo e é o valor padrão da `SwipeView.SwipeTransitionMode` propriedade.
- `Drag` indica que os itens do dedo serão arrastados para a exibição à medida que o `SwipeView` conteúdo é transdedodo.

Além disso, o `SwipeView.GetSwipeTransitionMode` método pode ser usado para retornar o `SwipeTransitionMode` que é aplicado ao `SwipeView` .

O resultado é que um `SwipeTransitionMode` valor especificado é aplicado ao `SwipeView` , que controla a transição usada ao abrir o `SwipeView` :

[![Captura de tela de SwipeView SwipeTransitionModes, no Android](swipeview-swipetransitionmode-images/swipetransitionmode.png "SwipeTransitionModes no Android")](swipeview-swipetransitionmode-images/swipetransitionmode-large.png#lightbox "SwipeTransitionModes no Android")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)