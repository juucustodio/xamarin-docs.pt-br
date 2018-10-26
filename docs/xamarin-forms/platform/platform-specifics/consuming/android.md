---
title: Especificidades da plataforma Android
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo demonstra como utilizar as Android-especificidades da plataforma que são criadas no xamarin. Forms.
ms.prod: xamarin
ms.assetid: C5D4AA65-9BAA-4008-8A1E-36CDB78A435D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/01/2018
ms.openlocfilehash: 50c7b05261cf3f07ea37373cdcdcc8f250243647
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108971"
---
# <a name="android-platform-specifics"></a>Especificidades da plataforma Android

_Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo demonstra como utilizar as Android-especificidades da plataforma que são criadas no xamarin. Forms._

## <a name="visualelements"></a>VisualElements

No Android, a seguinte funcionalidade específica da plataforma é fornecida para modos de exibição, páginas e layouts do xamarin. Forms:

- Controlando a ordem Z dos elementos visuais para determinar a ordem de desenho. Para obter mais informações, consulte [controlando a elevação de elementos visuais](#elevation).
- Desabilitar o modo de cor herdados em um com suporte [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obter mais informações, consulte [Desabilitar modo de cor herdado](#legacy-color-mode).

<a name="elevation" />

### <a name="controlling-the-elevation-of-visual-elements"></a>Controlando a elevação de elementos visuais

Este específicos da plataforma é usado para controlar a ordem Z, de elementos visuais em aplicativos ou a elevação destinados API 21 ou maior. A elevação de um elemento visual determina sua ordem de desenho, com elementos visuais com valores mais altos de Z occluding elementos visuais com baixos valores de Z. Ele é consumido em XAML, definindo o `VisualElement.Elevation` anexado à propriedade um `boolean` valor:

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

O `Button.On<Android>` método Especifica que este específicos da plataforma serão executado apenas no Android. O `VisualElement.SetElevation` método, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usado para definir a elevação do elemento visual como um valor anulável `float`. Além disso, o `VisualElement.GetElevation` método pode ser usado para recuperar o valor de elevação de um elemento visual.

O resultado é que a elevação de elementos visuais pode ser controlada, de modo que os elementos visuais com valores de Z maior occlude elementos visuais com baixos valores de Z. Portanto, no exemplo a segunda [ `Button` ](xref:Xamarin.Forms.Button) é renderizado acima a [ `BoxView` ](xref:Xamarin.Forms.BoxView) porque ele tem um valor mais alto de elevação:

![](android-images/elevation.png)

<a name="legacy-color-mode" />

### <a name="disabling-legacy-color-mode"></a>Desabilitar o modo de cor herdado

Algumas das exibições de xamarin. Forms apresentam um modo de cor herdado. Nesse modo, quando o [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) propriedade do modo de exibição é definida como `false`, o modo de exibição substituirá as cores definidas pelo usuário com os nativo as cores padrão para o estado desabilitado. Para versões anteriores compatibilidade, esse modo herdado cor permanece o comportamento padrão para modos de exibição com suporte.

Este específicos da plataforma desabilita nesse modo herdado de cor, para que permaneçam de cores definido em uma exibição pelo usuário, mesmo quando o modo de exibição está desabilitado. Ele é consumido em XAML, definindo o [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.IsLegacyColorModeEnabledProperty) anexado à propriedade `false`:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                android:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

_legacyColorModeDisabledButton.On<Android>().SetIsLegacyColorModeEnabled(false);
```

O `VisualElement.On<Android>` método Especifica que este específicos da plataforma serão executado apenas no Android. O [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement},System.Boolean)) método, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usada para controlar se o modo de cor herdados é desabilitado. Além disso, o [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement})) método pode ser usado para retornar se o modo de cor herdado está desabilitado.

O resultado é que o modo herdado de cor pode ser desabilitado, para que as cores definidas em uma exibição pelo usuário permaneçam até mesmo quando o modo de exibição está desabilitado:

![](android-images/legacy-color-mode-disabled.png "Modo herdado de cor desabilitado")

> [!NOTE]
> Ao definir uma [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) em uma exibição, o modo herdado de cor é totalmente ignorado. Para obter mais informações sobre estados visuais, consulte [o Gerenciador de estado Visual xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="views"></a>Exibições

No Android, a seguinte funcionalidade específica da plataforma é fornecida para modos de exibição do xamarin. Forms:

- Usando o preenchimento padrão e os valores de sombra dos botões do Android. Para obter mais informações, consulte [botões do Android usando](#button-padding-shadow).
- Configurando o método de entrada opções do editor para o teclado virtual para um [ `Entry` ](xref:Xamarin.Forms.Entry). Para obter mais informações, consulte [opções de configuração de entrada do IME](#entry-imeoptions).
- Habilitar a rolagem rápida em um [ `ListView` ](xref:Xamarin.Forms.ListView) para obter mais informações, consulte [habilitando o recurso de rolagem rápida em um ListView](#fastscroll).
- Controlar se uma [ `WebView` ](xref:Xamarin.Forms.WebView) pode exibir o conteúdo misto. Para obter mais informações, consulte [ativando misto conteúdo em um WebView](#webview-mixed-content).

<a name="button-padding-shadow" />

### <a name="using-android-buttons"></a>Usando os botões do Android

Este específicos da plataforma controla se os botões do xamarin. Forms usam o preenchimento padrão e os valores de sombra dos botões do Android. Ele é consumido em XAML, definindo o [ `Button.UseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPaddingProperty) e [ `Button.UseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadowProperty) propriedades para anexadas `boolean` valores:

```xaml
<ContentPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button ...
                android:Button.UseDefaultPadding="true"
                android:Button.UseDefaultShadow="true" />         
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

button.On<Android>().SetUseDefaultPadding(true).SetUseDefaultShadow(true);
```

O `Button.On<Android>` método Especifica que este específicos da plataforma serão executado apenas no Android. O [ `Button.SetUseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.SetUseDefaultPadding(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button},System.Boolean)) e[ `Button.SetUseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.SetUseDefaultShadow(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button},System.Boolean)) métodos, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, são usadas para controlar se os botões de xamarin. Forms usam o padrão preenchimento e valores de sombra dos botões do Android. Além disso, o [ `Button.UseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPadding(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button})) e [ `Button.UseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadow(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button})) métodos podem ser usados para retornar se um botão usa o padrão de preenchimento de valor e o valor padrão de sombra, respectivamente.

O resultado é que os botões de xamarin. Forms podem usar o preenchimento padrão e os valores de sombra dos botões do Android:

![](android-images/button-padding-and-shadow.png "Modo herdado de cor desabilitado")

Observe que, na captura de tela acima de cada [ `Button` ](xref:Xamarin.Forms.Button) tem definições idênticas, exceto que o direito `Button` usa o preenchimento padrão e valores de sombra dos botões do Android.

<a name="entry-imeoptions" />

### <a name="setting-entry-input-method-editor-options"></a>Opções de configuração de entrada do IME

Essa plataforma específica define o método de entrada opções de IME (editor) para o teclado virtual para um [ `Entry` ](xref:Xamarin.Forms.Entry). Isso inclui a definição no botão de ação do usuário no canto inferior do teclado virtual e as interações com o `Entry`. Ele é consumido em XAML, definindo o [ `Entry.ImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty) propriedade anexada a um valor da [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) enumeração:

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

