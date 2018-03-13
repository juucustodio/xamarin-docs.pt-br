---
title: "iOS específicos de plataforma"
description: "Específicos de plataforma permitem consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo demonstra como utilizar os iOS plataforma específicos que são integrados ao xamarin. Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: C0837996-A1E8-47F9-B3A8-98EE43B4A675
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/16/2017
ms.openlocfilehash: 798bb2b15534a620acbe76080e171af1a548ac25
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="ios-platform-specifics"></a>iOS específicos de plataforma

_Específicos de plataforma permitem consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo demonstra como utilizar os iOS plataforma específicos que são integrados ao xamarin. Forms._

No iOS, xamarin. Forms contém as seguintes especificações de plataforma:

- Desfoque dá suporte para nenhum [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/). Para obter mais informações, consulte [Aplicar desfoque](#blur).
- Controlar se o título da página é exibido como título na barra de navegação de página grande. Para obter mais informações, consulte [exibir títulos grandes](#large_title).
- Garantindo a página conteúda está posicionado em uma área da tela que é segura para todos os dispositivos iOS. Para obter mais informações, consulte [habilitar o guia de Layout de área de segurança](#safe_area_layout).
- Uma barra de navegação transparente. Para obter mais informações, consulte [fazer a barra de navegação transparente](#translucent_navigation_bar).
- Controlar se o texto da barra de status de cores em um [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) é ajustado para corresponder à luminosidade da barra de navegação. Para obter mais informações, consulte [ajustando o modo de cor do texto de barra de Status](#status_bar_color_mode).
- Garantir que inseridos texto caiba em uma [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) ajustando o tamanho da fonte. Para obter mais informações, consulte [ajustando o tamanho da fonte de uma entrada](#adjust_font_size).
- Controlar quando a seleção de itens ocorre em um [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/). Para obter mais informações, consulte [controlando a seleção de Item do seletor](#picker_update_mode).
- Definir a visibilidade da barra de status em um [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/). Para obter mais informações, consulte [definindo a visibilidade da barra de Status em uma página](#set_status_bar_visibility).
- Controlar se uma [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) trata um gesto de toque ou passá-lo para seu conteúdo. Para obter mais informações, consulte [atrasar ajustes conteúdo em um ScrollView](#delay_content_touches).

<a name="blur" />

## <a name="applying-blur"></a>Aplicação de desfoque

Essa plataforma específica é usada para desfocar o conteúdo em camadas abaixo dela e é consumida em XAML, definindo o [ `VisualElement.BlurEffect` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.BlurEffectProperty/) propriedade anexada a um valor da [ `BlurEffectStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/) enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
  ...
  <AbsoluteLayout HorizontalOptions="Center">
    <Image Source="monkeyface.png" />
    <BoxView x:Name="boxView" ios:VisualElement.BlurEffect="ExtraLight" HeightRequest="300" WidthRequest="300" />
  </AbsoluteLayout>
  ...
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

boxView.On<iOS>().UseBlurEffect(BlurEffectStyle.ExtraLight);
```

O `BoxView.On<iOS>` método Especifica que este específica de plataforma será executado apenas no iOS. O [ `VisualElement.UseBlurEffect` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.UseBlurEffect/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement}/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) namespace, é usado para aplicar o efeito de desfoque, com o [ `BlurEffectStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/) enumeração fornecendo quatro valores: [ `None` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.None/), [ `ExtraLight` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.ExtraLight/), [ `Light` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Light/), e [ `Dark` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Dark/).

O resultado é que a especificada [ `BlurEffectStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/) é aplicada para o [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) de instância que Desfoca a [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) em camadas abaixo dela:

![](ios-images/blur-effect.png "Desfoque efeito específico da plataforma")

<a name="large_title" />

## <a name="displaying-large-titles"></a>Exibir títulos grandes

Essa plataforma específica é usada para exibir o título da página como um título grande na barra de navegação, para dispositivos que usam iOS 11 ou superior. Um título grande é alinhado à esquerda e usa uma fonte maior e faz a transição para um título padrão como o usuário começa a rolagem de conteúdo, para que o espaço de tela é usado com eficiência. No entanto, na orientação paisagem, o título retornará para o centro da barra de navegação para otimizar o layout do conteúdo. Ele é consumido em XAML, definindo o `NavigationPage.PrefersLargeTitles` anexado a propriedade para um `boolean` valor:

```xaml
<NavigationPage xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ...
                ios:NavigationPage.PrefersLargeTitles="true">
  ...
</NavigationPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var navigationPage = new Xamarin.Forms.NavigationPage(new iOSLargeTitlePageCS());
navigationPage.On<iOS>().SetPrefersLargeTitles(true);
```

O `NavigationPage.On<iOS>` método Especifica que este específica de plataforma será executado apenas no iOS. O `NavigationPage.SetPrefersLargeTitle` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) namespace, controla se os títulos grandes estão habilitados.

Desde que os títulos grandes são habilitados a [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/), todas as páginas na pilha de navegação exibirá títulos grandes. Esse comportamento pode ser substituído nas páginas, definindo o `Page.LargeTitleDisplay` propriedade anexada a um valor da `LargeTitleDisplayMode` enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

Como alternativa, o comportamento de página pode ser substituído em c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSLargeTitlePageCS : ContentPage
{
    public iOSLargeTitlePageCS(ICommand restore)
    {
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        ...
    }
    ...
}
```

O `Page.On<iOS>` método Especifica que este específica de plataforma será executado apenas no iOS. O `Page.SetLargeTitleDisplay` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) namespace, controla o comportamento de título grande no [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), com o `LargeTitleDisplayMode` fornecer três possíveis de enumeração valores:

- `Always` – forçar a barra de navegação e a fonte tamanho para usar o formato grande.
- `Automatic` – use o mesmo estilo (pequeno ou grande) como o item anterior na pilha de navegação.
- `Never` – forçar o uso da barra de navegação formato regular, pequenos.

Além disso, o `SetLargeTitleDisplay` método pode ser usado para alternar os valores de enumeração chamando o `LargeTitleDisplay` método, que retorna atual `LargeTitleDisplayMode`:

```csharp
switch (On<iOS>().LargeTitleDisplay())
{
    case LargeTitleDisplayMode.Always:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Automatic);
        break;
    case LargeTitleDisplayMode.Automatic:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        break;
    case LargeTitleDisplayMode.Never:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Always);
        break;
}
```

O resultado é que a especificada `LargeTitleDisplayMode` é aplicada para o [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), que controla o comportamento de título grande:

![](ios-images/large-title.png "Desfoque efeito específico da plataforma")

<a name="safe_area_layout" />

## <a name="enabling-the-safe-area-layout-guide"></a>Habilitar o guia de Layout de área de segurança

Essa plataforma específica é usada para garantir que o conteúdo da página está posicionado em uma área da tela que é segura para todos os dispositivos que usam iOS 11 e superior. Especificamente, ele ajudará a garantir que o conteúdo não é recortado cantos arredondados dispositivo, o indicador inicial ou a estrutura de sensor em um iPhone X. Ele é consumido em XAML, definindo o `Page.UseSafeArea` anexado a propriedade para um `boolean` valor:

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

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

O `Page.On<iOS>` método Especifica que este específica de plataforma será executado apenas no iOS. O `Page.SetUseSafeArea` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) namespace, controla se o guia de layout de área de segurança está habilitado.

O resultado é que o conteúdo da página pode ser posicionado em uma área da tela que é segura para todos os iPhones:

[![](ios-images/safe-area-layout.png "Guia de Layout de área de segurança")](ios-images/safe-area-layout-large.png#lightbox "guia de Layout de área de segurança")

> [!NOTE]
> A área de segurança definida pela Apple é usada em xamarin. Forms para definir o [ `Page.Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Padding/) propriedade e substituirão quaisquer valores anteriores desta propriedade que foram definidos.

A área de segurança pode ser personalizada, recuperando seus [ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/) valor com o `Page.SafeAreaInsets` método do [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) namespace. Em seguida, podem ser modificada como necessárias e cedido ao `Padding` propriedade no construtor da página ou [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing()/) substituir:

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    var safeInsets = On<iOS>().SafeAreaInsets();
    safeInsets.Left = 20;
    Padding = safeInsets;
}
```

<a name="translucent_navigation_bar" />

## <a name="making-the-navigation-bar-translucent"></a>Tornando transparente a barra de navegação

Essa plataforma específica é usada para alterar a transparência da barra de navegação e consumida em XAML, definindo o [ `NavigationPage.IsNavigationBarTranslucent` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucentProperty/) anexado a propriedade para um `boolean` valor:

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                BackgroundColor="Blue"
                ios:NavigationPage.IsNavigationBarTranslucent="true">
  ...
</NavigationPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

(App.Current.MainPage as Xamarin.Forms.NavigationPage).BackgroundColor = Color.Blue;
(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().EnableTranslucentNavigationBar();
```

O `NavigationPage.On<iOS>` método Especifica que este específica de plataforma será executado apenas no iOS. O [ `NavigationPage.EnableTranslucentNavigationBar` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.EnableTranslucentNavigationBar/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/) método, além de [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) namespace, é usado para tornar a barra de navegação transparente. Além disso, o [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage/) classe no `Xamarin.Forms.PlatformConfiguration.iOSSpecific` namespace também tem um [ `DisableTranslucentNavigationBar` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.DisableTranslucentNavigationBar/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/) método que restaura a barra de navegação para seu estado padrão, e um [ `SetIsNavigationBarTranslucent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetIsNavigationBarTranslucent/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/System.Boolean/) método que pode ser usado para alternar a transparência da barra de navegação, chamando o [ `IsNavigationBarTranslucent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucent/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/) método:

```csharp
(App.Current.MainPage as Xamarin.Forms.NavigationPage)
  .On<iOS>()
  .SetIsNavigationBarTranslucent(!(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().IsNavigationBarTranslucent());
```

O resultado é que a transparência da barra de navegação pode ser alterada:

![](ios-images/translucent-navigation-bar.png "Barra de navegação transparente específico da plataforma")

<a name="status_bar_color_mode" />

## <a name="adjusting-the-status-bar-text-color-mode"></a>Ajuste o modo de cor do texto da barra de Status

Isso controla específico da plataforma se o texto da barra de status de cores em um [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) é ajustado para corresponder à luminosidade da barra de navegação. Ele é consumido em XAML, definindo o [ `NavigationPage.StatusBarTextColorMode` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.StatusBarTextColorModeProperty/) propriedade anexada a um valor da [ `StatusBarTextColorMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/) enumeração:

```xaml
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    x:Class="PlatformSpecifics.iOSStatusBarTextColorModePage">
    <MasterDetailPage.Master>
        <ContentPage Title="Master Page Title" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <NavigationPage BarBackgroundColor="Blue" BarTextColor="White"
                        ios:NavigationPage.StatusBarTextColorMode="MatchNavigationBarTextLuminosity">
            <x:Arguments>
                <ContentPage>
                    <Label Text="Slide the master page to see the status bar text color mode change." />
                </ContentPage>
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>

```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

IsPresentedChanged += (sender, e) =>
{
    var mdp = sender as MasterDetailPage;
    if (mdp.IsPresented)
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.DoNotAdjust);
    else
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.MatchNavigationBarTextLuminosity);
};
```

O `NavigationPage.On<iOS>` método Especifica que este específica de plataforma será executado apenas no iOS. O [ `NavigationPage.SetStatusBarTextColorMode` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetStatusBarTextColorMode/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage%7D/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) namespace, controles se o texto da barra de status cor o [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) é ajustado para corresponder a luminosidade da barra de navegação, com o [ `StatusBarTextColorMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/) enumeração fornecendo dois valores possíveis:

- [`DoNotAdjust`](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.DoNotAdjust/) – indica que a barra de cor do texto de status não devem ser ajustado.
- [`MatchNavigationBarTextLuminosity`](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.MatchNavigationBarTextLuminosity/) – indica que a barra de cor do texto de status deve corresponder a luminosidade da barra de navegação.

Além disso, o [ `GetStatusBarTextColorMode` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.GetStatusBarTextColorMode/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage%7D/) método pode ser usado para recuperar o valor atual do [ `StatusBarTextColorMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/) enumeração é aplicada para o [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/).

O resultado é que o status da barra de cor do texto em uma [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) pode ser ajustado para corresponder à luminosidade da barra de navegação. Neste exemplo, a barra de status para as alterações de cor de texto como o usuário alterna entre o [ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) e [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) páginas de um [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/):

![](ios-images/status-bar-text-color-mode.png "Barra de status texto cor modo específico de plataforma")

<a name="adjust_font_size" />

## <a name="adjusting-the-font-size-of-an-entry"></a>Ajustar o tamanho da fonte de uma entrada

Essa plataforma específica é usada para dimensionar o tamanho da fonte de um [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) para garantir que o texto de valores se ajuste no controle. Ele é consumido em XAML, definindo o [ `Entry.AdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidthProperty/) anexado a propriedade para um `boolean` valor:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    <StackLayout Margin="20">
        <Entry x:Name="entry"
               Placeholder="Enter text here to see the font size change"
               FontSize="22"
               ios:Entry.AdjustsFontSizeToFitWidth="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

entry.On<iOS>().EnableAdjustsFontSizeToFitWidth();
```

O `Entry.On<iOS>` método Especifica que este específica de plataforma será executado apenas no iOS. O [ `Entry.EnableAdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.EnableAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) namespace, é usada para dimensionar o tamanho da fonte do texto para garantir que ele se encaixa valores de [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/). Além disso, o [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry/) classe no `Xamarin.Forms.PlatformConfiguration.iOSSpecific` namespace também tem um [ `DisableAdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.DisableAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/) método que desativa este específicos de plataforma, e um [ `SetAdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.SetAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/System.Boolean/) método que pode ser usado para alternar o tamanho da fonte dimensionamento chamando o [ `AdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/) método:

```csharp
entry.On<iOS>().SetAdjustsFontSizeToFitWidth(!entry.On<iOS>().AdjustsFontSizeToFitWidth());
```

O resultado é que o tamanho da fonte de [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) é dimensionado para garantir que o texto de valores se ajuste no controle:

![](ios-images/entry-font-size.png "Ajuste específico de plataforma de tamanho de fonte de entrada")

<a name="picker_update_mode" />

## <a name="controlling-picker-item-selection"></a>Controlando a seleção de Item do seletor

Este específico da plataforma controla quando a seleção de itens ocorre em um [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/), permitindo que o usuário especificar que a seleção de item ocorre quando os itens no controle de navegação ou apenas uma vez o **feito** botão é pressionado. Ele é consumido em XAML, definindo o `Picker.UpdateMode` propriedade anexada a um valor da `UpdateMode` enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <Picker ... Title="Select a monkey" ios:Picker.UpdateMode="WhenFinished">
          ...
        </Picker>
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

O `Picker.On<iOS>` método Especifica que este específica de plataforma será executado apenas no iOS. O `Picker.SetUpdateMode` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) namespace, é usada para controlar quando a seleção de itens ocorre, com o `UpdateMode` enumeração fornecendo dois valores possíveis:

- `Immediately` a seleção de item ocorre quando o usuário navega itens do [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/). Este é o comportamento padrão no xamarin. Forms.
- `WhenFinished` – seleção de item só ocorre quando o usuário pressionou a **feito** no botão de [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/).

Além disso, o `SetUpdateMode` método pode ser usado para alternar os valores de enumeração chamando o `UpdateMode` método, que retorna atual `UpdateMode`:

```csharp
switch (picker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        picker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

O resultado é que a especificada `UpdateMode` é aplicada para o [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/), que controla quando ocorre a seleção de item:

[![](ios-images/picker-updatemode.png "Seletor de UpdateMode específica de plataforma")](ios-images/picker-updatemode-large.png#lightbox "Picker UpdateMode Plaform-Specific")

<a name="set_status_bar_visibility" />

## <a name="setting-the-status-bar-visibility-on-a-page"></a>Definindo a barra de Status visibilidade em uma página

Essa plataforma específica é usada para definir a visibilidade da barra de status em um [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), e inclui a capacidade de controlar como a barra de status entra ou sai do `Page`. Ele é consumido em XAML, definindo o `Page.PrefersStatusBarHidden` propriedade anexada a um valor da `StatusBarHiddenMode` enumeração e, opcionalmente, o `Page.PreferredStatusBarUpdateAnimation` propriedade anexada a um valor da `UIStatusBarAnimation` enumeração:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

O `Page.On<iOS>` método Especifica que este específica de plataforma será executado apenas no iOS. O `Page.SetPrefersStatusBarHidden` método, no `Xamarin.Forms.PlatformConfiguration.iOSSpecific` namespace, é usado para definir a visibilidade da barra de status em um [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) especificando um do `StatusBarHiddenMode` valores de enumeração: `Default`, `True` , ou `False`. O `StatusBarHiddenMode.True` e `StatusBarHiddenMode.False` valores definidos a visibilidade da barra de status, independentemente da orientação do dispositivo e o `StatusBarHiddenMode.Default` valor oculta a barra de status em um ambiente verticalmente compact.

O resultado é que a visibilidade da barra de status em um [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) pode ser definido:

![](ios-images/hide-status-bar.png "Específico de plataforma de visibilidade de barra de status")

> [!NOTE]
> Em um [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/), especificado `StatusBarHiddenMode` valor de enumeração também atualizará a barra de status em todas as páginas filho. Em todos os outros [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)-especificados, tipos derivados `StatusBarHiddenMode` valor de enumeração atualizará somente a barra de status na página atual.

O `Page.SetPreferredStatusBarUpdateAnimation` método é usado para definir como a barra de status entra ou sai do [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) especificando um do `UIStatusBarAnimation` valores de enumeração: `None`, `Fade`, ou `Slide`. Se o `Fade` ou `Slide` valor de enumeração for especificado, uma segunda 0,25 animação é executado como a barra de status entra ou sai do `Page`.

<a name="delay_content_touches" />

## <a name="delaying-content-touches-in-a-scrollview"></a>Atrasando ajustes de conteúdo em um ScrollView

Um timer implícita é acionado quando um gesto de toque começa em um [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) no iOS e o `ScrollView` decide, com base na ação de usuário dentro do período de temporizador, se ele deve tratar o gesto ou passá-lo ao seu conteúdo. Por padrão, o iOS `ScrollView` toques conteúdo atrasos, mas isso pode causar problemas em algumas circunstâncias com o `ScrollView` conteúdo vencedor não o gesto quando deveria. Portanto, isso controla específica de plataforma se um `ScrollView` trata um gesto de toque ou passá-lo para seu conteúdo. Ele é consumido em XAML, definindo o `ScrollView.ShouldDelayContentTouches` anexado a propriedade para um `boolean` valor:

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <MasterDetailPage.Master>
        <ContentPage Title="Menu" BackgroundColor="Blue" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <ContentPage>
            <ScrollView x:Name="scrollView" ios:ScrollView.ShouldDelayContentTouches="false">
                <StackLayout Margin="0,20">
                    <Slider />
                    <Button Text="Toggle ScrollView DelayContentTouches" Clicked="OnButtonClicked" />
                </StackLayout>
            </ScrollView>
        </ContentPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

scrollView.On<iOS>().SetShouldDelayContentTouches(false);
```

O `ScrollView.On<iOS>` método Especifica que este específica de plataforma será executado apenas no iOS. O `ScrollView.SetShouldDelayContentTouches` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) namespace, é usado para controlar se uma [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) trata um gesto de toque ou passá-lo para seu conteúdo. Além disso, o `SetShouldDelayContentTouches` método pode ser usado para alternar atrasando ajustes conteúdos chamando o `ShouldDelayContentTouches` método para retornar se o conteúdo ajustes estão atrasados:

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

O resultado é que um [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) pode desabilitar atrasando receber conteúdos toques, assim que neste cenário o [ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) recebe o gesto em vez de [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) página do [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/):

[![](ios-images/scrollview-delay-content-touches.png "Conteúdo de atraso ScrollView toca específica de plataforma")](ios-images/scrollview-delay-content-touches-large.png#lightbox "ScrollView Delay Content Touches Plaform-Specific")

## <a name="summary"></a>Resumo

Este artigo demonstrou como consumir os iOS plataforma específicos que são integrados ao xamarin. Forms. Específicos de plataforma permitem consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos.


## <a name="related-links"></a>Links relacionados

- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [iOSSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)
