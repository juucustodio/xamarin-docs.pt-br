---
title: Barra de navegação do FlyoutPage no Windows
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Windows específica que recolhe a barra de navegação em um FlyoutPage.
ms.prod: xamarin
ms.assetid: 0E7436C9-FA3E-40CD-801C-3F7ED95C412D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e2f6a4c3eaf2668eff7e2123378175f7844a3413
ms.sourcegitcommit: 1decf2c65dc4c36513f7dd459a5df01e170a036f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98115139"
---
# <a name="flyoutpage-navigation-bar-on-windows"></a>Barra de navegação do FlyoutPage no Windows

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esse Plataforma Universal do Windows específico à plataforma é usado para recolher a barra de navegação em um [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) , e é consumido em XAML definindo as [`FlyoutPage.CollapseStyle`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.FlyoutPage.CollapseStyleProperty) [`FlyoutPage.CollapsedPaneWidth`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.FlyoutPage.CollapsedPaneWidthProperty) Propriedades anexadas e:

```xaml
<FlyoutPage ...
                  xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
                  windows:FlyoutPage.CollapseStyle="Partial"
                  windows:FlyoutPage.CollapsedPaneWidth="48">
  ...
</FlyoutPage>

```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetCollapseStyle(CollapseStyle.Partial).CollapsedPaneWidth(148);
```

O `FlyoutPage.On<Windows>` método especifica que essa plataforma específica será executada somente no Windows. O [ `Page.SetCollapseStyle` ] (xref: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. FlyoutPage. setcolapsostyle ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . FlyoutPage}, Xamarin.Forms . O método PlatformConfiguration. WindowsSpecific. Collapsestyle)), no [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usado para especificar o estilo de recolhimento, com a [`CollapseStyle`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle) Enumeração fornecendo dois valores: [`Full`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Full) e [`Partial`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Partial) . O [ `FlyoutPage.CollapsedPaneWidth` ] (xref: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. FlyoutPage. CollapsedPaneWidth ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . FlyoutPage}, System. Double)) é usado para especificar a largura de uma barra de navegação parcialmente recolhida.

O resultado é que um especificado [`CollapseStyle`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle) é aplicado à [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) instância, com a largura também sendo especificada:

[![Barra de navegação recolhida específica da plataforma](flyoutpage-navigation-bar-images/collapsed-navigation-bar.png)](flyoutpage-navigation-bar-images/collapsed-navigation-bar-large.png#lightbox "Barra de navegação recolhida Platform-Specific")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)