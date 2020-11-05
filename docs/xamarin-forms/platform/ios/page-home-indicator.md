---
title: Visibilidade do indicador de página inicial no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma específica do iOS que define a visibilidade do indicador inicial em uma página.
ms.prod: xamarin
ms.assetid: F81022E0-3C6C-49C0-A000-FAF6574D3FB7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 49fd8c563509d88b80fcb91b5e6298b8a5bb3363
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372295"
---
# <a name="home-indicator-visibility-on-ios"></a>Visibilidade do indicador de página inicial no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do iOS específica define a visibilidade do indicador inicial em um [`Page`](xref:Xamarin.Forms.Page) . Ele é consumido em XAML definindo a [`Page.PrefersHomeIndicatorAutoHidden`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Page.PrefersHomeIndicatorAutoHiddenProperty) propriedade vinculável como a `boolean` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersHomeIndicatorAutoHidden="true">
    ...
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersHomeIndicatorAutoHidden(true);
```

O `Page.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O [ `Page.SetPrefersHomeIndicatorAutoHidden` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Page. SetPrefersHomeIndicatorAutoHidden ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Page}, System. Boolean)), no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, controla a visibilidade do indicador inicial. Além disso, o [ `Page.PrefersHomeIndicatorAutoHidden` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Page. PrefersHomeIndicatorAutoHidden ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Page})), o método pode ser usado para recuperar a visibilidade do indicador inicial.

O resultado é que a visibilidade do indicador inicial em um [`Page`](xref:Xamarin.Forms.Page) pode ser controlada:

![Captura de tela da visibilidade do indicador inicial em uma página do iOS](page-home-indicator-images/home-indicator-visibility.png "Visibilidade do indicador de página inicial")

> [!NOTE]
> Essa plataforma específica pode ser aplicada a [`ContentPage`](xref:Xamarin.Forms.ContentPage) objetos, [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) , [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) e [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) .

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)