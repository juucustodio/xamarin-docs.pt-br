---
title: Xamarin.FormsLayouts
description: Xamarin.FormsOs layouts são usados para compor controles de interface do usuário em estruturas visuais. Este artigo lista os layouts incluídos no Xamarin.Forms .
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/21/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 751a004e0051fcbfd0592654b0e73f26874b15d3
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84567080"
---
# <a name="xamarinforms-layouts"></a>Xamarin.FormsLayouts

[![Baixar exemplo ](~/media/shared/download.png) baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Os layouts do Xamarin. Forms são usados para compor controles de interface do usuário em estruturas visuais._

As [`Layout`](xref:Xamarin.Forms.Layout) [`Layout<T>`](xref:Xamarin.Forms.Layout`1) classes e no Xamarin.Forms são subtipos especializados de exibições que atuam como contêineres para exibições e outros layouts. A `Layout` própria classe deriva de [`View`](views.md) . Um `Layout` derivativo normalmente contém uma lógica para definir a posição e o tamanho dos elementos filho em Xamarin.Forms aplicativos.

[![Xamarin.FormsTipos de layout](layouts-images/layouts-sml.png "[! Parar. Tipos de layout não LOC (Xamarin. Forms)]")](layouts-images/layouts.png#lightbox "[! Parar. Tipos de layout não LOC (Xamarin. Forms)]")

As classes que derivam de `Layout` podem ser divididas em duas categorias:

## <a name="layouts-with-single-content"></a>Layouts com conteúdo único

Essas classes derivam de [`Layout`](xref:Xamarin.Forms.Layout) , que define [`Padding`](xref:Xamarin.Forms.Layout.Padding) e [`IsClippedToBounds`](xref:Xamarin.Forms.Layout.IsClippedToBounds) Propriedades.

### <a name="contentview"></a>ContentView

|     |     |
| --- | --- |
| [`ContentView`](xref:Xamarin.Forms.ContentView)contém um único filho que é definido com a [`Content`](xref:Xamarin.Forms.ContentView.Content) propriedade. A `Content` propriedade pode ser definida para qualquer `View` derivativo, incluindo outros `Layout` derivativos. `ContentView`é usado principalmente como um elemento estrutural e serve como uma classe base para [`Frame`](#frame) .<br /><br />[Documentação](xref:Xamarin.Forms.ContentView)  /  da API [Guia](~/xamarin-forms/user-interface/layouts/contentview.md)  /  [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/) de | [![Exemplo de ContentView](layouts-images/ContentView.png "Exemplo de ContentView")](layouts-images/ContentView-Large.png#lightbox "Exemplo de ContentView")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentViewDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentViewDemoPage.xaml) |
|     |     |

### <a name="frame"></a>Quadro

|     |     |
| --- | --- |
| A [`Frame`](xref:Xamarin.Forms.Frame) classe deriva de [`ContentView`](#contentview) e exibe uma borda, ou quadro, em torno de seu filho. A `Frame` classe tem um [`Padding`](xref:Xamarin.Forms.Layout.Padding) valor padrão de 20 e também define [`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor) [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius) as propriedades, e [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) .<br /><br />[Documentação](xref:Xamarin.Forms.Frame)  /  da API [Guia](~/xamarin-forms/user-interface/layouts/frame.md)  /  [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/) de | [![Exemplo de quadro](layouts-images/Frame.png "Exemplo de quadro")](layouts-images/Frame-Large.png#lightbox "Exemplo de quadro")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FrameDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FrameDemoPage.xaml) |
|     |     |

### <a name="scrollview"></a>ScrollView

|     |     |
| --- | --- |
| [`ScrollView`](xref:Xamarin.Forms.ScrollView)o é capaz de rolar seu conteúdo. Defina a [`Content`](xref:Xamarin.Forms.ScrollView.Content) propriedade como uma exibição ou layout muito grande para caber na tela. (O conteúdo de a `ScrollView` é muitas vezes um [`StackLayout`](#stacklayout) .) Defina a [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) propriedade para indicar se a rolagem deve ser vertical, horizontal ou ambas.<br /><br />[Documentação](xref:Xamarin.Forms.ScrollView)  /  da API [Guia](~/xamarin-forms/user-interface/layouts/scrollview.md)  /  [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) de | [![Exemplo de ScrollView](layouts-images/ScrollView.png "Exemplo de ScrollView")](layouts-images/ScrollView-Large.png#lightbox "Exemplo de ScrollView")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ScrollViewDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ScrollViewDemoPage.xaml) |
|     |     |

### <a name="templatedview"></a>TemplatedView

|     |     |
| --- | --- |
| [`TemplatedView`](xref:Xamarin.Forms.TemplatedView)exibe o conteúdo com um modelo de controle e é a classe base para [`ContentView`](#contentview) .<br /><br />[Documentação](xref:Xamarin.Forms.TemplatedView)  /  da API [Guia](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![Exemplo de TemplatedView](layouts-images/TemplatedView.png "Exemplo de TemplatedView")](layouts-images/TemplatedView.png#lightbox "Exemplo de TemplatedView") |
|     |     |

### <a name="contentpresenter"></a>ContentPresenter

|     |     |
| --- | --- |
| [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter)é um Gerenciador de layout para modos de exibição de modelo, usado em um [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) para marcar onde o conteúdo a ser apresentado é exibido.<br /><br />[Documentação](xref:Xamarin.Forms.ContentPresenter)  /  da API [Guia](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![Exemplo de ContentPresenter](layouts-images/ContentPresenter.png "Exemplo de ContentPresenter")](layouts-images/ContentPresenter.png#lightbox "Exemplo de ContentPresenter") |
|     |     |

## <a name="layouts-with-multiple-children"></a>Layouts com vários filhos

Essas classes derivam de [`Layout<View>`](xref:Xamarin.Forms.Layout`1) .

### <a name="stacklayout"></a>StackLayout

|     |     |
| --- | --- |
| [`StackLayout`](xref:Xamarin.Forms.StackLayout)posiciona os elementos filho em uma pilha horizontal ou verticalmente com base na [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) propriedade. A [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) Propriedade governa o espaçamento entre os filhos e tem um valor padrão de 6.<br /><br />[Documentação](xref:Xamarin.Forms.StackLayout)  /  da API [Guia](~/xamarin-forms/user-interface/layouts/stacklayout.md)  /  [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) de| [![Exemplo de StackLayout](layouts-images/StackLayout.png "Exemplo de StackLayout")](layouts-images/StackLayout-Large.png#lightbox "Exemplo de StackLayout")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StackLayoutDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StackLayoutDemoPage.xaml) |
|     |     |

### <a name="grid"></a>Grade

|     |     |
| --- | --- |
| [`Grid`](xref:Xamarin.Forms.Grid)posiciona seus elementos filho em uma grade de linhas e colunas. A posição de um filho é indicada usando as [Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md) [`Row`](xref:Xamarin.Forms.Grid.RowProperty) , [`Column`](xref:Xamarin.Forms.Grid.ColumnProperty) , [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) e [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) .<br /><br />[Documentação](xref:Xamarin.Forms.Grid)  /  da API [Guia](~/xamarin-forms/user-interface/layouts/grid.md)  /  [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) de | [![Exemplo de grade](layouts-images/Grid.png "Exemplo de grade")](layouts-images/Grid-Large.png#lightbox "Exemplo de grade")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/GridDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/GridDemoPage.xaml) |
|     |     |

### <a name="absolutelayout"></a>AbsoluteLayout

|     |     |
| --- | --- |
| [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)posiciona elementos filho em locais específicos em relação ao seu pai. A posição de um filho é indicada usando as [Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md) [`LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) e [`LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) . Um `AbsoluteLayout` é útil para animar as posições das exibições.<br /><br />[Documentação](xref:Xamarin.Forms.AbsoluteLayout)  /  da API [Guia](~/xamarin-forms/user-interface/layouts/absolute-layout.md)  /  [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) de | [![Exemplo de AbsoluteLayout](layouts-images/AbsoluteLayout.png "Exemplo de AbsoluteLayout")](layouts-images/AbsoluteLayout-Large.png#lightbox "Exemplo de AbsoluteLayout")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/AbsoluteLayoutDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml) com [code-behind](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml.cs) |
|     |     |

### <a name="relativelayout"></a>RelativeLayout

|     |     |
| --- | --- |
| [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)posiciona elementos filho em relação a `RelativeLayout` si mesmo ou a seus irmãos. A posição de um filho é indicada usando as [Propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md) definidas como objetos do tipo [`Constraint`](xref:Xamarin.Forms.Constraint) e [`BoundsConstraint`](xref:Xamarin.Forms.Constraint) .<br /><br />[Documentação](xref:Xamarin.Forms.RelativeLayout)  /  da API [Guia](~/xamarin-forms/user-interface/layouts/relative-layout.md)  /  [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) de | [![Exemplo de RelativeLayout](layouts-images/RelativeLayout.png "Exemplo de RelativeLayout")](layouts-images/RelativeLayout-Large.png#lightbox "Exemplo de RelativeLayout")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RelativeLayoutDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RelativeLayoutDemoPage.xaml) |
|     |     |

### <a name="flexlayout"></a>FlexLayout

|     |     |
| --- | --- |
| [`FlexLayout`](xref:Xamarin.Forms.FlexLayout)o é baseado no [módulo de layout de caixa flexível](https://www.w3.org/TR/css-flexbox-1/)CSS, normalmente conhecido como _layout flexível_ ou _caixa flexível_. `FlexLayout`define seis propriedades vinculáveis e cinco propriedades vinculáveis anexadas que permitem que os filhos sejam empilhados ou encapsulados com muitas opções de alinhamento e orientação.<br /><br />[Documentação](xref:Xamarin.Forms.FlexLayout)  /  da API [Guia](~/xamarin-forms/user-interface/layouts/flex-layout.md)  /  [Exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos) de | [![Exemplo de FlexLayout](layouts-images/FlexLayout.png "Exemplo de FlexLayout")](layouts-images/FlexLayout-Large.png#lightbox "Exemplo de FlexLayout")<br />[Código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FlexLayoutDemoPage.cs)  /  [Página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FlexLayoutDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Links relacionados

- [Xamarin.FormsExemplo de FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Xamarin.FormsAmostras](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Xamarin.FormsDocumentação da API](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
