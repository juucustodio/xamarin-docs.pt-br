---
title: Ícones de TabbedPage no Windows
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Windows específica que permite que os ícones de página sejam exibidos em uma barra de ferramentas do TabbedPage.
ms.prod: xamarin
ms.assetid: 7C5031A5-74EE-4469-994E-BEA7BA9D33CB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 60b331b01f7ba16bd2ea828d429ee64ae25766ea
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91560267"
---
# <a name="tabbedpage-icons-on-windows"></a>Ícones de TabbedPage no Windows

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa Plataforma Universal do Windows específica à plataforma permite que os ícones de página sejam exibidos em uma [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) barra de ferramentas e fornece a capacidade de especificar opcionalmente o tamanho do ícone. Ele é consumido em XAML, definindo a [`TabbedPage.HeaderIconsEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsEnabledProperty) Propriedade anexada como `true` e, opcionalmente, definindo a [`TabbedPage.HeaderIconsSize`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.HeaderIconsSizeProperty) Propriedade anexada como um [`Size`](xref:Xamarin.Forms.Size) valor:

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
    <ContentPage Title="Todo" IconImageSource="todo.png">
        ...
    </ContentPage>
    <ContentPage Title="Reminders" IconImageSource="reminders.png">
        ...
    </ContentPage>
    <ContentPage Title="Contacts" IconImageSource="contacts.png">
        ...
    </ContentPage>
</TabbedPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

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

    Children.Add(new ContentPage { Title = "Todo", IconImageSource = "todo.png" });
    Children.Add(new ContentPage { Title = "Reminders", IconImageSource = "reminders.png" });
    Children.Add(new ContentPage { Title = "Contacts", IconImageSource = "contacts.png" });
  }
}
```

O `TabbedPage.On<Windows>` método especifica que essa plataforma específica será executada somente no plataforma universal do Windows. O [ `TabbedPage.SetHeaderIconsEnabled` ] (xref: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. TabbedPage. SetHeaderIconsEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . TabbedPage}, System. Boolean)), no [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usado para ativar ou desativar ícones de cabeçalho. O [ `TabbedPage.SetHeaderIconsSize` ] (xref: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. TabbedPage. SetHeaderIconsSize ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . TabbedPage}, Xamarin.Forms . Size)). opcionalmente, o método especifica o tamanho do ícone de cabeçalho com um [`Size`](xref:Xamarin.Forms.Size) valor.

Além disso, a `TabbedPage` classe no `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` namespace também tem um [`EnableHeaderIcons`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.EnableHeaderIcons*) método que habilita ícones de cabeçalho, um [`DisableHeaderIcons`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.DisableHeaderIcons*) método que desabilita ícones de cabeçalho e um [`IsHeaderIconsEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.TabbedPage.IsHeaderIconsEnabled*) método que retorna um `boolean` valor que indica se os ícones de cabeçalho estão habilitados.

O resultado é que os ícones de página podem ser exibidos em uma [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) barra de ferramentas, com o tamanho do ícone definido opcionalmente para um tamanho desejado:

![Ícones de TabbedPage habilitados para plataforma específica](tabbedpage-icons-images/tabbedpage-icons.png "Ícones de TabbedPage habilitados para plataforma específica")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)