---
title: Layouts do xamarin. Forms
description: Layouts do xamarin. Forms são usados para compor controles de interface do usuário em estruturas de visual. Este artigo lista os layouts incluídos no xamarin. Forms.
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/21/2018
ms.openlocfilehash: 224eb2ee3958e5979382a3dc5e70110fdce51879
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994596"
---
# <a name="xamarinforms-layouts"></a>Layouts do xamarin. Forms

_Layouts do xamarin. Forms são usados para compor controles de interface do usuário em estruturas de visual._

O [ `Layout` ](xref:Xamarin.Forms.Layout) e [ `Layout<T>` ](xref:Xamarin.Forms.Layout`1) classes no xamarin. Forms são subtipos especializados de exibições que atuam como contêineres para outros layouts e exibições. O `Layout` deriva de classe em si [ `View` ](views.md). Um `Layout` derivativo normalmente contém lógica para definir a posição e tamanho dos elementos filho em aplicativos xamarin. Forms.

[![Tipos de Layout do xamarin. Forms](layouts-images/layouts-sml.png "tipos de Layout do xamarin. Forms")](layouts-images/layouts.png#lightbox "tipos de Layout do xamarin. Forms")

As classes que derivam de `Layout` podem ser divididos em duas categorias:

## <a name="layouts-with-single-content"></a>Layouts com conteúdo simples

Essas classes derivam [ `Layout` ](xref:Xamarin.Forms.Layout), que define [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) e [ `IsClippedToBounds` ](xref:Xamarin.Forms.Layout.IsClippedToBounds) propriedades.

<a name="contentView" />

### <a name="contentview"></a>ContentView

|     |     |
| --- | --- |
| [`ContentView`](xref:Xamarin.Forms.ContentView) contém um único filho que é definido com o [ `Content` ](xref:Xamarin.Forms.ContentView.Content) propriedade. O `Content` propriedade pode ser definida como qualquer `View` derivativo, incluindo outros `Layout` derivados. `ContentView` é usado principalmente como um elemento estrutural e serve como uma classe base para [ `Frame` ](#frame).<br /><br />[Documentação da API](xref:Xamarin.Forms.ContentView) | [![Exemplo de ContentView](layouts-images/ContentView.png "exemplo ContentView")](layouts-images/ContentView-Large.png#lightbox "ContentView do exemplo")<br />[O código c# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentViewDemoPage.xaml) |
|     |     |

<a named="frame" />

### <a name="frame"></a>Quadro

|     |     |
| --- | --- |
| O [ `Frame` ](xref:Xamarin.Forms.Frame) classe deriva [ `ContentView` ](#contentView) e exibe um quadro retangular ao redor de seu filho. `Frame` tem um padrão [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) valor de 20 e também define [ `OutlineColor` ](xref:Xamarin.Forms.Frame.OutlineColor), [ `CornerRadius` ](xref:Xamarin.Forms.Frame.CornerRadius), e [ `HasShadow` ](xref:Xamarin.Forms.Frame.HasShadow)propriedades.<br /><br />[Documentação da API](xref:Xamarin.Forms.Frame) | [![Exemplo de quadro](layouts-images/Frame.png "exemplo de quadro")](layouts-images/Frame-Large.png#lightbox "exemplo de quadro")<br />[O código c# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FrameDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FrameDemoPage.xaml) |
|     |     |

<a name="scrollView" />

### <a name="scrollview"></a>ScrollView

|     |     |
| --- | --- |
| [`ScrollView`](xref:Xamarin.Forms.ScrollView) é capaz de rolagem seu conteúdo. Defina as [ `Content` ](xref:Xamarin.Forms.ScrollView.Content) propriedade para uma exibição ou layout muito grande para caber na tela. (O conteúdo de um `ScrollView` costuma ser muito uma [ `StackLayout` ](#stackLayout).) Defina as [ `Orientation` ](xref:Xamarin.Forms.ScrollView.Orientation) propriedade para indicar se a rolagem deve ser vertical, horizontal, ou ambos.<br /><br />[Documentação da API](xref:Xamarin.Forms.ScrollView) / [guia](~/xamarin-forms/user-interface/layouts/scroll-view.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![Exemplo de ScrollView](layouts-images/ScrollView.png "exemplo ScrollView")](layouts-images/ScrollView-Large.png#lightbox "exemplo ScrollView")<br />[O código c# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ScrollViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ScrollViewDemoPage.xaml) |
|     |     |

### <a name="templatedview"></a>TemplatedView

|     |     |
| --- | --- |
| [`TemplatedView`](xref:Xamarin.Forms.TemplatedView) Exibe o conteúdo com um modelo de controle, e é a classe base para [ `ContentView` ](#contentView).<br /><br />[Documentação da API](xref:Xamarin.Forms.TemplatedView) / [guia](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![Exemplo de TemplatedView](layouts-images/TemplatedView.png "exemplo TemplatedView")](layouts-images/TemplatedView.png#lightbox "TemplatedView exemplo") |
|     |     |

### <a name="contentpresenter"></a>ContentPresenter

|     |     |
| --- | --- |
| [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) é um Gerenciador de layout para exibições com modelo, usado em uma [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) para marcar onde o conteúdo que deve ser apresentada aparece.<br /><br />[Documentação da API](xref:Xamarin.Forms.ContentPresenter) / [guia](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md) | [![Exemplo de ContentPresenter](layouts-images/ContentPresenter.png "exemplo ContentPresenter")](layouts-images/ContentPresenter.png#lightbox "exemplo ContentPresenter") |
|     |     |

## <a name="layouts-with-multiple-children"></a>Layouts com vários filhos

Essas classes derivam [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1).

<a name="stackLayout" />

### <a name="stacklayout"></a>StackLayout

|     |     |
| --- | --- |
| [`StackLayout`](xref:Xamarin.Forms.StackLayout) Posiciona os elementos filho em uma pilha horizontal ou verticalmente com base nas [ `Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation) propriedade. O [ `Spacing` ](xref:Xamarin.Forms.StackLayout.Spacing) propriedade rege o espaçamento entre os filhos e tem um valor padrão de 6.<br /><br />[Documentação da API](xref:Xamarin.Forms.StackLayout) / [guia](~/xamarin-forms/user-interface/layouts/stack-layout.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)| [![Exemplo de StackLayout](layouts-images/StackLayout.png "StackLayout exemplo")](layouts-images/StackLayout-Large.png#lightbox "StackLayout exemplo")<br />[O código c# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StackLayoutDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StackLayoutDemoPage.xaml) |
|     |     |

<a name="grid" />

### <a name="grid"></a>Grade

|     |     |
| --- | --- |
| [`Grid`](xref:Xamarin.Forms.Grid) Posiciona a seus elementos filho em uma grade de linhas e colunas. Posição de um filho é indicada usando o [propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md) [ `Row` ](xref:Xamarin.Forms.Grid.RowProperty), [ `Column` ](xref:Xamarin.Forms.Grid.ColumnProperty), [ `RowSpan` ](xref:Xamarin.Forms.Grid.RowSpanProperty), e [ `ColumnSpan` ](xref:Xamarin.Forms.Grid.ColumnSpanProperty).<br /><br />[Documentação da API](xref:Xamarin.Forms.Grid) / [guia](~/xamarin-forms/user-interface/layouts/grid.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![Exemplo de grade](layouts-images/Grid.png "exemplo de grade")](layouts-images/Grid-Large.png#lightbox "exemplo de grade")<br />[O código c# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/GridDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/GridDemoPage.xaml) |
|     |     |

### <a name="absolutelayout"></a>AbsoluteLayout

|     |     |
| --- | --- |
| [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) Posiciona os elementos filho em locais específicos em relação ao seu pai. Posição de um filho é indicada usando o [propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md) [ `LayoutBounds` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) e [ `LayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty). Um `AbsoluteLayout` é útil para animar as posições dos modos de exibição.<br /><br />[Documentação da API](xref:Xamarin.Forms.AbsoluteLayout) / [guia](~/xamarin-forms/user-interface/layouts/absolute-layout.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![Exemplo de AbsoluteLayout](layouts-images/AbsoluteLayout.png "exemplo AbsoluteLayout")](layouts-images/AbsoluteLayout-Large.png#lightbox "exemplo AbsoluteLayout")<br />[O código c# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/AbsoluteLayoutdDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml) com [de lógica](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml.cs) |
|     |     |

### <a name="relativelayout"></a>RelativeLayout

|     |     |
| --- | --- |
| [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) Posiciona os elementos filho relativo a `RelativeLayout` em si ou para seus irmãos. Posição de um filho é indicada usando o [propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md) que são definidas como objetos do tipo [ `Constraint` ](xref:Xamarin.Forms.Constraint) e [ `BoundsConstraint` ](xref:Xamarin.Forms.Constraint).<br /><br />[Documentação da API](xref:Xamarin.Forms.RelativeLayout) / [guia](~/xamarin-forms/user-interface/layouts/relative-layout.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/) | [![Exemplo de RelativeLayout](layouts-images/RelativeLayout.png "exemplo RelativeLayout")](layouts-images/RelativeLayout-Large.png#lightbox "RelativeLayout exemplo")<br />[O código c# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RelativeLayoutDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RelativeLayoutDemoPage.xaml) |
|     |     |

### <a name="flexlayout"></a>FlexLayout

|     |     |
| --- | --- |
| [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) se baseia na folha de estilos [módulo de Layout de caixa flexível](http://www.w3.org/TR/css-flexbox-1/), comumente conhecido como _flex layout_ ou _flex caixa_. `FlexLayout` define seis propriedades vinculáveis e cinco propriedades anexadas associáveis que permitem que os filhos para ser empilhadas ou empacotado com muitas opções de alinhamento e orientação.<br /><br />[Documentação da API](xref:Xamarin.Forms.FlexLayout) / [guia](~/xamarin-forms/user-interface/layouts/flex-layout.md) / [exemplo](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/FlexLayoutDemos/) | [![Exemplo de FlexLayout](layouts-images/FlexLayout.png "exemplo FlexLayout")](layouts-images/FlexLayout-Large.png#lightbox "FlexLayout exemplo")<br />[O código c# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FlexLayoutDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FlexLayoutDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Links relacionados

- [Introdução ao Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Exemplo de xamarin. Forms FormsGallery](https://developer.xamarin.com/samples/FormsGallery/)
- [Amostras do Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [Documentação da API de xamarin. Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
