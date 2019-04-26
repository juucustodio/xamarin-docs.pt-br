---
title: Estilo do cabeçalho de grupo de ListView no iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como utilizar o iOS específicos da plataforma que controla se as células de cabeçalho ListView float durante a rolagem.
ms.prod: xamarin
ms.assetid: 099B2C7F-727F-4BCF-903B-87E728108C24
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 58b3787b71cff9b78f1c6b577be6c320367f1cee
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60952137"
---
# <a name="listview-group-header-style-on-ios"></a>Estilo do cabeçalho de grupo de ListView no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Essa controla de específico da plataforma iOS se [ `ListView` ](xref:Xamarin.Forms.ListView) células de cabeçalho float durante a rolagem. Ele é consumido em XAML, definindo o `ListView.GroupHeaderStyle` propriedade associável a um valor da `GroupHeaderStyle` enumeração:

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

O `ListView.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O `ListView.SetGroupHeaderStyle` método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usada para controlar se [ `ListView` ](xref:Xamarin.Forms.ListView) células de cabeçalho float durante a rolagem. O `GroupHeaderStyle` enumeração fornece dois valores possíveis:

- `Plain` – indica que as células de cabeçalho float quando o [ `ListView` ](xref:Xamarin.Forms.ListView) é rolado (padrão).
- `Grouped` – indica que as células de cabeçalho não flutuante quando o [ `ListView` ](xref:Xamarin.Forms.ListView) é rolada.

Além disso, o `ListView.GetGroupHeaderStyle` método pode ser usado para retornar o `GroupHeaderStyle` que é aplicado para o [ `ListView` ](xref:Xamarin.Forms.ListView).

O resultado é que a especificada `GroupHeaderStyle` valor é aplicado para o [ `ListView` ](xref:Xamarin.Forms.ListView), que controla se as células de cabeçalho float durante a rolagem:

[![Captura de tela de flutuantes e não flutuante ListView células de cabeçalho, no iOS](listview-group-header-style-images/group-header-styles.png "ListView com células de cabeçalho flutuante e não flutuante")](listview-group-header-style-images/group-header-styles-large.png#lightbox "ListView com células de cabeçalho flutuante e não flutuante")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
