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
- ':::no-loc(Xamarin.Forms):::'
- ':::no-loc(Xamarin.Essentials):::'
ms.openlocfilehash: 0043d90e631c19a55b766a877a9cd30316f14650
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997368"
---
# <a name="tabbedpage-page-swiping-on-android"></a>TabbedPagendo o toque no Android

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do Android específica é usada para habilitar o passar o dedo com um gesto de dedo horizontal entre as páginas em um [`TabbedPage`](xref::::no-loc(Xamarin.Forms):::.TabbedPage) . Ele é consumido em XAML definindo a [`TabbedPage.IsSwipePagingEnabled`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty) Propriedade anexada como um `boolean` valor:

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific;assembly=:::no-loc(Xamarin.Forms):::.Core"
            android:TabbedPage.OffscreenPageLimit="2"
            android:TabbedPage.IsSwipePagingEnabled="true">
    ...
</TabbedPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using :::no-loc(Xamarin.Forms):::.PlatformConfiguration;
using :::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetOffscreenPageLimit(2)
             .SetIsSwipePagingEnabled(true);
```

O `TabbedPage.On<Android>` método especifica que essa plataforma específica será executada somente no Android. O [ `TabbedPage.SetIsSwipePagingEnabled` ] (xref: :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. AndroidSpecific. TabbedPage. SetIsSwipePagingEnabled ( :::no-loc(Xamarin.Forms)::: . O método vinculobject, System. Boolean), no [`:::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific) namespace, é usado para habilitar o passar o dedo entre as páginas em um [`TabbedPage`](xref::::no-loc(Xamarin.Forms):::.TabbedPage) . Além disso, a `TabbedPage` classe no `:::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific` namespace também tem um [ `EnableSwipePaging` ] (xref: :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. AndroidSpecific. TabbedPage. EnableSwipePaging ( :::no-loc(Xamarin.Forms)::: . IPlatformElementConfiguration { :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. Android, :::no-loc(Xamarin.Forms)::: . TabbedPage})), o método que habilita essa plataforma específica e um [ `DisableSwipePaging` ] (xref: :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. AndroidSpecific. TabbedPage. DisableSwipePaging ( :::no-loc(Xamarin.Forms)::: . IPlatformElementConfiguration { :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. Android, :::no-loc(Xamarin.Forms)::: . TabbedPage})) que desabilita essa plataforma específica. A [`TabbedPage.OffscreenPageLimit`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty) Propriedade anexada e [ `SetOffscreenPageLimit` ] (xref: :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. AndroidSpecific. TabbedPage. SetOffscreenPageLimit ( :::no-loc(Xamarin.Forms)::: . Vinculobject, System. Int32)), são usados para definir o número de páginas que devem ser retidas em um estado ocioso em cada lado da página atual.

O resultado é que o dedo de paginação pelas páginas exibidas por um [`TabbedPage`](xref::::no-loc(Xamarin.Forms):::.TabbedPage) está habilitado:

![Passar a paginação por meio de um TabbedPage](tabbedpage-page-swiping-images/tabbedpage-swipe.png)

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.AppCompat)
