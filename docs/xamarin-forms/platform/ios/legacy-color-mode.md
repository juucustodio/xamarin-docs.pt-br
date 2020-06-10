---
Título: "modo de cor herdado do Visualelement na iOS" Descrição: "as especificações da plataforma permitem consumir funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma específica do iOS que desabilita o Xamarin.Forms modo de cor herdado. "
MS. Prod: xamarin MS. AssetID: 60FFBA67-6E06-439B-A5EB-8C808285E2CD MS. Technology: xamarin-Forms autor: davidbritch MS. Author: dabritch MS. Date: 10/24/2018 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="visualelement-legacy-color-mode-on-ios"></a>Modo de cor herdado do visualelement no iOS

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Algumas das Xamarin.Forms exibições apresentam um modo de cor herdado. Nesse modo, quando a [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) propriedade da exibição é definida como `false` , a exibição substituirá as cores definidas pelo usuário com as cores nativas padrão para o estado desabilitado. Para compatibilidade com versões anteriores, esse modo de cores herdado permanece como o comportamento padrão para exibições com suporte.

Essa plataforma do iOS específica desabilita esse modo de cor herdado em um [`VisualElement`](xref:Xamarin.Forms.VisualElement) , de modo que as cores definidas em uma exibição pelo usuário permaneçam mesmo quando a exibição estiver desabilitada. Ele é consumido em XAML definindo a [`VisualElement.IsLegacyColorModeEnabled`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsLegacyColorModeEnabledProperty) Propriedade anexada como `false` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                ios:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

_legacyColorModeDisabledButton.On<iOS>().SetIsLegacyColorModeEnabled(false);
```

O `VisualElement.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O [ `VisualElement.SetIsLegacyColorModeEnabled` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Visualelement. SetIsLegacyColorModeEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Visualelement}, System. Boolean)), no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para controlar se o modo de cor herdado está desabilitado. Além disso, o [ `VisualElement.GetIsLegacyColorModeEnabled` ] (xref: Xamarin.Forms . PlatformConfiguration. iOSSpecific. Visualelement. GetIsLegacyColorModeEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Visualelement})), o método pode ser usado para retornar se o modo de cor herdado está desabilitado.

O resultado é que o modo de cor herdado pode ser desabilitado, de modo que as cores definidas em uma exibição pelo usuário permaneçam mesmo quando a exibição estiver desabilitada:

![](legacy-color-mode-images/legacy-color-mode-disabled.png "Legacy color mode disabled")

> [!NOTE]
> Ao definir um [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) em uma exibição, o modo de cor herdado é completamente ignorado. Para obter mais informações sobre os Estados visuais, consulte [o Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
