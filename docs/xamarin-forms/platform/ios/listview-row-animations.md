---
title: Animações de linha ListView no iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como consumir a plataforma específica do iOS que controla se as animações de linha são desabilitadas quando a coleção de itens ListView está sendo atualizada.
ms.prod: xamarin
ms.assetid: E8F5103F-4D8E-4A5A-A16C-7FA14EE786AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2019
ms.openlocfilehash: 9e44c22e670847102cf0bc0f79a860abcac30760
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652829"
---
# <a name="listview-row-animations-on-ios"></a>Animações de linha ListView no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esses controles específicos da plataforma iOS controlam se as animações de linha [`ListView`](xref:Xamarin.Forms.ListView) são desabilitadas quando a coleção de itens está sendo atualizada. Ele é consumido em XAML, definindo o `ListView.RowAnimationsEnabled` para a propriedade associável `false`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.RowAnimationsEnabled="false">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetRowAnimationsEnabled(false);
```

O `ListView.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `ListView.SetRowAnimationsEnabled` método, [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) no namespace, é usado para controlar se as animações de linha são desabilitadas [`ListView`](xref:Xamarin.Forms.ListView) quando a coleção de itens está sendo atualizada. Além disso, o `ListView.GetRowAnimationsEnabled` método pode ser usado para retornar se as animações de linha estão desabilitadas `ListView`no.

> [!NOTE]
> [`ListView`](xref:Xamarin.Forms.ListView)as animações de linha são habilitadas por padrão. Portanto, uma animação ocorre quando uma nova linha é inserida em `ListView`um.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
