---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e02ef600af761915d05c912b586e409dd6f46b85
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137079"
---
# <a name="searchbar-style-on-ios"></a>Estilo de SearchBar no iOS

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Esses controles específicos da plataforma iOS controlam se um [`SearchBar`](xref:Xamarin.Forms.SearchBar) tem um plano de fundo. Ele é consumido em XAML definindo a `SearchBar.SearchBarStyle` propriedade vinculável como um valor da `UISearchBarStyle` enumeração:

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

O `SearchBar.On<iOS>` método especifica que essa plataforma específica será executada somente no Ios. O `SearchBar.SetSearchBarStyle` método, no [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) namespace, é usado para controlar se o [`SearchBar`](xref:Xamarin.Forms.SearchBar) tem um plano de fundo. A `UISearchBarStyle` enumeração fornece três valores possíveis:

- `Default`indica que o [`SearchBar`](xref:Xamarin.Forms.SearchBar) tem o estilo padrão. Esse é o valor padrão da `SearchBar.SearchBarStyle` propriedade vinculável.
- `Prominent`indica que o [`SearchBar`](xref:Xamarin.Forms.SearchBar) tem um plano de fundo translúcido e que o campo de pesquisa é opaco.
- `Minimal`indica que o [`SearchBar`](xref:Xamarin.Forms.SearchBar) não tem nenhum plano de fundo e o campo de pesquisa é translúcida.

Além disso, o `SearchBar.GetSearchBarStyle` método pode ser usado para retornar o `UISearchBarStyle` que é aplicado ao `SearchBar` .

O resultado é que um `UISearchBarStyle` membro especificado é aplicado a um [`SearchBar`](xref:Xamarin.Forms.SearchBar) , que controla se o `SearchBar` tem um plano de fundo:

![Captura de tela de estilos de SearchBar, no iOS](searchbar-style-images/searchbar-styles.png "Estilos de SearchBar no iOS")

As capturas de tela a seguir mostram os `UISearchBarStyle` Membros aplicados aos [`SearchBar`](xref:Xamarin.Forms.SearchBar) objetos que têm suas `BackgroundColor` Propriedades definidas:

![Captura de tela de estilos de SearchBar com cor da tela de fundo, no iOS](searchbar-style-images/searchbar-background-styles.png "Estilos de SearchBar com cor de fundo no iOS")

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
