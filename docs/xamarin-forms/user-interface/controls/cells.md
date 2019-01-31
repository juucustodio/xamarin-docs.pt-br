---
title: Células do xamarin. Forms
description: As células do xamarin. Forms podem ser adicionadas a ListViews e TableViews. Este artigo lista as células incluídas no xamarin. Forms.
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 5ef1aba847954ccdeb230acd82ebbc5015ebd6b7
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55292292"
---
# <a name="xamarinforms-cells"></a>Células do xamarin. Forms

[![Baixar exemplo](~/media/shared/download.png) baixar o exemplo](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)

_As células do xamarin. Forms podem ser adicionadas a ListViews e TableViews._

Um *célula* é um elemento especializado usado para itens em uma tabela e descreve como cada item em uma lista deve ser renderizado. O [ `Cell` ](xref:Xamarin.Forms.Cell) classe deriva [ `Element` ](xref:Xamarin.Forms.Element), do qual [ `VisualElement` ](xref:Xamarin.Forms.Element) também deriva. Uma célula não é propriamente um elemento visual. em vez disso, ele é um modelo para a criação de um elemento visual.

`Cell` é usado exclusivamente com [ `ListView` ](views.md#listView) e [ `TableView` ](views.md#tableView) controles. Para saber como usar e personalizar células, consulte o [ `ListView` ](~/xamarin-forms/user-interface/listview/index.md) e [ `TableView` ](~/xamarin-forms/user-interface/tableview.md) documentação.

## <a name="cells"></a>Células

Xamarin. Forms dá suporte aos seguintes tipos de célula:

<a name="textCell" />

### <a name="textcell"></a>TextCell

|     |     |
| --- | --- |
| Um [ `TextCell` ](xref:Xamarin.Forms.TextCell) exibe uma ou duas cadeias de caracteres de texto. Defina as [ `Text` ](xref:Xamarin.Forms.TextCell.Text) propriedade e, opcionalmente, o [ `Detail` ](xref:Xamarin.Forms.TextCell.Detail) propriedade para essas cadeias de caracteres de texto.<br /><br />[Documentação da API](xref:Xamarin.Forms.TextCell) / [guia](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) | [![Exemplo de TextCell](cells-images/TextCell.png "exemplo TextCell")](cells-images/TextCell-Large.png#lightbox "TextCell exemplo")<br />[O código c# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
|     |     |

### <a name="imagecell"></a>ImageCell

|     |     |
| --- | --- |
| O [ `ImageCell` ](xref:Xamarin.Forms.ImageCell) exibe as mesmas informações que [ `TextCell` ](#textCell) , mas inclui um bitmap que podem ser definidas com o [ `Source` ](xref:Xamarin.Forms.Image.Source) propriedade.<br /><br />[Documentação da API](xref:Xamarin.Forms.ImageCell) / [guia](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell) | [![Exemplo de ImageCell](cells-images/ImageCell.png "exemplo ImageCell")](cells-images/ImageCell-Large.png#lightbox "ImageCell exemplo")<br />[O código c# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
|     |     |

### <a name="switchcell"></a>SwitchCell

|     |     |
| --- | --- |
| O [ `SwitchCell` ](xref:Xamarin.Forms.SwitchCell) contém o texto definido com o [ `Text`'](xref:Xamarin.Forms.SwitchCell.Text) propriedade e ativar/desativar opção inicialmente é definida com o valor booliano [ `On` ](xref:Xamarin.Forms.SwitchCell.On) propriedade. Lidar com o [ `OnChanged` ](xref:Xamarin.Forms.SwitchCell.OnChanged) evento a ser notificado quando o `On` alterações de propriedade.<br /><br />[Documentação da API](xref:Xamarin.Forms.SwitchCell) / [guia](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![Exemplo de SwitchCell](cells-images/SwitchCell.png "exemplo SwitchCell")](cells-images/SwitchCell-Large.png#lightbox "SwitchCell exemplo")<br />[O código c# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
|     |     |

### <a name="entrycell"></a>EntryCell

|     |     |
| --- | --- |
| O [ `EntryCell` ](xref:Xamarin.Forms.EntryCell) define um [ `Label` ](xref:Xamarin.Forms.EntryCell.Label) propriedade que identifica a célula e uma única linha de texto editável no [ `Text` ](xref:Xamarin.Forms.EntryCell.Text) propriedade. Lidar com o [ `Completed` ](xref:Xamarin.Forms.EntryCell.Completed) evento a ser notificado quando o usuário tiver concluído a entrada de texto.<br /><br />[Documentação da API](xref:Xamarin.Forms.EntryCell) / [guia](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![Exemplo de EntryCell](cells-images/EntryCell.png "exemplo EntryCell")](cells-images/EntryCell-Large.png#lightbox "EntryCell exemplo")<br />[O código c# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
|     |     |


## <a name="related-links"></a>Links relacionados

- [Exemplo de xamarin. Forms FormsGallery](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)
- [Amostras do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Documentação da API do Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
