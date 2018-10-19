---
title: Especificidades da plataforma Windows
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo demonstra como utilizar as Windows-especificidades da plataforma que são criadas no xamarin. Forms.
ms.prod: xamarin
ms.assetid: 22B403C0-FE6D-498A-AE53-095E6C4B527C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 2130bbbedbab66fac9427947ca42f21c346360ce
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2018
ms.locfileid: "38998401"
---
# <a name="windows-platform-specifics"></a>Especificidades da plataforma Windows

_Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo demonstra como utilizar as Windows-especificidades da plataforma que são criadas no xamarin. Forms._

## <a name="visualelements"></a>VisualElements

Na plataforma Universal do Windows, a seguinte funcionalidade específica da plataforma é fornecida para modos de exibição, páginas e layouts do xamarin. Forms:

- Definindo uma tecla de acesso para um [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obter mais informações, consulte [chaves de acesso da configuração VisualElement](#visualelement-accesskeys).
- Desabilitar o modo de cor herdados em um com suporte [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Para obter mais informações, consulte [Desabilitar modo de cor herdado](#legacy-color-mode).

<a name="visualelement-accesskeys" />

### <a name="setting-visualelement-access-keys"></a>Definir chaves de acesso VisualElement

Chaves de acesso são atalhos de teclado que melhoram a usabilidade e a acessibilidade de aplicativos na plataforma Universal do Windows, fornecendo uma maneira intuitiva para os usuários a navegar rapidamente e interagir com a interface do usuário visível do aplicativo por meio de um teclado em vez de por meio de toque ou um mouse. Eles são combinações da tecla Alt e uma ou mais chaves alfanuméricas, normalmente pressionadas em sequência. Atalhos de teclado automaticamente têm suporte para chaves de acesso que usam um único caractere alfanumérico.

Dicas de tecla de acesso são flutuantes selos exibidos ao lado de controles que incluem as chaves de acesso. Cada dica de tecla de acesso contém as chaves alfanuméricas que ativam o controle associado. Quando um usuário pressiona a tecla Alt, as dicas de tecla de acesso são exibidas.

Essa plataforma específica é usada para especificar uma chave de acesso para um [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Ele é consumido em XAML, definindo o [ `VisualElement.AccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyProperty) propriedade anexada para um valor alfanumérico e, opcionalmente, definindo o [ `VisualElement.AccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyPlacementProperty) propriedade anexada com o valor de [ `AccessKeyPlacement` ](xref:Xamarin.Forms.AccessKeyPlacement) enumeração, o [ `VisualElement.AccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyHorizontalOffsetProperty) anexado à propriedade um `double`e a [ `VisualElement.AccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyVerticalOffsetProperty) propriedade anexada a um `double`:

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <ContentPage Title="Page 1"
                 windows:VisualElement.AccessKey="1">
        <StackLayout Margin="20">
            ...
            <Switch windows:VisualElement.AccessKey="A" />
            <Entry Placeholder="Enter text here"
                   windows:VisualElement.AccessKey="B" />
            ...
            <Button Text="Access key F, placement top with offsets"
                    Margin="20"
                    Clicked="OnButtonClicked"
                    windows:VisualElement.AccessKey="F"
                    windows:VisualElement.AccessKeyPlacement="Top"
                    windows:VisualElement.AccessKeyHorizontalOffset="20"
                    windows:VisualElement.AccessKeyVerticalOffset="20" />
            ...
        </StackLayout>
    </ContentPage>
    ...
</TabbedPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

var page = new ContentPage { Title = "Page 1" };
page.On<Windows>().SetAccessKey("1");

var switchView = new Switch();
switchView.On<Windows>().SetAccessKey("A");
var entry = new Entry { Placeholder = "Enter text here" };
entry.On<Windows>().SetAccessKey("B");
...

var button4 = new Button { Text = "Access key F, placement top with offsets", Margin = new Thickness(20) };
button4.Clicked += OnButtonClicked;
button4.On<Windows>()
    .SetAccessKey("F")
    .SetAccessKeyPlacement(AccessKeyPlacement.Top)
    .SetAccessKeyHorizontalOffset(20)
    .SetAccessKeyVerticalOffset(20);
...
```

O `VisualElement.On<Windows>` método Especifica que este específicos da plataforma só serão executado na plataforma Universal do Windows. O [ `VisualElement.SetAccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKey(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.String)) método, no [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usado para definir o valor de chave de acesso para o `VisualElement`. O [ `VisualElement.SetAccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},Xamarin.Forms.AccessKeyPlacement)) método, opcionalmente, especifica a posição a ser usado para exibir a dica de tecla de acesso, com o [ `AccessKeyPlacement` ](xref:Xamarin.Forms.AccessKeyPlacement) enumeração fornecendo os seguintes valores possíveis:

- [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto) – indica que o posicionamento da dica de tecla de acesso será determinado pelo sistema operacional.
- [`Top`](xref:Xamarin.Forms.AccessKeyPlacement.Top) – indica que a dica de tecla de acesso será exibido acima da borda superior do `VisualElement`.
- [`Bottom`](xref:Xamarin.Forms.AccessKeyPlacement.Bottom) – indica que a dica de tecla de acesso será exibido abaixo da borda inferior do `VisualElement`.
- [`Right`](xref:Xamarin.Forms.AccessKeyPlacement.Right) – indica que a dica de tecla de acesso será exibido à direita da borda direita do `VisualElement`.
- [`Left`](xref:Xamarin.Forms.AccessKeyPlacement.Left) – indica que a dica de tecla de acesso será exibido à esquerda da borda esquerda do `VisualElement`.
- [`Center`](xref:Xamarin.Forms.AccessKeyPlacement.Center) – indica que a dica de tecla de acesso aparecem sobreposta no centro do `VisualElement`.

> [!NOTE]
> Normalmente, o [ `Auto` ](xref:Xamarin.Forms.AccessKeyPlacement.Auto) posicionamento da dica de tecla é suficiente, que inclui suporte para interfaces do usuário adaptável.

O [ `VisualElement.SetAccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyHorizontalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Double)) e [ `VisualElement.SetAccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetAccessKeyVerticalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Double)) métodos podem ser usados para um controle mais granular do local de dica de tecla de acesso. O argumento para o `SetAccessKeyHorizontalOffset` método indica como o momento para mover a dica de tecla de acesso para a esquerda ou direita e o argumento para o `SetAccessKeyVerticalOffset` método indica o quanto mover a dica de tecla de acesso para cima ou para baixo.

>[!NOTE]
> Deslocamentos de dica de tecla de acesso não podem ser definidos quando o posicionamento de chave de acesso é definido `Auto`.

Além disso, o [ `GetAccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKey(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})), [ `GetAccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})), [ `GetAccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyHorizontalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})), e [ `GetAccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetAccessKeyVerticalOffset(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})) métodos podem ser usados para recuperar um acesso de valor e do local da chave.

O resultado é que as dicas de tecla de acesso podem ser exibidas ao lado de qualquer [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) instâncias que definem a acessar as chaves, pressionando a tecla Alt:

![Específico da plataforma de teclas de acesso VisualElement](windows-images/visualelement-accesskeys.png "VisualElement acesso chaves específicas da plataforma")

Quando um usuário ativa uma chave de acesso, pressionando a tecla Alt, seguida pelo acesso de chave, a ação padrão para o `VisualElement` será executado. Por exemplo, quando um usuário ativa a chave de acesso em uma [ `Switch` ](xref:Xamarin.Forms.Switch), o `Switch` é alternada. Quando um usuário ativa a chave de acesso em uma [ `Entry` ](xref:Xamarin.Forms.Entry), o `Entry` ganha o foco. Quando um usuário ativa a chave de acesso em uma [ `Button` ](xref:Xamarin.Forms.Button), o manipulador de eventos para o [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) eventos é executado.

Para obter mais informações sobre chaves de acesso, consulte [chaves de acesso](/windows/uwp/design/input/access-keys#key-tip-positioning).

<a name="legacy-color-mode" />

### <a name="disabling-legacy-color-mode"></a>Desabilitar o modo de cor herdado

Algumas das exibições de xamarin. Forms apresentam um modo de cor herdado. Nesse modo, quando o [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) propriedade do modo de exibição é definida como `false`, o modo de exibição substituirá as cores definidas pelo usuário com os nativo as cores padrão para o estado desabilitado. Para versões anteriores compatibilidade, esse modo herdado cor permanece o comportamento padrão para modos de exibição com suporte.

Este específicos da plataforma desabilita nesse modo herdado de cor, para que permaneçam de cores definido em uma exibição pelo usuário, mesmo quando o modo de exibição está desabilitado. Ele é consumido em XAML, definindo o [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.IsLegacyColorModeEnabledProperty) anexado à propriedade `false`:

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

O `VisualElement.On<Windows>` método Especifica que este específicos da plataforma serão executado apenas no Windows. O [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Boolean)) método, no [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usada para controlar se o modo de cor herdados é desabilitado. Além disso, o [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})) método pode ser usado para retornar se o modo de cor herdado está desabilitado.

O resultado é que o modo herdado de cor pode ser desabilitado, para que as cores definidas em uma exibição pelo usuário permaneçam até mesmo quando o modo de exibição está desabilitado:

![](windows-images/legacy-color-mode-disabled.png "Modo herdado de cor desabilitado")

> [!NOTE]
> Ao definir uma [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) em uma exibição, o modo herdado de cor é totalmente ignorado. Para obter mais informações sobre estados visuais, consulte [o Gerenciador de estado Visual xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="views"></a>Exibições

Na Universal Windows Platform (UWP), a seguinte funcionalidade específica da plataforma é fornecida para modos de exibição do xamarin. Forms:

- Detectando a ordem de leitura do conteúdo de texto [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), e [ `Label` ](xref:Xamarin.Forms.Label) instâncias. Para obter mais informações, consulte [detectando a ordem de leitura do conteúdo](#inputview-readingorder).
- Habilitando o suporte de gestos de toque em um [ `ListView` ](xref:Xamarin.Forms.ListView). Para obter mais informações, consulte [Habilitar suporte de gestos de toque em um ListView](#listview-selectionmode).
- Habilitando um [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) para interagir com o mecanismo de verificação ortográfica. Para obter mais informações, consulte [habilitando SearchBar de verificação ortográfica](#searchbar-spellcheck).
- Habilitando um [ `WebView` ](xref:Xamarin.Forms.WebView) para exibir alertas de JavaScript em uma caixa de diálogo de mensagem UWP. Para obter mais informações, consulte [exibindo alertas de JavaScript](#webview-javascript-alert).

<a name="inputview-readingorder" />

### <a name="detecting-reading-order-from-content"></a>Detectando a ordem de leitura do conteúdo

Este específicos da plataforma permite que a ordem de leitura (esquerda para a direita ou à direita para esquerda) do texto bidirecional no [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), e [ `Label` ](xref:Xamarin.Forms.Label) instâncias para ser detectada dinamicamente. Ele é consumido em XAML, definindo o [ `InputView.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.DetectReadingOrderFromContentProperty) (para `Entry` e `Editor` instâncias) ou [ `Label.DetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Label.DetectReadingOrderFromContentProperty) anexado à propriedade um `boolean` valor:

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

O `Editor.On<Windows>` método Especifica que este específicos da plataforma só serão executado na plataforma Universal do Windows. O [ `InputView.SetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.SetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView},System.Boolean)) método, no [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usada para controlar se a ordem de leitura é detectada de conteúdo a [ `InputView` ](xref:Xamarin.Forms.InputView). Além disso, o `InputView.SetDetectReadingOrderFromContent` método pode ser usado para ativar ou desativar a ordem de leitura é detectada do conteúdo por meio da chamada a [ `InputView.GetDetectReadingOrderFromContent` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.InputView.GetDetectReadingOrderFromContent(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.InputView})) método para retornar o valor atual:

