---
title: "Páginas xamarin. Forms"
description: "Páginas xamarin. Forms representam as telas de aplicativo móvel de plataforma cruzada."
ms.topic: article
ms.prod: xamarin
ms.assetid: F2A02DEE-7137-42F4-9C0A-4E1CF75EA08F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: 35822dbbb7d5694e7f1f0a3f35f10df404206af9
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="xamarinforms-pages"></a>Páginas xamarin. Forms

_Páginas xamarin. Forms representam as telas de aplicativo móvel de plataforma cruzada._

<style>.tableimg {largura máxima: nenhum! importante;}</style>

## <a name="pages"></a>Páginas

O [ `Page` ](http://iosapi.xamarin.com/?link=T%3aXamarin.Forms.Page) classe é um elemento visual que ocupa a maior parte ou todo da tela e contém um único filho. Um `Xamarin.Forms.Page` representa um controlador de exibição no iOS ou uma página no Windows Phone. No Android cada página ocupa a tela como uma atividade, mas xamarin. Forms páginas *não* atividades.

 [ ![](pages-images/pages-sml.png "Os tipos de página xamarin. Forms")](pages-images/pages.png "os tipos de página xamarin. Forms")

<br clear="all" />

Xamarin. Forms oferece suporte a:

<table align="center" border="1" cellpadding="1" cellspacing="1">
  <tr>
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
  </thead></tr>
  <tbody>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/">ContentPage</a>
    </td>
    <td align="center" valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/">ContentPage</a> exibe uma única <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">exibição</a>, geralmente um contêiner, como um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/">StackLayout</a> ou um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ScrollView/">ScrollView</a>.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ContentPageDemoPage.cs"><img src="pages-images/ContentPage.png" title="Exemplo de ContentPage" class="tableimg">
    </a></td>
  </tr><tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/">MasterDetailPage</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">página</a> que gerencia os dois painéis de informações.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/MasterDetailPageDemoPage.cs"><img src="pages-images/MasterDetailPage.png" title="Exemplo de MasterDetailPage" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.NavigationPage/">NavigationPage</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">página</a> que gerencia a navegação e a experiência do usuário de uma pilha de outras páginas.  
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/NavigationPageDemoPage.cs"><img src="pages-images/NavigationPage.png" title="Exemplo de NavigationPage" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/">TabbedPage</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">página</a> que permite a navegação entre filhos páginas usando as guias.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/TabbedPageDemoPage.cs"><img src="pages-images/TabbedPage.png" title="Exemplo de TabbedPage" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TemplatedPage/">TemplatedPage</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">página</a> que exibe o conteúdo de tela inteira com um modelo de controle e a classe base para <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/">ContentPage</a>.
    </td>
    <td valign="top">
    <a href="https://github.com/xamarin/xamarin-forms-samples/tree/master/Templates/ControlTemplates/"><img src="pages-images/TemplatedPage.png" title="Exemplo de TemplatedPage" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.CarouselPage/">CarouselPage</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Page/">página</a> permitindo gestos passar entre subpáginas, como uma galeria.
    </td>
    <td valign="top">
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CarouselPageDemoPage.cs"><img src="pages-images/CarouselPage.png" title="Exemplo de CarouselPage" class="tableimg">
    </a></td>
  </tr>
  </tbody>
</table>



## <a name="related-links"></a>Links relacionados

- [Introdução ao Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Galeria xamarin. Forms (exemplo)](https://developer.xamarin.com/samples/FormsGallery/)
- [Exemplos de xamarin. Forms](https://developer.xamarin.com/samples/tag/Xamarin.Forms/)
- [Documentação da API de xamarin. Forms](http://iosapi.xamarin.com/?link=N%3aXamarin.Forms)
