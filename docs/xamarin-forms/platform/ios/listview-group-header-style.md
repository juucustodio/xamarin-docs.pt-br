---
title: Estilo do cabeçalho do grupo ListView no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma do iOS específica que controla se as células do cabeçalho ListView flutuam durante a rolagem.
ms.prod: xamarin
ms.assetid: 099B2C7F-727F-4BCF-903B-87E728108C24
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f40737799f63c6e0c61fcc6f4f59584222a49d6d
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "68648321"
---
# <a name="listview-group-header-style-on-ios"></a>Estilo do cabeçalho do grupo ListView no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esses controles específicos da plataforma iOS controlam se [`ListView`](xref:Xamarin.Forms.ListView) células de cabeçalho flutuam durante a rolagem. Ele é consumido em XAML definindo a propriedade `ListView.GroupHeaderStyle` vinculável como um valor da enumeração `GroupHeaderStyle`:

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

Como alternativa, ele pode ser consumido C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetGroupHeaderStyle(GroupHeaderStyle.Grouped);
```

O método `ListView.On<iOS>` especifica que essa plataforma específica será executada somente no iOS. O método `ListView.SetGroupHeaderStyle`, no namespace [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , é usado para controlar se [`ListView`](xref:Xamarin.Forms.ListView) células de cabeçalho flutuam durante a rolagem. A enumeração `GroupHeaderStyle` fornece dois valores possíveis:

- `Plain` – indica que as células de cabeçalho flutuam quando a [`ListView`](xref:Xamarin.Forms.ListView) é rolada (padrão).
- `Grouped` – indica que as células de cabeçalho não flutuam quando a [`ListView`](xref:Xamarin.Forms.ListView) é rolada.

Além disso, o método `ListView.GetGroupHeaderStyle` pode ser usado para retornar o `GroupHeaderStyle` que é aplicado ao [`ListView`](xref:Xamarin.Forms.ListView).

O resultado é que um valor de `GroupHeaderStyle` especificado é aplicado ao [`ListView`](xref:Xamarin.Forms.ListView), que controla se as células de cabeçalho flutuam durante a rolagem:

[![Captura de tela de células de cabeçalho ListView flutuantes e não flutuantes, no iOS](listview-group-header-style-images/group-header-styles.png "ListView com células de cabeçalho flutuantes e não flutuantes")](listview-group-header-style-images/group-header-styles-large.png#lightbox "ListView com células de cabeçalho flutuantes e não flutuantes")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
