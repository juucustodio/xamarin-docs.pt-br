---
title: Layouts do Xamarin.Forms
description: Layouts do xamarin. Forms são usados para compor controles de interface do usuário em estruturas de visual. Este artigo lista os layouts incluídos no xamarin. Forms.
ms.prod: xamarin
ms.assetid: F4180997-BA21-453A-9958-D1E2940DF050
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/21/2018
ms.openlocfilehash: e247be8387ce984d6695431ec432119d01344b42
ms.sourcegitcommit: 211fed94fb96127a3e158ae1ff5d7eb831a203d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75955778"
---
# <a name="xamarinforms-layouts"></a>Layouts do Xamarin.Forms

[![Baixar Exemplo](~/media/shared/download.png) Baixar o exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Layouts do xamarin. Forms são usados para compor controles de interface do usuário em estruturas de visual._

O [ `Layout` ](xref:Xamarin.Forms.Layout) e [ `Layout<T>` ](xref:Xamarin.Forms.Layout`1) classes no xamarin. Forms são subtipos especializados de exibições que atuam como contêineres para outros layouts e exibições. O `Layout` deriva de classe em si [ `View` ](views.md). Um `Layout` derivativo normalmente contém lógica para definir a posição e tamanho dos elementos filho em aplicativos xamarin. Forms.

[![Tipos de layout do Xamarin. Forms](layouts-images/layouts-sml.png "Tipos de layout do Xamarin. Forms")](layouts-images/layouts.png#lightbox "Tipos de layout do Xamarin. Forms")

As classes que derivam de `Layout` podem ser divididos em duas categorias:

## <a name="layouts-with-single-content"></a>Layouts com conteúdo simples

Essas classes derivam [ `Layout` ](xref:Xamarin.Forms.Layout), que define [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) e [ `IsClippedToBounds` ](xref:Xamarin.Forms.Layout.IsClippedToBounds) propriedades.

<a name="contentView" />

### <a name="contentview"></a>ContentView

|     |     |
| --- | --- |
| [`ContentView`](xref:Xamarin.Forms.ContentView) contém um único filho que é definido com o [ `Content` ](xref:Xamarin.Forms.ContentView.Content) propriedade. O `Content` propriedade pode ser definida como qualquer `View` derivativo, incluindo outros `Layout` derivados. `ContentView` é usado principalmente como um elemento estrutural e serve como uma classe base para [ `Frame` ](#frame).<br /><br />[Documentação da API](xref:Xamarin.Forms.ContentView) / [guia](~/xamarin-forms/user-interface/layouts/contentview.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-cardview/) | [![Exemplo de ContentView](layouts-images/ContentView.png "Exemplo de ContentView")](layouts-images/ContentView-Large.png#lightbox "Exemplo de ContentView")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ContentViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ContentViewDemoPage.xaml) |
|     |     |

<a named="frame" />

### <a name="frame"></a>{1&gt;Quadro&lt;1}

|     |     |
| --- | --- |
| A classe [`Frame`](xref:Xamarin.Forms.Frame) deriva de [`ContentView`](#contentView) e exibe uma borda, ou quadro, em torno de seu filho. A classe `Frame` tem um valor de [`Padding`](xref:Xamarin.Forms.Layout.Padding) padrão de 20 e também define as propriedades [`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor), [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius)e [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) .<br /><br />[Documentação da API](xref:Xamarin.Forms.Frame) / [guia](~/xamarin-forms/user-interface/layouts/frame.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/) | [![Exemplo de quadro](layouts-images/Frame.png "Exemplo de quadro")](layouts-images/Frame-Large.png#lightbox "Exemplo de quadro")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FrameDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FrameDemoPage.xaml) |
|     |     |

<a name="scrollView" />

### <a name="scrollview"></a>ScrollView

|     |     |
| --- | --- |
| [`ScrollView`](xref:Xamarin.Forms.ScrollView) é capaz de rolagem seu conteúdo. Defina as [ `Content` ](xref:Xamarin.Forms.ScrollView.Content) propriedade para uma exibição ou layout muito grande para caber na tela. (O conteúdo de um `ScrollView` é muitas vezes um [`StackLayout`](#stackLayout).) Defina a propriedade [`Orientation`](xref:Xamarin.Forms.ScrollView.Orientation) para indicar se a rolagem deve ser vertical, horizontal ou ambas.<br /><br />[Documentação da API](xref:Xamarin.Forms.ScrollView) / [guia](~/xamarin-forms/user-interface/layouts/scroll-view.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Exemplo de ScrollView](layouts-images/ScrollView.png "Exemplo de ScrollView")](layouts-images/ScrollView-Large.png#lightbox "Exemplo de ScrollView")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/ScrollViewDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/ScrollViewDemoPage.xaml) |
|     |     |

### <a name="templatedview"></a>TemplatedView

|     |     |
| --- | --- |
| [`TemplatedView`](xref:Xamarin.Forms.TemplatedView) Exibe o conteúdo com um modelo de controle, e é a classe base para [ `ContentView` ](#contentView).<br /><br />[Documentação da API](xref:Xamarin.Forms.TemplatedView) / [guia](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![Exemplo de TemplatedView](layouts-images/TemplatedView.png "Exemplo de TemplatedView")](layouts-images/TemplatedView.png#lightbox "Exemplo de TemplatedView") |
|     |     |

### <a name="contentpresenter"></a>ContentPresenter

|     |     |
| --- | --- |
| [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) é um Gerenciador de layout para exibições com modelo, usado em uma [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) para marcar onde o conteúdo que deve ser apresentada aparece.<br /><br />[Documentação da API](xref:Xamarin.Forms.ContentPresenter) / [guia](~/xamarin-forms/app-fundamentals/templates/control-template.md) | [![Exemplo de ContentPresenter](layouts-images/ContentPresenter.png "Exemplo de ContentPresenter")](layouts-images/ContentPresenter.png#lightbox "Exemplo de ContentPresenter") |
|     |     |

## <a name="layouts-with-multiple-children"></a>Layouts com vários filhos

Essas classes derivam [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1).

<a name="stackLayout" />

### <a name="stacklayout"></a>StackLayout

|     |     |
| --- | --- |
| [`StackLayout`](xref:Xamarin.Forms.StackLayout) Posiciona os elementos filho em uma pilha horizontal ou verticalmente com base nas [ `Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation) propriedade. O [ `Spacing` ](xref:Xamarin.Forms.StackLayout.Spacing) propriedade rege o espaçamento entre os filhos e tem um valor padrão de 6.<br /><br />[Documentação da API](xref:Xamarin.Forms.StackLayout) / [guia](~/xamarin-forms/user-interface/layouts/stack-layout.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)| [![Exemplo de StackLayout](layouts-images/StackLayout.png "Exemplo de StackLayout")](layouts-images/StackLayout-Large.png#lightbox "Exemplo de StackLayout")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/StackLayoutDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/StackLayoutDemoPage.xaml) |
|     |     |

<a name="grid" />

### <a name="grid"></a>Grade

|     |     |
| --- | --- |
| [`Grid`](xref:Xamarin.Forms.Grid) Posiciona a seus elementos filho em uma grade de linhas e colunas. Posição de um filho é indicada usando o [propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md) [ `Row` ](xref:Xamarin.Forms.Grid.RowProperty), [ `Column` ](xref:Xamarin.Forms.Grid.ColumnProperty), [ `RowSpan` ](xref:Xamarin.Forms.Grid.RowSpanProperty), e [ `ColumnSpan` ](xref:Xamarin.Forms.Grid.ColumnSpanProperty).<br /><br />[Documentação da API](xref:Xamarin.Forms.Grid) / [guia](~/xamarin-forms/user-interface/layouts/grid.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Exemplo de grade](layouts-images/Grid.png "Exemplo de grade")](layouts-images/Grid-Large.png#lightbox "Exemplo de grade")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/GridDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/GridDemoPage.xaml) |
|     |     |

### <a name="absolutelayout"></a>AbsoluteLayout

|     |     |
| --- | --- |
| [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) Posiciona os elementos filho em locais específicos em relação ao seu pai. Posição de um filho é indicada usando o [propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md) [ `LayoutBounds` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) e [ `LayoutFlags` ](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty). Um `AbsoluteLayout` é útil para animar as posições dos modos de exibição.<br /><br />[Documentação da API](xref:Xamarin.Forms.AbsoluteLayout) / [guia](~/xamarin-forms/user-interface/layouts/absolute-layout.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Exemplo de AbsoluteLayout](layouts-images/AbsoluteLayout.png "Exemplo de AbsoluteLayout")](layouts-images/AbsoluteLayout-Large.png#lightbox "Exemplo de AbsoluteLayout")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/AbsoluteLayoutdDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml) com [de lógica](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/AbsoluteLayoutDemoPage.xaml.cs) |
|     |     |

### <a name="relativelayout"></a>RelativeLayout

|     |     |
| --- | --- |
| [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) Posiciona os elementos filho relativo a `RelativeLayout` em si ou para seus irmãos. Posição de um filho é indicada usando o [propriedades anexadas](~/xamarin-forms/xaml/attached-properties.md) que são definidas como objetos do tipo [ `Constraint` ](xref:Xamarin.Forms.Constraint) e [ `BoundsConstraint` ](xref:Xamarin.Forms.Constraint).<br /><br />[Documentação da API](xref:Xamarin.Forms.RelativeLayout) / [guia](~/xamarin-forms/user-interface/layouts/relative-layout.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout) | [![Exemplo de RelativeLayout](layouts-images/RelativeLayout.png "Exemplo de RelativeLayout")](layouts-images/RelativeLayout-Large.png#lightbox "Exemplo de RelativeLayout")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/RelativeLayoutDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/RelativeLayoutDemoPage.xaml) |
|     |     |

### <a name="flexlayout"></a>FlexLayout

|     |     |
| --- | --- |
| [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) se baseia na folha de estilos [módulo de Layout de caixa flexível](https://www.w3.org/TR/css-flexbox-1/), comumente conhecido como _flex layout_ ou _flex caixa_. `FlexLayout` define seis propriedades vinculáveis e cinco propriedades anexadas associáveis que permitem que os filhos para ser empilhadas ou empacotado com muitas opções de alinhamento e orientação.<br /><br />[Documentação da API](xref:Xamarin.Forms.FlexLayout) / [guia](~/xamarin-forms/user-interface/layouts/flex-layout.md) / [exemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos) | [![Exemplo de FlexLayout](layouts-images/FlexLayout.png "Exemplo de FlexLayout")](layouts-images/FlexLayout-Large.png#lightbox "Exemplo de FlexLayout")<br />[O código C# para esta página](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/CodeExamples/FlexLayoutDemoPage.cs) / [página XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/FormsGallery/FormsGallery/FormsGallery/XamlExamples/FlexLayoutDemoPage.xaml) |
|     |     |

## <a name="related-links"></a>Links Relacionados

- [Exemplo de xamarin. Forms FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
- [Exemplos do Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [Documentação da API do Xamarin.Forms](https://docs.microsoft.com/dotnet/api/xamarin.forms?view=xamarin-forms)
