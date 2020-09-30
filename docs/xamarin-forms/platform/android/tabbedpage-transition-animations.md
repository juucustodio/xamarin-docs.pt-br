---
title: Animações de transição de página TabbedPage no Android
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Android específica que desabilita animações de transição ao navegar pelas páginas de um TabbedPage.
ms.prod: xamarin
ms.assetid: 2DB4EA6D-9CED-4137-BAB2-B20A457B1CA3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3a7fafcd154dc30df5d33f5728cf8fd8ae592de3
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562438"
---
# <a name="tabbedpage-page-transition-animations-on-android"></a>Animações de transição de página TabbedPage no Android

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do Android específica é usada para desabilitar animações de transição ao navegar pelas páginas, seja de forma programática ou ao usar a barra de guias, em um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Ele é consumido em XAML definindo a `TabbedPage.IsSmoothScrollEnabled` propriedade vinculável como `false` :

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.IsSmoothScrollEnabled="false">
    ...
</TabbedPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetIsSmoothScrollEnabled(false);
```

O `TabbedPage.On<Android>` método especifica que essa plataforma específica será executada somente no Android. O `TabbedPage.SetIsSmoothScrollEnabled` método, no [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usado para controlar se animações de transição serão exibidas ao navegar entre páginas em um [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Além disso, a `TabbedPage` classe no `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` namespace também tem os seguintes métodos:

- `IsSmoothScrollEnabled`, que é usado para recuperar se animações de transição serão exibidas ao navegar entre páginas em um `TabbedPage` .
- `EnableSmoothScroll`, que é usado para habilitar animações de transição ao navegar entre páginas em um `TabbedPage` .
- `DisableSmoothScroll`, que é usado para desabilitar animações de transição ao navegar entre páginas em um `TabbedPage` .

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)