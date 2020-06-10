---
Título: "início do indicador inicial na iOS" Descrição: "as especificações da plataforma permitem que você consuma a funcionalidade disponível apenas em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma do iOS específica que define a visibilidade do indicador inicial em uma página ".
MS. Prod: xamarin MS. AssetID: F81022E0-3C6C-49C0-A000-FAF6574D3FB7 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 05/09/2019 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="home-indicator-visibility-on-ios"></a>Visibilidade do indicador de página inicial no iOS

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

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

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
