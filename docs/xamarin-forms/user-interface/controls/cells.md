---
title: :::no-loc(Xamarin.Forms):::Nas
description: ':::no-loc(Xamarin.Forms):::as células podem ser adicionadas a ListViews e TableViews. Este artigo lista as células incluídas no :::no-loc(Xamarin.Forms)::: .'
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
no-loc:
- ':::no-loc(Xamarin.Forms):::'
- ':::no-loc(Xamarin.Essentials):::'
ms.openlocfilehash: 21fca31ca9e1cf39843e04c3c381bb41ef77335f
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997274"
---
# <a name="no-locxamarinforms-cells"></a>:::no-loc(Xamarin.Forms):::Nas

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_:::no-loc(Xamarin.Forms):::as células podem ser adicionadas a ListViews e TableViews._

Uma *célula* é um elemento especializado usado para itens em uma tabela e descreve como cada item em uma lista deve ser renderizado. A [`Cell`](xref::::no-loc(Xamarin.Forms):::.Cell) classe deriva de [`Element`](xref::::no-loc(Xamarin.Forms):::.Element) , da qual [`VisualElement`](xref::::no-loc(Xamarin.Forms):::.Element) também deriva. Uma célula não é, em si, um elemento Visual; em vez disso, é um modelo para criar um elemento visual.

`Cell`é usado exclusivamente com [`ListView`](xref::::no-loc(Xamarin.Forms):::.ListView) [`TableView`](xref::::no-loc(Xamarin.Forms):::.TableView) controles e. Para saber como usar e personalizar células, consulte a [`ListView`](~/xamarin-forms/user-interface/listview/index.md) [`TableView`](~/xamarin-forms/user-interface/tableview.md) documentação e.

## <a name="cells"></a>Células

:::no-loc(Xamarin.Forms):::o oferece suporte aos seguintes tipos de célula:

| Tipo | Descrição | Aparência |
| --- | --- | --- |
| `TextCell` | Um [`TextCell`](xref::::no-loc(Xamarin.Forms):::.TextCell) exibe uma ou duas cadeias de caracteres de texto. Defina a [`Text`](xref::::no-loc(Xamarin.Forms):::.TextCell.Text) propriedade e, opcionalmente, a [`Detail`](xref::::no-loc(Xamarin.Forms):::.TextCell.Detail) propriedade para essas cadeias de caracteres de texto.<br /><br />[Documentação](xref::::no-loc(Xamarin.Forms):::.TextCell)  /  da API [Guia](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell) | [![Exemplo de textcell](cells-images/TextCell.png "Exemplo de textcell")](cells-images/TextCell-Large.png#lightbox "Exemplo de textcell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
| `ImageCell` | O [`ImageCell`](xref::::no-loc(Xamarin.Forms):::.ImageCell) exibe as mesmas informações como [`TextCell`](xref::::no-loc(Xamarin.Forms):::.TextCell) , mas inclui um bitmap que você define com a [`Source`](xref::::no-loc(Xamarin.Forms):::.Image.Source) propriedade.<br /><br />[Documentação](xref::::no-loc(Xamarin.Forms):::.ImageCell)  /  da API [Guia](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell) | [![Exemplo de ImageCell](cells-images/ImageCell.png "Exemplo de ImageCell")](cells-images/ImageCell-Large.png#lightbox "Exemplo de ImageCell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
| `SwitchCell` | O [`SwitchCell`](xref::::no-loc(Xamarin.Forms):::.SwitchCell) texto Contains definido com a [`Text`](xref::::no-loc(Xamarin.Forms):::.SwitchCell.Text) propriedade e uma opção on/off inicialmente definida com a propriedade booliana [`On`](xref::::no-loc(Xamarin.Forms):::.SwitchCell.On) . Manipule o [`OnChanged`](xref::::no-loc(Xamarin.Forms):::.SwitchCell.OnChanged) evento a ser notificado quando a `On` propriedade for alterada.<br /><br />[Documentação](xref::::no-loc(Xamarin.Forms):::.SwitchCell)  /  da API [Guia](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![Exemplo de SwitchCell](cells-images/SwitchCell.png "Exemplo de SwitchCell")](cells-images/SwitchCell-Large.png#lightbox "Exemplo de SwitchCell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
| `EntryCell` | O [`EntryCell`](xref::::no-loc(Xamarin.Forms):::.EntryCell) define uma [`Label`](xref::::no-loc(Xamarin.Forms):::.EntryCell.Label) propriedade que identifica a célula e uma única linha de texto editável na [`Text`](xref::::no-loc(Xamarin.Forms):::.EntryCell.Text) propriedade. Manipule o [`Completed`](xref::::no-loc(Xamarin.Forms):::.EntryCell.Completed) evento a ser notificado quando o usuário tiver concluído a entrada de texto.<br /><br />[Documentação](xref::::no-loc(Xamarin.Forms):::.EntryCell)  /  da API [Guia](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![Exemplo de EntryCell](cells-images/EntryCell.png "Exemplo de EntryCell")](cells-images/EntryCell-Large.png#lightbox "Exemplo de EntryCell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
| | | |

## <a name="related-links"></a>Links relacionados

- [:::no-loc(Xamarin.Forms):::Exemplo de FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [:::no-loc(Xamarin.Forms):::Amostras](https://docs.microsoft.com/samples/browse/?products=xamarin&term=:::no-loc(Xamarin.Forms):::)
- [:::no-loc(Xamarin.Forms):::Documentação da API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
