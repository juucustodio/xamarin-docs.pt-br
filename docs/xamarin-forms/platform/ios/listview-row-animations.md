---
title: Animações de linha ListView no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma específica do iOS que controla se as animações de linha são desabilitadas quando a coleção de itens ListView está sendo atualizada.
ms.prod: xamarin
ms.assetid: E8F5103F-4D8E-4A5A-A16C-7FA14EE786AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 53930f6b6b83ea855db9538f19e88482a4d1a82f
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557446"
---
# <a name="listview-row-animations-on-ios"></a>Animações de linha ListView no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esses controles específicos da plataforma iOS controlam se as animações de linha são desabilitadas quando a [`ListView`](xref:Xamarin.Forms.ListView) coleção de itens está sendo atualizada. Ele é consumido em XAML definindo a `ListView.RowAnimationsEnabled` propriedade vinculável como `false` :

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

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetRowAnimationsEnabled(false);
```

O `ListView.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `ListView.SetRowAnimationsEnabled` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para controlar se as animações de linha são desabilitadas quando a [`ListView`](xref:Xamarin.Forms.ListView) coleção de itens está sendo atualizada. Além disso, o `ListView.GetRowAnimationsEnabled` método pode ser usado para retornar se as animações de linha estão desabilitadas no `ListView` .

> [!NOTE]
> [`ListView`](xref:Xamarin.Forms.ListView) as animações de linha são habilitadas por padrão. Portanto, uma animação ocorre quando uma nova linha é inserida em um `ListView` .

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)