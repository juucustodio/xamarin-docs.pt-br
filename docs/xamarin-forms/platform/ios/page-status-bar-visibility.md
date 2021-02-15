---
title: Visibilidade da barra de status da página no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma específica do iOS que define a visibilidade da barra de status em uma página.
ms.prod: xamarin
ms.assetid: D8BB7C24-A27F-4758-8557-6A81F909ABD9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f653f2a617022ddd80e910fcea7c43450cb911d0
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366718"
---
# <a name="page-status-bar-visibility-on-ios"></a>Visibilidade da barra de status da página no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma específica do iOS é usada para definir a visibilidade da barra de status em um [`Page`](xref:Xamarin.Forms.Page) e inclui a capacidade de controlar como a barra de status entra ou sai do `Page` . Ele é consumido em XAML definindo- `Page.PrefersStatusBarHidden` se a propriedade anexada como um valor da `StatusBarHiddenMode` enumeração e, opcionalmente, a `Page.PreferredStatusBarUpdateAnimation` Propriedade anexada a um valor da `UIStatusBarAnimation` enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

O `Page.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `Page.SetPrefersStatusBarHidden` método, no `Xamarin.Forms.PlatformConfiguration.iOSSpecific` namespace, é usado para definir a visibilidade da barra de status em um [`Page`](xref:Xamarin.Forms.Page) especificando um dos `StatusBarHiddenMode` valores de enumeração: `Default` , `True` ou `False` . Os `StatusBarHiddenMode.True` `StatusBarHiddenMode.False` valores e definem a visibilidade da barra de status, independentemente da orientação do dispositivo, e o `StatusBarHiddenMode.Default` valor oculta a barra de status em um ambiente verticalmente compacto.

O resultado é que a visibilidade da barra de status em um [`Page`](xref:Xamarin.Forms.Page) pode ser definida:

![Platform-Specific de visibilidade da barra de status](page-status-bar-visibility-images/hide-status-bar.png)

> [!NOTE]
> Em um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) , o `StatusBarHiddenMode` valor de enumeração especificado também atualizará a barra de status em todas as páginas filho. Em todos os outros [`Page`](xref:Xamarin.Forms.Page) tipos derivados, o `StatusBarHiddenMode` valor de enumeração especificado atualizará apenas a barra de status na página atual.

O `Page.SetPreferredStatusBarUpdateAnimation` método é usado para definir como a barra de status entra ou sai do [`Page`](xref:Xamarin.Forms.Page) especificando um dos `UIStatusBarAnimation` valores de enumeração: `None` , `Fade` ou `Slide` . Se o `Fade` `Slide` valor de enumeração ou for especificado, uma animação de 0,25 segundos será executada à medida que a barra de status entrar ou sair do `Page` .

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)