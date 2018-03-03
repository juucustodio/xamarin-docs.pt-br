---
title: "Específicos de plataforma do Windows"
description: "Específicos de plataforma permitem consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo demonstra como utilizar os Windows platform específicos que são integrados ao xamarin. Forms."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4729DB9C-8800-4E29-9D66-3BE13C5F8C94
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/11/2017
ms.openlocfilehash: da9279939af8dc4033cd89769a7add60a745ac85
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="windows-platform-specifics"></a>Específicos de plataforma do Windows

_Específicos de plataforma permitem consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo demonstra como utilizar os Windows platform específicos que são integrados ao xamarin. Forms._

Sobre o Windows UWP (plataforma Universal), xamarin. Forms contém as seguintes especificações de plataforma:

- Opções de posicionamento da barra de ferramentas. Para obter mais informações, consulte [alterando o posicionamento da barra de ferramentas](#toolbar_placement).
- Parcialmente retraído [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) barra de navegação. Para obter mais informações, consulte [recolhendo uma barra de navegação MasterDetailPage](#collapsable_navigation_bar).

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

[![](windows-images/toolbar-placement.png "Específico da barra de ferramentas da plataforma posicionamento")](windows-images/toolbar-placement-large.png "específica de plataforma do posicionamento da barra de ferramentas")

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

[![](windows-images/collapsed-navigation-bar.png "Recolhido específica de plataforma da barra de navegação")](windows-images/collapsed-navigation-bar-large.png "recolhido específica de plataforma da barra de navegação")

## <a name="summary"></a>Resumo

Este artigo demonstrou como utilizar os Windows platform específicos que são integrados ao xamarin. Forms. Específicos de plataforma permitem consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos.


## <a name="related-links"></a>Links relacionados

- [Criando específicos de plataforma](~/xamarin-forms/platform/platform-specifics/creating.md)
- [PlatformSpecifics (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [WindowsSpecific](https://developer.xamarin.com/api/namespace/Xamarin.Forms.PlatformConfiguration.WindowsSpecific/)
