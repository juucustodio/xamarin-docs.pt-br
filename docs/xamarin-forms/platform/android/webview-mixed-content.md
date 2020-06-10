---
Título: "conteúdo misto do WebView no Android" Descrição: "as especificações da plataforma permitem consumir funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Android específica que exibe conteúdo misto em uma WebView em aplicativos direcionados à API 21 ou superior. "
MS. Prod: xamarin MS. AssetID: 68F908F3-04C5-4B91-B6E5-B7E8357B4154 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 07/10/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="webview-mixed-content-on-android"></a>Conteúdo misto do WebView no Android

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do Android controla se um [`WebView`](xref:Xamarin.Forms.WebView) pode exibir conteúdo misto em aplicativos direcionados à API 21 ou superior. Conteúdo misto é o conteúdo que é inicialmente carregado em uma conexão HTTPS, mas que carrega recursos (como imagens, áudio, vídeo, folhas de estilo, scripts) em uma conexão HTTP. Ele é consumido em XAML definindo a [`WebView.MixedContentMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty) Propriedade anexada como um valor da [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) enumeração:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

O `WebView.On<Android>` método especifica que essa plataforma específica será executada somente no Android. O [ `WebView.SetMixedContentMode` ] (xref: Xamarin.Forms . PlatformConfiguration. AndroidSpecific. WebView. SetMixedContentMode ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . WebView}, Xamarin.Forms . PlatformConfiguration. AndroidSpecific. MixedContentHandling)), no [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usado para controlar se o conteúdo misto pode ser exibido, com a [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) Enumeração fornecendo três valores possíveis:

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow)– indica que o [`WebView`](xref:Xamarin.Forms.WebView) permitirá que uma origem https carregue o conteúdo de uma origem http.
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow)– indica que o [`WebView`](xref:Xamarin.Forms.WebView) não permitirá que uma origem https carregue conteúdo de uma origem http.
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode)– indica que o [`WebView`](xref:Xamarin.Forms.WebView) tentará ser compatível com a abordagem do navegador da Web do dispositivo mais recente. Algum conteúdo HTTP pode ter permissão para ser carregado por uma origem HTTPS e outros tipos de conteúdo serão bloqueados. Os tipos de conteúdo bloqueados ou permitidos podem mudar com cada versão do sistema operacional.

O resultado é que um [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) valor especificado é aplicado ao [`WebView`](xref:Xamarin.Forms.WebView) , que controla se o conteúdo misto pode ser exibido:

[![Plataforma de tratamento de conteúdo misto da WebView-específico](webview-mixed-content-images/webview-mixedcontent.png "Plataforma de tratamento de conteúdo misto da WebView-específico")](webview-mixed-content-images/webview-mixedcontent-large.png#lightbox "Plataforma de tratamento de conteúdo misto da WebView-específico")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
