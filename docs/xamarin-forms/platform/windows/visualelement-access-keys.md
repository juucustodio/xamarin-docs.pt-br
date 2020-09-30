---
title: Chaves de acesso do visualelement no Windows
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma específica do Windows que especifica uma chave de acesso para um Visualelement.
ms.prod: xamarin
ms.assetid: 771AF785-76B8-4372-89F5-E4D521D21E0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6e25654fb856935c119d731df5db3eaa2d501930
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560241"
---
# <a name="visualelement-access-keys-on-windows"></a>Chaves de acesso do visualelement no Windows

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Chaves de acesso são atalhos de teclado que melhoram a usabilidade e a acessibilidade de aplicativos no Plataforma Universal do Windows (UWP), fornecendo uma maneira intuitiva para os usuários navegarem e interagirem rapidamente com a interface do usuário visível do aplicativo por meio de um teclado em vez de por toque ou mouse. Elas são combinações da tecla Alt e uma ou mais chaves alfanuméricas, geralmente pressionadas em sequência. Os atalhos de teclado têm suporte automaticamente para chaves de acesso que usam um único caractere alfanumérico.

Dicas de tecla de acesso são notificações flutuantes exibidas ao lado de controles que incluem chaves de acesso. Cada dica de tecla de acesso contém as chaves alfanuméricas que ativam o controle associado. Quando um usuário pressiona a tecla Alt, as dicas de tecla de acesso são exibidas.

Essa plataforma UWP específica é usada para especificar uma chave de acesso para um [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Ele é consumido em XAML definindo a [`VisualElement.AccessKey`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyProperty) Propriedade anexada como um valor alfanumérico e, opcionalmente, definindo a [`VisualElement.AccessKeyPlacement`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyPlacementProperty) Propriedade anexada como um valor da [`AccessKeyPlacement`](xref:Xamarin.Forms.AccessKeyPlacement) enumeração, a [`VisualElement.AccessKeyHorizontalOffset`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyHorizontalOffsetProperty) Propriedade anexada como a `double` e a [`VisualElement.AccessKeyVerticalOffset`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyVerticalOffsetProperty) Propriedade anexada a `double` :

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

Como alternativa, ele pode ser consumido em C# usando a API fluente:

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

O `VisualElement.On<Windows>` método especifica que essa plataforma específica será executada somente no plataforma universal do Windows. O [ `VisualElement.SetAccessKey` ] (xref: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. Visualelement. SetAccessKey ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . O método visualelement}, System. String), no [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usado para definir o valor da chave de acesso para o `VisualElement` . O [ `VisualElement.SetAccessKeyPlacement` ] (xref: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. Visualelement. SetAccessKeyPlacement ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . Visualelement}, Xamarin.Forms . AccessKeyPlacement)), opcionalmente especifica a posição a ser usada para exibir a dica de tecla de acesso, com a [`AccessKeyPlacement`](xref:Xamarin.Forms.AccessKeyPlacement) enumeração que fornece os seguintes valores possíveis:

- [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto) – indica que o posicionamento da dica de tecla de acesso será determinado pelo sistema operacional.
- [`Top`](xref:Xamarin.Forms.AccessKeyPlacement.Top) – indica que a dica de tecla de acesso aparecerá acima da borda superior do `VisualElement` .
- [`Bottom`](xref:Xamarin.Forms.AccessKeyPlacement.Bottom) – indica que a dica de tecla de acesso aparecerá abaixo da borda inferior do `VisualElement` .
- [`Right`](xref:Xamarin.Forms.AccessKeyPlacement.Right) – indica que a dica de tecla de acesso aparecerá à direita da borda direita do `VisualElement` .
- [`Left`](xref:Xamarin.Forms.AccessKeyPlacement.Left) – indica que a dica de tecla de acesso será exibida à esquerda da borda esquerda do `VisualElement` .
- [`Center`](xref:Xamarin.Forms.AccessKeyPlacement.Center) – indica que a dica de tecla de acesso aparecerá sobreposta no centro do `VisualElement` .

> [!NOTE]
> Normalmente, o [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto) posicionamento da dica de tecla é suficiente, o que inclui suporte para interfaces de usuário adaptáveis.

O [ `VisualElement.SetAccessKeyHorizontalOffset` ] (xref: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. Visualelement. SetAccessKeyHorizontalOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . Visualelement}, sistema. Double)) e [ `VisualElement.SetAccessKeyVerticalOffset` ] (xref: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. Visualelement. SetAccessKeyVerticalOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . Elementos visualelement}, sistema. Double)) podem ser usados para um controle mais granular do local da dica de chave de acesso. O argumento para o `SetAccessKeyHorizontalOffset` método indica a distância de mover a dica de tecla de acesso para a esquerda ou direita, e o argumento para o `SetAccessKeyVerticalOffset` método indica até que distância mover a dica de tecla de acesso para cima ou para baixo.

>[!NOTE]
> Deslocamentos de dica de tecla de acesso não podem ser definidos quando o posicionamento da chave de acesso está definido `Auto` .

Além disso, o [ `GetAccessKey` ] (xref: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. Visualelement. GetAccessKey ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . Visualelement})), [ `GetAccessKeyPlacement` ] (xref: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. Visualelement. GetAccessKeyPlacement ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . Visualelement})), [ `GetAccessKeyHorizontalOffset` ] (xref: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. Visualelement. GetAccessKeyHorizontalOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . Visualelement})), e [ `GetAccessKeyVerticalOffset` ] (xref: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. Visualelement. GetAccessKeyVerticalOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . Visualelement})), os métodos podem ser usados para recuperar um valor de chave de acesso e sua localização.

O resultado é que as dicas de tecla de acesso podem ser exibidas ao lado de quaisquer [`VisualElement`](xref:Xamarin.Forms.VisualElement) instâncias que definem chaves de acesso, pressionando a tecla Alt:

![Chaves de acesso do visualelement-específicas da plataforma](visualelement-access-keys-images/visualelement-accesskeys.png "Chaves de acesso do visualelement-específicas da plataforma")

Quando um usuário ativa uma tecla de acesso, pressionando a tecla Alt seguida da tecla de acesso, a ação padrão para o `VisualElement` será executada. Por exemplo, quando um usuário ativa a tecla de acesso em um [`Switch`](xref:Xamarin.Forms.Switch) , o `Switch` é alternado. Quando um usuário ativa a tecla de acesso em um [`Entry`](xref:Xamarin.Forms.Entry) , o `Entry` foco ganha. Quando um usuário ativa a chave de acesso em um [`Button`](xref:Xamarin.Forms.Button) , o manipulador de eventos para o [`Clicked`](xref:Xamarin.Forms.Button.Clicked) evento é executado.

Para obter mais informações sobre chaves de acesso, consulte [chaves de acesso](/windows/uwp/design/input/access-keys#key-tip-positioning).

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)