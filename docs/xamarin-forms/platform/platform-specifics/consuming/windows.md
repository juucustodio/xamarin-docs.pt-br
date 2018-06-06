---
title: Específicos de plataforma do Windows
description: Específicos de plataforma permitem consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo demonstra como utilizar os Windows platform específicos que são integrados ao xamarin. Forms.
ms.prod: xamarin
ms.assetid: 22B403C0-FE6D-498A-AE53-095E6C4B527C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/30/2018
ms.openlocfilehash: 7299de658a3491928e9bbeaa4dd192a8e95c435e
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732795"
---
# <a name="windows-platform-specifics"></a>Específicos de plataforma do Windows

_Específicos de plataforma permitem consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo demonstra como utilizar os Windows platform específicos que são integrados ao xamarin. Forms._

Sobre o Windows UWP (plataforma Universal), xamarin. Forms contém as seguintes especificações de plataforma:

- Definindo opções de posicionamento da barra de ferramentas. Para obter mais informações, consulte [alterando o posicionamento da barra de ferramentas](#toolbar_placement).
- Recolher o [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) barra de navegação. Para obter mais informações, consulte [recolhendo uma barra de navegação MasterDetailPage](#collapsable_navigation_bar).
- Habilitando um [ `WebView` ](xref:Xamarin.Forms.WebView) para exibir alertas de JavaScript em uma caixa de diálogo de mensagem UWP. Para obter mais informações, consulte [exibir alertas de JavaScript](#webview-javascript-alert).
- Habilitando um [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) para interagir com o mecanismo de verificação ortográfica. Para obter mais informações, consulte [habilitando SearchBar de verificação ortográfica](#searchbar-spellcheck).
- Detectando a ordem de leitura do conteúdo de texto [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), e [ `Label` ](xref:Xamarin.Forms.Label) instâncias. Para obter mais informações, consulte [detectando a ordem de leitura do conteúdo](#inputview-readingorder).
- Desabilitar o modo de cor herdados em tem suporte [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obter mais informações, consulte [desabilitar o modo de cor herdado](#legacy-color-mode).
- Habilitar suporte a gestos toque em um [ `ListView` ](xref:Xamarin.Forms.ListView). Para obter mais informações, consulte [Habilitar suporte a gestos toque em uma ListView](#listview-selectionmode).

<a name="toolbar_placement" />

## <a name="changing-the-toolbar-placement"></a>Alterar a posição da barra de ferramentas

Este específica de plataforma é usada para alterar o posicionamento de uma barra de ferramentas em uma [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)e consumido em XAML, definindo o [ `Page.ToolbarPlacement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.ToolbarPlacementProperty/) propriedade anexada a um valor da [ `ToolbarPlacement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement/) enumeração:

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:Page.ToolbarPlacement="Bottom">
  ...
</TabbedPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetToolbarPlacement(ToolbarPlacement.Bottom);
```

O `Page.On<Windows>` método Especifica que este específica de plataforma será executado apenas no Windows. O [ `Page.SetToolbarPlacement` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.SetToolbarPlacement/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.Page}/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement/) método, no [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/) namespace, é usado para definir o posicionamento da barra de ferramentas, com o [ `ToolbarPlacement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement/) fornecendo de enumeração três valores: [ `Default` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Default/), [ `Top` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Top/), e [ `Bottom` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Bottom/).

O resultado é que o posicionamento da barra de ferramentas especificada é aplicado para o [ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/) instância:

[![](windows-images/toolbar-placement.png "Específico da barra de ferramentas da plataforma posicionamento")](windows-images/toolbar-placement-large.png#lightbox "específica de plataforma do posicionamento da barra de ferramentas")

<a name="collapsable_navigation_bar" />

## <a name="collapsing-a-masterdetailpage-navigation-bar"></a>Recolher uma barra de navegação MasterDetailPage

Essa plataforma específica é usada para recolher a barra de navegação em um [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/)e são consumidos no XAML, definindo o [ `MasterDetailPage.CollapseStyle` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapseStyleProperty/) e [ `MasterDetailPage.CollapsedPaneWidth` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidthProperty/)propriedades anexadas:

```xaml
<MasterDetailPage ...
                  xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
                  windows:MasterDetailPage.CollapseStyle="Partial"
                  windows:MasterDetailPage.CollapsedPaneWidth="48">
  ...
</MasterDetailPage>

```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetCollapseStyle(CollapseStyle.Partial).CollapsedPaneWidth(148);
```

O `MasterDetailPage.On<Windows>` método Especifica que este específica de plataforma será executado apenas no Windows. O [ `Page.SetCollapseStyle` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.SetCollapseStyle/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage}/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle/) método, no [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/) namespace, é usado para especificar o estilo de recolher, com o [ `CollapseStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle/) enumeração fornecendo dois valores: [ `Full` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Full/) e [ `Partial` ](https://developer.xamarin.com/api/field/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Partial/). O [ `MasterDetailPage.CollapsedPaneWidth` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidth/p/Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage}/System.Double/) método é usado para especificar a largura de uma barra de navegação colapsada parcialmente.

O resultado é que a especificada [ `CollapseStyle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle/) é aplicado ao [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) instância com a largura também sejam especificada:

[![](windows-images/collapsed-navigation-bar.png "Recolhido específica de plataforma da barra de navegação")](windows-images/collapsed-navigation-bar-large.png#lightbox "recolhido específica de plataforma da barra de navegação")

<a name="webview-javascript-alert" />

## <a name="displaying-javascript-alerts"></a>Exibindo alertas do JavaScript

Essa plataforma específica permite que um [ `WebView` ](xref:Xamarin.Forms.WebView) para exibir alertas de JavaScript em uma caixa de diálogo de mensagem UWP. Ele é consumido em XAML, definindo o [ `WebView.IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabledProperty) anexado a propriedade para um `boolean` valor:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <WebView ... windows:WebView.IsJavaScriptAlertEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

var webView = new Xamarin.Forms.WebView
{
  Source = new HtmlWebViewSource
  {
    Html = @"<html><body><button onclick=""window.alert('Hello World from JavaScript');"">Click Me</button></body></html>"
  }
};
webView.On<Windows>().SetIsJavaScriptAlertEnabled(true);
```

O `WebView.On<Windows>` método Especifica que este específico da plataforma apenas será executado na plataforma Universal do Windows. O [ `WebView.SetIsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.SetIsJavaScriptAlertEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.WebView},System.Boolean)) método, no [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usada para controlar se os alertas JavaScript estão habilitados. Além disso, o `WebView.SetIsJavaScriptAlertEnabled` método pode ser usado para ativar/desativar alertas de JavaScript chamando o [ `IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabled*) método para retornar se eles estão habilitados:

```csharp
_webView.On<Windows>().SetIsJavaScriptAlertEnabled(!_webView.On<Windows>().IsJavaScriptAlertEnabled());
```

O resultado é que os alertas de JavaScript podem ser exibidas em uma caixa de diálogo de mensagem UWP:

![Alerta do WebView JavaScript específico da plataforma](windows-images/webview-javascript-alert.png "WebView JavaScript alerta específico de plataforma")

<a name="searchbar-spellcheck" />

## <a name="enabling-searchbar-spell-check"></a>Habilitar verificação ortográfica SearchBar

Essa plataforma específica permite que um [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) para interagir com o mecanismo de verificação ortográfica. Ele é consumido em XAML, definindo o [ `SearchBar.IsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.IsSpellCheckEnabledProperty) anexado a propriedade para um `boolean` valor:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ... windows:SearchBar.IsSpellCheckEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

searchBar.On<Windows>().SetIsSpellCheckEnabled(true);
```

O `SearchBar.On<Windows>` método Especifica que este específico da plataforma apenas será executado na plataforma Universal do Windows. O [ `SearchBar.SetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.SetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar},System.Boolean)) método, no [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) e no namespace, desativa o verificador ortográfico. Além disso, o `SearchBar.SetIsSpellCheckEnabled` método pode ser usado para alternar o verificador ortográfico chamando o [ `SearchBar.GetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.GetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar})) método para retornar se o verificador ortográfico está habilitado:

```csharp
searchBar.On<Windows>().SetIsSpellCheckEnabled(!searchBar.On<Windows>().GetIsSpellCheckEnabled());
```

O resultado é inserido no texto do [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) pode ser ortográfica verificada, com erros de ortografia incorretos sendo indicados para o usuário:

![Específico de plataforma de verificação de ortografia de SearchBar](windows-images/searchbar-spellcheck.png "SearchBar ortográfica seleção específica da plataforma")

> [!NOTE]
> O `SearchBar` classe no [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) também tem um namespace [ `EnableSpellCheck` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.EnableSpellCheck*) e [ `DisableSpellCheck` ](xre:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.DisableSpellCheck*) métodos que podem ser usados para ativar e desativar o verificador ortográfico no `SearchBar`, respectivamente.

<a name="inputview-readingorder" />

## <a name="detecting-reading-order-from-content"></a>Detectando a ordem de leitura do conteúdo

Essa plataforma específica permite que a ordem de leitura (esquerda para a direita ou à direita para esquerda) de texto bidirecional em [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), e [ `Label` ](xref:Xamarin.Forms.Label) instâncias seja detectada dinamicamente. Ele é consumido em XAML, definindo o [ `InputView.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.DetectReadingOrderFromContentProperty) (para `Entry` e `Editor` instâncias) ou [ `Label.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Label.DetectReadingOrderFromContentProperty) anexado a propriedade para um `boolean` valor:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <Editor ... windows:InputView.DetectReadingOrderFromContent="true" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

editor.On<Windows>().SetDetectReadingOrderFromContent(true);
```

O `Editor.On<Windows>` método Especifica que este específico da plataforma apenas será executado na plataforma Universal do Windows. O [ `InputView.SetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.SetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView},System.Boolean)) método, no [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usada para controlar se a ordem de leitura é detectada do conteúdo no [ `InputView` ](xref:Xamarin.Forms.InputView). Além disso, o `InputView.SetDetectReadingOrderFromContent` método pode ser usado para ativar a ordem de leitura é detectada no conteúdo do chamando o [ `InputView.GetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.GetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView})) método para retornar o valor atual:

```csharp
editor.On<Windows>().SetDetectReadingOrderFromContent(!editor.On<Windows>().GetDetectReadingOrderFromContent());
```

O resultado é que [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), e [ `Label` ](xref:Xamarin.Forms.Label) instâncias podem ter a ordem de leitura de conteúdo dinamicamente detectado:

[![InputView detectando a ordem de leitura do conteúdo específico da plataforma](windows-images/editor-readingorder.png "InputView detectando a ordem de leitura do conteúdo específico da plataforma")](windows-images/editor-readingorder-large.png#lightbox "InputView detectando a ordem de leitura do conteúdo específico da plataforma")

> [!NOTE]
> Ao contrário da configuração de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade, a lógica de exibições que detectar que a ordem de leitura de seu conteúdo de texto não afetará o alinhamento do texto no modo de exibição. Em vez disso, ele ajusta a ordem na qual os blocos de texto bidirecional são dispostos.

<a name="legacy-color-mode" />

## <a name="disabling-legacy-color-mode"></a>Desabilitar o modo de cor herdado

Algumas das exibições de xamarin. Forms apresentam um modo de cor herdado. Nesse modo, quando o [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) propriedade do modo de exibição está definida como `false`, o modo de exibição substituirá as cores definidas pelo usuário com as cores padrão de nativo para o estado desabilitado. Versões anteriores a compatibilidade, esse modo herdado cor permanece o comportamento padrão para exibições com suporte.

Essa plataforma específica desabilita nesse modo herdado de cor, para que permaneçam de cores definidas em uma exibição pelo usuário mesmo quando o modo de exibição está desabilitado. Ele é consumido em XAML, definindo o [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.IsLegacyColorModeEnabledProperty) anexado propriedade `false`:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Editor Text="Enter text here"
                TextColor="Blue"
                BackgroundColor="Bisque"
                windows:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

_legacyColorModeDisabledEditor.On<Windows>().SetIsLegacyColorModeEnabled(false);
```

O `VisualElement.On<Windows>` método Especifica que este específica de plataforma será executado apenas no Windows. O [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Boolean)) método, no [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usada para controlar se o modo de cor herdado está desabilitado. Além disso, o [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})) método pode ser usado para retornar se o modo de cor herdado está desabilitado.

O resultado é que o modo de cor herdado pode ser desabilitado, para que as cores definidas em uma exibição pelo usuário permanecem mesmo quando o modo de exibição está desativado:

![](windows-images/legacy-color-mode-disabled.png "Modo herdado cor desabilitado")

> [!NOTE]
> Ao definir um [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) em uma exibição, o modo de cor herdados completamente será ignorado. Para obter mais informações sobre estados visuais, consulte [o Gerenciador de estado Visual xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

<a name="listview-selectionmode" />

## <a name="enabling-tap-gesture-support-in-a-listview"></a>Habilitando o suporte de gestos de toque em uma ListView

Na plataforma Universal do Windows, por padrão o xamarin. Forms [ `ListView` ](xref:Xamarin.Forms.ListView) usa nativo `ItemClick` evento para responder à interação, em vez de nativo `Tapped` eventos. Isso fornece recursos de acessibilidade para que o Narrador e o teclado podem interagir com o `ListView`. No entanto, ele também processa quaisquer gestos de toque dentro de `ListView` inoperante.

Isso controla específico da plataforma se itens em um [ `ListView` ](xref:Xamarin.Forms.ListView) pode responder para gestos de toque e, portanto, se nativo `ListView` é acionado o `ItemClick` ou `Tapped` evento. Ele é consumido em XAML, definindo o [ `ListView.SelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty) propriedade anexada a um valor da [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) enumeração:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <ListView ... windows:ListView.SelectionMode="Inaccessible">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

listView.On<Windows>().SetSelectionMode(ListViewSelectionMode.Inaccessible);
```

O `ListView.On<Windows>` método Especifica que este específico da plataforma apenas será executado na plataforma Universal do Windows. O [ `ListView.SetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)) método, no [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usado para controlar se itens em uma [ `ListView` ](xref:Xamarin.Forms.ListView) pode responder para toque gestos, com o [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) enumeração fornecendo dois valores possíveis:

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible) – indica que o `ListView` acionarão nativo `ItemClick` evento para manipular a interação e, portanto, fornecer recursos de acessibilidade. Portanto, o Narrador e o teclado podem interagir com o `ListView`. No entanto, os itens no `ListView` não pode responder para gestos de toque. Esse é o comportamento padrão para `ListView` instâncias na plataforma Universal do Windows.
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible) – indica que o `ListView` acionarão nativo `Tapped` eventos para tratar a interação. Portanto, os itens no `ListView` pode responder para gestos de toque. No entanto, não há nenhuma funcionalidade de acessibilidade e, portanto, o Narrador e o teclado não podem interagir com o `ListView`.

> [!NOTE]
> O `Accessible` e `Inaccessible` seleção modos são mutuamente exclusivos, e você precisará escolher entre um acessível [ `ListView` ](xref:Xamarin.Forms.ListView) ou um `ListView` que pode responder a gestos de toque.

Além disso, o [ `GetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.GetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView})) método pode ser usado para retornar o atual [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode).

O resultado é que a especificada [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) é aplicado ao [ `ListView` ](xref:Xamarin.Forms.ListView), que controla se os itens no `ListView` pode responder para gestos de toque e, portanto, se nativo `ListView` dispara o `ItemClick` ou `Tapped` eventos.

## <a name="summary"></a>Resumo

Este artigo demonstrou como utilizar os Windows platform específicos que são integrados ao xamarin. Forms. Específicos de plataforma permitem consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos.

## <a name="related-links"></a>Links relacionados

- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [WindowsSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/)
