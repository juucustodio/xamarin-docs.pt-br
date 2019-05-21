---
title: Zoom WebView no Android
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como utilizar o Android específicos da plataforma que permite que o zoom em uma exibição da Web.
ms.prod: xamarin
ms.assetid: DC1A3762-6A42-4298-929C-445F416C3E60
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: 0e180ca5019bd4e5d1351cfb2f7a8c28ade2afe2
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65971638"
---
# <a name="webview-zoom-on-android"></a>Zoom WebView no Android

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Este específicos da plataforma Android habilita pinçar para ampliar e um controle de zoom em uma [ `WebView` ](xref:Xamarin.Forms.WebView). Ele é consumido em XAML, definindo o `WebView.EnableZoomControls` e `WebView.DisplayZoomControls` propriedades vinculáveis para `boolean` valores:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView Source="https://www.xamarin.com"
             android:WebView.EnableZoomControls="true"
             android:WebView.DisplayZoomControls="true" />
</ContentPage>
```

O `WebView.EnableZoomControls` propriedade associável controla se pinçar para ampliar está habilitada no [ `WebView` ](xref:Xamarin.Forms.WebView)e o `WebView.DisplayZoomControls` propriedade associável controla se os controles de zoom estão sobrepostos no `WebView`.

Como alternativa, a plataforma específica pode ser consumida de C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>()
    .EnableZoomControls(true)
    .DisplayZoomControls(true);
```

O `WebView.On<Android>` método Especifica que este específicos da plataforma serão executado apenas no Android. O `WebView.EnableZoomControls` método, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usada para controlar se pinçar para ampliar está habilitada no [ `WebView` ](xref:Xamarin.Forms.WebView). O `WebView.DisplayZoomControls` método, no mesmo namespace, é usado para controlar se os controles de zoom estão sobrepostos no `WebView`. Além disso, o `WebView.ZoomControlsEnabled` e `WebView.ZoomControlsDisplayed` métodos podem ser usados para retornar se controles de pinçar para ampliar e zoom estão habilitados, respectivamente.

O resultado é que essa situação de emergência para zoom pode ser habilitada em um [ `WebView` ](xref:Xamarin.Forms.WebView), e controles de zoom podem ser sobreposta durante o `WebView`:

[![Captura de tela do WebView com zoom no Android](webview-zoom-controls-images/webview-zoom.png "ampliado WebView")](webview-zoom-controls-images/webview-zoom-large.png#lightbox "ampliado WebView")

> [!IMPORTANT]
> Controles de zoom devem ser habilitados e exibidos, por meio do respectivas propriedades vinculáveis ou métodos, para ser sobreposto em um [ `WebView` ](xref:Xamarin.Forms.WebView).

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