```csharp
editor.On<Windows>().SetDetectReadingOrderFromContent(!editor.On<Windows>().GetDetectReadingOrderFromContent());
```

O resultado é que [ `Entry` ](xref:Xamarin.Forms.Entry), [ `Editor` ](xref:Xamarin.Forms.Editor), e [ `Label` ](xref:Xamarin.Forms.Label) instâncias podem ter a ordem de leitura de seus conteúdos detectadas dinamicamente:

[![InputView detectando a ordem de leitura do conteúdo específico da plataforma](windows-images/editor-readingorder.png "InputView detectando a ordem de leitura do conteúdo específico da plataforma")](windows-images/editor-readingorder-large.png#lightbox "InputView detectando a ordem de leitura do conteúdo específico da plataforma")

> [!NOTE]
> Ao contrário da configuração de [ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) propriedade, a lógica para modos de exibição que detectar que a ordem de leitura do seu conteúdo de texto não afetará o alinhamento do texto dentro da exibição. Em vez disso, ele ajusta a ordem na qual os blocos de texto bidirecional são apresentados.

<a name="listview-selectionmode" />

### <a name="enabling-tap-gesture-support-in-a-listview"></a>Habilitando o suporte de gestos de toque em um ListView

Na plataforma Universal do Windows, por padrão, o xamarin. Forms [ `ListView` ](xref:Xamarin.Forms.ListView) usa nativo `ItemClick` eventos para responder à interação, em vez de nativo `Tapped` eventos. Isso fornece funcionalidades de acessibilidade para que o Narrador do Windows e o teclado podem interagir com o `ListView`. No entanto, ele também processa qualquer gestos de toque dentro a `ListView` inoperável.

Este controles específicos da plataforma se itens em uma [ `ListView` ](xref:Xamarin.Forms.ListView) pode responder para gestos de toque e, portanto, se nativo `ListView` é acionado o `ItemClick` ou `Tapped` eventos. Ele é consumido em XAML, definindo o [ `ListView.SelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty) propriedade anexada a um valor da [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) enumeração:

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

O `ListView.On<Windows>` método Especifica que este específicos da plataforma só serão executado na plataforma Universal do Windows. O [ `ListView.SetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)) método, no [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usada para controlar se itens em um [ `ListView` ](xref:Xamarin.Forms.ListView) pode responder para toque gestos, com o [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) enumeração fornecendo dois valores possíveis:

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible) – indica que o `ListView` acionarão nativo `ItemClick` eventos para manipular a interação e, portanto, fornecer funcionalidades de acessibilidade. Portanto, o Narrador do Windows e o teclado podem interagir com o `ListView`. No entanto, os itens no `ListView` não pode responder para gestos de toque. Esse é o comportamento padrão para `ListView` instâncias na plataforma Universal do Windows.
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible) – indica que o `ListView` acionarão nativo `Tapped` eventos para manipular a interação. Portanto, os itens no `ListView` pode responder para gestos de toque. No entanto, não há nenhuma funcionalidade de acessibilidade e, portanto, o Narrador do Windows e o teclado não é possível interagir com o `ListView`.

