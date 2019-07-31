---
title: Visibilidade do indicador de página inicial no iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como consumir a plataforma específica do iOS que define a visibilidade do indicador inicial em uma página.
ms.prod: xamarin
ms.assetid: F81022E0-3C6C-49C0-A000-FAF6574D3FB7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: c8acf826eb5b3f42f62803ac1caaaa5929c5ea75
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648865"
---
# <a name="home-indicator-visibility-on-ios"></a>Visibilidade do indicador de página inicial no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do iOS específica define a visibilidade do indicador inicial em um [`Page`](xref:Xamarin.Forms.Page). Ele é consumido em XAML definindo [`Page.PrefersHomeIndicatorAutoHidden`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Page.PrefersHomeIndicatorAutoHiddenProperty) a propriedade vinculável `boolean`como a:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersHomeIndicatorAutoHidden="true">
    ...
</ContentPage>
```

Como alternativa, ele pode ser consumido de C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersHomeIndicatorAutoHidden(true);
```

O `Page.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O [`Page.SetPrefersHomeIndicatorAutoHidden`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Page.SetPrefersHomeIndicatorAutoHidden(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Page},System.Boolean)) método, [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) no namespace, controla a visibilidade do indicador inicial. Além disso, o [`Page.PrefersHomeIndicatorAutoHidden`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Page.PrefersHomeIndicatorAutoHidden(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Page})) método pode ser usado para recuperar a visibilidade do indicador inicial.

O resultado é que a visibilidade do indicador inicial em um [`Page`](xref:Xamarin.Forms.Page) pode ser controlada:

![Captura de tela da visibilidade do indicador inicial em uma página do IOS](page-home-indicator-images/home-indicator-visibility.png "Visibilidade do indicador página inicial")

> [!NOTE]
> Essa plataforma específica pode [`ContentPage`](xref:Xamarin.Forms.ContentPage)ser aplicada a objetos, [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage), [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)e. [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