O `Entry.On<Android>` método Especifica que este específicos da plataforma serão executado apenas no Android. O [ `Entry.SetImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.SetImeOptions(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Entry},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)) método, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usado para definir a opção de ação do método de entrada para o teclado virtual para o [ `Entry` ](xref:Xamarin.Forms.Entry), com o [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) fornecendo os seguintes valores de enumeração:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default) – indica que nenhuma chave de ação específica é necessária e que o controle subjacente produzirá o seu próprio se possível. Isso será `Next` ou `Done`.
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None) – indica que nenhuma chave de ação será disponibilizado.
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go) – indica que a chave de ação executará uma operação de "Ir", direcionando o usuário para o destino do texto digitado.
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search) – indica que a chave de ação executa uma operação de "Pesquisar", direcionando o usuário para os resultados da pesquisa para o texto que tiver digitado.
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send) – indica que a chave de ação executará uma operação de "Enviar", fornecendo o texto para seu destino.
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next) – indica que a chave de ação executará uma operação de "next", direcionando o usuário para o próximo campo que aceita texto.
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done) – indica que a chave de ação executará uma operação de "done", fechando o teclado virtual.
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous) – indica que a chave de ação executará uma operação "anterior", direcionando o usuário para o campo anterior que aceita texto.
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction) – a máscara para selecionar opções de ação.
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning) – indica que o verificador ortográfico não saiba do usuário, nem sugerir correções com base no qual o usuário tiver digitado anteriormente.
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen) – indica que a interface do usuário não deve estar em tela inteira.
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi) – indica que nenhuma interface do usuário será mostrada para texto extraído.
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction) – indica que nenhuma interface do usuário será exibido para ações personalizadas.

O resultado é que a especificada [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) valor é aplicado para o teclado virtual para o [ `Entry` ](xref:Xamarin.Forms.Entry), que define o método de entrada, as opções do editor:

[![Método editor específico da plataforma de entrada de entrada](android-images/entry-imeoptions.png "específicos de plataforma de editor de método de entrada de entrada")](android-images/entry-imeoptions-large.png#lightbox "específicos de plataforma de editor de método de entrada de entrada")

<a name="fastscroll" />

### <a name="enabling-fast-scrolling-in-a-listview"></a>Habilitar a rolagem rápida em um ListView

Este específicos da plataforma é usado para habilitar a rolagem rápida por meio de dados em um [ `ListView` ](xref:Xamarin.Forms.ListView). Ele é consumido em XAML, definindo o `ListView.IsFastScrollEnabled` anexado à propriedade um `boolean` valor:

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

O `ListView.On<Android>` método Especifica que este específicos da plataforma serão executado apenas no Android. O `ListView.SetIsFastScrollEnabled` método, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usado para habilitar a rolagem rápida por meio de dados em um [ `ListView` ](xref:Xamarin.Forms.ListView). Além disso, o `SetIsFastScrollEnabled` método pode ser usado para ativar/desativar a rolagem rápida chamando o `IsFastScrollEnabled` método para retornar se a rolagem rápida está habilitada:

```csharp
listView.On<Android>().SetIsFastScrollEnabled(!listView.On<Android>().IsFastScrollEnabled());
```

O resultado é que rolagem rápida por meio de dados em um [ `ListView` ](xref:Xamarin.Forms.ListView) pode ser habilitado, que altera o tamanho de rolagem:

[![](android-images/fastscroll.png "ListView FastScroll específicos da plataforma")](android-images/fastscroll-large.png#lightbox "ListView FastScroll Plaform-Specific")

<a name="webview-mixed-content" />

### <a name="enabling-mixed-content-in-a-webview"></a>Habilitar conteúdo misturado no WebView

Este controles específicos da plataforma se um [ `WebView` ](xref:Xamarin.Forms.WebView) pode exibir conteúdo misto em aplicativos destinados a API 21 ou maior. Conteúdo misto é que é inicialmente carregada em uma conexão HTTPS, mas que carrega os recursos (como imagens, áudio, vídeo, folhas de estilo, scripts) em uma conexão HTTP. Ele é consumido em XAML, definindo o [ `WebView.MixedContentMode` ](x:ref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty) propriedade anexada a um valor da [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) enumeração:

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

O `WebView.On<Android>` método Especifica que este específicos da plataforma serão executado apenas no Android. O [ `WebView.SetMixedContentMode` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.SetMixedContentMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.WebView},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)) método, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usada para controlar se o conteúdo misto pode ser exibido, com o [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) enumeração fornecendo três valores possíveis:

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) – indica que o [ `WebView` ](xref:Xamarin.Forms.WebView) permitirá que uma origem HTTPS carregar conteúdo de uma origem HTTP.
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) – indica que o [ `WebView` ](xref:Xamarin.Forms.WebView) não permitirá que uma origem HTTPS carregar conteúdo de uma origem HTTP.
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) – indica que o [ `WebView` ](xref:Xamarin.Forms.WebView) tentará ser compatível com a abordagem do navegador da web mais recente do dispositivo. Parte do conteúdo HTTP poderá ter permissão para ser carregado por uma origem HTTPS e outros tipos de conteúdo serão bloqueados. Os tipos de conteúdo que são bloqueados ou permitidos podem ser alterados a cada versão do sistema operacional.

O resultado é que a especificada [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) valor é aplicado para o [ `WebView` ](xref:Xamarin.Forms.WebView), que controla se o conteúdo misto pode ser exibido:

[![WebView misto de manipulação de conteúdo específico da plataforma](android-images/webview-mixedcontent.png "WebView misto de manipulação de conteúdo específico da plataforma")](android-images/webview-mixedcontent-large.png#lightbox "WebView misto de manipulação de conteúdo específico da plataforma")

## <a name="pages"></a>Pages (Páginas)

No Android, a seguinte funcionalidade específica da plataforma é fornecida para páginas do xamarin. Forms:

- Definir a altura da barra de navegação em um [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Para obter mais informações, consulte [definindo a altura da barra de navegação em uma NavigationPage](#navigationpage-barheight).
- Desabilitar animações de transição ao navegar entre páginas em um [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Para obter mais informações, consulte [desabilitar animações de transição de página em uma TabbedPage](#tabbedpage-transition-animations).
- Habilitando o dedo entre páginas em um [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Para obter mais informações, consulte [permitindo passar o dedo entre páginas em uma TabbedPage](#enable_swipe_paging).
- Definir o posicionamento da barra de ferramentas e a cor em um [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Para obter mais informações, consulte [configuração TabbedPage barra de ferramentas de posicionamento e a cor](#tabbedpage-toolbar).

<a name="navigationpage-barheight" />

### <a name="setting-the-navigation-bar-height-on-a-navigationpage"></a>Definir a barra de navegação altura em uma NavigationPage

Essa plataforma específica define a altura da barra de navegação de um [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Ele é consumido em XAML, definindo o [ `NavigationPage.BarHeight` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) propriedade associável para um valor inteiro:

```xaml
<NavigationPage ...
                xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
                android:NavigationPage.BarHeight="450">
    ...
</NavigationPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;
...

public class AndroidNavigationPageCS : Xamarin.Forms.NavigationPage
{
    public AndroidNavigationPageCS()
    {
        On<Android>().SetBarHeight(450);
    }
}
```

O `NavigationPage.On<Android>` método Especifica que essa plataforma específica só será executada em compatibilidade de aplicativos Android. O [ `NavigationPage.SetBarHeight` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.SetBarHeight(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.NavigationPage},System.Int32)) método, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) namespace, é usado para definir a altura da barra de navegação em um [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Além disso, o [ `NavigationPage.GetBarHeight` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.GetBarHeight(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.NavigationPage})) método pode ser usado para retornar a altura da barra de navegação a `NavigationPage`.

O resultado é que a altura da barra de navegação em um [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) podem ser definidos:

![](android-images/navigationpage-barheight.png "Altura da barra de navegação NavigationPage")

<a name="tabbedpage-transition-animations" />

### <a name="disabling-page-transition-animations-in-a-tabbedpage"></a>Desabilitar animações de transição de página em uma TabbedPage

Este específicos da plataforma é usado para desabilitar animações de transição ao navegar pelas páginas, seja programaticamente ou ao usar a barra de guias, em um [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Ele é consumido em XAML, definindo o `TabbedPage.IsSmoothScrollEnabled` para a propriedade associável `false`:

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.IsSmoothScrollEnabled="false">
    ...
</TabbedPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetIsSmoothScrollEnabled(false);
```

O `TabbedPage.On<Android>` método Especifica que este específicos da plataforma serão executado apenas no Android. O `TabbedPage.SetIsSmoothScrollEnabled` método, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usada para controlar se as animações de transição serão exibidas quando navegar entre páginas em um [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Além disso, o `TabbedPage` classe o `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` namespace também tem os seguintes métodos:

- `IsSmoothScrollEnabled`, que é usada para recuperar se animações de transição serão exibidas quando navegar entre páginas em um `TabbedPage`.
- `EnableSmoothScroll`, que é usado para habilitar animações de transição ao navegar entre páginas em um `TabbedPage`.
- `DisableSmoothScroll`, que é usado para desabilitar animações de transição ao navegar entre páginas em um `TabbedPage`.

<a name="enable_swipe_paging" />

### <a name="enabling-swiping-between-pages-in-a-tabbedpage"></a>Habilitando o dedo entre páginas em uma TabbedPage

Este específicos da plataforma é usado para habilitar o dedo com um gesto horizontal dedo entre páginas em um [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Ele é consumido em XAML, definindo o [ `TabbedPage.IsSwipePagingEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty) anexado à propriedade um `boolean` valor:

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

O `TabbedPage.On<Android>` método Especifica que este específicos da plataforma serão executado apenas no Android. O [ `TabbedPage.SetIsSwipePagingEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetIsSwipePagingEnabled(Xamarin.Forms.BindableObject,System.Boolean)) método, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usado para habilitar o dedo entre páginas em um [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Além disso, o `TabbedPage` classe na `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` namespace também tem um [ `EnableSwipePaging` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.EnableSwipePaging(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})) método que permite que esse específicos da plataforma, e uma [ `DisableSwipePaging` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.DisableSwipePaging(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})) método desabilita Essa plataforma específica. O [ `TabbedPage.OffscreenPageLimit` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty) propriedade anexada e [ `SetOffscreenPageLimit` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetOffscreenPageLimit(Xamarin.Forms.BindableObject,System.Int32)) método, são usadas para definir o número de páginas que devem ser mantidos em um estado ocioso em ambos os lados da página atual.

O resultado é que paginação passe o dedo para as páginas exibidas por um [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) está habilitado:

![](android-images/tabbedpage-swipe.png)

<a name="tabbedpage-toolbar" />

### <a name="setting-tabbedpage-toolbar-placement-and-color"></a>Cor e o posicionamento da barra de ferramentas TabbedPage de configuração

Esses itens específicos à plataforma são usadas para definir o posicionamento e a cor da barra de ferramentas em uma [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Eles são consumidos no XAML, definindo o [ `TabbedPage.ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.ToolbarPlacementProperty) propriedade anexada a um valor da [ `ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) enumeração e o [ `TabbedPage.BarItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarItemColorProperty) e [ `TabbedPage.BarSelectedItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarSelectedItemColorProperty) anexado propriedades para um [ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.ToolbarPlacement="Bottom"
            android:TabbedPage.BarItemColor="Black"
            android:TabbedPage.BarSelectedItemColor="Red">
    ...
</TabbedPage>
```

Como alternativa, eles podem ser consumidos em c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetToolbarPlacement(ToolbarPlacement.Bottom)
             .SetBarItemColor(Color.Black)
             .SetBarSelectedItemColor(Color.Red);
```

O `TabbedPage.On<Android>` método Especifica que esses itens específicos à plataforma será executado apenas no Android. O [ `TabbedPage.SetToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetToolbarPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement)) método, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usado para definir o posicionamento da barra de ferramentas em um [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), com o [ `ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) fornecendo os seguintes valores de enumeração:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Default) – indica que a barra de ferramentas é colocada no local padrão na página. Esta é a parte superior da página em telefones e a parte inferior da página em outros idiomas de dispositivo.
- [`Top`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Top) – indica que a barra de ferramentas é colocada na parte superior da página.
- [`Bottom`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Bottom) – indica que a barra de ferramentas é colocada na parte inferior da página.

Além disso, o [ `TabbedPage.SetBarItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetBarItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage},Xamarin.Forms.Color)) e [ `TabbedPage.SetBarSelectedItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.SetBarSelectedItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage},Xamarin.Forms.Color)) métodos são usados para definir a cor dos itens da barra de ferramentas e itens de barra de ferramentas selecionado, respectivamente.

> [!NOTE]
> O [ `GetToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.GetToolbarPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})), [ `GetBarItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.GetBarItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})), e [ `GetBarSelectedItemColor` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.GetBarSelectedItemColor(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.TabbedPage})) métodos podem ser usados para recuperar o posicionamento e as cores a [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) barra de ferramentas.

