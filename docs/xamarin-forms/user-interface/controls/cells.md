---
title: Xamarin.FormsNas
description: Xamarin.Formsas células podem ser adicionadas a ListViews e TableViews. Este artigo lista as células incluídas no Xamarin.Forms .
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bd1a2398787fe39c0b4cbd08ccd5c5793775d5cf
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137274"
---
# <a name="xamarinforms-cells"></a>Xamarin.FormsNas

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_As células do Xamarin. Forms podem ser adicionadas a ListViews e TableViews._

Uma *célula* é um elemento especializado usado para itens em uma tabela e descreve como cada item em uma lista deve ser renderizado. A [`Cell`](xref:Xamarin.Forms.Cell) classe deriva de [`Element`](xref:Xamarin.Forms.Element) , da qual [`VisualElement`](xref:Xamarin.Forms.Element) também deriva. Uma célula não é, em si, um elemento Visual; em vez disso, é um modelo para criar um elemento visual.

`Cell`é usado exclusivamente com [`ListView`](views.md#listview) [`TableView`](views.md#tableview) controles e. Para saber como usar e personalizar células, consulte a [`ListView`](~/xamarin-forms/user-interface/listview/index.md) [`TableView`](~/xamarin-forms/user-interface/tableview.md) documentação e.

## <a name="cells"></a>Células

Xamarin.Formso oferece suporte aos seguintes tipos de célula:

<a name="textCell" />

### <a name="textcell"></a>Textcell

|     |     |
| --- | --- |
| Um [`TextCell`](xref:Xamarin.Forms.TextCell) exibe uma ou duas cadeias de caracteres de texto. Defina a [`Text`](xref:Xamarin.Forms.TextCell.Text) propriedade e, opcionalmente, a [`Detail`](xref:Xamarin.Forms.TextCell.Detail) propriedade para essas cadeias de caracteres de texto.<br /><br />[Documentação](xref:Xamarin.Forms.TextCell)  /  da API [Guia](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell) | [![Exemplo de textcell](cells-images/TextCell.png "Exemplo de textcell")](cells-images/TextCell-Large.png#lightbox "Exemplo de textcell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
|     |     |

### <a name="imagecell"></a>ImageCell

|     |     |
| --- | --- |
| O [`ImageCell`](xref:Xamarin.Forms.ImageCell) exibe as mesmas informações como [`TextCell`](#textCell) , mas inclui um bitmap que você define com a [`Source`](xref:Xamarin.Forms.Image.Source) propriedade.<br /><br />[Documentação](xref:Xamarin.Forms.ImageCell)  /  da API [Guia](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell) | [![Exemplo de ImageCell](cells-images/ImageCell.png "Exemplo de ImageCell")](cells-images/ImageCell-Large.png#lightbox "Exemplo de ImageCell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
|     |     |

### <a name="switchcell"></a>SwitchCell

|     |     |
| --- | --- |
| O [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) texto Contains definido com a [`Text`](xref:Xamarin.Forms.SwitchCell.Text) propriedade e uma opção on/off inicialmente definida com a propriedade booliana [`On`](xref:Xamarin.Forms.SwitchCell.On) . Manipule o [`OnChanged`](xref:Xamarin.Forms.SwitchCell.OnChanged) evento a ser notificado quando a `On` propriedade for alterada.<br /><br />[Documentação](xref:Xamarin.Forms.SwitchCell)  /  da API [Guia](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![Exemplo de SwitchCell](cells-images/SwitchCell.png "Exemplo de SwitchCell")](cells-images/SwitchCell-Large.png#lightbox "Exemplo de SwitchCell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
|     |     |

### <a name="entrycell"></a>EntryCell

|     |     |
| --- | --- |
| O [`EntryCell`](xref:Xamarin.Forms.EntryCell) define uma [`Label`](xref:Xamarin.Forms.EntryCell.Label) propriedade que identifica a célula e uma única linha de texto editável na [`Text`](xref:Xamarin.Forms.EntryCell.Text) propriedade. Manipule o [`Completed`](xref:Xamarin.Forms.EntryCell.Completed) evento a ser notificado quando o usuário tiver concluído a entrada de texto.<br /><br />[Documentação](xref:Xamarin.Forms.EntryCell)  /  da API [Guia](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![Exemplo de EntryCell](cells-images/EntryCell.png "Exemplo de EntryCell")](cells-images/EntryCell-Large.png#lightbox "Exemplo de EntryCell")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Links relacionados

- [Xamarin.FormsExemplo de FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.FormsAmostras](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.FormsDocumentação da API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
