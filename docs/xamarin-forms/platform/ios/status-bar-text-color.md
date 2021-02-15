---
title: Modo de cor de texto da barra de NavigationPage no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma específica do iOS que controla se a cor do texto da barra de status em uma NavigationPage corresponde à luminosidade da barra de navegação.
ms.prod: xamarin
ms.assetid: 03698A44-39F1-4030-9AF5-F10A6713828A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6aa435cebe1976897f8165d1e645179b1ca4aa9f
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940311"
---
# <a name="navigationpage-bar-text-color-mode-on-ios"></a>Modo de cor de texto da barra de NavigationPage no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma determina se a cor do texto da barra de status em uma [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) é ajustada para corresponder à luminosidade da barra de navegação. Ele é consumido em XAML definindo a [`NavigationPage.StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.StatusBarTextColorModeProperty) Propriedade anexada como um valor da [`StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) enumeração:

```xaml
<FlyoutPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    x:Class="PlatformSpecifics.iOSStatusBarTextColorModePage">
    <FlyoutPage.Flyout>
        <ContentPage Title="Flyout Page Title" />
    </FlyoutPage.Flyout>
    <FlyoutPage.Detail>
        <NavigationPage BarBackgroundColor="Blue" BarTextColor="White"
                        ios:NavigationPage.StatusBarTextColorMode="MatchNavigationBarTextLuminosity">
            <x:Arguments>
                <ContentPage>
                    <Label Text="Slide the master page to see the status bar text color mode change." />
                </ContentPage>
            </x:Arguments>
        </NavigationPage>
    </FlyoutPage.Detail>
</FlyoutPage>

```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

IsPresentedChanged += (sender, e) =>
{
    var flyoutPage = sender as FlyoutPage;
    if (flyoutPage.IsPresented)
        ((Xamarin.Forms.NavigationPage)flyoutPage.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.DoNotAdjust);
    else
        ((Xamarin.Forms.NavigationPage)flyoutPage.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.MatchNavigationBarTextLuminosity);
};
```

O `NavigationPage.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O [ `NavigationPage.SetStatusBarTextColorMode` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. SetStatusBarTextColorMode ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage}, Xamarin.Forms . PlatformConfiguration. iOSSpecific. StatusBarTextColorMode)), no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, controla se a cor do texto da barra de status no [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) é ajustada para corresponder à luminosidade da barra de navegação, com a [`StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) Enumeração fornecendo dois valores possíveis:

- [`DoNotAdjust`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.DoNotAdjust) – indica que a cor do texto da barra de status não deve ser ajustada.
- [`MatchNavigationBarTextLuminosity`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.MatchNavigationBarTextLuminosity) – indica que a cor do texto da barra de status deve corresponder à luminosidade da barra de navegação.

Além disso, o [ `GetStatusBarTextColorMode` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. GetStatusBarTextColorMode ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage})), o método pode ser usado para recuperar o valor atual da [`StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) Enumeração aplicada ao [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) .

O resultado é que a cor do texto da barra de status em um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) pode ser ajustada para corresponder à luminosidade da barra de navegação. Neste exemplo, a cor do texto da barra de status muda conforme o usuário alterna entre as [`Flyout`](xref:Xamarin.Forms.FlyoutPage.Flyout) [`Detail`](xref:Xamarin.Forms.FlyoutPage.Detail) páginas e de a [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) :

![Modo de cor de texto da barra de status Platform-Specific](status-bar-text-color-images/status-bar-text-color-mode.png)

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
