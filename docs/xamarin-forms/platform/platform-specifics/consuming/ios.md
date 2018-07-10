---
title: Especificidades da plataforma do iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo demonstra como utilizar as iOS-especificidades da plataforma que são criadas no xamarin. Forms.
ms.prod: xamarin
ms.assetid: C0837996-A1E8-47F9-B3A8-98EE43B4A675
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/30/2018
ms.openlocfilehash: be378a60a9d9a7b206b64f07ee70edb432cec8e3
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935648"
---
# <a name="ios-platform-specifics"></a>Especificidades da plataforma do iOS

_Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo demonstra como utilizar as iOS-especificidades da plataforma que são criadas no xamarin. Forms._

No iOS, xamarin. Forms contém as seguintes especificações de plataforma:

- Suporte para qualquer um de desfoque [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/). Para obter mais informações, consulte [Aplicar desfoque](#blur).
- Controlar se o título da página é exibido como um título grande na barra de navegação de página. Para obter mais informações, consulte [exibir títulos grandes](#large_title).
- Garantir que esse conteúdo da página está posicionado em uma área da tela que é segura para todos os dispositivos iOS. Para obter mais informações, consulte [habilitando o guia do Layout da área segura](#safe_area_layout).
- Uma barra de navegação translúcido. Para obter mais informações, consulte [fazendo a barra de navegação translúcido](#translucent_navigation_bar).
- Controlar se o texto da barra de status de cor em um [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) é ajustado para coincidir com a luminosidade da barra de navegação. Para obter mais informações, consulte [ajustando o modo de cor do texto de barra de Status](#status_bar_color_mode).
- Garantir que inseridos texto se adapta a um [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) ajustando o tamanho da fonte. Para obter mais informações, consulte [ajustando o tamanho da fonte de uma entrada](#adjust_font_size).
- Controlando quando a seleção de item ocorre em um [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/). Para obter mais informações, consulte [seleção de Item de seletor controlando](#picker_update_mode).
- Definindo a visibilidade da barra de status em uma [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/). Para obter mais informações, consulte [definindo a visibilidade da barra de Status em uma página](#set_status_bar_visibility).
- Controlar se uma [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) manipula um gesto de toque ou passá-lo ao seu conteúdo. Para obter mais informações, consulte [atrasando toques conteúdo em um ScrollView](#delay_content_touches).
- Definindo o estilo do separador em um [ `ListView` ](xref:Xamarin.Forms.ListView). Para obter mais informações, consulte [definindo o estilo do separador em um ListView](#listview-separatorstyle).
- Desabilitar o modo de cor herdados em um com suporte [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obter mais informações, consulte [Desabilitar modo de cor herdado](#legacy-color-mode).

<a name="blur" />

## <a name="applying-blur"></a>Aplicação de desfoque

Este específicos da plataforma é usado para desfocar o conteúdo em camadas abaixo dela e é consumido em XAML, definindo o [ `VisualElement.BlurEffect` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.BlurEffectProperty/) propriedade anexada a um valor da [ `BlurEffectStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/) enumeração:

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

O `BoxView.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O [ `VisualElement.UseBlurEffect` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.UseBlurEffect/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement}/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para aplicar o efeito de desfoque, com o [ `BlurEffectStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle) enumeração fornecendo quatro valores: [ `None` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.None), [ `ExtraLight` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.ExtraLight), [ `Light` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Light), e [ `Dark` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle.Dark).

O resultado é que o especificado [ `BlurEffectStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.BlurEffectStyle/) é aplicado ao [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) da instância, que Desfoca a [ `Image` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Image/) em camadas abaixo dela:

![](ios-images/blur-effect.png "Específico da plataforma efeito de desfoque")

<a name="large_title" />

## <a name="displaying-large-titles"></a>Exibir títulos grandes

Este específicos da plataforma é usado para exibir o título da página como um título grande na barra de navegação, para dispositivos que usam iOS 11 ou superior. Um título grande é alinhado à esquerda e usa uma fonte maior e faz a transição para um título padrão conforme o usuário começa a rolagem de conteúdo, para que o espaço na tela é usado com eficiência. No entanto, em orientação paisagem, o título retornará para o centro da barra de navegação para otimizar o layout do conteúdo. Ele é consumido em XAML, definindo o `NavigationPage.PrefersLargeTitles` anexado à propriedade um `boolean` valor:

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

O `NavigationPage.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `NavigationPage.SetPrefersLargeTitle` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) namespace, que controla se os títulos grandes estão habilitados.

Desde que os títulos grandes estão habilitados na [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/), todas as páginas na pilha de navegação serão exibir títulos grandes. Esse comportamento pode ser substituído nas páginas, definindo o `Page.LargeTitleDisplay` propriedade anexada a um valor da `LargeTitleDisplayMode` enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

Como alternativa, o comportamento de página pode ser substituído no c# usando a API fluente:

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

O `Page.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `Page.SetLargeTitleDisplay` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) namespace, controla o comportamento de título grande no [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), com o `LargeTitleDisplayMode` fornecendo três possíveis de enumeração valores:

- `Always` – forçar a barra de navegação e a fonte tamanho para usar o formato grande.
- `Automatic` – use o mesmo estilo (pequeno ou grande) como o item anterior na pilha de navegação.
- `Never` – forçar o uso da barra de navegação de formato pequeno regular.

Além disso, o `SetLargeTitleDisplay` método pode ser usado para alternar os valores de enumeração por meio da chamada a `LargeTitleDisplay` método, que retorna atual `LargeTitleDisplayMode`:

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

O resultado é que o especificado `LargeTitleDisplayMode` é aplicada para o [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), que controla o comportamento de título grande:

![](ios-images/large-title.png "Específico da plataforma efeito de desfoque")

<a name="safe_area_layout" />

## <a name="enabling-the-safe-area-layout-guide"></a>Habilitar o guia de Layout de área de segurança

Este específicos da plataforma é usado para garantir que o conteúdo da página é posicionado em uma área da tela que é segura para todos os dispositivos que usam iOS 11 e superior. Especificamente, ele ajudará a garantir que o conteúdo não é recortado pelo cantos arredondados de dispositivo, o indicador de base ou a estrutura do sensor em um iPhone X. Ele é consumido em XAML, definindo o `Page.UseSafeArea` anexado à propriedade um `boolean` valor:

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

O `Page.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `Page.SetUseSafeArea` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) namespace, que controla se o guia do layout da área de segurança está habilitado.

O resultado é que o conteúdo da página pode ser posicionado em uma área da tela que é segura para todos os iPhones:

[![](ios-images/safe-area-layout.png "Guia do Layout da área de segurança")](ios-images/safe-area-layout-large.png#lightbox "guia do Layout da área de segurança")

> [!NOTE]
> Área de segurança definida pela Apple é usada no xamarin. Forms para definir a [ `Page.Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Page.Padding/) propriedade e substituirá qualquer anteriores valores dessa propriedade que foram definidos.

Área de segurança pode ser personalizada, recuperando seu [ `Thickness` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Thickness/) de valor com o `Page.SafeAreaInsets` método da [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) namespace. Em seguida, pode ser modificado como necessário e reatribuído à `Padding` propriedade no construtor da página ou [ `OnAppearing` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.OnAppearing()/) substituir:

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

## <a name="making-the-navigation-bar-translucent"></a>Tornando a barra de navegação translúcido

Este específicos da plataforma é usado para alterar a transparência da barra de navegação e consumido em XAML, definindo o [ `NavigationPage.IsNavigationBarTranslucent` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucentProperty/) anexado à propriedade um `boolean` valor:

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

O `NavigationPage.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O [ `NavigationPage.EnableTranslucentNavigationBar` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.EnableTranslucentNavigationBar/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) namespace, é usado para fazer a barra de navegação translúcido. Além disso, o [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage/) classe o `Xamarin.Forms.PlatformConfiguration.iOSSpecific` namespace também tem um [ `DisableTranslucentNavigationBar` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.DisableTranslucentNavigationBar/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/) método que restaura a barra de navegação para seu estado padrão, e um [ `SetIsNavigationBarTranslucent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetIsNavigationBarTranslucent/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/System.Boolean/) método que pode ser usado para alternar a transparência da barra de navegação, chamando o [ `IsNavigationBarTranslucent` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucent/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}/) método:

```csharp
(App.Current.MainPage as Xamarin.Forms.NavigationPage)
  .On<iOS>()
  .SetIsNavigationBarTranslucent(!(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().IsNavigationBarTranslucent());
```

O resultado é que a transparência da barra de navegação pode ser alterada:

![](ios-images/translucent-navigation-bar.png "Barra de navegação translúcido específicos da plataforma")

<a name="status_bar_color_mode" />

## <a name="adjusting-the-status-bar-text-color-mode"></a>Ajustando o modo de cor do texto da barra de Status

Este controles específicos da plataforma se o texto da barra de status de cor em um [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) é ajustado para coincidir com a luminosidade da barra de navegação. Ele é consumido em XAML, definindo o [ `NavigationPage.StatusBarTextColorMode` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.StatusBarTextColorModeProperty/) propriedade anexada a um valor da [ `StatusBarTextColorMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/) enumeração:

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

O `NavigationPage.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O [ `NavigationPage.SetStatusBarTextColorMode` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetStatusBarTextColorMode/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage%7D/Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode/) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) namespace, controles se o texto da barra de status de cor no [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) é ajustado para coincidir com o luminosidade da barra de navegação, com o [ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) enumeração fornecendo dois valores possíveis:

- [`DoNotAdjust`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.DoNotAdjust) – indica que a barra de cor do texto de status não devem ser ajustado.
- [`MatchNavigationBarTextLuminosity`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.MatchNavigationBarTextLuminosity) – indica que a barra de cor do texto de status deve corresponder à luminosidade da barra de navegação.

Além disso, o [ `GetStatusBarTextColorMode` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.GetStatusBarTextColorMode/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage%7D/) método pode ser usado para recuperar o valor atual da [ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) enumeração é aplicada para o [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage).

O resultado é que o status da barra de cor do texto em uma [ `NavigationPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/) pode ser ajustado de acordo com a luminosidade da barra de navegação. Neste exemplo, a barra de alterações de cor do texto de status que o usuário alterna entre o [ `Master` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Master/) e [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) páginas de um [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/):

![](ios-images/status-bar-text-color-mode.png "Barra de status texto cor modo específico da plataforma")

<a name="adjust_font_size" />

## <a name="adjusting-the-font-size-of-an-entry"></a>Ajustar o tamanho da fonte de uma entrada

Este específicos da plataforma é usado para dimensionar o tamanho da fonte de um [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) para garantir que o texto de valores se encaixa no controle. Ele é consumido em XAML, definindo o [ `Entry.AdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidthProperty/) anexado à propriedade um `boolean` valor:

```xaml
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

O `Entry.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O [ `Entry.EnableAdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.EnableAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) namespace, é usado para dimensionar o tamanho da fonte do texto para garantir que ela se encaixa valores de [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/). Além disso, o [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry/) classe o `Xamarin.Forms.PlatformConfiguration.iOSSpecific` namespace também tem um [ `DisableAdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.DisableAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/) método que desabilita essa específicos da plataforma, e um [ `SetAdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.SetAdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/System.Boolean/) método que pode ser usado para ativar/desativar o tamanho da fonte dimensionamento chamando o [ `AdjustsFontSizeToFitWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry}/) método:

```csharp
entry.On<iOS>().SetAdjustsFontSizeToFitWidth(!entry.On<iOS>().AdjustsFontSizeToFitWidth());
```

O resultado é que o tamanho da fonte de [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) é dimensionado para garantir que o texto de valores se encaixa no controle:

![](ios-images/entry-font-size.png "Ajustar específicos de plataforma de tamanho de fonte de entrada")

<a name="picker_update_mode" />

## <a name="controlling-picker-item-selection"></a>Controlando a seleção de Item do seletor

Este específicos da plataforma controla quando a seleção de item ocorre em um [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/), permitindo que o usuário especifique que a seleção de item ocorre ao navegar por itens no controle, ou apenas uma vez a **feito** botão é pressionado. Ele é consumido em XAML, definindo o `Picker.UpdateMode` propriedade anexada a um valor da `UpdateMode` enumeração:

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

O `Picker.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `Picker.SetUpdateMode` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) namespace, é usado para controlar quando a seleção de item ocorre, com o `UpdateMode` enumeração fornecendo dois valores possíveis:

- `Immediately` – seleção de item ocorre conforme o usuário navega por itens na [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/). Esse é o comportamento padrão no xamarin. Forms.
- `WhenFinished` – seleção de item ocorrerá somente depois que o usuário pressiona o **feito** botão na [ `Picker` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/).

Além disso, o `SetUpdateMode` método pode ser usado para alternar os valores de enumeração por meio da chamada a `UpdateMode` método, que retorna atual `UpdateMode`:

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

[![](ios-images/picker-updatemode.png "Seletor de UpdateMode específicos da plataforma")](ios-images/picker-updatemode-large.png#lightbox "Picker UpdateMode Plaform-Specific")

<a name="set_status_bar_visibility" />

## <a name="setting-the-status-bar-visibility-on-a-page"></a>Definir a barra de Status visibilidade em uma página

Este específicos da plataforma é usado para definir a visibilidade da barra de status em uma [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/), e inclui a capacidade de controlar como a barra de status entra ou sai de `Page`. Ele é consumido em XAML, definindo o `Page.PrefersStatusBarHidden` propriedade anexada a um valor da `StatusBarHiddenMode` enumeração e, opcionalmente, o `Page.PreferredStatusBarUpdateAnimation` propriedade anexada a um valor da `UIStatusBarAnimation` enumeração:

```xaml
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

O `Page.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `Page.SetPrefersStatusBarHidden` método, no `Xamarin.Forms.PlatformConfiguration.iOSSpecific` namespace, é usado para definir a visibilidade da barra de status em um [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) especificando um dos `StatusBarHiddenMode` valores de enumeração: `Default`, `True` , ou `False`. O `StatusBarHiddenMode.True` e `StatusBarHiddenMode.False` valores definidos a visibilidade da barra de status, independentemente da orientação do dispositivo e o `StatusBarHiddenMode.Default` valor oculta a barra de status em um ambiente compact verticalmente.

O resultado é que a visibilidade da barra de status em uma [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) podem ser definidos:

![](ios-images/hide-status-bar.png "Específicos de plataforma de visibilidade de barra de status")

> [!NOTE]
> Em um [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/), especificado `StatusBarHiddenMode` valor de enumeração também atualizará a barra de status em todas as páginas filho. Em todos os outros [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)-tipos derivados, especificados `StatusBarHiddenMode` valor de enumeração só atualizará a barra de status na página atual.

O `Page.SetPreferredStatusBarUpdateAnimation` método é usado para definir como a barra de status entra ou sai do [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) especificando um da `UIStatusBarAnimation` valores de enumeração: `None`, `Fade`, ou `Slide`. Se o `Fade` ou `Slide` o valor de enumeração for especificado, uma segunda 0,25 animação é executada como a barra de status entra ou sai do `Page`.

<a name="delay_content_touches" />

## <a name="delaying-content-touches-in-a-scrollview"></a>Atrasando toques de conteúdo em um ScrollView

Um temporizador implícito é disparado quando um gesto de toque começa em um [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) no iOS e o `ScrollView` decide, com base na ação de usuário na extensão de timer, se ele deve tratar o gesto ou passá-lo ao seu conteúdo. Por padrão, o iOS `ScrollView` toques de conteúdo de atrasos, mas isso pode causar problemas em algumas circunstâncias com o `ScrollView` conteúdo não ganhando o gesto quando deveria. Portanto, esse controles específicos da plataforma se um `ScrollView` manipula um gesto de toque ou passá-lo ao seu conteúdo. Ele é consumido em XAML, definindo o `ScrollView.ShouldDelayContentTouches` anexado à propriedade um `boolean` valor:

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

O `ScrollView.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `ScrollView.SetShouldDelayContentTouches` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/) namespace, é usada para controlar se uma [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) manipula um gesto de toque ou passá-lo ao seu conteúdo. Além disso, o `SetShouldDelayContentTouches` método pode ser usado para ativar/desativar atrasando o conteúdo de toques, chamando o `ShouldDelayContentTouches` método para retornar se o conteúdo de toques estão atrasadas:

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

O resultado é que um [ `ScrollView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/) pode desabilitar atrasando o recebimento de toques de conteúdo, portanto, que neste cenário o [ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) recebe o gesto em vez de [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.MasterDetailPage.Detail/) página do [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/):

[![](ios-images/scrollview-delay-content-touches.png "Conteúdo de atraso de ScrollView toca específicos da plataforma")](ios-images/scrollview-delay-content-touches-large.png#lightbox "ScrollView Delay Content Touches Plaform-Specific")

<a name="listview-separatorstyle" />

## <a name="setting-the-separator-style-on-a-listview"></a>Definindo o estilo do separador em um ListView

Este específicos da plataforma controla se o separador entre células em uma [ `ListView` ](xref:Xamarin.Forms.ListView) usa a largura total do `ListView`. Ele é consumido em XAML, definindo o [ `ListView.SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SeparatorStyleProperty) propriedade anexada a um valor da [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.SeparatorStyle="FullWidth">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetSeparatorStyle(SeparatorStyle.FullWidth);
```

O `ListView.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O [ `ListView.SetSeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SetSeparatorStyle(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle)) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usada para controlar se o separador entre células no [ `ListView` ](xref:Xamarin.Forms.ListView) usa completo largura do `ListView`, com o [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) enumeração fornecendo dois valores possíveis:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.Default) – indica o comportamento do separador de iOS padrão. Esse é o comportamento padrão no xamarin. Forms.
- [`FullWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.FullWidth) – indica que os separadores serão desenhados de uma borda da `ListView` para outro.

O resultado é que o especificado [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) valor é aplicado a [ `ListView` ](xref:Xamarin.Forms.ListView), que controla a largura do separador entre células:

![](ios-images/listview-separatorstyle.png "ListView SeparatorStyle específicos da plataforma")

> [!NOTE]
> Depois que o estilo do separador foi definido como `FullWidth`, ele não pode ser alterado para `Default` em tempo de execução.

<a name="legacy-color-mode" />

## <a name="disabling-legacy-color-mode"></a>Desabilitar o modo de cor herdado

Algumas das exibições de xamarin. Forms apresentam um modo de cor herdado. Nesse modo, quando o [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) propriedade do modo de exibição é definida como `false`, o modo de exibição substituirá as cores definidas pelo usuário com os nativo as cores padrão para o estado desabilitado. Para versões anteriores compatibilidade, esse modo herdado cor permanece o comportamento padrão para modos de exibição com suporte.

Este específicos da plataforma desabilita nesse modo herdado de cor, para que permaneçam de cores definido em uma exibição pelo usuário, mesmo quando o modo de exibição está desabilitado. Ele é consumido em XAML, definindo o [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsLegacyColorModeEnabledProperty) anexado à propriedade `false`:

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

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

_legacyColorModeDisabledButton.On<iOS>().SetIsLegacyColorModeEnabled(false);
```

O `VisualElement.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement},System.Boolean)) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usada para controlar se o modo de cor herdados é desabilitado. Além disso, o [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.VisualElement})) método pode ser usado para retornar se o modo de cor herdado está desabilitado.

O resultado é que o modo herdado de cor pode ser desabilitado, para que as cores definidas em uma exibição pelo usuário permaneçam até mesmo quando o modo de exibição está desabilitado:

![](ios-images/legacy-color-mode-disabled.png "Modo herdado de cor desabilitado")

> [!NOTE]
> Ao definir uma [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) em uma exibição, o modo herdado de cor é totalmente ignorado. Para obter mais informações sobre estados visuais, consulte [o Gerenciador de estado Visual xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="summary"></a>Resumo

Este artigo demonstrou como consumir as iOS-especificidades da plataforma que são criadas no xamarin. Forms. Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos.


## <a name="related-links"></a>Links relacionados

- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (amostra)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [iOSSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.iOSSpecific/)
