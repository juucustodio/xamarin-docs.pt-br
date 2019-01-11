---
title: Estilo do separador de ListView no iOS
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como consumir o iOS específicos da plataforma que controla se o separador entre as células em um ListView que usa a largura total do ListView.
ms.prod: xamarin
ms.assetid: A4CB45CE-9FB7-47ED-8C3D-93E39BF282E4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 0fc8cdfac22ad54b73af193ce76e8fd27b8fb0da
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209461"
---
# <a name="listview-separator-style-on-ios"></a>Estilo do separador de ListView no iOS

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Este específicos da plataforma iOS controla se o separador entre células em uma [ `ListView` ](xref:Xamarin.Forms.ListView) usa a largura total do `ListView`. Ele é consumido em XAML, definindo o [ `ListView.SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SeparatorStyleProperty) propriedade anexada a um valor da [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.SeparatorStyle="FullWidth">
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

listView.On<iOS>().SetSeparatorStyle(SeparatorStyle.FullWidth);
```

O `ListView.On<iOS>` método Especifica que este específicos da plataforma serão executado apenas no iOS. O [ `ListView.SetSeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SetSeparatorStyle(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle)) método, no [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usada para controlar se o separador entre células no [ `ListView` ](xref:Xamarin.Forms.ListView) usa completo largura do `ListView`, com o [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) enumeração fornecendo dois valores possíveis:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.Default) – indica o comportamento do separador de iOS padrão. Esse é o comportamento padrão no xamarin. Forms.
- [`FullWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.FullWidth) – indica que os separadores serão desenhados de uma borda da `ListView` para outro.

O resultado é que o especificado [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) valor é aplicado a [ `ListView` ](xref:Xamarin.Forms.ListView), que controla a largura do separador entre células:

![](listview-separator-style-images/listview-separatorstyle.png "ListView SeparatorStyle específicos da plataforma")

> [!NOTE]
> Depois que o estilo do separador foi definido como `FullWidth`, ele não pode ser alterado para `Default` em tempo de execução.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