O resultado é que o posicionamento da barra de ferramentas, a cor dos itens da barra de ferramentas e a cor do item da barra de ferramentas selecionado podem ser definidas em um [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage):

![](android-images/tabbedpage-toolbar-placement.png)

## <a name="application"></a>Aplicativo

No Android, a seguinte funcionalidade específica da plataforma é fornecida para o xamarin. Forms [ `Application` ](xref:Xamarin.Forms.Application) classe:

- Definindo o modo de operação de um teclado virtual. Para obter mais informações, consulte [definindo o modo de entrada de teclado reversível](#soft_input_mode).
- Desabilitando o [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) e [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) página eventos de ciclo de vida em pausa e retomar, respectivamente, para aplicativos que usam AppCompat. Para obter mais informações, consulte [desabilitando a eventos de ciclo de vida de página que aparece e Disappearing](#disable_lifecycle_events).

<a name="soft_input_mode" />

### <a name="setting-the-soft-keyboard-input-mode"></a>Definindo o modo de entrada do teclado virtual

Este específicos da plataforma usado para definir o modo de operação para uma área de entrada do teclado virtual e é consumido em XAML, definindo o [ `Application.WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.WindowSoftInputModeAdjustProperty) propriedade anexada a um valor da [ `WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) enumeração:

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

O `Application.On<Android>` método Especifica que este específicos da plataforma serão executado apenas no Android. O [ `Application.UseWindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Application.UseWindowSoftInputModeAdjust(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust)) método, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usado para definir o modo de operação de área de entrada de teclado virtual, com o [ `WindowSoftInputModeAdjust` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust) enumeração fornecendo dois valores: [ `Pan` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Pan) e [ `Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize). O `Pan` valor usa o [ `AdjustPan` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustPan/) opção de ajuste, o que não redimensione a janela quando um controle de entrada tem o foco. Em vez disso, o conteúdo da janela estendido para que o foco atual não é obscurecido pelo teclado virtual. O `Resize` valor usa o [ `AdjustResize` ](https://developer.xamarin.com/api/field/Android.Views.SoftInput.AdjustResize/) opção de ajuste, que redimensiona a janela quando um controle de entrada tem o foco, para liberar espaço para o teclado virtual.

O resultado é que o teclado virtual área modo operacional pode ser definido quando um controle de entrada tem o foco de entrada:

[![](android-images/pan-resize.png "Teclado virtual operacional modo específico da plataforma")](android-images/pan-resize-large.png#lightbox "Soft Keyboard Operating Mode Plaform-Specific")

<a name="disable_lifecycle_events" />

### <a name="disabling-the-disappearing-and-appearing-page-lifecycle-events"></a>Desabilitando os eventos de ciclo de vida da página aparecendo e desaparecendo

Este específicos da plataforma é usado para desabilitar o [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) e [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) eventos de página no aplicativo pausar e retomar, respectivamente, para aplicativos que usam AppCompat. Além disso, ele inclui a capacidade de controlar se o teclado virtual é exibido ao continuar, se ele foi exibido em pausa, desde que o modo de operação do teclado virtual é definido para [ `WindowSoftInputModeAdjust.Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize).

> [!NOTE]
> Observe que esses eventos são habilitados por padrão para preservar o comportamento existente para aplicativos que dependem dos eventos. Desabilitar esses eventos faz com que o ciclo de eventos AppCompat corresponder o ciclo de evento de pré-AppCompat.

Este específicos da plataforma podem ser consumidos em XAML, definindo o [ `Application.SendDisappearingEventOnPause` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPauseProperty), [ `Application.SendAppearingEventOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResumeProperty), e [ `Application.ShouldPreserveKeyboardOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResumeProperty) propriedades anexadas para `boolean` valores:

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

O `Application.Current.On<Android>` método Especifica que este específicos da plataforma serão executado apenas no Android. O [ `Application.SendDisappearingEventOnPause` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendDisappearingEventOnPause(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean)) método, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) namespace, é usado para habilitar ou desabilitar o acionamento de [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) eventos de página, quando o aplicativo entra em segundo plano. O [ `Application.SendAppearingEventOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.SendAppearingEventOnResume(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean)) método é usado para habilitar ou desabilitar o acionamento de [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) evento de página, quando o aplicativo é retomado do plano de fundo. O [ `Application.ShouldPreserveKeyboardOnResume` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.Application.ShouldPreserveKeyboardOnResume(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Application},System.Boolean)) método é usado o controle se o teclado virtual é exibido ao continuar, se ele foi exibido em pausa, fornecido que o modo de operação do teclado virtual é definido como [ `WindowSoftInputModeAdjust.Resize` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WindowSoftInputModeAdjust.Resize).

O resultado é que o [ `Disappearing` ](xref:Xamarin.Forms.Page.Appearing) e [ `Appearing` ](xref:Xamarin.Forms.Page.Appearing) eventos de página não serão acionados em Pausar o aplicativo e retomar, respectivamente, e o teclado virtual de que, se foi exibido quando o aplicativo foi colocado em pausa, ele também será exibido quando o aplicativo é retomado:

[![](android-images/keyboard-on-resume.png "Específicos de plataforma de eventos de ciclo de vida")](android-images/keyboard-on-resume-large.png#lightbox "específicos de plataforma de eventos de ciclo de vida")

## <a name="summary"></a>Resumo

Este artigo demonstrou como consumir as Android-especificidades da plataforma que são criadas no xamarin. Forms. Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos.

## <a name="related-links"></a>Links relacionados

- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (amostra)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
