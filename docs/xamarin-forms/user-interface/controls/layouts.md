---
title: Layouts de xamarin. Forms
description: "Xamarin. Forms Layouts são usados para compor a controles de interface do usuário em estruturas lógicas."
ms.topic: article
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: ecea0f55360fcde7a50c52bb33c45a2c5fff5eeb
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="xamarinforms-layouts"></a>Layouts de xamarin. Forms

_Xamarin. Forms Layouts são usados para compor a controles de interface do usuário em estruturas lógicas._

<style>.tableimg {largura máxima: nenhum! importante;}</style>

## <a name="layouts"></a>Layouts

O [ `Layout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout) classe xamarin. Forms é um subtipo especializado de exibição, que atua como um contêiner para outros modos de exibição ou layout. Normalmente, ele contém a lógica para definir a posição e tamanho dos elementos filho em aplicativos xamarin. Forms.

 [ ![](layouts-images/layouts-sml.png "Tipos de Layout xamarin. Forms")](layouts-images/layouts.png "tipos de Layout xamarin. Forms")

<br clear="all" />

Xamarin. Forms oferece suporte a:

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
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/">ContentPresenter</a>
    </td>
    <td valign="top">
Um Gerenciador de layout para modos de exibição de modelo. Usado em uma <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/">ControlTemplate</a> para marcar onde o conteúdo a ser apresentados aparece.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/Templates/ControlTemplates/SimpleTheme/SimpleTheme/App.xaml"><img src="layouts-images/ContentPresenter.png" title="Exemplo de ContentPresenter" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/">ContentView</a>
    </td>
    <td valign="top">
Um elemento com um único conteúdo. ContentView tem muito pouco uso próprio. Sua finalidade é servir como uma classe base para modos de exibição compostas definido pelo usuário.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ContentViewDemoPage.cs"><img src="layouts-images/ContentView.png" title="Exemplo de ContentView" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Frame/">Quadro</a>
    </td>
    <td valign="top">
Um elemento que contém um único filho, com algumas opções de enquadramento. Quadro têm um padrão <a href="https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/">Xamarin.Forms.Layout.Padding</a> de 20.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/FrameDemoPage.cs"><img src="layouts-images/Frame.png" title="Exemplo de quadro" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/">ScrollView</a>
    </td>
    <td valign="top">
Requer um elemento capaz de se o conteúdo de rolagem.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ScrollViewDemoPage.cs"><img src="layouts-images/ScrollView.png" title="Exemplo de ScrollView" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedView/">TemplatedView</a>
    </td>
    <td valign="top">
Um elemento que exibe o conteúdo com um modelo de controle e a classe base para <a href=""/api/type/Xamarin.Forms.ContentView/">ContentView</a>.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/tree/master/Templates/ControlTemplates/"><img src="layouts-images/TemplatedView.png" title="Exemplo de TemplatedView" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/">AbsoluteLayout</a>
    </td>
    <td valign="top">
Posiciona elementos filho em posições absolutas de solicitada. Usuário atribuído âncoras e limites define a posição e o tamanho do controle.
    </td>
    <td valign="top">
      <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/AbsoluteLayoutDemoPage.cs"><img src="layouts-images/AbsoluteLayout.png" title="Exemplo de AbsoluteLayout" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/">Grade</a>
    </td>
    <td valign="top">
Um layout que contém exibições organizadas em linhas e colunas.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/GridDemoPage.cs"><img src="layouts-images/Grid.png" title="Exemplo de grade" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td>
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/">RelativeLayout</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/%601">Layout</a> que usa <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Constraint/">restrição</a>s para o layout de seus filhos.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/RelativeLayoutDemoPage.cs"><img src="layouts-images/RelativeLayout.png" title="Exemplo de RelativeLayout" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/">StackLayout</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/">Layout</a> que posiciona elementos filho em uma única linha que pode ser orientada verticalmente ou horizontalmente. Esse layout definirá o filho limites automaticamente durante um ciclo de layout. Usuário atribuído dos limites será substituído e, portanto, não deve ser definido em um elemento filho pelo usuário.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/StackLayoutDemoPage.cs"><img src="layouts-images/StackLayout.png" title="Exemplo de StackLayout" class="tableimg">
    </a></td>
  </tr>
  </tbody>
</table>



## <a name="related-links"></a>Links relacionados

- [Introdução ao Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Galeria xamarin. Forms (exemplo)](https://developer.xamarin.com/samples/FormsGallery/)
- [Exemplos de xamarin. Forms](https://developer.xamarin.com/samples/tag/Xamarin.Forms/)
- [Documentação da API de xamarin. Forms](https://developer.xamarin.com/api/namespace/Xamarin.Forms)
