---
title: Zoom do WebView no Android
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Android específica que permite o zoom em uma WebView.
ms.prod: xamarin
ms.assetid: DC1A3762-6A42-4298-929C-445F416C3E60
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 12c8eafd9768ee0da232ead4aac6cd6bdc3c9a2d
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93369604"
---
# <a name="webview-zoom-on-android"></a>Zoom do WebView no Android

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do Android específica permite pinçar para zoom e um controle de zoom em um [`WebView`](xref:Xamarin.Forms.WebView) . Ele é consumido em XAML definindo `WebView.EnableZoomControls` as `WebView.DisplayZoomControls` propriedades vinculáveis e como `boolean` valores:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView Source="https://www.xamarin.com"
             android:WebView.EnableZoomControls="true"
             android:WebView.DisplayZoomControls="true" />
</ContentPage>
```

A `WebView.EnableZoomControls` propriedade vinculável controla se o pinçar para zoom está habilitado no [`WebView`](xref:Xamarin.Forms.WebView) , e a `WebView.DisplayZoomControls` propriedade vinculável controla se os controles de zoom estão sobrepostos no `WebView` .

Como alternativa, a plataforma específica pode ser consumida em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>()
    .EnableZoomControls(true)
    .DisplayZoomControls(true);
```

O `WebView.On<Android>` método especifica que essa plataforma específica será executada somente no Android. O `WebView.EnableZoomControls` método, no [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usado para controlar se o pinçar para o zoom está habilitado no [`WebView`](xref:Xamarin.Forms.WebView) . O `WebView.DisplayZoomControls` método, no mesmo namespace, é usado para controlar se os controles de zoom estão sobrepostos no `WebView` . Além disso, os `WebView.ZoomControlsEnabled` `WebView.ZoomControlsDisplayed` métodos e podem ser usados para retornar se os controles de pinçar para zoom e zoom estão habilitados, respectivamente.

O resultado é que o pinçar para zoom pode ser habilitado em um [`WebView`](xref:Xamarin.Forms.WebView) , e os controles de zoom podem ser sobrepostos no `WebView` :

[![Captura de tela de WebView com zoom no Android](webview-zoom-controls-images/webview-zoom.png "Exibição do WebZoom")](webview-zoom-controls-images/webview-zoom-large.png#lightbox "Exibição do WebZoom")

> [!IMPORTANT]
> Os controles de zoom devem ser habilitados e exibidos, por meio das respectivas propriedades ou métodos vinculáveis, a serem sobrepostos em um [`WebView`](xref:Xamarin.Forms.WebView) .

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)