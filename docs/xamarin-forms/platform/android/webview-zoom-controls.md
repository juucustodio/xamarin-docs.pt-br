---
title: Zoom do WebView no Android
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Android específica que permite o zoom em uma WebView.
ms.prod: xamarin
ms.assetid: DC1A3762-6A42-4298-929C-445F416C3E60
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: 2142882add91d613263d11fa4c1e6d7ad142c7c7
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "68655990"
---
# <a name="webview-zoom-on-android"></a>Zoom do WebView no Android

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do Android específica permite pinçar para zoom e um controle de zoom em um [`WebView`](xref:Xamarin.Forms.WebView). Ele é consumido em XAML definindo o `WebView.EnableZoomControls` e `WebView.DisplayZoomControls` propriedades vinculáveis para `boolean` valores:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView Source="https://www.xamarin.com"
             android:WebView.EnableZoomControls="true"
             android:WebView.DisplayZoomControls="true" />
</ContentPage>
```

A propriedade `WebView.EnableZoomControls` vinculável controla se o pinçar para zoom está habilitado no [`WebView`](xref:Xamarin.Forms.WebView), e a propriedade `WebView.DisplayZoomControls` vinculável controla se os controles de zoom estão sobrepostos no `WebView`.

Como alternativa, a plataforma específica pode ser consumida C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>()
    .EnableZoomControls(true)
    .DisplayZoomControls(true);
```

O método `WebView.On<Android>` especifica que essa plataforma específica será executada somente no Android. O método `WebView.EnableZoomControls`, no namespace [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) , é usado para controlar se o pinçar para zoom está habilitado no [`WebView`](xref:Xamarin.Forms.WebView). O método `WebView.DisplayZoomControls`, no mesmo namespace, é usado para controlar se os controles de zoom estão sobrepostos na `WebView`. Além disso, os métodos `WebView.ZoomControlsEnabled` e `WebView.ZoomControlsDisplayed` podem ser usados para retornar se os controles de pinçar para zoom e zoom estão habilitados, respectivamente.

O resultado é que o pinçar para zoom pode ser habilitado em um [`WebView`](xref:Xamarin.Forms.WebView), e os controles de zoom podem ser sobrepostos na `WebView`:

[![Captura de tela de WebView com zoom no Android](webview-zoom-controls-images/webview-zoom.png "Exibição do WebZoom")](webview-zoom-controls-images/webview-zoom-large.png#lightbox "Exibição do WebZoom")

> [!IMPORTANT]
> Os controles de zoom devem ser habilitados e exibidos, por meio das respectivas propriedades ou métodos vinculáveis, a serem sobrepostos em um [`WebView`](xref:Xamarin.Forms.WebView).

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
