---
title: Estilo do cabeçalho do grupo ListView no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma do iOS específica que controla se as células do cabeçalho ListView flutuam durante a rolagem.
ms.prod: xamarin
ms.assetid: 099B2C7F-727F-4BCF-903B-87E728108C24
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 46e8dec3d5644defdeb8a2265a73815adfde92d8
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136026"
---
# <a name="listview-group-header-style-on-ios"></a>Estilo do cabeçalho do grupo ListView no iOS

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esses controles específicos da plataforma iOS controlam se [`ListView`](xref:Xamarin.Forms.ListView) as células de cabeçalho flutuam durante a rolagem. Ele é consumido em XAML definindo a `ListView.GroupHeaderStyle` propriedade vinculável como um valor da `GroupHeaderStyle` enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.GroupHeaderStyle="Grouped">
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

listView.On<iOS>().SetGroupHeaderStyle(GroupHeaderStyle.Grouped);
```

O `ListView.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `ListView.SetGroupHeaderStyle` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para controlar se [`ListView`](xref:Xamarin.Forms.ListView) as células de cabeçalho flutuam durante a rolagem. A `GroupHeaderStyle` enumeração fornece dois valores possíveis:

- `Plain`– indica que as células de cabeçalho flutuam quando o [`ListView`](xref:Xamarin.Forms.ListView) é rolado (padrão).
- `Grouped`– indica que as células de cabeçalho não flutuam quando a [`ListView`](xref:Xamarin.Forms.ListView) é rolada.

Além disso, o `ListView.GetGroupHeaderStyle` método pode ser usado para retornar o `GroupHeaderStyle` que é aplicado ao [`ListView`](xref:Xamarin.Forms.ListView) .

O resultado é que um `GroupHeaderStyle` valor especificado é aplicado ao [`ListView`](xref:Xamarin.Forms.ListView) , que controla se as células de cabeçalho flutuam durante a rolagem:

[![Captura de tela de células de cabeçalho ListView flutuantes e não flutuantes, no iOS](listview-group-header-style-images/group-header-styles.png "ListView com células de cabeçalho flutuantes e não flutuantes")](listview-group-header-style-images/group-header-styles-large.png#lightbox "ListView com células de cabeçalho flutuantes e não flutuantes")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
