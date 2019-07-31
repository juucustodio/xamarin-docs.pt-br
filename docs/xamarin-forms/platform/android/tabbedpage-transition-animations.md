---
title: Animações de transição de página TabbedPage no Android
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como consumir a plataforma Android específica que desabilita animações de transição ao navegar pelas páginas de um TabbedPage.
ms.prod: xamarin
ms.assetid: 2DB4EA6D-9CED-4137-BAB2-B20A457B1CA3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 4f8d6ec2b06855364970bc9b672c3d3f7b9bfdfc
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649839"
---
# <a name="tabbedpage-page-transition-animations-on-android"></a>Animações de transição de página TabbedPage no Android

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do Android específica é usada para desabilitar animações de transição ao navegar pelas páginas, seja de forma programática ou ao usar a barra de [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)guias, em um. Ele é consumido em XAML, definindo o `TabbedPage.IsSmoothScrollEnabled` para a propriedade associável `false`:

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.IsSmoothScrollEnabled="false">
    ...
</TabbedPage>
```

Como alternativa, ele pode ser consumido de C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetIsSmoothScrollEnabled(false);
```

O `TabbedPage.On<Android>` método Especifica que este específicos da plataforma serão executado apenas no Android. O `TabbedPage.SetIsSmoothScrollEnabled` método, no [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usada para controlar se as animações de transição serão exibidas quando navegar entre páginas em um [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). Além disso, o `TabbedPage` classe o `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` namespace também tem os seguintes métodos:

- `IsSmoothScrollEnabled`, que é usada para recuperar se animações de transição serão exibidas quando navegar entre páginas em um `TabbedPage`.
- `EnableSmoothScroll`, que é usado para habilitar animações de transição ao navegar entre páginas em um `TabbedPage`.
- `DisableSmoothScroll`, que é usado para desabilitar animações de transição ao navegar entre páginas em um `TabbedPage`.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
