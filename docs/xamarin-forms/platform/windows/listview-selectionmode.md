---
title: ListView SelectionMode no Windows
description: Especificidades da plataforma permitem que você consumir funcionalidade só está disponível em uma plataforma específica, sem implementar renderizadores personalizados ou efeitos. Este artigo explica como utilizar o Windows específicos da plataforma que controla se os itens em um ListView podem responder a gestos de toque.
ms.prod: xamarin
ms.assetid: 57EF3A7F-1407-4B31-AE21-D149293D4228
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: d2a94347448af031f50341729d77c7385225d107
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60855045"
---
# <a name="listview-selectionmode-on-windows"></a>ListView SelectionMode no Windows

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Na plataforma Universal do Windows, por padrão, o xamarin. Forms [ `ListView` ](xref:Xamarin.Forms.ListView) usa nativo `ItemClick` eventos para responder à interação, em vez de nativo `Tapped` eventos. Isso fornece funcionalidades de acessibilidade para que o Narrador do Windows e o teclado podem interagir com o `ListView`. No entanto, ele também processa qualquer gestos de toque dentro a `ListView` inoperável.

Este controles específicos da plataforma de plataforma Universal do Windows se itens em uma [ `ListView` ](xref:Xamarin.Forms.ListView) pode responder para gestos de toque e, portanto, se nativo `ListView` é acionado o `ItemClick` ou `Tapped` eventos. Ele é consumido em XAML, definindo o [ `ListView.SelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty) propriedade anexada a um valor da [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) enumeração:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <ListView ... windows:ListView.SelectionMode="Inaccessible">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Como alternativa, ele pode ser consumido de C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

listView.On<Windows>().SetSelectionMode(ListViewSelectionMode.Inaccessible);
```

O `ListView.On<Windows>` método Especifica que este específicos da plataforma só serão executado na plataforma Universal do Windows. O [ `ListView.SetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode)) método, no [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usada para controlar se itens em um [ `ListView` ](xref:Xamarin.Forms.ListView) pode responder para toque gestos, com o [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) enumeração fornecendo dois valores possíveis:

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible) – indica que o `ListView` acionarão nativo `ItemClick` eventos para manipular a interação e, portanto, fornecer funcionalidades de acessibilidade. Portanto, o Narrador do Windows e o teclado podem interagir com o `ListView`. No entanto, os itens no `ListView` não pode responder para gestos de toque. Esse é o comportamento padrão para `ListView` instâncias na plataforma Universal do Windows.
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible) – indica que o `ListView` acionarão nativo `Tapped` eventos para manipular a interação. Portanto, os itens no `ListView` pode responder para gestos de toque. No entanto, não há nenhuma funcionalidade de acessibilidade e, portanto, o Narrador do Windows e o teclado não é possível interagir com o `ListView`.

> [!NOTE]
> O `Accessible` e `Inaccessible` modos de seleção são mutuamente exclusivos, e você precisará escolher entre uma acessível [ `ListView` ](xref:Xamarin.Forms.ListView) ou um `ListView` que pode responder a gestos de toque.

Além disso, o [ `GetSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.GetSelectionMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.ListView})) método pode ser usado para retornar atual [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode).

O resultado é que o especificado [ `ListViewSelectionMode` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) é aplicado ao [ `ListView` ](xref:Xamarin.Forms.ListView), que controla se itens do `ListView` pode responder para gestos de toque e, portanto, se nativo `ListView` é acionado o `ItemClick` ou `Tapped` eventos.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (amostra)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
