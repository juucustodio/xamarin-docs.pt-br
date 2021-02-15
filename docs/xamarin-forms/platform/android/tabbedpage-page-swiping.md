---
title: TabbedPagendo o toque no Android
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Android específica que permite passar o dedo com um gesto de dedo horizontal entre as páginas de um TabbedPage.
ms.prod: xamarin
ms.assetid: D1C09CCB-7246-41A4-8BD2-FA6FABCF1C72
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 96e05e29c6488113f31c05020870ca9e9a73f3f5
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368616"
---
# <a name="tabbedpage-page-swiping-on-android"></a>TabbedPagendo o toque no Android

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

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

![Passar a paginação por meio de um TabbedPage](tabbedpage-page-swiping-images/tabbedpage-swipe.png)

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)