---
title: "Modos de exibição do xamarin. Forms"
description: "Xamarin. Forms exibições são os blocos de construção de interfaces de usuário móvel de plataforma cruzada."
ms.topic: article
ms.prod: xamarin
ms.assetid: AC070686-A423-4A98-8BB6-0B9F94C062CC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/12/2016
ms.openlocfilehash: df8c8463b2556035c5369c70cb10dbc3dc6b6743
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="xamarinforms-views"></a>Modos de exibição do xamarin. Forms

_Xamarin. Forms exibições são os blocos de construção de interfaces de usuário móvel de plataforma cruzada._

<style>.tableimg {largura máxima: nenhum! importante;}</style>

## <a name="views"></a>Exibições

Xamarin. Forms usa a palavra *exibição* para se referir a objetos visuais, como botões, rótulos ou caixas de entrada de texto - o que podem ser mais comumente conhecidas como controles de widgets.

Esses elementos de interface do usuário são normalmente são subclasses de [ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/).

<br clear="right" />

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
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ActivityIndicator/">ActivityIndicator</a>
    </td>
    <td valign="top">
Um controle visual usado para indicar que algo está em andamento. Esse controle fornece uma indicação visual para o usuário que algo está acontecendo, sem informações sobre seu progresso.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ActivityIndicatorDemoPage.cs"><img src="views-images/ActivityIndicator.png" title="Exemplo de ActivityIndicator" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/">BoxView</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">exibição</a> usada para desenhar um retângulo colorido sólido. BoxView é um substituto útil para imagens ou elementos personalizados ao fazer protótipos inicial. BoxView tem uma solicitação de tamanho padrão de 40 x 40. Se você precisar de um tamanho diferente, atribua o <a href="https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/">VisualElement.WidthRequest</a> e <a href="https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/">VisualElement.HeightRequest</a>.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/BoxViewDemoPage.cs"><img src="views-images/BoxView.png" title="Exemplo de BoxView" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Button/">Button</a>
    </td>
    <td align="center" valign="top">
Um botão <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">exibição</a> que reage a eventos de toque.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ButtonDemoPage.cs"><img src="views-images/Button.png" title="Exemplo de botão" class="tableimg">
    </a></td>
  </tr>
  <tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.DatePicker/">Selecionador de data</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">exibição</a> que permite a separação de data. A representação visual de um DatePicker é muito semelhante a uma das <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">entrada</a>, exceto que um controle especial para escolher uma data é exibido no lugar de um teclado </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/DatePickerDemoPage.cs"><img src="views-images/DatePicker.png" title="Exemplo de DatePicker" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Editor/">Editor</a>
    </td>
    <td valign="top">
Um controle que pode editar várias linhas de texto. Para entradas de única linha, consulte <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">entrada</a>.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/EditorDemoPage.cs"><img src="views-images/Editor.png" title="Exemplo de editor" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">Entry</a>
    </td>
    <td valign="top">
