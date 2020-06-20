---
title: Altura da barra de NavigationPage no Android
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma do Android específica que define a altura da barra de navegação em um NavigationPage.
ms.prod: xamarin
ms.assetid: C8A73B64-FE70-408A-A72E-8AF147F0C52C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2dcabe3c0067734250834c2927fd4cbb83906943
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128785"
---
# <a name="navigationpage-bar-height-on-android"></a>Altura da barra de NavigationPage no Android

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esta plataforma Android específica define a altura da barra de navegação em um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . Ele é consumido em XAML definindo a [`NavigationPage.BarHeight`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) propriedade vinculável como um valor inteiro:

```xaml
<NavigationPage ...
                xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
                android:NavigationPage.BarHeight="450">
    ...
</NavigationPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;
...

public class AndroidNavigationPageCS : Xamarin.Forms.NavigationPage
{
    public AndroidNavigationPageCS()
    {
        On<Android>().SetBarHeight(450);
    }
}
```

O `NavigationPage.On<Android>` método especifica que essa plataforma específica será executada somente no Android compatível com o aplicativo. O [ `NavigationPage.SetBarHeight` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. AppCompat. NavigationPage. SetBarHeight ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . NavigationPage}, System. Int32)), no [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) namespace, é usado para definir a altura da barra de navegação em um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . Além disso, o [ `NavigationPage.GetBarHeight` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. AppCompat. NavigationPage. GetBarHeight ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . NavigationPage})), o método pode ser usado para retornar a altura da barra de navegação no `NavigationPage` .

O resultado é que a altura da barra de navegação em um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) pode ser definida:

![](navigationpage-bar-height-images/navigationpage-barheight.png "NavigationPage navigation bar height")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
