---
title: Xamarin.Forms Nas
description: Xamarin.Forms as células podem ser adicionadas a ListViews e TableViews. Este artigo lista as células incluídas no Xamarin.Forms .
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 01f625d9ecfb91bc36013b7f6d45fb3d275e8bee
ms.sourcegitcommit: 10c7dd16fe78226053d1d036492b6c9102fc421b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "93370813"
---
# <a name="xamarinforms-cells"></a>Xamarin.Forms Nas

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](/samples/xamarin/xamarin-forms-samples/formsgallery)

_Xamarin.Forms as células podem ser adicionadas a ListViews e TableViews._

Uma *célula* é um elemento especializado usado para itens em uma tabela e descreve como cada item em uma lista deve ser renderizado. A [`Cell`](xref:Xamarin.Forms.Cell) classe deriva de [`Element`](xref:Xamarin.Forms.Element) , da qual [`VisualElement`](xref:Xamarin.Forms.Element) também deriva. Uma célula não é, em si, um elemento Visual; em vez disso, é um modelo para criar um elemento visual.

`Cell` é usado exclusivamente com [`ListView`](xref:Xamarin.Forms.ListView) [`TableView`](xref:Xamarin.Forms.TableView) controles e. Para saber como usar e personalizar células, consulte a [`ListView`](~/xamarin-forms/user-interface/listview/index.md) [`TableView`](~/xamarin-forms/user-interface/tableview.md) documentação e.

## <a name="cells"></a>Células

Xamarin.Forms o oferece suporte aos seguintes tipos de célula:

| Tipo | Descrição | Aparência |
| --- | --- | --- |
| `TextCell` | Um [`TextCell`](xref:Xamarin.Forms.TextCell) exibe uma ou duas cadeias de caracteres de texto. Defina a [`Text`](xref:Xamarin.Forms.TextCell.Text) propriedade e, opcionalmente, a [`Detail`](xref:Xamarin.Forms.TextCell.Detail) propriedade para essas cadeias de caracteres de texto.<br /><br />[Documentação](xref:Xamarin.Forms.TextCell)  /  da API [Guia](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell) | [![Exemplo de textcell](cells-images/TextCell.png "Exemplo de textcell")](cells-images/TextCell-Large.png#lightbox "Exemplo de textcell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
| `ImageCell` | O [`ImageCell`](xref:Xamarin.Forms.ImageCell) exibe as mesmas informações como [`TextCell`](xref:Xamarin.Forms.TextCell) , mas inclui um bitmap que você define com a [`Source`](xref:Xamarin.Forms.Image.Source) propriedade.<br /><br />[Documentação](xref:Xamarin.Forms.ImageCell)  /  da API [Guia](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell) | [![Exemplo de ImageCell](cells-images/ImageCell.png "Exemplo de ImageCell")](cells-images/ImageCell-Large.png#lightbox "Exemplo de ImageCell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
| `SwitchCell` | O [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) texto Contains definido com a [`Text`](xref:Xamarin.Forms.SwitchCell.Text) propriedade e uma opção on/off inicialmente definida com a propriedade booliana [`On`](xref:Xamarin.Forms.SwitchCell.On) . Manipule o [`OnChanged`](xref:Xamarin.Forms.SwitchCell.OnChanged) evento a ser notificado quando a `On` propriedade for alterada.<br /><br />[Documentação](xref:Xamarin.Forms.SwitchCell)  /  da API [Guia](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![Exemplo de SwitchCell](cells-images/SwitchCell.png "Exemplo de SwitchCell")](cells-images/SwitchCell-Large.png#lightbox "Exemplo de SwitchCell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
| `EntryCell` | O [`EntryCell`](xref:Xamarin.Forms.EntryCell) define uma [`Label`](xref:Xamarin.Forms.EntryCell.Label) propriedade que identifica a célula e uma única linha de texto editável na [`Text`](xref:Xamarin.Forms.EntryCell.Text) propriedade. Manipule o [`Completed`](xref:Xamarin.Forms.EntryCell.Completed) evento a ser notificado quando o usuário tiver concluído a entrada de texto.<br /><br />[Documentação](xref:Xamarin.Forms.EntryCell)  /  da API [Guia](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![Exemplo de EntryCell](cells-images/EntryCell.png "Exemplo de EntryCell")](cells-images/EntryCell-Large.png#lightbox "Exemplo de EntryCell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
| | | |

## <a name="related-links"></a>Links relacionados

- [Xamarin.Forms Exemplo de FormsGallery](/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.Forms Amostras](/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.Forms Documentação da API](/dotnet/api/xamarin.forms?view=xamarin-forms)