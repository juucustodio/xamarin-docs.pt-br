---
title: "Células xamarin. Forms"
description: "Xamarin. Forms células podem ser adicionadas ao ListViews e TableViews."
ms.topic: article
ms.prod: xamarin
ms.assetid: F2A02DEE-7137-42F4-9C0A-4E1CF75EA08F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 509ecc509754bba544115c140e619f634bd64eae
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="xamarinforms-cells"></a>Células xamarin. Forms

_Xamarin. Forms células podem ser adicionadas ao ListViews e TableViews._

<style>.tableimg {largura máxima: nenhum! importante;}</style>

## <a name="cells"></a>Células

Uma célula é um elemento especializado usado para itens em uma tabela e descreve como cada item em uma lista deve ser desenhada. Célula derivada de [ `Element` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Element/), do qual VisualElement também deriva. No entanto, célula não é um elemento visual, ele apenas descreve um modelo para criar um elemento visual. [`Cell`](https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/) fornece a classe base e recursos para todas as células do xamarin. Forms. As células são elementos projetados para ser adicionado ao [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) ou [ `TableView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/) controles.

Para saber como usar e personalizar células, consulte o [ListView](~/xamarin-forms/user-interface/listview/index.md) e [modo de tabela](~/xamarin-forms/user-interface/tableview.md) documentação.

<table align="center" border="1" cellpadding="1" cellspacing="1">
<thead>
    <th>
      <strong>tipo de</strong>
    </th>
    <th>
      <strong>Descrição</strong>
    </th>
    <th style="min-width:400px">
      <strong>captura de tela</strong>
    </th>
  </thead>
  <tbody>
    <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.EntryCell/">EntryCell</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/">Xamarin.Forms.Cell</a> com um rótulo e um campo de entrada de texto de única linha.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/EntryDemoPage.cs"><img src="cells-images/EntryCell.png" title="Exemplo de EntryCell" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.SwitchCell/">SwitchCell</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/">Xamarin.Forms.Cell</a> com um nome e uma chave liga/desliga.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/SwitchCellDemoPage.cs"><img src="cells-images/SwitchCell.png" title="Exemplo de SwitchCell" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/">TextCell</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/">Xamarin.Forms.Cell</a> com texto primário e secundário.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/TextCellDemoPage.cs"><img src="cells-images/TextCell.png" title="Exemplo de TextCell" class="tableimg">
    </a></td>
  </tr>
      <tr>
    <td>
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/">ImageCell</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/">texto da célula</a> que também inclui uma imagem.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ImageCellDemoPage.cs"><img src="cells-images/ImageCell.png" title="Exemplo de ImageCell" class="tableimg">
    </a></td>
  </tr>
  </tbody>
</table>



## <a name="related-links"></a>Links relacionados

- [Introdução ao Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Galeria xamarin. Forms (exemplo)](https://developer.xamarin.com/samples/xamarin-forms/FormsGallery/)
- [Exemplos de xamarin. Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Documentação da API de xamarin. Forms](https://developer.xamarin.com/api/namespace/Xamarin.Forms/)
