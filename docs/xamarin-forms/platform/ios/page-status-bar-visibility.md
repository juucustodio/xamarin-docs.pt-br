---
title: Página de visibilidade da barra de Status no iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como utilizar a plataforma específica do iOS que define a visibilidade da barra de status em uma página.
ms.prod: xamarin
ms.assetid: D8BB7C24-A27F-4758-8557-6A81F909ABD9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 98eba6dea1fb528aa15a1fb242b0fb0eb7dada56
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61258575"
---
# <a name="page-status-bar-visibility-on-ios"></a>Página de visibilidade da barra de Status no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Este específicos da plataforma iOS é usado para definir a visibilidade da barra de status em uma [ `Page` ](xref:Xamarin.Forms.Page), e inclui a capacidade de controlar como a barra de status entra ou sai de `Page`. Ele é consumido em XAML, definindo o `Page.PrefersStatusBarHidden` propriedade anexada a um valor da `StatusBarHiddenMode` enumeração e, opcionalmente, o `Page.PreferredStatusBarUpdateAnimation` propriedade anexada a um valor da `UIStatusBarAnimation` enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

Como alternativa, ele pode ser consumido de C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

O `Page.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `Page.SetPrefersStatusBarHidden` método, no `Xamarin.Forms.PlatformConfiguration.iOSSpecific` namespace, é usado para definir a visibilidade da barra de status em um [ `Page` ](xref:Xamarin.Forms.Page) especificando um dos `StatusBarHiddenMode` valores de enumeração: `Default`, `True` , ou `False`. O `StatusBarHiddenMode.True` e `StatusBarHiddenMode.False` valores definidos a visibilidade da barra de status, independentemente da orientação do dispositivo e o `StatusBarHiddenMode.Default` valor oculta a barra de status em um ambiente compact verticalmente.

O resultado é que a visibilidade da barra de status em uma [ `Page` ](xref:Xamarin.Forms.Page) podem ser definidos:

![](page-status-bar-visibility-images/hide-status-bar.png "Específicos de plataforma de visibilidade de barra de status")

> [!NOTE]
> Em um [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), especificado `StatusBarHiddenMode` valor de enumeração também atualizará a barra de status em todas as páginas filho. Em todos os outros [ `Page` ](xref:Xamarin.Forms.Page)-tipos derivados, especificados `StatusBarHiddenMode` valor de enumeração só atualizará a barra de status na página atual.

O `Page.SetPreferredStatusBarUpdateAnimation` método é usado para definir como a barra de status entra ou sai do [ `Page` ](xref:Xamarin.Forms.Page) especificando um da `UIStatusBarAnimation` valores de enumeração: `None`, `Fade`, ou `Slide`. Se o `Fade` ou `Slide` o valor de enumeração for especificado, uma segunda 0,25 animação é executada como a barra de status entra ou sai do `Page`.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
