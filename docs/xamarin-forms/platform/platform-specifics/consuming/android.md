---
title: Especificações de plataforma Android
description: Específicos de plataforma permitem consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo demonstra como utilizar os Android plataforma específicos que são integrados ao xamarin. Forms.
ms.prod: xamarin
ms.assetid: C5D4AA65-9BAA-4008-8A1E-36CDB78A435D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/23/2018
ms.openlocfilehash: 8d7ec3f2f64fdb8be903fd13bd72bcf545265a3d
ms.sourcegitcommit: 4f646dc5c51db975b2936169547d625c78a22b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2018
---
# <a name="android-platform-specifics"></a>Especificações de plataforma Android

_Específicos de plataforma permitem consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo demonstra como utilizar os Android plataforma específicos que são integrados ao xamarin. Forms._

No Android, xamarin. Forms contém as seguintes especificações de plataforma:

- Definindo o modo de operação de um teclado virtual. Para obter mais informações, consulte [definindo o modo de entrada de teclado flexível](#soft_input_mode).
- Habilitar a rolagem rápida em um [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) para obter mais informações, consulte [habilitando o recurso de rolagem rápido em uma ListView](#fastscroll).
- Habilitando a passar o dedo entre páginas em um [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/). Para obter mais informações, consulte [habilitando passar o dedo entre páginas em um TabbedPage](#enable_swipe_paging).
- Controlando a ordem Z de elementos visuais para determinar a ordem de desenho. Para obter mais informações, consulte [controlando a elevação de elementos visuais](#elevation).
- Desabilitando o [ `Disappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) e [ `Appearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) página eventos de ciclo de vida em Pausar e retomar respectivamente, para aplicativos que usam AppCompat. Para obter mais informações, consulte [desabilitando o Disappearing e eventos de ciclo de vida de página que aparecem](#disable_lifecycle_events).
- Controlar se uma [ `WebView` ](xref:Xamarin.Forms.WebView) pode exibir conteúdo misto. Para obter mais informações, consulte [habilitando misto conteúdo no WebView](#webview-mixed-content).
- Definir o método de entrada de opções do editor para o teclado virtual para um [ `Entry` ](xref:Xamarin.Forms.Entry). Para obter mais informações, consulte [opções de configuração de entrada IME](#entry-imeoptions).

<a name="soft_input_mode" />

## <a name="setting-the-soft-keyboard-input-mode"></a>Definindo o modo de entrada do teclado virtual

Essa plataforma específica é usada para definir o modo de operação para uma área de entrada do teclado virtual e consumida em XAML, definindo o [ `Application.WindowSoftInputModeAdjust` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.WindowSoftInputModeAdjustProperty/) propriedade anexada a um valor da [ `WindowSoftInputModeAdjust` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust/) enumeração:

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize">
  ...
</Application>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

App.Current.On<Android>().UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize);
```

O `Application.On<Android>` método Especifica que este específica de plataforma será executado somente no Android. O [ `Application.UseWindowSoftInputModeAdjust` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.UseWindowSoftInputModeAdjust/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust/) método, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/) namespace, é usado para definir o modo de operação de área de entrada de teclado virtual, com o [ `WindowSoftInputModeAdjust` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust/) fornece dois valores de enumeração: [ `Pan` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Pan/) e [ `Resize` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize/). O `Pan` valor usa o [ `AdjustPan` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustPan/) opção de ajuste, que não redimensiona a janela quando um controle de entrada tem foco. Em vez disso, o conteúdo da janela estendido para que o foco atual não será ocultado pelo teclado virtual. O `Resize` valor usa o [ `AdjustResize` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustResize/) opção de ajuste que redimensiona a janela quando um controle de entrada tem o foco, para liberar espaço para o teclado virtual.

O resultado é que o teclado virtual área modo operacional pode ser definido quando um controle de entrada tem o foco de entrada:

[![](android-images/pan-resize.png "Teclado virtual específico de plataforma de modo de operação")](android-images/pan-resize-large.png#lightbox "Soft Keyboard Operating Mode Plaform-Specific")

<a name="fastscroll" />

## <a name="enabling-fast-scrolling-in-a-listview"></a>Habilitar a rolagem rápida em uma ListView

Essa plataforma específica é usada para habilitar a rolagem rápida por meio de dados em um [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/). Ele é consumido em XAML, definindo o `ListView.IsFastScrollEnabled` anexado a propriedade para um `boolean` valor:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        ...
        <ListView ItemsSource="{Binding GroupedEmployees}"
                  GroupDisplayBinding="{Binding Key}"
                  IsGroupingEnabled="true"
                  android:ListView.IsFastScrollEnabled="true">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var listView = new Xamarin.Forms.ListView { IsGroupingEnabled = true, ... };
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "GroupedEmployees");
listView.GroupDisplayBinding = new Binding("Key");
listView.On<Android>().SetIsFastScrollEnabled(true);
```

O `ListView.On<Android>` método Especifica que este específica de plataforma será executado somente no Android. O `ListView.SetIsFastScrollEnabled` método, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/) namespace, é usado para habilitar a rolagem rápida por meio de dados em um [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/). Além disso, o `SetIsFastScrollEnabled` método pode ser usado para alternar a rolagem rápida chamando o `IsFastScrollEnabled` método para retornar se a rolagem rápida está habilitada:

```csharp
listView.On<Android>().SetIsFastScrollEnabled(!listView.On<Android>().IsFastScrollEnabled());
```

O resultado é que percorrer rápida de dados em um [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) podem ser habilitadas, que altera o tamanho do controle deslizante de rolagem:

[![](android-images/fastscroll.png "Específico da plataforma ListView FastScroll")](android-images/fastscroll-large.png#lightbox "ListView FastScroll Plaform-Specific")

<a name="enable_swipe_paging" />

## <a name="enabling-swiping-between-pages-in-a-tabbedpage"></a>Habilitando a passar o dedo entre páginas em um TabbedPage

Essa plataforma específica é usada para habilitar a passar o dedo com um gesto de dedo horizontal entre as páginas em um [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/). Ele é consumido em XAML, definindo o [ `TabbedPage.IsSwipePagingEnabled` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty/) anexado a propriedade para um `boolean` valor:

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.OffscreenPageLimit="2"
            android:TabbedPage.IsSwipePagingEnabled="true">
    ...
</TabbedPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetOffscreenPageLimit(2)
             .SetIsSwipePagingEnabled(true);
```

O `TabbedPage.On<Android>` método Especifica que este específica de plataforma será executado somente no Android. O [ `TabbedPage.SetIsSwipePagingEnabled` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetIsSwipePagingEnabled/p/Xamarin.Forms.BindableObject/System.Boolean/) método, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/) namespace, é usado para habilitar a passar o dedo entre páginas em um [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/). Além disso, o `TabbedPage` classe no `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` namespace também tem um [ `EnableSwipePaging` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.EnableSwipePaging/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage%7D/) método que permite que esse específicos de plataforma, e um [ `DisableSwipePaging` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.DisableSwipePaging/p/Xamarin.Forms.IPlatformElementConfiguration%7BXamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage%7D/) método desabilita Essa plataforma específica. O [ `TabbedPage.OffscreenPageLimit` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty/) propriedade, anexada e [ `SetOffscreenPageLimit` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetOffscreenPageLimit/p/Xamarin.Forms.BindableObject/System.Int32/) método, são usadas para definir o número de páginas que devem ser mantidos em um estado ocioso em ambos os lados da página atual.

O resultado é que paginação passar por meio das páginas exibidas por um [ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/) está habilitado:

![](android-images/tabbedpage-swipe.png)

<a name="elevation" />

## <a name="controlling-the-elevation-of-visual-elements"></a>Controlando a elevação de elementos visuais

Essa plataforma específica é usado para controlar a elevação ou ordem Z, de elementos visuais em aplicativos voltados para o API 21 ou maior. A elevação de um elemento visual determina sua ordem de desenho, com elementos visuais com valores mais altos de Z occluding elementos visuais com baixos valores de Z. Ele é consumido em XAML, definindo o `VisualElement.Elevation` anexado a propriedade para um `boolean` valor:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
             Title="Elevation">
    <StackLayout>
        <Grid>
            <Button Text="Button Beneath BoxView" />
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>        
        <Grid Margin="0,20,0,0">
            <Button Text="Button Above BoxView - Click Me" android:VisualElement.Elevation="10"/>
            <BoxView Color="Red" Opacity="0.2" HeightRequest="50" />
        </Grid>
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

public class AndroidElevationPageCS : ContentPage
{
    public AndroidElevationPageCS()
    {
        ...
        var aboveButton = new Button { Text = "Button Above BoxView - Click Me" };
        aboveButton.On<Android>().SetElevation(10);

        Content = new StackLayout
        {
            Children =
            {
                new Grid
                {
                    Children =
                    {
                        new Button { Text = "Button Beneath BoxView" },
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                },
                new Grid
                {
                    Margin = new Thickness(0,20,0,0),
                    Children =
                    {
                        aboveButton,
                        new BoxView { Color = Color.Red, Opacity = 0.2, HeightRequest = 50 }
                    }
                }
            }
        };
    }
}
```

O `Button.On<Android>` método Especifica que este específica de plataforma será executado somente no Android. O `VisualElement.SetElevation` método, além de [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/) namespace, é usado para definir a elevação do elemento visual como um valor nulo `float`. Além disso, o `VisualElement.GetElevation` método pode ser usado para recuperar o valor de elevação de um elemento visual.

O resultado é que a elevação de elementos visuais pode ser controlada para que os elementos visuais com valores mais altos de Z occlude elementos visuais com baixos valores de Z. Portanto, no exemplo a segunda [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) é renderizado acima de [ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/) porque ele tem um valor mais alto de elevação:

![](android-images/elevation.png)

<a name="disable_lifecycle_events" />

## <a name="disabling-the-disappearing-and-appearing-page-lifecycle-events"></a>Desabilitando os eventos de ciclo de vida da página desaparecendo e exibido

Essa plataforma específica é usada para desabilitar o [ `Disappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) e [ `Appearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) eventos de página no aplicativo pausar e retomar respectivamente, para aplicativos que usam AppCompat. Além disso, ele inclui a capacidade de controlar se o teclado virtual é exibido ao continuar, se ele foi exibido em pausa, desde que o modo de operação do teclado virtual é definido para [ `WindowSoftInputModeAdjust.Resize` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize/).

> [!NOTE]
> Observe que esses eventos são ativados por padrão para preservar o comportamento existente para aplicativos que dependem de eventos. Desabilitar esses eventos faz com que o ciclo de eventos de AppCompat corresponder o ciclo de evento de pré-AppCompat.

Essa plataforma específica pode ser consumida em XAML, definindo o [ `Application.SendDisappearingEventOnPause` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPauseProperty/), [ `Application.SendAppearingEventOnResume` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResumeProperty/), e [ `Application.ShouldPreserveKeyboardOnResume` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResumeProperty/) propriedades anexadas para `boolean` valores:

```xaml
<Application ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"             xmlns:androidAppCompat="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
             android:Application.WindowSoftInputModeAdjust="Resize"
             androidAppCompat:Application.SendDisappearingEventOnPause="false"
             androidAppCompat:Application.SendAppearingEventOnResume="false"
             androidAppCompat:Application.ShouldPreserveKeyboardOnResume="true">
  ...
</Application>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;
...

Xamarin.Forms.Application.Current.On<Android>()
     .UseWindowSoftInputModeAdjust(WindowSoftInputModeAdjust.Resize)
     .SendDisappearingEventOnPause(false)
     .SendAppearingEventOnResume(false)
     .ShouldPreserveKeyboardOnResume(true);
```

O `Application.Current.On<Android>` método Especifica que este específica de plataforma será executado somente no Android. O [ `Application.SendDisappearingEventOnPause` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPause/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/System.Boolean/) método, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat/) namespace, é usado para habilitar ou desabilitar o acionamento de [ `Disappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) evento de página, quando o aplicativo Insere o plano de fundo. O [ `Application.SendAppearingEventOnResume` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResume/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/System.Boolean/) método é usado para habilitar ou desabilitar o acionamento de [ `Appearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) evento de página, quando o aplicativo retoma do plano de fundo. O [ `Application.ShouldPreserveKeyboardOnResume` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResume/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application}/System.Boolean/) método é usado o controle se o teclado virtual é exibido ao continuar, se ele foi exibido em pausa, fornecido que o modo de operação do teclado virtual é definido como [ `WindowSoftInputModeAdjust.Resize` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize/).

O resultado é que o [ `Disappearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) e [ `Appearing` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/) eventos de página não ser acionados em pausa do aplicativo e retomar respectivamente, e que, se o teclado virtual foi exibida quando o aplicativo foi pausada, ele também será exibido quando o aplicativo retoma o:

[![](android-images/keyboard-on-resume.png "Específico de plataforma de eventos de ciclo de vida")](android-images/keyboard-on-resume-large.png#lightbox "específica de plataforma de eventos de ciclo de vida")

<a name="webview-mixed-content" />

## <a name="enabling-mixed-content-in-a-webview"></a>Habilitar conteúdo misto no WebView

Isso controla específico da plataforma se um [ `WebView` ](xref:Xamarin.Forms.WebView) pode exibir conteúdo misto em aplicativos voltados para o API 21 ou maior. Conteúdo misto é inicialmente carregado em uma conexão HTTPS, mas que carrega os recursos (como imagens, áudio, vídeo, folhas de estilo, scripts) em uma conexão HTTP. Ele é consumido em XAML, definindo o [ `WebView.MixedContentMode` ](x:ref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty) propriedade anexada a um valor da [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) enumeração:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

O `WebView.On<Android>` método Especifica que este específica de plataforma será executado somente no Android. O [ `WebView.SetMixedContentMode` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.SetMixedContentMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.WebView},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)) método, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usado para controlar se o conteúdo misto pode ser exibido, com o [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) enumeração fornecer três valores possíveis:

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) – indica que o [ `WebView` ](xref:Xamarin.Forms.WebView) permitirá que uma origem HTTPS carregar conteúdo de uma origem HTTP.
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) – indica que o [ `WebView` ](xref:Xamarin.Forms.WebView) não permitirá que uma origem HTTPS carregar conteúdo de uma origem HTTP.
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) – indica que o [ `WebView` ](xref:Xamarin.Forms.WebView) tentará ser compatível com a abordagem do navegador da web mais recente do dispositivo. Algum conteúdo HTTP poderão ser carregados por uma origem HTTPS e outros tipos de conteúdo serão bloqueados. Os tipos de conteúdo que está bloqueado ou permitido podem ser alterados a cada versão do sistema operacional.

O resultado é que a especificada [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) valor é aplicado para o [ `WebView` ](xref:Xamarin.Forms.WebView), que controla se o conteúdo misto pode ser exibido:

[![WebView misto tratamento de conteúdo específico da plataforma](android-images/webview-mixedcontent.png "WebView misto tratamento de conteúdo específico da plataforma")](android-images/webview-mixedcontent-large.png#lightbox "WebView misto tratamento de conteúdo específico da plataforma")

<a name="entry-imeoptions" />

## <a name="setting-entry-input-method-editor-options"></a>Opções do Editor de método de entrada de entrada de configuração

Essa plataforma específica define o método de entrada opções do editor (IME) para o teclado virtual para um [ `Entry` ](xref:Xamarin.Forms.Entry). Isso inclui a definição do botão de ação do usuário no canto inferior do teclado virtual e as interações com o `Entry`. Ele é consumido em XAML, definindo o [ `Entry.ImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty) propriedade anexada a um valor da [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) enumeração:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Entry ... android:Entry.ImeOptions="Send" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

entry.On<Android>().SetImeOptions(ImeFlags.Send);
```

O `Entry.On<Android>` método Especifica que este específica de plataforma será executado somente no Android. O [ `Entry.SetImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.SetImeOptions(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Entry},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)) método, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usado para definir a opção de ação do método de entrada para o teclado virtual para o [ `Entry` ](xref:Xamarin.Forms.Entry), com o [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) enumeração fornecendo os seguintes valores:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default) – indica que nenhuma chave de ação específica é necessária e que o controle subjacente produzirá si só, se possível.
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None) – indica que nenhuma chave de ação será disponibilizado.
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go) – indica que a chave de ação executará uma operação "go", levando o usuário para o destino do texto digitado.
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search) – indica que a chave de ação executa uma operação de "Pesquisar", levando ao usuário os resultados da pesquisa de texto digitou.
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send) – indica que a chave de ação executará uma operação "Enviar", fornecendo o texto para seu destino.
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next) – indica que a chave de ação executará uma operação "Avançar", direcionando o usuário para o próximo campo que aceitará texto.
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done) – indica que a chave de ação executará uma operação de "concluída", fechando o teclado virtual.
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous) – indica que a chave de ação executará uma operação "anterior", direcionando o usuário para o campo anterior que aceitará texto.
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction) – a máscara para selecionar opções de ação.
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning) – indica que o verificador ortográfico não saiba do usuário, nem sugerir correções com base no qual o usuário tiver digitado anteriormente.
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen) – indica que a interface do usuário não deve estar em tela inteira.
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi) – indica que nenhuma interface do usuário será mostrada para texto extraído.
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction) – indica que nenhuma interface do usuário será exibido para ações personalizadas.

O resultado é que a especificada [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) valor é aplicado para o teclado virtual para o [ `Entry` ](xref:Xamarin.Forms.Entry), que define o método de entrada opções do editor:

[![Entrada específica de plataforma de editor de método de entrada](android-images/entry-imeoptions.png "entrada específica de plataforma de editor de método de entrada")](android-images/entry-imeoptions-large.png#lightbox "entrada específica de plataforma de editor de método de entrada")

## <a name="summary"></a>Resumo

Este artigo demonstrou como consumir os Android plataforma específicos que são integrados ao xamarin. Forms. Específicos de plataforma permitem consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos.

## <a name="related-links"></a>Links relacionados

- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [AndroidSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific/)
- [AndroidSpecific.AppCompat](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat/)
