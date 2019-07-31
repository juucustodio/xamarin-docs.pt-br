---
title: Estilo do cabeçalho do grupo ListView no iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como consumir a plataforma do iOS específica que controla se as células do cabeçalho ListView flutuam durante a rolagem.
ms.prod: xamarin
ms.assetid: 099B2C7F-727F-4BCF-903B-87E728108C24
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f40737799f63c6e0c61fcc6f4f59584222a49d6d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648321"
---
# <a name="listview-group-header-style-on-ios"></a>Estilo do cabeçalho do grupo ListView no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esses controles específicos da plataforma iOS controlam se [`ListView`](xref:Xamarin.Forms.ListView) as células de cabeçalho flutuam durante a rolagem. Ele é consumido em XAML definindo `ListView.GroupHeaderStyle` a propriedade vinculável como um valor `GroupHeaderStyle` da enumeração:

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

Como alternativa, ele pode ser consumido de C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetGroupHeaderStyle(GroupHeaderStyle.Grouped);
```

O `ListView.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `ListView.SetGroupHeaderStyle` método, [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) no namespace, é usado para controlar se [`ListView`](xref:Xamarin.Forms.ListView) as células de cabeçalho flutuam durante a rolagem. A `GroupHeaderStyle` enumeração fornece dois valores possíveis:

- `Plain`– indica que as células de cabeçalho flutuam quando o [`ListView`](xref:Xamarin.Forms.ListView) é rolado (padrão).
- `Grouped`– indica que as células de cabeçalho não flutuam [`ListView`](xref:Xamarin.Forms.ListView) quando a é rolada.

Além disso, o `ListView.GetGroupHeaderStyle` método pode ser usado para retornar o `GroupHeaderStyle` [`ListView`](xref:Xamarin.Forms.ListView)que é aplicado ao.

O resultado é que um valor `GroupHeaderStyle` especificado é aplicado [`ListView`](xref:Xamarin.Forms.ListView)ao, que controla se as células de cabeçalho flutuam durante a rolagem:

[ ![Captura de tela de células de cabeçalho ListView flutuantes e não flutuantes, em ListView do IOS](listview-group-header-style-images/group-header-styles.png "com células de cabeçalho flutuantes e não flutuantes") ] (listview-group-header-style-images/group-header-styles-large.png#lightbox "ListView com células de cabeçalho flutuantes e não flutuantes")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
