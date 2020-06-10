---
Título: "separador de barra de NavigationPage no iOS" Descrição: "as especificações de plataforma permitem que você consuma a funcionalidade disponível apenas em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma específica do iOS que oculta a linha separadora e sombra que está na parte inferior da barra de navegação em um NavigationPage. "
MS. Prod: xamarin MS. AssetID: 5A45748A-6779-4441-82F2-415BD68473B9 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 10/24/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="navigationpage-bar-separator-on-ios"></a>Separador de barra NavigationPage no iOS

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do iOS específica oculta a linha separadora e sombra que está na parte inferior da barra de navegação em um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . Ele é consumido em XAML definindo a [`NavigationPage.HideNavigationBarSeparator`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) propriedade vinculável como `false` :

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ios:NavigationPage.HideNavigationBarSeparator="true">

</NavigationPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;

public class iOSTitleViewNavigationPageCS : Xamarin.Forms.NavigationPage
{
    public iOSTitleViewNavigationPageCS()
    {
        On<iOS>().SetHideNavigationBarSeparator(true);
    }
}
```

O `NavigationPage.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O [ `NavigationPage.SetHideNavigationBarSeparator` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. SetHideNavigationBarSeparator ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage}, System. Boolean)), no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para controlar se o separador da barra de navegação está oculto. Além disso, o [ `NavigationPage.HideNavigationBarSeparator` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. HideNavigationBarSeparator ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage})), o método pode ser usado para retornar se o separador da barra de navegação está oculto.

O resultado é que o separador de barra de navegação em um [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) pode ser ocultado:

![](navigation-bar-separator-images/navigationpage-hideseparatorbar.png "NavigationPage navigation bar hidden")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
