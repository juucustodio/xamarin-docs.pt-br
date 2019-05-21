---
title: WebView conteúdo no Android misturadas
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como utilizar o Android específicos da plataforma que é exibido o conteúdo misto em uma exibição da Web em aplicativos que destino a API 21 ou maior.
ms.prod: xamarin
ms.assetid: 68F908F3-04C5-4B91-B6E5-B7E8357B4154
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 67ab68ceda69f9000bb160d1e443fd82ad7b61b3
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65924575"
---
# <a name="webview-mixed-content-on-android"></a>WebView conteúdo no Android misturadas

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

Este específicos da plataforma Android controla se um [ `WebView` ](xref:Xamarin.Forms.WebView) pode exibir conteúdo misto em aplicativos destinados a API 21 ou maior. Conteúdo misto é que é inicialmente carregada em uma conexão HTTPS, mas que carrega os recursos (como imagens, áudio, vídeo, folhas de estilo, scripts) em uma conexão HTTP. Ele é consumido em XAML, definindo o [ `WebView.MixedContentMode` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty) propriedade anexada a um valor da [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) enumeração:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

Como alternativa, ele pode ser consumido de C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

O `WebView.On<Android>` método Especifica que este específicos da plataforma serão executado apenas no Android. O [ `WebView.SetMixedContentMode` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.SetMixedContentMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.WebView},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)) método, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usada para controlar se o conteúdo misto pode ser exibido, com o [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) enumeração fornecendo três valores possíveis:

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) – indica que o [ `WebView` ](xref:Xamarin.Forms.WebView) permitirá que uma origem HTTPS carregar conteúdo de uma origem HTTP.
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) – indica que o [ `WebView` ](xref:Xamarin.Forms.WebView) não permitirá que uma origem HTTPS carregar conteúdo de uma origem HTTP.
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) – indica que o [ `WebView` ](xref:Xamarin.Forms.WebView) tentará ser compatível com a abordagem do navegador da web mais recente do dispositivo. Parte do conteúdo HTTP poderá ter permissão para ser carregado por uma origem HTTPS e outros tipos de conteúdo serão bloqueados. Os tipos de conteúdo que são bloqueados ou permitidos podem ser alterados a cada versão do sistema operacional.

O resultado é que a especificada [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) valor é aplicado para o [ `WebView` ](xref:Xamarin.Forms.WebView), que controla se o conteúdo misto pode ser exibido:

[![WebView misto de manipulação de conteúdo específico da plataforma](webview-mixed-content-images/webview-mixedcontent.png "WebView misto de manipulação de conteúdo específico da plataforma")](webview-mixed-content-images/webview-mixedcontent-large.png#lightbox "WebView misto de manipulação de conteúdo específico da plataforma")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