> [!NOTE]
> O `Accessible` e `Inaccessible` modos de seleção são mutuamente exclusivos, e você precisará escolher entre uma acessível [ `ListView` ](xref:Xamarin.Forms.ListView) ou um `ListView` que pode responder a gestos de toque.

Além disso, o [ `GetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.GetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView})) método pode ser usado para retornar atual [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode).

O resultado é que o especificado [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) é aplicado ao [ `ListView` ](xref:Xamarin.Forms.ListView), que controla se itens do `ListView` pode responder para gestos de toque e, portanto, se nativo `ListView` é acionado o `ItemClick` ou `Tapped` eventos.

<a name="searchbar-spellcheck" />

### <a name="enabling-searchbar-spell-check"></a>Habilitar verificação ortográfica SearchBar

Este específicos da plataforma permite que um [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) para interagir com o mecanismo de verificação ortográfica. Ele é consumido em XAML, definindo o [ `SearchBar.IsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.IsSpellCheckEnabledProperty) anexado à propriedade um `boolean` valor:

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

O `SearchBar.On<Windows>` método Especifica que este específicos da plataforma só serão executado na plataforma Universal do Windows. O [ `SearchBar.SetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.SetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar},System.Boolean)) método, no [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) e no namespace, desativa o verificador ortográfico. Além disso, o `SearchBar.SetIsSpellCheckEnabled` método pode ser usado para ativar/desativar a verificação ortográfica, chamando o [ `SearchBar.GetIsSpellCheckEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.GetIsSpellCheckEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.SearchBar})) método para retornar se o verificador ortográfico está habilitado:

