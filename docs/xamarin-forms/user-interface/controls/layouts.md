---
title: Layouts do Xamarin.Forms
description: Os layouts do Xamarin. Forms são usados para compor controles de interface do usuário em estruturas visuais. Este artigo lista os layouts incluídos no Xamarin. Forms.
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/21/2018
ms.openlocfilehash: 39ea210670476604b18c9f1aac760cc130b93c3c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73005393"
---
# <a name="xamarinforms-layouts"></a>Layouts do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Os layouts do Xamarin. Forms são usados para compor controles de interface do usuário em estruturas visuais._

As classes [`Layout`](xref:Xamarin.Forms.Layout) e [`Layout<T>`](xref:Xamarin.Forms.Layout`1) no Xamarin. Forms são subtipos especializados de exibições que atuam como contêineres para exibições e outros layouts. A própria classe `Layout` deriva de [`View`](views.md). Um `Layout` derivativo normalmente contém a lógica para definir a posição e o tamanho dos elementos filho nos aplicativos Xamarin. Forms.

[![Tipos de layout do Xamarin. Forms](layouts-images/layouts-sml.png "Tipos de layout do Xamarin. Forms")](layouts-images/layouts.png#lightbox "Tipos de layout do Xamarin. Forms")

As classes que derivam de `Layout` podem ser divididas em duas categorias:

## <a name="layouts-with-single-content"></a>Layouts com conteúdo único

Essas classes derivam de [`Layout`](xref:Xamarin.Forms.Layout), que define [`Padding`](xref:Xamarin.Forms.Layout.Padding) e [`IsClippedToBounds`](xref:Xamarin.Forms.Layout.IsClippedToBounds) Propriedades.

<a name="contentView" />

### <a name="contentview"></a>ContentView

|     |     |
| --- | --- |
| [`ContentView`](xref:Xamarin.Forms.ContentView) contém um único filho que é definido com a propriedade [`Content`](xref:Xamarin.Forms.ContentView.Content) . A propriedade `Content` pode ser definida como qualquer derivação de `View`, incluindo outros `Layout` derivativos. `ContentView` é usado principalmente como um elemento estrutural e serve como uma classe base para [`Frame`](#frame).<br /><br />[Documentação da API](xref:Xamarin.Forms.ContentView)  / [guia](~/xamarin-forms/user-interface/layouts/contentview.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-cardview/) | [![Exemplo de ContentView](layouts-images/ContentView.png "Exemplo de ContentView")](layouts-images/ContentView-Large.png#lightbox "Exemplo de ContentView")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentViewDemoPage.cs) |
|     |     |

<a named="frame" />

### <a name="frame"></a>Quadro

|     |     |
| --- | --- |
| A classe [`Frame`](xref:Xamarin.Forms.Frame) deriva de [`ContentView`](#contentView) e exibe uma borda, ou quadro, em torno de seu filho. A classe `Frame` tem um valor de [`Padding`](xref:Xamarin.Forms.Layout.Padding) padrão de 20 e também define as propriedades [`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor), [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius)e [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) .<br /><br />[Documentação da API](xref:Xamarin.Forms.Frame)  / [guia](~/xamarin-forms/user-interface/layouts/frame.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/) | [![Exemplo de quadro](layouts-images/Frame.png "Exemplo de quadro")](layouts-images/Frame-Large.png#lightbox "Exemplo de quadro")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FrameDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FrameDemoPage.cs) |
|     |     |

<a name="scrollView" />

### <a name="scrollview"></a>ScrollView

|     |     |
| --- | --- |
| [`ScrollView`](xref:Xamarin.Forms.ScrollView) é capaz de rolar seu conteúdo. Defina a propriedade [`Content`](xref:Xamarin.Forms.ScrollView.Content) como uma exibição ou layout muito grande para caber na tela. (O conteúdo de um `ScrollView` é muitas vezes um [`StackLayout`](#stackLayout).) Defina a propriedade [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) para indicar se a rolagem deve ser vertical, horizontal ou ambas.<br /><br />[Documentação da API](xref:Xamarin.Forms.ScrollView)  / [guia](~/xamarin-forms/user-interface/layouts/scroll-view.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Exemplo de ScrollView](layouts-images/ScrollView.png "Exemplo de ScrollView")](layouts-images/ScrollView-Large.png#lightbox "Exemplo de ScrollView")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ScrollViewDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ScrollViewDemoPage.cs) |
|     |     |

### <a name="templatedview"></a>TemplatedView

|     |     |
| --- | --- |
| [`TemplatedView`](xref:Xamarin.Forms.TemplatedView) exibe o conteúdo com um modelo de controle e é a classe base para [`ContentView`](#contentView).<br /><br />[Guia](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) de  /  de [documentação da API](xref:Xamarin.Forms.TemplatedView) | [![Exemplo de TemplatedView](layouts-images/TemplatedView.png "Exemplo de TemplatedView")](layouts-images/TemplatedView.png#lightbox "Exemplo de TemplatedView") |
|     |     |

### <a name="contentpresenter"></a>ContentPresenter

|     |     |
| --- | --- |
| [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) é um Gerenciador de layout para modos de exibição de modelo, usado em uma [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) para marcar onde o conteúdo a ser apresentado é exibido.<br /><br />[Guia](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) de  /  de [documentação da API](xref:Xamarin.Forms.ContentPresenter) | [![Exemplo de ContentPresenter](layouts-images/ContentPresenter.png "Exemplo de ContentPresenter")](layouts-images/ContentPresenter.png#lightbox "Exemplo de ContentPresenter") |
|     |     |

## <a name="layouts-with-multiple-children"></a>Layouts com vários filhos

Essas classes derivam de [`Layout<View>`](xref:Xamarin.Forms.Layout`1).

<a name="stackLayout" />

### <a name="stacklayout"></a>StackLayout

|     |     |
| --- | --- |
| [`StackLayout`](xref:Xamarin.Forms.StackLayout) posiciona os elementos filho em uma pilha horizontal ou verticalmente com base na propriedade [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) . A propriedade [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) governa o espaçamento entre os filhos e tem um valor padrão de 6.<br /><br />[Documentação da API](xref:Xamarin.Forms.StackLayout)  / [guia](~/xamarin-forms/user-interface/layouts/stack-layout.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)| [![Exemplo de StackLayout](layouts-images/StackLayout.png "Exemplo de StackLayout")](layouts-images/StackLayout-Large.png#lightbox "Exemplo de StackLayout")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StackLayoutDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StackLayoutDemoPage.cs) |
|     |     |

<a name="grid" />

### <a name="grid"></a>Grade

|     |     |
| --- | --- |
| [`Grid`](xref:Xamarin.Forms.Grid) posiciona seus elementos filho em uma grade de linhas e colunas. A posição de um filho é indicada usando as [Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md) [`Row`](xref:Xamarin.Forms.Grid.RowProperty), [`Column`](xref:Xamarin.Forms.Grid.ColumnProperty), [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty)e [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty).<br /><br />[Documentação da API](xref:Xamarin.Forms.Grid)  / [guia](~/xamarin-forms/user-interface/layouts/grid.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Exemplo de grade](layouts-images/Grid.png "Exemplo de grade")](layouts-images/Grid-Large.png#lightbox "Exemplo de grade")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/GridDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/GridDemoPage.cs) |
|     |     |

### <a name="absolutelayout"></a>AbsoluteLayout

|     |     |
| --- | --- |
| [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) posiciona elementos filho em locais específicos em relação ao seu pai. A posição de um filho é indicada usando as [Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md) [`LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) e [`LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty). Um `AbsoluteLayout` é útil para animar as posições das exibições.<br /><br />[Documentação da API](xref:Xamarin.Forms.AbsoluteLayout)  / [guia](~/xamarin-forms/user-interface/layouts/absolute-layout.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Exemplo de AbsoluteLayout](layouts-images/AbsoluteLayout.png "Exemplo de AbsoluteLayout")](layouts-images/AbsoluteLayout-Large.png#lightbox "Exemplo de AbsoluteLayout")<br />código para esta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml) com [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml.cs) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/AbsoluteLayoutdDemoPage.cs) |
|     |     |

### <a name="relativelayout"></a>RelativeLayout

|     |     |
| --- | --- |
| [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) posiciona elementos filho em relação ao `RelativeLayout` em si ou a seus irmãos. A posição de um filho é indicada usando as [Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md) definidas como objetos do tipo [`Constraint`](xref:Xamarin.Forms.Constraint) e [`BoundsConstraint`](xref:Xamarin.Forms.Constraint).<br /><br />[Documentação da API](xref:Xamarin.Forms.RelativeLayout)  / [guia](~/xamarin-forms/user-interface/layouts/relative-layout.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Exemplo de RelativeLayout](layouts-images/RelativeLayout.png "Exemplo de RelativeLayout")](layouts-images/RelativeLayout-Large.png#lightbox "Exemplo de RelativeLayout")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RelativeLayoutDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RelativeLayoutDemoPage.cs) |
|     |     |

### <a name="flexlayout"></a>FlexLayout

|     |     |
| --- | --- |
| o [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) é baseado no [módulo de layout de caixa flexível](https://www.w3.org/TR/css-flexbox-1/)CSS, normalmente conhecido como _layout flexível_ ou _caixa flexível_. `FlexLayout` define seis propriedades vinculáveis e cinco propriedades vinculáveis anexadas que permitem que os filhos sejam empilhados ou encapsulados com muitas opções de alinhamento e orientação.<br /><br />[Documentação da API](xref:Xamarin.Forms.FlexLayout)  / [guia](~/xamarin-forms/user-interface/layouts/flex-layout.md)  / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos) | [![Exemplo de FlexLayout](layouts-images/FlexLayout.png "Exemplo de FlexLayout")](layouts-images/FlexLayout-Large.png#lightbox "Exemplo de FlexLayout")<br />código desta página  / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FlexLayoutDemoPage.xaml) [ C# ](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FlexLayoutDemoPage.cs) |
|     |     |

## <a name="related-links"></a>Links relacionados

- [Exemplo de FormsGallery do Xamarin. Forms](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Amostras do Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentação da API do Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
