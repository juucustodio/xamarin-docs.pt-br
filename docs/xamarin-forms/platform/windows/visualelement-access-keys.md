---
title: Chaves de acesso de VisualElement no Windows
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como utilizar o Windows específicos da plataforma que especifica uma chave de acesso para um VisualElement.
ms.prod: xamarin
ms.assetid: 771AF785-76B8-4372-89F5-E4D521D21E0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f14014b257ee5061b6dd074719c3ca27577c6013
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209331"
---
# <a name="visualelement-access-keys-on-windows"></a>Chaves de acesso de VisualElement no Windows

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Chaves de acesso são atalhos de teclado que melhoram a usabilidade e a acessibilidade de aplicativos na plataforma Universal de Windows (UWP), fornecendo uma maneira intuitiva para os usuários a navegar rapidamente e interagir com a interface do usuário visível do aplicativo por meio de um teclado em vez de por meio de toque ou um mouse. Eles são combinações da tecla Alt e uma ou mais chaves alfanuméricas, normalmente pressionadas em sequência. Atalhos de teclado automaticamente têm suporte para chaves de acesso que usam um único caractere alfanumérico.

Dicas de tecla de acesso são flutuantes selos exibidos ao lado de controles que incluem as chaves de acesso. Cada dica de tecla de acesso contém as chaves alfanuméricas que ativam o controle associado. Quando um usuário pressiona a tecla Alt, as dicas de tecla de acesso são exibidas.

Essa plataforma específica de UWP é usada para especificar uma chave de acesso para um [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Ele é consumido em XAML, definindo o [ `VisualElement.AccessKey` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyProperty) propriedade anexada para um valor alfanumérico e, opcionalmente, definindo o [ `VisualElement.AccessKeyPlacement` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyPlacementProperty) propriedade anexada com o valor de [ `AccessKeyPlacement` ](xref:Xamarin.Forms.AccessKeyPlacement) enumeração, o [ `VisualElement.AccessKeyHorizontalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyHorizontalOffsetProperty) anexado à propriedade um `double`e a [ `VisualElement.AccessKeyVerticalOffset` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyVerticalOffsetProperty) propriedade anexada a um `double`:

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

![Específico da plataforma de teclas de acesso VisualElement](visualelement-access-keys-images/visualelement-accesskeys.png "VisualElement acesso chaves específicas da plataforma")

Quando um usuário ativa uma chave de acesso, pressionando a tecla Alt, seguida pelo acesso de chave, a ação padrão para o `VisualElement` será executado. Por exemplo, quando um usuário ativa a chave de acesso em uma [ `Switch` ](xref:Xamarin.Forms.Switch), o `Switch` é alternada. Quando um usuário ativa a chave de acesso em uma [ `Entry` ](xref:Xamarin.Forms.Entry), o `Entry` ganha o foco. Quando um usuário ativa a chave de acesso em uma [ `Button` ](xref:Xamarin.Forms.Button), o manipulador de eventos para o [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) eventos é executado.

Para obter mais informações sobre chaves de acesso, consulte [chaves de acesso](/windows/uwp/design/input/access-keys#key-tip-positioning).

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
