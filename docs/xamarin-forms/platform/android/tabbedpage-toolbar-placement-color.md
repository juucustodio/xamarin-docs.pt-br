---
title: Posicionamento e cor da barra de ferramentas TabbedPage no Android
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Android específica que define o posicionamento e a cor da barra de ferramentas em um TabbedPage.
ms.prod: xamarin
ms.assetid: A5C68D6A-9A5F-42EE-845D-1E5B0CB1544E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- ':::no-loc(Xamarin.Forms):::'
- ':::no-loc(Xamarin.Essentials):::'
ms.openlocfilehash: e32c516c4a0a8b12bd8a76478557905149890c6a
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997326"
---
# <a name="tabbedpage-toolbar-placement-and-color-on-android"></a>Posicionamento e cor da barra de ferramentas TabbedPage no Android

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

> [!IMPORTANT]
> As especificações de plataforma que definem a cor da barra de ferramentas em um [`TabbedPage`](xref::::no-loc(Xamarin.Forms):::.TabbedPage) agora são obsoletas e foram substituídas [`SelectedTabColor`](xref::::no-loc(Xamarin.Forms):::.TabbedPage.SelectedTabColor) pelas [`UnselectedTabColor`](xref::::no-loc(Xamarin.Forms):::.TabbedPage.UnselectedTabColor) Propriedades e. Para obter mais informações, consulte [criar um TabbedPage](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md#create-a-tabbedpage).

Essas especificações de plataforma são usadas para definir o posicionamento e a cor da barra de ferramentas em um [`TabbedPage`](xref::::no-loc(Xamarin.Forms):::.TabbedPage) . Eles são consumidos em XAML definindo a [`TabbedPage.ToolbarPlacement`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.TabbedPage.ToolbarPlacementProperty) Propriedade anexada como um valor da [`ToolbarPlacement`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) enumeração e as [`TabbedPage.BarItemColor`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.TabbedPage.BarItemColorProperty) [`TabbedPage.BarSelectedItemColor`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.TabbedPage.BarSelectedItemColorProperty) Propriedades anexadas e a [`Color`](xref::::no-loc(Xamarin.Forms):::.Color) :

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific;assembly=:::no-loc(Xamarin.Forms):::.Core"
            android:TabbedPage.ToolbarPlacement="Bottom"
            android:TabbedPage.BarItemColor="Black"
            android:TabbedPage.BarSelectedItemColor="Red">
    ...
</TabbedPage>
```

Como alternativa, eles podem ser consumidos em C# usando a API fluente:

```csharp
using :::no-loc(Xamarin.Forms):::.PlatformConfiguration;
using :::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetToolbarPlacement(ToolbarPlacement.Bottom)
             .SetBarItemColor(Color.Black)
             .SetBarSelectedItemColor(Color.Red);
```

O `TabbedPage.On<Android>` método especifica que essas especificações de plataforma serão executadas somente no Android. O [ `TabbedPage.SetToolbarPlacement` ] (xref: :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. AndroidSpecific. TabbedPage. SetToolbarPlacement ( :::no-loc(Xamarin.Forms)::: . IPlatformElementConfiguration { :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. Android, :::no-loc(Xamarin.Forms)::: . TabbedPage}, :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. AndroidSpecific. ToolbarPlacement)), no [`:::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific) namespace, é usado para definir o posicionamento da barra de ferramentas em um [`TabbedPage`](xref::::no-loc(Xamarin.Forms):::.TabbedPage) , com a [`ToolbarPlacement`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) Enumeração fornecendo os seguintes valores:

- [`Default`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Default)– indica que a barra de ferramentas é colocada no local padrão na página. Essa é a parte superior da página em telefones e a parte inferior da página em outros idiomas do dispositivo.
- [`Top`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Top)– indica que a barra de ferramentas é colocada na parte superior da página.
- [`Bottom`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Bottom)– indica que a barra de ferramentas é colocada na parte inferior da página.

Além disso, o [ `TabbedPage.SetBarItemColor` ] (xref: :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. AndroidSpecific. TabbedPage. SetBarItemColor ( :::no-loc(Xamarin.Forms)::: . IPlatformElementConfiguration { :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. Android, :::no-loc(Xamarin.Forms)::: . TabbedPage}, :::no-loc(Xamarin.Forms)::: . Cor)) e [ `TabbedPage.SetBarSelectedItemColor` ] (xref: :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. AndroidSpecific. TabbedPage. SetBarSelectedItemColor ( :::no-loc(Xamarin.Forms)::: . IPlatformElementConfiguration { :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. Android, :::no-loc(Xamarin.Forms)::: . TabbedPage}, :::no-loc(Xamarin.Forms)::: . Cor)) os métodos são usados para definir a cor dos itens da barra de ferramentas e dos itens da barra de ferramentas selecionados, respectivamente.

> [!NOTE]
> O [ `GetToolbarPlacement` ] (xref: :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. AndroidSpecific. TabbedPage. GetToolbarPlacement ( :::no-loc(Xamarin.Forms)::: . IPlatformElementConfiguration { :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. Android, :::no-loc(Xamarin.Forms)::: . TabbedPage})), [ `GetBarItemColor` ] (xref: :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. AndroidSpecific. TabbedPage. GetBarItemColor ( :::no-loc(Xamarin.Forms)::: . IPlatformElementConfiguration { :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. Android, :::no-loc(Xamarin.Forms)::: . TabbedPage})) e [ `GetBarSelectedItemColor` ] (xref: :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. AndroidSpecific. TabbedPage. GetBarSelectedItemColor ( :::no-loc(Xamarin.Forms)::: . IPlatformElementConfiguration { :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. Android, :::no-loc(Xamarin.Forms)::: . TabbedPage})), os métodos podem ser usados para recuperar o posicionamento e a cor da [`TabbedPage`](xref::::no-loc(Xamarin.Forms):::.TabbedPage) barra de ferramentas.

O resultado é que o posicionamento da barra de ferramentas, a cor dos itens da barra de ferramentas e a cor do item da barra de ferramentas selecionado podem ser definidos em um [`TabbedPage`](xref::::no-loc(Xamarin.Forms):::.TabbedPage) :

![Configuração da barra de ferramentas TabbedPage](tabbedpage-toolbar-placement-color-images/tabbedpage-toolbar-placement.png)

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.AppCompat)
