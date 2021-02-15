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
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8dc17ea4dba8257ef82c33aa87d6ffc9974658ac
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374180"
---
# <a name="tabbedpage-toolbar-placement-and-color-on-android"></a>Posicionamento e cor da barra de ferramentas TabbedPage no Android

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

> [!IMPORTANT]
> As especificações de plataforma que definem a cor da barra de ferramentas em um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) agora são obsoletas e foram substituídas [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor) pelas [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor) Propriedades e. Para obter mais informações, consulte [criar um TabbedPage](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md#create-a-tabbedpage).

Essas especificações de plataforma são usadas para definir o posicionamento e a cor da barra de ferramentas em um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Eles são consumidos em XAML definindo a [`TabbedPage.ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.ToolbarPlacementProperty) Propriedade anexada como um valor da [`ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) enumeração e as [`TabbedPage.BarItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarItemColorProperty) [`TabbedPage.BarSelectedItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarSelectedItemColorProperty) Propriedades anexadas e a [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.ToolbarPlacement="Bottom"
            android:TabbedPage.BarItemColor="Black"
            android:TabbedPage.BarSelectedItemColor="Red">
    ...
</TabbedPage>
```

Como alternativa, eles podem ser consumidos em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetToolbarPlacement(ToolbarPlacement.Bottom)
             .SetBarItemColor(Color.Black)
             .SetBarSelectedItemColor(Color.Red);
```

O `TabbedPage.On<Android>` método especifica que essas especificações de plataforma serão executadas somente no Android. O [ `TabbedPage.SetToolbarPlacement` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. SetToolbarPlacement ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage}, Xamarin.Forms . PlatformConfiguration. AndroidSpecific. ToolbarPlacement)), no [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usado para definir o posicionamento da barra de ferramentas em um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) , com a [`ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) Enumeração fornecendo os seguintes valores:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Default) – indica que a barra de ferramentas é colocada no local padrão na página. Essa é a parte superior da página em telefones e a parte inferior da página em outros idiomas do dispositivo.
- [`Top`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Top) – indica que a barra de ferramentas é colocada na parte superior da página.
- [`Bottom`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Bottom) – indica que a barra de ferramentas é colocada na parte inferior da página.

Além disso, o [ `TabbedPage.SetBarItemColor` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. SetBarItemColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage}, Xamarin.Forms . Cor)) e [ `TabbedPage.SetBarSelectedItemColor` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. SetBarSelectedItemColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage}, Xamarin.Forms . Cor)) os métodos são usados para definir a cor dos itens da barra de ferramentas e dos itens da barra de ferramentas selecionados, respectivamente.

> [!NOTE]
> O [ `GetToolbarPlacement` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. GetToolbarPlacement ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage})), [ `GetBarItemColor` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. GetBarItemColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage})) e [ `GetBarSelectedItemColor` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. GetBarSelectedItemColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage})), os métodos podem ser usados para recuperar o posicionamento e a cor da [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) barra de ferramentas.

O resultado é que o posicionamento da barra de ferramentas, a cor dos itens da barra de ferramentas e a cor do item da barra de ferramentas selecionado podem ser definidos em um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) :

![Configuração da barra de ferramentas TabbedPage](tabbedpage-toolbar-placement-color-images/tabbedpage-toolbar-placement.png)

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)