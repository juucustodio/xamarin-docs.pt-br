---
title: Rolagem rápida de ListView no Android
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Android específica que permite a rolagem rápida por meio de dados em um ListView.
ms.prod: xamarin
ms.assetid: 37D95A2D-74AC-488A-B903-2BDD799EAA5C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 80b8dc663457316eef831c1e60894cc6b95e281e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91564037"
---
# <a name="listview-fast-scrolling-on-android"></a>Rolagem rápida de ListView no Android

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Essa plataforma do Android específica é usada para habilitar a rolagem rápida por meio de dados em um [`ListView`](xref:Xamarin.Forms.ListView) . Ele é consumido em XAML definindo a `ListView.IsFastScrollEnabled` Propriedade anexada como um `boolean` valor:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        ...
        <ListView ItemsSource="{Binding GroupedEmployees}"
                  GroupDisplayBinding="{Binding Key}"
                  IsGroupingEnabled="true"
                  android:ListView.IsFastScrollEnabled="true">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var listView = new Xamarin.Forms.ListView { IsGroupingEnabled = true, ... };
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "GroupedEmployees");
listView.GroupDisplayBinding = new Binding("Key");
listView.On<Android>().SetIsFastScrollEnabled(true);
```

O `ListView.On<Android>` método especifica que essa plataforma específica será executada somente no Android. O `ListView.SetIsFastScrollEnabled` método, no [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) namespace, é usado para habilitar a rolagem rápida por meio de dados em um [`ListView`](xref:Xamarin.Forms.ListView) . Além disso, o `SetIsFastScrollEnabled` método pode ser usado para alternar a rolagem rápida chamando o `IsFastScrollEnabled` método para retornar se a rolagem rápida está habilitada:

```csharp
listView.On<Android>().SetIsFastScrollEnabled(!listView.On<Android>().IsFastScrollEnabled());
```

O resultado é que a rolagem rápida por meio de dados em um [`ListView`](xref:Xamarin.Forms.ListView) pode ser habilitada, o que altera o tamanho do polegar de rolagem:

[![ListView FastScroll específico da plataforma](listview-fast-scrolling-images/fastscroll.png)](listview-fast-scrolling-images/fastscroll-large.png#lightbox "ListView FastScroll específico da plataforma")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)