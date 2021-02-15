---
title: Posicionamento da barra de ferramentas da página no Windows
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma específica do Windows que altera o posicionamento de uma barra de ferramentas em uma página.
ms.prod: xamarin
ms.assetid: 99F29E95-0C36-4A3B-BDE8-7E9F119E844E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4caf352024d7ae2a89d49ff021f0f90f35fdd8eb
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372204"
---
# <a name="page-toolbar-placement-on-windows"></a>Posicionamento da barra de ferramentas da página no Windows

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esse Plataforma Universal do Windows específico da plataforma é usado para alterar o posicionamento de uma barra de ferramentas em um [`Page`](xref:Xamarin.Forms.Page) , e é consumido em XAML definindo a [`Page.ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.ToolbarPlacementProperty) Propriedade anexada como um valor da [`ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement) enumeração:

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:Page.ToolbarPlacement="Bottom">
  ...
</TabbedPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetToolbarPlacement(ToolbarPlacement.Bottom);
```

O `Page.On<Windows>` método especifica que essa plataforma específica será executada somente no Windows. O [ `Page.SetToolbarPlacement` ] (xref: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. Page. SetToolbarPlacement ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . Página}, Xamarin.Forms . PlatformConfiguration. WindowsSpecific. ToolbarPlacement)), no [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usado para definir o posicionamento da barra de ferramentas, com a [`ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement) Enumeração fornecendo três valores: [`Default`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Default) , [`Top`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Top) e [`Bottom`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Bottom) .

O resultado é que o posicionamento da barra de ferramentas especificado é aplicado à [`Page`](xref:Xamarin.Forms.Page) instância:

[![Posicionamento da barra de ferramentas específico da plataforma](page-toolbar-placement-images/toolbar-placement.png)](page-toolbar-placement-images/toolbar-placement-large.png#lightbox "Platform-Specific de posicionamento da barra de ferramentas")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)