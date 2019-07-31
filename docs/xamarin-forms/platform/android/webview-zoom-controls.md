---
title: Zoom do WebView no Android
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como consumir a plataforma Android específica que permite o zoom em uma WebView.
ms.prod: xamarin
ms.assetid: DC1A3762-6A42-4298-929C-445F416C3E60
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: 2142882add91d613263d11fa4c1e6d7ad142c7c7
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655990"
---
# <a name="webview-zoom-on-android"></a>Zoom do WebView no Android

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do Android específica permite pinçar para zoom e um controle de zoom em um [`WebView`](xref:Xamarin.Forms.WebView). Ele é consumido em XAML definindo `WebView.EnableZoomControls` as `WebView.DisplayZoomControls` propriedades vinculáveis `boolean` e como valores:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView Source="https://www.xamarin.com"
             android:WebView.EnableZoomControls="true"
             android:WebView.DisplayZoomControls="true" />
</ContentPage>
```

A `WebView.EnableZoomControls` propriedade vinculável controla se o pinçar para zoom está habilitado [`WebView`](xref:Xamarin.Forms.WebView)no, e a `WebView.DisplayZoomControls` propriedade vinculável controla se os controles de `WebView`zoom estão sobrepostos no.

Como alternativa, a plataforma específica pode ser consumida C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>()
    .EnableZoomControls(true)
    .DisplayZoomControls(true);
```

O `WebView.On<Android>` método Especifica que este específicos da plataforma serão executado apenas no Android. O `WebView.EnableZoomControls` método, [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) no namespace, é usado para controlar se o pinçar para o [`WebView`](xref:Xamarin.Forms.WebView)zoom está habilitado no. O `WebView.DisplayZoomControls` método, no mesmo namespace, é usado para controlar se os controles de `WebView`zoom estão sobrepostos no. Além disso, os `WebView.ZoomControlsEnabled` métodos `WebView.ZoomControlsDisplayed` e podem ser usados para retornar se os controles de pinçar para zoom e zoom estão habilitados, respectivamente.

O resultado é que o pinçar para zoom pode ser habilitado em um [`WebView`](xref:Xamarin.Forms.WebView), e os controles de zoom podem ser sobrepostos `WebView`no:

[ ![Captura de tela do modo de exibição da Web com zoom no Android](webview-zoom-controls-images/webview-zoom.png "ampliado") ] (webview-zoom-controls-images/webview-zoom-large.png#lightbox "Exibição") do WebZoom

> [!IMPORTANT]
> Os controles de zoom devem ser habilitados e exibidos, por meio das respectivas propriedades ou métodos vinculáveis, a [`WebView`](xref:Xamarin.Forms.WebView)serem sobrepostos em um.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