Um controle que pode editar uma única linha de texto. Entrada é uma entrada de texto de única linha. Ele é mais adequado para coletar pequenas partes distintas de informações, como nomes de usuário e senhas.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/EntryDemoPage.cs"><img src="views-images/Entry.png" title="Exemplo de entrada" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Image/">Imagem</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">exibição</a> que contém uma imagem.
    <br />
    <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Image/">Imagem de API</a>
    <br />
    <a href="~/xamarin-forms/user-interface/images.md">Trabalhando com imagens</a>
    <br />
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ImageDemoPage.cs">Origem de demonstração</a>
    </td>
    <td>
    <img src="views-images/Image.png" title="Exemplo de imagem" class="tableimg">
    </td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Label/">Label</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">exibição</a> que exibe o texto em um formato de somente leitura. Um rótulo é usado para exibir elementos do texto de linha única, bem como blocos de várias linhas de texto.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/LabelDemoPage.cs"><img src="views-images/Label.png" title="Exemplo de rótulo" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/">ListView</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ItemsView%3CTVisual%3E/">ItemView</a> que exibe uma coleção de dados como uma lista vertical.
    <br />
    <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/">API de ListView</a>
    <br />
    <a href="~/xamarin-forms/user-interface/listview/index.md">Documentação de ListView</a>
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ListViewDemoPage.cs"><img src="views-images/ListView.png" title="Exemplo de ListView" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.OpenGLView/">OpenGLView</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">exibição</a> que exibe o conteúdo de OpenGL.
    <ul>
      <li>Só funciona para iOS e Android projetos (sem suporte do Windows Phone).
      <li>Requer uma referência para o <b>OpenTK 1.0</b> assembly em projetos Android e iOS.</li>
      <li>É mais adequada para usar em projetos compartilhados; Se usado em um PCL um DependencyService também será necessário.</li>
    </ul>
    </td>
    <td>
    <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.OpenGLView/"><img src="views-images/OpenGL.png" title="Exemplo de OpenGlView" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Picker/">Seletor</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">exibição</a> controle para a separação de um elemento em uma lista. A representação visual de um seletor é semelhante a um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">entrada</a>, mas um controle seletor é exibido no lugar de um teclado.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/PickerDemoPage.cs"><img src="views-images/Picker.png" title="Exemplo de seletor" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.ProgressBar/">ProgressBar</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">exibição</a> controle indicando um progresso.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/ProgressBarDemoPage.cs"><img src="views-images/ProgressBar.png" title="Exemplo de barra de progresso de classe ="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/">SearchBar</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">exibição</a> controle que fornece uma caixa de pesquisa.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/SearchBarDemoPage.cs"><img src="views-images/SearchBar.png" title="Exemplo de SearchBar" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/">Controle deslizante</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">exibição</a> controle que insere um valor linear.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/SliderDemoPage.cs"><img src="views-images/Slider.png" title="Exemplo de controle deslizante" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Stepper/">Seletor</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">exibição</a> controle que insere um valor discreto, restrito a um intervalo.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/StepperDemoPage.cs"><img src="views-images/Stepper.png" title="Exemplo de seletor" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/">Switch</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">exibição</a> controle que fornece um valor alternado.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/SwitchDemoPage.cs"><img src="views-images/Switch.png" title="Exemplo de switch" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/">TableView</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">exibição</a> que contém linhas de <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Cell/">célula</a>s.
    <br />
    <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TableView/">API do modo de tabela</a>
    <br />
    <a href="~/xamarin-forms/user-interface/tableview.md">Documentação do modo de tabela</a>
    <br />
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/TableViewFormDemoPage.cs">Origem de demonstração</a>
    </td>
    <td>
    <img src="views-images/TableViewNewest.png" title="Exemplo de modo de tabela" class="tableimg">
    </td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.TimePicker/">TimePicker</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">exibição</a> controle que fornece a separação de tempo. A representação visual de um TimePicker é muito semelhante a uma das <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/">entrada</a>, exceto que um controle especial para um tempo de separação é exibido no lugar de um teclado.
    </td>
    <td>
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/TimePickerDemoPage.cs"><img src="views-images/TimePicker.png" title="Exemplo de TimePicker" class="tableimg">
    </a></td>
  </tr>
  <tr>
    <td valign="top">
      <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/">WebView</a>
    </td>
    <td valign="top">
Um <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.View/">exibição</a> que apresenta conteúdo HTML.
    <br />
    <a href="https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/">API do WebView</a>
    <br />
    <a href="~/xamarin-forms/user-interface/webview.md">Documentação do WebView</a>
    <br />
    <a href="https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/WebViewDemoPage.cs">Origem de demonstração</a>
    </td>
    <td>
    <img src="views-images/WebView.png" title="Exemplo do WebView" class="tableimg">
    </td>
  </tr>
  </tbody>
</table>



## <a name="related-links"></a>Links relacionados

- [Introdução ao Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Galeria xamarin. Forms (exemplo)](https://developer.xamarin.com/samples/FormsGallery/)
- [Exemplos de xamarin. Forms](https://developer.xamarin.com/samples/tag/Xamarin.Forms/)
- [Documentação da API de xamarin. Forms](https://developer.xamarin.com/api/root/Xamarin.Forms/)
