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
ms.openlocfilehash: 61c0137788303363769fdec80a16542e2d8bea5e
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128603"
---
# <a name="tabbedpage-page-swiping-on-android"></a>TabbedPagendo o toque no Android

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do Android específica é usada para habilitar o passar o dedo com um gesto de dedo horizontal entre as páginas em um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Ele é consumido em XAML definindo a [`TabbedPage.IsSwipePagingEnabled`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty) Propriedade anexada como um `boolean` valor:

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.OffscreenPageLimit="2"
            android:TabbedPage.IsSwipePagingEnabled="true">
    ...
</TabbedPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetOffscreenPageLimit(2)
             .SetIsSwipePagingEnabled(true);
```

O `TabbedPage.On<Android>` método especifica que essa plataforma específica será executada somente no Android. O [ `TabbedPage.SetIsSwipePagingEnabled` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. SetIsSwipePagingEnabled ( Xamarin.Forms . O método vinculobject, System. Boolean), no [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usado para habilitar o passar o dedo entre as páginas em um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Além disso, a `TabbedPage` classe no `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` namespace também tem um [ `EnableSwipePaging` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. EnableSwipePaging ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage})), o método que habilita essa plataforma específica e um [ `DisableSwipePaging` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. DisableSwipePaging ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage})) que desabilita essa plataforma específica. A [`TabbedPage.OffscreenPageLimit`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty) Propriedade anexada e [ `SetOffscreenPageLimit` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. SetOffscreenPageLimit ( Xamarin.Forms . Vinculobject, System. Int32)), são usados para definir o número de páginas que devem ser retidas em um estado ocioso em cada lado da página atual.

O resultado é que o dedo de paginação pelas páginas exibidas por um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) está habilitado:

![](tabbedpage-page-swiping-images/tabbedpage-swipe.png)

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
