---
title: Animações de linha de ListView no iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como utilizar o iOS específicos da plataforma que controla se as animações de linha estão desabilitadas quando a coleção de itens de ListView está sendo atualizada.
ms.prod: xamarin
ms.assetid: E8F5103F-4D8E-4A5A-A16C-7FA14EE786AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2019
ms.openlocfilehash: 50480f5b21c6f0c855ff6f9aa22b6126c6a6787c
ms.sourcegitcommit: 00744f754527e5b55154365f89691caaf1c9d929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57557438"
---
# <a name="listview-row-animations-on-ios"></a>Animações de linha de ListView no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Essa controla de específico da plataforma iOS de linha se as animações são desabilitados quando o [ `ListView` ](xref:Xamarin.Forms.ListView) coleção de itens está sendo atualizada. Ele é consumido em XAML, definindo o `ListView.RowAnimationsEnabled` para a propriedade associável `false`:

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

Como alternativa, ele pode ser consumido de c# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetRowAnimationsEnabled(false);
```

O `ListView.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `ListView.SetRowAnimationsEnabled` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usada para controlar se as animações de linha são desabilitado quando o [ `ListView` ](xref:Xamarin.Forms.ListView) coleção de itens está sendo atualizada. Além disso, o `ListView.GetRowAnimationsEnabled` método pode ser usado para retornar se animações de linha estão desabilitadas no `ListView`.

> [!NOTE]
> [`ListView`](xref:Xamarin.Forms.ListView) animações de linha são habilitadas por padrão. Portanto, uma animação ocorre quando uma nova linha é inserida em um `ListView`.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