```csharp
searchBar.On<Windows>().SetIsSpellCheckEnabled(!searchBar.On<Windows>().GetIsSpellCheckEnabled());
```

O resultado é inserido no texto a [ `SearchBar` ](xref:Xamarin.Forms.SearchBar) pode ser ortografia verificada, com erros de ortografia incorretos sendo indicados para o usuário:

![Específicos de plataforma de verificação de ortografia de SearchBar](windows-images/searchbar-spellcheck.png "SearchBar ortográfica seleção específica da plataforma")

> [!NOTE]
> O `SearchBar` classe de [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) também tem um namespace [ `EnableSpellCheck` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.EnableSpellCheck*) e [ `DisableSpellCheck` ](xre:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.SearchBar.DisableSpellCheck*) métodos que podem ser usados para habilitar e desabilitar o verificador ortográfico no `SearchBar`, respectivamente.

<a name="webview-javascript-alert" />

### <a name="displaying-javascript-alerts"></a>Exibição de alertas de JavaScript

Este específicos da plataforma permite que um [ `WebView` ](xref:Xamarin.Forms.WebView) para exibir alertas de JavaScript em uma caixa de diálogo de mensagem UWP. Ele é consumido em XAML, definindo o [ `WebView.IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabledProperty) anexado à propriedade um `boolean` valor:

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

O `WebView.On<Windows>` método Especifica que este específicos da plataforma só serão executado na plataforma Universal do Windows. O [ `WebView.SetIsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.SetIsJavaScriptAlertEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.WebView},System.Boolean)) método, no [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usada para controlar se os alertas de JavaScript estão habilitados. Além disso, o `WebView.SetIsJavaScriptAlertEnabled` método pode ser usado para ativar/desativar alertas de JavaScript por meio da chamada a [ `IsJavaScriptAlertEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabled*) método para retornar se eles estão habilitados:

```csharp
_webView.On<Windows>().SetIsJavaScriptAlertEnabled(!_webView.On<Windows>().IsJavaScriptAlertEnabled());
```

O resultado é que os alertas de JavaScript podem ser exibidas em uma caixa de diálogo de mensagem UWP:

![Alerta do WebView do JavaScript específico da plataforma](windows-images/webview-javascript-alert.png "alerta WebView do JavaScript específico da plataforma")

## <a name="pages"></a>Pages (Páginas)

Na plataforma Universal do Windows, a seguinte funcionalidade específica da plataforma é fornecida para páginas do xamarin. Forms:

- Recolher o [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) barra de navegação. Para obter mais informações, consulte [recolhendo uma barra de navegação MasterDetailPage](#collapsable_navigation_bar).
- Definindo opções de posicionamento da barra de ferramentas. Para obter mais informações, consulte [alterar o posicionamento da barra de ferramentas de página](#toolbar_placement).
- Habilitar ícones de página a ser exibido em uma [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) barra de ferramentas. Para obter mais informações, consulte [habilitando ícones em uma TabbedPage](#tabbedpage-icons).

<a name="collapsable_navigation_bar" />

### <a name="collapsing-a-masterdetailpage-navigation-bar"></a>Uma barra de navegação MasterDetailPage de recolhimento

Este específicos da plataforma é usado para recolher a barra de navegação em um [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)e é consumido em XAML, definindo o [ `MasterDetailPage.CollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapseStyleProperty) e [ `MasterDetailPage.CollapsedPaneWidth` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidthProperty)propriedades anexadas:

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

O `MasterDetailPage.On<Windows>` método Especifica que este específicos da plataforma serão executado apenas no Windows. O [ `Page.SetCollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.SetCollapseStyle(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle)) método, no [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usado para especificar o estilo de recolher, com o [ `CollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle) enumeração fornecendo dois valores: [ `Full` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Full) e [ `Partial` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Partial). O [ `MasterDetailPage.CollapsedPaneWidth` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.MasterDetailPage},System.Double)) método é usado para especificar a largura de uma barra de navegação colapsada parcialmente.

