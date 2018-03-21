---
title: "Células xamarin. Forms"
description: "Xamarin. Forms células podem ser adicionadas ao ListViews e TableViews."
ms.topic: article
ms.prod: xamarin
ms.assetid: 77DA0C89-35D6-4C09-A072-3ADE53FD56CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 0f8886546004702adbdbca7d991c67d5700e453e
ms.sourcegitcommit: d450ae06065d8f8c80f3588bc5a614cfd97b5a67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2018
---
# <a name="xamarinforms-cells"></a>Células xamarin. Forms

_Xamarin. Forms células podem ser adicionadas ao ListViews e TableViews._

Um *célula* é um elemento especializado usado para itens em uma tabela e descreve como cada item em uma lista deve ser processado. O [ `Cell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/) classe derivada de [ `Element` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Element/), do qual [ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Element/) também deriva. Uma célula não em si é um elemento visual. em vez disso, é um modelo para criar um elemento visual. 

`Cell` é usado exclusivamente com [ `ListView` ](views.md#listView) e [ `TableView` ](views.md#tableView) controles. Para saber como usar e personalizar células, consulte o [ `ListView` ](~/xamarin-forms/user-interface/listview/index.md) e [ `TableView` ](~/xamarin-forms/user-interface/tableview.md) documentação.

## <a name="cells"></a>Células

Xamarin. Forms suporta os seguintes tipos de célula:

<a name="textCell" />

### <a name="textcell"></a>TextCell

|     |     |
| --- | --- |
| Um [ `TextCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell) exibe uma ou duas cadeias de caracteres de texto. Definir o [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TextCell.Text/) propriedade e, opcionalmente, o [ `Detail` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TextCell.Detail/) propriedade para essas cadeias de caracteres de texto.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell) / [guia](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell) | [![Exemplo de TextCell](cells-images/TextCell.png "TextCell exemplo")](cells-images/TextCell-Large.png#lightbox "TextCell exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/TextCellDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/TextCellDemoPage.xaml) |
|     |     |

### <a name="imagecell"></a>ImageCell

|     |     |
| --- | --- |
| O [ `ImageCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell) exibe as mesmas informações que [ `TextCell` ](#textCell) , mas inclui um bitmap que podem ser definidas com o [ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Image.Source/) propriedade.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell) / [guia](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell) | [![Exemplo de ImageCell](cells-images/ImageCell.png "ImageCell exemplo")](cells-images/ImageCell-Large.png#lightbox "ImageCell exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ImageCellDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ImageCellDemoPage.xaml) |
|     |     |

### <a name="switchcell"></a>SwitchCell

|     |     |
| --- | --- |
| O [ `SwitchCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell) contém texto com o [ `Text`'](https://developer.xamarin.com/api/property/Xamarin.Forms.SwitchCellText/) propriedade e e ativar/desativar opção inicialmente definida com o valor booliano [ `On` ](https://developer.xamarin.com/api/property/Xamarin.Forms.SwitchCell.On/) propriedade. Manipular o [ `OnChanged` ](https://developer.xamarin.com/api/event/Xamarin.Forms.SwitchCell.OnChanged/) evento a ser notificado quando o `On` alterações de propriedade.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell) / [guia](~/xamarin-forms/user-interface/tableview.md#switchcell) | [![Exemplo de SwitchCell](cells-images/SwitchCell.png "SwitchCell exemplo")](cells-images/SwitchCell-Large.png#lightbox "SwitchCell exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/SwitchCellDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/SwitchCellDemoPage.xaml) |
|     |     |

### <a name="entrycell"></a>EntryCell

|     |     |
| --- | --- |
| O [ `EntryCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell) define uma [ `Label` ](https://developer.xamarin.com/api/property/Xamarin.Forms.EntryCell.Label/) propriedade que identifica a célula e uma única linha de texto editável no [ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.EntryCell.Text/) propriedade. Manipular o [ `Completed` ](https://developer.xamarin.com/api/event/Xamarin.Forms.EntryCell.Completed/) evento a ser notificado quando o usuário tiver concluído a entrada de texto.<br /><br />[Documentação da API](https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell) / [guia](~/xamarin-forms/user-interface/tableview.md#entrycell) | [![Exemplo de EntryCell](cells-images/EntryCell.png "EntryCell exemplo")](cells-images/EntryCell-Large.png#lightbox "EntryCell exemplo")<br />[O código c# para essa página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/EntryCellDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/EntryCellDemoPage.xaml) |
|     |     |


## <a name="related-links"></a>Links relacionados

- [Introdução ao Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Exemplo de FormsGallery xamarin. Forms](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)
- [Amostras do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Documentação da API de xamarin. Forms](https://developer.xamarin.com/api/root/Xamarin.Forms/)
