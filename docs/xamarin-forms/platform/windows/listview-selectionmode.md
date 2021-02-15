---
title: ListView SelectionMode no Windows
description: As especificações de plataforma permitem que você consuma a funcionalidade que só está disponível em uma plataforma específica, sem implementar renderizadores ou efeitos personalizados. Este artigo explica como consumir a plataforma Windows específica que controla se os itens em um ListView podem responder a gestos de toque.
ms.prod: xamarin
ms.assetid: 57EF3A7F-1407-4B31-AE21-D149293D4228
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9588feeaae1391bcd2ac7fd05e7340a3a40f6236
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374791"
---
# <a name="listview-selectionmode-on-windows"></a>ListView SelectionMode no Windows

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Na Plataforma Universal do Windows, por padrão, o Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) usa o `ItemClick` evento nativo para responder à interação, em vez do `Tapped` evento nativo. Isso fornece a funcionalidade de acessibilidade para que o narrador do Windows e o teclado possam interagir com o `ListView` . No entanto, ele também renderiza quaisquer gestos de toque dentro do `ListView` inoperante.

Essa Plataforma Universal do Windows específica da plataforma controla se os itens em um [`ListView`](xref:Xamarin.Forms.ListView) podem responder a gestos de toque e, portanto, se o nativo `ListView` dispara o `ItemClick` `Tapped` evento ou. Ele é consumido em XAML definindo a [`ListView.SelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty) Propriedade anexada como um valor da [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) enumeração:

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

Como alternativa, ele pode ser consumido em C# usando a API fluente:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

listView.On<Windows>().SetSelectionMode(ListViewSelectionMode.Inaccessible);
```

O `ListView.On<Windows>` método especifica que essa plataforma específica será executada somente no plataforma universal do Windows. O [ `ListView.SetSelectionMode` ] (xref: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. ListView. setselectionmode ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . ListView}, Xamarin.Forms . PlatformConfiguration. WindowsSpecific. ListViewSelectionMode)), no [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) namespace, é usado para controlar se os itens em um [`ListView`](xref:Xamarin.Forms.ListView) podem responder a gestos de toque, com a [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) Enumeração fornecendo dois valores possíveis:

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible) – indica que o `ListView` acionará o `ItemClick` evento nativo para lidar com a interação e, portanto, fornecerá a funcionalidade de acessibilidade. Portanto, o Windows Narrator e o teclado podem interagir com o `ListView` . No entanto, os itens no `ListView` não podem responder a gestos de toque. Esse é o comportamento padrão para `ListView` instâncias no plataforma universal do Windows.
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible) – indica que o `ListView` irá disparar o `Tapped` evento nativo para lidar com a interação. Portanto, os itens no `ListView` podem responder a gestos de toque. No entanto, não há nenhuma funcionalidade de acessibilidade e, portanto, o narrador do Windows e o teclado não podem interagir com o `ListView` .

> [!NOTE]
> Os `Accessible` `Inaccessible` modos de seleção e são mutuamente exclusivos, e você precisará escolher entre um acessível [`ListView`](xref:Xamarin.Forms.ListView) ou um `ListView` que possa responder a gestos de toque.

Além disso, o [ `GetSelectionMode` ] (xref: Xamarin.Forms . PlatformConfiguration. WindowsSpecific. ListView. getselectionmode ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . ListView})), o método pode ser usado para retornar o atual [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) .

O resultado é que um especificado [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) é aplicado ao [`ListView`](xref:Xamarin.Forms.ListView) , que controla se os itens no `ListView` podem responder a gestos de toque e, portanto, se o nativo `ListView` dispara `ItemClick` o `Tapped` evento ou.

## <a name="related-links"></a>Links relacionados

- [PlatformSpecifics (exemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Criação de itens específicos à plataforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)