O resultado é que o especificado [ `CollapseStyle` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle) é aplicado para o [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) instância, com a largura também está sendo especificada:

[![](windows-images/collapsed-navigation-bar.png "Recolhido específicos da plataforma barra de navegação")](windows-images/collapsed-navigation-bar-large.png#lightbox "recolhidos específico da plataforma barra de navegação")

<a name="toolbar_placement" />

### <a name="changing-the-page-toolbar-placement"></a>Alterar o posicionamento da barra de ferramentas da página

Este específicos da plataforma é usado para alterar o posicionamento de uma barra de ferramentas em uma [ `Page` ](xref:Xamarin.Forms.Page)e consumido em XAML, definindo o [ `Page.ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.ToolbarPlacementProperty) propriedade anexada a um valor da [ `ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement) enumeração:

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

O `Page.On<Windows>` método Especifica que este específicos da plataforma serão executado apenas no Windows. O [ `Page.SetToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.SetToolbarPlacement(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.Page},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement)) método, no [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usado para definir o posicionamento da barra de ferramentas, com o [ `ToolbarPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement) fornecendo de enumeração três valores: [ `Default` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Default), [ `Top` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Top), e [ `Bottom` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Bottom).

O resultado é que o posicionamento da barra de ferramentas especificada é aplicado para o [ `Page` ](xref:Xamarin.Forms.Page) instância:

[![](windows-images/toolbar-placement.png "Barra de ferramentas posicionamento específico da plataforma")](windows-images/toolbar-placement-large.png#lightbox "específicos de plataforma do posicionamento da barra de ferramentas")

<a name="tabbedpage-icons" />

### <a name="enabling-icons-on-a-tabbedpage"></a>Habilitando os ícones em uma TabbedPage

Este específicos da plataforma permite que os ícones de página a ser exibido em uma [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) barra de ferramentas e fornece a capacidade de, opcionalmente, especificar o tamanho do ícone. Ele é consumido em XAML, definindo o [ `TabbedPage.HeaderIconsEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsEnabledProperty) anexado à propriedade `true`e, opcionalmente, definindo o [ `TabbedPage.HeaderIconsSize` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsSizeProperty) anexado à propriedade um [ `Size` ](xref:Xamarin.Forms.Size) valor:

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:TabbedPage.HeaderIconsEnabled="true">
    <windows:TabbedPage.HeaderIconsSize>
        <Size>
            <x:Arguments>
                <x:Double>24</x:Double>
                <x:Double>24</x:Double>
            </x:Arguments>
        </Size>
    </windows:TabbedPage.HeaderIconsSize>
    <ContentPage Title="Todo" Icon="todo.png">
        ...
    </ContentPage>
    <ContentPage Title="Reminders" Icon="reminders.png">
        ...
    </ContentPage>
    <ContentPage Title="Contacts" Icon="contacts.png">
        ...
    </ContentPage>
</TabbedPage>
```

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

public class WindowsTabbedPageIconsCS : Xamarin.Forms.TabbedPage
{
  public WindowsTabbedPageIconsCS()
    {
    On<Windows>().SetHeaderIconsEnabled(true);
    On<Windows>().SetHeaderIconsSize(new Size(24, 24));

    Children.Add(new ContentPage { Title = "Todo", Icon = "todo.png" });
    Children.Add(new ContentPage { Title = "Reminders", Icon = "reminders.png" });
    Children.Add(new ContentPage { Title = "Contacts", Icon = "contacts.png" });
  }
}
```

O `TabbedPage.On<Windows>` método Especifica que este específicos da plataforma só serão executado na plataforma Universal do Windows. O [ `TabbedPage.SetHeaderIconsEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.SetHeaderIconsEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.TabbedPage},System.Boolean)) método, no [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usado para ativar ou desativar os ícones de cabeçalho. O [ `TabbedPage.SetHeaderIconsSize` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.SetHeaderIconsSize(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.TabbedPage},Xamarin.Forms.Size)) método, opcionalmente, especifica o tamanho do ícone de cabeçalho com um [ `Size` ](xref:Xamarin.Forms.Size) valor.

Além disso, o `TabbedPage` classe o `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` namespace também tem um [ `EnableHeaderIcons` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.EnableHeaderIcons*) método que permite que os ícones de cabeçalho, uma [ `DisableHeaderIcons` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.DisableHeaderIcons*) método que desabilita os ícones de cabeçalho, e uma [ `IsHeaderIconsEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.IsHeaderIconsEnabled*) método que retorna um `boolean` valor que indica se os ícones de cabeçalho estão habilitados.

O resultado é que essa página ícones podem ser exibidos em uma [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage) barra de ferramentas, com o tamanho do ícone, opcionalmente, que está sendo definido como um tamanho desejado:

![Específico da plataforma TabbedPage ícones habilitadas](windows-images/tabbedpage-icons.png "específicos da plataforma TabbedPage ícones habilitados")

## <a name="summary"></a>Resumo

Este artigo demonstrou como consumir as Windows-especificidades da plataforma que são criadas no xamarin. Forms. Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos.

## <a name="related-links"></a>Links relacionados

- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (amostra)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
