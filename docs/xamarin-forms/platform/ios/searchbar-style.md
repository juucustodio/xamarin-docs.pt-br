---
title: Estilo de SearchBar no iOS
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma do iOS específica que controla se um SearchBar tem um plano de fundo.
ms.prod: xamarin
ms.assetid: 3D512DD6-078E-4BC6-926E-62BA6F4DE640
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2020
ms.openlocfilehash: 7d95a90e96f868b6d8368054659f978555bc28ac
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82532698"
---
# <a name="searchbar-style-on-ios"></a>Estilo de SearchBar no iOS

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esses controles específicos da plataforma iOS controlam [`SearchBar`](xref:Xamarin.Forms.SearchBar) se um tem um plano de fundo. Ele é consumido em XAML definindo `SearchBar.SearchBarStyle` a propriedade vinculável como um valor `UISearchBarStyle` da enumeração:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ios:SearchBar.SearchBarStyle="Minimal"
                   Placeholder="Enter search term" />
        ...
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

SearchBar searchBar = new SearchBar { Placeholder = "Enter search term" };
searchBar.On<iOS>().SetSearchBarStyle(UISearchBarStyle.Minimal);
```

O `SearchBar.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `SearchBar.SetSearchBarStyle` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para controlar se o tem [`SearchBar`](xref:Xamarin.Forms.SearchBar) um plano de fundo. A `UISearchBarStyle` enumeração fornece três valores possíveis:

- `Default`indica que o [`SearchBar`](xref:Xamarin.Forms.SearchBar) tem o estilo padrão. Esse é o valor padrão da propriedade `SearchBar.SearchBarStyle` vinculável.
- `Prominent`indica que o [`SearchBar`](xref:Xamarin.Forms.SearchBar) tem um plano de fundo translúcido e que o campo de pesquisa é opaco.
- `Minimal`indica que o [`SearchBar`](xref:Xamarin.Forms.SearchBar) não tem nenhum plano de fundo e o campo de pesquisa é translúcida.

Além disso, o `SearchBar.GetSearchBarStyle` método pode ser usado para retornar o `UISearchBarStyle` que é aplicado ao `SearchBar`.

O resultado é que um membro `UISearchBarStyle` especificado é aplicado a um [`SearchBar`](xref:Xamarin.Forms.SearchBar), que controla se o `SearchBar` tem um plano de fundo:

![Captura de tela de estilos de SearchBar, no iOS](searchbar-style-images/searchbar-styles.png "Estilos de SearchBar no iOS")

As capturas de tela a `UISearchBarStyle` seguir mostram os [`SearchBar`](xref:Xamarin.Forms.SearchBar) Membros aplicados aos objetos `BackgroundColor` que têm suas propriedades definidas:

![Captura de tela de estilos de SearchBar com cor da tela de fundo, no iOS](searchbar-style-images/searchbar-background-styles.png "Estilos de SearchBar com cor de fundo no iOS")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
