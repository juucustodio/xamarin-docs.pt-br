---
Título: "guia de layout de área segura no iOS" Descrição: "as especificações de plataforma permitem consumir funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma do iOS específica que garante que o conteúdo da página seja posicionado em uma área da tela que seja segura para todos os dispositivos que usam o iOS 11 e superior. "
MS. Prod: xamarin MS. AssetID: 2B6789C1-39B4-4C16-ADE1-3ED3378EAC63 MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 10/24/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="safe-area-layout-guide-on-ios"></a>Guia de layout de área segura no iOS

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma específica do iOS é usada para garantir que o conteúdo da página seja posicionado em uma área da tela que seja segura para todos os dispositivos que usam o iOS 11 e superior. Especificamente, ele ajudará a garantir que o conteúdo não seja recortado por cantos do dispositivo arredondado, pelo indicador inicial ou pelo invólucro do sensor em um iPhone X. Ele é consumido em XAML definindo a `Page.UseSafeArea` Propriedade anexada como um `boolean` valor:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Safe Area"
             ios:Page.UseSafeArea="true">
    <StackLayout>
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

O `Page.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `Page.SetUseSafeArea` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, controla se o guia de layout de área segura está habilitado.

O resultado é que o conteúdo da página pode ser posicionado em uma área da tela que é segura para todos os iPhones:

[![](page-safe-area-images/safe-area-layout.png "Safe Area Layout Guide")](page-safe-area-images/safe-area-layout-large.png#lightbox "Safe Area Layout Guide")

> [!NOTE]
> A área segura definida pela Apple é usada no Xamarin.Forms para definir a [`Page.Padding`](xref:Xamarin.Forms.Page.Padding) propriedade e substituirá quaisquer valores anteriores dessa propriedade que foram definidos.

A área segura pode ser personalizada por meio da recuperação de seu [`Thickness`](xref:Xamarin.Forms.Thickness) valor com o `Page.SafeAreaInsets` método do [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace. Em seguida, ele pode ser modificado conforme necessário e atribuído novamente à `Padding` propriedade no construtor de página ou [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) substituir:

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    var safeInsets = On<iOS>().SafeAreaInsets();
    safeInsets.Left = 20;
    Padding = safeInsets;
}
```

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
